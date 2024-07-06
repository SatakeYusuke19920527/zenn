---
title: "【2024年7月最新🚀】Azure Functions V4 環境構築 TypeScript編"
emoji: "⚡️"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: []
published: true
---

![Azure logo](/images/azure_functions_introduction/pic1.png)

Azure Functions は、Microsoft Azure のサーバーレスコンピューティングサービスです。これを利用することで、インフラ管理を気にすることなくコードを実行することができます。

この記事では、2024年7月時点の最新情報に基づき、TypeScript で Azure Functions V4 の環境を構築する手順をご紹介します。

Azure FunctionsでHTTPトリガーを作成します。

# 前提条件
- Node.js のインストール (version 16.x 以上推奨 筆者はv20.9.0で実施)
- Azure アカウント
- Visual Studio Code (推奨)

# ステップ 1: 開発環境のセットアップ
まず、必要なツールをインストールします。

## 1.1 Node.js のインストール
公式サイトから最新の Node.js をダウンロードし、インストールします。
https://nodejs.jp/

```bash
node -v
```

## 1.2 Azure Functions Core Tools のインストール
Azure Functions Core Tools は、ローカル環境で Azure Functions を開発・実行するためのツールです。

```bash
npm install -g azure-functions-core-tools@4 --unsafe-perm true
```

# ステップ 2: プロジェクトの作成
次に、Azure Functions プロジェクトを作成します。

## 2.1 新しいプロジェクトの作成
以下のコマンドで新しい Azure Functions プロジェクトを作成します。

```bash
func init --typescript
```

## 2.2 関数の作成

以下のコマンドで新しい関数を作成します。
今回はHTTPトリガーを作成します。

```bash
func new --name HttpTrigger  --template "HTTP trigger" --authlevel "anonymous"
```

上記コマンドを実行すると、src/functions ディレクトリに関数が作成されていることを確認してください。

# ステップ 3: TypeScript の設定
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

## 3.2 ディレクトリ構造の設定
src ディレクトリを作成し、その中に TypeScript ファイルを配置します。例えば、src/functions/HttpExample.ts に以下のように記載されていることがわかります。

```typescript
import { app, HttpRequest, HttpResponseInit, InvocationContext } from "@azure/functions";

export async function HttpExample(request: HttpRequest, context: InvocationContext): Promise<HttpResponseInit> {
    context.log(`Http function processed request for url "${request.url}"`);

    const name = request.query.get('name') || await request.text() || 'world';

    return { body: `Hello, ${name}!` };
};

app.http('HttpExample', {
    methods: ['GET', 'POST'],
    authLevel: 'anonymous',
    handler: HttpExample
});
```

# ステップ 4: Functionsの実行
## 4.1 ローカルでの実行
以下のコマンドで TypeScript の関数をローカルで実行します

```bash
npm start
```

![Azure logo](/images/azure_functions_introduction/pic2.png)


ブラウザで  http://localhost:7071/api/HttpExample?name=test_name にアクセスし、関数が正しく動作することを確認します。

以下のような画面になればOKです！
![Azure logo](/images/azure_functions_introduction/pic3.png)

## 4.2 Azure へのデプロイ 
Azure FunctionsをDeployします。
Azure のポータル画面に行き、リソースグループと関数アプリを作成してください。

![Azure logo](/images/azure_functions_introduction/pic4.png)

作成からホスティングプランを消費を選択し、作成します。
設定は以下のようになります。

![Azure logo](/images/azure_functions_introduction/pic5.png)

ApplicationInsightsはOFFにして、後はデフォルトの設定をして関数アプリを作成してください。

![Azure logo](/images/azure_functions_introduction/pic6.png)

出来ましたでしょうか。

そしてDeployです！
ローカルでターミナルより以下のコマンドを実行します。
```bash
func azure functionapp publish <App Name>
```

ターミナルを確認し、Errorがなければデプロイ完了です。

![Azure logo](/images/azure_functions_introduction/pic11.png)

お疲れ様でした！

# まとめ
この記事では、Azure Functions V4をTypeScriptで構築する手順を詳しく解説致しました。

必要な前提条件としてNode.jsのインストール、Azureアカウントの作成、Visual Studio Codeの準備が含まれます。開発環境のセットアップから始まり、新しいプロジェクトとHTTPトリガー関数の作成、TypeScriptの設定方法を説明しています。

ローカル環境での実行確認後、AzureポータルやVisual Studio Codeを使用したデプロイ手順も提供しています。具体的なコマンドや設定ファイルの内容が詳細に示されており、手順通りに進めることで、Azure Functionsを使ったサーバーレスアプリケーションの開発が可能です。

今後の更新や追加情報についても随時お知らせしますので、お見逃しなく🚀


# 参考文献
https://learn.microsoft.com/ja-jp/azure/azure-functions/create-first-function-cli-typescript?tabs=macos%2Cazure-cli%2Cbrowser&pivots=nodejs-model-v4