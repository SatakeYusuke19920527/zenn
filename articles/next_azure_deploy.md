---
title: "【Let's ハンズオン🤙】Next.jsをAzure Static Web Appsを使ってデプロイ"
emoji: "🤙"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["Next.js","React", "Azure", "Static Web Apps"]
published: true
---

# 【Let's ハンズオン🤙】 Next.jsをAzureのStatic Web Appsを使ってDeployしていくやで

## 目次
1. Next.jsのプロジェクトを準備
2. Azure PortalからStatic Web Appsを準備
3. Githubと連携させて、Deploy自動化
4. 最後に


# Next.jsのプロジェクトを準備
まずはgithubのプロジェクトを作成しまししょう！
[github](https://github.com/)へアクセスして、Newをクリックです！
![github](/images/next_azure_deploy/github.png)

そしてリポジトリをcreateしましょう！
![create_repo](/images/next_azure_deploy/create_repo.png)

こんな感じの画面が表示されればOKです！
![fin_create_repo](/images/next_azure_deploy/fin_create_repo.png)

リポジトリをローカルへcloneしましょう。
ターミナルを起動して、以下のコマンドを実行です。

```bash
git clone [githubのURL]
```

ローカルにプロジェクトが作成されましたか？
その後は作成されたディレクトリへ移動です。

```bash
cd [作成されたディレクトリ]
```

そこで、Next.jsのプロジェクトを作成するために以下のコマンドを打ちましょう！

```bash
npx create-next-app@latest . --ts
```

Success!と出ればOKです！

ちょっとだけ、コードを変更。


> pages/index.tsxのImageをimgに変更してあげてください。

※これをしとかないと、後々Deployで失敗します...

では、ちょっと動作確認してみましょう。
以下のコマンドでアプリを起動してみてください！

```bash
npm run dev
```

http://localhost:3000/

にアクセスして以下の画面が表示されればOKです！


![fin_create_repo](/images/next_azure_deploy/nextjs.png)

ここまでできたら、githubへpushしちゃいましょう！

できましたか？

![fin_create_repo](/images/next_azure_deploy/github_push.png)

# Azure PortalからStatic Web Appsを準備
次はAzureポータルからAzureStaticWebAppsを作成しましょう！

Azureportalのログイン画面はこんな感じ。

![Azure_portal](/images/next_azure_deploy/azure_potral.png)

全てのサービ→静的Webサービスを選択してください！

そして静的Webサービスを作成しましょう！

![staticwebapps](/images/next_azure_deploy/staticwebapps.png)

そして静的Webサービスの初期設定はこんな感じ。

![settings](/images/next_azure_deploy/setting.png)

![settings](/images/next_azure_deploy/setting2.png)

![settings](/images/next_azure_deploy/setting3.png)

設定ができたら作成をクリック！

![settings](/images/next_azure_deploy/deploy_fin.png)

こんな感じの画面がでたらOKです！

ここからはGithubと連携させてDeployしていくための設定をしていきます。

# Githubと連携させて、Deploy自動化
それでは、先ほど作成したGithubの画面に戻ってください。

![settings](/images/next_azure_deploy/github2.png)

Actionsを見ると、Deployがエラーとなっているはずです。

こんなことでは落ち込みません。
想定内です。

では、ターミナルから以下のコマンドを実行してください。

```bash
git pull
```

こんなymlファイルが生成されましたか？

![yml](/images/next_azure_deploy/ymlfile.png)

ここにNext.jsのDeployの設定を記載していきます

jobsのところをこんな感じで書き換えてみてください。

```yml
jobs:
  build_and_deploy_job:
    if: github.event_name == 'push' || (github.event_name == 'pull_request' && github.event.action != 'closed')
    runs-on: ubuntu-latest
    name: Build and Deploy Job
    steps:
      - uses: actions/checkout@v2
        with:
          submodules: true
      - name: Setup Node.js
        uses: actions/setup-node@v2
        with:
          node-version: 16.x

      - name: Install NPM packages
        run: npm i

      - name: Build Next.js app
        run: npm run build && npm run export

      - name: Build And Deploy
        id: builddeploy
        uses: Azure/static-web-apps-deploy@v1
```

そして、package.jsonのscriptsの箇所へnext exportを追記してください。

```json
"scripts": {
    "dev": "next dev",
    "build": "next build",
    "start": "next start",
    "export": "next export",
    "lint": "next lint"
}
```

書き換えれたら、再度、ymlファイルとpackage.jsonをgithubへpushです！

その後、githubへアクセスし、Actionのところからdeployがうまくいくことを見守っててください。

githubのActionsでこんな感じで表示されたら拍手です👏

![yml](/images/next_azure_deploy/deploy_success.png)

Azureポータルへ移動しましょう。
urlをクリックして...
![portal](/images/next_azure_deploy/deploy_portal.png)

この画面が出てきたらOKです！

![deploy](/images/next_azure_deploy/next_deploy.png)

後はソースコード更新→githubへpushすると自動的にデプロイしてくれます！

### お疲れ様でした！



