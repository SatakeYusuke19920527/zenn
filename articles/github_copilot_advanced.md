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

:::message
本記事は GitHub Copilot の基礎ではなく、応用的な内容となります。
基本的な内容から理解されたい場合は以下の記事をご参照ください。
GitHub Copilot を完全に使いこなす会
https://zenn.dev/microsoft/articles/github_copilot_intro
:::

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

### Agent HQ とは？

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

### 各モデルの使い所

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

### プレミアムリクエストについて

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

GitHub Copilot の Agent モードを使いこなすことが出来れば、
