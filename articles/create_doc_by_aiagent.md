---
title: 'AI Agent にAI最新情報まとめ資料を作ってもらう会【LangGraph/LangSmith】'
emoji: '📝'
type: 'tech' # tech: 技術記事 / idea: アイデア
topics: ['AI', 'LangGraph', 'LangSmith', 'AzureOpenAI', 'AIAgent']
published: false
publication_name: microsoft
---

# はじめに

https://youtu.be/dBI7LhHfto0

今回は AI Agent に AI 関連の最新情報を取得し、スライド形式でドキュメントにまとめてもらう PoC を作成してみました。
実装は Python のみで、LangChain・LangGraph・LangSmith を活用し簡単に実装しています。

全体のアーキテクチャフローは以下のような感じです。

![](https://storage.googleapis.com/zenn-user-upload/22e4ae811c7b-20250906.png)

フローをざっくり説明すると以下のようになります。

0. collect_info -> Tavily 情報収集
1. make_outline -> アウトライン生成
2. make_toc -> 目次生成（JSON）
3. write_slides -> スライド本文生成（Marp）
4. evaluate_slides -> 自動評価とリトライ分岐
5. save_and_render -> 保存 & marp-cli レンダリング

LangGraph で AI Agent を実装しつつ、LangSmith でエージェントの動きの可視化を行いながら AI Agent の動きを学べる内容になります。

# 主要コンポーネントの説明

## LangChain とは

![](https://storage.googleapis.com/zenn-user-upload/7f13d4a0fd14-20250906.png)

**LangChain** は、**大規模言語モデル（LLM）をアプリケーションに組み込むためのフレームワーク**で、LLM を単なる文章生成にとどめず、外部のデータベースや API、ユーザーインターフェースとつなげて、 **実際に役立つアプリケーション（AI Agent や RAG システム）**を作ることを目的としています。

- **開発元**: 米国の LangChain, Inc.
- **利用分野**: AI スタートアップから大企業まで幅広く採用
- **特徴**: モデル接続、ツール統合、ワークフロー構築、評価基盤まで一気通貫でカバー

### 主な機能

#### 1. LLM ラッパー

- OpenAI, Azure OpenAI, Anthropic, Google Gemini など多数のモデルに共通 API で接続。

#### 2. Tools

- 検索エンジン、DB クエリ、Python 実行環境などを「ツール」として呼び出し可能。

#### 3. Agents

- LLM がタスクを理解し、必要に応じてツールを利用して**自律的に処理**。
- 例: 「ユーザーの質問 → Web 検索 → 要約 → 回答生成」

#### 4. Chains

- プロンプト、LLM、ツール、出力処理を組み合わせた**処理パイプライン**。

#### 5. Memory

- 会話履歴や状態を保持して、**文脈を理解した継続的な対話**を実現。

### LangChain で作れるものの例

- **RAG（検索拡張生成）システム**  
  → 社内ナレッジベースを検索して正確な回答を返す QA アプリ

- **AI エージェント**  
  → タスクを理解し、API 呼び出しや資料自動生成を行うアシスタント

- **マルチエージェントシステム**  
  → 役割分担されたエージェント同士が協調して問題解決

- **業務自動化 Bot**  
  → メール下書き、スケジュール管理、顧客対応などを半自動化

LangChain は「LLM を使う」から「LLM でアプリを作る」へ進化させるためのフレームワークです。  
特に **AI エージェント** や **RAG システム** の実装基盤として世界中で注目を集めています。

## LangGraph とは

![](https://storage.googleapis.com/zenn-user-upload/22e4ae811c7b-20250906.png)

**LangGraph** は、**LangChain をベースにした状態遷移型のワークフロー構築フレームワーク**です。  
AI エージェントや複雑な対話システムを「グラフ（ノードとエッジ）」として設計できるのが特徴です。

## 主な特徴

### 1. 状態遷移型ワークフロー

- 各処理（ノード）を明示的に定義し、処理の流れ（エッジ）をつなげる。
- `StateGraph` を使って、開始（START）から終了（END）までの流れを制御。

### 2. マルチステップの制御

- 単純な LLM 呼び出しだけでなく、**条件分岐**や**ループ**を実装可能。
- 「失敗したらリトライ」「評価スコアが低ければ別ルートへ」などを設計できる。

### 3. LangChain との親和性

- LangChain の**エージェント**や**ツール呼び出し**をそのまま組み込み可能。
- LLM ＋外部ツール＋ RAG の流れを**グラフで可視化**できる。

## 典型的なユースケース

- **AI エージェントのオーケストレーション**
  - 複数のステップを経てタスクを解決するエージェントを構築。
- **マルチエージェントシステム**
  - 役割ごとに異なるエージェントを配置し、協調させる。
- **RAG パイプライン**
  - 検索 → 評価 → 回答生成 の流れをグラフで表現。
- **条件付きタスク実行**
  - 評価スコアや外部シグナルに応じてルートを切り替える。

実際にコードを見た方がイメージがつきやすいと思うので、以下に簡単なサンプルコードを示します。

## 簡単なサンプルコード

```python
from langgraph.graph import StateGraph, START, END

# StateGraphを定義
graph = StateGraph(dict)

# ノードを定義
def step1(state):
    print("Step1")
    return {"x": 1}

def step2(state):
    print("Step2")
    return {"y": state["x"] + 1}

# ノードを追加
graph.add_node("step1", step1)
graph.add_node("step2", step2)

# エッジを追加
graph.add_edge(START, "step1")
graph.add_edge("step1", "step2")
graph.add_edge("step2", END)

# 実行
app = graph.compile()
result = app.invoke({})
print(result)
```

サンプルコード内で各処理（ノード）を明示的に定義して、処理の流れ（エッジ）を繋げていますね。
このように、LangGraph は、**「AI エージェントを状態遷移グラフで表現する仕組み」**です。

## LangSmith とは

![](https://storage.googleapis.com/zenn-user-upload/5fab28b94335-20250906.png)

**LangSmith** は、LangChain 公式が提供する **LLM アプリケーションの評価・デバッグ・監視プラットフォーム** です。  
AI エージェントや RAG システムの開発において、**「どう動いたか」「どの部分が悪いか」**を可視化するために使われます。

## 主な特徴

### 1. トレーシング（Tracing）

- LLM 呼び出し・プロンプト・ツール実行を**時系列で記録**。
- 実際の入力と出力を**可視化してデバッグ**可能。

### 2. 評価（Evaluation）

- **自動評価（Auto Eval）**: LLM や定義済み基準を使った品質評価。
- **人手評価（Human Eval）**: アノテータがラベルを付与し、回答の良し悪しをチェック。
- スコア化・フィードバックループにより改善を継続。

### 3. データセット管理

- テストケースを**データセット化**して保存。
- 回帰テストのように**同じケースで再評価**できる。

### 4. LangChain/LangGraph との統合

- LangChain アプリで `@traceable` デコレータを付けるだけで**自動的に LangSmith に記録**。
- LangGraph の実行フローも可視化される。

---

## ユースケース

- **プロンプト改善**  
  どのプロンプトが不適切か、どのツール呼び出しが失敗したかを分析。

- **RAG パイプラインの評価**  
  「検索 → 回答生成」のプロセスが正しく動いているか確認。

- **AI エージェントの品質保証**  
  複雑なエージェントワークフローを**トレース & スコアリング**して信頼性を担保。

---

## サンプルコード（簡略）

```python
from langsmith import traceable

@traceable
def my_chain(input: str) -> str:
    # LangSmithでトレースされる
    return f"Hello, {input}"

print(my_chain("Yusuke"))
```

上記のように @traceable を付けると、LangSmith ダッシュボードで入力と出力が追跡可能になります。
LangChain アプリを本番運用できる品質に引き上げる役割を担います。
Dev → Test → Prod のライフサイクル全体をカバーするので、LLM アプリの品質管理プラットフォームです。

**まとめると以下の理解で OK です。**

- LangChain = 開発（アプリを作る）
- LangGraph = 設計（フローを組む）
- LangSmith = 運用（LLMOps で品質管理）

## Tavily とは

![](https://storage.googleapis.com/zenn-user-upload/740cc2c00d8e-20250906.png)

**Tavily** は、**AI 向けの検索・情報収集 API** を提供するサービスです。  
従来の検索エンジンに比べて、**LLM（大規模言語モデル）が扱いやすい形式で情報を返す**ことを目的としています。

- **用途**: AI エージェントや RAG（検索拡張生成）の「情報収集部分」に利用
- **強み**: JSON 形式でのレスポンス、要約付き検索結果、複数クエリ処理、鮮度制御（直近の情報だけを取得）

## 主な特徴

### 1. 高精度な Web 検索

- 通常の検索エンジンに比べ、ノイズを減らし**短文サマリ＋リンク付き**で返す。
- LLM に直接渡しやすい形式で情報を提供。

### 2. 構造化レスポンス（JSON）

- レスポンスには「title」「url」「content（サマリ）」が含まれる。
- そのまま LangChain などのツールに組み込める。

### 3. クエリ制御

- `time_range` パラメータで「day / week / month / year」など、**情報の鮮度を指定可能**。
- 公式ドメイン（例: microsoft.com, openai.com）のみに限定して検索できる。

### 4. AI エージェントとの相性

- **LangChain / LangGraph** のツールとして使いやすい。
- 最新情報を取り込みながら、RAG やスライド自動生成に活用可能。

以下に Azure の 2025 年に出た最新情報を取得するサンプルコードを示します。

## サンプルコード（Python）

```python
import requests

endpoint = "https://api.tavily.com/search"
payload = {
    "api_key": "YOUR_TAVILY_API_KEY",
    "query": "Azure OpenAI September 2025 updates",
    "search_depth": "advanced",
    "include_answer": True,
    "max_results": 5,
    "time_range": "month"
}

res = requests.post(endpoint, json=payload, timeout=60)
print(res.json())
```

すごく簡単に WEB 検索が取得出来るので、AI エージェントの情報収集に最適です。

ユースケースとしては以下になるかと思います。

- AI スライド自動生成
  - 最新情報を集めて要約 → LLM でスライド化
- ニュースサマリボット
  - 直近の出来事だけをまとめて配信
- RAG システム強化
  - ナレッジベースだけでなく「最新の Web 情報」も回答に統合

Tavily は、**AI が外部の最新情報を取り込むための「検索 API」**です。
LangChain や LangGraph と組み合わせることで、最新情報を扱えるエージェントを簡単に構築できます。
今回の実装では、AI 関連の最新情報を取得するために活用しています。

## Marp とは

**Marp (Markdown Presentation Ecosystem)** は、**Markdown でスライド資料を作成できるオープンソースのフレームワーク** です。

以下のような利用用途になります。

- **Markdown 記法でプレゼン資料を作成可能**
- **テーマやスタイルを指定してスライド化**
- **PDF / PNG / HTML 形式で出力可能**

## 主な特徴

### 1. Markdown ベース

- `# タイトル` → スライドの見出し
- `---` → スライド区切り
- 普段の Markdown ライティング感覚でスライドを作成できる。

### 2. 多様な出力形式

- **PDF**: 印刷や配布に便利
- **PNG**: 画像として利用可能
- **HTML**: Web 上に公開・共有できる

### 3. テーマカスタマイズ

- `default` / `gaia` / `uncover` など公式テーマを選択可能
- CSS や独自テーマを作成してブランドデザインにも対応

### 4. CLI ツール

- `marp-cli` を使うと Markdown ファイルからワンコマンドでスライド生成可能

```bash
npx @marp-team/marp-cli slides.md --pdf
```

Marp は最近私が愛用しているのですが、サクッとスライド資料を作成できるので非常に便利です。
今回は AI 最新情報の取得後のスライド化に活用しています。

# 実装開始 🚀

## 必要なファイル

任意のフォルダを作成して、以下のファイルを用意します。

- requirements.txt
- .env
- langgraph.json
- marp_agent.py
- slides/ <- ディレクトリを作成。このディレクトリに pdf が出力されます。

それぞれ実装していきます。

#### requirements.txt

requirements.txt には以下を記載します。

```plaintext
# --- Core runtime ---
requests>=2.31.0
python-dotenv>=1.0.0

# --- LangChain / LangGraph stack ---
langchain-core>=0.3.0,<0.4.0
langchain-openai>=0.2.0,<0.3.0
langgraph>=0.2.0,<0.4.0
langsmith>=0.1.0,<0.2.0

# --- Compatibility helpers ---
typing_extensions>=4.8.0
backports.zoneinfo; python_version<"3.9"

```

#### .env

.env には以下を記載します。

```plaintext
# LangSmith
LANGCHAIN_TRACING_V2=true
LANGCHAIN_API_KEY=lsv2_pt_xxxxxxxxxxxxxxxxxxxxxxx_xxxxxxxxxx
LANGCHAIN_PROJECT=marp_agent
LANGCHAIN_ENDPOINT=https://api.smith.langchain.com

# Azure OpenAI
AZURE_OPENAI_ENDPOINT=https://xxxxxxxxxxxxxxxx.azure.com/
AZURE_OPENAI_API_KEY=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
AZURE_OPENAI_API_VERSION=2024-12-01-preview
AZURE_OPENAI_DEPLOYMENT=gpt-4o

# Tavily
TAVILY_API_KEY=tvly-dev-xxxxxxxxxxxxxxxxxxxxxxx

# Marp
SLIDE_FORMAT=pdf
```

LANGCHAIN_API_KEY は LangSmith の API キーを指定します。
LangSmith を起動して、[Settings] - [API Keys] から取得できます。
![](https://storage.googleapis.com/zenn-user-upload/1c0ae962dd21-20250906.png)

Tavily_API_KEY は Tavily の API キーを指定します。
Tavily にサインアップして、Top Page に記載されています。
![](https://storage.googleapis.com/zenn-user-upload/0e21552e5066-20250906.png)

Azure OpenAI Service の各種 Key は Azure AI Foundry より記載してください。

#### langgraph.json

LangSmith を実行する為に準備必要なパーツになります。

```json
{
  "dependencies": ["langgraph==0.5.2"],
  "graphs": {
    "poc-aiagent": {
      "path": "./marp_agent.py:graph",
      "description": "AI Agent create pdf about AI News"
    }
  },
  "env": ".env"
}
```

#### marp_agent.py

では、python にて実装していきましょう。

````python
# LangGraph × LangSmith × Azure OpenAI × Tavily × Marp
# フロー:
# 0) Tavilyで情報収集 -> 1) アウトライン生成 -> 2) 目次生成 -> 3) スライド(Marp)本文生成 -> 4) 評価(>=8でOK/それ以外は2へループ) -> 5) スライドMarkdown保存(+ marp-cliでpdf/png/html出力)

import os
import re
import json
import shutil
import subprocess
from typing import Any, Union
import requests  # type: ignore
from typing import TypedDict, List, Dict, Optional
from datetime import datetime
from pathlib import Path
from dotenv import load_dotenv  # type: ignore
from datetime import timedelta
from langsmith import traceable  # type: ignore
from langgraph.graph import StateGraph, START, END  # type: ignore
from langchain_openai import AzureChatOpenAI  # type: ignore
from langchain_core.runnables import RunnableConfig  # type: ignore
from zoneinfo import ZoneInfo
from langchain_core.tools import tool # type: ignore

# -------------------
# 環境変数読み込み
# -------------------
load_dotenv()

def _env(key: str, default: Optional[str] = None) -> str:
    v = os.getenv(key, default)
    if v is None:
        raise RuntimeError(f"Missing environment variable: {key}")
    return v

# LangSmith（ON推奨）
os.environ.setdefault("LANGCHAIN_TRACING_V2", "true")
os.environ.setdefault("LANGCHAIN_ENDPOINT", "https://api.smith.langchain.com")

# Azure OpenAI
AZURE_OPENAI_ENDPOINT    = _env("AZURE_OPENAI_ENDPOINT")
AZURE_OPENAI_API_KEY     = _env("AZURE_OPENAI_API_KEY")
AZURE_OPENAI_API_VERSION = _env("AZURE_OPENAI_API_VERSION", "2024-06-01")
AZURE_OPENAI_DEPLOYMENT  = _env("AZURE_OPENAI_DEPLOYMENT")

# Tavily
TAVILY_API_KEY = _env("TAVILY_API_KEY")

# Marp 出力（pdf / png / html）。空なら .md のみ
SLIDE_FORMAT = os.getenv("SLIDE_FORMAT", "").lower().strip()
MARP_THEME   = os.getenv("MARP_THEME", "default")  # default, gaia, uncover 等
MARP_PAGINATE = os.getenv("MARP_PAGINATE", "true") # "true"/"false"

# -------------------
# LLM クライアント
# -------------------
llm = AzureChatOpenAI(
    azure_endpoint=AZURE_OPENAI_ENDPOINT,
    api_key=AZURE_OPENAI_API_KEY,
    api_version=AZURE_OPENAI_API_VERSION,
    azure_deployment=AZURE_OPENAI_DEPLOYMENT,
    temperature=0.2,
)

# -------------------
# ユーティリティ
# -------------------
def _log(state: dict, msg: str) -> List[str]:
    return (state.get("log") or []) + [msg]

def _strip_bullets(lines: List[str]) -> List[str]:
    out = []
    for line in lines:
        t = line.strip()
        if not t:
            continue
        t = t.lstrip("・-•* \t")
        out.append(t)
    return out

def _slugify_en(text: str, max_len: int = 80) -> str:
    text = (text or "").lower()
    text = re.sub(r"[^a-z0-9]+", "-", text)
    text = re.sub(r"-+", "-", text).strip("-")
    return text[:max_len] or "slides"

def _find_json(text: str) -> Optional[str]:
    t = text.strip()
    t = re.sub(r"^```(?:json)?\s*", "", t)
    t = re.sub(r"\s*```$", "", t)
    m = re.search(r"\{.*\}\s*$", t, flags=re.DOTALL)
    if m:
        return m.group(0)
    return None

def _ensure_marp_header(md: str, title: str) -> str:
    header = (
        "---\n"
        "marp: true\n"
        f"paginate: {MARP_PAGINATE}\n"
        f"theme: {MARP_THEME}\n"
        f"title: {title}\n"
        "---\n\n"
    )
    # 既存のfront-matterは剥がして先頭に再付与（安全策）
    body = re.sub(r"^---[\s\S]*?---\s*", "", md.strip(), count=1, flags=re.DOTALL)
    return header + (body + ("\n" if not body.endswith("\n") else ""))

# コードブロックを壊さずに H2 の前へ区切りを入れる
def _insert_separators(md: str) -> str:
    """
    コードブロックを壊さず、H2(## )の直前に1つだけ '---' を入れる。
    """
    out = []
    in_code = False
    fence = None
    prev = ""

    def need_sep(prev_line: str) -> bool:
        pl = prev_line.strip()
        # 直前が既に区切りなら不要
        return pl != "---"

    for line in md.splitlines():
        if line.startswith("```") or line.startswith("~~~"):
            if not in_code:
                in_code, fence = True, line[:3]
            else:
                if fence and line.startswith(fence):
                    in_code, fence = False, None
            out.append(line)
            prev = line
            continue

        if not in_code and line.startswith("## "):
            if need_sep(prev):
                out.append("---")
            out.append(line)
        else:
            out.append(line)
        prev = line

    return "\n".join(out).strip() + "\n"

def _dedupe_separators(md: str) -> str:
    """
    連続する区切り（---, 空行, --- ...）を1個に圧縮。
    """
    # --- の連続や、--- の間の空行を潰す
    md = re.sub(r"(?:\n*\s*---\s*\n+){2,}", "\n---\n", md, flags=re.MULTILINE)
    # 先頭の余分な --- を1個に
    md = re.sub(r"^(?:\s*---\s*\n)+", "---\n", md)
    return md

def _strip_whole_code_fence(md: str) -> str:
    t = md.strip()
    if t.startswith("```"):
        t = re.sub(r"^```[a-zA-Z0-9_-]*\s*\n?", "", t, flags=re.DOTALL)
        t = re.sub(r"\n?```$", "", t.strip(), flags=re.DOTALL)
    return t

def _clean_title(raw: str) -> str:
    """
    LLMが『以下のようなタイトル…』など前置きや引用記号を混ぜても
    1行の素のタイトルだけにする。
    """
    t = (raw or "").strip()
    # 1行目だけ採用
    t = t.splitlines()[0]
    # 日本語引用や英語引用を除去
    t = t.strip("「」『』\"' 　:：")
    # 典型的な前置きを除去
    t = re.sub(r"^(以下のようなタイトル.*|title:?|suggested:?|案:?)[\s：:]*", "", t, flags=re.IGNORECASE)
    return t or "AI最新情報"

# JSTの現在日時ツール
JST = ZoneInfo("Asia/Tokyo")

def now_jst():
    return datetime.now(JST)

def today_iso(fmt: str = "%Y-%m-%d") -> str:
    return now_jst().strftime(fmt)

def month_ja() -> str:
    dt = now_jst()
    # Windowsでも動くように %-m を使わず、0埋めもしない
    return f"{dt.year}年{dt.month}月"

def month_en() -> str:
    months = ["January","February","March","April","May","June",
              "July","August","September","October","November","December"]
    dt = now_jst()
    return f"{months[dt.month-1]} {dt.year}"

@tool("get_today_jst", return_direct=True)
def get_today_jst(fmt: str = "%Y-%m-%d") -> str:
    """JSTで今日の日付を返す（例: %Y-%m-%d, %Y年%m月 など）"""
    return datetime.now(JST).strftime(fmt)

def _clean_title(raw: str) -> str:
    t = (raw or "").strip().splitlines()[0]
    t = t.strip("「」『』\"' 　:：")
    t = re.sub(r"^(以下のようなタイトル.*|title:?|suggested:?|案:?)[\s：:]*", "", t, flags=re.IGNORECASE)
    return t or "[本日の日付] AI最新情報まとめ"

def _strip_whole_code_fence(md: str) -> str:
    t = md.strip()
    if t.startswith("```"):
        t = re.sub(r"^```[a-zA-Z0-9_-]*\s*\n?", "", t, flags=re.DOTALL)
        t = re.sub(r"\n?```$", "", t.strip(), flags=re.DOTALL)
    return t

def _remove_presenter_lines(md: str) -> str:
    """タイトルスライド（先頭～最初の'---'まで）から発表者行を除去"""
    parts = md.split("\n---\n", 1)
    head = parts[0]
    head = re.sub(r"^\s*(発表者|Presenter|Speaker)\s*[:：].*$", "", head, flags=re.MULTILINE)
    head = re.sub(r"\n{3,}", "\n\n", head).strip() + "\n"
    return head + ("\n---\n" + parts[1] if len(parts) == 2 else "")



# -------------------
# Tavily 検索
# -------------------
def tavily_search(
    query: str,
    max_results: int = 8,
    include_domains: Optional[List[str]] = None,
    time_range: str = "month",   # "day" | "week" | "month" | "year"
) -> Dict:
    endpoint = "https://api.tavily.com/search"
    payload: Dict[str, Any] = {
        "api_key": TAVILY_API_KEY,
        "query": query,
        "search_depth": "advanced",
        "include_answer": True,
        "max_results": max_results,
        "time_range": time_range,       # ★ 直近の情報に寄せる
    }
    if include_domains:
        payload["include_domains"] = include_domains  # ★ 公式ドメインに限定
    r = requests.post(endpoint, json=payload, timeout=60)
    r.raise_for_status()
    return r.json()

def tavily_collect_context(
    queries: List[Union[str, Dict[str, Any]]],
    max_per_query: int = 6,
    default_time_range: str = "month",
) -> Dict[str, List[Dict[str, str]]]:
    """
    queries は以下の2形式をサポート:
      - "plain text"
      - {"q": "...", "include_domains": ["example.com", ...], "time_range": "week"}
    """
    seen = set()
    out: Dict[str, List[Dict[str, str]]] = {}
    for q in queries:
        if isinstance(q, dict):
            qtext = q.get("q", "")
            inc = q.get("include_domains")
            tr  = q.get("time_range", default_time_range)
        else:
            qtext = q
            inc   = None
            tr    = default_time_range

        if not qtext:
            continue

        data = tavily_search(qtext, max_per_query, include_domains=inc, time_range=tr)
        items = []
        for r in data.get("results", []):
            url = r.get("url")
            if not url or url in seen:
                continue
            seen.add(url)
            items.append({
                "title": (r.get("title") or "")[:160],
                "url": url,
                "content": ((r.get("content") or "").replace("\n", " "))[:600],
            })
        out[qtext] = items
    return out

def context_to_bullets(ctx: Dict[str, List[Dict[str, str]]]) -> str:
    # LLMに渡しやすい、出典付きの短文箇条書きにする
    bullets = []
    for q, items in ctx.items():
        bullets.append(f"### Query: {q}")
        for it in items:
            title = it["title"]
            url = it["url"]
            content = it["content"].replace("\n", " ")
            bullets.append(f"- {title} — {content} [source]({url})")
        bullets.append("")  # 空行
    return "\n".join(bullets)

# -------------------
# State
# -------------------
class State(TypedDict):
    topic: str
    outline: List[str]
    toc: List[str]
    slide_md: str           # 生成された Marp スライド Markdown
    score: float
    subscores: Dict[str, float]
    reasons: Dict[str, str]
    suggestions: List[str]
    risk_flags: List[str]
    passed: bool
    feedback: str
    title: str
    slide_path: str
    attempts: int
    error: str
    log: List[str]
    # 追加
    context_md: str            # Tavilyからの要約（箇条書き）
    sources: Dict[str, List[Dict[str, str]]]  # 生ソース

# =======================
# Node 0: Tavily 情報収集（直近2ヶ月 & 公式ドメイン）
# =======================
@traceable(name="0_tavily_collect")
def collect_info(state: State) -> Dict:
    topic = state.get("topic") or "AI最新情報まとめ"

    # JST現在と 月英語表記（例: "September 2025"）
    def month_en_for(dt: datetime) -> str:
        months = ["January","February","March","April","May","June",
                  "July","August","September","October","November","December"]
        return f"{months[dt.month-1]} {dt.year}"

    now = now_jst()
    # 先月は「今月1日の前日」
    prev_month_dt = (now.replace(day=1) - timedelta(days=1))
    month_labels = [month_en_for(now), month_en_for(prev_month_dt)]  # 直近2ヶ月

    # ベンダ毎の公式ドメイン
    vendors = [
        (["azure.microsoft.com","news.microsoft.com","learn.microsoft.com"],
         ["Microsoft AI updates", "Azure OpenAI updates"]),
        (["openai.com"], ["OpenAI announcements","OpenAI updates"]),
        (["blog.google","ai.googleblog.com","research.google"], ["Google AI updates", "Gemini updates"]),
        (["aws.amazon.com"], ["AWS Bedrock updates","Amazon AI updates"]),
        (["ai.meta.com"], ["Meta AI updates", "Llama updates"]),
        (["anthropic.com"], ["Anthropic Claude updates","Claude announcements"]),
    ]

    # ★ 直近2ヶ月 × 各社のパターンで検索クエリを構成
    queries: List[Dict[str, Any]] = []
    for m in month_labels:
        for domains, patterns in vendors:
            for p in patterns:
                queries.append({"q": f"{p} {m}", "include_domains": domains, "time_range": "month"})

    try:
        sources = tavily_collect_context(queries, max_per_query=6, default_time_range="month")
        context_md = context_to_bullets(sources)
        return {
            "sources": sources,
            "context_md": context_md,
            "log": _log(state, f"[tavily] months={month_labels} queries={len(queries)}")
        }
    except Exception as e:
        return {"error": f"tavily_error: {e}", "log": _log(state, f"[tavily] EXCEPTION {e}")}
# -------------------
# Node 1: アウトライン
# -------------------
@traceable(name="1_outline")
def make_outline(state: State) -> Dict:
    topic = state.get("topic") or "AI最新情報"
    ctx = state.get("context_md") or ""
    prompt = [
        ("system", "あなたはMicrosoftのSolution Engineerです。これからMarpスライドでAI最新情報を発表します。"),
        ("user",
         "以下の“最新情報サマリ（出典付き）”を参考に、発表の重要ポイントを各社5個、"
         "短い箇条書きで。URLも含めてください。\n\n"
         f"[最新情報サマリ]\n{ctx}\n\n[トピック]\n{topic}")
    ]
    try:
        msg = llm.invoke(prompt)
        bullets = _strip_bullets(msg.content.splitlines())[:5] or [msg.content.strip()]
        return {"outline": bullets, "log": _log(state, f"[outline] {bullets}")}
    except Exception as e:
        return {"error": f"outline_error: {e}", "log": _log(state, f"[outline] EXCEPTION {e}")}

# -------------------
# Node 2: 目次
# -------------------
@traceable(name="2_make_toc")
def make_toc(state: State) -> Dict:
    outline = state.get("outline") or []
    outline = state.get("outline") or []
    prompt = [
        ("system", "あなたはMicrosoftのSolution Engineerです。Marpスライドの構成（目次）を作ります。"),
        ("user",
         "以下のアウトラインから、5〜8個の章立て（配列）を JSON の {\"toc\": [ ... ]} 形式で返してください。\n\n"
         "アウトライン:\n- " + "\n- ".join(outline))
    ]
    try:
        msg = llm.invoke(prompt)
        try:
            data = json.loads(_find_json(msg.content) or msg.content)
            toc = [s.strip() for s in data.get("toc", []) if s.strip()]
        except Exception:
            toc = _strip_bullets(msg.content.splitlines())
        toc = toc[:8] or ["はじめに", "背景", "実装手順", "評価と改善", "公開・運用", "まとめ"]
        return {"toc": toc, "error": "", "log": _log(state, f"[toc] {toc}")}
    except Exception as e:
        return {"error": f"toc_error: {e}", "log": _log(state, f"[toc] EXCEPTION {e}")}

# -------------------
# Node 3: スライド本文（Marp）
# -------------------
@traceable(name="3_write_slides")
def write_slides(state: State) -> Dict:
    ctx = state.get("context_md") or ""   # ★ ここが重要
    sources = state.get("sources") or {}

    # 参考リンク（最大控えめ）
    refs = []
    for q, items in sources.items():
        for it in items[:2]:
            refs.append(f"- **{it['title']}** — {it['url']}")
    refs_md = "\n".join(refs)

    # ▼ タイトルは当月で自動固定（LLMに任せない）
    ja_title = f"{month_ja()}_AI最新情報セッション"

    prompt = [
        ("system",
         "あなたはMicrosoftのSolution Engineerで、Marp形式のスライドを作ります。"
         "出力はコードブロックで囲まず、スライド区切り（---）は入れないでください。"
         "各スライドは必ず H2 見出し（## ）で開始。タイトルスライドに発表者名は書かないでください。"
         "重要: 以下の“最新情報サマリ”の範囲内の事実のみに基づいて作成し、サマリに無い情報は書かないでください。"),
        ("user",
         "最新情報サマリ（出典付き）:\n"
         f"{ctx}\n\n"
         "要件:\n"
         f"- タイトル（表紙の大見出し）: {ja_title}\n"
         "- 1ページ目は # 見出しと短いサブタイトルのみ（発表者名は書かない）\n"
         "- 2ページ目は Agenda（章立てを列挙）\n"
         "- 以降は各社ごとのAI最新情報を簡潔に。各項目にURLを含める\n"
         "- 各章は必ず H2（## ）で始める")
    ]

    try:
        msg = llm.invoke(prompt)
        slide_md = msg.content.strip()

        # 1) 全体コードフェンスを剥がす
        slide_md = _strip_whole_code_fence(slide_md)
        # 2) H2の直前にだけ --- を自動挿入
        slide_md = _insert_separators(slide_md)
        # 3) 連続する --- を1つに圧縮
        slide_md = _dedupe_separators(slide_md)
        # 4) front-matter を先頭に付与（title は“タイトルだけ”）
        slide_md = _ensure_marp_header(slide_md, _clean_title(ja_title))
        # 5) 表紙から“発表者: …”などを除去
        slide_md = _remove_presenter_lines(slide_md)

        return {"slide_md": slide_md, "title": ja_title,
                "error": "", "log": _log(state, f"[slides] generated ({len(slide_md)} chars)")}
    except Exception as e:
        return {"error": f"slides_error: {e}", "log": _log(state, f"[slides] EXCEPTION {e}")}

# -------------------
# Node 4: 評価（スライド前提）
# -------------------
MAX_ATTEMPTS = 3

@traceable(name="4_evaluate_slides")
def evaluate_slides(state: State) -> Dict:
    if state.get("error"):
        return {}
    slide_md = state.get("slide_md") or ""
    toc = state.get("toc") or []
    topic = state.get("topic") or ""
    eval_guide = (
        "評価観点と重み:\n"
        "- structure(0.20): スライドの流れ、章立て、1スライド1メッセージ\n"
        "- practicality(0.25): 実務に使える具体性（手順、コード例、注意点）\n"
        "- accuracy(0.25): 事実・API仕様・用語の正確さ\n"
        "- readability(0.15): 簡潔明瞭、視認性（箇条書きの粒度）\n"
        "- conciseness(0.15): 冗長性の少なさ\n"
        "合格: score >= 8.0\n"
    )

    prompt = [
        ("system",
         "あなたはMicrosoftのCloud Solution Architectです。以下のMarpスライドMarkdownを、"
         "指定の観点・重みで厳密に採点します。出力はJSONのみ。"),
        ("user",
         f"Topic: {topic}\nTOC: {json.dumps(toc, ensure_ascii=False)}\n\n"
         "Slides (Marp Markdown):\n<<<SLIDES\n" + slide_md + "\nSLIDES\n\n"
         "Evaluation Guide:\n<<<EVAL\n" + eval_guide + "\nEVAL\n\n"
         "Return strictly this JSON schema:\n"
         "{\n"
         "  \"score\": number,\n"
         "  \"subscores\": {\"structure\": number, \"practicality\": number, \"accuracy\": number, \"readability\": number, \"conciseness\": number},\n"
         "  \"reasons\": {\"structure\": string, \"practicality\": string, \"accuracy\": string, \"readability\": string, \"conciseness\": string},\n"
         "  \"suggestions\": [string],\n"
         "  \"risk_flags\": [string],\n"
         "  \"pass\": boolean,\n"
         "  \"feedback\": string\n"
         "}"
    )]
    try:
        msg = llm.invoke(prompt)
        raw = msg.content or ""
        js = _find_json(raw) or raw
        data = json.loads(js)

        score = float(data.get("score", 0.0))
        subscores = data.get("subscores") or {}
        reasons = data.get("reasons") or {}
        suggestions = data.get("suggestions") or []
        risk_flags = data.get("risk_flags") or []
        passed = bool(data.get("pass", score >= 8.0))
        feedback = str(data.get("feedback", "")).strip()
        attempts = (state.get("attempts") or 0) + 1

        return {
            "score": score,
            "subscores": subscores,
            "reasons": reasons,
            "suggestions": suggestions,
            "risk_flags": risk_flags,
            "passed": passed,
            "feedback": feedback,
            "attempts": attempts,
            "log": _log(state, f"[evaluate] score={score:.2f} pass={passed} attempts={attempts}")
        }
    except Exception as e:
        return {"error": f"eval_error: {e}", "log": _log(state, f"[evaluate] EXCEPTION {e}")}

def route_after_eval(state: State) -> str:
    if (state.get("attempts") or 0) >= MAX_ATTEMPTS:
        return "ok"
    return "ok" if state.get("passed") else "retry"

# -------------------
# Node 5: 保存 & Marpレンダリング
# -------------------
@traceable(name="5_save_and_render")
def save_and_render(state: State) -> Dict:
    if state.get("error"):
        return {}
    slide_md = state.get("slide_md") or ""
    title = state.get("title") or "AIスライド"

    # ファイル名
    slug_prompt = [
        ("system", "You create concise English slugs for filenames."),
        ("user",
         "Convert the following Japanese title into a short English filename base (<=6 words). "
         "Only letters and spaces; no punctuation or numbers.\n\n"
         f"Title: {title}")
    ]
    emsg = llm.invoke(slug_prompt)
    file_stem = _slugify_en(emsg.content.strip()) or _slugify_en(title)

    slides_dir = Path("slides")
    slides_dir.mkdir(parents=True, exist_ok=True)
    slide_md_path = slides_dir / f"{file_stem}_slides.md"
    slide_md_path.write_text(slide_md, encoding="utf-8")

    # marp-cli で出力
    marp = shutil.which("marp")
    out_path = str(slide_md_path)
    if marp and SLIDE_FORMAT in {"pdf", "png", "html"}:
        ext = SLIDE_FORMAT
        out_file = slides_dir / f"{file_stem}.{ext}"
        try:
            subprocess.run(
                [marp, str(slide_md_path), f"--{ext}", "-o", str(out_file)],
                check=True,
                stdout=subprocess.PIPE,
                stderr=subprocess.PIPE,
            )
            out_path = str(out_file)
            log_msg = f"[marp] generated {ext} -> {out_file}"
        except subprocess.CalledProcessError as e:
            log_msg = f"[marp] marp-cli failed: {e}"
    else:
        if not marp and SLIDE_FORMAT:
            log_msg = "[marp] marp-cli not found – skipped rendering (left .md)."
        else:
            log_msg = "[marp] rendering skipped (SLIDE_FORMAT not set)."

    return {"slide_path": out_path, "log": _log(state, log_msg)}

# -------------------
# グラフ構築
# -------------------
graph_builder = StateGraph(State)
graph_builder.add_node("collect_info", collect_info)
graph_builder.add_node("make_outline", make_outline)
graph_builder.add_node("make_toc", make_toc)
graph_builder.add_node("write_slides", write_slides)
graph_builder.add_node("evaluate_slides", evaluate_slides)
graph_builder.add_node("save_and_render", save_and_render)

graph_builder.add_edge(START, "collect_info")
graph_builder.add_edge("collect_info", "make_outline")
graph_builder.add_edge("make_outline", "make_toc")
graph_builder.add_edge("make_toc", "write_slides")
graph_builder.add_edge("write_slides", "evaluate_slides")

graph_builder.add_conditional_edges(
    "evaluate_slides",
    route_after_eval,
    {"retry": "make_toc", "ok": "save_and_render"},
)

graph_builder.add_edge("save_and_render", END)

graph = graph_builder.compile()

# -------------------
# 実行
# -------------------
if __name__ == "__main__":
    print("LangSmith Tracing:", os.getenv("LANGCHAIN_TRACING_V2"),
          "| Project:", os.getenv("LANGCHAIN_PROJECT"))
    init: State = {
        "topic": "LangGraph × LangSmith × Azure OpenAI × Tavilyで作る：最新AI動向スライド",
        "outline": [], "toc": [], "slide_md": "",
        "score": 0.0,
        "subscores": {}, "reasons": {},
        "suggestions": [], "risk_flags": [],
        "passed": False, "feedback": "",
        "title": "", "slide_path": "",
        "attempts": 0, "error": "", "log": [],
        "context_md": "", "sources": {}
    }
    config: RunnableConfig = {
        "run_name": "tavily-marp-agent",
        "tags": ["marp", "langgraph", "langsmith", "azure-openai", "tavily"],
        "metadata": {"env": "dev", "date": datetime.utcnow().isoformat()},
        "recursion_limit": 60,
    }
    out = graph.invoke(init, config=config)

    print("\n=== RESULT ===")
    if out.get("error"):
        print("ERROR:", out["error"])
    else:
        print("Title    :", out.get("title"))
        print("Slide    :", out.get("slide_path"))
        print("Score    :", out.get("score"))
        print("Passed   :", out.get("passed"))
    print("\n-- LOG --")
    for line in out.get("log", []):
        print(line)
````

コードの実装はこれで完了です。
では、実行してみましょう！

## 実行

以下のコードを実行すると、LangSmith のダッシュボードが開きます。

```bash
langgraph dev
```

![](https://storage.googleapis.com/zenn-user-upload/2b4949af5e6a-20250906.png)

ダッシュボードが開いたら、画面中央下部の Submit ボタンを教えて実行します。
![](https://storage.googleapis.com/zenn-user-upload/2bd8835b0164-20250906.png)

実行が完了すると、slides フォルダに PDF ファイルが生成されます。
![](https://storage.googleapis.com/zenn-user-upload/1d5739ee32e4-20250906.png)

いい感じに各社の Update がまとまっていますね！
これでスライド資料の完成です。

実装お疲れ様でした。

# まとめ

今回は LangGraph と LangSmith を使って、AI 最新情報を収集し、Marp でスライド資料を自動生成するエージェントを作成しました。
LangGraph の状態遷移と LangSmith のトレーシング機能を活用することで、複雑なワークフローも AIAgent がどのような動きをしているか見ながら実装することが出来るので、個人的にはかなりおすすめの機能でした。

是非みなさんも試してみてください。

それでは 🖐️

# 筆者情報

https://x.com/fe_js_engineer
https://www.linkedin.com/in/satyus/

# 参考リンク

https://www.langchain.com/
https://www.langchain.com/langgraph
https://www.langchain.com/langsmith
