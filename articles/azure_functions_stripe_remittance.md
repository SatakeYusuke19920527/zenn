---
title: "【Azure Functions ✖️ StripeAPI】Azure FunctionsとStripeでpaypayのような送金処理を実現する"
emoji: "⚡️"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["Azure", "Azure Functions", "Stripe", "送金"]
published: true
---

# Azure FunctionsとStripeで送金処理を実装する

この記事では、Azure FunctionsとStripeを用いて送金処理を実装する方法を紹介します。
StripeAPI を使った山分けの送金処理（ 顧客：送金先 = １：n ）の実装方法となります。

ストライプのDashboardは以下を使用します。
https://dashboard.stripe.com/dashboard


# 決済実装方法概要

StripeAPIとAzure Functionsを用いて、以下の流れで送金処理を実装します。
全体概要を図で表すと以下のようなイメージです。
![Img](/images/azure_functions_stripe_remittance/img1.png)

- Customer
  - ユーザを顧客として登録します。このCustomerには決済用のクレジットカードの情報が紐づきます。

- Platform
  - この箇所が今回Azure Functionsで実現するものとなります。

- Connected Accounts
  - こちらが送金先のアカウント情報となります。銀行口座の情報をユーザに紐づけます。

実装する流れとしては以下となります。

1. 顧客の作成
2. カード情報をユーザへ紐づける
3. 送金先アカウントを作成
4. 送金先アカウントの本人確認
5. 本人確認されたか確認
6. 決済を作成
7. 送金を作成


それでは、実装してみたいと思います！

## Azure Functions 環境構築
以下の記事を参照し、Azure Functionsの環境構築を行ってください。

https://zenn.dev/yusu29/articles/azure_functions_introduction

## 0. 共通部分の作成
src/helper/stripe.tsを作成し、StripeのAPIキーを設定します。

```typescript:stripe.ts
export const stripe = require('stripe')(
  'sk_xxxxxxxxxxxxxxxxxx', // StripeDashboardよりシークレットキーを取得してください。
  {
    typescript: true,
  }
);

```

## 1. 顧客の作成

```typescript:createCustomer.ts
import { app, HttpRequest, HttpResponseInit, InvocationContext } from "@azure/functions";
import { stripe } from '../helper/stripe';
/**
 * 1.顧客の作成
 */
export async function CreateCustomer(request: HttpRequest, context: InvocationContext): Promise<HttpResponseInit> {
    
    const customer = await stripe.customers.create({
      name: "test user 1",
      email: "test@test.com",
      description: "This is test user 1",
    });
    return customer;
};

app.http('CreateCustomer', {
  methods: ['GET', 'POST'],
  authLevel: 'anonymous',
  handler: CreateCustomer,
});
```
上記、関数を実行すると、cusから始まるカスタマーIDが作成されます：`cus_xxxxxxxxxxxxxxxxx`

![Img](/images/azure_functions_stripe_remittance/img2.png)
![Img](/images/azure_functions_stripe_remittance/img3.png)

## 2. カード情報をユーザへ紐づける

次は1で作成したユーザにクレジットカードの情報を紐づけます。

```typescript
import { app, HttpRequest, HttpResponseInit, InvocationContext } from "@azure/functions";
import { stripe } from '../helper/stripe';

/**
 * カード情報の型を指定
 */
export type CardType = {
  number: string;
  exp_month: number;
  exp_year: number;
  cvc: string;
};

/**
 * クレジットカードのtoken作成
 * @param card
 * @returns
 */
const createCardToken = async (card: CardType) => {
  const token = await stripe.tokens.create({ card });
  return token.id;
};

/**
 * 2.カード情報をユーザへ紐づける
 */
export async function createCardInfo(
  request: HttpRequest,
  context: InvocationContext
): Promise<HttpResponseInit> {
  const customerId = 'cus_QWaaItV1HNFSfo'; // 先ほど作成した顧客情報のカスタマーIDを指定
  const cardInfo: CardType = {
    number: '4242424242424242',
    exp_month: 11,
    exp_year: 2027,
    cvc: '123',
  }; // Test環境なのでdummyのカード情報を指定
  const cardToken = await createCardToken(cardInfo);
  return stripe.customers.createSource(customerId, { source: cardToken });
};

app.http('createCardInfo', {
  methods: ['GET', 'POST'],
  authLevel: 'anonymous',
  handler: createCardInfo,
});
```
上記の関数を実行すると、以下のように顧客にカード情報が紐づけられます。
![Img](/images/azure_functions_stripe_remittance/img4.png)


