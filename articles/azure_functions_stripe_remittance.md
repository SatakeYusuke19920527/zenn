---
title: "Azure FunctionsとStripeで送金処理を実装する"
emoji: "⚡️"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["Azure", "Azure Functions", "Stripe", "送金"]
published: false
---

# Azure FunctionsとStripeで送金処理を実装する

この記事では、Azure FunctionsとStripeを用いて送金処理を実装する方法を紹介します。


https://dashboard.stripe.com/dashboard


## 💸 Stripeドキュメント

- Payments
  - https://stripe.com/docs/payments
- 【StripeAPI】複数の送金先への山分けを実装する
  - https://zenn.dev/mamushi/articles/multiple_transfer
- Stripe Connect
  - https://stripe.com/docs/connect
- Stripe Connect | 自社プラットフォームに決済インフラを導入
  - https://stripe.com/ja-us/connect
- Creating separate charges and transfers
  - https://stripe.com/docs/connect/charges-transfers
- Stripe API reference – curl
  - https://stripe.com/docs/api
- Connect Onboarding for Custom accounts
  - https://stripe.com/docs/connect/connect-onboarding

## 決済実装方法

### 顧客の作成

    カスタマーIDが作成される：`cus_xxxxxxxxxxxxxxxxx`
    
    呼び出し方：`bet.v1.stripe.createCustomer({})`

    ```typescript
    export const createCustomer = regionFunctions.https.onCall(
      async (data, context) => {
        console.log('🚀 ~ file: createCustomer.ts:10 ~ data:', data);
        const customer = await stripe.customers.create({
          description: 'satake',
        });
        return customer;
      }
    );
    ```

### カード情報をユーザへ紐づける

    1で作成したユーザにクレジットカードの情報を紐づける
    
    呼び出し方：`bet.v1.stripe.createCardInfo({customerId: "cus_xxxxxxxxxxxxxxxxx", cardInfo: { number: '4242424242424242', exp_month: 5, exp_year: 2024, cvc: '314'}})`

    ```typescript
    export const createCardInfo = regionFunctions.https.onCall(
      async (data, context) => {
        const customerId = data.customerId;
        const cardToken = await createCardToken(data.cardInfo);
        return stripe.customers.createSource(customerId, { source: cardToken });
      }
    );

    const createCardToken = async (card: CardType) => {
      const token = await stripe.tokens.create({ card });
      return token.id;
    };
    ```

### 送金先アカウントを作成

    送金を受けるユーザを作成する
    
    呼び出し方：`bet.v1.stripe.createConnectAccount({})`

    ```typescript
    export const createConnectAccount = regionFunctions.https.onCall(
      async (data, context) => {
        const idempotencyKey = uuid.v4();

        const account = await stripe.accounts.create(
          {
            country: 'JP',
            type: 'express',
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
              url: 'https://example.com',
            },
          },
          {
            idempotencyKey: idempotencyKey,
          }
        );

        return account;
      }
    );
    ```

### 送金先アカウントの本人確認

    本人確認 + 銀行口座の認証。リンクを取得して認証してもらう
    
    呼び出し方：`bet.v1.stripe.createAccountLinks({})`

    ```typescript
    export const createAccountLinks = regionFunctions.https.onCall(
      async (data, context) => {
        const accountLink = await stripe.accountLinks.create({
          account: 'acct_xxxxxxxxxxxxxxxx',
          refresh_url: 'https://bet-webapp.vercel.app/app',
          return_url: 'https://bet-webapp.vercel.app/app',
          type: 'account_onboarding',
        });

        return accountLink;
      }
    );
    ```

    以下はレスポンスの例です：

    ```json
    {
      "result": {
        "object": "account_link",
        "created": 1684299388,
        "expires_at": 1684299688,
        "url": "https://connect.stripe.com/setup/e/acct_xxxxxxxxxxxxxxxx/dVovPX9TnUaq"
      }
    }
    ```

5. ### 本人確認されたか確認

    呼び出し方：`bet.v1.stripe.createAccountRetrieve({})`
    
    認証済みかどうかを確認する方法は要検討

    ```typescript
    export const createAccountRetrieve = regionFunctions.https.onCall(
      async (data, context) => {
        const account = await stripe.accounts.retrieve('acct_xxxxxxxxxxxxxxxx');
        return account;
      }
    );
    ```

    認証済みの場合のレスポンス：

    ```json
    {
      "result": {
        // 認証済みの詳細情報
      }
    }
    ```

    認証まだの場合のレスポンス：

    ```json
    {
      "result": {
        // 認証されていない詳細情報
      }
    }
    ```

6. ### 決済を作成

    呼び出し方：`bet.v1.stripe.createCharge({})`

    ```typescript
    export const createCharge = regionFunctions.https.onCall(
      async (data, context) => {
        const idempotencyKey = uuid.v4();
        const customer = 'cus_xxxxxxxxxxxxxx'; // 顧客
        const amount = 15000; // 支払い総額
        return stripe.charges.create(
          {
            customer: customer,
            amount: amount,
            currency: 'jpy',
          },
          {
            idempotencyKey: idempotencyKey,
          }
        );
      }
    );
    ```

7. ### 送金を作成

    銀行への送金とプラットフォーム側への送金方法を調査する必要があります
    
    呼び出し方：`bet.v1.stripe.createTransfer({})`

    ```typescript
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

https://stripe.com/docs/connect/collect-then-transfer-guide のサンプルが一番近いです。

    createTransferの流れ：

    ![スクリーンショット](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/a6183fa7-3af6-4644-a9d1-07edda8bba7f/%E3%82%B9%E3%82%AF%E3%83%AA%E3%83%BC%E3%83%B3%E3%82%B7%E3%83%A7%E3%83%83%E3%83%88_2023-05-24_14.07.07.png)

## 本番環境で送金が日数としてどの程度かかるか

商品購入→入金は6/29に決済完了→7/7に銀行口座へ入金予定

送金→送金開始6/4→6/16

商品購入は1週間程度、送金は2週間程度かかる計算になりました。

## 決済のロジック

1. 1時間に1回スケジュールの試合が終了しているかどうか確認
2. 終了している試合のmidを取得
3. betにかけられているmidの中に入っているdocumentIdの内容を確認
4. winnerとloserに分ける
5. winnerには手数料を抜いた掛金を送金
6. winnerがbetした額に応じて重み付けして分配
7. 送金

---

この記事が皆様の参考になれば幸いです。
