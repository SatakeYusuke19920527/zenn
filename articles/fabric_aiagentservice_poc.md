---
title: 'ビッグデータをAI Agentで分析するシステムを構築する会'
emoji: '📚'
type: 'tech' # tech: 技術記事 / idea: アイデア
topics: ['microsoftfabric', 'azure', 'agent', 'openai', 'azureopenai']
published: true
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

| ステージ | 主役               | 役割 (ざっくり 説明)                                                                                                                                           |
| -------- | ------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ① 集める | データレイク       | ありのままを “全部貯める池”<br>だと理解すれば OK• ログ・画像・CSV・Parquet など多種多様<br>• スキーマ‑オン‑リード (読むときに意味づけ)<br>• 安価で無限スケール |
| ② 整える | データウェアハウス | 統合・整形した高品質データ<br>• フォーマット統一・欠損補完・型変換<br>• スキーマ‑オン‑ライト (書込時に定義)<br>• 列指向・インデックスで高速集計                |
| ③ 届ける | データマート       | データを小分けして配布する機能<br>• 部門/業務特化 (販売、人事 など)<br>• 要件に合わせて列・粒度を絞り込み<br>• 自サービスで BI を作る際に利用                  |

データレイクへのデータ蓄積とデータウェアハウスでのデータ構造化やデータマートの構築を Fabric を使って行い、意思決定プロセスを AI Agent Service を用いてアプリケーション側で実施することが本記事の目標です。

ビッグデータ分析から AI Agent の利活用を検討されている方に参考になれば幸いです。

# 大目標

Microsoft Fabric で OneLake へ蓄積、Data Engineering にて分析したデータを AI Agent 経由でアプリケーションから AgentRAG を実施すること

# 目標とする構成