## 3. 送金先アカウントを作成

送金を受けるユーザアカウントを作成します。

```typescript:createConnectAccount.ts
import { app, HttpRequest, HttpResponseInit, InvocationContext } from "@azure/functions";
import { stripe } from '../helper/stripe';

/**
 * 3.送金先アカウントの作成
 */
export async function CreateConnectAccount(
  request: HttpRequest,
  context: InvocationContext
): Promise<HttpResponseInit> {
  const uuid = require('uuid');
  const idempotencyKey = uuid.v4();

  const account = await stripe.accounts.create(
    {
      country: 'JP',
      type: 'express',
      email: "test@test.com",
      capabilities: {
        card_payments: {
          requested: true,
        },
        transfers: {
          requested: true,
        },
      },
      business_type: 'individual',
      business_profile: {
        mcc: '5734',
        name: "testuser1",
        url: 'https://proaca.vercel.app/',
        product_description: 'テストユーザです。',
      },
    },
    {
      idempotencyKey: idempotencyKey,
    }
  );

  return account;
};

app.http('CreateConnectAccount', {
  methods: ['GET', 'POST'],
  authLevel: 'anonymous',
  handler: CreateConnectAccount,
});
```

上記を実行すると、StripeのDashboardの連結アカウントより、アカウントが追加されます。
![Img](/images/azure_functions_stripe_remittance/img5.png)

アカウントは作成されましたが、まだ本人確認が完了していないので、次のステップで本人確認を行います。

## 4.送金先アカウントの本人確認

本人確認 + 銀行口座の認証を実施します。
認証にはStripeより提供される認証のURLを使用します。

```typescript:createAccountLinks.ts
import { app, HttpRequest, HttpResponseInit, InvocationContext } from "@azure/functions";
import { stripe } from '../helper/stripe';

/**
 * 4.送金先アカウントの本人確認
 */
export async function CreateAccountLinks(
  request: HttpRequest,
  context: InvocationContext
): Promise<HttpResponseInit> {
  const stcuid = 'acct_xxxxxxxxxxxxxx'; // 送金先アカウントのIDを指定

  const accountLink = await stripe.accountLinks.create({
    account: stcuid,
    refresh_url: 'https://xxxxx.com/refresh',
    return_url: 'https://xxxxx.com/return',
    type: 'account_onboarding',
  });
  console.log("🚀 ~ accountLink:", accountLink)

  return accountLink;
};

app.http('CreateAccountLinks', {
  methods: ['GET', 'POST'],
  authLevel: 'anonymous',
  handler: CreateAccountLinks,
});
```

上記関数を実行すると以下のようなURLがreturnされます。
![Img](/images/azure_functions_stripe_remittance/img6.png)

以下のreturn されたURLを開くと、本人確認の画面が表示されます。
https://connect.stripe.com/setup/e/acct_1PerK5CxK1pdnAec/xxxxxxx

![Img](/images/azure_functions_stripe_remittance/img7.png)

こちらの画面から本人確認を進めていくと、送金先の銀行口座の登録などを求められるので、そのままぽちぽち登録していきます。
![Img](/images/azure_functions_stripe_remittance/img8.png)

連携アカウントの本人確認が完了すると、ステータスが```制限あり```から```完了```へと変化します。
![Img](/images/azure_functions_stripe_remittance/img5.png)
![Img](/images/azure_functions_stripe_remittance/img9.png)

