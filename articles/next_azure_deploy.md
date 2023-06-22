---
title: "【Let's ハンズオン🤙】Next.jsをAzure Static Web Appsを使ってWEBサイトをデプロイ"
emoji: '🤙'
type: 'tech' # tech: 技術記事 / idea: アイデア
topics: ['Next.js', 'React', 'Azure', 'Static Web Apps']
published: true
---

# 【Let's ハンズオン 🤙】 Next.js を Azure の Static Web Apps を使って WEB サイトを Deploy していくやで

## 目次

1. Next.js のプロジェクトを準備
2. Azure Portal から Static Web Apps を準備
3. Github と連携させて、Deploy 自動化
4. 最後に

# Next.js のプロジェクトを準備

まずは github のプロジェクトを作成しまししょう！
[github](https://github.com/)へアクセスして、New をクリックです！
![github](/images/next_azure_deploy/github.png)

そしてリポジトリを create しましょう！
![create_repo](/images/next_azure_deploy/create_repo.png)

こんな感じの画面が表示されれば OK です！
![fin_create_repo](/images/next_azure_deploy/fin_create_repo.png)

リポジトリをローカルへ clone しましょう。
ターミナルを起動して、以下のコマンドを実行です。

```bash
git clone [githubのURL]
```

ローカルにプロジェクトが作成されましたか？
その後は作成されたディレクトリへ移動です。

```bash
cd [作成されたディレクトリ]
```

そこで、Next.js のプロジェクトを作成するために以下のコマンドを打ちましょう！

```bash
npx create-next-app@latest . --ts
```

Success!と出れば OK です！

ちょっとだけ、コードを変更。

> pages/index.tsx の Image を img に変更してあげてください。

※これをしとかないと、後々 Deploy で失敗します...

では、ちょっと動作確認してみましょう。
以下のコマンドでアプリを起動してみてください！

```bash
npm run dev
```

http://localhost:3000/

にアクセスして以下の画面が表示されれば OK です！

![fin_create_repo](/images/next_azure_deploy/nextjs.png)

ここまでできたら、github へ push しちゃいましょう！

できましたか？

![fin_create_repo](/images/next_azure_deploy/github_push.png)

# Azure Portal から Static Web Apps を準備

次は Azure ポータルから AzureStaticWebApps を作成しましょう！

Azureportal のログイン画面はこんな感じ。

![Azure_portal](/images/next_azure_deploy/azure_potral.png)

全てのサービ → 静的 Web サービスを選択してください！

そして静的 Web サービスを作成しましょう！

![staticwebapps](/images/next_azure_deploy/staticwebapps.png)

そして静的 Web サービスの初期設定はこんな感じ。

![settings](/images/next_azure_deploy/setting.png)

![settings](/images/next_azure_deploy/setting2.png)

![settings](/images/next_azure_deploy/setting3.png)

設定ができたら作成をクリック！

![settings](/images/next_azure_deploy/deploy_fin.png)

こんな感じの画面がでたら OK です！

ここからは Github と連携させて Deploy していくための設定をしていきます。

# Github と連携させて、Deploy 自動化

それでは、先ほど作成した Github の画面に戻ってください。

![settings](/images/next_azure_deploy/github2.png)

Actions を見ると、Deploy がエラーとなっているはずです。

こんなことでは落ち込みません。
想定内です。

では、ターミナルから以下のコマンドを実行してください。

```bash
git pull
```

こんな yml ファイルが生成されましたか？

![yml](/images/next_azure_deploy/ymlfile.png)

ここに Next.js の Deploy の設定を記載していきます

jobs のところをこんな感じで書き換えてみてください。

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

そして、package.json の scripts の箇所へ next export を追記してください。

```json
"scripts": {
    "dev": "next dev",
    "build": "next build",
    "start": "next start",
    "export": "next export",
    "lint": "next lint"
}
```

書き換えれたら、再度、yml ファイルと package.json を github へ push です！

その後、github へアクセスし、Action のところから deploy がうまくいくことを見守っててください。

github の Actions でこんな感じで表示されたら拍手です 👏

![yml](/images/next_azure_deploy/deploy_success.png)

Azure ポータルへ移動しましょう。
url をクリックして...
![portal](/images/next_azure_deploy/deploy_portal.png)

この画面が出てきたら OK です！

![deploy](/images/next_azure_deploy/next_deploy.png)

後はソースコード更新 →github へ push すると自動的にデプロイしてくれます！

### お疲れ様でした！
