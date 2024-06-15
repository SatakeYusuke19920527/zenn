---
title: "Azure OpenAI ServiceがMicrosoft BuildでUpdate発表されたよ🎉"
emoji: "🚀"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["Azure", "Azure OpenAI Service", "AI", "OpenAI", "AI"]
published: false
---

# 目次
1. はじめに
2. この記事のターゲット
3. 2024年5月13日にMicrosoftは、Azure AI における OpenAI の新しい主力モデルである GPT-4o のリリースを発表したよ！
4. 参考サイト

# はじめに
この記事では、5/21 - 5/23 で発表されたMicrosoft Build の中で特に昨今話題のAzure OpenAI Serviceについて何がUpdateされたのかわかりやすく、いい感じにまとめていきます。
特にGPT-4oについて焦点をあてていこうと思います。

# この記事のターゲット

## IT業界には様々な方がいらっしゃいますが、本記事を読むといいのは以下のような人です。
- Azureに興味がある人
- OpenAIに興味がある人
- Microsoft Buildってなんかやってたね...知らんけど...の人
- 興味はあるよ！でも英語の記事読むのめんどくさいよ！の人
- キャッチアップしてやるから、ええ感じの記事書いてみろや！の人
- なんでもいいから、最新の情報を知りたい人

```是非、本記事でええ感じにキャッチアップしていただければと思います！```

# 2024年5月13日にMicrosoftは、Azure AI における OpenAI の新しい主力モデルである GPT-4o のリリースを発表したよ🎉

## GPT-4oの概要
 
***
GPT-4o（「o」は「omni」の略）は、より自然な人間とコンピュータのインタラクションに向けた一歩です。テキスト、音声、画像、ビデオの任意の組み合わせを入力として受け入れ、テキスト、音声、画像の出力を任意の組み合わせで生成します。音声入力には最短232ミリ秒で応答でき、平均320ミリ秒で人間の応答時間に近い応答が可能です。(新しいウィンドウで開きます)会話で。英語のテキストとコードでは GPT-4 Turbo のパフォーマンスに匹敵し、英語以外の言語のテキストでは大幅に改善され、API でははるかに高速で 50% 安価です。GPT-4o は、既存のモデルと比較して、視覚と音声の理解に特に優れています。
***

GPT-4oのoはomniの略なんですね。
omniっていうと、```全て``` という意味があるので、GPT-4oは、テキスト、音声、画像、動画全部いけちゃうぜという意気込みを感じます。

その中でも、OpenAI社の以下のDEMOはかなりインパクトがありましたね。
https://vimeo.com/945586717 

従来のモデルであるGPT-3.5やGPT-4は音声モードを使用して ChatGPT と会話することができ、平均で 2.8 秒 (GPT-3.5) と 5.4 秒 (GPT-4) の遅延がありました。
まぁ、そんなイライラするほどのことでもないですが、AIと話している感じはするなぁという感触でした。

ここでGPT-4oのパフォーマンスを見てみましょう。

![gpt4o](/images/azure_openai_service_build/gpt4o.png)

縦軸の説明は以下です。
- MMLU: Massive Multi-task Language Understandingは大規模マルチタスク言語理解
- GPQA: Graduate Level Expert Reasoning は高度な推論能力を評価するためのベンチマーク
- MATH: 数学の計算能力を示すベンチマーク
- HumanEval: プログラム合成の能力を評価するためのベンチマーク
- MGSM: Multilingual Grade School Mathは小学校レベルの算数の問題を解く能力を評価するベンチマーク
- DROP: Discrete Reasoning Over Paragraphsは、モデルが英語テキストの段落から関連情報を抽出してから、それらに対して個別の推論手順を実行する必要がある評価を行うベンチマーク

GPT-4oになってから、マルチタスクの実行能力や、数学の計算能力、コーティングの能力にかなりの向上があることがわかります。

また、1分あたりのトークン数(TPM)は150k,
Training Data: 2023年10月まで学習されており、
価格(100万トークン):Input: $5/ Output: $15と従来のモデルよりお安くなっています。

### 結論を言うと、いっぱい文字数を処理出来て、２０２３年１０月までのデータから推論も可能で且つ値段も従来よりや安いモデル
それがGPT-4oになりますね。


最後になりますが、GPT-4oはOpenAI社も全ての能力を把握しているわけではなく、以下のような記述をしています。
***
GPT-4o では、テキスト、ビジョン、オーディオにわたってエンドツーエンドで単一の新しいモデルをトレーニングしました。つまり、すべての入力と出力が同じニューラル ネットワークによって処理されます。GPT-4o はこれらすべてのモダリティを組み合わせた最初のモデルであるため、モデルで何ができるのか、そしてその限界についてはまだ調査の段階にすぎません。
***

生まれてきた我が子は能力が未知数なので、色々と使ってみて出来ること、出来ないことを明確にしていきましょうね！ということですね。

GPT-4oは使用する人のアイディア次第でこれからもどんどん使用用途が向上していきそうです。

それでは。

# 参考サイト
- Microsoft Build 2024 Book of News  
https://news.microsoft.com/build-2024-book-of-news/

- Product category: AI + Machine Learning  
https://azure.microsoft.com/en-us/blog/category/ai-machine-learning/

- Introducing GPT-4o: OpenAI’s new flagship multimodal model now in preview on Azure  
https://azure.microsoft.com/en-us/blog/introducing-gpt-4o-openais-new-flagship-multimodal-model-now-in-preview-on-azure/

- Introducing GPT-4o: OpenAI’s new flagship multimodal model now in preview on Azure  
https://azure.microsoft.com/en-us/blog/introducing-gpt-4o-openais-new-flagship-multimodal-model-now-in-preview-on-azure/

- Hello GPT 4o  
https://openai.com/index/hello-gpt-4o/