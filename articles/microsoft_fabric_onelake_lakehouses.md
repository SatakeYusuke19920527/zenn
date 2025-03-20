---
title: 'Microsoft Fabric徹底入門 ~OneLake編~'
emoji: '📚'
type: 'tech' # tech: 技術記事 / idea: アイデア
topics: ['microsoftfabric', 'onelake', 'lakehouse', 'azure']
published: false
publication_name: microsoft
---

![](https://storage.googleapis.com/zenn-user-upload/b026ed82bb05-20250202.png)

# はじめに

今回は Microsoft Fabric の中でも OneLake について解説していきます。

# OneLake とは

## OneLake の特徴

OneLake は、組織全体で 1 つに統合されたまざまなソースからの大量のデータを処理出来るデータレイクです。
OneDrive と同様、OneLake はすべての Microsoft Fabric テナントに付属し、すべての分析データの単一の場所になるように設計されています。

OneLake が顧客に提供する内容は次のとおりです。

- 組織全体に対する 1 つのデータ レイク
- 複数の分析エンジンで使用するデータの 1 つのコピー

Fabric で使用する全ての機能は OneLake 内のデータを参照しています。
![](https://storage.googleapis.com/zenn-user-upload/0943779b26f9-20250202.png)

OneLake は、すべての Fabric ワークロードが構築される基盤です。
Fabric プラットフォームに組み込まれており、ワークロードが動作するすべての組織データを保存するための統合された場所になります。

内部の話をすると、OneLake は Azure Data Lake Storage Gen2 上に構築されています。

プロ開発者と市民開発者の両方に役立つ、単一の SaaS エクスペリエンスとテナント全体のデータ ストアを提供します。
リソース グループ、RBAC (ロールベースのアクセス制御)、Azure Resource Manager、冗長性、リージョンなどのインフラストラクチャの概念を理解する必要性を排除することで、Fabric エクスペリエンスを簡素化します。

尚、Fabric を使用するのに Azure アカウントは必要ありません。

Azure Data Lake Storage
https://learn.microsoft.com/ja-jp/azure/storage/blobs/data-lake-storage-introduction

まとめると、Azure Data Lake Storage 上に Fabric 作って、中央集権的にデータを管理する OneLake があるという理解で OK です。

そして、Microsoft Fabric ではテナント内に、任意の数のワークスペースを作成できます。
ワークスペースを使用すると、組織のさまざまな部門が所有権とアクセス ポリシーを配布できます。
各ワークスペースは、特定のリージョンに関連付けられている容量の一部であり、個別に課金されます。
![](https://storage.googleapis.com/zenn-user-upload/c730ab993d13-20250204.png)

## OneLake の特徴

アプリケーションではストレージとコンピューティングが分離されている場合がありますが、多くの場合、データは 1 つのエンジン用に最適化されるため、複数のアプリケーションで同じデータを再利用することが困難になります。

Fabric を使用すると、さまざまな分析エンジン (T-SQL、Apache Spark、Analysis Services など) が、オープンな Delta Parquet 形式でデータを格納するため、複数のエンジンにまたがって同じデータを使用できるようになります。

データを別のエンジンで使用するためだけにコピーする必要はもうありません。 実行しようとしているジョブに最適なエンジンを常に選択できます。

たとえば、SQL エンジニアのチームが完全なトランザクション データ ウェアハウスを構築するとします。 T-SQL エンジンと T-SQL のすべての機能を使用して、データの作成、変換、テーブルへのデータの読み込みを行うことができます。

データ サイエンティストがこのデータを利用する場合、特別な Spark/SQL ドライバーを使用する必要はもうありません。

OneLake は、すべてのデータを Delta Parquet 形式で格納します。 データ サイエンティストは、Spark エンジンとそのオープンソース ライブラリの全機能をデータに対して直接使用できます。

ビジネス ユーザーは、Analysis Services エンジンの新しい Direct Lake モードを使用して、OneLake に基づいて直接 Power BI レポートを作成できます。

Analysis Services エンジンは、Power BI セマンティック モデルが利用するもので、データにアクセスするために、常にインポートと直接クエリの 2 つのモードを提供してきました。

Direct Lake モードを使用すると、データをコピーする必要がないため、インポートそのままの速度がユーザーに提供され、インポートと DirectQuery の利点が組み合わされます。

### One Copy

OneLake はデータの移動や複製をせずに、データの単一コピーをして全機能からアクセスできるように設計されています。
なので、特定箇所だけにデータがある。みたいな状態にはならなくて、Fabric に取り込まれたデータは確実に OneLake 上にコピーされます。

### ショートカット

### ミラーリング

# まとめ

# スタートアップ企業様向けのお知らせ

日本マイクロソフトでは、スタートアップ企業様向けに、ビジネスを支援するプログラムをご提供しています。
Azure の無料クレジットが`最大$150,000`もらえるので、是非チェックしてみてください。

https://speakerdeck.com/satakeyusuke19920527/microsoft-for-startups-founders-hub

# 参考文献

https://learn.microsoft.com/ja-jp/fabric/onelake/
