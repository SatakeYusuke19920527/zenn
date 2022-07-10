---
title: "React勉強部屋その② 状態管理stateについて😎"
emoji: "✨"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["React","状態管理", "JSX"]
published: true
---

# Reactのstateをマスターしよう

## 目次
1. JSXへstateを埋め込もう
2. 状態管理~state~とは
3. いいねボタンを作成してみましょう！
4. Homework


以下の前回講義で、create-react-appにて環境構築が完了し、JSXをいじれるようになったかと思います。

- React勉強部屋その① JSXを修正してみよう
  
[React勉強部屋その① JSXを修正してみよう😎](https://zenn.dev/yusu29/articles/environment_setup)

今回はReactの状態管理で必要不可欠なstateについて学習していきます！


# JSXへstateを埋め込もう
この部分を修正すると、ブラウザの画面へ描画されることはもうOKだと思います。
![JSX説明](/images/fix_jsx/jsx.png)

今回は、React内にJavaScriptの構文を埋め込み、変数を格納していきます。
![JSX説明](/images/about_state/jsx_javascript.png)

赤文字の様に、returnの上部分に変数の式を、returnの中へ中括弧で囲んだ変数を記述すると、JavaScriptをreturn内へ記載することができます。


# 状態管理~state~とは

公式サイトの説明はこんな感じ

> StateはMutableな値を定義することが出来ます。

なめてんのかっていう説明ですが、筆者には全くもってわかりませんでした。

![state説明](/images/about_state/state_explain.png)

App.jsx内で変更する以下の様な値
- いいねボタンのいいね数
- ログインした時のユーザー名

みたいなものを扱うって理解でOKです。

そこでstateを扱う練習をしてみましょう。
下記のコードを写経してみてください。
![state_pra](/images/about_state/pra_state.png)

upボタンを押すと、カウントが上昇していますか？
これがよくあるいいねボタンの原理です。

ここでuseStateについてご説明

![explain_useState](/images/about_state/about_useState.png)

配列の第一引数に変数を、第二引数に変数に値を格納する関数をそれぞれ書いていきます。

理解できましたか？

# いいねボタンを作成してみましょう

以下の様な画面を作成してみてください！
もちろんボタンを押すといいね数が上昇するように作成してくださいね！

![explain_useState](/images/about_state/iine_pra.png)


これができたら本日の内容はOKです！
お疲れ様でした👍

# Homework
1. よくないねボタンを作成し、カウントを減らして行ってください
![よくないね](/images/about_state/yokunaine.png)

2. リセットボタンを作成してください
![リセット](/images/about_state/reset.png)

上記の問題はプログラミングコミュニティであるProAcaが運営しております！

無料でいつでも質問できる環境を整えていますので、ぜひご参加ください！

# ProAca モダン技術専用のプログラミングコミュニティ
http://proaca.tech/