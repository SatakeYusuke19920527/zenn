---
title: "Next.jsとMicrosoft Entra External IDでログイン機能を持つWebサイトを作る"
emoji: "🚀"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["microsoft", "azure", "nextjs", "externalid", "microsoftentra"]
published: false
publication_name: "microsoft"
---

# はじめに
今回はAzure AD B2CとNext.jsを使って、ログイン機能を実装してみたいと思います。

作成するWebサイトのアーキテクチャは以下のようになります。

![img](/images/azure_adb2c_nextjs_auth/img2.png)

リポジトリは以下になります。
ご参考までに！

https://github.com/SatakeYusuke19920527/azure-externalid-nextjs-auth

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
export const msalConfig = {
  auth: {
    clientId: 'xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx', // Azure ポータルで取得したクライアントID
    authority:
      'https://login.microsoftonline.com/xxxxxxxxx-xxxxxxxxxxx-xxxxxxxxxxx', // テナントID
    redirectUri: 'http://localhost:3000', // リダイレクトURI
  },
  cache: {
    cacheLocation: 'sessionStorage', // キャッシュの場所
    storeAuthStateInCookie: false, // IE11やEdgeをサポートする場合はtrueに設定
  },
};

export const loginRequest = {
  scopes: ['User.Read'],
};

```

xxxxの部分は後ほどリソース作成後に置き換えるイメージです。

次にsrc/components/Providers.tsxを以下のように実装してください。
```tsx:src/components/Providers.tsx
'use client';

import { msalConfig } from '@/lib/msalConfig';
import { PublicClientApplication } from '@azure/msal-browser';
import { MsalProvider } from '@azure/msal-react';
import React from 'react';

const msalInstance = new PublicClientApplication(msalConfig);

export function Providers({ children }: { children: React.ReactNode }) {
  return <MsalProvider instance={msalInstance}>{children}</MsalProvider>;
}

```

次にユーザー取得のためのカスタムフックを作成します。
src/hooks/useCurrentUser.tsを作成し、以下のように実装してください。
```tsx:src/hooks/useCurrentUser.ts
import { AccountInfo } from '@azure/msal-browser';
import { useMsal } from '@azure/msal-react';

interface Account extends AccountInfo {
  idTokenClaims: {
    aud: string;
    iss: string;
    iat: number;
    nbf: number;
    exp: number;
    idp: string;
    name: string;
    nonce: string;
    oid: string;
    preferred_username: string;
    rh: string;
    sub: string;
    tid: string;
    uti: string;
    ver: string;
  };
}

export interface User {
  sub: string;
  user_name: string;
  email: string;
}

const useCurrentUser = (): User | null | undefined => {
  const { accounts } = useMsal();
  if (accounts.length > 0) {
    const account = accounts[0] as Account;
    const user: User = {
      sub: account.idTokenClaims?.aud,
      user_name: account.idTokenClaims?.name,
      email: account.idTokenClaims?.preferred_username,
    };
    return user;
  }
  return null;
};

export default useCurrentUser;


```

次にsrc/app/page.tsxを以下のように実装してください。
```tsx:src/app/page.tsx
'use client';
import useCurrentUser from '@/hooks/useCurrentUser';
import { loginRequest } from '@/lib/msalConfig';
import { useMsal } from '@azure/msal-react';
import { useEffect } from 'react';

export default function Home() {
  const { instance, accounts } = useMsal();
  const user = useCurrentUser();
  useEffect(() => {
    console.log('🚀 ~ SideMenu ~ user:', user);
    console.log('🚀 ~ SideMenu ~ accounts:', accounts);
  }, [accounts]);
  return (
    <main>
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
      <h1>
        {user === null
          ? 'Please login to see your profile'
          : `Welcome ${user?.user_name}! 🚀 Warm Welcome 🚀 Your email: ${user?.email} && ${user?.sub}`}
      </h1>
    </main>
  );
}

```

次にsrc/app/layout.tsxを以下のように実装してください。
```tsx:src/app/layout.tsx
import { Providers } from '@/components/Providers'
import { Inter } from 'next/font/google'
import './globals.css'

const inter = Inter({ subsets: ['latin'] })

export const metadata = {
  title: 'External ID',
  description: 'Generated by create next app',
}

