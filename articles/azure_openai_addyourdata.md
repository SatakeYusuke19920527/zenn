---
title: '生成AIを用いて企業の独自情報にも回答してくれるAIを出来る限り簡単に実装する方法'
emoji: '🐲'
type: 'tech' # tech: 技術記事 / idea: アイデア
topics: ['Azure', 'openai', 'next.js', 'T3Stack', 'addyourdata']
published: true
---

# 目次

1. はじめに
2. 今回作成するシステムの概要
3. Azure OpenAI Service セットアップ
4. Azure AI Search セットアップ
5. Azure Blob Storage セットアップ
6. Add your data で AI Search にインデックスを作成
7. アプリケーションを作成
8. 動作確認
9. お片付け

## はじめに

![Azure logo](/images/azure_openai_handson/azureopenai_logo.png)

昨今話題の Azure OpenAI に独自データを扱うことが出来る【Add your data】がプレビューでリリースされました。

本当に簡単に独自データを扱うことが出来たので、本記事をご覧になられた皆様へ知見を共有していこうと思います。

そしてどうせなら動くものを見ながら「おぉ～」と言っていただきたいので、ハンズオン形式で進めていければと存じます！

### では、Let's ハンズオン！

## 今回作成するシステムの概要

今回作成するシステムは Azure 上で作成します。
下の図をご覧ください。

![Azure dashboard](/images/azure_openai_addyourdata/zentai.png)

以下の様な技術を使います。

- Azure OpenAI(Add your data)
- Azure AI Search
- Azure Blob Storage
- Azure Static Web Apps
- Next.js(T3 Stack)

## Azure OpenAI セットアップ

まずは Azure OpenAI のセットアップです。全体図で言うとここの部分です。
![Azure dashboard](/images/azure_openai_addyourdata/aoai.png)

