---
title: '【2025/4最新】GitHub Copilot Deep Dive 入門から応用、最新情報までキャッチアップ'
emoji: '🐈'
type: 'tech' # tech: 技術記事 / idea: アイデア
topics: ['GitHub', 'GitHub Copilot', 'AI', 'ChatGPT']
published: false
publication_name: microsoft
---

# はじめに

本記事は、GitHub Copilot の入門から応用、最新情報までを網羅的に解説します。GitHub Copilot は、AI を活用したコード補完ツールであり、開発者の生産性を向上させるための強力なサポートを提供します。
GitHub Copilot の基本的な使い方から、実際のプロジェクトでの活用方法、最新の機能やアップデートについて詳しく解説します。

# GitHub Copilot とは

GitHub Copilot は、開発者がコードをより速く、少ない労力で記述できるように支援する AI コーディング アシスタントです。

コンテキストに応じた支援を提供し、開発者が入力中にコードの提案を行います。

これは、行の補完の場合もあれば、まったく新しいコードのブロックの場合もあります。

これにより、開発者は問題解決、共同作業、イノベーションに集中できます。主要なエディターと統合され、GitHub にネイティブに組み込まれているこのツールは、最も広く採用されている AI 開発者ツールであり、仕事の満足度と生産性を向上させます。

https://github.com/features/copilot

# 大規模言語モデルと GitHub Copilot

## 自然言語処理 (NLP)

- コンピュータが人間の言語を理解し、生成する能力を開発する人工知能（AI）の一分野
- **NLP** はテキストや音声データを解析して、その意味や文脈を理解することが目的で、統計的手法や機械学習モデルを使用

- **自然言語理解（NLU）**
  - エンティティの認識
  - 意味理解
- **自然言語生成（NLG）**
  1. 内容の決定
  2. 自然言語の生成
  3. 文法の確認

## GitHub Copilot の対応言語

基本的にはあらゆる言語に対応しているが、情報がより多くネットに掲載されている以下の言語はより得意な傾向がある。

- Python
- JavaScript
- TypeScript
- Ruby
- Go
- C#
- C++

# GitHub Copilot で出来ること

- **コード補完**

  - 開発者がエディタに入力する際に、次に何を入力すべきかを予測し、コードの提案を行う

- **コード生成 / テストコード生成**

  - コメントまたは、コードから機能実現のためのコードスニペットを生成する

- **コードの改善提案**

  - コードに不具合がある場合、問題を解決するための修正案を提供する
  - AI にどのような改善を行いたいかを明確にすることにより、内容に沿った修正案を提供する（リファクタリング、パフォーマンス改善など）

- **コード解説**
  - コードスニペットを指定することで処理の解説を行う

# GitHub Copilot をうまく使う為の Tips

## 関連ファイルを開いておく

- **GitHub Copilot** は、エディターで現在開いているファイルを参考にサーバーに送るプロンプトを生成するため、より的確な提案を受けることが可能

- コンテキスト変数 `#file:` を用いることでファイルを添付することも可能

## 適切なコメントを入れる

- ファイルの最初にファイル内で行われる全体的な処理のコメントを記載

  - 処理の流れ
  - インプット、アウトプット

- 関数の前に関数で行われる処理を記載

- 関数内の変数や、呼び出す関数に利用目的や処理概要などのコメントを記載

## 意味のある命名をする

- 関数などを作成する際、関数名が処理を表す名前にすることで最適な処理の提案を受けることが可能

## ライブラリバージョンを指定

- 回答で用いられるコードが古いライブラリバージョンのコードになっているといった問題が発生するような場合
- 具体的なライブラリ名だけでなくバージョンを指定することで、より意に沿った回答を得られやすくなる

## コンテキストが変わる場合、新規チャット

- **Copilot Chat** は同じスレッド内の履歴も回答生成時の判断材料とするため、コンテキストが異なる場合は新規チャットを開く

# 便利なショートカット

## インラインチャット

