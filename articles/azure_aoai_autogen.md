---
title: '【AutoGenハンズオン】マルチエージェントにディスカッションさせて結論を出させてみる🚀【マルチエージェント入門】'
emoji: '🧑‍🏫'
type: 'tech' # tech: 技術記事 / idea: アイデア
topics: ['Azure', 'AutoGen', 'AI', 'agent', 'multi-agent']
published: true
publication_name: microsoft
---

# はじめに

今回は今話題の AutoGen を使って、生徒の進学先をマルチエージェントにディスカッションさせて決めてみます。
ハンズオン形式で進めていくので是非一緒に手を動かしながら学んでいければと思います。

本記事を最後まで実施すると、動画のような AutoGen を使ったマルチエージェント(先生 × 生徒)のディスカッション → 結論を出すところまでを体験することができます。

- 今回のシチュエーション
  - 関西の高校生で、進路に悩んでいる生徒が先生に志望校は大阪公立大学・神戸大学・大阪大学・京都大学・東京大学です。
  - 先生は大阪公立大学出身で、生徒を大阪公立大学に進学させたいと考えています。
  - さて、生徒は先生とディスカッションしてどこにいくことになるのでしょうか？

https://youtu.be/miOxu2kiKgM

是非頑張ってください 🚀

# 前提知識

## AI エージェントとは？

AI エージェントとは、複雑な目標を自律的に遂行できる AI システムを指します。
従来の AI システムが特定のタスクに特化していたのに対し、LLM を活用した AI エージェントは、与えられた目標を達成するために必要な行動を自ら決定し、実行することができます。
理想を言えば、人がほとんど指示をしなくとも、AI が自分でやるべきことを考えて、さまざまなツールを活用して人間が求める目標に向かって積極的にタスクをこなしてもらいたいとのこと。

## マルチエージェントとは？

一連の処理の中で、複数のシステムプロンプトを使って、役割やステップごとに別々の AI エージェントで処理を行う、ワークフローの最適化を目的とした処理のことです。

