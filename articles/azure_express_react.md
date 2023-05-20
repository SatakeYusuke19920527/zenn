---
title: 'Azure Web AppsにReactをDeployする【Azure Web Apps + Express + React】'
emoji: '🌏'
type: 'tech' # tech: 技術記事 / idea: アイデア
topics: ['Azure', 'React', 'Azure Web Apps', 'Express']
published: true
---

# Azure Web Apps に React を Deploy する【Azure Web Apps + Express + React】

## 目次

0. 目標
1. Azure ポータルから Azure Web Apps を準備
2. ローカルで Express のアプリを作成
3. ローカルで React のアプリを作成
4. Express 上で React アプリを動作させる
5. Azure Web Apps 上へ Deploy
6. 最後に

# 0. 目標

今回の目標は Azure Web Apps に React アプリを手動で Deploy できることを目標とします。
コードを Github にも AzureDevOps にも上げられない弊社が苦肉の策で手動で Azure へ Deploy します。

# 1. Azure ポータルから Azure Web Apps を準備

まずは Azure ポータルへアクセスし、リソースグループの作成です。
下記のリソースグループをクリックです。
![Azure Portal](/images/azure_express_react/azure_portal.png)

いい感じのリソースグループ名をつけて作成をクリック
![Azure Portal rg](/images/azure_express_react/rg_make.png)

リソースグループはできましたか？
![Azure Portal rg fin](/images/azure_express_react/rg_fin.png)

では画面上部の検索から`app service`と検索して、App Service へ行き、作成をクリックし、以下のような設定で AppService を作成してください。

![Azure app service](/images/azure_express_react/app_service.png)

これで一旦 AppService の作成は完了です。

# 2. ローカルで Express のアプリを作成

続いて Express のアプリを作成します。
ターミナルを開き、以下のコマンドを実行してください。

Node.js と NPM と共に既定でインストールされる Express Generator を使って、Node.js アプリケーションを作成します。

```bash
npx express-generator server --view ejs
```

アプリケーションのディレクトリに変更し、NPM パッケージをインストールします。

```bash
cd server && npm install
```

デバッグ情報を使用して開発サーバーを起動します。

```bash
DEBUG=myexpressapp:* npm start
```

ブラウザーで、http://localhost:3000 に移動します。 次のような結果が表示されます。

![express](/images/azure_express_react/express.png)

# 3. ローカルで React のアプリを作成

下記のようなディレクトリ構成とします。

```
root
  |-server(Express のコード)
  |-client(React のコード)
```

client のディレクトリに移動し、以下のコマンドを実行します。

```bash
create-react-app client --template-typescript
```

react の雛形アプリが出来たら、以下のコマンドを実行してください。

```bash
npm start
```

以下の画面が表示されれば OK です。
![react](/images/azure_express_react/react.png)

# 4. Express 上で React アプリを動作させる

client ディレクトリで以下のコードを実行してください。

```bash
npm run build
```

`build`とというディレクトリができたかと思います。
その中身を`server/publicの中に貼り付けてください。`

![directory](/images/azure_express_react/directory.png)

`server`ディレクトリへ移動して以下のコマンドを実行してください。

```bash
DEBUG=myexpressapp:* npm start
```

Express のサーバを実行して React の画面が表示されれば OK です。

![react](/images/azure_express_react/react.png)

# 5. Azure Web Apps 上へ Deploy

server ディレクトリを VSCode で開き、Azure の拡張機能を押してください。

![directory](/images/azure_express_react/azure_extent.png)

先ほど作成した web apps を選択してください。

まずは Application Settings に以下の設定を足してください。

![directory](/images/azure_express_react/deploy_setting.png)

`SCM_DO_BUILD_DURING_DEPLOYMENT`を`true`と設定してください。

次に Deploy をしていきます。

![directory](/images/azure_express_react/azure_deploy.png)

![directory](/images/azure_express_react/deploy.png)

Deploy success!の表示がされるまで待ちましょう！

# 6. 最後に

Azure ポータルに戻り、固定のドメインの URL へアクセスしてください。

![directory](/images/azure_express_react/azure_portal_2.png)

こんな感じになりましたか？
![react](/images/azure_express_react/react.png)

# 最後に

上記の問題はプログラミングコミュニティである ProAca が運営しております！
いつでも質問できる環境を整えていますので、ぜひご参加ください！

### ProAca モダン技術専用のプログラミングコミュニティ

#### [ProAca](https://proaca.vercel.app/)
