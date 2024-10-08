---
title: "RAGってお安くならへんの？"
emoji: "💰"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["RAG", "OpenAI", "Microsoft", "CosmosDB", "AOAI"]
published: false
publication_name: "microsoft"
---

![Azure](/images/azure_can_rag_be_cheaper/img1.png)

# はじめに

### ** 「 RAGってもっと安くならないの？ 」 **

この仕事をしてから7万回ぐらい言われているような気がします。

RAGを作ると、もっと精度高い良いものを作りたい...
でも、同時にコストも出来る限りは抑えたい....

コストとパフォーマンス… 切っても切り離せないトレードオフの関係であり、商売する中で全ての人が直面する課題だと思います。
そしてパフォーマンスをある程度まで抑えてしまえば、コストもかなり抑えれるのではないかと思う今日この頃の私です。

そんな時に**CosmosDB for NoSQLでベクトル検索が可能**になるという発表がMicrosoft Buildであったので、CosmosDBでRAGを作るとどれぐらい安く作れるのか検証してみました。

技術とコスト、そんな二律背反な関係に悩む全てのビジネスマンの為に本記事を書きたいと思います。

# RAG とは
![Azure](/images/azure_can_rag_be_cheaper/img2.png)
Retrieval-Augmented Generation (RAG) は、大規模言語モデル（LLM）によるテキスト生成に、外部情報の検索を組み合わせることで、回答精度を向上させるLLMのフレームワークです。

外部情報の検索を組み合わせることで、大規模言語モデル（LLM）の出力結果を簡単に最新の情報に更新できるようになる効果や、出力結果の根拠が明確になり、事実に基づかない情報を生成する現象（ハルシネーション）を抑制する効果などが期待されています。

上の図で言うと、ChatGPTのようなチャットのアプリが上記のAppServerにホスティングされていると想定してください。
以下のような流れで動作します。
1. 質問「日本の2024年1-3月のGDPの成長率教えて？」
2. Azure AI SearchでDataSourceから作成したIndexを用いて検索し検索結果「-0.7%やで」
3. 検索結果「-0.7%やで」をAppServerにReturn
4. 検索結果「-0.7%やで」と質問「日本の2024年1-3月のGDPの成長率教えて？」をAzure OpenAIに投げて回答を生成
5. 最後、ユーザへ回答をReturn

一言で言うと、Azure AI Searchで検索して、Azure OpenAIで回答を生成してユーザに返すという流れですね。

# 比較する前提条件
- 1日あたりのリクエスト数は1000回
# よく見るRAGアーキテクチャとそのコスト
# こうやったら安くなるかも
# PTUという選択肢
# まとめ
# 参考文献
