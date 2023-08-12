---
title: 'まだOpenAI使ったことないの？この記事で全員ハンズオンさせてやんよ！'
emoji: '😎'
type: 'tech' # tech: 技術記事 / idea: アイデア
topics: ['Azure', 'gpt-4', 'openai', 'next.js', 'TypeScript']
published: false
---

# 目次

1. はじめに
2. 今回作成するシステムの概要
3. Azure OpenAI セットアップ
4. Azure DevOps のセットアップから Next.js のコードを push
5. Azure Static Web Apps へ Pipelines を用いて Deploy
6. 動作確認

## はじめに

![Azure logo](/images/azure_openai_handson/azureopenai_logo.png)

昨今ちまたで話題の OpenAI。chatGPT はさらっと触ったけど、API までは触ってないなぁ…という方向けのハンズオン 🖐️ となります。
この記事の目標としては、OpenAI を触ってみたい全てのアゲアゲエンジニアがハンズオン出来ることです。
セットアップで詰まるところはどんどんコメント欄に質問していただいたら、がんがん返していきますので、ご遠慮なく質問してください！

### では、Let's ハンズオン！

## 今回作成するシステムの概要

今回作成するシステムは Azure 上で作成します。
下の図をご覧ください。

![Azure dashboard](/images/azure_openai_handson/zentai.png)

以下の様な技術を使います。

- Azure OpenAI
- Azure Static Web Apps
- Azure DevOps
- Azure Repos
- Azure Pipelines
- Next.js(TypeScript)

盛りだくさんのハンズオンですが、頑張っていきましょう！

## Azure OpenAI セットアップ

まずは Azure OpenAI のセットアップです。全体図で言うとここの部分です。
![Azure dashboard](/images/azure_openai_handson/openai_set.png)

