---
title: "【超簡単】Next.jsとAzure AD B2Cでログイン機能を持つWebサイトを作る【Next.js 14】"
emoji: "🚀"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["microsoft", "azure", "nextjs", "azureadb2c"]
published: false
publication_name: "microsoft"
---

# はじめに
今回はAzure AD B2CとNext.jsを使って、ログイン機能を実装してみたいと思います。

作成するWebサイトのアーキテクチャは以下のようになります。

![img](/images/azure_adb2c_nextjs_auth/img2.png)

リポジトリは以下になります。
ご参考までに！

https://github.com/SatakeYusuke19920527/azure-adb2c-nextjs-auth

# 環境構築
## Githubリポジトリの作成
まずはリポジトリを作成します。

https://github.com/

![img](/images/azure_adb2c_nextjs_auth/img1.png)

サクサク進めていきます。

![img](/images/azure_adb2c_nextjs_auth/img3.png)

コードの箇所からURLをコピーしてローカルへcloneします。

これでリポジトリの作成は完了です。

## Next.jsプロジェクトの作成


準備できたディレクトリに以下のコマンドを実行してください。
```bash
npx create-next-app . --ts
```

色々質問されるので、全部Yesで回答
```bash
✔ Would you like to use ESLint with this project? … No / Yes
✔ Would you like to use Tailwind CSS with this project? … No / Yes
✔ Would you like to use `src/` directory with this project? … No / Yes
✔ Use App Router (recommended)? … No / Yes
✔ Would you like to customize the default import alias? … No / Yes
✔ What import alias would you like configured? … @/*
Creating a new Next.js app in /Users/s.y/prj/04.webapp/sat-web-repo.
```

出来上がったプロジェクトをVSCodeで開いてください。

まずは必要なモジュールをインストールします。
```bash
npm install @azure/msal-browser @azure/msal-react
```

次に、src/lib/config.tsを作成してください。
```ts:src/lib/config.ts
// src/lib/auth/config.ts
import { Configuration } from '@azure/msal-browser';

export const msalConfig: Configuration = {
  auth: {
    clientId: 'xxxxx',
    authority: 'https://xxxxx.b2clogin.com/xxxxx.onmicrosoft.com/b2c_xxxxx',
    knownAuthorities: ['xxxxx.b2clogin.com'],
    redirectUri: '/',
    postLogoutRedirectUri: '/',
  },
  cache: {
    cacheLocation: 'localStorage',
  },
};

export const loginRequest = {
  scopes: ['openid', 'offline_access'],
};

```

xxxxの部分は後ほどリソース作成後に置き換えるイメージです。

次にsrc/app/layout.tsxを以下のように実装してください。
```tsx:src/app/(main)/layout.tsx

'use client';
import { msalConfig } from '@/lib/config';
import { PublicClientApplication } from '@azure/msal-browser';
import { MsalProvider } from '@azure/msal-react';
import React from 'react';

const MainLayout = ({ children }: { children: React.ReactNode }) => {
  const pca = new PublicClientApplication(msalConfig);
  return (
    <div className="flex h-screen">
        <MsalProvider instance={pca}>
          <main className="bg-slate-50 flex-1 overflow-auto">{children}</main>
        </MsalProvider>
    </div>
  );
};

export default MainLayout;


```

次にユーザー取得のためのカスタムフックを作成します。
src/hooks/useCurrentUser.tsを作成し、以下のように実装してください。
```tsx:src/hooks/useCurrentUser.ts

import { AccountInfo } from '@azure/msal-browser';
import { useMsal } from '@azure/msal-react';

interface Account extends AccountInfo {
  idTokenClaims: {
    aud: string;
    auth_time: number;
    family_name: string;
    given_name: string;
    emails: string[];
    iss: string;
    nbf: number;
    nonce: string;
    sub: string;
    tfp: string;
    ver: string;
  };
}

export interface User {
  sub: string;
  familyName: string;
  givenName: string;
  email: string;
}

const useCurrentUser = (): User | null | undefined => {
  const { accounts } = useMsal();
  if (accounts.length > 0) {
    const account = accounts[0] as Account;
    const user: User = {
      sub: account.idTokenClaims?.sub,
      familyName: account.idTokenClaims?.family_name,
      givenName: account.idTokenClaims?.given_name,
      email: account.idTokenClaims?.emails[0],
    };
    return user;
  }
  return null;
};

export default useCurrentUser;

```