export default function RootLayout({
  children,
}: {
  children: React.ReactNode
}) {
  return (
    <html lang="ja">
      <body className={inter.className}>
        <Providers>{children}</Providers>
      </body>
    </html>
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

# Microsoft Entra External IDの設定

まずはEntra管理センターへログインして、外部テナントを作成します。

https://entra.microsoft.com/#home

概要→テナントの管理の画面で作成をクリックしてください。
![img](/images/microsoft_entra_externalid_nextjs_auth/img1.png)

外部を選択して、テナントを選択して作成してください。

![img](/images/microsoft_entra_externalid_nextjs_auth/img2.png)

テナント情報を入力...
![img](/images/microsoft_entra_externalid_nextjs_auth/img5.png)

これで外部テナントのAzureサブスクリプションの作成は完了です。
![img](/images/microsoft_entra_externalid_nextjs_auth/img3.png)

元のAzure PortalからExternal IDのテナントが作成出来ていればOKです！

![img](/images/microsoft_entra_externalid_nextjs_auth/img7.png)


## アプリケーションの登録
Microsoft 管理センターから画面左側のアプリケーション→アプリの登録をクリック

![img](/images/microsoft_entra_externalid_nextjs_auth/img8.png)


アプリケーション登録に際して、必要な情報を入力してください。
今回はNext.jsで実装するので、シングルページアプリケーションを選択してください。
![img](/images/microsoft_entra_externalid_nextjs_auth/img9.png)


```xxxxに管理者の同意を与えます```という箇所があるので、クリックして同意を与えてください。
![img](/images/microsoft_entra_externalid_nextjs_auth/img10.png)

こんな感じに表示されていればOKです！
![img](/images/microsoft_entra_externalid_nextjs_auth/img11.png)

## ユーザーフローの作成
次はユーザーフローを作成します。

画面左の箇所からExternal Identitiesのユーザーフローを選択してください。

![img](/images/microsoft_entra_externalid_nextjs_auth/img12.png)


新しいユーザーフローをクリック
![img](/images/microsoft_entra_externalid_nextjs_auth/img13.png)

ユーザーフローを作成する画面にて、ユーザーフローの名前とパスワードを含むメールを選択し、
![img](/images/microsoft_entra_externalid_nextjs_auth/img14.png)

ユーザー属性には以下3つのチェックを入れて作成をクリックしてください。
- Given Name
- Surname
- Display Name

ユーザーフローをEntraID portalから実行して、リダイレクトされることを確認し、無事にユーザーが登録されていればOKです！

![img](/images/microsoft_entra_externalid_nextjs_auth/img15.png)

## config.tsの設定
先ほど記載した以下のmsalConfigファイルを修正します。

```ts:src/lib/msalConfig.ts
export const msalConfig = {
  auth: {
    clientId: 'xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx', // Azure ポータルで取得したクライアントID
    authority:
      'https://login.microsoftonline.com/xxxxxxxxx-xxxxxxxxxxx-xxxxxxxxxxx', // テナントID
    redirectUri: 'http://localhost:3000', // リダイレクトURI
  },
  cache: {
    cacheLocation: 'sessionStorage', // キャッシュの場所
    storeAuthStateInCookie: false, // IE11やEdgeをサポートする場合はtrueに設定
  },
};

export const loginRequest = {
  scopes: ['User.Read'],
};

```

clientIdはEntra portalのアプリケーション→アプリの登録→全てのアプリケーションの箇所から、作成したアプリケーションを選択し、クライアントIDの箇所を貼り付けます。
![img](/images/microsoft_entra_externalid_nextjs_auth/img16.png)

authorityはURL末尾にテナントIDをくっつけます。xxxxの箇所に記載してください。

これで設定は完了です。

# 動作確認
では、今回はローカルで動かしてみましょう。
Next.jsのアプリケーションを起動してください。
```bash
npm run dev
```

これ以上ないぐらいシンプルな画面になってますね。
![img](/images/microsoft_entra_externalid_nextjs_auth/img17.png)

loginをクリックすると、こんな感じのカスタマイズされたログイン画面が表示されます。
![img](/images/microsoft_entra_externalid_nextjs_auth/img18.png)

別のアカウントを使用するをクリックするとこんな画面が表示されます。
![img](/images/microsoft_entra_externalid_nextjs_auth/img19.png)

メールアドレスにコードを送ってくれます。
![img](/images/microsoft_entra_externalid_nextjs_auth/img20.png)


コードを入力すると、パスワードの設定画面が表示されます！
![img](/images/microsoft_entra_externalid_nextjs_auth/img21.png)

もろもろ登録を完了すると、Microsoft EntraID 管理センターにユーザーが登録されていればOKです！
![img](/images/microsoft_entra_externalid_nextjs_auth/img22.png)




これにて実装は完了です！

お疲れ様でした！

# 参考資料
https://learn.microsoft.com/ja-jp/entra/external-id/customers/sample-single-page-app-react-sign-in
https://learn.microsoft.com/ja-jp/entra/external-id/customers/how-to-create-external-tenant-portal