まずは、Azure 上で動作させるので、何がともあれ Azure の無料アカウントを作成してみましょう！
[Azure の無料アカウントを使ってクラウドで構築](https://azure.microsoft.com/ja-jp/free)

Microsoft アカウントを作成して Azure へログインすれば OK です。

こんな感じの画面が出てきたでしょうか。

![Azure](/images/azure_openai_handson/azure_main.png)

何がともあれ、リソースグループを作成しましょう！
上の検索窓に`リソースグループ`と入力して検索してください。

![Azure](/images/azure_openai_handson/resouce_gr.png)

リソースグループの画面に進んだら、左上らへんにある作成をクリックして、リソースグループを作成しましょう！
次へを進んで、タグは別になくて大丈夫です。
![Azure](/images/azure_openai_handson/make_rg.png)

作成できましたか？

![Azure](/images/azure_openai_handson/rg_fin.png)

ここまで作成できたら、また検索窓に`OpenAI`と入力して Azure OpenAI の画面を開いてください。

また、左上らへんにある作成ボタンをクリックです。
リソースグループは先ほど作成したものを指定し、あとは下の画像のように設定してください。
![Azure](/images/azure_openai_handson/make_ai.png)

ネットワークは`インターネットを含むすべてのネットワークがこのリソースにアクセスできます。`を選択してください。
タグは特に何もつけなくて OK です。

作成ボタンをクリックしてください。デプロイが進行するはずです。(5 分ぐらいでしょうか。)
デプロイが完了すれば、リソースへ移動です。

次はモデルを Deploy する必要があります。

画面左上らへんにある`Azure OpenAI Studioへ移動`をクリックしてください。

![Azure](/images/azure_openai_handson/azureopenaistudio.png)

新しいデプロイの作成をクリック → 新しいデプロイの作成をクリックしてください。

以下の様な画面が出てくると思うので、いい感じに入力して作成をクリック

![Azure](/images/azure_openai_handson/modeldeploy.png)

このデプロイはすぐ完了します。
下の画面のようになりましたか？

![Azure](/images/azure_openai_handson/deploysuccess.png)

ここまで来れば、AzureOpenAI のセットアップは完了です！
次に進みましょう！

## Azure DevOps のセットアップから Next.js のコードを push

次は Next.js のコードを Azure DevOps の Repos へ push します。
全体図で言うとここです。
![Azure](/images/azure_openai_handson/devops_next_set.png)

まずは Azure DevOps へアクセス
ゆうじろうは DevOps の画面見ただけでワクワクします

[AzureDevOps](https://azure.microsoft.com/ja-jp/products/devops/?nav=min)

![AzureDevOps](/images/azuredevops_staticapps_next/devops.png)

次にプロジェクトの作成です。

サインインして`+ New Project` からプロジェクトを作成してください。

![AzureDevOps](/images/azuredevops_staticapps_next/createprj.png)

無事、project が create されたら、Welcome to the Project と暖かく迎えてくれます。

![AzureDevOps](/images/azuredevops_staticapps_next/welcome.png)

(ロゴかわいいですよね)

まずはコードを管理するリポジトリを作成します。
画面左側の Repos をクリックしてください。

以下のリポジトリの url が表示されるので、コピーしてローカル環境へ git clone です。

![AzureDevOps](/images/azuredevops_staticapps_next/repo.png)

```bash
git clone [リポジトリのurl]
```

これでローカル環境へリポジトリをクローンできれば AzureDevOps の環境構築は完了です。

## Next.js でフロントエンド構築

ここからはローカル環境で Next.js の環境構築をします。

まずは、ローカルへ clone したディレクトリへ移動してください。

```bash
cd tech-blog
```

中に入れたら容赦なく Next.js のプロジェクトを create です。

```bash
create-next-app . --ts
```

色々聞かれるので、とりあえず設定

```bash
 ➜  tech-blog git:(main) ✗ create-next-app . --ts
✔ Would you like to use ESLint with this project? … No / Yes
✔ Would you like to use Tailwind CSS with this project? … No / Yes
✔ Would you like to use `src/` directory with this project? … No / Yes
✔ Use App Router (recommended)? … No / Yes
✔ Would you like to customize the default import alias? … No / Yes
✔ What import alias would you like configured? … @/*
Creating a new Next.js app in /Users/s.y/dev/05.next/tech-blog.

Using npm.

Initializing project with template: default-tw


Installing dependencies:
- react
- react-dom
- next
- typescript
- @types/react
- @types/node
- @types/react-dom
- tailwindcss
- postcss
- autoprefixer


added 119 packages, and audited 120 packages in 24s

21 packages are looking for funding
  run `npm fund` for details

found 0 vulnerabilities
Success! Created tech-blog at /Users/s.y/dev/05.next/tech-blog
```

success!と言われれば OK です。

では、VSCode を開いて`next.config.js`に追記しましょう。

```javascript
/** @type {import('next').NextConfig} */
const nextConfig = {
  reactStrictMode: true,
  output: 'standalone', // 追記
};

module.exports = nextConfig;
```

output: 'standalone'を追記しました。

> Static Web Apps にデプロイするために Next.js アプリを構成するには、Next.js プロジェクトのスタンドアロン機能を有効にします。
> この手順では、Next.js プロジェクトのサイズを小さくして、Static Web Apps のサイズ制限を下回るようにします。
> 詳細については、スタンドアロンに関するセクション[https://learn.microsoft.com/ja-jp/azure/static-web-apps/deploy-nextjs-hybrid#enable-standalone-feature]を参照してください。

では、せっかくの Next.js なので、サーバサイドレンダリングも追加してみましょう。
pages/index.tsx は以下のコードとなります。

```typescript
export async function getServerSideProps() {
  const data = JSON.stringify({ time: new Date() });
  return { props: { data } };
}

export default function Home({ data }: { data: string }) {
  const serverData = JSON.parse(data);
  return (
    <main
      className={`flex min-h-screen flex-col items-center justify-between p-24`}
    >
      <h1>tech-blog</h1>
      <h2>
        Welcome to{' '}
        <a href="https://nextjs.org">Next.js! The time is {serverData.time}</a>
      </h2>
    </main>
  );
}
```

ここまで実装して、実行した時にエラーがなければ OK です。

```bash
npm run dev
```

![Next.js](/images/azuredevops_staticapps_next/next.png)

問題なければ Azure DevOps の Repos へ push しましょう。
Azure DevOps に戻ったら以下のように code が push されていれば OK です。

![AzureDevOps](/images/azuredevops_staticapps_next/devopsrepos.png)

## AzureStaticWebApps の作成

では、最後にホスティングです。

Azure へログインして、Azure Static Web Apps を作成しましょう。
![Azure](/images/azuredevops_staticapps_next/azurestaticwebapps.png)

East Asia でデプロイの詳細はその他を入力

![Azure](/images/azuredevops_staticapps_next/setting.png)

タグ → 確認及び作成より作成してください。

リソースに移動して、デプロイトークンの管理からデプロイトークンをコピーしておいてください。

では、AzureDepOps に戻り、Azure Pipeline をクリックし、create pipeline をクリック

Azure Repos Git を選択
![Azure](/images/azuredevops_staticapps_next/reposgit.png)

リポジトリを選択したら、Starter Pipeline を選択

![Azure](/images/azuredevops_staticapps_next/stater.png)

yml ファイルへ AzureStaticWebApps の箇所を追記

```yml
# Starter pipeline
# Start with a minimal pipeline that you can customize to build and deploy your code.
# Add steps that build, run tests, deploy, and more:
# https://aka.ms/yaml

trigger:
  - main

pool:
  vmImage: ubuntu-latest

steps:
  - script: echo Hello, world!
    displayName: 'Run a one-line script'

  - script: |
      echo Add other tasks to build, test, and deploy your project.
      echo See https://aka.ms/yaml
    displayName: 'Run a multi-line script'

  # Static Web Appsのデプロイ
  - task: AzureStaticWebApp@0
    inputs:
      app_location: '/'
      api_location: ''
      output_location: '.next'
    env:
      azure_static_web_apps_api_token: $(deployment_token)
```

画面右上の Variables→New Variables より Name に`deployment_token`と入力し、value へ先ほどコピーしたデプロイメントトークンを貼り付けて OK→save をクリック

![Azure](/images/azuredevops_staticapps_next/deploytoken.png)

あとは`Save and Run`を実行して、神に Deploy が成功することを祈りましょう。

![Azure](/images/azuredevops_staticapps_next/deploying.png)

ざわ...
ざわ...

...

#### success!!!

![Azure](/images/azuredevops_staticapps_next/deploysuccess.png)

では、Azure の Azure Static Web Apps に戻って URL にアクセスしてみましょう！

![Azure](/images/azuredevops_staticapps_next/url.png)

無事に Deploy されていることを確認して作業は完了です！

## Azure Static Web Apps へ Pipelines を用いて Deploy

## 動作確認