![](https://storage.googleapis.com/zenn-user-upload/cbd3bc5195fc-20241207.png)

例えば、以下のようなシナリオで使うことが可能です。

1. 数式問題の回答
2. RAG を用いたチャット
3. 意思決定
4. マルチエージェントによるコード記述
5. 動的なグループチャット
6. 会話型のチェスゲーム対決

マルチエージェント型のアーキテクチャの動作例はこんな感じ

![](https://storage.googleapis.com/zenn-user-upload/d49d661d8e10-20241207.png)

夢にまでみた、**_「勝手にええ感じにやっといてくれや」_** が実現できそうな予感です。

## AutoGen とは？

Microsoft、ペンシルベニア州立大学、ワシントン大学が中心になって開発されている汎用的に使える AI エージェントツールです。Python 版と.NET 版の 2 パッケージがオープンソースソフトウェアとして開発されています。

![](https://storage.googleapis.com/zenn-user-upload/590c7cd8aa94-20241207.png)

Agent はカスタマイズ可能で、2 人の会話や 3 人の会話、はたまたグループの会話なども構築することが出来ます。

![](https://storage.googleapis.com/zenn-user-upload/7b453fab46de-20241207.png)

ここまでで前提知識の input は完了です！

ではハンズオンしてみましょう 🚀

# python 実行環境構築手順

VSCode の拡張機能である DevContainer を使って、python の開発環境を構築します。

0. VSCode へ DevContainer をインストール
   ![](https://storage.googleapis.com/zenn-user-upload/9167c43c2bd7-20241207.png)

1. VSCode の DevContainer を使って環境構築
   作業ディレクトリの作成

```bash
mkdir py-func
```

VSCode を開き、cmd + shift + p から
Add DevContainer Configuration file を選択

![](https://storage.googleapis.com/zenn-user-upload/c92e49c6ee4e-20241203.png)

ワークスペースに追加
![](https://storage.googleapis.com/zenn-user-upload/271e43eea492-20241203.png)

python3 を選択
![](https://storage.googleapis.com/zenn-user-upload/1211deaf23c4-20241203.png)

version は 3.11-bookworm
![](https://storage.googleapis.com/zenn-user-upload/a289e2db023c-20241203.png)

追加機能は poetry を選択
![](https://storage.googleapis.com/zenn-user-upload/6b56d13f807e-20241203.png)

.github ファイルを追加
![](https://storage.googleapis.com/zenn-user-upload/82258e599dff-20241203.png)

devcontainer.json へ poetry の version を記載

```:json
// For format details, see https://aka.ms/devcontainer.json. For config options, see the
// README at: https://github.com/devcontainers/templates/tree/main/src/python
{
  "name": "Python 3",
  // Or use a Dockerfile or Docker Compose file. More info: https://containers.dev/guide/dockerfile
  "image": "mcr.microsoft.com/devcontainers/python:1-3.11-bookworm",
  "features": {
    "ghcr.io/devcontainers-extra/features/poetry:2": {
			"version": "1.5.1"
		}
  }

  // Features to add to the dev container. More info: https://containers.dev/features.
  // "features": {},

  // Use 'forwardPorts' to make a list of ports inside the container available locally.
  // "forwardPorts": [],

  // Use 'postCreateCommand' to run commands after the container is created.
  // "postCreateCommand": "pip3 install --user -r requirements.txt",

  // Configure tool-specific properties.
  // "customizations": {},

  // Uncomment to connect as root instead. More info: https://aka.ms/dev-containers-non-root.
  // "remoteUser": "root"
}
```

再度、command + shift + p から reopen in Container を選択
![](https://storage.googleapis.com/zenn-user-upload/7c9c25b7fc3f-20241203.png)

Linux 上に開発環境が構築出来る
![](https://storage.googleapis.com/zenn-user-upload/5e4be234aafe-20241203.png)

python と poetry の version を確認

```:bash
python --version
poetry --version
```

![](https://storage.googleapis.com/zenn-user-upload/2331b79f0547-20241203.png)

これで DevContainer の環境構築は完了です。

# AutoGen 実装

今回は python で実行するので、モジュールをインストールします。

```bash
pip install pyautogen
```

次に main.py を作成し、以下のコードを記述します。

```python
import os

from autogen import ConversableAgent

model = os.getenv("AOAI_MODEL")
api_key = os.getenv("AOAI_API_KEY")
endpoint = os.getenv("AOAI_ENDPOINT")

Student = ConversableAgent(
    "Student",
    system_message=(
        "あなたは関西の高校生で、進路に悩んでいる生徒です。志望校は大阪公立大学・神戸大学・大阪大学・京都大学・東京大学です。"
        "具体的にどの大学に行くか迷っています。もしくは就職という選択肢もあります。将来的にはエンジニアになりたいという目標も持っています"
        "先生と話し合いながら、どの進路が自分に合っているのか考え、この会話で進路を確定させる必要があります。"
        "自分の目標を明確にしてください。積極的に質問をして、自分の将来について具体的に相談しましょう。"
    ),
    llm_config={"config_list": [{
        "model": model,
        "api_key": api_key,
        "api_type": "azure",
        "base_url": endpoint,
        "api_version": "2024-02-15-preview",
    },]},
    human_input_mode="NEVER",
)

Teacher = ConversableAgent(
    "Teacher",
    system_message=(
        "あなたは進路指導の先生です。生徒が進路に迷っているため、その相談に乗っています。"
        "生徒が持つ希望や得意なこと、興味を引き出しながらどの進路が適しているかを一緒に考え、進路を決定してください。"
        "また、生徒の成績は優秀で、どの大学においても合格可能ですが、就職という選択肢もあります。"
        "あなたは大阪公立大学の出身で、生徒を大阪公立大学に進学させたいと考えています。"
        "最終的に生徒が自信を持って進路を決められることがゴールです。ただし、この会話の中で具体的に以下の選択肢の中から進路を確定してください。"
        "大阪公立大学・神戸大学・大阪大学・京都大学・東京大学・ハーバード大学・エンジニアへ就職"
    ),
    llm_config={"config_list": [{
        "model": model,
        "api_key": api_key,
        "api_type": "azure",
        "base_url": endpoint,
        "api_version": "2024-02-15-preview",
    },]},
    human_input_mode="NEVER",
)

result = Teacher.initiate_chat(
    Student,
    message=(
        "こんにちは！では進路相談を開始しましょう！生徒の相談はなんでも聞きますよ！"
        "大学どこにしましょうか。どこか進学したい大学はありますか？"
        "どんな分野に興味があるのか、そして目標について教えてください。"
        "最後に、どこの大学もしくは就職するのか、確定してください。"
    ),
    max_turns=3
)
print(result)

```

最後に環境変数を設定します。

```bash
export AOAI_MODEL="gpt-4o"
export AOAI_API_KEY="xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"
export AOAI_ENDPOINT="https://xxxxxxxxxxxxx.openai.azure.com/openai/deployments/gpt-4o/chat/completions?api-version=2024-08-01-preview"
```

それでは実行してみましょう 🚀

```bash
python main.py
```

完成品がこちらになります。
大阪大学か神戸大学にいくことになっちゃいましたね...
https://youtu.be/miOxu2kiKgM

# まとめ

今回は AutoGen を使って、生徒の進学先をマルチエージェントにディスカッションさせて決めてみました。
AutoGen を使うことで、簡単にマルチエージェントのディスカッションを実装することができます。
他にも様々な機能があるので、是非色々なシチュエーションで使ってみてください。

それでは 👋

# スタートアップ企業様向けのお知らせ

日本マイクロソフトでは、スタートアップ企業様向けに、ビジネスを支援するプログラムをご提供しています。
Azure の無料クレジットが`最大$150,000`もらえるので、是非チェックしてみてください。

https://speakerdeck.com/satakeyusuke19920527/microsoft-for-startups-founders-hub

# 参考文献

https://microsoft.github.io/autogen/0.2/
