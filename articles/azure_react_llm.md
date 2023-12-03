---
title: 'Azure OpenAIとLangChainを使ってReActの概念を学ぶ'
emoji: '🎁'
type: 'tech' # tech: 技術記事 / idea: アイデア
topics: ['Azure', 'openai', 'langchain', 'next.js', 'ReAct']
published: false
---

# はじめに

![react](/images/azure_react/react.png)

この記事は、Azure OpenAI Studio と LangChain を使って ReAct の概念を学びます。
大規模言語モデルの概念の説明から記載しているので、初学者の方もご安心して読み進めていただければと存じます。

<b>人工知能・大規模言語モデル(LLM)にご興味がある方は、ぜひ一読ください。</b>

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
- <b>GPT3/4 モデルの学習データは 2021 年 9 月までのデータしかない</b>

上記で注目したい点は、<b>GPT3/4 モデルの学習データは 2021 年 9 月までのデータしかない</b>という点です。
これから新しい情報や未知の情報(各企業の独自の情報等)を AI に組み込み、利活用していきたいと考えると LLM を強化する必要があります。

# LLM を強化する方法

前述した通り、大規模言語モデル(LLM)の問題点は<b>人工知能が学習した範囲内でしか回答ができない</b>ということです。
昨今人工知能の需要は高まり、以下のようなニーズがあるかと思います。

- 2021 年以降の情報が必要となる回答も正確に返してほしい
- 外部には公開していない社内の情報への質問にも正確に返してほしい
  ...etc

上記目標を達成する為には LLM を強化させなくてはなりません。

そこで、LLM を強化させる方法として 2 つの方法があります。

1. `Prompt Engineering`
   - LLM に与えるプロンプト内に追加データを含めたり、不足がある場合に外部ツールを呼び出してデータを追加することで回答制度を向上させる方法
     - 以前の情報をヒントにして回答
     - Web で検索して回答...etc
2. `Fine-tuning`
   - LLM 自体を再学習させ、パラメータを調整することで回答制度を向上させる方法
     - GPT3.5 や GPT4 自体を新たに作成...etc

もちろん Fine-tuning の方が精度の高い回答を得られる可能性が高まります。
ただ、LLM 自体が数億・数十億とお金をかけて大量のデータを用いて作成したものであるので、Fine-tuning を実施しようとすると膨大なコスト及びデータ量が必要になります。

そんな時に活躍するのが Prompt Engineering の手法である`ReAct`という概念になります。

# ReAct とは？

`ReAct（REasoning and ACTing）`は、言語モデル（LLM）を使用して思考（推論）と行動を同時に行うための新しい枠組みです。
ReAct の主な目的は、自然言語推論と意思決定のタスクを統合することで、より高度な言語理解と知識活用を可能にすることです。

!(ReAct の論文はこちら)[https://react-lm.github.io/]

# ハンズオンして動作検証および解説

# お片付け

最後にずっと残してて課金されないようにお片付けです。

以下、３つの操作を実行すれば OK です。

- Azure OpenAI Studio からデプロイの削除
- Azure よりリソースグループの削除

しっかりお片付けして本日は終わりです。

お疲れ様でした。