---
title: 'GitHub Copilot を完全に使いこなす会'
emoji: '📖'
type: 'tech' # tech: 技術記事 / idea: アイデア
topics: ['GitHub', 'GitHub Copilot', 'AI', 'ChatGPT', 'openai']
published: true
publication_name: microsoft
---

![](https://storage.googleapis.com/zenn-user-upload/9eb157a75aca-20250412.png)

# はじめに

この記事は GitHub Copilot の Tips を詰め込んだ記事になります。

GitHub Copilot を普段使っているが、コード補完しか使ってない方や、これから使おうと思っている方に向けて Tips をまとめて紹介する記事になります。

是非日々の開発ライフにお役立てください 🚀

# GitHub Copilot とは？

GitHub Copilot は、開発者がコードをより速く、少ない労力で記述できるように支援する AI コーディング アシスタントです。

コンテキストに応じた支援を提供し、開発者が入力中にコードの提案を行います。

これは、行の補完の場合もあれば、まったく新しいコードのブロックの場合もあります。

これにより、開発者は問題解決、共同作業、イノベーションに集中できます。主要なエディターと統合され、GitHub にネイティブに組み込まれているこのツールは、最も広く採用されている AI 開発者ツールであり、仕事の満足度と生産性を向上させます。

https://docs.github.com/ja/copilot

# 大規模言語モデルと GitHub Copilot

## 自然言語処理 (NLP)

- コンピュータが人間の言語を理解し、生成する能力を開発する人工知能（AI）の一分野で **NLP** はテキストや音声データを解析して、その意味や文脈を理解することが目的。統計的手法や機械学習モデルを使用します。
- 自然言語理解と自然言語生成に分けることが出来ます。

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

また、対応しているエディタにもかなりの種類があり、我々の開発ライフを支えてくれます。
| # | エディタ・ツール名 |
|----------|-------------------|
| ![GitHub](https://github.githubassets.com/assets/GitHub-Mark-ea2971cee799.png) | GitHub |
| ![Visual Studio Code](https://code.visualstudio.com/assets/apple-touch-icon.png) | Visual Studio Code |
| ![Visual Studio](https://visualstudio.microsoft.com/wp-content/uploads/2021/10/Product-Icon.svg) | Visual Studio |
| ![Xcode](https://developer.apple.com/assets/elements/icons/xcode-12/xcode-12-96x96_2x.png) | Xcode |
| ![JetBrains](https://resources.jetbrains.com/storage/products/company/brand/logos/jb_beam.svg) | JetBrains IDE（IntelliJ IDEA など） |
| ![Neovim](https://neovim.io/images/logo@2x.png) | Neovim |
| ![Azure](https://azure.microsoft.com/svghandler/azure?width=600&height=315) | Azure Data Studio |
| ![Eclipse](https://www.eclipse.org/eclipse.org-common/themes/solstice/public/images/logo/eclipse-foundation-white-orange.svg) | Eclipse |

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

# GitHub Copilot の価格

### GitHub Copilot 個人向けプラン比較表（Individuals）

| プラン | 料金                  | 含まれる機能・内容                                                                                                                                                                                                                       |
| ------ | --------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Free   | $0/月                 | - 月 50 回の Agent モードまたはチャットリクエスト<br>- 月 2,000 回のコード補完<br>- Claude 3.5 Sonnet、GPT-4o などのモデルにアクセス可能                                                                                                 |
| Pro    | $10/月 または $100/年 | **Free の全機能に加え**<br>- GPT-4o を利用した Agent モードやチャットが無制限<br>- コード補完無制限<br>- コードレビュー、Claude 3.7 Sonnet, o1 などにアクセス可能<br>- 最新モデルへのプレミアムリクエストが Free の 6 倍、追加購入も可能 |
| Pro+   | $39/月 または $390/年 | **Pro の全機能に加え**<br>- GPT-4.5 を含むすべてのモデルにアクセス可能<br>- 最新モデルへのプレミアムリクエストが Free の 30 倍、追加購入も可能                                                                                           |

こう見ると、Enterprise のプランを契約しなくても全てのモデルにアクセス出来る Pro+ のプランはかなりお得ですね。

### GitHub Copilot 法人向けプラン比較表（Business / Enterprise）

| プラン     | 料金                     | 含まれる機能・内容                                                                                                                                                                                                                                                                                              |
| ---------- | ------------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Business   | $19/月（ユーザーあたり） | - GPT-4o を利用した Agent モードやチャットが無制限<br>- コード補完無制限<br>- コードレビュー、Claude 3.5/3.7 Sonnet、o1 などにアクセス可能<br>- ユーザーあたり月 300 回の最新モデルへのプレミアムリクエスト、追加購入も可能<br>- ユーザー管理と利用状況メトリクス<br>- 知的財産補償およびデータプライバシー保護 |
| Enterprise | $39/月（ユーザーあたり） | **Business の全機能に加え**<br>- GPT-4.5 を含むすべてのモデルにアクセス可能<br>- 最新モデルへのプレミアムリクエストが Business の 3.33 倍、追加購入も可能                                                                                                                                                       |

# GitHub Copilot をうまく使う為の Tips

## 関連ファイルを開いておくと生成 AI のレコメンド精度が上がる

- **GitHub Copilot** は、エディターで現在開いているファイルを参考にサーバーに送るプロンプトを生成するため、より的確な提案を受けることが可能です。

- コンテキスト変数 `#file:` を用いることでファイルを添付することも可能だったりします。

## 適切なコメントを入れる

- こちら意外と非常に重要。ファイルの最初にファイル内で行われる全体的な処理のコメントを記載することで、生成 AI のレコメンドの精度がちゃんと上がります。GitHub Copilot にも関数にコメントつけてというとコメントつけてくれるので、日々の実装はコメントをしっかりつけながら進めていくのが Good です。

- 特に処理の流れやインプット、アウトプット、関数の前に関数で行われる処理を記載することや関数内の変数や、呼び出す関数に利用目的や処理概要などのコメントを記載することが大事です。

## 意味のある命名をする

- リーダブルコードを読む我々ならあたりまえのことですが、数年働くと不思議と変な名前をつけちゃう時もあることは知っています。
- 関数などを作成する際、関数名が処理を表す名前にすることで最適な処理の提案精度がしっかり上がります。

## ライブラリバージョンを指定

- node だったり、python だったり、回答で用いられるコードが古いライブラリバージョンのコードになっているといった問題が発生するような場合、具体的なライブラリ名だけでなくバージョンを指定することで、より意に沿った回答を得られやすくなる傾向にあります。

## コンテキストが変わる場合、新規チャット

- **Copilot Chat** は同じスレッド内の履歴も回答生成時の判断材料とするため、コンテキストが異なる場合は新規チャットを開くことが大事だったりします。 /clear コマンドで新規チャットを開くことが出来ます。(後ほど説明します。)

# 便利なショートカット一覧

ここでは普段の開発で使える便利なショートカットを紹介します。
私自身まとめていると使いこなせてなかったなと思う場面もあったので、是非参考にしてみてください。

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

コメントを記載してとか指示送れるのはかなり便利。
![](https://storage.googleapis.com/zenn-user-upload/9ba8de3c57d2-20250412.png)

- `@terminal`

  - 統合ターミナルに関連する質問

![](https://storage.googleapis.com/zenn-user-upload/adc927af1fd1-20250412.png)

- `@vscode`
  - Visual Studio Code（エディタ）に関連する機能の質問

![](https://storage.googleapis.com/zenn-user-upload/e4f40dc3c39b-20250412.png)

# スラッシュコマンド

## @workspace ワークスペースに対するコマンド

- `/explain`

  - 指定した範囲のコードが実行される際の動作を説明

![](https://storage.googleapis.com/zenn-user-upload/d71c5c481e11-20250412.png)

- `/fix`

  - 選択したコードの問題点、修正点の提案

![](https://storage.googleapis.com/zenn-user-upload/ec9aab08b0e8-20250412.png)
console.log の g を抜いているのを訂正してくれています

- `/tests`

  - コードの単体テストの作成

![](https://storage.googleapis.com/zenn-user-upload/cd690d6e142e-20250412.png)
今回は Jest を選択しました。
![](https://storage.googleapis.com/zenn-user-upload/2a4849418e09-20250412.png)
変更の適用をすると、ディレクトリが作成され、コマンドを実行すると jest によるテストコードが実行されます。

- `/new`
  - 入力した自然言語に基いた新規ワークスペースの作成

![](https://storage.googleapis.com/zenn-user-upload/68849a593008-20250412.png)

## @vscode エディタに対するコマンド

- `/search`
  - エディタの検索機能で、ワークスペース内のファイルを検索

![](https://storage.googleapis.com/zenn-user-upload/edc0337a80fe-20250412.png)

## @terminal ターミナルに対するコマンド

- `/explain`
  - ターミナルで実行された処理の内容を説明

![](https://storage.googleapis.com/zenn-user-upload/f672778d9720-20250412.png)

## GitHub Copilot Chat に関連するコマンド

- `/help`

  - Copilot Chat の使用に関するヘルプを表示する

![](https://storage.googleapis.com/zenn-user-upload/38ab555672c0-20250412.png)

- `/clear`
  - 現在のチャットを終わらせて新規チャットを開始する

![](https://storage.googleapis.com/zenn-user-upload/5fde6fd3daee-20250412.png)

# コンテキスト変数

- `#codebase`

  - workspace 内のコードの説明が可能

![](https://storage.googleapis.com/zenn-user-upload/b46df529455a-20250412.png)

- `#selection`
  - 選択したコードの説明が可能

![](https://storage.googleapis.com/zenn-user-upload/8001e8914ea5-20250412.png)

# GitHub 関連の Tips

## コミットメッセージ生成

Form の右側の星マークをクリックすると、
![](https://storage.googleapis.com/zenn-user-upload/c8348117a02e-20250412.png)

こんな感じで内容を鑑みて自動生成してくれます。
![](https://storage.googleapis.com/zenn-user-upload/f09d424778b2-20250412.png)

GitHub の commit message がずっと"commit" だったので、これからは猛省し、AI によるレコメンドされたコメントを利用するようにしたいと思います。

しかも結構わかりやすいのでおすすめです。

# GitHub Spark

現在、Technical Preview 中(2025 年 4 月現在)の機能で面白い機能が出たのでご紹介 🚀

- 概要説明
  GitHub Spark は、AI を活用して個人が簡単にマイクロアプリ（“sparks”）を作成・共有できるツールです。​ コーディングやデプロイの知識がなくても、自然言語でアイデアを記述するだけでアプリを構築できます。​ これにより、開発経験の有無に関わらず、誰もが自分のニーズに合わせたアプリを作成し、デスクトップやモバイルデバイスから直接利用できます。

まとめると、`誰でも簡単に自然言語でソフトウェアを作成・カスタマイズできるようにするAIツール` というところですね。

https://githubnext.com/projects/github-spark

GitHub Spark の DEMO がさわれそうだったので、触ってみました。
https://spark.githubnext.com/api/spark-signin?callbackUrl=%2F

![](https://storage.googleapis.com/zenn-user-upload/44d4bebee786-20250412.png)

これがあれば、自然言語でアプリケーションの開発が出来るので、どんな人でも情熱があればアプリケーションを作れる時代になりそうですね。

GA されるのが非常に楽しみな機能です。

# 最後に

本記事では GitHub Copilot の概要説明から Tips、現在開発中の機能まで幅広く紹介しました。
これから、GitHub Copilot を使ってみたい方や、すでに使っている方も、是非参考になれば幸いです。

今後のエンジニアライフがより快適になることを祈ってこの記事を締めたいと思います。

それでは 👋

# スタートアップ企業様向けのお知らせ

日本マイクロソフトでは、スタートアップ企業様向けに、ビジネスを支援するプログラムをご提供しています。  
Azure の無料クレジットが`最大$150,000`もらえるので、是非チェックしてみてください。

https://speakerdeck.com/satakeyusuke19920527/microsoft-for-startups-founders-hub

# 参考文献

https://www.docswell.com/s/yuma/K3GD9E-2025-04-05-aidd#p1

https://www.udemy.com/course/github-copilot-fundamental/

https://github.com/features/copilot

https://github.com/features/copilot/plans?cft=copilot_li.features_copilot
