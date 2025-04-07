---
title: 'TypeScriptで作るマルチエージェントアプリハンズオン🚀'
emoji: '🤖'
type: 'tech' # tech: 技術記事 / idea: アイデア
topics: ['typescript', 'azure', 'azure-ai-agent-service', 'openai', 'gpt-4o']
published: false
publication_name: microsoft
---

![](https://storage.googleapis.com/zenn-user-upload/5f7f40f505d9-20250407.png)

# はじめに

今回は Azure AI Agent Service を使って、シンプルなマルチエージェントアプリを TypeScript で実装していきます。
python での実装記事はかなり多く出てきているのですが、TypeScript になるとあまり情報が出てこなかった為、今回は TypeScript でハンズオンをしてみました。

是非ご参考ください 👍

# 本記事の目標

以下の構成を TypeScript で実装し、Azure AI Agent Service を利用して、マルチエージェントアプリを構築します。
![](https://storage.googleapis.com/zenn-user-upload/995f26ce2789-20250405.png)

1. App Service へホスティングした Express.js を通じて、AI Agent Service SDK を使用
2. Agent を管理している AI Foundry 内の Azure AI Agent Service から各種必要な Agent を呼び出し
3. AI Agent より帰ってきた結果を Express.js へ return

今回準備する AI Agent は 2 体

- Knowledge-Agent
  - 独自のドキュメントをセットしておき、RAG のようにドキュメントに沿った返答を行います。
- Action-Agent
  - Function Calling を用いて２種類の Functions を呼び出します。
  - Azure Functions は今回はモックとして天気の情報とユーザー情報を return するだけのものを用意します。

本記事で Azure AI Agent Service の基本的な使い方を学ぶことが出来ます。

それでは進めていきましょう。

# ハンズオン

## Azure AI Agent Service のセットアップ

![](https://storage.googleapis.com/zenn-user-upload/dc1e825145d7-20250407.png)

以下の記事を参照し、AI Agent を構築していく

https://learn.microsoft.com/ja-jp/azure/ai-services/agents/quickstart?view=azure-dotnet-preview&pivots=ai-foundry-portal

AI Foundry へアクセスし、新しいプロジェクトの作成を選択
![](https://storage.googleapis.com/zenn-user-upload/b74a4ac44f87-20250315.png)

そのまま、プロジェクトの作成をクリック
![](https://storage.googleapis.com/zenn-user-upload/d3e6ed95ba0c-20250315.png)

プロジェクトを作成すると、以下のリソースが作成される
![](https://storage.googleapis.com/zenn-user-upload/ebbbb035c1d8-20250315.png)

図解すると以下。Azure AI hub の下に AI project が作成され、その下に KeyVault や AI Search, AI Services, Storage Account が作成される。
![](https://storage.googleapis.com/zenn-user-upload/44ca7c13bc0f-20250315.png)

Azure AI Agent Service にてリソースを選択し、作成を進めていく。
![](https://storage.googleapis.com/zenn-user-upload/c1cd6869044d-20250315.png)

モデル を Deploy する
![](https://storage.googleapis.com/zenn-user-upload/3324bfbbea20-20250315.png)

今回は GPT-4o を Deploy する
![](https://storage.googleapis.com/zenn-user-upload/bc00e91a54f3-20250315.png)

Agent が GUI 上で作成することが出来る。
これで AI Agent Service のセットアップは完了です。
![](https://storage.googleapis.com/zenn-user-upload/5b73204b1959-20250315.png)

# Azure Functions のセットアップ

![](https://storage.googleapis.com/zenn-user-upload/f1b40f2d2076-20250407.png)

以下の記事を参考にし、Azure Functions のセットアップを行います。

https://zenn.dev/yusu29/articles/azure_functions_introduction

2 つの Azure Functions を作成します。

- GetWeather
  - 天気の情報を返すだけのモック
- GetUserInfo
  - ユーザーの情報を返すだけのモック

## 開発環境のセットアップ

まず、必要なツールをインストールします。

### Node.js のインストール

公式サイトから最新の Node.js をダウンロードし、インストールします。
https://nodejs.jp/

```bash
node -v
```

### Azure Functions Core Tools のインストール

Azure Functions Core Tools は、ローカル環境で Azure Functions を開発・実行するためのツールです。

```bash
npm install -g azure-functions-core-tools@4 --unsafe-perm true
```

## プロジェクトの作成

次に、Azure Functions プロジェクトを作成します。

### 新しいプロジェクトの作成

以下のコマンドで新しい Azure Functions プロジェクトを作成します。

```bash
func init --typescript
```

## 関数の作成

以下のコマンドで新しい関数を作成します。
今回は HTTP トリガーを作成します。

```bash
func new --name get_wheather  --template "HTTP trigger" --authlevel "anonymous"
func new --name get_users  --template "HTTP trigger" --authlevel "anonymous"
```

上記コマンドを実行すると、src/functions ディレクトリに関数が作成されていることを確認してください。

## TypeScript の設定

プロジェクトを TypeScript で実装するための設定を行います。

3.1 TypeScript の設定
tsconfig.json ファイルを作成し、以下の内容を記述します。

```json
{
  "compilerOptions": {
    "module": "commonjs",
    "target": "es6",
    "outDir": "dist",
    "rootDir": ".",
    "sourceMap": true,
    "strict": false
  }
}
```

## 各関数の実装

今回はモックの関数なので、簡単な Azure Functions の実装を行います。

```typescript:get_wheather.ts
import {
  app,
  HttpRequest,
  HttpResponseInit,
  InvocationContext,
} from '@azure/functions';

export async function get_weather(
  request: HttpRequest,
  context: InvocationContext
): Promise<HttpResponseInit> {
  // ログ出力
  context.log(`Mocked weather function called. URL: "${request.url}"`);

  // 例: 適当な天気・気温をランダムで返す
  // ※ 完全に固定値にしたい場合は、下記のロジックを除いて
  //   "{ weather: '晴れ', temp: 25 }" のようにベタ書きでもOK
  const weatherCandidates = ['晴れ', '曇り', '雨', '雷雨'];
  const randomWeather =
    weatherCandidates[Math.floor(Math.random() * weatherCandidates.length)];

  // 15〜30度の範囲でランダム
  const randomTemp = (Math.random() * 15 + 15).toFixed(1);

  // モックのレスポンスデータ
  const data = {
    city: 'Osaka',
    weather: randomWeather,
    temperature: `${randomTemp}℃`,
  };

  return {
    status: 200,
    // JSON文字列として返す
    body: JSON.stringify({
      message: `モックの大阪の天気: ${randomWeather}, 気温: ${randomTemp}℃`,
      data,
    }),
    headers: { 'Content-Type': 'application/json' },
  };
}

// Azure Functions に登録 (Azure Functions Core Tools で func start したりして動作確認)
app.http('get_weather', {
  methods: ['GET', 'POST'],
  authLevel: 'anonymous',
  handler: get_weather,
});

```

```typescript:get_users.ts
import {
  app,
  HttpRequest,
  HttpResponseInit,
  InvocationContext,
} from '@azure/functions';

export async function get_users(
  request: HttpRequest,
  context: InvocationContext
): Promise<HttpResponseInit> {
  context.log(`Http function processed request for url "${request.url}"`);

  // 適当なユーザー情報を用意
  const mockUsers = [
    {
      id: 1,
      name: 'Alice',
      email: 'alice@example.com',
    },
    {
      id: 2,
      name: 'Bob',
      email: 'bob@example.com',
    },
    {
      id: 3,
      name: 'Charlie',
      email: 'charlie@example.com',
    },
  ];

  return {
    status: 200,
    body: JSON.stringify({
      message: 'Mocked user list',
      data: mockUsers,
    }),
    headers: {
      'Content-Type': 'application/json',
    },
  };
}

app.http('get_users', {
  methods: ['GET', 'POST'],
  authLevel: 'anonymous',
  handler: get_users,
});

```

## Functions の実行

### ローカルでの実行

以下のコマンドで TypeScript の関数をローカルで実行します

```bash
npm start
```

![](https://storage.googleapis.com/zenn-user-upload/68cfe5ac32b0-20250407.png)

ブラウザでそれぞれ http://localhost:7071/api/get_users と http://localhost:7071/api/get_weather にアクセスし、関数が正しく動作することを確認します。

## Azure へのデプロイ

Azure Functions を Deploy します。
Azure のポータル画面に行き、リソースグループと関数アプリを作成してください。

![Azure logo](/images/azure_functions_introduction/pic4.png)

作成からホスティングプランを消費を選択し、作成します。
設定は以下のようになります。

![Azure logo](/images/azure_functions_introduction/pic5.png)

ApplicationInsights は OFF にして、後はデフォルトの設定をして関数アプリを作成してください。

![Azure logo](/images/azure_functions_introduction/pic6.png)

出来ましたでしょうか。

ここまでできたら、VSCode から Azure Functions をデプロイします。
![](https://storage.googleapis.com/zenn-user-upload/b6ceb749e10e-20250407.png)

こちらクリックして Deploy 完了です。
Azure Portal より、二つの関数が作成されていることを確認してください。

![](https://storage.googleapis.com/zenn-user-upload/5bad622f3471-20250407.png)

# Express.js のセットアップ

# 最後に

今回の構成で Azure AI Agent Service を使って、マルチエージェントアプリを構築することが出来ました。

今後は AI Agent Service の SDK もより多くのコネクタに対応するようになりそうなので、引き続き check していきたいと思います。

それでは 👋

# スタートアップ企業様向けのお知らせ

日本マイクロソフトでは、スタートアップ企業様向けに、ビジネスを支援するプログラムをご提供しています。
Azure の無料クレジットが`最大$150,000`もらえるので、是非チェックしてみてください。

https://speakerdeck.com/satakeyusuke19920527/microsoft-for-startups-founders-hub