まずは、Azure 上で動作させるので、何がともあれ Azure の無料アカウントを作成してみましょう！
[Azure の無料アカウントを使ってクラウドで構築](https://azure.microsoft.com/ja-jp/free)

Microsoft アカウントを作成して Azure へログインすれば OK です。

こんな感じの画面が出てきたでしょうか。

![Azure](/images/azure_openai_handson/azure_main.png)

何がともあれ、リソースグループを作成しましょう！
上の検索窓に`リソースグループ`と入力して検索してください。

![Azure](/images/azure_openai_handson/resouce_gr.png)

リソースグループの画面に進んだら、左上らへんにある作成をクリックして、リソースグループを作成しましょう！
次へを進んで、タグは別になくて大丈夫です。
![Azure](/images/azure_openai_handson/make_rg.png)

作成できましたか？

![Azure](/images/azure_openai_handson/rg_fin.png)

ここまで作成できたら、また検索窓に`OpenAI`と入力して Azure OpenAI の画面を開いてください。

また、左上らへんにある作成ボタンをクリックです。
リソースグループは先ほど作成したものを指定し、あとは下の画像のように設定してください。
![Azure](/images/azure_openai_handson/make_ai.png)

ネットワークは`インターネットを含むすべてのネットワークがこのリソースにアクセスできます。`を選択してください。
タグは特に何もつけなくて OK です。

作成ボタンをクリックしてください。デプロイが進行するはずです。(5 分ぐらいでしょうか。)
デプロイが完了すれば、リソースへ移動です。

次はモデルを Deploy する必要があります。

画面左上らへんにある`Azure OpenAI Studioへ移動`をクリックしてください。

![Azure](/images/azure_openai_handson/azureopenaistudio.png)

新しいデプロイの作成をクリック → 新しいデプロイの作成をクリックしてください。

以下の様な画面が出てくると思うので、`gpt-35-turbo-16k`以上を選択して作成をクリック`

![Azure](/images/azure_openai_addyourdata/modeldeploy.png)

このデプロイはすぐ完了します。
下の画面のようになりましたか？

![Azure](/images/azure_openai_addyourdata/deploysuccess.png)

ここまで来れば、AzureOpenAI のセットアップは完了です！
次に進みましょう！

:::message
初回はこのような表示が出ている可能性があります。

![Azure](/images/azure_openai_handson/aoai_error.png)

その場合は表示されているリンクをクリックすると、Microsoft へリクエストを送ることができます。

![Azure](/images/azure_openai_handson/request_access.png)

筆者は数時間ぐらいで許可されたので、気長に待ちましょう。

:::

## Azure AI Search セットアップ

次は Azure DevOps に Repos を作成してコードを管理する場所を作ります。
全体図で言うとここです。

![Azure](/images/azure_openai_addyourdata/aisearch.png)

まずは Azure ポータルから検索窓へ Azure AI Service と検索し、AI Search の作成を選択してください。

![AzureAISearch](/images/azure_openai_addyourdata/aisearch2.png)

次に AISearch リソースの作成です。
価格レベルは Basic(基本)としておいてください。

![AzureAISearch](/images/azure_openai_addyourdata/makeaisearch.png)

残りはデフォルトの設定で OK です。

## Azure Blob Storage セットアップ

独自データ(今回は PDF ファイル)を格納するストレージを作成します。
全体図で言うとここです。

![Azure](/images/azure_openai_addyourdata/blob.png)

Azure ポータルから検索窓へ`ストレージアカウント`と入力して、画面左上の作成よりストレージアカウントを作成してください。

![Azure](/images/azure_openai_addyourdata/blobstorage.png)

残りはデフォルトの設定で大丈夫です。
リソースが作成されたら、移動してコンテナを作成します。

画面左側ペインよりコンテナを選択
![Azure](/images/azure_openai_addyourdata/container.png)

+コンテナボタンよりコンテナを作成してください。

![Azure](/images/azure_openai_addyourdata/input.png)

本記事では名前を input に、匿名アクセスレベルを BLOB へ設定しました。

コンテナが作成出来たら、独自データが記載された PDF ファイルを配置してください。

今回は私が石垣島に旅行に行った際に作成した旅のしおりを使用します。(誰得)

![Azure](/images/azure_openai_addyourdata/shiori.png)

作成したコンテナを選択 → アップロードから PDF ファイルをアップロードしてください。

![Azure](/images/azure_openai_addyourdata/upload.png)

これで Storage の設定は完了です。

## Add your data で AI Search にインデックスを作成

では、Azure AI Service の `Add your data`を用いて独自データの情報を AI Search 内へインデックスを作成し、検索出来るようにしましょう。

全体で言うとこの部分です。

![Azure](/images/azure_openai_addyourdata/makeindex.png)

Azure OpenAI Studio へ移動してください。
画面左側ペインの`チャット`を選択し、`データの追加(プレビュー)`を選択してください。
![Azure](/images/azure_openai_addyourdata/aistudio.png)

そしてデータソースの追加を選択してください。

データソースは先ほど作成した BlobStorage を選択
![Azure](/images/azure_openai_addyourdata/adddata1.png)

先ほど作成したコンテナと AI Search を選択し、次へをクリック

![Azure](/images/azure_openai_addyourdata/adddata2.png)

その後は検索の種類は`キーワード`で OK

保存して閉じるをクリックすると、インデックスの作成が開始されます。

その後、画面右上の配置先→新しいデプロイ先を選択してWeb Appをデプロイしてください。

10分ぐらいたって、デプロイが完了したら、webアプリの規定のドメインのURLをコピーしておいてください。

これで`Add your data`を用いて独自機能を検索出来るバックエンド側の実装が完了しました。
非常に簡単ですよね。

## アプリケーションを作成

今回は T3 Stack という Next.js でフロントサイドもサーバサイドも実装出来るフレームワークを使ってアプリケーションを作成してみようと思います。

- T3 Stack  
  https://create.t3.gg/

まずはお好きなフォルダに移動して git のリポジトリをクローンして
以下のコマンドを実行してください。

```bash
npm create t3-app@latest
```

![Azure](/images/azure_openai_addyourdata/t3init.png)

各種設定は以下のように設定しました。

- t3-sample-app
- TypeScript
- Yes
- Yes
- NextAuth.js
- Prisma
- No
- Yes
- Yes
- ~/

次に、GitHub の認証機能を簡単につけておきます。

環境変数の DISCORD となっている箇所を GITHUB へ変更してください。

そして、github→settings→Developer settings→OAuth Apps へ移動してください。

New OAuth App をクリックして、CLIENT_ID と CLIENT_SECRET を取得してください。

取得した文字列を.env ファイルの以下の箇所へ記載してください。

```env
GITHUB_CLIENT_ID=xxxxxxx
GITHUB_CLIENT_ID=xxxxxxx
```

そこまでできれば、あとは以下のコマンドを実行すれば OK です。

```bash
npm run dev
```

以下の画面が表示されましたでしょうか。
![Azure](/images/azure_openai_addyourdata/createt3app.png)

では、コンポーネントを作成していきます。

src 配下に components フォルダを作成し、その中に以下のようなファイルを作成してください。

- components/Layout.tsx

```tsx: components/Layout.tsx
import { FC, ReactNode } from 'react'
import Head from 'next/head'

type Props = {
  title: string
  children: ReactNode
}

export const Layout: FC<Props> = ({ children, title = 'T3 Stack' }) => {
  return (
    <>
      <Head>
        <title>{title}</title>
        <meta name="description" content="Generated by create-t3-app" />
        <link rel="icon" href="/favicon.ico" />
      </Head>
      <main className="container mx-auto flex min-h-screen flex-col items-center justify-center p-4">
        {children}
      </main>
    </>
  )
}
```

- components/Auth.tsx

```tsx:components/Auth.tsx
import { signIn } from 'next-auth/react'

export const Auth = () => {
  return (
    <div>
      <button
        className="rounded bg-blue-600 py-2 px-4 font-bold text-white hover:bg-blue-800"
        onClick={() => signIn('github')}
      >
        GitHub Auth
      </button>
    </div>
  )
}
```

そして、今回は使用しませんが、環境構築の練習がてら、Docker を使って postgresql のコンテナを作成してみましょう。

ルート階層へ docker-compose.yml ファイルを作成してください。

- docker-compose.yml

```yml:docker-compose.yml
version: "3.8"
services:
  dev-postgres:
    image: postgres:14.4-alpine
    ports:
      - 5434:5432
    environment:
      POSTGRES_USER: yujiro
      POSTGRES_PASSWORD: yujiro
      POSTGRES_DB: yujiro
    restart: always
    networks:
      - lesson
networks:
  lesson:
```

これで OK です。
では、以下のコマンドを実行してください。

```bash
docker compose up -d
```

docker が起動し、postgresql のコンテナが起動していることを確認してください。

その後、Prisma を使用して DB を構築 + 環境変数の設定をします。

.env ファイルへ以下の記載を追記してください。

```env
# AOAI
AZURE_OPENAI_ENDPOINT="https://xxxxxxxx.azure.com/"
AZURE_OPENAI_API_KEY="xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"
AZURE_OPENAI_DEPLOYMENT_ID="gpt-35-turbo-16k"
```

そして、src/env.js の runtimeEnv と server の箇所に以下の記載を追記してください。

```js:src/env.js
runtimeEnv: {
    ...
    AZURE_OPENAI_ENDPOINT: process.env.AZURE_OPENAI_ENDPOINT,
    AZURE_OPENAI_API_KEY: process.env.AZURE_OPENAI_API_KEY,
    AZURE_OPENAI_DEPLOYMENT_ID: process.env.AZURE_OPENAI_DEPLOYMENT_ID,
  },
```

```js:src/env.js
server: {
    ...
    AZURE_OPENAI_ENDPOINT: z.string(),
    AZURE_OPENAI_API_KEY: z.string(),
    AZURE_OPENAI_DEPLOYMENT_ID: z.string(),
  },

```

```bash
npx prisma migrate dev
```

上記コマンドでこちらの入力が必要になった時は init と入力してください。

```bash
npx prisma generate
```

そして上記コマンドで、Prisma のコードを生成し、

```bash
npx prisma studio
```

上記コマンドで、Prisma の管理画面を起動してください。
以下のような画面が起動したら OK です。

![Azure](/images/azure_openai_addyourdata/prismastudio.png)

これで DB の構築も OK です。

ではここから、Azure OpenAI Service へアクセスするためのコードを作成します。
まずはサーバサイドから実装していきます。

まずは使用するライブラリをインストールします。

```bash
npm install @azure/openai
```

そして src/schema/addyourdata.ts というファイルを作成してください。

- src/schema/addyourdata.ts

```ts:src/schema/addyourdata.ts
import z from 'zod'

export const getAddYourDataSchema = z.object({
  content: z.string(),
})

export type GetAddYourDataType = z.TypeOf<typeof getAddYourDataSchema>
```

次に、server/api/routers/addyourdata.ts というファイルを作成してください。

- server/api/routers/addyourdata.ts

```ts:server/api/routers/addyourdata.ts

import {
  getAddYourDataSchema,
} from '../../../schema/addyourdata'

import { AzureKeyCredential, OpenAIClient } from '@azure/openai';

import axios from 'axios';

import {
  createTRPCRouter,
  protectedProcedure,
  publicProcedure,
} from "~/server/api/trpc";

import { env } from '~/env'

export const addYourDataRouter = createTRPCRouter({
  getAddYourData: protectedProcedure
    .input(getAddYourDataSchema)
    .mutation(async ({ ctx, input }) => {

      const endpoint = env.AZURE_OPENAI_ENDPOINT!;
      const azureApiKey = env.AZURE_OPENAI_API_KEY!;
      const deploymentId = env.AZURE_OPENAI_DEPLOYMENT_ID!;

      console.log('🚀 ~ Add your data start ~ 🚀')

      const apiUrl = 'https://[先ほどのWebAppのURL]/conversation';

      const requestData = {
        messages: [
          { role: 'user', content: input.content }
        ]
      };

      const res = await axios.post(apiUrl, requestData);

      const content = `
      # 質問
      ${input.content}
      # 回答
      ${res.data}

      回答を質問に対して要約してください。
      `;
      const messages: any[] = [
        {
          role: 'system',
          content: 'You are a helpful assistant.'
        },
        {
          role: 'user',
          content,
        },
      ];
      const client = new OpenAIClient(
        endpoint,
        new AzureKeyCredential(azureApiKey)
      );

      const result = await client.getChatCompletions(deploymentId, messages);
      console.log("🚀 ~ .mutation ~ result:", result.choices)
      return result.choices;
    }),
})

```

URLを`https://[先ほどのWebAppのURL]/conversation` と/conversationとするところがPoint。

Router を作成したら、server/api/root.ts へ addyourdataRouter を追記してください。

```ts:server/api/root.ts
import { addYourDataRouter } from "~/server/api/routers/addyourdata";
import { postRouter } from "~/server/api/routers/post";
import { createTRPCRouter } from "~/server/api/trpc";

/**
 * This is the primary router for your server.
 *
 * All routers added in /api/routers should be manually added here.
 */
export const appRouter = createTRPCRouter({
  post: postRouter,
  addyourdata: addYourDataRouter,
});

// export type definition of API
export type AppRouter = typeof appRouter;

```

最後にフロントサイドの実装です。

src/pages/index.tsx へ以下の記載を追記してください。

```tsx:src/pages/index.tsx
import type { NextPage } from "next";
import { signOut, useSession } from "next-auth/react";
import { useState } from "react";
import { Auth } from "../components/Auth";
import { Layout } from "../components/Layout";
import { api } from "../utils/api";

const Home: NextPage = () => {
  const { data: session } = useSession();
  const [isLoading, setIsLoading] = useState<boolean>(false);
  const [content, setContent] = useState<string>("");
  const [message, setMessage] = useState<string>("");
  const getAddyourdataQuery = api.addyourdata.getAddYourData.useMutation();

  const getAddyourdata = async () => {
    setIsLoading(true);
    try {
      console.log("🚀 ~ getAddyourdata ~ message:", message);
      const res = await getAddyourdataQuery.mutateAsync({ content: message });
      const resMessage =
        res[0]?.message?.content !== undefined ? res[0]?.message?.content! : "";
      setContent(resMessage);
    } catch (err) {
      console.log("🚀 ~ file: index.tsx:40 ~ getCogsrchData ~ err:", err);
    }
    setIsLoading(false);
  };

  if (!session) {
    return (
      <Layout title="Login">
        <Auth />
      </Layout>
    );
  }
  return (
    <Layout title="Todo App">
      <div className="pt-12">
        <main className="my-12 mt-10 grid w-full md:grid-cols-1 md:gap-6">
          <div>
            <div className="mb-5">
              <p>🔸概要</p>
              <p>
                このサイトは、Azure OpenAI Service + Add your data
                Searchを使った回答を返すサイトです。
              </p>
            </div>
            <div className="mb-5">
              <button onClick={() => signOut()}>sign out</button>
              <p className="my-3 text-xl text-blue-600">
                👍Hello {session?.user?.name}
              </p>
            </div>
          </div>
          <label htmlFor="message" className="block font-medium text-gray-900">
            🔸PDFにて取り込んだ独自情報に関する質問をしてみてください。
          </label>
          <textarea
            id="message"
            onChange={(e) => setMessage(e.target.value)}
            rows={4}
            className="block w-full rounded-lg border border-gray-300 bg-gray-50 p-2.5 text-sm text-gray-900 focus:border-blue-500 focus:ring-blue-500"
            placeholder="Write your thoughts here..."
          ></textarea>
          <div className="flex items-center">
            <button
              onClick={getAddyourdata}
              type="button"
              className="my-5 mb-2 mr-2 rounded-lg border border-gray-200 bg-white px-5 py-2.5 text-sm font-medium text-gray-900 hover:bg-gray-100 hover:text-blue-700 focus:z-10 focus:outline-none focus:ring-4 focus:ring-gray-200"
            >
              get Addyourdata
            </button>
          </div>
          {isLoading ? (
            <div role="status">
              <svg
                aria-hidden="true"
                className="mr-2 h-8 w-8 animate-spin fill-blue-600 text-gray-200 dark:text-gray-600"
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
              {content === "" ? (
                <div></div>
              ) : (
                <div className="block w-full rounded-lg border border-gray-200 bg-white p-6 shadow hover:bg-gray-100">
                  <p className="font-normal text-gray-700">{content}</p>
                </div>
              )}
            </div>
          )}
        </main>
      </div>
    </Layout>
  );
};

export default Home;

```

## 動作確認

では動作確認していきましょう。
![Azure](/images/azure_openai_addyourdata/confirm.png)

以下の画面の PDF にて取り込んだ独自情報に関する質問をしてみてください。

取り込んだ PDF に沿った回答を返してくれるはずです。  
![Azure](/images/azure_openai_addyourdata/12.png)

できましたか？
こんなに簡単に企業の独自情報がAIで活用出来るようになるとは驚きです！

皆さまも是非ハンズオンしてみてくださいね！

なかなか盛りだくさんの内容なので、詰まっちゃった方はぜひコメント欄にてどしどし質問してくださいね！

## お片付け

最後にずっと残してて課金されないようにお片付けです。

以下、2 つの操作を実行すれば OK です。

- Azure OpenAI Studio からデプロイの削除
- Azure よりリソースグループの削除

しっかりお片付けして本日は終わりです。

お疲れ様でした。
