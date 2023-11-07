---
title: 'OpenAIよ。2022年1月より先の未来を見たくないか？【Azure OpenAI × LangChain ハンズオン】'
emoji: '😎'
type: 'tech' # tech: 技術記事 / idea: アイデア
topics: ['Azure','AzureOpenAI','langchain', 'openai', 'next.js']
published: false
---

# 目次

1. はじめに
2. 今回作成するシステムの概要と前提条件
3. Azure Bing Search セットアップ
4. Next.jsコーディング
5. 動作確認
6. お片付け

## はじめに

:::message

### この記事はAzure OpenAIとLangChainを組み合わせて2022年1月以降の知識は検索しながら補完するAIを作成するハンズオン記事となります。

- 前提条件として、以下の記事のハンズオン後を想定しています。
前段のAzure OpenAIへのリクエストまでのハンズオンは以下の記事に記載しておりますので、こちらをご参照ください。
> [まだOpenAI使ったことないの？この記事で全員ハンズオンさせてやんよ！](https://zenn.dev/yusu29/articles/azure_openai_handson)

上記のハンズオンでBing Search以外のセットアップが完了しているところまで実装できます。
ハンズオンを行う際は上記の記事から初めていただけますと、スムーズに進めることが出来ます。

:::

![Azure logo](/images/azure_openai_handson/azureopenai_logo.png)

### 2022年1月...。
このワードを聞いてピンときた方は日頃かなり勉強されているつよつよエンジニアに違いありません。
2022年1月というのは、OpenAIのGPT-3.5モデルの学習範囲の果てと言われています。

![Azure logo](/images/azure_openai_langchain_handson/gpt.png)

### ということは...2022年1月以降の知識であれば...

![Azure logo](/images/azure_openai_langchain_handson/w.png)

### ファーー！！

2022年1月以降の情報であればマウント取り放題です！

...

でも、マウントをとっていても仕方がありません。
私は以前、恩師より```マウントはドライブだけで充分です。```という言葉を教えていただいたことを思い出しました。


では、2022年1月以降の情報でも適切に回答できるようにするにはどうすれば良いのか？
そんな方々に向けたアンサーの一つをこの記事にまとめてみました。
### Let's ハンズオン！


## 今回作成するシステムの概要と前提条件

今回作成するシステムは Azure 上で作成します。
下の図をご覧ください。

![Azure dashboard](/images/azure_openai_langchain_handson/zentai.png)

#### 使用する技術
- Azure Static Web Apps : フロントエンドのホスティングに使用
- Azure OpenAI : 推論等、LLMのサービスにて使用
- Azure Bing Search : 推論のツールとしてBing Searchを使用
- Azure Repos : Next.jsのソースコードを格納
- Azure Pipelines : CDCI環境の構築に使用

#### 知っておくとうれしい概念
- LLM
> LLMとは“Large Language Models”の略で、日本語にすると「大規模言語モデル」を意味します。 AIにインターネットなどから取得した膨大なテキストデータを学習させ、その知識をもとに、文章生成などの自然言語能力の開発・向上に役立てるディープラーニング技術です。
- ReAct
> ReAct（REasoning and ACTing）は、言語モデル（LLM）を使用して思考（推論）と行動を同時に行うための新しい枠組みです。 ReActの主な目的は、自然言語推論と意思決定のタスクを統合することで、より高度な言語理解と知識活用を可能にすることです。

#### 処理の流れ
ざっくりとした処理の流れは以下の通りです。
1. ユーザーがフロントエンドにて質問を入力し、Azure OpenAIへリクエスト
2. 2022年1月以降の知識が必要な場合はLangChainのAgentがBing Searchを用いて回答を補完
3. 補完した回答をAzure OpenAIにて返却
4. Azure Static Web Appsへ返却された回答を表示

では、Azure OpenAIをLangChainを使って強化していきましょう！

## Azure Bing Search のセットアップ

まずは Azure OpenAI のセットアップです。全体図で言うとここの部分です。
![Azure dashboard](/images/azure_openai_langchain_handson/bing.png)

Azureポータルの画面へ移動してください。
![azure](/images/azure_openai_langchain_handson/portal.png)

画面上部の検索窓より、```Bing Search```と入力してください。

Bingリソースを選択して、追加 > Bing Search をクリックしてください。

![azure](/images/azure_openai_langchain_handson/portalbing.png)

リソースグループ及び、名前を入力して、
価格レベルはF1でOKです。

ご契約条件を読み、同意して確認と作成をクリックして、リソースを作成してください。

これでBing Searchのセットアップは完了です。

## Next.jsコーディング

ここからはローカル環境で Next.js の環境構築をします。

![azure](/images/azure_openai_langchain_handson/next_set.png)

Next.jsのコードを少しづつ修正していきます。

まずは、```src/pages/index.tsx```を以下のように修正してください。

```typescript
import axios from 'axios';
import { useState } from 'react';

export default function Home() {
  const [isLoading, setIsLoading] = useState<boolean>(false);
  const [content, setContent] = useState<string>('');
  const [message, setMessage] = useState<string>('');

  const getAzData = async () => {
    setIsLoading(true);
    try {
      console.log('start');
      const res = await axios.post('api/azopenai', { message });
      setContent(res.data[0].message.content);
    } catch (err) {
      console.log('🚀 ~ file: index.tsx:32 ~ getAzData ~ err:', err);
    }
    setIsLoading(false);
  };

  const getAgentData = async () => {
    setIsLoading(true);
    try {
      console.log('start');
      const res = await axios.post('api/langchain', { message });
      setContent(res.data.result.output);
    } catch (err) {
      console.log('🚀 ~ file: index.tsx:32 ~ getAzData ~ err:', err);
    }
    setIsLoading(false);
  };

  return (
    <main className={`flex min-h-screen flex-col items-center p-24`}>
      <h1>🤖なんでも聞いてください🤖</h1>

      <label
        htmlFor="message"
        className="block mb-2 text-sm font-medium text-gray-900 dark:text-white"
      >
        Your message
      </label>
      <textarea
        id="message"
        onChange={(e) => setMessage(e.target.value)}
        rows={4}
        className="block p-2.5 w-full text-sm text-gray-900 bg-gray-50 rounded-lg border border-gray-300 focus:ring-blue-500 focus:border-blue-500"
        placeholder="Write your thoughts here..."
      ></textarea>
      <div className="flex items-center">
        <button
          onClick={getAzData}
          type="button"
          className="my-5 py-2.5 px-5 mr-2 mb-2 text-sm font-medium text-gray-900 focus:outline-none bg-white rounded-lg border border-gray-200 hover:bg-gray-100 hover:text-blue-700 focus:z-10 focus:ring-4 focus:ring-gray-200"
        >
          Azure OpenAIの回答
        </button>
        <button
          onClick={getAgentData}
          type="button"
          className="my-5 py-2.5 px-5 mr-2 mb-2 text-sm font-medium text-gray-900 focus:outline-none bg-white rounded-lg border border-gray-200 hover:bg-gray-100 hover:text-blue-700 focus:z-10 focus:ring-4 focus:ring-gray-200"
        >
          Azure OpenAI + LangChainを使った回答
        </button>
      </div>
      {isLoading ? (
        <div role="status">
          <svg
            aria-hidden="true"
            className="w-8 h-8 mr-2 text-gray-200 animate-spin dark:text-gray-600 fill-blue-600"
            viewBox="0 0 100 101"
            fill="none"
            xmlns="http://www.w3.org/2000/svg"
          >
            <path
              d="M100 50.5908C100 78.2051 77.6142 100.591 50 100.591C22.3858 100.591 0 78.2051 0 50.5908C0 22.9766 22.3858 0.59082 50 0.59082C77.6142 0.59082 100 22.9766 100 50.5908ZM9.08144 50.5908C9.08144 73.1895 27.4013 91.5094 50 91.5094C72.5987 91.5094 90.9186 73.1895 90.9186 50.5908C90.9186 27.9921 72.5987 9.67226 50 9.67226C27.4013 9.67226 9.08144 27.9921 9.08144 50.5908Z"
              fill="currentColor"
            />
            <path
              d="M93.9676 39.0409C96.393 38.4038 97.8624 35.9116 97.0079 33.5539C95.2932 28.8227 92.871 24.3692 89.8167 20.348C85.8452 15.1192 80.8826 10.7238 75.2124 7.41289C69.5422 4.10194 63.2754 1.94025 56.7698 1.05124C51.7666 0.367541 46.6976 0.446843 41.7345 1.27873C39.2613 1.69328 37.813 4.19778 38.4501 6.62326C39.0873 9.04874 41.5694 10.4717 44.0505 10.1071C47.8511 9.54855 51.7191 9.52689 55.5402 10.0491C60.8642 10.7766 65.9928 12.5457 70.6331 15.2552C75.2735 17.9648 79.3347 21.5619 82.5849 25.841C84.9175 28.9121 86.7997 32.2913 88.1811 35.8758C89.083 38.2158 91.5421 39.6781 93.9676 39.0409Z"
              fill="currentFill"
            />
          </svg>
          <span className="sr-only">Loading...</span>
        </div>
      ) : (
        <div>
          {content === '' ? (
            <div></div>
          ) : (
            <div className="block max-w-sm p-6 bg-white border border-gray-200 rounded-lg shadow hover:bg-gray-100">
              <p className="font-normal text-gray-700">{content}</p>
            </div>
          )}
        </div>
      )}
    </main>
  );
}

```

次にLangChainを用いてAzure OpenAIの回答を強化するためのコードを追加します。
pages/api/langchain.tsを作成し、以下のように実装してください。

```typescript
import { ChatOpenAI } from 'langchain/chat_models/openai';
import { initializeAgentExecutorWithOptions } from 'langchain/agents'
import { BingSerpAPI } from 'langchain/tools'
import { Calculator } from 'langchain/tools/calculator'
import { NextApiRequest, NextApiResponse } from 'next';

export default async function handler(
  req: NextApiRequest,
  res: NextApiResponse
) {
  try {
    const input = req.body.message;
    const llm = new ChatOpenAI({
      modelName: "gpt-3.5-turbo",
      temperature: 0.9,
      topP: 1,
    })
    const tools = [
      new BingSerpAPI(
        process.env.AZURE_BINGSEARCH_API_KEY,
        { 'mkt': 'ja-JP' },
      ),
      new Calculator()
    ]

    const executor = await initializeAgentExecutorWithOptions(
      tools,
      llm,
      {
        agentType: 'chat-conversational-react-description',
        verbose: true
      }
    )

    const result = await executor.call({ input });

    return res.json({ result })

  } catch (error) {
    console.log('🚀 ~ file: langchain.ts:20 ~ error:', error);
    return res.json({ message: "エラー" })
  }
}
```

LangChainが必要になるので、ここでインストールしておきます。
```bash
npm install langchain
```

そして、LangChainは.env.localの文字列を規定のものにしておくと、自動で読み取ってくれます。
以下のように.env.localを修正してください。
```env
AZURE_OPENAI_API_KEY=xxxxxxxxxxxxxxxxxxxxxxx
AZURE_OPENAI_API_INSTANCE_NAME=xxxxxxxxxxxxxxxxxxxxxxx
AZURE_OPENAI_API_DEPLOYMENT_NAME=xxxxxxxxxxxxxxxxxxxxxxx
AZURE_OPENAI_API_VERSION=2023-08-01-preview
AZURE_OPENAI_API_ENDPOINT=https://xxxxxxxxxxxxxxxxxxxxxxx
AZURE_BINGSEARCH_API_KEY=xxxxxxxxxxxxxxxxxxxxxxx
```


- AZURE_OPENAI_API_KEYとAZURE_OPENAI_API_ENDPOINTはAzureポータルのOpenAIリソース内の```キーとエンドポイント```の箇所からキー1をコピーしてください。
- AZURE_OPENAI_API_INSTANCE_NAMEはAzureポータルのOpenAIリソース名を記載してください。
- AZURE_OPENAI_API_DEPLOYMENT_NAMEはAzure OpenAI Studioのデプロイ名を記載してください。
- AZURE_OPENAI_API_VERSION=2023-08-01-preview はこのままでOKです。
- AZURE_BINGSEARCH_API_KEYはAzureポータルのBingリソース内の```キーとエンドポイント```の箇所からキー1をコピーしてください。

最後に静的アプリの構成の箇所から、環境変数名を変更してください。
![Azure](/images/azure_openai_langchain_handson/env.png)

これで準備完了です！

## 動作確認

では聞いてみましょう。
![Azure](/images/azure_openai_langchain_handson/aoai.png)

ふふ。そうです。
あなただけの力(AOAI)ではわからないでしょう..

では、LangChainと組み合わせ、BingSearchを組み込んだAOAIに聞いてみましょう。

![Azure](/images/azure_openai_langchain_handson/langchain.png)

### すばらしいですね！
### 2022年1月以降の知識も組み込めたAzure OpenAIが完成しました！

できましたか？

なかなか盛りだくさんの内容なので、詰まっちゃった方はぜひコメント欄にてどしどし質問してくださいね！

## お片付け

最後にずっと残してて課金されないようにお片付けです。

以下、３つの操作を実行すれば OK です。

- Azure OpenAI Studio からデプロイの削除
- Azure よりリソースグループの削除
- Azure DevOps よりプロジェクトの削除

しっかりお片付けして本日は終わりです。

お疲れ様でした。
