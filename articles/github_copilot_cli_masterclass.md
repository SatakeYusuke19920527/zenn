---
title: 'GitHub Copilot CLI マスタークラス'
emoji: '🚀'
type: 'tech' # tech: 技術記事 / idea: アイデア
topics: ['githubcopilot', 'githubcopilotcli', 'microsoft', 'github', 'claude']
published: false
publication_name: microsoft
---

![](https://storage.googleapis.com/zenn-user-upload/fb12a4cdf7be-20260401.png)

# はじめに

この記事では、GitHub Copilot CLIをマスターしたい人に向けて、インストールからGitHub Copilot CLIを用いたアプリケーションの開発まで手を動かしながら完全に理解する会となります。

最近ではほぼ毎日GitHub CopilotのUpdateがあり、非常に進化が早い分野になります。
また、日々どんどんと便利機能が追加されている状況です。

特に2026年2月25日にGAされたGitHub Copilot CLIは非常に便利で、エンジニアだけではなく、ビジネス職の方にも是非使っていただきたいツールになります。

そこで今回は、GitHub Copilot CLIを使って開発をする上で、**基本的な使い方** から始め、 **開発ワークフロー** や **SKILLs**、**MCP連携**、最後にはアプリ開発まで体感していただけるマスタークラスを作ってみました。

本記事が読み終わる頃には、GitHub Copilot CLIを使いこなして、日々の開発に活用できるようになっているはずです。

それでは早速やっていきましょう🚀

# 目次

＃＃＃作成予定

# GitHub Copilot CLIをインストールして利用を開始する

では、早速GitHub Copilot CLIをインストールして利用を開始してみましょう。

Install する方法は以下のコマンドになります。

```bash
npm install -g @github/copilot
```

startするには以下のコマンドを実行します。

```bash
copilot
```

以下の画像ような感じで起動できます。
![](https://storage.googleapis.com/zenn-user-upload/a7cb28a43d28-20260321.png)

また、２回目以降のログイン時はバナーが出ないのですが、以下のコマンドであれば何度でもバナーを見ることが出来ます。

```bash
copilot --banner
```

ちなみに筆者は毎回バナーを表示させる系です。

ログインすると、このような画像の感じで入力待ちになります。
![](https://storage.googleapis.com/zenn-user-upload/52644685b0c4-20260321.png)

以下のコマンドを入力して、ログインしましょう。

```bash
login
```

GitHub.comへログインでOKです。
![](https://storage.googleapis.com/zenn-user-upload/a2e23e752c7b-20260321.png)

以下の表示がCLI上に表示されるので、Linkにアクセスして、コードを入力しましょう。

> Enter one-time code: XXXX-XXXX at https://github.com/login/device

これでログインは完了です。
まとめると、以下のような流れですね。
![](https://storage.googleapis.com/zenn-user-upload/8c3ddb3c9ff4-20260321.png)

Login後、Helloといったらこんな感じで返してくれます。
![](https://storage.googleapis.com/zenn-user-upload/8e9f0e261d9f-20260321.png)

# GitHub Copilot CLIの利用を終了する

次は終了の仕方を見てみましょう。
GitHub Copilot CLIは以下のコマンドで終了することができます。

```bash
/exit
```

GitHub Copilot CLIはセッションにて情報を管理しています。
exitしたけど、セッション前のやつでやり直したいね...。みたいな時は以下で起動するとセッションが復活して以前の会話内容からスタートしてくれます。

```bash
copilot --continue
```

特定のセッションを思い出したい時は以下です。

```bash
# Pick from a list of sessions interactively
copilot --resume

# Or resume a specific session by ID
copilot --resume abc123
```

セッション一覧を見ることができるコマンドもあります。

```bash
/session
```

![](https://storage.googleapis.com/zenn-user-upload/4512bfec9860-20260403.png)

そのような便利コマンドはGitHub Copilot CLIには多く搭載されています。
2026/04/01 時点でGitHub Copilot CLIで使えるコマンドは以下になります。

| 指示                   | その機能                                           | 使用時期                              |
| ---------------------- | -------------------------------------------------- | ------------------------------------- |
| `/init`                | リポジトリ用の Copilot 指示ファイルを初期化する    | 新しいリポジトリで最初に設定するとき  |
| `/agent`               | 利用可能なエージェントを確認・選択する             | 専用エージェントを使いたいとき        |
| `/skills`              | スキル機能を管理する                               | Copilot の拡張機能を調整したいとき    |
| `/mcp`                 | MCP サーバー設定を管理する                         | 外部ツール連携を設定したいとき        |
| `/plugin`              | プラグインやマーケットプレイスを管理する           | プラグインを追加・確認したいとき      |
| `/model`               | 使用する AI モデルを表示・切り替える               | モデルを変更したいとき                |
| `/delegate`            | セッションを GitHub 側に渡して PR 作成まで進める   | 実装をまとめて PR 化したいとき        |
| `/fleet`               | 並列サブエージェント実行を有効化する               | 複数タスクを並行で進めたいとき        |
| `/tasks`               | バックグラウンドタスクを確認・管理する             | 実行中の作業状況を見たいとき          |
| `/ide`                 | IDE ワークスペースに接続する                       | エディタ連携を有効にしたいとき        |
| `/diff`                | 現在のディレクトリでの変更差分を確認する           | 変更内容を見直したいとき              |
| `/pr`                  | 現在ブランチの Pull Request を操作する             | PR を作成・確認・更新したいとき       |
| `/review`              | コードレビューエージェントで変更を分析する         | 実装後にレビューしたいとき            |
| `/lsp`                 | Language Server の設定を管理する                   | 補完や解析環境を整えたいとき          |
| `/terminal-setup`      | ターミナルの複数行入力を設定する                   | 長いコマンドを入力しやすくしたいとき  |
| `/allow-all`           | すべての権限を許可する                             | 制限なくツールを使いたいとき          |
| `/add-dir`             | ファイルアクセスを許可するディレクトリを追加する   | 特定フォルダを触れるようにしたいとき  |
| `/list-dirs`           | 許可済みディレクトリ一覧を表示する                 | どこにアクセスできるか確認したいとき  |
| `/cwd`                 | 現在ディレクトリを表示・変更する                   | 作業場所を確認・移動したいとき        |
| `/reset-allowed-tools` | 許可済みツールをリセットする                       | 権限設定を初期化したいとき            |
| `/resume`              | 別セッションに切り替える                           | 以前の作業を再開したいとき            |
| `/rename`              | セッション名を変更する                             | セッションを整理したいとき            |
| `/context`             | コンテキスト使用量を表示する                       | トークン消費を確認したいとき          |
| `/usage`               | 利用統計を表示する                                 | 使用量を把握したいとき                |
| `/session`             | セッション情報を管理する                           | セッション全体を整理したいとき        |
| `/compact`             | 会話履歴を要約してコンテキストを節約する           | 長いやり取りの後に軽くしたいとき      |
| `/share`               | セッションや調査結果を Markdown や Gist に共有する | 内容を外部保存したいとき              |
| `/copy`                | 直前の応答をクリップボードにコピーする             | すぐ貼り付けたいとき                  |
| `/rewind`              | 直前のターンに戻して変更を巻き戻す                 | ひとつ前の操作を取り消したいとき      |
| `/help`                | 利用可能なすべてのコマンドを表示する               | コマンドを忘れたとき                  |
| `/changelog`           | CLI の更新履歴を表示する                           | 何が変わったか確認したいとき          |
| `/feedback`            | CLI へのフィードバックを送る                       | 改善要望を伝えたいとき                |
| `/theme`               | カラーテーマを確認・変更する                       | 見た目を変えたいとき                  |
| `/update`              | CLI を最新版に更新する                             | 最新版にしたいとき                    |
| `/version`             | バージョン情報を表示する                           | 現在の CLI バージョンを確認したいとき |
| `/experimental`        | 実験機能を確認・切り替える                         | 新機能を試したいとき                  |
| `/clear`               | 現在の会話を破棄して新しく始める                   | 話題を切り替えたいとき                |
| `/instructions`        | カスタム指示ファイルを確認・切り替える             | 指示設定を見直したいとき              |
| `/streamer-mode`       | 配信向け表示モードを切り替える                     | 画面共有や配信時に情報を隠したいとき  |
| `/exit` / `/quit`      | セッションを終了する                               | 作業を終えるとき                      |
| `/login`               | Copilot にログインする                             | 利用開始時や再ログイン時              |
| `/logout`              | Copilot からログアウトする                         | アカウントを切り替えたいとき          |
| `/new`                 | 新しい会話を始める                                 | 別テーマで作業したいとき              |
| `/plan`                | 実装前に計画を立てる                               | 複雑な機能を作る前                    |
| `/research`            | GitHub と Web を使って詳細調査する                 | 実装前に調査が必要なとき              |
| `/restart`             | セッションを維持したまま CLI を再起動する          | 動作をリフレッシュしたいとき          |
| `/undo`                | 直前の操作を巻き戻す                               | ひとつ前の変更を取り消したいとき      |
| `/user`                | GitHub ユーザー一覧を管理する                      | ユーザー設定を確認したいとき          |

忘れた時はいつでも `/help` コマンドで確認できますので、安心してください。

# GitHub Copilot CLIのモード

GitHub Copilot は4つのモードがあります。
![](https://storage.googleapis.com/zenn-user-upload/8f38829f3cab-20260321.png)

- Interactive > 通常の会話形式のモードです。
- Programmatic > プログラムモード（copilot -p "<your prompt>"）は、簡単な単発の質問に使用します。
- Plan > プランモード（/plan）は、コーディング前に詳細に計画を立てる必要がある場合に利用します。
- Autopilot > オートパイロットモードは、自動化されたタスク実行に使用します。

それぞれを用途に分けて使いこなしていけば非常に強力な共同開発者になってくれます。

# コンテキストを用いた会話

## @ の使い方

@を使うとファイルを参照することが出来ます。
利用パターンとしては以下になります。
| パターン | その機能 | 使用例 |
|---|---|---|
| `@ファイル名` | 単一のファイルを参照する | `/review @samples/lib/sample.py` |
| `@フォルダ名/` | ディレクトリ内のすべてのファイルを参照する | `/review @samples/lib/` |
| `@file1.py @file2.py` | 複数のファイルを参照する | `Compare @samples/lib/file1.py @samples/lib/file2.py` |

みたいな感じで使えます。

例えば、

```bash
please explain about @ディレクトリ名
```

とか入力すると、ディレクトリの説明を行なってくれます。
![](https://storage.googleapis.com/zenn-user-upload/c5f020a1afa6-20260321.png)

単一ファイルを参照することももちろん可能ですし、複数ファイルを比較することも出来ます。
例えば、

```bash
@ディレクトリ名1, @ディレクトリ名2, @ディレクトリ名3 について説明してください。
```

筆者のローカル環境で実行した結果はこんな感じです。
![](https://storage.googleapis.com/zenn-user-upload/970db0dadf39-20260321.png)

ディレクトリ配下もいい感じに説明してくれるのはいいですよね。
コマンドはたくさんありますが、基本的には日本語でも英語でも自然な形で指示すれば、理解してくれます。

コマンドも覚えられればBestですが、とりあえずは自然な形で指示してみて、慣れてきたらコマンドも覚えていくスタイルでいいですね。

# 開発ワークフローでのGitHub Copilot CLIの活用

この章では、開発者が一般的に使用する5つのワークフローについて説明します。
GitHub Copilotを使えば、各ワークフローご自身のニーズに合致し、現在のプロジェクトに最適なワークフローが簡単に実現できます。
![](https://storage.googleapis.com/zenn-user-upload/506416374aaf-20260321.png)

以下の流れでGitHub Copilot CLIの使い方を説明していきます。

- コードレビュー
- リファクタリング
- デバッグ
- テスト生成
- PR作成

## ワークフロー1: コードレビュー

### 基本的なコードレビュー

ファイルのレビューには **/review**を使って実施します。
以下のようにコマンドを実施します。

```bash
/review @ファイル名 for code quality
```

/review @ファイル名 for **気になる項目** のようにcopilotに支持すると、そこを重点的に確認してくれます。
こんな感じで実行して..
![](https://storage.googleapis.com/zenn-user-upload/f674be115a4f-20260403.png)

こんな感じで表示してくれます。
![](https://storage.googleapis.com/zenn-user-upload/165faaaef52c-20260403.png)

Bugはもちろん、Minorな警告も出してくれていますね。
Reviewerも非常に助かる機能です。

### クロスファイルプロジェクトレビュー

もちろん、複数ファイルやディレクトリの横断的なレビューも可能です。
以下のような形でプロジェクト全体をレビューすることが出来ます。

```bash
@ディレクトリ名 Review this entire project. Create a markdown checklist of issues found, categorized by severity
```

こんな感じで実行します。
サンプルでは、src/lib/ai というディレクトリについてレビューをしてもらい、checklistを出してもらいます。
![](https://storage.googleapis.com/zenn-user-upload/7b8b4600df2b-20260403.png)

こんな感じで重要度に分けて結果を返してくれます。また、最優先の事項も表示してくれてますね...(RBAC...(´Д` ))
![](https://storage.googleapis.com/zenn-user-upload/405e4ca5704d-20260403.png)

### インタラクティブなコードレビュー

前述にもありました通り、GitHub Copilot CLIはインタラクティブなコードレビューも提供してくれます。

先ほどのエラーハンドリング不足を対象にインタラクティブに解決したいと思います。

![](https://storage.googleapis.com/zenn-user-upload/d329273d3ee1-20260404.png)

こんな感じで指示すると、以下のような結果が得られました。
![](https://storage.googleapis.com/zenn-user-upload/17d33eb96b32-20260404.png)
![](https://storage.googleapis.com/zenn-user-upload/b0ce94bfdcad-20260404.png)
![](https://storage.googleapis.com/zenn-user-upload/343b91b7ae22-20260404.png)

修正を適用しますか？と聞いてくれるのは非常に便利ですね。

### レビューチェックリストテンプレート

GitHub Copilot CLI に、出力を特定の形式を指定することも出来ます。

```bash
/review @samples/sample-project/ and create a markdown checklist of issues found, categorized by:
- Critical (data loss risks, crashes)
- High (bugs, incorrect behavior)
- Medium (performance, maintainability)
- Low (style, minor improvements)
```

この形式でやってくれると、人間側としても見やすいですよね。

### Gitの変更を理解する（`/review` にとって重要）

この `/review` コマンドを使う前に、Gitにおける **2種類の変更** を理解しておく必要があります。

#### 変更の種類

| 変更の種類           | 意味                                                 | 確認方法            |
| -------------------- | ---------------------------------------------------- | ------------------- |
| 段階的な変更         | 次のコミット用にマークしたファイル（`git add` 済み） | `git diff --staged` |
| 非ステージングの変更 | 変更したが、まだ `git add` していないファイル        | `git diff`          |

みなさんご存知のgitのコマンドですが、念の為...

```bash
git status           # ステージ済み・未ステージの両方を表示
git add file.py      # ファイルをコミット対象としてステージする
git diff             # 未ステージの変更を表示
git diff --staged    # ステージ済みの変更を表示
```

### `/review` コマンドの使用

`/review` コマンドは、組み込みの **コードレビューエージェント** を起動します。  
このエージェントは、**ステージ済み** および **未ステージ** の変更を高感度で分析するように最適化されています。

自由形式のプロンプトを書く代わりに、**スラッシュコマンド** を使って専用の組み込みエージェントを起動してください。

```bash
/review
```

ステージ済み / 未ステージの変更に対してコードレビューを実行
実用的で具体的なフィードバックを返す
/review Check for security issues in authentication
認証まわりのセキュリティ観点に絞ってレビューを実行

コードレビューエージェントは、保留中の変更がある場合 に最も効果を発揮します。
より集中的なレビューを行いたい場合は、git add でファイルをステージしてから実行してください。

## ワークフロー2：リファクタリング

### シンプルなリファクタリング

コードをレビューしてもらった次はリファクタリングです。
リファクタリングも非常に簡単にGitHub Copilot CLIにお願いできます。

例えばリファクタリング必要そうな以下のコードがあったとします。

```typescript
// 生徒の成績データを処理するサンプル関数群（リファクタリング練習用）

export function proc(d: any[], t: string, m: string) {
  let r: any[] = [];
  for (let i = 0; i < d.length; i++) {
    if (d[i].type == t) {
      if (d[i].month == m) {
        let s = 0;
        for (let j = 0; j < d[i].scores.length; j++) {
          s = s + d[i].scores[j];
        }
        let a = s / d[i].scores.length;
        let g;
        if (a >= 90) {
          g = 'S';
        } else if (a >= 80) {
          g = 'A';
        } else if (a >= 70) {
          g = 'B';
        } else if (a >= 60) {
          g = 'C';
        } else {
          g = 'D';
        }
        r.push({
          name: d[i].name,
          avg: a,
          grade: g,
          scores: d[i].scores,
          total: s,
          count: d[i].scores.length,
          type: d[i].type,
          month: d[i].month,
          passed: a >= 60 ? true : false,
          message:
            a >= 60
              ? d[i].name + 'さんは合格です'
              : d[i].name + 'さんは不合格です',
        });
      }
    }
  }
  // ソート
  for (let i = 0; i < r.length; i++) {
    for (let j = 0; j < r.length - 1; j++) {
      if (r[j].avg < r[j + 1].avg) {
        let tmp = r[j];
        r[j] = r[j + 1];
        r[j + 1] = tmp;
      }
    }
  }
  for (let i = 0; i < r.length; i++) {
    r[i].rank = i + 1;
  }
  return r;
}

export function makeReport(d: any[], t: string, m: string) {
  let results = proc(d, t, m);
  let report = '';
  report = report + '=== 成績レポート ===\n';
  report = report + '種別: ' + t + '\n';
  report = report + '月: ' + m + '\n';
  report = report + '受験者数: ' + results.length + '\n';

  let totalAvg = 0;
  for (let i = 0; i < results.length; i++) {
    totalAvg = totalAvg + results[i].avg;
  }
  if (results.length > 0) {
    totalAvg = totalAvg / results.length;
  }
  report = report + '平均点: ' + totalAvg.toFixed(1) + '\n';
  report = report + '---\n';

  for (let i = 0; i < results.length; i++) {
    report =
      report +
      results[i].rank +
      '位: ' +
      results[i].name +
      ' - ' +
      results[i].avg.toFixed(1) +
      '点 (' +
      results[i].grade +
      ') ' +
      results[i].message +
      '\n';
  }

  let sCount = 0;
  let aCount = 0;
  let bCount = 0;
  let cCount = 0;
  let dCount = 0;
  for (let i = 0; i < results.length; i++) {
    if (results[i].grade == 'S') sCount++;
    if (results[i].grade == 'A') aCount++;
    if (results[i].grade == 'B') bCount++;
    if (results[i].grade == 'C') cCount++;
    if (results[i].grade == 'D') dCount++;
  }

  report = report + '---\n';
  report = report + 'S: ' + sCount + '人\n';
  report = report + 'A: ' + aCount + '人\n';
  report = report + 'B: ' + bCount + '人\n';
  report = report + 'C: ' + cCount + '人\n';
  report = report + 'D: ' + dCount + '人\n';
  report =
    report +
    '合格率: ' +
    (results.length > 0
      ? (
          (results.filter((x: any) => x.passed).length / results.length) *
          100
        ).toFixed(1)
      : '0') +
    '%\n';

  return report;
}
```

リファクタリングしがいがありますね。ツッコミどころが満載のコードです。

早速レビューしてみます。
![](https://storage.googleapis.com/zenn-user-upload/002bffca1bdc-20260403.png)

こんな感じでいい感じにご指摘を得られました。
緊急度で分けてくれるのいいですね。あと、これならすぐにリファクタリングに入れそうです。
![](https://storage.googleapis.com/zenn-user-upload/76a7e9d7a18c-20260403.png)

では、本題のリファクタリングをしていただきましょう。
![](https://storage.googleapis.com/zenn-user-upload/404314f3d565-20260403.png)

リファクタリングした一覧を出してくれました。
![](https://storage.googleapis.com/zenn-user-upload/776bf13f60a1-20260403.png)

```typescript
// 生徒の成績データを処理するユーティリティ

/** 成績グレード */
type Grade = 'S' | 'A' | 'B' | 'C' | 'D';

const GRADE_THRESHOLDS: { min: number; grade: Grade }[] = [
  { min: 90, grade: 'S' },
  { min: 80, grade: 'A' },
  { min: 70, grade: 'B' },
  { min: 60, grade: 'C' },
];
const DEFAULT_GRADE: Grade = 'D';
const PASSING_THRESHOLD = 60;

/** 生徒の成績入力データ */
export interface StudentScoreInput {
  name: string;
  type: string;
  month: string;
  scores: number[];
}

/** 評価済みの成績結果 */
export interface StudentResult {
  name: string;
  type: string;
  month: string;
  scores: number[];
  total: number;
  count: number;
  average: number;
  grade: Grade;
  passed: boolean;
  rank: number;
}

function calculateAverage(scores: number[]): number {
  const total = scores.reduce((sum, score) => sum + score, 0);
  return total / scores.length;
}

function determineGrade(average: number): Grade {
  return (
    GRADE_THRESHOLDS.find(({ min }) => average >= min)?.grade ?? DEFAULT_GRADE
  );
}

/**
 * 成績データをフィルタ・評価・ランキングして返す
 */
export function processStudentScores(
  data: StudentScoreInput[],
  type: string,
  month: string,
): StudentResult[] {
  const results: StudentResult[] = data
    .filter((student) => student.type === type && student.month === month)
    .filter((student) => student.scores.length > 0)
    .map((student) => {
      const total = student.scores.reduce((sum, s) => sum + s, 0);
      const average = calculateAverage(student.scores);
      const grade = determineGrade(average);
      return {
        name: student.name,
        type: student.type,
        month: student.month,
        scores: student.scores,
        total,
        count: student.scores.length,
        average,
        grade,
        passed: average >= PASSING_THRESHOLD,
        rank: 0,
      };
    });

  results.sort((a, b) => b.average - a.average);
  results.forEach((result, index) => {
    result.rank = index + 1;
  });

  return results;
}

function countByGrade(results: StudentResult[]): Record<Grade, number> {
  const counts: Record<Grade, number> = { S: 0, A: 0, B: 0, C: 0, D: 0 };
  for (const { grade } of results) {
    counts[grade]++;
  }
  return counts;
}

/**
 * 成績レポートのテキストを生成する
 */
export function makeReport(
  data: StudentScoreInput[],
  type: string,
  month: string,
): string {
  const results = processStudentScores(data, type, month);

  const overallAverage =
    results.length > 0
      ? results.reduce((sum, r) => sum + r.average, 0) / results.length
      : 0;

  const gradeCounts = countByGrade(results);
  const passCount = results.filter((r) => r.passed).length;
  const passRate = results.length > 0 ? (passCount / results.length) * 100 : 0;

  const header = [
    `=== 成績レポート ===`,
    `種別: ${type}`,
    `月: ${month}`,
    `受験者数: ${results.length}`,
    `平均点: ${overallAverage.toFixed(1)}`,
    `---`,
  ].join('\n');

  const ranking = results
    .map((r) => {
      const status = r.passed ? '合格' : '不合格';
      return `${r.rank}位: ${r.name} - ${r.average.toFixed(1)}点 (${r.grade}) ${r.name}さんは${status}です`;
    })
    .join('\n');

  const gradeLabels: Grade[] = ['S', 'A', 'B', 'C', 'D'];
  const distribution = gradeLabels
    .map((g) => `${g}: ${gradeCounts[g]}人`)
    .join('\n');

  return [
    header,
    ranking,
    `---`,
    distribution,
    `合格率: ${passRate.toFixed(1)}%`,
    '',
  ].join('\n');
}
```

コメントも入っていたり、TypeScriptの型もわかりやすく定義してくれていたり非常に読みやすいコードになりましたね。

もちろん、単一のファイルだけではなく、複数ファイルのリファクタリングも可能です。

```bash
/review @samples/sample1.py, @samples/sample2.py for code quality
```

と確認したあとに、

```bash
@samples/sample1.py, @samples/sample2.py をリファクタリングして
```

とすると、複数ファイルにまたがるリファクタリングも可能です。
もちろん、ディレクトリを指定して、プロジェクト全体のリファクタリングも可能です。

```bash
src/lib/ai/ の配下のファイルをリファクタリングして
```

### テストを用いた安全なリファクタリング

関連する2つの依頼を、複数ターンの対話としてつなげます。  
まずテストを生成し、その後でテストを安全網としてリファクタリングを行います。

```bash
@samples/sample.py Before refactoring, generate tests for current behavior
```

```bash
Now refactor the BookCollection class to use a context manager for file operations
```

まずテストを作成してからリファクタリングすることで、**既存の振る舞いが保たれていることを確認しながら** 安心して改善できます。

私は結構慎重派且つ、一発でリファクタリングを終わらせたい人なので、テスト作って確認してからリファクタリングを実施するようにしています。

## ワークフロー3：デバッグ

### シンプルなデバッグ

GitHub Copilot CLIを使うと、**コンテキストを理解したデバッグ** が可能になります。

以下のコードで試してみましょう。
3つのバグが含まれています。一読して見つけて見てください。

```typescript
// 生徒の成績データを処理するユーティリティ

/** 成績グレード */
type Grade = 'S' | 'A' | 'B' | 'C' | 'D';

const GRADE_THRESHOLDS: { min: number; grade: Grade }[] = [
  { min: 90, grade: 'S' },
  { min: 80, grade: 'A' },
  { min: 70, grade: 'B' },
  { min: 60, grade: 'C' },
];
const DEFAULT_GRADE: Grade = 'D';
const PASSING_THRESHOLD = 60;

/** 生徒の成績入力データ */
export interface StudentScoreInput {
  name: string;
  type: string;
  month: string;
  scores: number[];
}

/** 評価済みの成績結果 */
export interface StudentResult {
  name: string;
  type: string;
  month: string;
  scores: number[];
  total: number;
  count: number;
  average: number;
  grade: Grade;
  passed: boolean;
  rank: number;
}

function calculateAverage(scores: number[]): number {
  const total = scores.reduce((sum, score) => sum + score, 1);
  return total / scores.length;
}

function determineGrade(average: number): Grade {
  return (
    GRADE_THRESHOLDS.find(({ min }) => average >= min)?.grade ?? DEFAULT_GRADE
  );
}

/**
 * 成績データをフィルタ・評価・ランキングして返す
 */
export function processStudentScores(
  data: StudentScoreInput[],
  type: string,
  month: string,
): StudentResult[] {
  const results: StudentResult[] = data
    .filter((student) => student.type === type && student.month === month)
    .filter((student) => student.scores.length > 0)
    .map((student) => {
      const total = student.scores.reduce((sum, s) => sum + s, 0);
      const average = calculateAverage(student.scores);
      const grade = determineGrade(average);
      return {
        name: student.name,
        type: student.type,
        month: student.month,
        scores: student.scores,
        total,
        count: student.scores.length,
        average,
        grade,
        passed: average >= PASSING_THRESHOLD,
        rank: 0,
      };
    });

  results.sort((a, b) => a.average - b.average);
  results.forEach((result, index) => {
    result.rank = index + 1;
  });

  return results;
}

function countByGrade(results: StudentResult[]): Record<Grade, number> {
  const counts: Record<Grade, number> = { S: 0, A: 0, B: 0, C: 0, D: 0 };
  for (const { grade } of results) {
    counts[grade]++;
  }
  return counts;
}

/**
 * 成績レポートのテキストを生成する
 */
export function makeReport(
  data: StudentScoreInput[],
  type: string,
  month: string,
): string {
  const results = processStudentScores(data, type, month);

  const overallAverage =
    results.length > 0
      ? results.reduce((sum, r) => sum + r.average, 0) / results.length
      : 0;

  const gradeCounts = countByGrade(results);
  const passCount = results.filter((r) => r.passed).length;
  const passRate = results.length > 0 ? (passCount / results.length) * 100 : 0;
  gradeCounts['S'] += gradeCounts['A'];

  const header = [
    `=== 成績レポート ===`,
    `種別: ${type}`,
    `月: ${month}`,
    `受験者数: ${results.length}`,
    `平均点: ${overallAverage.toFixed(1)}`,
    `---`,
  ].join('\n');

  const ranking = results
    .map((r) => {
      const status = r.passed ? '合格' : '不合格';
      return `${r.rank}位: ${r.name} - ${r.average.toFixed(1)}点 (${r.grade}) ${r.name}さんは${status}です`;
    })
    .join('\n');

  const gradeLabels: Grade[] = ['S', 'A', 'B', 'C', 'D'];
  const distribution = gradeLabels
    .map((g) => `${g}: ${gradeCounts[g]}人`)
    .join('\n');

  return [
    header,
    ranking,
    `---`,
    distribution,
    `合格率: ${passRate.toFixed(1)}%`,
    '',
  ].join('\n');
}
```

わかりましたか？
GitHub Copilot CLIに聞いてみましょう。

```bash
@ファイル名のコードにはバグが含まれているようです。一覧で表示して。
```

以下のバグが含まれていました。
![](https://storage.googleapis.com/zenn-user-upload/ff4fbdffaab3-20260404.png)

(私としては結構わかりにくいバグを仕込めたと思ったのですが、さすがですね...)

デバッグは以下の指令でGitHub Copilot CLIにお願いしてみましょう。

```bash
# Pattern: "Expected X but got Y"
@ファイル名 calculateAverage([80, 70, 90]) の結果が 80.0 になるはずが 80.3 になっている。reduce の初期値を確認して修正して
```

```bash
# Pattern: "Unexpected behavior"
@ファイル名 rocessStudentScores のランキングで、最も点数が低い生徒が1位になっている。成績順位は平均点が高い順に並ぶべき。ソート処理を確認して修正して
```

```bash
# Pattern: "Wrong results"
@ファイル名 makeReport のグレード分布でSの人数が実際より多く表示される。Aが3人いるとSの人数が3人多くなる。gradeCountsの集計処理を確認して不要な加算を修正して
```

とこんな感じで、**症状**（何が起こっているか）と **期待される動作**（何が起こるべきか）を説明してください。残りの分析はいい感じに GitHub Copilot CLI が進めてくれます。

### AIが関連するバグを発見

コンテキストを理解したデバッグが真価を発揮するのは、こうした場面です。  
バグのあるアプリで、次のシナリオを試してみてください。ファイル全体を `@` で参照し、ユーザーが報告した症状だけを伝えます。GitHub Copilot CLI は **根本原因** を追跡し、近くに潜む他のバグまで見つけることがあります。

```bash
@src/lib/sample.ts
ユーザー報告:「成績の平均点が手計算と微妙にずれている。差は小さいが常に高めに出る」
なぜこうなるのか調査して原因を特定して
```

結果は以下のようになりました。
![](https://storage.googleapis.com/zenn-user-upload/4d0d66ec16af-20260404.png)

原因箇所の特定と、状況の説明などしてくれており、非常にわかりやすいですね。

これが出来る理由は、GitHub Copilot CLI が **ファイル全体を読み込み**、**バグレポートの文脈を理解し**、**明確な説明と具体的な修正方法** を提示できるからです。

GitHub Copilot CLI はファイル全体を分析するため、質問していない別の問題まで見つけることがあります。たとえば検索機能の修正中に、**大文字小文字の扱い** に関する別のバグにも気づくかもしれません。

### 実世界のセキュリティに関する補足情報

自分のコードのデバッグも重要ですが、本番環境のアプリケーションにおける **セキュリティ脆弱性** を理解することも極めて重要です。  
次の例では、見慣れないファイルを Copilot CLI に渡し、セキュリティ上の問題がないか監査させます。

```bash
@ファイル名 Find all security vulnerabilities in this TypeScript service
```

このファイルには、実際の運用環境で遭遇しうる **現実的なセキュリティパターン** が含まれている想定です。

### よく見かけるセキュリティ用語

- **SQLインジェクション**: ユーザー入力がデータベースクエリに直接埋め込まれ、攻撃者が悪意のあるコマンドを実行できる状態
- **パラメータ化クエリ**: 安全な代替手段。プレースホルダー（`?` など）を使って、ユーザーデータと SQL コマンドを分離する
- **競合状態**: 2つの操作が同時に発生し、互いに干渉して不整合を起こす状態
- **XSS（クロスサイトスクリプティング）**: 攻撃者が悪意のあるスクリプトを Web ページに埋め込むこと

セキュリティ面では、開発の初期では後回しにされがちな項目ではありますが、昨今非常に重要な要素の一つになります。
ただ、セキュリティ担当者がコード全体のレビューやテストを実施するのは現実的ではない為、GitHub Copilot CLIのようなツールを活用して、**コード全体を分析** し、**潜在的な脆弱性を特定** するのが効果的です。

### エラーを理解する

スタックトレースとファイル参照をプロンプトに直接貼り付けると、GitHub Copilot CLI がエラーをソースコードに対応付けやすくなります。

```bash
I'm getting this error:
src/lib/sample.ts:55:51 - error TS2367:
 This comparison appears to be unintentional because
 the types 'string' and 'number' have no overlap.

 student.month === month
 ~~~~~~~~~~~~~~~~~~~~~~~

@ファイル名 Explain why and how to fix it
```

### テストケースを使用したデバッグ

具体的で再現可能なテストケースを渡すことで、GitHub Copilot CLI がより正確に推論できます。  
**正確な入力** と **観測された出力** を書くのがポイントです。

```bash
@ファイル名
 以下のテストケースで期待値と異なる結果が出る。原因を調査して修正して
 入力:    （具体的なコードで再現手順を示す）
 期待:    （正しい値）
 実際:    （観測された値）
 補足:    （再現条件やパターンがあれば）
ポイント: 入力・期待・実際の3点セットを揃えると、GitHub Copilotが原因箇所をピンポイントで特定しやすくなります。
```

![](https://storage.googleapis.com/zenn-user-upload/3dbfa77ecf65-20260404.png)

### コードを通して問題を追跡する

複数のファイルを参照すると、GitHub Copilot CLI にそれらの間の **データフロー** を追跡させて、問題の発生箇所を特定させることができます。

```bash
@src/app/api/sample/route.ts @src/lib/file1.ts @src/lib/file2.ts
 振替推薦のスコアで「同一科目マッチ +40点」が常に加算されている。
 APIルートから getRecommendations() → listSchedules()
 までのデータフローを追跡して、科目フィルタとスコアリングに矛盾がないか確認して
```

こんな感じで指示をだすことで、データフローの追跡と問題発生箇所を特定することができます。

#### データ問題を理解する

データファイルを読み込むコードと、実際のデータファイルを一緒に渡すことで、GitHub Copilot CLI はエラー処理の改善案をより適切に提案できます。

プロンプトの例としては以下のようになります。

```bash
@処理するコードのファイル
@型定義ファイル（あれば）
以下のデータを {関数名} に渡したときのエラーハンドリングを確認して改善案を提案して

 正常データ:
 {JSON}

 異常データ:
 {JSON — 欠損・不正値・型違いなど}

各ケースでクラッシュせず適切に処理できるか確認して
```

## ワークフロー4：テスト生成

### テストを一気に増やす

人が手動でテストを書くと、まずは 2〜3 個の基本的なテストだけで終わることがよくあります。(めんどくさいので...)

テストケースは観点網羅して作成するのが大事ですが、なかなか難しかったりします。

- 正常な入力をテストする
- 不正な入力をテストする
- 代表的なエッジケースをテストする
  ...etc

しかし、GitHub Copilot CLI に **包括的なテスト** を作らせると、もっと広い観点で一気にテストを増やせます。

たとえば、対象のコードファイルを指定しながら、**どの観点をテストしたいか** を箇条書きで伝えると、抜け漏れの少ないテストを作りやすくなります。

例えば、以下のように記載すると非常に効果的です。

```bash
@対象ファイル @関連する型定義
 テストを以下の観点で生成して：
 - 正常系: {主要な機能ごとに1行}
 - 境界値: {しきい値・上限・下限}
 - フィルタ: {条件の組み合わせ}
 - ソート/順序: {並び順の検証}
 - エッジケース: {空データ、1件、大量データ、不正値}
```

### どんなテストが増えるのか

GitHub Copilot CLI は、単に数を増やすだけでなく、**異なる種類の観点** を含めたテストを提案できます。
確認してみたところ、観点網羅も非常に優れている印象でした。

作ってもらったテストは以下のようになりました。
いい感じでかけてそうですね。

```typescript
// sample.ts の簡易テスト（Node.js 直接実行）
// 実行: npx tsx scripts/test-sample.ts

import { processStudentScores, makeReport } from '../src/lib/sample';
import type { StudentScoreInput } from '../src/lib/sample';

let passed = 0;
let failed = 0;

function assert(name: string, actual: unknown, expected: unknown) {
  if (actual === expected) {
    console.log(`  ✅ ${name}`);
    passed++;
  } else {
    console.log(`  ❌ ${name}`);
    console.log(`     期待: ${expected}`);
    console.log(`     実際: ${actual}`);
    failed++;
  }
}

function assertApprox(
  name: string,
  actual: number,
  expected: number,
  tolerance = 0.001,
) {
  if (Math.abs(actual - expected) < tolerance) {
    console.log(`  ✅ ${name}`);
    passed++;
  } else {
    console.log(`  ❌ ${name}`);
    console.log(`     期待: ${expected} (±${tolerance})`);
    console.log(`     実際: ${actual}`);
    failed++;
  }
}

// ============================================================
// 正常系: 主要な機能
// ============================================================

console.log('\n📊 正常系1: 平均点の計算');
{
  const data: StudentScoreInput[] = [
    { name: '田中', type: '期末', month: '04', scores: [80, 70, 90] },
  ];
  const results = processStudentScores(data, '期末', '04');
  assert('平均点が正しい (80+70+90)/3 = 80', results[0].average, 80);
  assert('合計点が正しい 80+70+90 = 240', results[0].total, 240);
  assert('科目数が正しい', results[0].count, 3);
}

console.log('\n📊 正常系2: グレード判定');
{
  const data: StudentScoreInput[] = [
    { name: 'S生徒', type: '期末', month: '04', scores: [95] },
    { name: 'A生徒', type: '期末', month: '04', scores: [85] },
    { name: 'B生徒', type: '期末', month: '04', scores: [75] },
    { name: 'C生徒', type: '期末', month: '04', scores: [65] },
    { name: 'D生徒', type: '期末', month: '04', scores: [40] },
  ];
  const results = processStudentScores(data, '期末', '04');
  const byName = Object.fromEntries(results.map((r) => [r.name, r]));
  assert('95点 → S', byName['S生徒'].grade, 'S');
  assert('85点 → A', byName['A生徒'].grade, 'A');
  assert('75点 → B', byName['B生徒'].grade, 'B');
  assert('65点 → C', byName['C生徒'].grade, 'C');
  assert('40点 → D', byName['D生徒'].grade, 'D');
}

console.log('\n📊 正常系3: 合否判定');
{
  const data: StudentScoreInput[] = [
    { name: '合格者', type: '期末', month: '04', scores: [80] },
    { name: '不合格者', type: '期末', month: '04', scores: [50] },
  ];
  const results = processStudentScores(data, '期末', '04');
  const byName = Object.fromEntries(results.map((r) => [r.name, r]));
  assert('80点 → 合格', byName['合格者'].passed, true);
  assert('50点 → 不合格', byName['不合格者'].passed, false);
}

console.log('\n📊 正常系4: ランキング付与');
{
  const data: StudentScoreInput[] = [
    { name: '佐藤', type: '期末', month: '04', scores: [95] },
    { name: '田中', type: '期末', month: '04', scores: [80] },
    { name: '鈴木', type: '期末', month: '04', scores: [50] },
  ];
  const results = processStudentScores(data, '期末', '04');
  assert('1位は佐藤（95点）', results[0].name, '佐藤');
  assert('2位は田中（80点）', results[1].name, '田中');
  assert('3位は鈴木（50点）', results[2].name, '鈴木');
  assert('1位のrankは1', results[0].rank, 1);
  assert('2位のrankは2', results[1].rank, 2);
  assert('3位のrankは3', results[2].rank, 3);
}

console.log('\n📊 正常系5: レポート生成');
{
  const data: StudentScoreInput[] = [
    { name: '田中', type: '期末', month: '04', scores: [80] },
  ];
  const report = makeReport(data, '期末', '04');
  assert('ヘッダーに種別あり', report.includes('種別: 期末'), true);
  assert('ヘッダーに月あり', report.includes('月: 04'), true);
  assert('受験者数あり', report.includes('受験者数: 1'), true);
  assert('合格率あり', report.includes('合格率:'), true);
}

// ============================================================
// 境界値: しきい値・上限・下限
// ============================================================

console.log('\n🔬 境界値1: グレードの閾値ちょうど');
{
  const data: StudentScoreInput[] = [
    { name: '90点', type: '期末', month: '04', scores: [90] },
    { name: '89点', type: '期末', month: '04', scores: [89] },
    { name: '80点', type: '期末', month: '04', scores: [80] },
    { name: '79点', type: '期末', month: '04', scores: [79] },
    { name: '70点', type: '期末', month: '04', scores: [70] },
    { name: '69点', type: '期末', month: '04', scores: [69] },
    { name: '60点', type: '期末', month: '04', scores: [60] },
    { name: '59点', type: '期末', month: '04', scores: [59] },
  ];
  const results = processStudentScores(data, '期末', '04');
  const byName = Object.fromEntries(results.map((r) => [r.name, r]));
  assert('90点 → S（境界）', byName['90点'].grade, 'S');
  assert('89点 → A（境界）', byName['89点'].grade, 'A');
  assert('80点 → A（境界）', byName['80点'].grade, 'A');
  assert('79点 → B（境界）', byName['79点'].grade, 'B');
  assert('70点 → B（境界）', byName['70点'].grade, 'B');
  assert('69点 → C（境界）', byName['69点'].grade, 'C');
  assert('60点 → C（境界）', byName['60点'].grade, 'C');
  assert('59点 → D（境界）', byName['59点'].grade, 'D');
}

console.log('\n🔬 境界値2: 合否の閾値ちょうど');
{
  const data: StudentScoreInput[] = [
    { name: '60点', type: '期末', month: '04', scores: [60] },
    { name: '59点', type: '期末', month: '04', scores: [59] },
  ];
  const results = processStudentScores(data, '期末', '04');
  const byName = Object.fromEntries(results.map((r) => [r.name, r]));
  assert('60点ちょうど → 合格', byName['60点'].passed, true);
  assert('59点 → 不合格', byName['59点'].passed, false);
}

console.log('\n🔬 境界値3: 極端なスコア');
{
  const data: StudentScoreInput[] = [
    { name: '満点', type: '期末', month: '04', scores: [100] },
    { name: '零点', type: '期末', month: '04', scores: [0] },
  ];
  const results = processStudentScores(data, '期末', '04');
  const byName = Object.fromEntries(results.map((r) => [r.name, r]));
  assert('100点 → S', byName['満点'].grade, 'S');
  assert('100点 → 合格', byName['満点'].passed, true);
  assert('100点の平均', byName['満点'].average, 100);
  assert('0点 → D', byName['零点'].grade, 'D');
  assert('0点 → 不合格', byName['零点'].passed, false);
  assert('0点の平均', byName['零点'].average, 0);
}

// ============================================================
// フィルタ: 条件の組み合わせ
// ============================================================

console.log('\n🔍 フィルタ1: type による絞り込み');
{
  const data: StudentScoreInput[] = [
    { name: '期末の人', type: '期末', month: '04', scores: [80] },
    { name: '中間の人', type: '中間', month: '04', scores: [70] },
    { name: '小テスト', type: '小テスト', month: '04', scores: [90] },
  ];
  const results = processStudentScores(data, '期末', '04');
  assert('期末のみ1件', results.length, 1);
  assert('期末の人が取れる', results[0].name, '期末の人');
}

console.log('\n🔍 フィルタ2: month による絞り込み');
{
  const data: StudentScoreInput[] = [
    { name: '4月', type: '期末', month: '04', scores: [80] },
    { name: '5月', type: '期末', month: '05', scores: [70] },
    { name: '6月', type: '期末', month: '06', scores: [90] },
  ];
  const results = processStudentScores(data, '期末', '05');
  assert('5月のみ1件', results.length, 1);
  assert('5月の人が取れる', results[0].name, '5月');
}

console.log('\n🔍 フィルタ3: type と month の組み合わせ');
{
  const data: StudentScoreInput[] = [
    { name: 'A', type: '期末', month: '04', scores: [80] },
    { name: 'B', type: '期末', month: '05', scores: [70] },
    { name: 'C', type: '中間', month: '04', scores: [90] },
    { name: 'D', type: '中間', month: '05', scores: [60] },
  ];
  assert('期末/04 → 1件', processStudentScores(data, '期末', '04').length, 1);
  assert('中間/05 → 1件', processStudentScores(data, '中間', '05').length, 1);
  assert('期末/06 → 0件', processStudentScores(data, '期末', '06').length, 0);
}

console.log('\n🔍 フィルタ4: 空スコアの除外');
{
  const data: StudentScoreInput[] = [
    { name: '空配列', type: '期末', month: '04', scores: [] },
    { name: '正常', type: '期末', month: '04', scores: [75] },
  ];
  const results = processStudentScores(data, '期末', '04');
  assert('空配列は除外され1件', results.length, 1);
  assert('正常のみ残る', results[0].name, '正常');
}

// ============================================================
// ソート/順序: 並び順の検証
// ============================================================

console.log('\n🏆 ソート1: 降順ソート（高得点が上位）');
{
  const data: StudentScoreInput[] = [
    { name: '低', type: '期末', month: '04', scores: [30] },
    { name: '高', type: '期末', month: '04', scores: [95] },
    { name: '中', type: '期末', month: '04', scores: [70] },
  ];
  const results = processStudentScores(data, '期末', '04');
  assert('1番目は高（95点）', results[0].name, '高');
  assert('2番目は中（70点）', results[1].name, '中');
  assert('3番目は低（30点）', results[2].name, '低');
}

console.log('\n🏆 ソート2: 同点の場合');
{
  const data: StudentScoreInput[] = [
    { name: '先', type: '期末', month: '04', scores: [80] },
    { name: '後', type: '期末', month: '04', scores: [80] },
  ];
  const results = processStudentScores(data, '期末', '04');
  assert('同点でも2件返る', results.length, 2);
  assert(
    'rank が 1 と 2',
    results[0].rank === 1 && results[1].rank === 2,
    true,
  );
}

console.log('\n🏆 ソート3: レポートのランキング表示順');
{
  const data: StudentScoreInput[] = [
    { name: '低い', type: '期末', month: '04', scores: [40] },
    { name: '高い', type: '期末', month: '04', scores: [90] },
  ];
  const report = makeReport(data, '期末', '04');
  const rankingLines = report.split('\n').filter((l) => l.includes('位:'));
  assert('1位の行が先に来る', rankingLines[0].startsWith('1位:'), true);
  assert('1位は高い', rankingLines[0].includes('高い'), true);
}

// ============================================================
// エッジケース: 空データ、1件、大量データ
// ============================================================

console.log('\n⚠️ エッジ1: データ0件');
{
  const results = processStudentScores([], '期末', '04');
  assert('空配列を渡すと0件', results.length, 0);
}

console.log('\n⚠️ エッジ2: 該当なし（フィルタで全除外）');
{
  const data: StudentScoreInput[] = [
    { name: '別type', type: '中間', month: '04', scores: [80] },
  ];
  const results = processStudentScores(data, '期末', '04');
  assert('該当なしで0件', results.length, 0);
}

console.log('\n⚠️ エッジ3: 1件のみ');
{
  const data: StudentScoreInput[] = [
    { name: '唯一', type: '期末', month: '04', scores: [75] },
  ];
  const results = processStudentScores(data, '期末', '04');
  assert('1件だけでも動作する', results.length, 1);
  assert('rank は 1', results[0].rank, 1);
}

console.log('\n⚠️ エッジ4: レポート0件のとき');
{
  const report = makeReport([], '期末', '04');
  assert('0件でもクラッシュしない', typeof report, 'string');
  assert('受験者数0', report.includes('受験者数: 0'), true);
  assert('合格率0%', report.includes('合格率: 0'), true);
}

console.log('\n⚠️ エッジ5: スコアが1科目のみ');
{
  const data: StudentScoreInput[] = [
    { name: '一科目', type: '期末', month: '04', scores: [73] },
  ];
  const results = processStudentScores(data, '期末', '04');
  assert('1科目の平均 = そのスコア', results[0].average, 73);
  assert('1科目の合計 = そのスコア', results[0].total, 73);
}

console.log('\n⚠️ エッジ6: グレード分布の合計が受験者数と一致');
{
  const data: StudentScoreInput[] = [
    { name: 'S', type: '期末', month: '04', scores: [95] },
    { name: 'A', type: '期末', month: '04', scores: [85] },
    { name: 'B', type: '期末', month: '04', scores: [75] },
    { name: 'C', type: '期末', month: '04', scores: [65] },
    { name: 'D', type: '期末', month: '04', scores: [40] },
  ];
  const report = makeReport(data, '期末', '04');
  assert('S: 1人', report.includes('S: 1人'), true);
  assert('A: 1人', report.includes('A: 1人'), true);
  assert('B: 1人', report.includes('B: 1人'), true);
  assert('C: 1人', report.includes('C: 1人'), true);
  assert('D: 1人', report.includes('D: 1人'), true);
  assert('受験者数: 5', report.includes('受験者数: 5'), true);
}

// ============================================================
// 結果サマリー
// ============================================================
console.log('\n' + '='.repeat(40));
console.log(
  `結果: ${passed} passed / ${failed} failed / ${passed + failed} total`,
);
if (failed > 0) {
  console.log('⚠️  失敗したテストがあります！');
  process.exit(1);
} else {
  console.log('🎉 全テスト通過！');
}
```

このように、**正常系・異常系・境界値・保存処理・文字種** まで含めて、広くテストを作れるのが強みです。

また、以下のコマンドも準備してくれて、簡単にテストが実行できるようにしてくれています。

```bash
npx tsx scripts/test-sample.ts
```

これを作ってくれといってすぐ出してくる同僚はなかなか出来る同僚ですね。

### 単体テストを作る

1つの関数だけを集中的にテストしたい場合は、**対象の関数名** と **確認したい入力パターン** を具体的に伝えると効果的です。

```bash
@ファイル名 テストを生成してください。そして以下の観点をカバーしてください。:
- Valid input
- Empty strings
- Invalid number formats
- Very long text
- Special characters
```

このように書くと、GitHub Copilot CLI はその関数に絞って、入力パターンごとのテストを作りやすくなります。

### テストの実行方法を聞く

テストの書き方だけでなく、**どう実行すればいいか** をそのまま質問することもできます。
個人的にはこれが非常に便利だと思ってます。

```bash
どんなテストケースやフレームワークを使うべき？
```

こんな感じでVitestやJestなどテストフレームワークを検討してくれた結果を教えてくれます。

![](https://storage.googleapis.com/zenn-user-upload/061cc6041a25-20260404.png)

Vitestが一番良いみたいですね。

#### 特定シナリオだけをテストする

想定している特定のケースがあるなら、それをそのまま列挙すると、GitHub Copilot CLI がその観点に沿ったテストを作ってくれます。

```bash
@ファイル名
 {関数名} のテストを作成して。{観点}を検証したい：
 - {具体的な入力} → {期待する出力}
 - {具体的な入力} → {期待する出力}
 - ...
```

ここで大事なのは、**「何が心配か」を具体的に書くこと** です。  
そうすると、GitHub Copilot CLI はその不安ポイントに沿ったテストを考えやすくなります。

### 既存テストに追加する

すでにテストファイルがある場合でも、**足りない観点だけ追加** させることができます。
また、GitHub Copilotに記載してもらったテストから更に新たな機能を実装した時にテストケースを追加することもできます。(便利ですよね。)

```bash
@ファイル名 sample_func 関数に対して、以下のエッジケースを含む追加テストを生成してください。
ハイフンを含む名前
複数のファーストネーム
空文字列を入力した場合
アクセント付き文字を含む名前
```

このようにすると、既存テストを丸ごと作り直すのではなく、**不足しているケースだけ補強** しやすくなります。

### Git と組み合わせる流れ

テスト作成の次には、Git と組み合わせた流れも便利です。  
たとえば、変更内容を確認しながら次のような作業につなげられます。

- コミットメッセージを考える
- プルリクエストの説明を書く
- 差分を確認する
- レビュー用の文章を作る

テストを作る → 修正する → 差分を確認する → PRを作る、という流れで使うと実用的です。

### 実装前に調査する

いきなりコードを書くのではなく、先に **ライブラリやベストプラクティスを調べる** 使い方も有効です。

```bash
Researching: What are the best TypeScript libraries for validating user input in CLI apps?
```

いい感じのOutputが出てきました。
![](https://storage.googleapis.com/zenn-user-upload/471233bbc436-20260404.png)

Zodが推奨みたいですね。
このように聞くと、GitHub Copilot は関連情報を調べて、比較しやすい形でまとめてくれます。

### おすすめの使い方

新しい機能や改善を進めるときは、次の順番が分かりやすいです。

1. **/research 調査する**
2. **/plan 実装方針を考える**
3. **コードを書く**
4. **テストを増やす**
5. **差分を確認する**
6. **コミットやPRを整える**

#### /research — 調査する

```bash
/research Next.js 16 の App Router API Routes で Zod
バリデーションを導入するベストプラクティスを調査して。safeParse のエラーレスポンス形式、Standard
Schema との互換性、パフォーマンスへの影響も含めて
```

#### /plan — 実装方針を考える

```bash
 /plan 全 API ルート（12ファイル）に Zod でリクエストバリデーションを追加したい：
 - src/lib/schemas/ にドメインごとのスキーマを定義
 - 既存の型定義（src/lib/types/）からスキーマを生成
 - POST は全フィールド必須、PATCH は全フィールド optional
 - 不正リクエストは 400 + 日本語メッセージ
 - request.json() の try-catch も統一
```

#### コードを書く

```bash
 @src/lib/types/test.ts
 Test 型に基づいた Zod スキーマを src/lib/schemas/test.ts に作成して。TestCreateInput 用と TestUpdateInput 用の2つ。gradeLevel は GradeLevel のユニオン型で制約して

 @src/app/api/tests/route.ts @src/lib/schemas/test.ts
 POST ルートに作成したスキーマでバリデーションを追加して。request.json() の try-catch、safeParseのエラー時は 400 + フィールドごとのエラーメッセージを返すようにして
```

#### テストを増やす

```bash
 @src/app/api/tests/route.ts @src/lib/schemas/test.ts
 バリデーション追加後のテストを生成して：
 - 正常データで 201 が返る
 - name が空文字 → 400 + "名前は必須です"
 - gradeLevel が不正値 → 400
 - request body が JSON でない → 400
 - 必須フィールド欠損 → 400 + 欠損フィールド名
 - 境界値（maxStudents が 0 や負数）
```

#### /diff — 差分を確認する

```bash
 /diff
追加の指示を付けることも可能：
 /diff バリデーションの追加漏れがないか確認して。全ての POST/PATCH ルートに safeParse
が入っているかチェックして
```

#### コミットや PR を整える

```bash
コミット：
 今回の変更をコミットして。コミットメッセージは日本語で、変更内容を簡潔にまとめて

PR 作成：
 /pr create として、今回のバリデーション追加の PR
を作成して。タイトルと本文は日本語で、以下を含めて：
 - 変更の概要（何を、なぜ）
 - 変更ファイル一覧
 - テスト方法
 - 影響範囲

既存 PR の操作：
 /pr              ← 現在のブランチの PR を表示
 /pr status       ← PR のステータス確認
 /pr checks       ← CI の状態確認
```

この流れにすると、**調べてから作る**、**作ったあとにテストする**、**最後にレビューする** という自然な開発手順になります。
まとめると、以下のようになります。

1. /research → 技術調査（Zod + Next.js 16）
2. /plan → TODO・実装計画を plan.md に整理
3. 自然言語 → スキーマ定義 → API ルート修正
4. 自然言語 → テスト生成・実行
5. /diff → 変更差分の確認・レビュー
6. 自然言語 → コミット → /pr で PR 作成

### 要点

- ファイル名は `@your_file.ts` のように、自分のファイル名に置き換えて使う
- 「何をテストしたいか」を箇条書きで具体的に書く
- 正常系だけでなく、異常系・空データ・特殊文字・保存失敗も含める
- まず調査してから実装すると、やり直しが減る
- テスト生成は、たたき台作成に特に強い

GitHub Copilot CLI は、**テストを0から全部任せる** というより、  
**広い観点を一気に洗い出す相棒** として使うと非常に強力です。

## ワークフロー5：Git統合

GitHub Copilot CLI は、コードを書くときだけでなく、**コミットメッセージ作成・変更説明・PR作成・最終チェック** にも使えます。  
ここでは、日々の Git ワークフローで役立つ使い方を、前提知識がなくても分かる形で整理します。

#### コミットメッセージを生成する

まずは、いくつかの変更を **ステージング** してください。  
そのうえで、ステージ済みの差分をもとに Copilot CLI にコミットメッセージを作らせます。

```bash
# まず現在のステージ済み変更を確認
git diff --staged
```

```bash
# Conventional Commit 形式でコミットメッセージを生成
Generate a conventional commit message for: $(git diff --staged)
```

**Conventional Commit** は、次のような形式で書くルールです。

- `feat:` 新機能
- `fix:` バグ修正
- `docs:` ドキュメント修正
- `refactor:` リファクタリング
- `test:` テスト追加・修正

たとえば、次のような形です。

```text
feat(search): add partial keyword matching
fix(auth): handle empty token input
docs(readme): update setup instructions
```

#### 変更内容を平易な言葉で説明してもらう

「このコミットって結局何をしたのか？」を自然な言葉でまとめたい場合もあります。  
そのときは、直近のコミット情報を Copilot CLI に渡します。

```bash
Explain what this commit does: $(git show HEAD --stat)
```

いい感じで以下がわかりやすくなります。

- 最後のコミットの要約を作る
- レビュー相手向けに変更内容を説明する
- 後から自分で見返しやすくする

#### PR の説明を生成する

プルリクエストの説明文も、変更履歴から自動で作れます。

```bash
copilot -p "Generate a pull request description for these changes:
$(git log main..HEAD --oneline)

Include:
- Summary of changes
- Why these changes were made
- Testing done
- Breaking changes? (yes/no)"
```

#### このコマンドの意味

- `git log main..HEAD --oneline`  
  → 現在のブランチで、`main` から増えたコミット一覧を短く表示
- その内容を Copilot CLI に渡して、PR説明文を作らせる

#### 含めてもらう項目

- 変更内容の要約
- なぜ変更したのか
- どんなテストをしたか
- 破壊的変更があるかどうか

これにより、**PR本文を書く手間** をかなり減らせます。

#### 対話モードで `/pr` を使う

Copilot CLI の対話モードでは、`/pr` コマンドで PR 操作ができます。

```text
/pr [view|create|fix|auto]
```

### できること

- `view` : PR を確認する
- `create` : 新しい PR を作る
- `fix` : 既存PRの内容を修正する
- `auto` : 状況に応じて Copilot CLI に判断させる

普段から対話モードを使っているなら、こちらの方が自然に扱えます。

#### プッシュ前に最終レビューする

ブランチ全体の差分を見て、「このまま push して大丈夫か」をチェックする使い方も便利です。

```bash
copilot -p "Review these changes for issues before I push:
$(git diff main..HEAD)"
```

この使いどころは以下になります。

- push 前の最終確認
- 自分で見落としている問題の洗い出し
- 小さな不整合や危険な変更の発見

これは **最終健全性チェック** としてかなり実用的です。

#### `/delegate` で作業を委譲する

明確に切り出せる作業であれば、GitHub Copilot CLI にバックグラウンドで任せることもできます。

```text
/delegate Add input validation to the login form
```

```text
& Fix the typo in the README header
```

### 何が起こるのか

Copilot CLI は状況に応じて、次のような流れで作業を進めます。

1. 変更を新しいブランチにまとめる
2. Draft PR を作成する
3. GitHub 上でバックグラウンド作業を進める
4. 完了後にレビューを求める

#### 向いているタスク

- 入力チェックの追加
- README の軽微な修正
- 明確なバグ修正
- 小さめの独立した作業

つまり、**細かく定義できる仕事を別レーンで進めたいとき** に向いています。

#### `/diff` でセッション中の変更を見る

Copilot CLI とのやり取りで加えた変更を、まとめて確認したいときは `/diff` が便利です。

```text
/diff
```

#### できること

- このセッション中に変更された内容を一覧表示
- コミット前の確認
- 「どこまで変わったか」を視覚的に把握

**コミットする前の見直し** にかなり役立ちます。

#### 実装前に `/research` する

いきなりコードを書くのではなく、まずライブラリやベストプラクティスを調べる使い方も有効です。

```text
/research What are the best Python libraries for validating user input in CLI apps?
```

#### できること

- 関連ライブラリを比較する
- ベストプラクティスを調べる
- 初めて触る分野をざっくり理解する
- 実装前に方針を固める

調査してから作ることで、**手戻りを減らしやすくなります**。

#### おすすめの流れ

新しい変更を作るときは、次の流れが分かりやすいです。

1. **調査する**
2. **方針を考える**
3. **実装する**
4. **テストする**
5. **差分を確認する**
6. **コミットメッセージを作る**
7. **PR説明を書く**
8. **push前にレビューする**

### 例

```text
/research Best practices for validating user input in Python CLI apps
/plan Add input validation to the CLI
/review
/diff
```

```bash
copilot -p "Generate a conventional commit message for: $(git diff --staged)"
```

```bash
copilot -p "Generate a pull request description for these changes:
$(git log main..HEAD --oneline)

Include:
- Summary of changes
- Why these changes were made
- Testing done
- Breaking changes? (yes/no)"
```

```bash
copilot -p "Review these changes for issues before I push:
$(git diff main..HEAD)"
```

ポイントとしては以下になります。

- コミットメッセージは、差分を渡せばかなり自然に作れる
- PR説明は、コミット履歴から自動生成できる
- push前レビューで見落としを減らせる
- `/delegate` は、切り出しやすい小タスクに向いている
- `/diff` は、コミット前の見直しに便利
- `/research` を先に使うと、実装の質が上がりやすい

GitHub Copilot CLI は、**コードを書く支援** だけでなく、  
**Git を含めた開発全体の流れをスムーズにする相棒** として使うと真価を発揮されますね。

振り返ると、具体的な指示により、コードレビューはより包括的なものとなり、リファクタリングは、先にテストを生成しておけばより安全になると感じました。
また、デバッグには、Copilot CLI にエラーとコードの両方を表示することが有効で、テスト生成には、エッジケースとエラーシナリオを含めるべきと考えています。
最後にGit統合により、コミットメッセージとプルリクエストの説明が自動化されます。これは非常に良いですよね。

# 組み込みエージェントとカスタムエージェント

GitHub Copilot CLI には、**組み込みエージェント** と **カスタムエージェント** の2種類のエージェントが存在します。

組み込みエージェントは、GitHub Copilot CLI に最初から用意されているエージェントで、特定のタスクに特化したものです。

一方、カスタムエージェントは、ユーザーが自分で定義して作成するエージェントで、より柔軟にタスクをこなすことができます。
