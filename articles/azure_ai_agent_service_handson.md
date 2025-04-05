---
title: 'TypeScriptで作るマルチエージェントアプリハンズオン【Azure AI Agent Service】'
emoji: '🤖'
type: 'tech' # tech: 技術記事 / idea: アイデア
topics: ['typescript', 'azure', 'azure-ai-agent-service', 'openai', 'gpt-4o']
published: false
publication_name: microsoft
---

# 本記事の目標

以下の構成を TypeScript で実装し、Azure AI Agent Service を利用して、マルチエージェントアプリを構築します。
![](https://storage.googleapis.com/zenn-user-upload/995f26ce2789-20250405.png)

App Service へホスティングした Express.js を通じて、Agent を管理している AI Foundry 内の AI Agent Service から各種必要なエージェントを呼び出します。

- Knowledge Agent は独自のドキュメントをセットしておき、RAG のようにドキュメントに沿った返答を行います。
- Action Agent は、Function Calling を用いて２種類の Functions を呼び出します。

Azure Functions は今回はモックとして天気の情報とユーザー情報を return するだけのものを用意します。

本記事で Azure AI Agent Service の基本的な使い方を学ぶことが出来ます。

それでは進めていきましょう。

# ハンズオン

## Azure AI Agent Service のセットアップ

以下の記事を参照し、AI Agent を構築していく
https://learn.microsoft.com/ja-jp/azure/ai-services/agents/quickstart?view=azure-dotnet-preview&pivots=ai-foundry-portal

AI Foundry へアクセスし、新しいプロジェクトの作成を選択
![](https://storage.googleapis.com/zenn-user-upload/b74a4ac44f87-20250315.png)

そのまま、プロジェクトの作成をクリック
![](https://storage.googleapis.com/zenn-user-upload/d3e6ed95ba0c-20250315.png)

プロジェクトを作成すると、以下のリソースが作成される
![](https://storage.googleapis.com/zenn-user-upload/ebbbb035c1d8-20250315.png)

図解すると以下
![](https://storage.googleapis.com/zenn-user-upload/44ca7c13bc0f-20250315.png)

Agent Service にて
![](https://storage.googleapis.com/zenn-user-upload/c1cd6869044d-20250315.png)

モデルを Deploy する
![](https://storage.googleapis.com/zenn-user-upload/3324bfbbea20-20250315.png)

今回は GPT-4o と GPT-4o-mini を作成する
![](https://storage.googleapis.com/zenn-user-upload/bc00e91a54f3-20250315.png)
![](https://storage.googleapis.com/zenn-user-upload/8bec0ddcab1d-20250315.png)

各種設定をしてモデルを Deploy
![](https://storage.googleapis.com/zenn-user-upload/8d0b4d06bac2-20250315.png)

Agent が GUI 上で作成することが出来る
![](https://storage.googleapis.com/zenn-user-upload/5b73204b1959-20250315.png)
