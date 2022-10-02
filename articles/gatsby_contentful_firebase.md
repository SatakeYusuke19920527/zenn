---
title: "Gatsby + Firebaseでブログサイトを作成してみる"
emoji: "📖"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["gatsby","contentful", "Firebase","React"]
published: true
---

# Gatsby + Contentful + Firebaseでブログサイトを作成してみる

![gatsby](/images/gatsby_contentful_firebase/gatsby_logo.png)

こんにちは。ゆうじろうです。
Gatsbyが以前から興味はあったのですが、なかなか触る機会がなかったので、自分自身のポートフォリオサイトを作るついでに触ってみようと思います。

# TypeScript + Gatsbyの構成で環境構築

まずは環境構築。
以下のGatsbyとTypeScriptのスターターを使用しました。
https://www.gatsbyjs.com/starters/jpedroschmitz/gatsby-starter-ts


以下のコマンドを叩きます。

```bash
npx gatsby new gatsby-starter-ts https://github.com/jpedroschmitz/gatsby-starter-ts
```

こんな感じのプロジェクトができていれば成功です！

![gatsby](/images/gatsby_contentful_firebase/gatsby_directory.png)

では、一旦動作確認しましょう。

```bash
gatsby develop
```

以下のシンプルな画面がでれば完璧です！

![gatsby](/images/gatsby_contentful_firebase/hello-typescript.png)

# firebse　設定
以下、Firebaseのサイトへ移動しましょう。
https://firebase.google.com/?hl=ja

コンソールへ移動→プロジェクトを追加から、プロジェクトを追加してみてください。

ここからはコンソールにて設定します。

以下、２つの機能をインストールしてください。

```bash
npm install firebase
npm install -g firebase-tools
```

そこから、初期化を開始します。

```bash
firebase init
```

firebase initが終われば、デプロイの実行です。

```bash
firebase deploy
```

これで、webサイトへアクセスできればOKです。

## お疲れ様でした。





