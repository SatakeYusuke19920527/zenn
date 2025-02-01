---
title: 'Microsoft Fabric徹底入門 ~ Microsoft Fabricとは？ ~'
emoji: '📚'
type: 'tech' # tech: 技術記事 / idea: アイデア
topics: ['azure', 'microsoftfabric', 'onelake', 'powerbi', 'data']
published: true
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

特に機能として魅力的だと感じている点は以下です。

- OneLake でデータ管理とアクセスが簡単に
- Copilot サポートがサポートしてくれる
- Microsoft 365 との統合できること
- Azure AI Foundry との連携が可能
- 統合データ管理:ガバナンス、共有、アクセスを簡素化する集中型データ検出

それぞれの Microsoft 製品と連携することが出来る為、Fabric を使えば社内にあるデータを活用しやすくなります。

具体的には以下のような機能があります。

### Power BI

Power BI を使用すると、データソースに簡単に接続し、重要な情報を視覚化して検出することが出来ます。この統合エクスペリエンスにより、ビジネス オーナーは Fabric 内のすべてのデータに迅速かつ直感的にアクセスし、データを使用してより適切な意思決定を行うことができます。

Power BI
https://learn.microsoft.com/en-us/power-bi/fundamentals/power-bi-overview

### Databases

Microsoft Fabric のデータベースは、Azure SQL Database などの開発者向けのトランザクション データベースであり、Fabric で運用データベースを簡単に作成できます。
ミラーリング機能を使用すると、さまざまなシステムのデータを OneLake にまとめることができます。
Azure SQL Database、Azure Cosmos DB、Azure Databricks、Snowflake、Fabric SQL データベースのデータなど、既存のデータ資産を Fabric の OneLake に直接継続的にレプリケートできます。

Microsoft Fabric SQL Database
https://learn.microsoft.com/ja-jp/fabric/database/sql/overview

ミラーリング
https://learn.microsoft.com/ja-jp/fabric/database/mirrored-database/overview

### Data Factory

Data Factory は、豊富なデータソースからデータを取り込み、準備し、変換するための最新のデータ統合エクスペリエンスを提供します。
Power Query のシンプルさが組み込まれており、200 を超えるネイティブコネクタを使用してオンプレミスおよびクラウドのデータソースに接続できます。

Data Factory
https://learn.microsoft.com/en-us/fabric/data-factory/data-factory-overview

### Industry Solutions

Fabric は、業界固有のニーズと課題に対応する業界固有のデータソリューションを提供し、データ管理、分析、意思決定が含まれます。

Industry Solutions
https://learn.microsoft.com/en-us/industry/industry-data-solutions-fabric

### Real-Time Intelligence

リアルタイムインテリジェンスは、イベント駆動型のシナリオ、ストリーミング データ、およびデータ ログ向けのエンドツーエンドのソリューションです。
データの取り込み、変換、保存、分析、視覚化、追跡、AI、およびリアルタイム アクションを処理することで、移動中のデータに関する洞察の抽出、視覚化、およびアクションを可能にします。リアルタイム インテリジェンスのリアルタイム ハブは、さまざまなコード不要のコネクタを提供し、Fabric 全体で保護、管理、および統合された組織データのカタログに集約されます。

Real-Time Intelligence
https://learn.microsoft.com/en-us/fabric/real-time-intelligence/overview

### Data Engineering

Fabric Data Engineering は、優れたオーサリングエクスペリエンスを備えた Spark プラットフォームを提供します。
これにより、膨大な量のデータを収集、保存、処理、分析するためのインフラストラクチャを作成、管理、最適化できます。Fabric Spark と Data Factory の統合により、ノートブックと Spark ジョブをスケジュールおよび調整できます。

Data Engineering
https://learn.microsoft.com/en-us/fabric/data-engineering/data-engineering-overview

### Fabric Data Science

Fabric Data Science を使用すると、Fabric から機械学習モデルを構築、展開、運用化できます。Azure Machine Learning と統合して、組み込みの実験追跡とモデル レジストリを提供します。
データ サイエンティストは予測によって組織データを強化でき、ビジネス アナリストはそれらの予測を BI レポートに統合して、説明的な分析情報から予測的な分析情報に移行できます。

