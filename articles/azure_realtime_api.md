---
title: "🗣️VoiceRAG 生成AIとリアルタイム性溢れる会話を嗜む【gpt-4o-realtime-preview】"
emoji: "🚀"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["Azure", "gpt-4o-realtime-preview", "OpenAI", "GPT-4o", "Microsoft"]
published: true
publication_name: microsoft
---

![](https://storage.googleapis.com/zenn-user-upload/8e7d7984d2c5-20241122.png)

# 本記事の概要
Azure OpenAI Service の ```gpt-4o-realtime-preview``` についての解説とVoiceRAGのハンズオンを行います。

# Azure OpenAI Service の　gpt-4o-realtime-preview について

2024年10月1日にOpenAI社から```gpt-4o-realtime-preview```というパブリックベータ版がリリースされました。

音声とオーディオ用 Azure OpenAI GPT-4o Realtime API は、GPT-4o モデル の一つであり、speach to speachという```音声で入力し、音声で出力する```ことができます。 (すごいですよね)

今までだと、ユーザの音声をSpeach to Textの機能を使ってテキストに変換し、OpenAIのAPIにリクエストを送り、その結果をText to Speechを用いて音声に変換していました。
アーキテクチャにするとこんな感じ。

![Azure](/images/azure_next_english_lesson/img16.png)

これでもなかなかのスピードが出せていたのですが、やはり生成AIと会話している感じが出ているなぁという感じでした。

今回、この```gpt-4o-realtime-preview```を使うと、人との会話スピードを実現出来るどころか、食い気味で会話してくれます。

本記事では、```gpt-4o-realtime-preview```の基本的な知識と共に、VoiceRAGのハンズオンを行います🚀

まずは、Azureでの```gpt-4o-realtime-preview```について詳しく見ていきましょう。

# サポートされていいるバージョンと地域
現在 ```gpt-4o-realtime-preview``` はバージョン：2024-10-01-preview のみでサポートされています。
gpt-4o-realtime-preview モデルは、```East US2``` と```Sweden Central```のグローバル デプロイで使用できます。

日本にも早くきてほしいですね...!!

# モデルの制限について
token数の入力と出力、最新のトレーニングデータの学習日は以下の通りです。
| モデル ID                                   | 説明                                     | 最大要求 (トークン)          | トレーニング データ (最大) |
|--------------------------------------------|------------------------------------------|-----------------------------|--------------------------|
| gpt-4o-realtime-preview (2024-10-01-preview) | GPT-4o audio リアルタイム オーディオ処理のためのオーディオ モデル | 入力: 128,000<br>出力: 4,096 | 2023年10月              |

リアルタイム オーディオ (gpt-4o-realtime-preview) の現在のレート制限は、1 分あたりの新しい Websocket 接続の数として定義されます。 
例えば、1 分あたり 6 要求 (RPM) は、1 分あたり 6 つの新しい接続を意味します。 Websocketの接続にて1分あたり6つの接続が可能ということですね。

現時点ではプレビュー版なので、gpt-4o-realtime-preview の使用は新機能開発の為に使うのが良いですね。

# 気になるコスト
RealTime APIのコストは以下の通りです。

| Model                                | Type  | Pricing (1M Tokens)      |
|--------------------------------------|-------|---------------------------|
| GPT-4o-Realtime-Preview-Global       | Text  | Input: ¥767.4001<br>Output: ¥3,069.600004 |
|                                      | Audio | Input: ¥15,348.0001<br>Output: ¥30,696.000031 |

こちらだけ見ると、1M tokenって約100万～150万文字程度とされているので、それだったらこんなもんなのかなと思いきや、GPT-4oと比較してみましょう。

| Model                  | Pricing (1M Tokens)                     | Pricing with Batch API (1M Tokens) |
|------------------------|------------------------------------------|-------------------------------------|
| GPT-4o-2024-08-06 Global | Input: ¥383.70001<br>Cached Input: ¥191.8501<br>Output: ¥1,534.8001 | Not Specified                      |

同じ1MでもGPT-4o-Realtime-Preview-GlobalのAudioだとなかなかいいお値段しますね。

これぐらいの価格でもリアルタイム性を追求したい！というスピード狂の方に是非おすすめしたいモデルとなっております。

# VoiceRAGについて

```gpt-4o-realtime-preview```と掛け合わせて音声でのRAGアーキテクチャも構築出来ます。
通常、RAGアーキテクチャだと返答までに時間がかかるのですが、```gpt-4o-realtime-preview```を使うことで、RAGを使ってもかなりリアルタイム性を追求した会話が可能です。

今回、ハンズオンする構成は以下になります。
![](https://storage.googleapis.com/zenn-user-upload/591233ec3f6f-20241122.png)

アプリケーションの大まかな流れは以下の通りです。
![](https://storage.googleapis.com/zenn-user-upload/7cddcff9d551-20241122.png)

1. デバイスのマイクで音声入力を取得
2. 取得した音声をバックエンドにリアルタイムで送信
3. Azure OpenAI GPT-4o Realtime APIで入力音声をテキストに変換
4. Azure AI Searchを使用してナレッジベースから関連情報を検索
5. 検索結果があればGPT-4o Realtime APIに送信し、応答音声（とテキスト）を生成
6. 生成された音声をリアルタイムにブラウザから出力

Container Appsでは

# VoiceRAG ハンズオン🚀
では、実際にハンズオンです！

以下のリポジトリをcloneしてください。
https://github.com/Azure-Samples/aoai-realtime-audio-sdk/

次にAzure アカウントにログインします。
```bash
azd auth login --use-device-code
```

envファイルを作成して環境変数を読み込みします。
```bash
azd env new
```

新しい azd 環境を作成します。各リソースのリージョンを選択して
```bash
azd up
```

Azure Portalを確認すると、以下のようなリソースが表示されます。
![](https://storage.googleapis.com/zenn-user-upload/d240da9bf179-20241122.png)

では、ローカル環境で動作を確認してみましょう。

以下コマンドでフロントサイド・サーバサイド両方が起動します。
```bash
./scripts/start.sh
```

websocketで接続され、音声通話がReadyの状態になります。
![](https://storage.googleapis.com/zenn-user-upload/e6749f32bea9-20241122.png)

以下のローカルのURLでアクセス出来ると記載されていますね。
それではアクセスしてみましょう。
http://localhost:8765

このような画面が表示されましたでしょうか。
![](https://storage.googleapis.com/zenn-user-upload/8e7d7984d2c5-20241122.png)

(ちょっとデコレーションして私の経営する学習塾のサイトにしてみました)

実際に会話してみたのがこちらです。

https://youtu.be/1evCD3t07P8

# 最後に
今回はAzure OpenAI Service の ```gpt-4o-realtime-preview``` についての解説とVoiceRAGのハンズオンを行いました。
生成AIとここまで滑らかに話せる時代がきて、本当に驚きですね。
皆様のサービス開発に是非取り入れてみてください。

それでは👋

# スタートアップ企業様向けのお知らせ
日本マイクロソフトでは、スタートアップ企業様向けに、ビジネスを支援するプログラムをご提供しています。
Azureの無料クレジットが```最大$150,000```もらえるので、是非チェックしてみてください。

https://speakerdeck.com/satakeyusuke19920527/microsoft-for-startups-founders-hub


# 参考文献

https://learn.microsoft.com/ja-jp/azure/ai-services/openai/realtime-audio-quickstart?pivots=programming-language-javascript