- `Ctrl + I`（Mac: `⌘ + i`）
  - ファイル内で実行することにより、インラインチャットを開始
    ![](https://storage.googleapis.com/zenn-user-upload/10924a49fd90-20250409.png)

## コード補完

- `Tab`

  - コード補完された際に実行することで GitHub Copilot の提案を受け入れ
    ![](https://storage.googleapis.com/zenn-user-upload/bab6f504caa5-20250412.png)

- `Esc`
  - コード補完された際に実行することで GitHub Copilot の提案を拒否
    ![](https://storage.googleapis.com/zenn-user-upload/7eb6fed283bc-20250412.png)

## コード補完

- `Alt + ]`（Mac: `option + ]`）

  - コード補完の次の提案を見る
    ![](https://storage.googleapis.com/zenn-user-upload/ec933b7f3134-20250412.png)
    ※候補が切り替わります

- `Alt + [`（Mac: `option + [`）
  - コード補完の前の提案を見る
    ![](https://storage.googleapis.com/zenn-user-upload/ec933b7f3134-20250412.png)
    ※前の候補がみれます

## コード補完

- `Ctrl + →`（Mac: `⌘ + →`）
  - コード補完の結果に対して単語ごとに提案を受け入れる
    ![](https://storage.googleapis.com/zenn-user-upload/bcf145c271e2-20250412.png)

## コード補完

- `Ctrl + Enter`（Mac: `⌘ + Enter`）
  - コード補完された際に実行することで GitHub Copilot から複数の提案を表示
  - 表示されたサジェストで「Accept」することで選択したコードスニペットをファイルに挿入
    ![](https://storage.googleapis.com/zenn-user-upload/689020832be6-20250412.png)

## フォーカス移動

- `Ctrl + Alt + i`（Mac: `⌘ + Ctrl + i`）
  - Copilot チャットにカーソルを移動
    ![](https://storage.googleapis.com/zenn-user-upload/201b0e93d65a-20250412.png)
    右下のチャットのコメント入力の箇所にフォーカスが移動します(意外と便利)

# Agent 機能

GitHub Copilot Chat の中での質問で、質問範囲を限定することが出来ます。

- `@workspace`

  - ワークスペース内のコードやファイルに関連する質問
    ![](https://storage.googleapis.com/zenn-user-upload/078e9ebb012a-20250412.png)

- `@terminal`

  - 統合ターミナルに関連する質問
    ![](https://storage.googleapis.com/zenn-user-upload/adc927af1fd1-20250412.png)

- `@vscode`
  - Visual Studio Code（エディタ）に関連する機能の質問
    ![](https://storage.googleapis.com/zenn-user-upload/e4f40dc3c39b-20250412.png)

# スラッシュコマンド

## @workspace ワークスペースに対するコマンド

- `/doc`

  - ドキュメントコメントを追加

- `/explain`

  - 指定した範囲のコードが実行される際の動作を説明

- `/fix`

  - 選択したコードの問題点、修正点の提案

- `/generate`

  - プロンプトに沿ったコードを生成

- `/optimize`

  - 選択したコードのパフォーマンス改善

- `/tests`

  - コードの単体テストの作成

- `/new`
  - 入力した自然言語に基いた新規ワークスペースの作成

## @vscode エディタに対するコマンド

- `/search`

  - エディタの検索機能で、ワークスペース内のファイルを検索

- `/api`
  - エディタの拡張機能の開発に関する回答を生成

## @terminal ターミナルに対するコマンド

- `/explain`
  - ターミナルで実行された処理の内容を説明

## GitHub Copilot Chat に関連するコマンド

- `/help`

  - Copilot Chat の使用に関するヘルプを表示する

- `/clear`
  - 現在のチャットを終わらせて新規チャットを開始する

# エージェント + スラッシュコマンド

# 最後に

それでは 👋

# スタートアップ企業様向けのお知らせ

日本マイクロソフトでは、スタートアップ企業様向けに、ビジネスを支援するプログラムをご提供しています。  
Azure の無料クレジットが`最大$150,000`もらえるので、是非チェックしてみてください。

https://speakerdeck.com/satakeyusuke19920527/microsoft-for-startups-founders-hub

# 参考文献

https://www.udemy.com/course/github-copilot-fundamental/
