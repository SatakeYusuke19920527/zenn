---
title: "Microsoft Fabricを触りながら理解する会"
emoji: "🧑‍🏫"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["Microsoft Fabric","Power BI", ,"lakehouse","warehouse","data engineering"]
published: false
publication_name: microsoft
---

# はじめに
この記事では、Microsoft Fabricを触りながら理解する会となります。

近年、生成AIの進化により、データの取り扱い方についても考える機会が多くなってきました。

Microsoft Fabricは、データの統合、分析、可視化を一元的に行うことができるプラットフォームです。

この記事では、Microsoft Fabricの基本的な使い方をハンズオン形式で理解を深めていきます。

# Microsoft Fabricとは

# Let's get started

## Hands-Onで構築するデータ基盤

今回のハンズオンで構築するデータ基盤は以下となります。


Fabricの無料試用版が60日間も使えるので、今回のハンズオンはこちらを利用していきます。
![](https://storage.googleapis.com/zenn-user-upload/b4d7bae38191-20250610.png)

## workspaceの作成

ログイン出来たら、まずは画面左側メニューのワークスペースより +新しいワークスペース を選択してワークスペースを作成します。
![](https://storage.googleapis.com/zenn-user-upload/0a1443c3644e-20250610.png)

筆者は今回、satake-fabric-poc というワークスペースとしました。
![](https://storage.googleapis.com/zenn-user-upload/d741379e6bd1-20250610.png)





## OneLakeの作成

