---
title: 'GitHub Copilot を極める会'
emoji: '♥️'
type: 'tech' # tech: 技術記事 / idea: アイデア
topics: ['github', 'copilot', 'agent', 'githubcopilot', 'llm']
published: false
publication_name: microsoft
---

![](https://storage.googleapis.com/zenn-user-upload/a5ee2bfe7017-20260110.png)

# はじめに

GitHub Copilot は、もはや単なるコード補完ツールではありません。
どの文脈を与え、どこまで任せ、どこで人が介入するか
その設計次第で、生産性にもコード品質にも大きな差が生まれます。

本記事「GitHub Copilot を極める会」では、
GitHub Copilot を 補完ツール としてではなく、
設計・実装・リファクタ・テストまでを支援する**開発パートナー**として使い切ることを目標に話を進めます。

中でもエージェントモードを使いこなすことや、カスタムされた命令を Copilot に読み込ませること、更にはコーディングエージェントへコードレビューをさせる方法までこの記事でご紹介させていただきます。

「書かせている」状態から、「制御して使っている」状態へ ---

GitHub Copilot を使う側のレベルを一段引き上げる記事となれば幸いです。

それでは行きましょう 🚀

本記事は GitHub Copilot の基礎ではなく、応用的な内容となります。
基本的な内容から理解されたい場合は以下の記事をご参照ください。
GitHub Copilot を完全に使いこなす会
https://zenn.dev/microsoft/articles/github_copilot_intro

# Visual Studio Code から GitHub Copilot を利用

まずは GitHub にリポジトリを作成してローカルにクローンし、Visual Studio Code を起動してください。
VSCode を起動すると、画面上部にチャットを開くというメニューがありますので、そちらをクリック。
![](https://storage.googleapis.com/zenn-user-upload/0ce369cfc590-20260110.png)

下記画像のように、GitHub Copilot とチャット出来る画面が表示されます。
![](https://storage.googleapis.com/zenn-user-upload/08c58a391fc6-20260110.png)

## GitHub Copilot のモードについて

下記の画像にもある通り、GitHub Copilot にはモードが複数存在します。
![](https://storage.googleapis.com/zenn-user-upload/03db19e91868-20260110.png)
それぞれの役割をいかにまとめます。

| モード名      | 主な用途         | 特徴                                                             |
| ------------- | ---------------- | ---------------------------------------------------------------- |
| Ask           | 質問・理解       | 自然言語で質問し、コードの意味・使い方・エラー原因や対策を確認   |
| Edit          | コード編集       | 既存コードに対して追加・修正・削除を具体的に指示                 |
| Plan          | 設計・整理       | 実行前にタスク分解・手順設計・全体像の整理を行う                 |
| Agent         | 自律的な作業実行 | 抽象的な指示を分解して実行し、ファイル編集やコマンド実行まで対応 |
| AIAgentExpert | Agent 設計・拡張 | カスタム Agent を作成・設定し、特定分野に特化した動作を実現      |

モデルについてはそれぞれの用途に合わせて使っていきましょう。

## GitHub Copilot で使える LLM モデルについて

最近では Agent HQ という概念が GitHub Universe で発表された通り、本当に色々なモデルが GitHub Copilot から利用出来るようになりました。
https://github.blog/jp/2025-10-29-welcome-home-agents/

## Agent HQ とは？

GitHub Universe 2025 で発表された新構想で、あらゆる AI エージェントを単一のプラットフォームとして統合・管理するための基盤 です。
GitHub の既存ワークフロー（Issue、Pull Request、GitHub Actions など）に AI エージェント体験をネイティブ統合 します。

Agent HQ の目的と背景は、現在 AI 活用は多数のツールやインターフェースに分断されているが、一つのインターフェースから呼び出すことが出来るようになれば夢のようだよねという思いを実現したものとなります。
GitHub は AI を単なる補助ツールではなく、開発体験の中核にする ことを目指しているので、大量の AI エージェントを 分散させず統一的に管理・活用できる環境を GitHub Copilot として提供します。

特に**Anthropic**、**OpenAI**、**Google**、**Cognition**、**xAI**など複数のエージェントが利用可能になり、今後、有料 GitHub Copilot サブスクリプションを通じてアクセスできるようになります。

ツール選定で迷うのではなく、GitHub Copilot を選べば全部使えるので開発者としては非常にうれしいですね。

また、ミッションコントロール（Mission Control）という複数エージェントにタスクを割り当て・監視・追跡する 統合ダッシュボードもリリースされます。
主な機能としては以下になります。

- エージェントごとのタスク割り当て・進捗管理
- ブランチコントロール（エージェント生成コードへの CI 実行タイミングなどを制御）
- エージェントのアクセス・ポリシー管理（ID 機能）
- ワンクリックでマージ競合解決
- Slack / Linear / Azure Boards / Teams などとの連携強化

## 各モデルの使い所

現在は非常に多くのモデルが GitHub Copilot から選択出来ます。
![](https://storage.googleapis.com/zenn-user-upload/65c79175711f-20260110.png)
主な使い所や特徴をまとめると以下になります。
| モデル名 | 提供会社 | 主な使いどころ / 特徴 |
| -------------------------------- | ------------ | ------------------------------- |
| **Auto** | GitHub（内部制御） | タスクに応じて最適なモデルを自動選択。迷ったらこれ |
| **GPT-4.1** | OpenAI | 高精度なコード理解・複雑な設計レビュー・難易度高めのリファクタ |
| **GPT-4o** | OpenAI | 高速＋高品質のバランス型。日常的な実装・レビュー・設計相談 |
| **GPT-5 mini** | OpenAI | 軽量・高速。短い補完、簡単な修正、反復作業向き |
| **Claude Haiku 4.5** | Anthropic | 超高速・低コスト。コメント生成、軽い修正、簡単な質問 |
| **Claude Opus 4.5** | Anthropic | 大規模コードベース理解、設計・仕様レビュー、品質重視のタスク |
| **Claude Sonnet 4** | Anthropic | バランス型。リファクタ、PR レビュー、構造整理が得意 |
| **Claude Sonnet 4.5** | Anthropic | Sonnet 強化版。複数ファイル編集・設計補助に最適 |
| **Gemini 2.5 Pro** | Google | 大規模コンテキスト理解、全文解析、ドキュメント＋コード混在 |
| **Gemini 3 Flash (Preview)** | Google | 超高速応答。試行錯誤・ブレスト・即レス用途 |
| **Gemini 3 Pro (Preview)** | Google | 高精度推論。複雑ロジック理解、設計検討 |
| **GPT-5** | OpenAI | 次世代汎用モデル。長期的な設計、Agent 的タスク向け |
| **GPT-5-Codex (Preview)** | OpenAI | コーディング特化。実装スピード重視、Agent 実行向き |
| **GPT-5.1** | OpenAI | GPT-5 の安定版。日常開発の主力モデル |
| **GPT-5.1-Codex** | OpenAI | 高精度コード生成・修正。実装〜修正の主戦力 |
| **GPT-5.1-Codex-Max** | OpenAI | 大規模変更・複雑 PR・Agent HQ 向け |
| **GPT-5.1-Codex-Mini (Preview)** | OpenAI | 軽量 Codex。高速補完・小修正 |
| **GPT-5.2** | OpenAI | 最新・最上位。Agent モード、Plan ＋実装の一気通貫 |

好みに合わせてお好きなモデルを...というところですが、筆者は主に GPT-5.2 や Codex シリーズを中心に使っています。

## プレミアムリクエストについて

![](https://storage.googleapis.com/zenn-user-upload/65c79175711f-20260110.png)
画像の各モデルの横に記載されている ×1 や ×3 などの表記は GitHub Copilot のプレミアムリクエストについての表記となります。
https://docs.github.com/ja/billing/concepts/product-billing/github-copilot-premium-requests
プレミアムリクエストとは、GitHub Copilot の有料機能を使うときに消費する「使い切りポイント」 のようなものです。
Copilot の高度な機能や一部モデルを使うと、このリクエストを消費します。
簡単に言えば、高度なモデルや AI 機能を使う権利を消費するポイントという理解で OK です。

まとめると以下。
| 要素 | 説明 |
| -------------- | --------------------------- |
| **プレミアムリクエスト** | Copilot の「高度機能を使うための消費ポイント」 |
| **いつ消費される？** | チャット・エージェント・高負荷モデルの応答時 |
| **いつリセット？** | 毎月 1 日 |
| **使い切ったら？** | 有料プランは基本機能は使えるが、優先権や高度機能は制限 |

各プランで利用出来るポイントが付与されており、使い切るとそれ以上使いたい場合は課金が必要。また、毎月 1 日にはポイントがリセットされ、再度利用再開出来るようになります。

# Agent モードの使い方について

GitHub Copilot の Agent モードを使いこなすことが出来れば、曖昧な指示であってもこちらの意図を汲み取って自律的に動いてくれるようになります。
誰もが一度は上司から言われたことがある **ええ感じにやっといて** が実現出来るわけです。

Agent に対して事前情報を与えておく為に有効な手段としては以下の 3 つがあります。

- コンテキストの指定
- ツールの準備
- 参照する MCP Server の設定

上記が設定出来ていれば、こちらの指示が多少曖昧なものであったとしても、方針がブレることなく、的確にタスクを遂行してくれます。

それではそれぞれの設定方法について見ていきましょう。

# コンテキスト

コンテキストとは、GitHub Copilot の Agent が応答を生成する際に参照する情報を事前に設定しておくものになります。
![](https://storage.googleapis.com/zenn-user-upload/10a63729a6d8-20260110.png)
指定するものとしては以下のようなものがあります。

- ファイルとフォルダ
- MCP リソース
- スクリーンショットウィンドウ
- ツール...etc

では、コンテキストで具体的にどのような指示でどのような設定が出来るのか確認しましょう。

## ソースコードに関するコンテキスト

### #codebase

現在のワークスペース(ワークスペース内のファイルとフォルダーに関する情報、およびそのワークスペース固有の設定や構成)の内容すべてを Agent が参照します。
![](https://storage.googleapis.com/zenn-user-upload/a9aafdb0fc8a-20260110.png)
画像の例だと、.gitignore や README.md などワークスペース内の全ファイルが参照出来ていますね。

### #file:{FILE_NAME}

ワークスペース内の指定したファイルを Agent が参照します。
![](https://storage.googleapis.com/zenn-user-upload/4aaeb3c63f7c-20260110.png)

画像の例では `.gitignore` ファイルを参照するように指示しています。

実はこれは複数ファイル指定できたりもします。
![](https://storage.googleapis.com/zenn-user-upload/1e7cbfe1a445-20260110.png)
画像の例では `.gitignore`, `README.md` ファイルを参照出来てますね。

他にもアクティブなエディタを対象とすることができる #selection というコンテキストの指定方法もあります。

## ターミナルに関するコンテキスト

### #terminalLastCommand

アクティブなターミナルで最後に実行したコマンドの結果を取得することが出来ます。
![](https://storage.googleapis.com/zenn-user-upload/7ac693f1692f-20260110.png)
上記の画像では、Next.js を起動した`npm run dev`コマンドが実行されたことがわかりますね。

### #terminalSelection

アクティブなターミナルで選択された内容を Agent が取得してくれます。
![](https://storage.googleapis.com/zenn-user-upload/958cabbe10dc-20260110.png)
上記の例では、Next.js が「ワークスペースのルート推測」を誤っている可能性がある、という警告を取得しており、その対応策まで出してくれてますね。

## Git に関するコンテキスト

### #git

現在の Git リポジトリに関する情報が取得出来ます。
![](https://storage.googleapis.com/zenn-user-upload/644c61429d41-20260110.png)
画像の例だと、#git_blanch と指定しているので、ブランチの情報が取得出来ていますね。

### #changes

変更されたファイルに関する情報が取得出来ます。
![](https://storage.googleapis.com/zenn-user-upload/ce88b34721b8-20260110.png)
git status の内容を自然言語で教えてくれるのは見やすくてありがたいですね。

# ツール

ツールは GitHub Copilot の Agent のタスクを実行する際に利用できる外部リソースや機能を設定したり、アクション実行時の権限を渡したりすることが出来ます。
![](https://storage.googleapis.com/zenn-user-upload/fc0f8ac9e502-20260110.png)
設定出来るものとしては以下の 3 種類があります。

- 組み込み機能
  - Visual Studio Code で標準利用できる機能
- 拡張機能
  - Visual Studio Code に追加した拡張機能によって利用可能になった機能
- MCP
  - 生成 AI の機能拡張を行うプロトコル。MCP に対応したサーバーを登録すると、該当サーバーの機能が利用可能

## 組み込み機能

VSCode 標準で組み込まれている機能をツールとして設定すると、Agent から参照することが出来ます。
![](https://storage.googleapis.com/zenn-user-upload/91066c319ece-20260110.png)
上記の画像のように組み込みと記載された箇所のチェックマークを入れると、Agent からその機能が利用出来るようになります。

VSCode 標準の組み込み機能としては以下のようなものがあります。

- **changes**  
  変更されたファイルの差分を取得します

- **edit**  
  ワークスペース内のファイルを編集します

- **extensions**  
  VS Code の拡張機能を検索します

- **fetch**  
  Web ページからメインコンテンツをフェッチします  
  （フェッチするページの URL を指定）

- **githubRepo**  
  GitHub リポジトリに関連するソースコードやスニペットを検索します

- **newScaffold**  
  新しいワークスペースを VS Code 固有の構成で作成し、  
  コンパイル・デバッグなどを行えるようにします

- **openSimpleBrowser**  
  Simple Browser でローカルでホストされている Web サイトをプレビューします

- **problems**  
  ファイルのエラーを確認します

- **runCommands**  
  ターミナルでコマンドを実行します

- **runNotebooks**  
  Notebook のセルを実行します

- **runSubagent**  
  タスクを別の分離されたサブエージェントコンテキストで実行します  
  （効率的なタスク分割を可能にします）

- **runTasks**  
  タスクを実行し、ワークスペースの出力を取得します

- **runTests**  
  単体テストを実行します

- **search**  
  ワークスペース内のファイルを検索します

- **testFailure**  
  前回の単体テストの失敗に関する情報を含めます

- **todos**  
  タスク計画のための ToDo 項目の管理および追跡ツール

- **usages**  
  シンボルの参照、定義、その他の使用箇所を検索します

結構色々あるので、用途に分けて使い分けると良いですね。

## 拡張機能

Visual Studio Code で拡張機能として組み込まれている機能をツールで設定すると参照することが出来ます。

例えば VSCode 拡張機能の箇所から以下の GitHub Pull Requests の機能を見てみましょう。
![](https://storage.googleapis.com/zenn-user-upload/f4d3e689c3a8-20260110.png)

**チャット参加者** という箇所に名前があるのがわかりますね。ここをチェックしておきます。
次にこの機能をインストールしてみると、ツールの設定の箇所から GitHub Pull Requests がツールで選択出来るようになります。
![](https://storage.googleapis.com/zenn-user-upload/ca5d2f6597ae-20260110.png)

これで組み込み機能を GitHub Copilot の Agent からツールとして利用出来るようになりました。非常に簡単ですね。

## MCP

### MCP（Model Context Protocol）とは？

そもそも MCP とはというところですが、**MCP は、生成 AI（GitHub Copilot / Agent）が外部システムの情報や機能を、安全かつ共通ルールで利用するためのプロトコル**です。

![](https://storage.googleapis.com/zenn-user-upload/2e0cbf127c8b-20260110.png)

### MCP Client（左側）

- GitHub Copilot
- Visual Studio Code
- Copilot Chat / Plan / Agent

👉 ユーザーが操作し、AI が実行する側

### MCP（中央）

- Model Context Protocol
- どの情報に、どの権限で、どんな形式でアクセスできるかを定義

👉 AI と外部サービスをつなぐ「安全な橋渡し」

### MCP Server（右側）

- GitHub
- Microsoft Docs

👉 実データ・実機能を提供する側

MCP でできることは以下になります。

- 外部サービスの情報取得（Issues / PR / Docs / チケット）
- 外部サービスへの操作（PR 作成、Issue 更新、ページ作成）
- 権限・スコープを明確にした安全な実行
- 複数サービスを同一ルールで扱う

MCP がない時とある時

#### MCP ない時

- API ごとに個別実装が必要
- 権限や安全性が曖昧
- Agent が実務を代行しにくい

#### MCP ある時

- アクセス範囲が明確
- 読み取り / 書き込み / 実行を制御可能
- Agent が業務を安全に実行できる

具体例としては以下

### GitHub × MCP

- Issue を読む
- PR を作成・更新
- 差分を確認...etc

### Microsoft Docs × MCP

- 最新公式ドキュメントを参照
- 古い知識を避けた回答...etc

Agent が参照してくれる先が MCP で構築された共通のルールで通信出来る為、いい感じに外部サービスの情報を Agent が取得してくれるように出来るのが MCP の強みですね。

### 公開 MCP の追加

以下のサイトより、公開されている MCP サーバーを確認出来ます。

https://code.visualstudio.com/mcp

![](https://storage.googleapis.com/zenn-user-upload/8f99f1e6121c-20260110.png)

では実際に MCP サーバーを追加してみましょう。

まずは VSCode の拡張機能の箇所から Enable MCP Servers Marketplace のボタンをクリックします。
![](https://storage.googleapis.com/zenn-user-upload/53258b861491-20260110.png)

今回はこの Microsoft Learn MCP Server を追加してみます。
![](https://storage.googleapis.com/zenn-user-upload/29725829cc19-20260110.png)

すると、画面左下の箇所に Microsoft Learn の MCP サーバーが追加されたことがわかりますね。
では、画面右側の GitHub Copilot で Microsoft Learn MCP Server を用いて質問してみましょう。

質問は以下にしてみました。

> Azure で GitHub へ push したコードを自動で Azure Container Apps へ Deploy したい。手順を教えて。

途中経過では、先ほど追加した MCP サーバーから情報を取得していることがわかりますね。
![](https://storage.googleapis.com/zenn-user-upload/954d9cd22026-20260110.png)

そして最終的にすごく丁寧に手順を教えてくれました。
![](https://storage.googleapis.com/zenn-user-upload/6e082764161f-20260110.png)

すばらしいですね。是非色々な MCP サーバーを試してみてください。

### MCP 手動追加

手動追加は直接 MCP の設定ファイルに追記して MCP サーバーを追加する方法になります。
MCP の設定ファイルは以下で開くことが出来ます。

VSCode 画面上部の検索窓より以下を入力で開くことが出来ます。

> MCP: Open User Configuration
> ![](https://storage.googleapis.com/zenn-user-upload/ea9a84151ba8-20260110.png)

こんな感じの設定ファイルが開けると OK です
![](https://storage.googleapis.com/zenn-user-upload/2ec25021c318-20260110.png)

設定ファイルはこんな感じになっていると思います。
先ほど追加した microsoftdocs/mcp というサーバーが登録されているのがわかりますね。
json の"servers"のセクションに MCP サーバーを追記していきます。

```:json
{
	"servers": {
		"microsoftdocs/mcp": {
			"type": "http",
			"url": "https://learn.microsoft.com/api/mcp",
			"gallery": "https://api.mcp.github.com",
			"version": "1.0.0"
		}
	},
	"inputs": []
}
```

リモート上にある MCP Server を追加する際のフォーマットは以下になります。

```:json
{
  "servers": {
    "<MCP_SERVER_NAME>": {
      "type": "http or sse",
      "url": "<MCP_SERVER_URL>"
    }
  },
  "inputs": []
}
```

ローカル上にある MCP Server を追加する際のフォーマットは以下になります。

```:json
{
  "servers": {
    "<MCP_SERVER_NAME>": {
      "type": "npx or uv or java or dotnet",
      "args": [
        "-y",
        "@azure/mcp@latest",
        "server",
        "start",
      ]
    }
  },
  "inputs": []
}
```

MCP Server の情報にシークレットを記載したい場合は以下のように含めることが出来ます。

```:json
{
  "servers": {
    "<MCP_SERVER_NAME>": {
      "type": "npx or uv or java or dotnet",
      "args": [
        "-y",
        "@azure/mcp@latest",
        "server",
        "start",
      ],
      "env": {
        "API_KEY": "${input:my_api_key}" // シークレットを参照
      }
    }
  },
  "inputs": []
}
```

#### MCP 手動追加で一つ MCP Server を追加してみましょう

今回はローカルのファイルを検索してくれる MCP Server を追加してみます。

以下のコードを install しておきます。

```: bash
npm install -g @modelcontextprotocol/server-filesystem
```

![](https://storage.googleapis.com/zenn-user-upload/00a8f0ed97e2-20260110.png)

そして、先ほど開いた MCP の設定ファイルに以下のコードを追記して保存してください。

![](https://storage.googleapis.com/zenn-user-upload/89443a66efad-20260110.png)
設定を保存したら、画面左下の MCP Server の箇所に filesystem が追加されていることがわかりますね。

追加部分は以下になります。

```:json
"filesystem": {
  "type": "stdio",
  "command": "npx",
  "args": [
    "-y",
    "@modelcontextprotocol/server-filesystem",
    "/work" // この箇所は検索したいフォルダパスに変更してください。
  ]
}
```

これでローカルのファイルが MCP Server 経由で検索出来るようになりました。
![](https://storage.googleapis.com/zenn-user-upload/45c905874a85-20260110.png)

色々と利用ユースケースありそうですね。

## ツールセットの作成

ツールセットとは、GitHub Copilot の Agent がタスクを実行する際に利用できるツールの集合体を定義したものです。

実はツールは 128 個までしか設定出来ず、毎回全てのツールを選択していると、足りなくなる可能性があります。
![](https://storage.googleapis.com/zenn-user-upload/07541ad23588-20260110.png)

そうした時にツールセットを作成しておくことで、複数のツールセットを切り替えながら利用出来るようになります。

ツールセットの作成方法は以下になります。
まずは VSCode 右上の歯車マークをクリックし、ツールセット を開きます。
![](https://storage.googleapis.com/zenn-user-upload/7111d320dc65-20260110.png)

新しいツールセットファイル作成を選択
![](https://storage.googleapis.com/zenn-user-upload/0be25be17464-20260110.png)

最初はこんな感じで作成されます。
![](https://storage.googleapis.com/zenn-user-upload/1891e40b46ef-20260110.png)

それぞれの項目を説明すると以下になります。

- toolSetName: ツールセットの名前
- tools: ツールセットに含めるツールのリスト
- description: ツールセットの説明
- icon: ツールセットのアイコン

ツールセットのアイコンは以下サイトから選択できます。
https://code.visualstudio.com/api/references/icons-in-labels

今回ツールセットには先ほど追加した、Microsoft Learn MCP Server と filesystem MCP Server を追加してみます。

```:json
{
  "sample-toolset": {
    "tools": ["filesystem", "microsoftdocs/mcp"],
    "description": "sample toolset has two MCP servers.",
    "icon": "tools"
  }
}
```

こんな感じになりました。
![](https://storage.googleapis.com/zenn-user-upload/f18843b8c54e-20260110.png)

では、VSCode 右下のツールの箇所をクリックして、ツールセットを確認してみましょう。

作成したツールセットが VSCode 上で選択出来るようになっていますね！
![](https://storage.googleapis.com/zenn-user-upload/45878c00a9e9-20260110.png)

これで、プロジェクトに合わせてツールセットの切り替えが簡単に出来るようになります。

是非活用してみてください。
