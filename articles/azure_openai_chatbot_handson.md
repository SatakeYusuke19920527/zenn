---
title: 'Azure OpenAI ✖️ LINE MessagingAPI を使って自分だけのAIチャットボットを作ろう！'
emoji: '😎'
type: 'tech' # tech: 技術記事 / idea: アイデア
topics: ['Azure', 'botservice', 'openai', 'line', 'TypeScript']
published: true
---

# 目次

1. はじめに
2. 今回作成するシステムの概要
3. リソースグループの作成
4. Azure OpenAI セットアップ
5. Azure Bot Service のセットアップ
6. App Service のセットアップ
7. App ServiceへDeploy
8. LINE MessagingAPIの設定
9. 動作確認
10. お片付け

## はじめに

![Azure logo](/images/azure_openai_handson/azureopenai_logo.png)

昨今ちまたで話題の OpenAI。AIと普段使ってるサービスを連携させて面白いものを作りたいなぁと考えるエンジニアの同志達に向けたハンズオン 🖐️ となります。
この記事の目標としては、LINE botにメッセージを送信すると、AIが返信をくれるサービスをリリースすることです。
セットアップで詰まるところはどんどんコメント欄に質問していただいたら、がんがん返していきますので、ご遠慮なく質問してください！

### では、Let's ハンズオン！

## 今回作成するシステムの概要

今回作成するシステムは Azure 上で作成します。
下の図をご覧ください。

![Azure](/images/azure_openai_chatbot_handson/zentai.png)

以下の様な技術を使います。

- Azure OpenAI
- Azure App Service
- Azure Bot Service
- Managed ID
- LINE MessagingAPI
- javascript

工夫次第でめちゃくちゃ面白いものが作れる予感...！！
みなさん頑張っていきましょう！

## 前提条件
以下のインストールおよびサブスクリプションの契約が必要となります。
- Node.js
  - https://nodejs.org/en/blog/release/v16.20.2
  - Node.js v16.20.2
  - npm v8.19.4

- Bot Framework Emulator
  - https://github.com/Microsoft/BotFramework-Emulator/releases/tag/v4.14.1

- Azure
  - https://azure.microsoft.com/ja-jp/pricing/purchase-options/pay-as-you-go

## リソースグループの作成
まずは何がともあれリソースグループの作成です。
全体図で言うとここの部分です。
![Azure](/images/azure_openai_chatbot_handson/rg.png)

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

## Azure OpenAI セットアップ

次に Azure OpenAI のセットアップです。全体図で言うとここの部分です。
![Azure dashboard](/images/azure_openai_chatbot_handson/ai.png)

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

:::message
初回はこのような表示が出ている可能性があります。

![Azure](/images/azure_openai_handson/aoai_error.png)

その場合は表示されているリンクをクリックすると、Microsoft へリクエストを送ることができます。

![Azure](/images/azure_openai_handson/request_access.png)

ゆうじろうは数時間ぐらいで許可されたので、気長に待ちましょう。

:::

## Azure Bot Service のセットアップ

次は Azure Bot Service をセットアップしていきます。
全体図で言うとここです。

![Azure](/images/azure_openai_chatbot_handson/bot.png)

まずは ローカルでJavaScriptで少しだけコーディングしてみます。
お好きなディレクトリに移動して、以下のコマンドを実行してください。

> 私は```tmp```フォルダで作業することにします。
![Azure](/images/azure_openai_chatbot_handson/tmp.png)

```bash
npm init -y
```

package.json が作成されたら、以下のコマンドを実行してください。

```bash
npm install --save-dev yo generator-botbuilder
```

次にスケルトンの作成をしていきます。

```bash
npx yo botbuilder
```

上記コマンドを実行すると、ウィザードが上がってきます。

