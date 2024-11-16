---
title: "Microsoft EntraIDでGoogleログインを実装する方法"
emoji: "👍"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["azure", "google", "microsoft", "externalid", "microsoftentra"]
published: true
publication_name: microsoft
---

![](https://storage.googleapis.com/zenn-user-upload/f9ad2987ab6f-20241116.png)

# はじめに
Microsoft EntraIDのExternal IDを使ってGoogleログインを実装する方法を紹介します。

GCPではFirebase AuthやGoogle Identity Platformを使ってGoogleログインを実装することが出来ますが、AzureでもMicrosoft EntraIDのExternal IDを使ってGoogleログインを実装することが可能だったりします。

ユーザー名とパスワードでログインももちろん良いですが、Googleログインもあった方がユーザー体験が向上するので、今回はMicrosoft EntraIDのExternal IDを使ってGoogleログインを実装してみたいと思います。

ユーザー名 + passwordでログインするハンズオンはこちらです。

https://zenn.dev/microsoft/articles/microsoft_entra_externalid_nextjs_auth

今回のハンズオンのサンプルコードを以下に配置していますのでご参照ください。
https://github.com/SatakeYusuke19920527/azure-externalid-google-federation

それではハンズオン開始🖐️

# 環境構築
まずはGithubのリポジトリ作成してください。
![](https://storage.googleapis.com/zenn-user-upload/23c1f844a1f5-20241116.png)

次にローカルへcloneです。
- git clone https://github.com/SatakeYusuke19920527/azure-externalid-google-federation.git
![](https://storage.googleapis.com/zenn-user-upload/44f516b421ce-20241116.png)

以下のコマンドでNext.jsプロジェクトを作成します。
```bash
create-next-app . --ts
```

![](https://storage.googleapis.com/zenn-user-upload/94c34baad0ef-20241116.png)

npm run dev で動作確認し、Next.jsのtemplateが表示されればOKです。

# Microsoft Entra 外部 IDで Google フェデレーションを構成する
EntraIDの管理画面にアクセスします。

https://entra.microsoft.com/

External Identities > すべての ID プロバイダーからIDプロバイダーにGoogleを追加しにいきます。
![](https://storage.googleapis.com/zenn-user-upload/10b3de19ac7d-20241116.png)

クライアントIDとクライアントシークレットを記載と書かれているので、今からこれらを入手しにGoogle Developer Consoleへアクセスします。
![](https://storage.googleapis.com/zenn-user-upload/5e5efa818aeb-20241116.png)


まずは、Google Developer Consoleへアクセスします。
https://console.cloud.google.com/apis

GCPで新しいプロジェクトを作成してください。
![](https://storage.googleapis.com/zenn-user-upload/96bdd008f185-20241116.png)

new projectを作成します。
![](https://storage.googleapis.com/zenn-user-upload/2f4273ec1301-20241116.png)

projectが作成出来ると、ダッシュボードはこんな感じになります
![](https://storage.googleapis.com/zenn-user-upload/2e75c3e8ebd3-20241116.png)

OAuth同意画面でUser Typeを外部に設定し作成します。
![](https://storage.googleapis.com/zenn-user-upload/1d616c2a8d27-20241116.png)

[OAuth 同意画面] の [アプリ情報] で、アプリケーションの [名前] を入力します。
[ユーザー サポートのメール] のアドレスを選択します。
[認可済みドメイン] セクションで、[ドメインの追加] を選択し、ciamlogin.com と microsoftonline.com を追加します。
![](https://storage.googleapis.com/zenn-user-upload/659c5ed006c5-20241116.png)

認証情報から認証情報作成をクリック
![](https://storage.googleapis.com/zenn-user-upload/63d3ba5e69a5-20241116.png)

OAuth クライアントIDを選択
![](https://storage.googleapis.com/zenn-user-upload/b0345398846d-20241116.png)

URLをいっぱい追加します。
![](https://storage.googleapis.com/zenn-user-upload/957f0941cf42-20241116.png)

追加するURLは以下です。
- https://login.microsoftonline.com
- https://login.microsoftonline.com/te/<tenant-ID>/oauth2/authresp
- https://login.microsoftonline.com/te/<tenant-subdomain>.onmicrosoft.com/oauth2/authresp
- https://<tenant-ID>.ciamlogin.com/<tenant-ID>/federation/oidc/accounts.google.com
- https://<tenant-ID>.ciamlogin.com/<tenant-subdomain>.onmicrosoft.com/federation/oidc/accounts.google.com
- https://<tenant-subdomain>.ciamlogin.com/<tenant-ID>/federation/oauth2
- https://<tenant-subdomain>.ciamlogin.com/<tenant-subdomain>.onmicrosoft.com/federation/oauth2

tenantIDとtenant-subdomainはEntraIDの管理画面にアクセスして確認してください。

これでようやくクライアントIDとクライアントシークレット取得することが出来ました。
![](https://storage.googleapis.com/zenn-user-upload/0afdf6a40adb-20241116.png)

EntraIDのポータルに戻り、クライアントIDとクライアントシークレットを入力します。
![](https://storage.googleapis.com/zenn-user-upload/5cc57c927734-20241116.png)

IDプロバイダーより、Googleが構成済みになればOKです。
![](https://storage.googleapis.com/zenn-user-upload/88b4ff87f13d-20241116.png)


# Microsoft Entra ID でユーザーフローにGoogleIDプロバイダーを追加する

Entra管理センターから、Googleログインする為の新しいユーザーフローを作成します。
![](https://storage.googleapis.com/zenn-user-upload/18d6e9eb697e-20241116.png)

ユーザーフロー追加してください。
IDプロバイダーはGoogleを選択です。
![](https://storage.googleapis.com/zenn-user-upload/923660ce7cef-20241116.png)

これでユーザーフローが作成できました。
![](https://storage.googleapis.com/zenn-user-upload/f2f05451242c-20241116.png)

次はアプリケーションの作成です。
![](https://storage.googleapis.com/zenn-user-upload/6f8f8481dd02-20241116.png)

そして、先ほど作成したユーザーフローにアプリケーションを紐付けます。
![](https://storage.googleapis.com/zenn-user-upload/6f9e88ba1c27-20241116.png)

これで準備完了です、次は実装に移ります。

# Next.jsの実装

VSCodeより、Next.jsのプロジェクトを開きます。
msal-reactをインストールします。
```bash
npm install @azure/msal-react
```

それぞれ実装していきます。
```ts:src/app/layout.tsx
import { Providers } from '@/components/Providers';
import { Inter } from 'next/font/google';
import './globals.css';

const inter = Inter({ subsets: ['latin'] });

export const metadata = {
  title: 'External ID',
  description: 'Generated by create next app',
};

export default function RootLayout({
  children,
}: {
  children: React.ReactNode;
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

```ts:src/app/page.tsx
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

```ts:src/hooks/useCurrentUser.ts
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
  console.log("🚀 ~ useCurrentUser ~ accounts:", accounts)
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

```ts:src/components/Providers.tsx
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

```ts:src/lib/msalConfig.ts
export const msalConfig = {
  auth: {
    clientId: 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx', // Azure ポータルで取得したクライアントID
    authority:
      'https://login.microsoftonline.com/xxxx-xxxx-xxxxx-xxxx-xxxxxxxx', // テナントID
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

これにて実装は完了です。

# 動作確認

では実行してみましょう。
```bash
npm run dev
```

ものすごくシンプルな画面が表示されます。
![](https://storage.googleapis.com/zenn-user-upload/d340eb05a631-20241116.png)

Loginというボタンをクリックしてみてください。


GoogleSignInが出来そうなボタンが表示されます。
![](https://storage.googleapis.com/zenn-user-upload/b875fcdaecee-20241116.png)


SignInを進めると、ログインが無事成功されます。
![](https://storage.googleapis.com/zenn-user-upload/81db085120a1-20241116.png)


これでMicrosoft EntraIDでGoogleログインが実装されました。
お疲れ様でした👋

# 参考文献
https://learn.microsoft.com/ja-jp/entra/external-id/google-federation

https://learn.microsoft.com/ja-jp/entra/external-id/customers/how-to-google-federation-customers