---
title: 'BigDataをAI Agentで分析する会'
emoji: '📚'
type: 'tech' # tech: 技術記事 / idea: アイデア
topics: ['microsoftfabric', 'azure', 'agent', 'openai', 'azureopenai']
published: false
publication_name: microsoft
---

![](https://storage.googleapis.com/zenn-user-upload/e95fc71949b3-20250420.png)

# はじめに

この記事は Microsoft Fabric Data Agent を用いた Microsoft Fabric でのデータの処理 ×AI Agent による分析と意思決定を実施します。

ここでは、Fabric と AI Agent Service に焦点を当て、AI Agent によるビッグデータ分析を最小単位にて実施します。

2025/4/1 に AI Agent Service から Microsoft Fabric のコネクタがリリースされました。
https://techcommunity.microsoft.com/blog/azure-ai-services-blog/expand-azure-ai-agent-with-new-knowledge-tools-microsoft-fabric-and-tripadvisor/4398085

この Update により、Microsoft Fabric Data Agent を使用して、OneLake 内のデータを AI Agent が参照出来るアプリケーションが簡単に構築することが出来ます。

すなわち、Microsoft Fabric × Azure AI Agent Service を連携して、以下のフローを自動化することが出来ることになります。

**_データレイクによる情報収集 → データウェアハウスでのデータ構造化 → データマートで必要箇所のデータの区分分け →AI が必要となるデータ部分を参照し、最適な意思決定を実施 →AI Agent を用いたアクションの実行_**

データ収集から構造化、分析、意思決定、アクションに至るまでのプロセスをいい感じにやってくれそうですね。

少し話はそれますが、データレイク・データウェアハウス・データマートの説明は以下になります。

![](https://storage.googleapis.com/zenn-user-upload/2fa93d1c140f-20250420.png)

| ステージ | 主役               | 役割 (ざっくり ⤴︎)                                                                                                                                           | 例 (Azure)                                   |
| -------- | ------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------ | -------------------------------------------- |
| ① 集める | データレイク       | ありのままを “全部貯める池”<br>• 生ログ・画像・CSV・Parquet など多種多様<br>• スキーマ‑オン‑リード (読むときに意味づけ)<br>• 安価で無限スケール              | Azure Data Lake Storage Gen2、Fabric OneLake |
| ② 整える | データウェアハウス | “精製工場” で統合・整形した高品質データ<br>• フォーマット統一・欠損補完・型変換<br>• スキーマ‑オン‑ライト (書込時に定義)<br>• 列指向・インデックスで高速集計 | Azure Synapse Warehouse、SQL DW              |
| ③ 届ける | データマート       | “使いやすい売店” に小分けして配布<br>• 部門/業務特化 (販売、人事 など)<br>• 要件に合わせて列・粒度を絞り込み<br>• 自サービスでセルフ BI                      | Fabric Data Mart、Power BI Datamart          |

データレイクへのデータ蓄積とデータウェアハウスでのデータ構造化やデータマートの構築を Fabric を使って行い、意思決定プロセスを AI Agent Service を用いてアプリケーション側で実施することが本記事の目標です。

ビッグデータ分析から AI Agent の利活用を検討されている方に参考になれば幸いです。

# 大目標

Microsoft Fabric で OneLake へ蓄積、Data Engineering にて分析したデータを AI Agent 経由でアプリケーションから AgentRAG を実施

# 目標とする構成

![](https://storage.googleapis.com/zenn-user-upload/11f5645d1e5f-20250420.png)

## Microsoft Fabric データ エージェント利用の前提条件

Microsoft Fabric データ エージェント利用の為には以下の設定を実施する必要があります。

- F64 以上の有料ファブリック容量リソースをという条件で使用する
- Fabric データ エージェントのテナント設定が有効になっている。
- Copilot テナント スイッチ が有効になっています。
- AI のクロス geo 処理が有効になっています。
- AI のクロスジオ格納が有効になっています。
- データを含む、ウェアハウス、レイクハウス、1 つ以上の Power BI セマンティック モデル、または KQL - データベースのうち少なくとも 1 つ。
- POWER BI セマンティック モデルのデータ ソースに対して、XMLA エンドポイントテナントスイッチ を介して Power BI セマンティック モデルが有効になります。

上記の設定をしていきます。

## Microsoft Fabric データ エージェント利用の各種設定

#### F64 以上の有料ファブリック容量リソースをという条件で使用する

こちらは Azure の Fabric→ スケール → サイズの変更から F64 を選択
![](https://storage.googleapis.com/zenn-user-upload/80f9239c04e5-20250420.png)

#### Fabric データ エージェントのテナント設定が有効になっている。

以下のドキュメントを参照
https://learn.microsoft.com/ja-jp/fabric/data-science/data-agent-tenant-settings

#### Copilot テナント スイッチ が有効になっています。

#### AI のクロス geo 処理が有効になっています。

#### AI のクロスジオ格納が有効になっています。

#### データを含む、ウェアハウス、レイクハウス、1 つ以上の Power BI セマンティック モデル、または KQL - データベースのうち少なくとも 1 つ。

#### POWER BI セマンティック モデルのデータ ソースに対して、XMLA エンドポイントテナントスイッチ を介して Power BI セマンティック モデルが有効になります。

# 最後に

# 参考資料

https://learn.microsoft.com/ja-jp/azure/ai-services/agents/how-to/tools/fabric?tabs=python&pivots=overview
https://learn.microsoft.com/ja-jp/fabric/data-science/concept-data-agent
https://learn.microsoft.com/ja-jp/fabric/fundamentals/copilot-fabric-overview#available-regions-for-azure-openai-service