```powershell
PS C:\Users\yusuke\dev\03.azure\tmp> npx yo botbuilder

Welcome to the Microsoft Bot Builder generator v4.17.0.

Detailed documentation can be found at https://aka.ms/botbuilder-generator

? What's the name of your bot? chatbot
? What will your bot do? Demonstrate the core capabilities of the Microsoft Bot Framework
? What programming language do you want to use? JavaScript
? Which template would you like to start with? Echo Bot - https://aka.ms/bot-template-echo
? Looking good.  Shall I go ahead and create your new bot? Yes

...

Run `npm audit` for details.
------------------------
 Your new bot is ready!
------------------------
Open the README.md to learn how to run your bot.
Thank you for using the Microsoft Bot Framework.

```

上記のような表示がされればOKです！

そして今回使用するのはchatbotだけなので、残りのフォルダは削除してください。

![Azure](/images/azure_openai_chatbot_handson/chatbot.png)

一旦ターミナルやpowershellでchatbotへ入り、以下のコマンドを実行です。

```bash
npm install
```
問題なく終了すれば、以下のコマンドでVSCodeを開きます。

```bash
code .
```

デバッグ実行したいので、VSCodeの左側のデバッグをクリックしてください。
そこでlaunch.jsonを作成します。をクリック。
![Azure](/images/azure_openai_chatbot_handson/vscode.png)

デバッガーの選択では```Node.js```を選択してください。

launch.jsonは以下のように記載してください。

```json
{
  // IntelliSense を使用して利用可能な属性を学べます。
  // 既存の属性の説明をホバーして表示します。
  // 詳細情報は次を確認してください: https://go.microsoft.com/fwlink/?linkid=830387
  "version": "0.2.0",
  "configurations": [
    {
      "type": "node",
      "request": "launch",
      "name": "プログラムの起動",
      "skipFiles": [
        "<node_internals>/**"
      ],
      "program": "${workspaceFolder}/index.js",
      "envFile": "${workspaceFolder}/.env"
    }
  ]
}
```
ここまで出来たらセーブして、F5を押してください。
デバッグ実行が開始されます。
デバッグコンソールへ以下の表示がされればOKです。(これからlocalhost:3978へアクセスしに行きます。)
![Azure](/images/azure_openai_chatbot_handson/debugconsole.png)

次にBot Framework Emulatorを使って動作確認していきます。

