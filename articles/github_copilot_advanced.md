---
title: 'GitHub Copilot を極める会'
emoji: '♥️'
type: 'tech' # tech: 技術記事 / idea: アイデア
topics: ['github', 'copilot', 'agent', 'githubcopilot', 'llm']
published: false
publication_name: microsoft
---

![](https://storage.googleapis.com/zenn-user-upload/f21551400bda-20260112.png)

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

本記事は GitHub Copilot の**Agent モード**の使い方や**MCP**、**カスタム命令**などの高度な内容を扱います。

基本的な内容から理解されたい場合は以下の記事をご参照ください。

- GitHub Copilot を完全に使いこなす会

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

![](https://storage.googleapis.com/zenn-user-upload/f43fe868493f-20260110.png)

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
現時点の使い心地としては、Codex が一番 Good かなと感じています。

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

# MCP

### MCP（Model Context Protocol）とは？

そもそも MCP とはというところですが、**MCP は、生成 AI（GitHub Copilot / Agent）が外部システムの情報や機能を、安全かつ共通ルールで利用するためのプロトコル**です。

![](https://storage.googleapis.com/zenn-user-upload/2e0cbf127c8b-20260110.png)

### MCP Client

- GitHub Copilot
- Visual Studio Code
- Copilot Chat / Plan / Agent

👉 ユーザーが操作し、AI が実行する側

### MCP

- Model Context Protocol
- どの情報に、どの権限で、どんな形式でアクセスできるかを定義

👉 AI と外部サービスをつなぐ「安全な橋渡し」

### MCP Server

- GitHub
- Microsoft Learn

👉 実データ・実機能を提供する側

MCP でできることは以下になります。

- 外部サービスの情報取得（Issues / PR / Docs / チケット）
- 外部サービスへの操作（PR 作成、Issue 更新、ページ作成）
- 権限・スコープを明確にした安全な実行
- 複数サービスを同一ルールで扱う

MCP がない時とある時を比較すると以下。

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

まずは VSCode の拡張機能の箇所から左下の MCP サーバーと記載されている箇所にある Enable MCP Servers Marketplace のボタンをクリックします。
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

設定を保存したら、画面左下の MCP Server の箇所に filesystem が追加されていることがわかりますね。

![](https://storage.googleapis.com/zenn-user-upload/65b408f6f07f-20260110.png)

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

# カスタム命令

GitHub Copilot に対して「常に守ってほしい前提条件・振る舞い」を事前に与える設定機能です。
毎回プロンプトで説明しなくても、Copilot が“空気を読んで”動くようになります。

一言で言うと 👇
**Copilot の性格・前提知識・ルールを定義する仕組み** です。

では、設定すると何が変わるのか？

カスタム命令なしだと...

- 毎回ルールを説明する必要がある
- 人によって回答品質がブレる
- プロジェクト固有ルールを守らないことがある

カスタム命令ありだと...

- ✅ 常に同じルールでコード生成
- ✅ プロジェクト文脈を理解した回答

カスタム設定は個人用だと、出力を簡潔・詳細を設定出来たり、コメントを英語・日本語の設定、思考プロセスの説明有無の表示が設定出来たりします。

Agent にこんな感じのルールで実装しているから、ええ感じに引き継いでほしいルールを記載するイメージですね。

Agent 向けのカスタム命令には以下のようなものがあります。
| ファイル名 | 役割 | 効果 |
|---|---|---|
| AGENTS.md | AI（Copilot/Agent）の最上位ルール | 技術スタック固定・Copilot 暴走防止 |
| instructions.md | 実装・設計・UI・セキュリティの共通ルール | 実装品質の安定・判断ブレ防止 |
| docs/introduction.md | アプリの概要・目的・非目的を定義 | 要件誤解の防止・AI の文脈理解向上 |
| docs/architecture.md | 全体アーキテクチャ定義（Client/Backend/Data） | 構成の一貫性維持・勝手な設計変更防止 |
| docs/api/README.md | API 全体の方針・共通仕様 | API 設計の統一・破壊的変更防止 |
| docs/api/xxx.md | xxx API の契約定義 | フロント/バックの連携安定 |
| docs/decisions/README.md | ADR（設計判断）一覧 | 設計理由の可視化・将来の迷走防止 |

ディレクトリ構造としては以下のようなイメージです。

```
.
├── AGENTS.md *important
├── instructions.md *important
├── docs/
│   ├── introduction.md *important
│   ├── architecture.md *important
│   ├── api/
│   │   ├── README.md
│   │   ├── daily.md
│   │   └── user.md
│   └── decisions/
│       ├── README.md
└── src/
    ├── app/
    │    ├── api/
    │    ├── pages/
    │    └── layout.tsx
    │    └── page.tsx
    ├── components/
    └── lib/
```

この中で特に重要なのが、

- AGENTS.md
- instructions.md
- docs/introduction.md
- docs/architecture.md

のあたりが重要です。(最悪これだけあれば OK です。)
AI 駆動開発を進める上で、これらのドキュメントがあると Agent が迷いなく実装することが出来、その結果、品質の高いコードが生成されやすくなります。

その結果、手戻りが減少し、開発スピードが向上する傾向にあります。

## カスタム命令の設定方法 (xxx.instructions.md)

GitHub Copilot の挙動を自分に合った形に調整できる設定機能のことです。
記載の仕方でいくと以下のように書き分けるイメージです。

- 個人向けのカスタム命令の記載
  - 回答は結論 → 理由 → 手順の順で
  - 不確実な点は明示する
  - 可能な限り実務ベースで説明する
- リポジトリ向けのカスタム命令の記載
  - このプロジェクトは Next.js + TypeScript
  - state 管理は Redux Toolkit
  - UI は shadcn/ui と Tailwind CSS
  - 命名は camelCase、関数は動詞始まり
  - Database は CosmosDB...etc

では、実際にカスタム命令を設定してみましょう。

GitHub Copilot の 📎 マークをクリックして、手順を選択
![](https://storage.googleapis.com/zenn-user-upload/c5651aa5ee8b-20260112.png)

手順の構成を選択
![](https://storage.googleapis.com/zenn-user-upload/fde03e907ca3-20260112.png)

新しい命令ファイルを選択
![](https://storage.googleapis.com/zenn-user-upload/d973f895d0cd-20260112.png)

.github/instructions を選択
![](https://storage.googleapis.com/zenn-user-upload/8bf8082b2158-20260112.png)

名前を入力すると、**xxx.instractions.md** というファイルが作成されます。
![](https://storage.googleapis.com/zenn-user-upload/74a808d2dc66-20260112.png)

ここに個人用の指示や、リポジトリのルールなどを記載していきます。
そうすると、GitHub Copilot の Agent がこの指示を読んでいい感じに動いてくれるようになります。

### カスタム命令の記載例

カスタム命令の実態は instructions.md という Markdown ファイルです。
サンプルを記載すると以下のようになります。

```:markdown:instructions.md
---
applyTo: '**'
---

# Basic Instructions
- 常に日本語で応答すること（ユーザー向け出力・説明・エラーメッセージを含む）。
- 技術用語は必要に応じて英語併記してよいが、本文は日本語を主とすること。

# Tech Stack / Constraints
- アプリケーションは **Next.js（App Router）+ TypeScript** で実装すること。
- 認証は **Clerk** を使用すること。
- データベースは **Azure Cosmos DB** を使用すること。
- 環境変数は **`.env.local`** にのみ定義し、リポジトリにコミットしないこと（`.gitignore` に含める）。
- 追加ライブラリ導入は最小限とし、導入理由を明確にすること。

# UI / Design
- CSSは **Tailwind CSS** と **shadcn/ui** を用いてモダンなデザインにすること。
- **レスポンシブ対応必須**：
  - スマホ（例：375px前後）とiPad（例：768px前後）を主要ターゲットとして最適化する。
  - Tailwindのブレークポイント（`sm`, `md`, `lg`）を適切に用い、崩れないUIにする。
  - タップ操作を前提に、ボタン/入力のヒット領域は十分に確保する（目安：44px相当以上）。
- UIコンポーネントは可能な限り shadcn/ui を利用し、独自実装は必要最小限にすること。

# Architecture / Implementation Rules
- Next.jsの **Server/Client境界** を明確にすること。
  - Server Componentsを基本とし、Client Componentsは必要な箇所に限定する（`"use client"` の乱用を避ける）。
- API呼び出しは **Route Handlers（`app/api/**`）** を基本とする（要件に応じてServer Actionsも可）。
- Cosmos DBアクセスはサーバー側で行い、クライアントに接続文字列などを露出させないこと。
- データアクセス層（Cosmos）とUI層を分離し、関心の分離を保つこと。

# State Management (Redux Toolkit)
- クライアント状態管理は **Redux Toolkit** を使用すること。
  - UI状態（モーダル開閉、フィルタ、フォーム一時状態など）や、必要なキャッシュに限定する。
  - サーバー状態の過剰な複製は避け、取得・更新の責務を明確にする。
- Slice/Thunkは責務ごとに分割し、命名規則を統一すること。

# Security
- Clerkのセッション/ユーザーIDを基準に、**ユーザー単位のデータ分離**（AuthZ）を徹底すること。
- 入力値は必ずサーバー側で検証し、不正入力・権限外アクセスを防止すること。
- 機密情報（キー、トークン、接続文字列）はログ出力しないこと。

# Commit Message
- コミットメッセージは日本語で記述すること。
- 形式は次を推奨：`<種別>: <要約>`
  - 例：`機能: 日記投稿APIを追加`
  - 例：`修正: Clerkセッション検証の不具合を修正`
  - 例：`整理: UIコンポーネントの配置を整理`

# Code generation
- 生成するコードには可能な限りコメントを付与し、意図・前提・注意点を明確にすること。
- 型（TypeScript）を適切に付与し、`any` の乱用を避けること。
- 変更時は「目的」「影響範囲」「確認方法（コマンド）」が分かるように記述すること。

# Verification
- 変更後は最低限、以下が通る状態を維持すること：
  - `lint`
  - `typecheck`
  - `build`
- 追加・変更した機能には、動作確認手順を併記すること（UI操作手順またはAPI例）。


```

ずらっと書いてますが、こういった感じでルールを定義しておくと、毎回同じ説明をしなくても GitHub Copilot がここを見ていい感じに動いてくれるようになります。

毎回プロンプトに同じ説明を書くのは大変なので、是非カスタム命令を活用してみてください。

その他のファイルについては、基本的には GitHub Copilot に作成してと頼めばいい感じに作成してもらえるようになります。
開発が進むにつれて、定期的にカスタム命令を更新していくと良いでしょう。

# コーディングエージェント

Issue に書かれたユーザーのやりたいこと（実装したい内容）を解釈、反復的に実装することが可能。
特徴としては、以下。
・抽象的な内容を小さなステップに分割して反復実行
・エラーも自動的に検知、解消
・出来上がったコードは Pull Request を作成

issue の修正もこれからは GitHub Copilot にお任せ出来る時代がきています。

sample として、コーディングエージェントがどんな感じで働いてくれるのか見てみましょう。

## サンプルアプリケーションをバイブコーディングで作ってみる 🎸

コーディングエージェントで修正することを体験する為に、簡単な日記アプリを作成してみましょう。
筆者のリポジトリは以下になります。
https://github.com/SatakeYusuke19920527/github-copilot-zenn

ローカルにクローンして、Visual Studio Code で開きます。
![](https://storage.googleapis.com/zenn-user-upload/7b69330b0bd6-20260113.png)

せっかくなので、最初のアプリもコーディングエージェントに作成してもらいましょう。
簡単なアプリケーションを作ってみます。

GitHub Copilot で以下のように指示します。
以下を GitHub Copilot のチャットに貼り付けてアプリを Agent に実装してもらいましょう。

```
あなたはシニアフルスタックエンジニアです。Next.js（App Router）+ TypeScriptで「日記WEBアプリ」を実装してください。
状態管理は Redux Toolkit を使用し、UI は Tailwind CSS + shadcn/ui を用いたモダンなデザインにしてください。

# 技術スタック / 制約
- Next.js（App Router）, TypeScript
- Redux Toolkit（store, slice, typed hooks）
- Tailwind CSS + shadcn/ui
- まずはローカル状態（永続化なし）で CRUD が完結する構成にする（DB/認証は不要）

# 機能要件（必須）
1) 日記一覧表示
- トップページに日記一覧を表示
- 各日記カードに「タイトル」「本文の冒頭」「更新日時」を表示
- 日記の一覧表示はshadcn/ui の Card コンポーネントを利用
- Card上部にunsplash(https://unsplash.com/ja)を用いて各日記のイメージ画像を作成して
- 日記が0件の場合は空状態（Empty State）を表示
- 3×3のグリッドレイアウトで表示（レスポンシブ対応）

2) 追加（Create）
- 画面右下に固定配置の「+」フローティングボタン（FAB）を配置
- クリックで新規作成用の Dialog（モーダル）を開く
- 入力項目: title（必須）, content（必須）
- 保存すると一覧に即反映し、Dialogは閉じる

3) 編集（Update）
- 一覧の各カードに「Edit」ボタン
- クリックで編集用 Dialog を開く（既存値を初期値として表示）
- 保存で更新し、一覧に即反映

4) 削除（Delete）
- 一覧の各カードに「Delete」ボタン
- 誤削除防止のため Confirm Dialog を挟む（OKで削除）
- 削除後は一覧に即反映

# 画面/UI要件
- Tailwindで余白・文字サイズ・カードレイアウトを整える
- shadcn/ui の Button / Card / Dialog / Input / Textarea を利用
- FAB（+ボタン）は右下 fixed、丸型、影あり、hoverで視認性が上がる
- レスポンシブ（スマホ〜デスクトップ）で崩れないように

# 状態管理（Redux Toolkit）
- diarySlice を作成し、stateは以下の形にする
  - diaries: Diary[]
  - selectedId: string | null（必要なら）
- Diary型:
  - id: string
  - title: string
  - content: string
  - createdAt: string（ISO）
  - updatedAt: string（ISO）
- actions:
  - addDiary(payload: {title, content})
  - updateDiary(payload: {id, title, content})
  - deleteDiary(payload: {id})
- store設定と typed hooks（useAppDispatch/useAppSelector）を用意
- App Router で Redux Provider を app/providers.tsx 等に分離して layout.tsx に組み込む
- ReduxToolkitは永続化出来るようにして

# 実装方針
- 追加/編集は共通の DiaryFormDialog コンポーネントにまとめる（mode: "create" | "edit"）
- 削除は ConfirmDialog コンポーネントに分離する
- ID生成は crypto.randomUUID() を使用（利用不可ならフォールバック実装）
- 日時は new Date().toISOString()

# 成果物
- 主要ファイルの作成/編集を行い、動作する状態にしてください
  - app/page.tsx（一覧画面）
  - app/layout.tsx（Provider組み込み）
  - app/providers.tsx（Redux Provider）
  - lib/store.ts, lib/hooks.ts
  - features/diary/diarySlice.ts
  - components/DiaryList.tsx, DiaryCard.tsx, DiaryFormDialog.tsx, ConfirmDialog.tsx, FloatingAddButton.tsx
- 可能なら簡単な初期ダミーデータを1件入れて UI 確認しやすくする（任意）

まずは「動く最小構成（MVP）」を完成させ、次にUIの整え（余白、カード、DialogのUX）まで仕上げてください。

```

実装中はこんな感じです。
![](https://storage.googleapis.com/zenn-user-upload/ffc1b95305ca-20260113.png)

途中、create-next-app のコマンドを実行して良いか聞いてくれます。
![](https://storage.googleapis.com/zenn-user-upload/11a074ce1465-20260113.png)

許可すると、ターミナルも使って作っていってくれます。
![](https://storage.googleapis.com/zenn-user-upload/423a2efb80c0-20260113.png)

次は ReduxToolkit や shadcn/ui のインストールも聞いてくれます。
![](https://storage.googleapis.com/zenn-user-upload/a94ed8594133-20260113.png)

頑張ってくれている進捗は以下の TODO の箇所で確認出来ます。いいですね。
![](https://storage.googleapis.com/zenn-user-upload/cdad783909cc-20260113.png)

作っている途中でエラーが発生したら、自動的にエラー内容を解析して修正も行ってくれます。
![](https://storage.googleapis.com/zenn-user-upload/6012850f38b9-20260113.png)

完成したら、サマリーも作成してくれます。
![](https://storage.googleapis.com/zenn-user-upload/eeb538b28fb7-20260113.png)

では、ターミナルで以下のコマンドを実行してアプリを起動してみましょう。

```
npm run dev
```

筆者のアプリは以下のようになりました。(いい感じですね)
![](https://storage.googleapis.com/zenn-user-upload/45d11bc2ef81-20260113.png)

日記も追加してみました。
![](https://storage.googleapis.com/zenn-user-upload/a3c3f932e37d-20260113.png)
いい感じで追加できてますね。
![](https://storage.googleapis.com/zenn-user-upload/b91edf22ed57-20260113.png)

ReduxToolkit で永続化ができているかどうかも確認してみましょう。
画面をリロードしても、追加した日記が残っていることがわかりますね。

これでサンプルアプリの実装は完了なので、GitHub へ push しておきましょう。
最近はコミットメッセージももっぱら AI で次自動生成です。

push できたら次は GitHub Copilot でコーディングエージェントを使ってみます。
![](https://storage.googleapis.com/zenn-user-upload/057af7e25f43-20260113.png)

## コーディングエージェントを Issue へ割り当て

では、さっそくコーディングエージェントを Issue に割り当てて、アプリのバグを修正してもらいましょう。
https://github.com/SatakeYusuke19920527/github-copilot-zenn

では、issue を作成してみます。
アプリには Header と Footer がないので、Header と Footer を追加する Issue を作成してみます。

Issue→New issue から以下のように記載して作成します。
![](https://storage.googleapis.com/zenn-user-upload/1bb37236e5b3-20260113.png)

Issue の titile は以下にしてみました。

> Header / Footer 追加 Issue 作成の依頼

内容は以下のように記載しています。

```
## 背景
現在の「日記WEBアプリ」には、グローバルな **Header** および **Footer** が存在していません。
アプリ全体の情報構造・ナビゲーション・視認性を向上させるため、Header / Footer を追加するための Issue を新規に作成したいと考えています。

## 依頼内容
このリポジトリに対して、**Header / Footer を追加** してください。

### 実装要件

#### Header
- 全ページ共通で表示
- 左側にアプリ名（例: Diary App）
- 右側に将来拡張を考慮したナビゲーション領域（今はプレースホルダーでOK）
- 高さは固定、スクロールしても常に上部に表示される（sticky or fixed）
- Tailwind CSS + shadcn/ui を使用
- レスポンシブデザインでスマホでも快適に見られるようにしてください。

#### Footer
- 全ページ共通で表示
- アプリ最下部に配置
- コピーライト表記（例: © 2026 Diary App）
- シンプルで主張しすぎないデザイン
- Tailwind CSS + shadcn/ui を使用
- レスポンシブデザインでスマホでも快適に見られるようにしてください。

### 4. 技術的制約
- Next.js App Router を前提
- `app/layout.tsx` に組み込む構成
- Header / Footer はそれぞれ独立したコンポーネントとして実装
- TypeScript を使用

### 5. 完了条件（Acceptance Criteria）
- すべてのページで Header / Footer が表示されている
- レスポンシブ対応（モバイル / デスクトップ）
- 既存の UI や機能（日記の CRUD）に影響がない
- コードが読みやすく、責務が分離されている

## 補足
- デザインは「モダン・シンプル」を重視
- 今後、認証やナビゲーションが追加される前提で拡張しやすい構造にする
```

では、ここまで記載したら、Assigners の箇所で GitHub Copilot を選択して割り当てます。
![](https://storage.googleapis.com/zenn-user-upload/1f4b3174e59c-20260113.png)
Create をクリックして Issue を作成します。
![](https://storage.googleapis.com/zenn-user-upload/c1b2688e105f-20260113.png)

👀 のマークがついて GitHubCopilot が確認したことが確認でき、[WIP] Work in Progress の記載があれば GitHub Copilot が仕事を進めてくれています。
![](https://storage.googleapis.com/zenn-user-upload/fec40b2a0c4f-20260113.png)

そして、実装完了したら pull request が自動的に作成されます。すごいですね。
![](https://storage.googleapis.com/zenn-user-upload/62065b27a81e-20260113.png)

では、GitHub Copilot が作成した pull request を確認してみましょう。
Changes が綺麗にまとまってますね。
![](https://storage.googleapis.com/zenn-user-upload/d367c3984b67-20260113.png)

そして最近驚きなのがスクリーンショットも自動的に追加してくれるんですよね。
![](https://storage.googleapis.com/zenn-user-upload/fb06bca78afa-20260113.png)

しかも、レスポンシブ後のモバイルも。
![](https://storage.googleapis.com/zenn-user-upload/8b0953a3c599-20260113.png)

では Marge してローカルで動作を確認してみましょう。
![](https://storage.googleapis.com/zenn-user-upload/570607bf0bf0-20260113.png)

いい感じに Header と Footer が追加されていますね。すばらしい働きです。
![](https://storage.googleapis.com/zenn-user-upload/7bdaf67951be-20260113.png)

# コードレビュー

次に Pull Request（PR）に対して GitHub Copilot が自動的にレビューコメントを生成してくれます。

特徴としては以下になります。
・PR に Copilot を割り当てると自動的にコメントを作成
・セキュリティリスク、バグの可能性、冗長な処理、ベストプラクティスに沿っているかなどを指摘
・指摘対応も同時に提案

これで、PR をチェックする開発リーダーの負担が大幅に低減されそうですね。
それでは、使ってみましょう。

先ほどのアプリに以下のエラーが出ていることがわかりました。
![](https://storage.googleapis.com/zenn-user-upload/ee09ca989083-20260113.png)

Issue を作成して、人間が修正して pull request を出して GitHub Copilot に Review してもらいましょう。

では、Issue を作成します。
また、New Issue から以下のように記載して作成します。

titile

> Hydration failed because the server rendered text didn't match the client. As a result this tree will be regenerated on the client. This can happen if a SSR-ed Client Component used:

エラー内容

````
# React Hydration Error 修正内容

## エラー概要

Next.js（App Router）において **Hydration Error** が発生しています。

主な症状は以下です：

- サーバーで生成された HTML と
- クライアントで初回レンダリングされた HTML

が一致していないため、React が再構築（hydrate）できずエラーになっています。

今回の差分では、**DiaryCard 内の表示内容が Server / Client で異なっている**ことが原因です。

```diff
+ src="https://images.unsplash.com/photo-1504384308090-..."
- src="https://images.unsplash.com/photo-1496307042754-..."

+ alt="test title"
- alt="はじめての日記"

+ test title
- はじめての日記
根本原因（今回のケース）
❌ 問題点
以下のいずれか、もしくは複合です。

初期表示時に Math.random() や Date.now() を直接使っている

サーバーとクライアントで初期データが一致していない

Diary データを Client Component 側で生成している

useEffect 前後で表示内容が変わっている

今回のログから判断すると、最も可能性が高いのは：

日記データ（title / image URL）を Client Component 側でランダム生成している

修正方針（正解）
原則
初回レンダリングで使うデータは、Server / Client で完全に一致させる

修正方法①（推奨）
ランダム値・日時生成を reducer or action 内に移動する
❌ NG（Component 内で生成）

ts
コードをコピーする
// ❌ ダメ：レンダリング毎に変わる
const diary = {
  id: crypto.randomUUID(),
  title: Math.random() > 0.5 ? 'test title' : 'はじめての日記',
}
✅ OK（dispatch 時に一度だけ生成）

ts
コードをコピーする
// diarySlice.ts
addDiary: (state, action) => {
  state.diaries.push({
    id: crypto.randomUUID(),
    title: action.payload.title,
    content: action.payload.content,
    imageUrl: action.payload.imageUrl,
    createdAt: new Date().toISOString(),
    updatedAt: new Date().toISOString(),
  })
}
➡ レンダリング中に値が変わらなくなる

修正方法②
初期ダミーデータを固定値にする
ts
コードをコピーする
const initialState = {
  diaries: [
    {
      id: 'dummy-1',
      title: 'はじめての日記',
      content: 'これはサンプルの日記です',
      imageUrl: 'https://images.unsplash.com/photo-1496307042754-b4aa456c4a2d',
      createdAt: '2026-01-01T00:00:00.000Z',
      updatedAt: '2026-01-01T00:00:00.000Z',
    },
  ],
}
➡ Server / Client で 完全一致

修正方法③（最終手段・非推奨）
Client Only Rendering にする
ts
コードをコピーする
'use client'
import dynamic from 'next/dynamic'

export default dynamic(() => import('./DiaryList'), {
  ssr: false,
})
⚠️ SEO・初期表示性能が落ちるため非推奨

今回のケースでの結論（ベストプラクティス）
✅ DiaryCard / DiaryList は「純粋な表示コンポーネント」にする
✅ データ生成（ランダム・日時）は reducer / action で一度だけ行う
✅ 初期表示に使う値は固定 or サーバー由来にする
````

今度は人間が Issue を修正して pull request を出します。
まずは、ローカルでブランチを作成し、修正を行います。
ここは GitHub Copilot chat を使って修正を行います。
![](https://storage.googleapis.com/zenn-user-upload/b8aa14461fbf-20260113.png)

ローカル環境で修正されていることが確認出来たので、これで OK。
![](https://storage.googleapis.com/zenn-user-upload/a9268fc41aef-20260113.png)

pull request 作成して、reviewer を GitHub Copilot に設定しましょう。
![](https://storage.googleapis.com/zenn-user-upload/044b0cf7a7a0-20260113.png)

ちゃんとレビューもして、コメントも残してくれていますね。
![](https://storage.googleapis.com/zenn-user-upload/7b68a209a80d-20260113.png)

では、Marge してローカルで動作を確認してみましょう。
![](https://storage.googleapis.com/zenn-user-upload/2ac81f10595c-20260113.png)

これで一連の GitHub Copilot の Agent を活用した修正作業が完了です。

すばらしすぎますね。

# 最後に

GitHub Copilot は最近ますます進化しており、全ての開発者にとって強力なツールとなっています。
特に Agent モードや MCP、カスタム命令などの高度な機能を活用することで、開発効率が大幅に向上します。

もしかしたら、我々が手を動かしてコードを書く時代ももうそろそろ終わりで、以下にうまく指示を出せるかがポイントになってくるかもしれませんね。

GitHub Copilot の進化は目覚ましく、今後も新しい機能や改善が期待されているので、是非積極的に活用してみてください。

それでは 🖐️

# 参考文献

https://github.com/features/copilot?locale=ja

https://github.blog/jp/2025-10-29-welcome-home-agents/

https://www.udemy.com/course/github-copilot-next/
