---
title: "【Microsoft Fabric入門】Fabricでデータ収集・加工・可視化までをハンズオンで理解する会"
emoji: "💡"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["Microsoft Fabric","Power BI","lakehouse","warehouse","data engineering"]
published: true
publication_name: microsoft
---

![](https://storage.googleapis.com/zenn-user-upload/1746c53373eb-20250201.png)

# はじめに
この記事では、Microsoft Fabricを触りながら理解する会となります。

近年、生成AIの進化により、データの取り扱い方についても考える機会が多くなってきました。
データの収集、加工、可視化を一元的に行うことができるMicrosoft Fabricは、データエンジニアリングやデータサイエンスの業務を効率化するための強力なツールです。

そんなMicrosoft Fabricですが、まだまだ日本語の情報が少なく、使い方が分からないという方も多いという声をよく聞きます。

なので、今回はハンズオンを通じて、Microsoft Fabricの基本的な使い方を理解していきたいと思います。

それでは、早速始めていきましょう🚀

# Microsoft Fabricとは

![](https://storage.googleapis.com/zenn-user-upload/1c43ea0eb2a2-20250201.png)
Microsoft Fabricは、データの統合、分析、可視化を一元的に行うことができるプラットフォームです。

以下の記事に概要がまとまっていますので、是非ご覧ください。
https://zenn.dev/microsoft/articles/microsoft_fabric_intro

# Let's ハンズオン🚀
今回のハンズオンでは、Microsoft Fabricを使って、データの収集、加工、可視化までの一連の流れを体験していきます。
最終的にはダッシュボードを作成し、データの可視化を行います。
![](https://storage.googleapis.com/zenn-user-upload/a6a4419bdb50-20250615.png)

データは筆者がChatGPTを使って、サンプルデータを作成したものなので、皆様も良ければご興味あるデータを作成してハンズオンで使ってみてください。

## Hands-Onで構築するデータ基盤

今回のハンズオンで構築するデータ基盤は以下となります。
![](https://storage.googleapis.com/zenn-user-upload/ce7c2b2aa4ad-20250615.png)

外部データをLakehouseに取り込み、dataflowでデータを加工し、Warehouseでデータを蓄積・探索し、Power BIで可視化する流れを体験していきます。

## Microsoft Fabricのアカウント作成

嬉しい機能として、Fabricの無料試用版が60日間も使えるので、今回のハンズオンは無料枠の中で進めていきます。
まずは、Microsoft Fabricのアカウントを作成していきましょう。
![](https://storage.googleapis.com/zenn-user-upload/b4d7bae38191-20250610.png)

上記画像のようになっていますので、無料で試すをクリックして、Microsoft Fabricのアカウントを作成していきます。

こんな感じ画面が出てくればアカウント作成は完了です。
![](https://storage.googleapis.com/zenn-user-upload/16ed9b44c5ed-20250615.png)

## workspaceの作成

ログイン出来たら、まずは画面左側メニューのワークスペースより +新しいワークスペース を選択してワークスペースを作成します。
![](https://storage.googleapis.com/zenn-user-upload/0a1443c3644e-20250610.png)

筆者は今回、satake-fabric-poc というワークスペースとしました。
![](https://storage.googleapis.com/zenn-user-upload/d741379e6bd1-20250610.png)


## レイクハウスの作成

次に、データを格納する場所を作成します。
Microsoft FabricはOneLakeという場所に一元管理されるものになり、機能としてはLakehouseという名前で提供されています。

画面左上の +新しい項目 を選択し、検索ボックスに「レイクハウス」と入力して、レイクハウスを選択します。
![](https://storage.googleapis.com/zenn-user-upload/cda516e0c6cc-20250615.png)


レイクハウスの名前を入力して、作成をクリックします。
![](https://storage.googleapis.com/zenn-user-upload/b251bb0d047e-20250615.png)

こんな感じにレイクハウスが作成されました。
![](https://storage.googleapis.com/zenn-user-upload/95d222e42317-20250615.png)


## Lakehouseにデータを取り込む
レイクハウスの作成が出来たので、次はレイクハウスにデータを取り込んでいきます。
![](https://storage.googleapis.com/zenn-user-upload/ce7c2b2aa4ad-20250615.png)

ハンズオンのサンプルデータはこちらよりお使いください。
https://github.com/SatakeYusuke19920527/sample-data

フォルダーをUploadしていきます。
![](https://storage.googleapis.com/zenn-user-upload/95d222e42317-20250615.png)

データをアップロードしていきます。
![](https://storage.googleapis.com/zenn-user-upload/d46464baaac9-20250615.png)

こんな感じでデータがアップロードされました。
![](https://storage.googleapis.com/zenn-user-upload/16511b86ea83-20250615.png)

そして、csvファイルをテーブルに読み込みます。
それぞれ4つを読み込んであげてください。
![](https://storage.googleapis.com/zenn-user-upload/be95d0b9c8b2-20250615.png)

読み込みをクリック
![](https://storage.googleapis.com/zenn-user-upload/2eadb0d9a255-20250615.png)

こんな感じでテーブルが作成されればOKです
![](https://storage.googleapis.com/zenn-user-upload/cb3ab0430315-20250615.png)

## dataflowとwarehouseでデータの加工

次は取り込んだデータを加工していきます。
下記の画像で言うと、dataflowとwarehouseの部分です。
![](https://storage.googleapis.com/zenn-user-upload/ce7c2b2aa4ad-20250615.png)

## warehouseの作成
まずは、warehouseを作成していきます。
画面左上の +新しい項目 を選択し、検索ボックスに「ウェアハウス」と入力して、ウェアハウスを選択します。
![](https://storage.googleapis.com/zenn-user-upload/c51db8dcc2e2-20250615.png)

いい感じの名前を入力して、作成をクリックします。
![](https://storage.googleapis.com/zenn-user-upload/927969fa9a94-20250615.png)

こんな感じでウェアハウスが表示されればOKです。
![](https://storage.googleapis.com/zenn-user-upload/fbd3454bce28-20250615.png)

## dataflowの作成
次に、dataflowを作成していきます。
warehouseの画面に
新しいデータフロー Gen2 があるので、それをクリックします。
![](https://storage.googleapis.com/zenn-user-upload/e03d0cbb410f-20250615.png)

こちらにもいい感じに名前を付けてあげてください。
![](https://storage.googleapis.com/zenn-user-upload/cdb63ec170f4-20250615.png)

こんな感じでdataflowが作成されました。
![](https://storage.googleapis.com/zenn-user-upload/04d524eb803f-20250615.png)

別のソース -> からデータを取得する を選択します。
先程作成したレイクハウスを選択します。
![](https://storage.googleapis.com/zenn-user-upload/cd9b0b4c895d-20250615.png)

先程、csvから作成したテーブルを選択して作成をクリックします。
![](https://storage.googleapis.com/zenn-user-upload/10a9bb4d0d2e-20250615.png)

画面右下のダイアグラムビューをクリックしてください。
![](https://storage.googleapis.com/zenn-user-upload/982c71892e46-20250615.png)

画面上部にダイアグラムが表示されればOKです。
![](https://storage.googleapis.com/zenn-user-upload/5127593eb7e2-20250615.png)

## データの加工
少しだけ、データの加工をしてみます。

![](https://storage.googleapis.com/zenn-user-upload/5c9686610434-20250615.png)

カスタム列の追加を選択し、Copilotに男女共学かどうかの判定列を追加してもらいます。
![](https://storage.googleapis.com/zenn-user-upload/3de52d59350d-20250615.png)

こんな感じでcolumnが追加されました。
![](https://storage.googleapis.com/zenn-user-upload/a55fcde551c6-20250615.png)

そして画面左上の保存と実行をクリックしてデータフローを開始してください。
![](https://storage.googleapis.com/zenn-user-upload/2ab62648e9bf-20250615.png)

念の為、warehouseの画面に戻って、先程作成したdataflowをクリックして、データが更新されているか確認してみてください。
Coeducation列が追加されていることが確認できました。
![](https://storage.googleapis.com/zenn-user-upload/3a818c3e385e-20250615.png)

## セマンティックモデルの作成
ウェアハウスの画面に戻り、Model Layoutsをクリックします。
そして、画面上部の新しいセマンティックモデルをクリック

![](https://storage.googleapis.com/zenn-user-upload/0c241f7c74f5-20250615.png)


新しいセマンティックモデルの名前を入力して、テーブルを全て選択し、作成をクリックします。
![](https://storage.googleapis.com/zenn-user-upload/472f2dbda68b-20250615.png)


こんな感じでセマンティックモデルが作成されました。
![](https://storage.googleapis.com/zenn-user-upload/ca70eb463878-20250615.png)

次にリレーションシップを設定していきます。
画面上部のリレーションシップの管理をクリックします。
![](https://storage.googleapis.com/zenn-user-upload/dccb9a305169-20250615.png)

+新しいリレーションシップ をクリックして、どんどんリレーションシップを設定していきます。
![](https://storage.googleapis.com/zenn-user-upload/bd1f67dedb3c-20250615.png)

全部で3つのリレーションシップを作成してみました。
![](https://storage.googleapis.com/zenn-user-upload/a2392755b730-20250615.png)

こんな感じでキレイに各tableのリレーションシップが設定されました。
![](https://storage.googleapis.com/zenn-user-upload/51de499f964c-20250615.png)

## Power BIでデータの可視化
最後にPowerBIでデータの可視化を行います。
セマンティックモデルの画面に戻り、```このデータを探索する```をクリックして、レポートの自動作成をクリックします。
![](https://storage.googleapis.com/zenn-user-upload/f25170efe776-20250615.png)

こんな感じで自動にレポートが作成されました。
![](https://storage.googleapis.com/zenn-user-upload/1a1e4c0af967-20250615.png)

これでハンズオンは終了となります。
今回は非常にシンプルなデータでしたが、実際の業務ではより複雑なデータを扱うことが多いと思います。
今回のハンズオンを通じて、Microsoft Fabricの基本的な使い方を理解していただけたかと思うので、是非今後のデータ分析に活用していただければと思います。

お疲れ様でした。

# まとめ
この記事では、Microsoft Fabricを使ってデータ基盤を構築する方法をハンズオン形式で紹介しました。

Microsoft Fabricは、データの統合、分析、可視化を一元的に行うことができるプラットフォームであり、データエンジニアリングやデータサイエンスの業務を効率化することができます。

今後、Microsoft Fabricの機能を活用して、データ基盤の構築する需要が多くなっていくと思いますので、先んじてFabricの使い方をマスターしていただけますと幸いです。

データ分析基盤の構築について、ご興味がある方は是非お気軽にご連絡ください。

Xだとよく反応出来るので、以下リンクよりDMいただけますと幸いです。
https://x.com/fe_js_engineer

それでは👍

# 参考リンク
