---
title: 'Azure OpenAIとLangChainを使ってReActの概念を学ぶ'
emoji: '🎁'
type: 'tech' # tech: 技術記事 / idea: アイデア
topics: ['Azure', 'openai', 'langchain', 'next.js', 'ReAct']
published: false
---

![react](/images/azure_react/react.png)

# はじめに

この記事は、Azure OpenAI Service と LangChain を使って ReAct の概念を学びます。
大規模言語モデルの概念の説明から記載しているので、初学者の方もご安心して読み進めていただければと存じます。

**人工知能・大規模言語モデル(LLM)にご興味がある方は、ぜひ一読ください。**

# 大規模言語モデル(LLM)の概要と課題

大規模言語モデル(LLM)は自然言語処理(NLP)の分野で用いられる大量のテキストデータから学習した機会学習モデルのことです。

LLM は以下のようなことが出来ます。

- 自然言語生成
- 質問応答
- 翻訳
- 要約
- 文書分類
- 要素認識
- 感情分析

特に質問応答は ChatGPT の登場でお馴染みになりましたよね。

ChatGPT が有名ですが、実は他にも大規模言語モデルは各社リリースしています。

![llm](/images/azure_react/llmlist.png)
[https://arxiv.org/pdf/2104.04473.pdf](https://arxiv.org/pdf/2104.04473.pdf)

| enterprise | llm name    |
| ---------- | ----------- |
| Google     | BERT        |
| OpenAI     | GPT         |
| NVIDIA     | Megatron-LM |
| Microsoft  | Turning-NLG |
| Google     | Bard        |

特に最近注目されている ChatGPT を例に挙げて紹介してみると以下のような感じです。

- 2022 年 11 月 30 日リリース
- GPT モデルを使って作られたチャットアプリサービス
- リリース後 2 ヶ月で１億ユーザ突破
- **GPT3/4 モデルの学習データは 2022 年 1 月までのデータしかない**

上記で注目したい点は、`GPT3/4 モデルの学習データは 2022 年 1 月までのデータしかない`という点です。
これから新しい情報や未知の情報(各企業の独自の情報等)を AI に組み込み、利活用していきたいと考えると LLM を強化する必要があります。

# LLM を強化する方法

前述した通り、大規模言語モデル(LLM)の問題点は**人工知能が学習した範囲内でしか回答ができない**ということです。
昨今人工知能の需要は高まり、以下のようなニーズがあるかと思います。

- 2022 年 1 月以降の情報が必要となる回答も正確に返してほしい
- 外部には公開していない社内の情報への質問にも正確に返してほしい
  ...etc

上記目標を達成する為には LLM を強化させなくてはなりません。

そこで、LLM を強化させる方法として 2 つの方法があります。

1. **Prompt Engineering**
   - LLM に与えるプロンプト内に追加データを含めたり、不足がある場合に外部ツールを呼び出してデータを追加することで回答制度を向上させる方法
     - 以前の情報をヒントにして回答
     - Web で検索して回答...etc
2. **Fine-tuning**
   - LLM 自体を再学習させ、パラメータを調整することで回答制度を向上させる方法
     - GPT3.5 や GPT4 自体を新たに作成...etc

もちろん Fine-tuning の方が精度の高い回答を得られる可能性が高まります。
ただ、LLM 自体が数億・数十億とお金をかけて大量のデータを用いて作成したものであるので、Fine-tuning を実施しようとすると膨大なコスト及びデータ量が必要になります。

そんな時に活躍するのが Prompt Engineering の手法である**ReAct**という概念になります。

# ReAct とは？

`ReAct（REasoning and ACTing）`は、言語モデル（LLM）を使用して思考（推論）と行動を同時に行うための新しい枠組みです。
ReAct の主な目的は、自然言語推論と意思決定のタスクを統合することで、より高度な言語理解と知識活用を可能にすることです。

!(ReAct の論文はこちら)[https://react-lm.github.io/]

**ReAct**の動作は主に以下の 2step からなります。

1. **Reasoning(推論)**
   - 問題領域を分析して検索の方法と内容を検討
2. **Acting(行動) + Observation(観察)**
   - 問題領域の分析結果に応じて具体的な情報を検索、情報収集
   - 検索結果を要約して解答を作成

具体例を出すと以下の画像のような感じになります。

![react](/images/azure_react/react_zentai.png)

**ReAct**の流れ

1. 人からの質問
2. OpenAI へ質問を投げかける。(この時 OpenAI へ送るプロンプトにツールとして BingSearch が使えることを含める)
3. OpenAI だけでの解答が難しい場合は、BingSearch を使って検索を行い、情報を得る
4. BingSearch から得た情報も含めて再度 OpenAI へ情報を送り、回答の要約を作成する
5. 人へ回答を返す

上記のように 2022 年 1 月以降の情報でも、検索情報を含めて再度 OpenAI へ送ることにより回答が可能になります。

上記では、BingSearch を使用していますが、他にも様々なツールを使用することが可能です。
例えば、BingSearch の箇所を社内情報に変更してみたりすると、社内のことを回答してくれる OpenAI が作成できます。

拡張性が高く、かなり便利で面白いシステムが構築出来ると考えています。
これからは各企業で DX 化がどんどん推進されていくかと思いますが、その際に ReAct の概念が多く使われるのではないかと個人的に期待しています。

**では実際に簡単なアプリケーションを作成し、動作を確認しましょう！**

# ReAct アプリケーションの動作検証および解説

以下のリポジトリの README に従って、ReAct アプリケーションを作成します。
!(github)[https://github.com/SatakeYusuke19920527/poc-react-app]

1. Azure リソースの Deploy
   まずは github の README に従って、Azure リソースをデプロイします。
   以下 3 つのリソースが作成されます。

- Azure OpenAI Service
- Bing Search
- Azure Static Web Apps

## Azure OpenAI Service のセットアップ

まずは、Azure OpenAI のセットアップです。全体図で言うとここの部分です。

まずは、Azure 上で動作させるので、何がともあれ Azure の無料アカウントを作成してみましょう！
[Azure の無料アカウントを使ってクラウドで構築](https://azure.microsoft.com/ja-jp/free)

Microsoft アカウントを作成して Azure へログインすれば OK です。

こんな感じの画面が出てきたでしょうか。

![Azure](/images/azure_openai_handson/azure_main.png)

何がともあれ、リソースグループを作成しましょう！
上の検索窓に`リソースグループ`と入力して検索してください。

![Azure](/images/azure_openai_handson/resouce_gr.png)

リソースグループの画面に進んだら、左上らへんにある作成をクリックして、リソースグループを作成しましょう！
次へを進んで、タグは別になくて大丈夫です。
![Azure](/images/azure_openai_handson/make_rg.png)

作成できましたか？

![Azure](/images/azure_openai_handson/rg_fin.png)

ここまで作成できたら、また検索窓に`OpenAI`と入力して Azure OpenAI の画面を開いてください。

また、左上らへんにある作成ボタンをクリックです。
リソースグループは先ほど作成したものを指定し、あとは下の画像のように設定してください。
![Azure](/images/azure_openai_handson/make_ai.png)

ネットワークは`インターネットを含むすべてのネットワークがこのリソースにアクセスできます。`を選択してください。
タグは特に何もつけなくて OK です。

作成ボタンをクリックしてください。デプロイが進行するはずです。(5 分ぐらいでしょうか。)
デプロイが完了すれば、リソースへ移動です。

次はモデルを Deploy する必要があります。

画面左上らへんにある`Azure OpenAI Studioへ移動`をクリックしてください。

![Azure](/images/azure_openai_handson/azureopenaistudio.png)

新しいデプロイの作成をクリック → 新しいデプロイの作成をクリックしてください。

以下の様な画面が出てくると思うので、いい感じに入力して作成をクリック

![Azure](/images/azure_openai_handson/modeldeploy.png)

このデプロイはすぐ完了します。
下の画面のようになりましたか？

![Azure](/images/azure_openai_handson/deploysuccess.png)

ここまで来れば、AzureOpenAI のセットアップは完了です！
次に進みましょう！

:::message
初回はこのような表示が出ている可能性があります。

![Azure](/images/azure_openai_handson/aoai_error.png)

その場合は表示されているリンクをクリックすると、Microsoft へリクエストを送ることができます。

![Azure](/images/azure_openai_handson/request_access.png)

ゆうじろうは数時間ぐらいで許可されたので、気長に待ちましょう。

:::

## Azure Bing Search のセットアップ

Azure ポータルの画面へ移動してください。
![azure](/images/azure_openai_langchain_handson/portal.png)

画面上部の検索窓より、`Bing Search`と入力してください。

Bing リソースを選択して、追加 > Bing Search をクリックしてください。

![azure](/images/azure_openai_langchain_handson/portalbing.png)

リソースグループ及び、名前を入力して、
価格レベルは F1 で OK です。

ご契約条件を読み、同意して確認と作成をクリックして、リソースを作成してください。

これで Bing Search のセットアップは完了です。

## Azure Static Web Apps のセットアップ

Azure へログインして、Azure Static Web Apps を作成しましょう。
![Azure](/images/azure_openai_handson/azurestaticwebapps.png)

下記の Github リポジトリをフォークし、Azure Static Web Apps へデプロイします。
!(github)[https://github.com/SatakeYusuke19920527/poc-react-app]

Azure Static Web Apps へデプロイ Azure へ移動し、Azure Static Web Apps のデプロイ設定へ github の URL を指定し、CI/CD を有効にします。

タグ → 確認及び作成より作成してください。

2. 環境変数の設定
   環境変数ファイルの作成及び、Static Web Apps に環境変数を設定 .env.loca ファイルを作成し、以下の内容を記載します。

   ```:env
   AZURE_OPENAI_API_KEY=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
   AZURE_OPENAI_API_INSTANCE_NAME=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
   AZURE_OPENAI_API_DEPLOYMENT_NAME=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
   AZURE_OPENAI_API_VERSION=2023-08-01-preview
   AZURE_OPENAI_API_ENDPOINT=https://xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
   AZURE_BINGSEARCH_API_KEY=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
   ```

3. 動作確認
   github と連携し、自動デプロイが完了した後、Azure Static Web Apps の URL へアクセスします。

# 最後に

最後にずっと残してて課金されないようにお片付けです。

以下、３つの操作を実行すれば OK です。

- Azure OpenAI Studio からデプロイの削除
- Azure よりリソースグループの削除

しっかりお片付けして今日のお勉強は終わりです。

お疲れ様でした。