Fabric Data Science
https://learn.microsoft.com/en-us/fabric/data-science/data-science-overview

### Fabric Data Warehouse

Fabric Data Warehouse は、業界をリードする SQL パフォーマンスとスケールを提供します。コンピューティングとストレージを分離し、両方のコンポーネントを独立してスケーリングできるようにします。さらに、オープンな Delta Lake 形式でデータをネイティブに保存します。詳細については、「Microsoft Fabric のデータ ウェアハウスとは」を参照してください。

Fabric Data Warehouse
https://learn.microsoft.com/en-us/fabric/data-warehouse/data-warehousing

# Microsoft Fabric の価格計算方法について

Microsoft Fabric の価格計算は、以下の計算で算出出来ます。
コンピューティングである Fabric の capacity + ストレージである OneLake の容量 + PowerBI のライセンス料 + データ通信量

![](https://storage.googleapis.com/zenn-user-upload/0a9c6ca89940-20250201.png)

https://azure.microsoft.com/ja-jp/pricing/details/microsoft-fabric/

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

# OneLake とは

OneLake は、すべての Fabric ワークロードが構築される基盤です。
OneLake は、Fabric プラットフォームに組み込まれており、ワークロードが動作するすべての組織データを保存するための統合された場所を提供します。

OneLake は Azure Data Lake Storage Gen2 上に構築されています。プロ開発者と市民開発者の両方に役立つ、単一の SaaS エクスペリエンスとテナント全体のデータ ストアを提供します。
OneLake は、リソース グループ、RBAC (ロールベースのアクセス制御)、Azure Resource Manager、冗長性、リージョンなどのインフラストラクチャの概念を理解する必要性を排除することで、Fabric エクスペリエンスを簡素化します。尚、Fabric を使用するのに Azure アカウントは必要ありません。

Azure Data Lake Storage
https://learn.microsoft.com/ja-jp/azure/storage/blobs/data-lake-storage-introduction

まとめると、Azure Data Lake Storage 上に Fabric 作って、中央集権的にデータを管理する OneLake があるという理解で OK です。

イメージとしては以下。
![](https://storage.googleapis.com/zenn-user-upload/6c5fceb21352-20250201.png)

# Fabric のコンピューティングエンジンについて

![](https://storage.googleapis.com/zenn-user-upload/bf604c1a9160-20250201.png)
Office アプリケーションが組織の OneDrive を使用するように事前設定されているのと同様に、Microsoft Fabric のすべてのコンピューティング エクスペリエンスは OneLake に事前設定されています。

データ エンジニアリング、データ ウェアハウス、データ ファクトリー、Power BI、リアルタイム インテリジェンスなどのエクスペリエンスでは、ネイティブ ストアとして OneLake が使用されます。追加の構成は必要ありません。

OneLake では、ショートカット機能を使用して、既存の PaaS ストレージ アカウントを OneLake に即座にマウントできます。
ショートカットを使用すると、Azure Data Lake Storage に保存されているデータにアクセスできます。

ショートカットを使用すると、情報を移動したり複製したりすることなく、ユーザーとアプリケーション間でデータを簡単に共有することもできます。
他のストレージ システムへのショートカットを作成して、透過的でインテリジェントなキャッシュによりクラウド間でデータを作成および分析し、出力コストを削減してデータをコンピューティングに近づけることができます。

# まとめ

今回は Microsoft Fabric の概要について紹介しました。
次回はそれぞれの機能について詳細に記載していこうと思います。

それでは 👋

---

# スタートアップ企業様向けのお知らせ

日本マイクロソフトでは、スタートアップ企業様向けに、ビジネスを支援するプログラムをご提供しています。
Azure の無料クレジットが`最大$150,000`もらえるので、是非チェックしてみてください。

https://speakerdeck.com/satakeyusuke19920527/microsoft-for-startups-founders-hub

---

### 参考リンク

https://learn.microsoft.com/ja-jp/fabric/fundamentals/microsoft-fabric-overview

---
