---
title: '真剣に技術ブログを作ろう【Azure DevOps × Azure Static Web Apps × Next.js】'
emoji: '🗡️'
type: 'tech' # tech: 技術記事 / idea: アイデア
topics: ['Azure', 'Next.js', 'DevOps', 'StaticWebApps']
published: true
---

# 最近偉い人に言われました。

> ---
>
> エンジニアたるもの、アウトプットしてなんぼだと。
>
> お前がどれだけ勉強しているかなんて知ったことではない。
>
> こいつはエンジニアとしてどの程度使えるかどうかを可視化する必要がある。その為に常日頃アウトプットするんや！と。
>
> ---

確かにおっしゃる通り。ほな作りましょか。ということで自分専用の WEB アプリを作成しようと思います。

# 使用する技術

所属している会社が Azure を使うので、それに合わせて技術選定してみました。
フロントは最近アゲアゲっぽい Next.js を選定
サーバサイドはおそらく Azure の Functions を使って拡張していく予定

まずは Azure DevOps の Repos と Pipeline を使って Next.js の WEB アプリを Deploy するところまでやってみる。

> ---
>
> フロントエンド
> → Next.js(TypeScript)
> サーバサイド
> → TypeScript
> ホスティング
> → Azure Static Web Apps
> DevOps
> → Azure DevOps
> CDCI
> → Azure Pipelines
>
> ---

# では、Let's ハンズオン！

## 目次

1. AzureDevOps でプロジェクトの作成
2. Next.js でフロントエンド構築
3. AzureStaticWebApps の作成
4. 最後に

### AzureDevOps でプロジェクトの作成

まずは、リポジトリが必要。せっかく Azure 使うので、DevOps を使ってみたく、プロジェクトを AzureDevOps 上に作ってみた。

技術ブログは自分がテンション上がる技術を使うのがよさげ
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

![Azure](/images/azuredevops_staticapps_next/deploying.png)

では、Azure の Azure Static Web Apps に戻って URL にアクセスしてみましょう！

![Azure](/images/azuredevops_staticapps_next/url.png)

無事に Deploy されていることを確認して作業は完了です！

お疲れ様でした！

![Azure](/images/azuredevops_staticapps_next/url.png)

## 最後に

Deploy ってよくつまづくからなんでも質問してね。