次にsrc/app/page.tsを作成し、以下のように実装してください。
```tsx:src/app/(main)/page.ts
"use client";
import { useMsal } from '@azure/msal-react';
import { useEffect } from 'react';
import useCurrentUser from '../../hooks/useCurrentUser';
import { loginRequest } from '../../lib/config';

export default function Home() {
  const { instance, accounts } = useMsal();
  const user = useCurrentUser();
  useEffect(() => {
    console.log('🚀 ~ SideMenu ~ user:', user);
    console.log('🚀 ~ SideMenu ~ accounts:', accounts);
  }, [accounts]);
  return (
    <main className="flex min-h-screen flex-col items-center justify-between p-24">
      <h1
        onClick={() =>
          user === null
            ? instance.loginRedirect(loginRequest)
            : instance.logout()
        }
        className="flex justify-center px-4 text-2xl font-bold cursor-pointer"
      >
        {user === null ? 'login' : 'logout'}
      </h1>
    </main>
  );
}


```

次に、node.jsのversionを指定しておきます。
以下を一番下に追記してください。

```json:package.json
"engines": {
    "node": "20.9.0"
}
```

次に、next.config.jsをoutput: "standalone"を追記してください
```js:next.config.js
/** @type {import('next').NextConfig} */
const nextConfig = {
  output: "standalone"
}

module.exports = nextConfig
```

ここまで記載したら一回リポジトリへpushしましょう。

![img](/images/azure_adb2c_nextjs_auth/img5.png)

## Azure Static Web Appsの作成
Azureポータルにログインして、リソースグループを作成します。

![img](/images/azure_adb2c_nextjs_auth/img6.png)

![img](/images/azure_adb2c_nextjs_auth/img7.png)

次にStatic Web Appsを作成します。

![img](/images/azure_adb2c_nextjs_auth/img8.png)

![img](/images/azure_adb2c_nextjs_auth/img9.png)

![img](/images/azure_adb2c_nextjs_auth/img10.png)

Static Web AppsのDeployの設定で先ほど作成したGithubを指定します。

あとはデフォルトの設定で作成します。

作成ボタンをクリックして、static web appsをが作成されたことを確認します。

![img](/images/azure_adb2c_nextjs_auth/img11.png)

これでAzure Static Web Appsの作成は完了です。

## Github Actionsを使ってNext.jsをStaticWebAppsにデプロイ

では、Githubのリポジトリへ画面を戻ってください。

Actionsタブに行くと、Azure Static Web Apps CI/CDのワークフローが作成されていることを確認できます。

![img](/images/azure_adb2c_nextjs_auth/img12.png)

しばらく待ってエラーなくDeployされているか確認しましょう。

![img](/images/azure_adb2c_nextjs_auth/img13.png)

こんな感じになっていればOKです。

これで下準備は完了です。
肝心のログイン機能を作っていきましょう！

# Azure AD B2Cの作成

Azureポータルにログインして、Azure AD B2Cを作成します。
Azureportalトップ画面のプラスボタンをクリックして、Azure AD B2Cを検索してください。

Azure MarketplaceからAzure AD B2Cを選択します。
![img](/images/azure_adb2c_nextjs_auth/img15.png)

こんな感じの画面から作成出来ます。
![img](/images/azure_adb2c_nextjs_auth/img16.png)

新しいテナントを作成するを選択して、
![img](/images/azure_adb2c_nextjs_auth/img18.png)

テナントの組織名やリソースグループを記入して作成します。
![img](/images/azure_adb2c_nextjs_auth/img19.png)

テナントを作成して以下のような画面になったら作成完了です。
![img](/images/azure_adb2c_nextjs_auth/img17.png)


画面右上のテナント名が先ほど作成したテナントの名前になっていればOKです！
![img](/images/azure_adb2c_nextjs_auth/img20.png)


## アプリケーションの登録
AzureポータルからAzure AD B2Cの箇所に行き、アプリの登録を選択してください。そこで、新規登録をクリック

以下の画像のように、アプリケーション名とリダイレクトURLの箇所をシングルページアプリケーションに設定し、作成してください。

![img](/images/azure_adb2c_nextjs_auth/img22.png)

## ユーザーフローの作成
次はユーザーフローを作成します。

画面左の箇所からユーザーフローを選択し、新しいユーザーフローをクリック

![img](/images/azure_adb2c_nextjs_auth/img23.png)

ユーザーフローを作成する画面にて、サインインとサインアップを選択してください。
バージョンは推奨でOKです。
![img](/images/azure_adb2c_nextjs_auth/img24.png)

わかりやすいユーザーフローの名前を入れて、ローカルアカウントを選択し、条件付きアクセスの箇所で以下の画像のようにチェックボックスをつけてください。
![img](/images/azure_adb2c_nextjs_auth/img25.png)
![img](/images/azure_adb2c_nextjs_auth/img26.png)

後は、アプリの登録から作成したアプリケーションを選択して、管理>認証をクリックしてください。

スライドして下の方へ行くと、暗黙的な許可およびハイブリッド フローの箇所があるので、それぞれのチェックボックスをオンにして保存してください。

![img](/images/azure_adb2c_nextjs_auth/img27.png)




# 参考資料
https://fwywd.com/tech/aadb2c-auth
https://fwywd.com/tech/nextjs-azure-ad-b2c