Bot Framework Emulatorのインストールは[こちら](https://github.com/Microsoft/BotFramework-Emulator/releases/tag/v4.14.1)

- MACの方はこちら
  - BotFramework-Emulator-4.14.1-mac.dmg

- Windowsの方はこちら
  - BotFramework-Emulator-4.14.1-windows-setup.exe

以下のようなエミュレータが起動出来ればOKです。

![Azure](/images/azure_openai_chatbot_handson/botemulator.png)

Open Botを開き、以下のように入力してください。

![Azure](/images/azure_openai_chatbot_handson/openbot.png)

Connectをクリックして、以下のような表示になったでしょうか。
Hello and Welcome!と表示されればOKです。

![Azure](/images/azure_openai_chatbot_handson/botemu.png)

今のところはオウム返ししてくるBotになります。

![Azure](/images/azure_openai_chatbot_handson/oumu.png)


これをOpenAIに接続し、AIと会話出来るチャットボットにするためにbot.jsを以下のコードに差し替えてください。

```javascript:bot.js
/* eslint-disable */
const { ActivityHandler, MessageFactory } = require('botbuilder');
const axios = require('axios');

const OPENAI_RESOURCE = process.env.OPENAI_RESOURCE;
const OPENAI_DEPLOYMENT = process.env.OPENAI_DEPLOYMENT;
const OPENAI_API_VERSION = process.env.OPENAI_API_VERSION;
const OPENAI_API_KEY = process.env.OPENAI_API_KEY;
const OPENAI_COMPLETION_URL = `https://${OPENAI_RESOURCE}.openai.azure.com/openai/deployments/${OPENAI_DEPLOYMENT}/chat/completions?api-version=${OPENAI_API_VERSION}`;

var getCompletion = async function (text) {
  var data = {
    messages: [
      {
        role: 'user',
        content: text,
      },
    ],
  };
  var res = await axios({
    method: 'post',
    url: OPENAI_COMPLETION_URL,
    headers: {
      'Content-Type': 'application/json',
      'api-key': OPENAI_API_KEY,
    },
    data,
  });
  return (res.data.choices[0] || []).message?.content;
};

class EchoBot extends ActivityHandler {
  constructor() {
    super();
    this.onMessage(async (context, next) => {
      try {
        const replyText = await getCompletion(context.activity.text);
        await context.sendActivity(replyText);
      } catch (error) {
        console.log(
          '🚀 ~ file: bot.js:43 ~ EchoBot ~ this.onMessage ~ error:',
          error.message
        );
      }
      await next();
    });

    this.onMembersAdded(async (context, next) => {
      const membersAdded = context.activity.membersAdded;
      const welcomeText = 'Hello and welcome!';
      for (let cnt = 0; cnt < membersAdded.length; ++cnt) {
        if (membersAdded[cnt].id !== context.activity.recipient.id) {
          await context.sendActivity(
            MessageFactory.text(welcomeText, welcomeText)
          );
        }
      }
      // By calling next() you ensure that the next BotHandler is run.
      await next();
    });
  }
}

module.exports.EchoBot = EchoBot;

```

次に.envファイルを修正します。

```env:.env
MicrosoftAppType=
MicrosoftAppId=
MicrosoftAppPassword=
MicrosoftAppTenantId=
OPENAI_RESOURCE=xxxxxxxxxxxxxxxxx
OPENAI_DEPLOYMENT=xxxxxxxxxxxxxxxxx
OPENAI_API_VERSION=2023-03-15-preview
OPENAI_API_KEY=xxxxxxxxxxxxxxxxx
```

それぞれの値の確認方法は以下です。
- OPENAI_RESOURCE
  - Azureポータルより、Azure OpenAIのリソース名を参照
- OPENAI_DEPLOYMENT
  - Azure OpenAI Studioのデプロイの箇所より、デプロイ名を参照
- OPENAI_API_VERSION
  - 2023-03-15-preview と入力
- OPENAI_API_KEY
  - AzureポータルのAzure OpenAIのキーとエンドポイントの箇所より、キー 1 を参照

これで再度実行してみましょう！
以下のようにAIが返信してくれるはずです。

![Azure](/images/azure_openai_chatbot_handson/aireturn.png)

ここまで出来たら、次にAzureにてAzure Bot Serviceを作成します。

![Azure](/images/azure_openai_chatbot_handson/botservice.png)

Bot Service → 作成をクリックしてください。

```Azure Bot```というサービスが表示されるので、選択し作成をクリック。(かなり下の方です。)
![Azure](/images/azure_openai_chatbot_handson/azurebot.png)


作成画面ではボットハンドルとリソースグループを指定してください。

![Azure](/images/azure_openai_chatbot_handson/makebot.png)

もう少し下のほうにMicrosoft App ID (Managed ID)の作成項目があります。

アプリの種類を```ユーザ割り当て済みマネージドID```へ変更してください。

こちらの設定は以上です。
確認と作成をクリックして、BotServiceを作成してください。

今のところはリソースグループを確認すると３つ出来ていれば順調です。

![Azure](/images/azure_openai_chatbot_handson/tochu.png)

## App Service のセットアップ

ここからはBot Serviceの裏側で動作するApp Serviceを作成していきます。
全体図で言うとここです。

![Azure](/images/azure_openai_chatbot_handson/appservice.png)
まずは、Azureポータルへログインし、App Serviceを検索してください。

![Azure](/images/azure_openai_chatbot_handson/appservice1.png)

App Serviceの画面へ進んだら、Webアプリ作成をクリックしてください。

![Azure](/images/azure_openai_chatbot_handson/appservice2.png)

nodeのバージョンはご自身の環境に併せて設定してください。
以下のコマンドで調べることが出来ます。
```bash
node -v
```

価格プランはBasicを選択してください。

![Azure](/images/azure_openai_chatbot_handson/appservice3.png)

デプロイ・ネットワーク・監視は初期設定のままでOK
そこまで出来たら、確認及び作成をクリックしてください。

WebAppが作成されたらOKです！

## App ServiceへDeploy

ここまでできたら後はデプロイとなります。
VSCodeを開けて、画面左側の拡張機能のAzureのアイコンをクリックし、App Serviceを選択してください。

右クリックメニューよりDeploy to Web Appを選択し、デプロイを開始してください。

![Azure](/images/azure_openai_chatbot_handson/deploy.png)

問題なくデプロイが完了したら、Application Settingの箇所を選択し、右クリックから ```Upload local Settings```を選択。
.envファイルの内容が自動でUploadされます。

これにてローカルでの作業は完了です。
次にAzureポータルへ再度アクセスしてください。
先ほどBotService作成時に生成したマネージドIDをAppServiceへ適用していきます。

Azureポータル→App Service→IDを選択

ユーザ割り当て済みのタブを選択し、ユーザ割り当て済みマネージドIDを追加をクリック。

![Azure](/images/azure_openai_chatbot_handson/managedid2.png)

先ほど作成したマネージドIDを選択し、追加をクリック

![Azure](/images/azure_openai_chatbot_handson/managedid3.png)

続いて最後に環境変数の設定です。
そのままAppServiceの構成より以下のように環境変数を設定していきます。

```
MicrosoftAppId→マネージドIDのプロパティよりクライアントIDをコピーして設定
MicrosoftAppType→UserAssignedMSI と入力
MicrosoftAppPassword→こちらは設定しなくてOK
MicrosoftAppTenantId→マネージドIDのプロパティよりテナントIDをコピーして設定
```

上記、入力出来たら、保存をクリックしてください。

## LINE MessagingAPIの設定

ここからはBot ServiceとLINEの連携を行っていきます。
全体図で言うとここです。

![Azure](/images/azure_openai_chatbot_handson/botandline1.png)

まずはApp ServiceとBot Serviceを連携させます。
Azureポータル→Bot Service→構成→メッセージングエンドポイントへAppServiceの概要の規定のドメインに記載されているURLに/api/messagesと追記して設定してください。

![Azure](/images/azure_openai_chatbot_handson/url.png)

次はBot ServiceにLINEを設定していきます。

AzureポータルよりBot Serviceを開き、チャンネルからLINEを選択
LINEのチャネルシークレットとチャネルアクセストークンを入力します。

以下サイトより、コンソールへログインし、プロバイダーを作成してください。
Messaging APIを使用します。
[LINE Developers](https://developers.line.biz/ja/)

以下のようなプロバイダーを作成してください。

![Azure](/images/azure_openai_chatbot_handson/messagingapi.png)

作成したら、チャネルの基本設定より、チャネルシークレットを、
MessagingAPIの箇所でチャネルアクセストークンを発行してください。

発行したら、Azureポータルに戻り、チャネルシークレットとチャネルアクセストークンを入力後、
Webhook の URLを生成してください。

上記で生成したURLをWebhook URLに貼り付ければOKです。

![Azure](/images/azure_openai_chatbot_handson/lc1.png)

これで設定は全て完了です。
お疲れさまでした。

## 動作確認
では、LINE botへメッセージを送信してみましょう！

![Azure](/images/azure_openai_chatbot_handson/output.png)

AIが返信してくれましたか？
ここまで出来たら完了です！
本当にお疲れさまでした。

## お片付け

最後にずっと残してて課金されないようにお片付けです。

以下、2つの操作を実行すれば OK です。

- Azure OpenAI Studio からデプロイの削除
- Azure よりリソースグループの削除

しっかりお片付けして本日は終わりです。

お疲れ様でした。

## 最後に
なかなか盛りだくさんの内容なので、詰まっちゃった方はぜひコメント欄にてどしどし質問してくださいね！