![](https://storage.googleapis.com/zenn-user-upload/a97398a86f9d-20250420.png)

# Microsoft Fabric データ エージェント利用の前提条件

Microsoft Fabric データ エージェント利用の為には以下の設定を実施する必要があります。

- F64 以上の有料ファブリック容量リソースをという条件で使用する
- Fabric データ エージェントのテナント設定が有効になっている。
- Copilot テナント スイッチ が有効になっています。
- AI のクロス geo 処理が有効になっています。
- AI のクロスジオ格納が有効になっています。
- データを含む、ウェアハウス、レイクハウス、1 つ以上の Power BI セマンティック モデル、または KQL - データベースのうち少なくとも 1 つ。
- POWER BI セマンティック モデルのデータ ソースに対して、XMLA エンドポイントテナントスイッチ を介して Power BI セマンティック モデルが有効になります。

上記の設定をしていきます。

# Microsoft Fabric データ エージェント利用の各種設定

### F64 以上の有料ファブリック容量リソースをという条件で使用する

こちらは Azure の Fabric→ スケール → サイズの変更から F64 を選択
![](https://storage.googleapis.com/zenn-user-upload/80f9239c04e5-20250420.png)

### Fabric データ エージェントのテナント設定が有効になっている。

Fabric へアクセスして右上の歯車をクリックし、管理ポータルを選択
![](https://storage.googleapis.com/zenn-user-upload/b8c9caf7b1d6-20250420.png)

### テナントの設定から**Copilot と Azure OpenAI Service**の箇所は全て On に設定する

- Copilot と Azure OpenAI テナント スイッチを有効にする を On に設定
  ![](https://storage.googleapis.com/zenn-user-upload/bce58bdbf074-20250420.png)

- Azure OpenAI に送信されるデータは、容量の地理的領域、コンプライアンス境界、または国内クラウド インスタンスの外部で処理される可能性がある箇所も念の為 On へ設定
  ![](https://storage.googleapis.com/zenn-user-upload/746b6ca5a32e-20250420.png)

以下二つの設定も On へ変更

- 容量は Fabric Copilot 容量として指定できます
- Azure OpenAI に送信されたデータは、容量の地理的リージョン、コンプライアンス境界、または国内クラウド インスタンスの外部に格納できます

### Fabric データ エージェントのテナント設定を有効にするの設定を確認します。

[テナント設定] で、[Fabric データ エージェント] セクションを見つけます。
この設定を有効にするには、次のスクリーンショットに示すように、テナント設定 のオプションをオンにします。
![](https://storage.googleapis.com/zenn-user-upload/b2496066cc4a-20250420.png)

### XMLA エンドポイントを使用して Power BI セマンティック モデルの統合を有効にします。

Fabric データ エージェントは、XMLA (XML for Analysis) エンドポイントを使用して、Power BI セマンティック モデルのクエリと管理をプログラムで実行できます。 この機能を有効にするには、XMLA エンドポイントを正しく構成する必要があります。

[テナント設定]で、[統合設定] セクションに移動します。
次のスクリーンショットに示すように、[XMLA エンドポイントとオンプレミスのデータセットを使用した "Excel で分析" を許可する] を見つけて有効にします。

![](https://storage.googleapis.com/zenn-user-upload/1196a85fabd9-20250420.png)

# Fabric データ エージェントを作成

新しい Fabric データ エージェントを作成するには、まずワークスペースに移動し、[+ 新しい項目] ボタンを選択します。
次に、[すべてのアイテム] タブで、Fabric データ エージェントを選択
![](https://storage.googleapis.com/zenn-user-upload/774f14f1dff8-20250420.png)

**_※2025/4/20 段階では、East US, East US2, South Central US, and West US のエリア且つ F64 の CU で Fabric を起動していないと項目内に出てきません。_**

データエージェントに名前をつけてあげましょう。
![](https://storage.googleapis.com/zenn-user-upload/1b9ea68e689f-20250420.png)

# Fabric データエージェントが参照するデータを選択

Fabric データ エージェントを作成した後は、**_レイクハウス、ウェアハウス、Power BI セマンティック モデル、KQL データベース_** など、最大 5 つのデータ ソースを任意の組み合わせで追加できます。 たとえば、5 つの Power BI セマンティック モデル、または 2 つの Power BI セマンティック モデル、1 つの lakehouse、1 つの KQL データベースを追加できます。

初めて Fabric データ エージェントを作成する場合は、名前を指定する際に OneLake カタログが自動的に表示され、ここからデータ ソースを追加できます。 データ ソースを追加するには、+データソース をクリックして、追加をクリック。

![](https://storage.googleapis.com/zenn-user-upload/1c38eecaac0b-20250420.png)

# Fabric データエージェントの動作確認と公開

データ ソースを追加すると、Fabric データ エージェント ページの左側のペインにあるエクスプローラーに、使用可能なテーブルが選択したデータ ソースごとに設定されます。

今回選択したのは、私の zenn の記事を Notebook から API で取得し、DataLake に格納しています。
特徴を教えて！と質問すると、こんな感じでエクスプローラーで選択したデータソースを参照して AI が回答を考えてくれます。

![](https://storage.googleapis.com/zenn-user-upload/e17c176f16d4-20250420.png)

では、画面上部の公開ボタンから Data Agent を公開していきます。

![](https://storage.googleapis.com/zenn-user-upload/cc5171411ab3-20250420.png)

こんな感じで公開されます。
![](https://storage.googleapis.com/zenn-user-upload/9123254205f0-20250420.png)

# Azure AI Agent Service と Microsoft Fabric Data Agent を連携

では、AI Agent Service 側から Microsoft Fabric で公開した Data Agent を AI Agent と連携させましょう。

AI Foundry にアクセスして、エージェントから新しいエージェントを作成
今回は Fabric-Agent と名前をつけました。

![](https://storage.googleapis.com/zenn-user-upload/4c9a9e4026a0-20250420.png)

ナレッジを+追加をクリック
![](https://storage.googleapis.com/zenn-user-upload/5b52eed1772a-20250420.png)

Fabric を選択
![](https://storage.googleapis.com/zenn-user-upload/8d6ce6da5ff1-20250420.png)

作成した Data Agent のコネクタを選択し接続ボタンをクリック
![](https://storage.googleapis.com/zenn-user-upload/c4413a31d2e6-20250420.png)

これで Fabric と AI Agent Service の連携が完了です。

おつかれさまでした。

これをコードから呼び出す場合、コードは `コードの表示` の箇所に記載してくれています。

```:javascript
import { AIProjectsClient } from "@azure/ai-projects";
import { DefaultAzureCredential } from "@azure/identity";

async function runAgentConversation() {

const client = AIProjectsClient.fromConnectionString(
  "eastus2.api.azureml.ms;xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx;xxxxxxxxxxx",
  new DefaultAzureCredential()
);

const agent = await client.agents.getAgent("asst_xxxxxxxxxxxxxxxxxxxx");
console.log(`Retrieved agent: ${agent.name}`);

const thread = await client.agents.getThread("thread_xxxxxxxxxxxxxxxxxxxxx");
console.log(`Retrieved thread, thread ID: ${thread.id}`);

const message = await client.agents.createMessage(thread.id, {
  role: "user",
  content: "Hello！"
});
console.log(`Created message, message ID: ${message.id}`);

// Create run
let run = await client.agents.createRun(thread.id, agent.id);

// Poll until the run reaches a terminal status
while (
  run.status === "queued" ||
  run.status === "in_progress"
) {
  // Wait for a second
  await new Promise((resolve) => setTimeout(resolve, 1000));
  run = await client.agents.getRun(thread.id, run.id);
}

console.log(`Run completed with status: ${run.status}`);

// Retrieve messages
const messages = await client.agents.listMessages(thread.id);

// Display messages
for (const dataPoint of messages.data.reverse()) {
  console.log(`${dataPoint.createdAt} - ${dataPoint.role}:`);
  for (const contentItem of dataPoint.content) {
    if (contentItem.type === "text") {
      console.log(contentItem.text.value);
    }
  }
}
}

// Main execution
runAgentConversation().catch(error => {
  console.error("An error occurred:", error);
});
```

是非ご活用ください。

BigData の分析 × AI Agent でのデータの分析及びアクションが可能となり、データの構造化から AI の利活用までの流れが一気に加速することが期待されます。

これからも Data/AI 分野の発展に期待が高めて本日の記事を終了したいと思います。

# 最後に

今回の構成で Microsoft Fabric と Azure AI Agent Service を使って、ビッグデータの構造化と AI Agent の連携を実現することが出来ました。

今後も Azure AI Agent Service の SDK がより多くのコネクタに対応するようになりそうなので、引き続き check していきたいと思います。

それでは 👋

# スタートアップ企業様向けのお知らせ

日本マイクロソフトでは、スタートアップ企業様向けに、ビジネスを支援するプログラムをご提供しています。
Azure の無料クレジットが`最大$150,000`もらえるので、是非チェックしてみてください。

https://speakerdeck.com/satakeyusuke19920527/microsoft-for-startups-founders-hub

# 参考資料

https://learn.microsoft.com/ja-jp/azure/ai-services/agents/how-to/tools/fabric?tabs=python&pivots=overview
https://learn.microsoft.com/ja-jp/fabric/data-science/concept-data-agent
https://learn.microsoft.com/ja-jp/fabric/fundamentals/copilot-fabric-overview#available-regions-for-azure-openai-service
https://learn.microsoft.com/ja-jp/fabric/data-science/data-agent-tenant-settings
https://learn.microsoft.com/ja-jp/fabric/data-science/how-to-create-data-agent