これで送金先に無事口座情報を紐づけることが出来、本人確認も完了した状態になります。

## 5. 決済を作成

次に決済を作成します。

```typescript:createCharge.ts
import { app, HttpRequest, HttpResponseInit, InvocationContext } from "@azure/functions";
import { stripe } from '../helper/stripe';
/**
 * 5. 決済を作成
 */
export async function CreateCharge(
  request: HttpRequest,
  context: InvocationContext
): Promise<HttpResponseInit> {
  const uuid = require('uuid');
  const idempotencyKey = uuid.v4();
  const customer = "cus_xxxxxxxxxxx"; // 1. で作成した顧客のIDを指定
  const amount = 123456; // 支払い総額
  const transfer_group = 123456; // 支払い総額
  return stripe.charges.create(
    {
      customer: customer,
      amount: amount,
      currency: 'jpy',
      transfer_group,
    },
    {
      idempotencyKey: idempotencyKey,
    }
  );
};

app.http('CreateCharge', {
  methods: ['GET', 'POST'],
  authLevel: 'anonymous',
  handler: CreateCharge,
});
```

ch_xxxx という支払いIDが作成されます。
![Img](/images/azure_functions_stripe_remittance/img11.png)

## 6. 送金を作成

最後に送金を作成します。
5. で作成した支払いIDを、3. で作成した送金先アカウントを指定して送金します。
今回は20,000円を送金してあげたいと思います。

```typescript:createTransfer.ts
export const createTransfer = regionFunctions.https.onCall(
  async (data, context) => {
    const idempotencyKey = uuid.v4();
    const chargeId = 'ch_xxxxxxxxxxxxxxxxxxx';
    await stripe.transfers.create(
      {
        amount: 20000,
        currency: 'jpy',
        destination: 'acct_xxxxxxxxxxxxxx',
        source_transaction: chargeId,
      },
      {
        idempotencyKey: idempotencyKey,
      }
    );
  }
);
```
指定した送金先アカウントへ20,000円が送金されていることがわかります。
![Img](/images/azure_functions_stripe_remittance/img12.png)

これで送金処理が完了しました。
実装お疲れ様でした。

## FYI -- 本番環境で送金が日数としてどの程度かかるか

私の環境で、実際に本番環境での送金が銀行口座に入金されるまでの日数を計測しました。

送金: 6/4
銀行口座へ反映: 6/16

送金は2週間程度かかるもようです。
今後、より素早く送金していただけることを期待です。

また、送金にかかるStripe側へ支払う手数料は3.6%(2024/7月時点)でした。
![Img](/images/azure_functions_stripe_remittance/img10.png)

こちらは手数料がもっと安くなることを期待です。

## まとめ
この記事では、Azure FunctionsとStripeを使って送金処理を実装する方法を紹介しました。
主な手順としては、以下の流れとなります。

1. 顧客の作成
2. クレジットカード情報の紐付け
3. 送金先アカウントの作成および本人確認
4. 決済
5. 送金

StripeのAPIを活用し、比較的簡単に送金処理が実装できることがわかりました。
送金ロジックが実装出来れば、あらゆるアプリケーションでのマネタイズに活用できると思います。

この記事が皆様の参考になれば幸いです！

それでは！

## 参考資料
- Payments  
https://stripe.com/docs/payments

- 【StripeAPI】複数の送金先への山分けを実装する  
https://zenn.dev/mamushi/articles/multiple_transfer

- Stripe Connect  
https://stripe.com/docs/connect

- Stripe Connect | 自社プラットフォームに決済インフラを導入  
https://stripe.com/ja-us/connect

- Creating separate charges and transfers  
https://stripe.com/docs/connect/charges-transfers

- Stripe API reference – curl  
https://stripe.com/docs/api

- Connect Onboarding for Custom accounts  
https://stripe.com/docs/connect/connect-onboarding