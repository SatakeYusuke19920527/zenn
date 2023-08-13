---
title: 'まだOpenAI使ったことないの？この記事で全員ハンズオンさせてやんよ！'
emoji: '😎'
type: 'tech' # tech: 技術記事 / idea: アイデア
topics: ['Azure', 'azuredevops', 'openai', 'next.js', 'TypeScript']
published: true
---

# 目次

1. はじめに
2. 今回作成するシステムの概要
3. Azure OpenAI セットアップ
4. Azure DevOps の Azure Repos をセットアップ
5. Next.js でフロントエンド構築
6. Azure Static Web Apps へ Pipelines を用いて Deploy
7. 動作確認
8. お片付け

## はじめに

![Azure logo](/images/azure_openai_handson/azureopenai_logo.png)

昨今ちまたで話題の OpenAI。chatGPT はさらっと触ったけど、API までは触ってないなぁ…という方向けのハンズオン 🖐️ となります。
この記事の目標としては、OpenAI を触ってみたい全てのアゲアゲエンジニアがハンズオン出来ることです。
セットアップで詰まるところはどんどんコメント欄に質問していただいたら、がんがん返していきますので、ご遠慮なく質問してください！

### では、Let's ハンズオン！

## 今回作成するシステムの概要

今回作成するシステムは Azure 上で作成します。
下の図をご覧ください。

![Azure dashboard](/images/azure_openai_handson/zentai.png)

以下の様な技術を使います。

- Azure OpenAI
- Azure Static Web Apps
- Azure DevOps
- Azure Repos
- Azure Pipelines
- Next.js(TypeScript)

盛りだくさんのハンズオンですが、頑張っていきましょう！

## Azure OpenAI セットアップ

まずは Azure OpenAI のセットアップです。全体図で言うとここの部分です。
![Azure dashboard](/images/azure_openai_handson/openai_set.png)

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

以下の様な画面が出てくると思うので、いい感じに入力して作成をクリック

![Azure](/images/azure_openai_handson/modeldeploy.png)

このデプロイはすぐ完了します。
下の画面のようになりましたか？

![Azure](/images/azure_openai_handson/deploysuccess.png)

ここまで来れば、AzureOpenAI のセットアップは完了です！
次に進みましょう！

## Azure DevOps の Azure Repos をセットアップ

次は Azure DevOps に Repos を作成してコードを管理する場所を作ります。
全体図で言うとここです。

![Azure](/images/azure_openai_handson/devops_next_set.png)

まずは Azure DevOps へアクセス
ゆうじろうは DevOps の画面見ただけでワクワクします

