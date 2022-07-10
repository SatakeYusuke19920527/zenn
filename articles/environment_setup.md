---
title: "React勉強部屋その⓪ React環境構築方法 👍"
emoji: "✨"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["React","環境構築", "2022最新版"]
published: true
---

# React環境構築方法

## 目次
1. Node.js のインストール
2. create-react-app のインストール
3. Reactアプリ実行

![ReactIcon](/images/environment_setup/Reactimage.png)


### Node.js のインストール

以下のリンクからNode.jsをインストールしましょう！
LTS版(Long Time Support版)でOKです！


[Node.jsのインストール](https://nodejs.org/ja/)

![Node.js](/images/environment_setup/NodeImage.png)

* 以下の順番です
1. [Node.jsのインストール](https://nodejs.org/ja/)よりLTS版をダウンロード
2. Node.jsのインストーラを実行
3. ようこそNode.jsインストーラーへ →　続ける
4. 使用許諾契約　→ 続ける　→ 同意する
5. インストール先の選択　→　続ける
6. "XXXXX"に標準インストール　→ インストール
7. インストールが完了しました　→ 閉じる

#### Nodeがインストールできたかどうか確認

コマンドプロンプト(Windows)もしくはTerminal(Mac)を開いて、以下のコマンドを叩きます。

```bash
node -v
```

以下の様にバージョン番号が出てきたらインストール成功です。

```bash
v16.14.0
```

npm(node package manager)もインストールされているかどうか確認

```bash
npm -v
```

こちらも以下の様にバージョン番号が出てきたらインストール成功です。

```bash
8.3.1
```


### create-react-app のインストール

次はReactの環境を簡単に作成できるcreate-react-appをインストールします。

```bash
npm install -g create-react-app
```

では、デスクトップなどにReactのプロジェクトを作成していきましょう。

```bash
 create-react-app react-app
```

Reactの雛形のインストールが終わったらHappy Hacking!と祈ってくれるはずです。


### Reactアプリ実行

作成したプロジェクトに移動しましょう。

```bash
 cd react-app
```

移動したら、Reactアプリ開始のコマンドを実行です！

```bash
 npm start
```

以下の様な画面がでたら成功です。

![React環境構築完了](/images//environment_setup/createreactappImage.png)


これでReactアプリの環境構築が完了です！
🚀皆様お疲れ様でした🚀

# Homework
1. [Edit src/App.js and save to reload]のところを[Hello world]に変えてみてください
2. 背景の色をピンク色[#F69896]へ変更してみてください
3. タブのタイトルの名前を[ReactApp]から[MyApp]へ変えてみてください

上記の問題はプログラミングコミュニティであるProAcaが運営しております！
いつでも質問できる環境を整えていますので、ぜひご参加ください！

# ProAca モダン技術専用のプログラミングコミュニティ
http://proaca.tech/
