---
title: "🗣️VoiceRAG🗣️ 生成AIとリアルタイム性溢れる会話を嗜む【gpt-4o-realtime-preview】"
emoji: "🗣️"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["Azure", "gpt-4o-realtime-preview", "OpenAI", "GPT-4o", "Microsoft"]
published: false
publication_name: microsoft
---

# 本記事の概要
Azure OpenAI Service の　gpt-4o-realtime-preview についての解説とVoiceRAGのハンズオンを行います。

# Azure OpenAI Service の　gpt-4o-realtime-preview について

2024年10月1日にOpenAI社から```gpt-4o-realtime-preview```というパブリックベータ版がリリースされました。

音声とオーディオ用 Azure OpenAI GPT-4o Realtime API は、GPT-4o モデル の一つであり、speach to speachという"音声で入力し、音声で出力する"ことができます。 

今までだと、ユーザの音声をSpeach to Textの機能を使ってテキストに変換し、OpenAIのAPIにリクエストを送り、その結果をText to Speechを用いて音声に変換していました。
これでもなかなかのスピードが出せていたのですが、やはり生成AIと会話している感じが出ているなぁという感じでした。

今回、この```gpt-4o-realtime-preview```を使うと、人との会話スピードを実現出来るどころか、食い気味で会話してくれます。

では、Azureでの```gpt-4o-realtime-preview```について詳しく見ていきましょう。

# サポートされていいるバージョンと地域
現在 ```gpt-4o-realtime-preview``` はバージョン：2024-10-01-preview のみでサポートされています。
gpt-4o-realtime-preview モデルは、```East US2``` と```Sweden Central```のグローバル デプロイで使用できます。

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


# 参考文献

https://learn.microsoft.com/ja-jp/azure/ai-services/openai/realtime-audio-quickstart?pivots=programming-language-javascript