[AzureDevOps](https://azure.microsoft.com/ja-jp/products/devops/?nav=min)

![AzureDevOps](/images/azuredevops_staticapps_next/devops.png)

次にプロジェクトの作成です。

サインインして`+ New Project` からプロジェクトを作成してください。

![AzureDevOps](/images/azure_openai_handson/devops_create.png)

無事、project が create されたら、Welcome to the Project と暖かく迎えてくれます。

![AzureDevOps](/images/azure_openai_handson/create_repo.png)

(ロゴかわいいですよね)

まずはコードを管理するリポジトリを作成します。
画面左側の Repos をクリックしてください。

以下のリポジトリの url が表示されるので、コピーしてローカル環境へ git clone です。

![AzureDevOps](/images/azure_openai_handson/create_repo_gitclone.png)

```bash
git clone [リポジトリのurl]
```

これでローカル環境へリポジトリをクローンできれば Azure Repos の環境構築は完了です。

## Next.js でフロントエンド構築

ここからはローカル環境で Next.js の環境構築をします。

![AzureDevOps](/images/azure_openai_handson/next_set.png)

まずは、ローカルへ clone したディレクトリへ移動してください。

```bash
cd yujiro-handson-devops
```

中に入れたら容赦なく Next.js のプロジェクトを create です。

```bash
create-next-app . --ts
```

色々聞かれるので、とりあえず設定

```bash
 ➜  tech-blog git:(main) ✗ create-next-app . --ts
✔ Would you like to use ESLint with this project? … No / Yes
✔ Would you like to use Tailwind CSS with this project? … No / Yes
✔ Would you like to use `src/` directory with this project? … No / Yes
✔ Use App Router (recommended)? … No / Yes
✔ Would you like to customize the default import alias? … No / Yes
✔ What import alias would you like configured? … @/*
Creating a new Next.js app in /Users/s.y/dev/05.next/tech-blog.

Using npm.

Initializing project with template: default-tw


Installing dependencies:
- react
- react-dom
- next
- typescript
- @types/react
- @types/node
- @types/react-dom
- tailwindcss
- postcss
- autoprefixer


added 119 packages, and audited 120 packages in 24s

21 packages are looking for funding
  run `npm fund` for details

found 0 vulnerabilities
Success! Created tech-blog at /Users/s.y/dev/05.next/tech-blog
```

success!と言われれば OK です。

では、VSCode を開いて`next.config.js`に追記しましょう。

```javascript
/** @type {import('next').NextConfig} */
const nextConfig = {
  reactStrictMode: true,
  output: 'standalone', // 追記
};

module.exports = nextConfig;
```

output: 'standalone'を追記しました。

> Static Web Apps にデプロイするために Next.js アプリを構成するには、Next.js プロジェクトのスタンドアロン機能を有効にします。
> この手順では、Next.js プロジェクトのサイズを小さくして、Static Web Apps のサイズ制限を下回るようにします。
> 詳細については、スタンドアロンに関するセクション[https://learn.microsoft.com/ja-jp/azure/static-web-apps/deploy-nextjs-hybrid#enable-standalone-feature]を参照してください。

では、せっかくの Next.js なので、サーバサイドレンダリングも追加してみましょう。
pages/index.tsx は以下のコードとなります。

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
  return (
    <main className={`flex min-h-screen flex-col items-center p-24`}>
      <h1>ネガティブなことを言ってみてください</h1>

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
      <button
        onClick={getAzData}
        type="button"
        className="my-5 py-2.5 px-5 mr-2 mb-2 text-sm font-medium text-gray-900 focus:outline-none bg-white rounded-lg border border-gray-200 hover:bg-gray-100 hover:text-blue-700 focus:z-10 focus:ring-4 focus:ring-gray-200"
      >
        ポジティブ変換
      </button>
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

axios を使用するので、install しておきましょう。

```bash
npm install axios
```

src/pages/api/azopenai.ts を作成してください。
コードは以下となります。

```typescript
import { getAzOpenAIData } from '@/models/azopenai/azopenaiApplicationService';
import type { NextApiRequest, NextApiResponse } from 'next';

export default async function handler(
  req: NextApiRequest,
  res: NextApiResponse
) {
  try {
    const message = req.body.message;
    const data = await getAzOpenAIData(message);
    res.json(data);
  } catch (error) {
    console.log('🚀 ~ file: hello.ts:13 ~ error:', error);
  }
}
```

次は OpenAI へアクセスする関数を作成します。
以下二つのフォルダとファイルを作成してください。

> src/models/azopenai/azopenaiApplicationService.ts
> src/models/azopenai/azopenaiRepository.ts

それぞれのコードは以下です。

azopenaiApplicationService.ts

```typescript
import { AzOpenaiRepository } from './azopenaiRepository';

export const getAzOpenAIData = async (message: string) => {
  try {
    const repo = new AzOpenaiRepository();
    return await repo.getAzOpenAIData(message);
  } catch (err) {
    return err;
  }
};
```

azopenaiRepository.ts

```typescript
import { AzureKeyCredential, OpenAIClient } from '@azure/openai';

export class AzOpenaiRepository {
  async getAzOpenAIData(message: string) {
    console.log('start', process.env.AZURE_OPENAI_ENDPOINT!);
    return new Promise(async (resolve, reject) => {
      const endpoint = process.env.AZURE_OPENAI_ENDPOINT!;
      const azureApiKey = process.env.AZURE_OPENAI_API_KEY!;
      const deploymentId = process.env.AZURE_OPENAI_DEPLOYMENT_ID!;
      const content = `
      ${message}
      #上記をポジティブなフィードバックに変換して
      `;
      try {
        const messages = [
          { role: 'system', content: 'You are a helpful assistant.' },
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
        resolve(result.choices);
      } catch (error: any) {
        console.log(
          '🚀 ~ file: openaiRepository.ts:29 ~ AzOpenaiRepository ~ returnnewPromise ~ error:',
          error
        );
        reject(error);
      }
    });
  }
}
```

以下もインストールしておきましょう！

```bash
npm install @azure/openai
```

環境変数は.env.local に記載です。
ルート階層に`.env.local`を作成してください。

.env.local

```
AZURE_OPENAI_ENDPOINT=https://xxxxxxxxxxxxxxxxx/
AZURE_OPENAI_API_KEY=xxxxxxxxxxxxxxxxx
AZURE_OPENAI_DEPLOYMENT_ID=xxxxxxxxxxxxxxxxx
```

では 3 つの環境変数を取得しましょう。
AZURE_OPENAI_ENDPOINT と AZURE_OPENAI_API_KEY はは
Azure へログイン →OpenAI と検索 → 作成した OpenAI を選択 → リソース管理 → キーとエンドポイントの箇所を参照です。

![AzureDevOps](/images/azure_openai_handson/key_endpoint.png)

AZURE_OPENAI_DEPLOYMENT_ID は
Azure OpenAI Studio→ デプロイの箇所からデプロイ名をコピーしてきてください。

ここまで実装して、実行した時にエラーがなければ OK です。

```bash
npm run dev
```

![Next.js](/images/azure_openai_handson/run_dev.png)

問題なければ Azure DevOps の Repos へ push しましょう。
Azure DevOps に戻ったら以下のように code が push されていれば OK です。

![AzureDevOps](/images/azure_openai_handson/repos.png)

## Azure Static Web Apps へ Pipelines を用いて Deploy

では、Azure Static Web Apps へ Pipeline よりホスティングです。
全体図で言うとこの部分です。
![AzureDevOps](/images/azure_openai_handson/pipeline_set.png)

Azure へログインして、Azure Static Web Apps を作成しましょう。
![Azure](/images/azure_openai_handson/azurestaticwebapps.png)

East Asia でデプロイの詳細はその他を入力

![Azure](/images/azuredevops_staticapps_next/setting.png)

タグ → 確認及び作成より作成してください。

リソースに移動して、デプロイトークンの管理からデプロイトークンをコピーしておいてください。

では、Azure DevOps に戻り、Azure Pipeline をクリックし、create pipeline をクリック

Azure Repos Git を選択
![Azure](/images/azuredevops_staticapps_next/reposgit.png)

リポジトリを選択したら、Starter Pipeline を選択

![Azure](/images/azuredevops_staticapps_next/stater.png)

yml ファイルへ AzureStaticWebApps の箇所を追記

```yml
# Starter pipeline
# Start with a minimal pipeline that you can customize to build and deploy your code.
# Add steps that build, run tests, deploy, and more:
# https://aka.ms/yaml

trigger:
  - main

pool:
  vmImage: ubuntu-latest

steps:
  - script: echo Hello, world!
    displayName: 'Run a one-line script'

  - script: |
      echo Add other tasks to build, test, and deploy your project.
      echo See https://aka.ms/yaml
    displayName: 'Run a multi-line script'

  # Static Web Appsのデプロイ
  - task: AzureStaticWebApp@0
    inputs:
      app_location: '/'
      api_location: ''
      output_location: '.next'
    env:
      azure_static_web_apps_api_token: $(deployment_token)
      AZURE_OPENAI_ENDPOINT: $(AZURE_OPENAI_ENDPOINT)
      AZURE_OPENAI_API_KEY: $(AZURE_OPENAI_API_KEY)
      AZURE_OPENAI_DEPLOYMENT_ID: $(AZURE_OPENAI_DEPLOYMENT_ID)
```

画面右上の Variables→New Variables より Name に`deployment_token`と入力し、value へ先ほどコピーしたデプロイメントトークンを貼り付けて OK→save をクリック

![Azure](/images/azuredevops_staticapps_next/deploytoken.png)

あとは`Save and Run`を実行して、神に Deploy が成功することを祈りましょう。

![Azure](/images/azuredevops_staticapps_next/deploying.png)

ざわ...
ざわ...

...

#### success!!!

![Azure](/images/azuredevops_staticapps_next/deploysuccess.png)

では、Azure の Azure Static Web Apps に戻って URL にアクセスしてみましょう！

![Azure](/images/azuredevops_staticapps_next/url.png)

無事に Deploy されていることを確認して作業は完了です！

## 動作確認

ここまでできたら後はネガティブをアプリへぶつけるだけです。

思いのたけをテキストボックスに入れてボタンをクリックしてみてください。

OpenAI がポジティブに変換していい感じに返事をしてくれます！

![Azure](/images/azure_openai_handson/run_dev.png)

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
