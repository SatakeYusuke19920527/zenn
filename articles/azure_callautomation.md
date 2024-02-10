---
title: "OpenAIが自動応答するコールセンターを作ってみよう📲"
emoji: "📞"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["Azure","OpenAI","TypeScript", "コールセンター"]
published: true
---

# 概要
今回はAzure Communication ServiceとAzure OpenAI Serviceを使って、コールセンターを作ってみたいと思います。

全体アーキテクチャは以下のようになります。
![AzureCommunicationSservice](/images/azure_callautomation/image1.png)

全体の流れは以下の通りです。

1. ユーザーが Azure Communication Service に登録された電話番号に架電する。
2. Azure Communication Servicce の Incoming Call をトリガーとしてサーバーサイドアプリケーションの API に Webhook を実行する。
3. Webhook 要求にサーバーサイドアプリケーションが応答する。
4. ユーザー発話内容に基づいて Azure Cognitive Service が Speech-to-Text、Azure OpenAI Service からの応答内容に基づいて Text-to-Speech を実行する。
5. ユーザー発話内容に基づいて Azure OpenAI Service が回答内容を生成する。

総じて、ユーザが電話をかけると、OpenAIが回答を作成して通話越しから応答を返すという流れです。

それでは作っていきましょう。

# 目次

1. Azure Communication Serviceセットアップ
2. Azure AI Multi Serviceのセットアップ
3. Azure OpenAI Serviceのセットアップ
4. Azure DevTunnelセットアップ
5. Azure AI Multi Service と Azure Communication Services の連携
6. EventGridのセットアップ
7. 動作検証

# Azure Communication Serviceセットアップ

まずは、Azure Communication Serviceをセットアップします。
役割としては、ユーザからの電話を受け付ける役割となります。

Azure portalにリソースグループを作成後、Azure Communication Serviceを作成します。

日本語では```通信サービス```という名前ですね。

![AzureCommunicationService](/images/azure_callautomation/image2.png)

画面左上の```作成```から以下のように設定して作成してください。

![AzureCommunicationService](/images/azure_callautomation/image3.png)

作成完了したら、リソースへ移動し、電話番号を取得します。

電話番号の取得方法については以下の記事に詳細を記載しましたので、参考にしてください。

https://zenn.dev/yusu29/articles/azure_communication_get_phonenumber

# Azure AI Multi Serviceのセットアップ

次は、Azure AI Multi Serviceのセットアップです。
役割としては、ユーザの発話内容をテキストに変換すること(speech to text)と、OpenAIが生成したテキストを音声に変換すること(text to speech)です。

Azure Portalの上部の検索より、```AI services```と検索し、```Azure AI services multi-service account```を選択してください。

![AzureCommunicationService](/images/azure_callautomation/image4.png)

本記事作成時には、text to speechとspeech to textが日本リージョンでは使用できなかったので、eastusリージョンで作成しました。

![AzureCommunicationService](/images/azure_callautomation/image5.png)

ネットワークはデフォルトのままで作成してください。
Identityはシステム割り当てIDをオンにしてください。後ほど、Azure Communication Serviceとの連携で使用します。

ここまでくれば後は作成でOKです。

# Azure OpenAI Serviceのセットアップ

検索窓に`OpenAI`と入力して Azure OpenAI の画面を開いてください。

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

筆者は数時間ぐらいで許可されたので、気長に待ちましょう。

:::

# Azure DevTunnelセットアップ
Azure DevTunnels は、インターネット上でホストされているローカル Web サービスを共有可能にする Azure サービスです。 このドキュメントに記載されているコマンドを使用して、ローカル開発環境をパブリック インターネットに接続します。 これにより永続的なエンドポイント URL のあるトンネルが作成され、匿名アクセスが可能になります。 このエンドポイントを使用して、ACS Call Automation サービスからの呼び出しイベントをアプリケーションに通知します。

以下のコマンドを実行し、DevTunnelをセットアップしてください。

```bash
devtunnel create --allow-anonymous
devtunnel port create -p 8080
devtunnel host
```


# Azure AI Multi Service と Azure Communication Services の連携

Azure Communication Services と Azure AI Multi Service を連携させるためには、Azure Communication Services の Identity に Azure AI Multi Service の Identity を追加します。

Azure Portal にて、Azure Communication Services のリソースへ移動し、画面左側ペインからCognitive Serviceを選択して、```Cognitive Serviceに接続```をクリックして、Azure Communication ServiceとAzure AI Multi Serviceを連携させます。

![AzureCommunicationService](/images/azure_callautomation/image6.png)

# Event Grid のセットアップ
Azure Communication Services から Web App への通知を受け取るために、Event Grid をセットアップします。

Azure Portal にて、Azure Communication Services のリソースへ移動し、画面左側ペインからEvent Gridを選択して、```Event Grid サブスクリプションを作成```をクリックして、Event Grid をセットアップします。


画面左側ペインから、システムトピック→作成をクリック
![AzureCommunicationService](/images/azure_callautomation/image7.png)

名前やリソースを入力してEventGridのトピックを作成してください。

次にサブスクリプションの設定です。
EventGridからWebhookでWebAppへ通知を送信します。
以下の記事を参考に設定してください。
https://learn.microsoft.com/ja-jp/azure/communication-services/concepts/call-automation/incoming-call-notification

ポイントとしては以下２点です。
1. URLはAzure DevTunnelのURLを使います。ex. https://xxxx.devtunnels.ms/api/IncomingCall
2. イベントタイプは```Microsoft.Communication.IncomingCall```を選択します。
3. フィルターでキー：```data.to.PhoneNumber.Value```、演算子：```次の文字列で始まる```、値：```Azure Communication Serviceの電話番号```を設定します。

これにて設定は完了です。

# 動作検証
以下のリポジトリをcloneして、以下のコマンドを実行してください。

```bash
cd poc-callAutomation
npm install
npm run dev
```
Azure Communication Services の電話番号に架電して、OpenAIが生成した回答を答えてくれます。

これにて、コールセンターを作成する手順は完了です！

## お疲れ様でした📞

# 参考
2023年Ignite JapanですきやねんAzureのセッションにてDEMOをされていたので、それを参考にしました。
下記はC#にて実装されていたので、今回はTypeScriptで実装しました。
https://github.com/TK3214-MS/POC-Ignite2023-CallAutomation