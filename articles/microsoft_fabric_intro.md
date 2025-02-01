---
title: 'これだけ読めば大丈夫Microsoft Fabric徹底入門'
emoji: '📚'
type: 'tech' # tech: 技術記事 / idea: アイデア
topics: ['azure', 'browser-use', 'automation', 'python']
published: false
publication_name: microsoft
---

## はじめに

![](https://storage.googleapis.com/zenn-user-upload/1746c53373eb-20250201.png)

# Microsoft Fabric の概要

![](https://storage.googleapis.com/zenn-user-upload/1c43ea0eb2a2-20250201.png)

Microsoft Fabric は、統合ソリューションを必要とする企業向けに設計されたエンドツーエンドの分析およびデータ プラットフォームです。
データの移動、処理、取り込み、変換、リアルタイム イベント ルーティング、レポート作成がこのツール一つで出来ちゃったりします。

データ エンジニアリング、データ ファクトリ、データ サイエンス、リアルタイム分析、データ ウェアハウス、データベースなど、包括的なサービスを提供する SaaS だと思えば OK です。

これまで、データ分析といえば、複数のベンダーからさまざまなサービスを集め、それぞれのツールを使いこなす必要がありましたが、Microsoft Fabric を使えば一つのツールで色々出来てしまうというところが魅力ですね。

Microsoft Fabric で取り扱うデータは、OneLake と呼ばれるデータストレージへ一括で保管されます。
また、昨今大流行中の AI 機能も Fabric 内にシームレスに組み込まれているため、手動で統合する必要はありません。
Fabric を使用すると、生データからビジネス ユーザー向けの実用的な分析情報を簡単に取得できます。

具体的には以下のような機能があります。

### Power BI

Power BI を使用すると、データ ソースに簡単に接続し、重要な情報を視覚化して検出し、それを任意の相手と共有できます。この統合エクスペリエンスにより、ビジネス オーナーは Fabric 内のすべてのデータに迅速かつ直感的にアクセスし、データを使用してより適切な意思決定を行うことができます。詳細については、「Power BI とは」を参照してください。

### Databases

Microsoft Fabric のデータベースは、Azure SQL Database などの開発者向けのトランザクション データベースであり、Fabric で運用データベースを簡単に作成できます。ミラーリング機能を使用すると、さまざまなシステムのデータを OneLake にまとめることができます。Azure SQL Database、Azure Cosmos DB、Azure Databricks、Snowflake、Fabric SQL データベースのデータなど、既存のデータ資産を Fabric の OneLake に直接継続的にレプリケートできます。詳細については、「Microsoft Fabric の SQL データベース」および「Fabric のミラーリングとは」を参照してください。

### Data Factory

Data Factory は、豊富なデータ ソースからデータを取り込み、準備し、変換するための最新のデータ統合エクスペリエンスを提供します。Power Query のシンプルさが組み込まれており、200 を超えるネイティブ コネクタを使用してオンプレミスおよびクラウドのデータ ソースに接続できます。詳細については、「Microsoft Fabric の Data Factory とは」を参照してください。

### Industry Solutions

Fabric は、業界固有のニーズと課題に対応する業界固有のデータ ソリューションを提供し、データ管理、分析、意思決定が含まれます。詳細については、「Microsoft Fabric の業界ソリューション」を参照してください。

### Real-Time Intelligence

リアルタイム インテリジェンスは、イベント駆動型のシナリオ、ストリーミング データ、およびデータ ログ向けのエンドツーエンドのソリューションです。データの取り込み、変換、保存、分析、視覚化、追跡、AI、およびリアルタイム アクションを処理することで、移動中のデータに関する洞察の抽出、視覚化、およびアクションを可能にします。リアルタイム インテリジェンスのリアルタイム ハブは、さまざまなコード不要のコネクタを提供し、Fabric 全体で保護、管理、および統合された組織データのカタログに集約されます。詳細については、「Fabric のリアルタイム インテリジェンスとは」を参照してください。

### Data Engineering

Fabric Data Engineering は、優れたオーサリング エクスペリエンスを備えた Spark プラットフォームを提供します。これにより、膨大な量のデータを収集、保存、処理、分析するためのインフラストラクチャを作成、管理、最適化できます。Fabric Spark と Data Factory の統合により、ノートブックと Spark ジョブをスケジュールおよび調整できます。詳細については、「Microsoft Fabric のデータ エンジニアリングとは」を参照してください。

### Fabric Data Science

Fabric Data Science を使用すると、Fabric から機械学習モデルを構築、展開、運用化できます。Azure Machine Learning と統合して、組み込みの実験追跡とモデル レジストリを提供します。データ サイエンティストは予測によって組織データを強化でき、ビジネス アナリストはそれらの予測を BI レポートに統合して、説明的な分析情報から予測的な分析情報に移行できます。詳細については、「Microsoft Fabric のデータ サイエンスとは」を参照してください。

### Fabric Data Warehouse

Fabric Data Warehouse は、業界をリードする SQL パフォーマンスとスケールを提供します。コンピューティングとストレージを分離し、両方のコンポーネントを独立してスケーリングできるようにします。さらに、オープンな Delta Lake 形式でデータをネイティブに保存します。詳細については、「Microsoft Fabric のデータ ウェアハウスとは」を参照してください。

# 価格

![](https://storage.googleapis.com/zenn-user-upload/0a9c6ca89940-20250201.png)

## Microsoft Fabric の価格計算方法について

https://azure.microsoft.com/ja-jp/pricing/details/microsoft-fabric/
Microsoft Fabric の価格計算は、以下の計算で算出出来ます。
コンピューティングである Fabric の capacity + ストレージである OneLake の容量 + PowerBI のライセンス料 + データ通信量

Fabric ワークスペースへの Power BI 以外のアイテムの利用作成でしたら Power BI Free で十分ですが、Power BI も含んだすべてのアイテムを触らせたい際には、ユーザーに Pro 以上のライセンスを付与する必要があります。
ただし、SKU が F64 以上の容量を購入することで Power BI のユーザライセンスを問わずデータを見ることができるようになります。
F64 からは 3 人のユーザーへのライセンス付与が可能です。
https://www.microsoft.com/ja-jp/power-platform/products/power-bi/pricing

## Microsoft Fabric Capacity の価格

コンピューティングの方の価格表が以下になります。

| SKU   | Capacity unit (CU) | Pay-as-you-go (¥/hour) | Reservation (¥/hour) | Savings |
| ----- | ------------------ | ---------------------- | -------------------- | ------- |
| F2    | 2                  | ¥66.174                | ¥39.353              | ~41%    |
| F4    | 4                  | ¥132.347               | ¥78.706              | ~41%    |
| F8    | 8                  | ¥264.693               | ¥157.412             | ~41%    |
| F16   | 16                 | ¥529.385               | ¥314.823             | ~41%    |
| F32   | 32                 | ¥1,058.770             | ¥629.645             | ~41%    |
| F64   | 64                 | ¥2,117.540             | ¥1,259.289           | ~41%    |
| F128  | 128                | ¥4,235.079             | ¥2,518.578           | ~41%    |
| F256  | 256                | ¥8,470.157             | ¥5,037.156           | ~41%    |
| F512  | 512                | ¥16,940.314            | ¥10,074.312          | ~41%    |
| F1024 | 1024               | ¥33,880.628            | ¥20,148.623          | ~41%    |
| F2048 | 2048               | ¥67,761.255            | ¥40,297.246          | ~41%    |

- SKU は Fabric の Price の単位で、CPU は計算能力の単位になります。1 秒間に 1 つの命令を実行できることを意味します。CPU は、コンピューターの中央処理装置 (CPU) が実行する命令の数を表します。

## One Lake Storage の価格

以下が One Lake の価格になります。
ストレージ料金はかなり安いですね。

| ストレージ                 | 価格 (¥/GB・月) |
| -------------------------- | --------------- |
| OneLake ストレージ/月      | ¥3.9389         |
| OneLake BCDR ストレージ/月 | ¥7.0900         |
| OneLake キャッシュ/月      | ¥45.6910        |

## データ通信量

https://azure.microsoft.com/ja-jp/pricing/details/bandwidth/

基本的に同じ可溶性ゾーンのデータ転送は無料で、大陸内・間データ転送にお金が発生するという特徴があります。

| 大陸内データ転送                   | 料金 (¥/GB) |
| ---------------------------------- | ----------- |
| 北米内のリージョン間               | ¥3.152      |
| ヨーロッパ内のリージョン間         | ¥3.152      |
| アジア内のリージョン間             | ¥12.605     |
| オセアニア内のリージョン間         | ¥12.605     |
| 中東およびアフリカ内のリージョン間 | ¥12.605     |
| 南米内のリージョン間               | ¥25.209     |

| 大陸間のデータ転送     | 料金 (¥/GB) |
| ---------------------- | ----------- |
| 北米から他の大陸       | ¥7.878      |
| ヨーロッパから他の大陸 | ¥7.878      |
| アジアから他の大陸     | ¥12.605     |
| オセアニアから他の大陸 | ¥12.605     |
| アフリカから他の大陸   | ¥12.605     |
| 南米から他の大陸       | ¥25.209     |

Fabric の料金については、Capacity が主な料金になります。データを単一リージョンに格納する設計と、必要なコンピューティングリソースに応じて SKU を設定すれば適切な料金で利用することが可能です。

それでは 👋

---

# スタートアップ企業様向けのお知らせ

日本マイクロソフトでは、スタートアップ企業様向けに、ビジネスを支援するプログラムをご提供しています。
Azure の無料クレジットが`最大$150,000`もらえるので、是非チェックしてみてください。

https://speakerdeck.com/satakeyusuke19920527/microsoft-for-startups-founders-hub

---

### 参考リンク

- [**Multi-Agent-Custom-Automation-Engine-Solution-Accelerator GitHub リポジトリ**](https://github.com/microsoft/Multi-Agent-Custom-Automation-Engine-Solution-Accelerator)
- [**Azure OpenAI Service 公式ドキュメント**](https://learn.microsoft.com/azure/cognitive-services/openai/)
- [**Autogen の最新情報**](https://www.microsoft.com/en-us/research/lab/ai-frontiers/)

---
