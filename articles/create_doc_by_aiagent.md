---
title: 'AI Agent に資料を作ってもらう会【LangGraph/LangSmith】'
emoji: '📝'
type: 'tech' # tech: 技術記事 / idea: アイデア
topics: ['AI', 'LangGraph', 'LangSmith', 'AzureOpenAI', 'AIAgent']
published: false
publication_name: microsoft
---

# 目次

- はじめに
- 主要コンポーネントの説明
  - LangChain とは
  - LangGraph とは
  - LangSmith とは
  - Tavily とは
  - Marp とは
- セットアップ
  - 必要な環境変数（.env）
  - 依存パッケージのインストール
- 実装の全体像（Python スクリプト構成）
- コード解説（ブロック別 / フロー順）
  - 0. Tavily 情報収集
  - 1. アウトライン生成
  - 2. 目次生成（JSON）
  - 3. スライド本文生成（Marp）
  - 4. 自動評価とリトライ分岐
  - 5. 保存 & marp-cli レンダリング
- 運用のポイント（精度・再現性・可観測性）
- トラブルシュート（よくあるエラー）
- まとめ
- 付録：フルコード

# はじめに

https://youtu.be/dBI7LhHfto0

今回は AI Agent に AI 関連の最新情報を取得し、スライド形式でドキュメントにまとめてもらう PoC を作成してみました。
実装は Python のみで、LangChain・LangGraph・LangSmith を活用し簡単に実装しています。

全体のアーキテクチャフローは以下のような感じです。

![](https://storage.googleapis.com/zenn-user-upload/22e4ae811c7b-20250906.png)

フローをざっくり説明すると以下のようになります。 0) collect_info -> Tavily 情報収集

1. make_outline -> アウトライン生成
2. make_toc -> 目次生成（JSON）
3. write_slides -> スライド本文生成（Marp）
4. evaluate_slides -> 自動評価とリトライ分岐
5. save_and_render -> 保存 & marp-cli レンダリング

# 実装
