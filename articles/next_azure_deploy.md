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

では、ちょっと動作確認してみましょう。
以下のコマンドでアプリを起動してみてください！

```bash
npm run dev
```

http://localhost:3000/

にアクセスして以下の画面が表示されればOKです！


![fin_create_repo](/images/next_azure_deploy/nextjs.png)

ここまでできたら、githubへpushしちゃいましょう！
