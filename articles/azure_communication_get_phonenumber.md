---
title: "Azure Communication Serviceで電話番号を取得しよう"
emoji: "📞"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["Azure","AzureCommunicationService","電話番号", "コールセンター"]
published: true
---

# 目次

1. はじめに
2. Azure Communication Serviceの概要
3. Azure Communication Serviceセットアップ
4. いざ、電話番号の取得
5. お疲れ様でした

# はじめに
![AzureCommunicationSservice](/images/azure_communication_get_phonenumber/azurecommunication.png)

今回はAzure Communication Serviceで電話番号を取得する方法について記事にしてみました。

# Azure Communication Serviceの概要
Azure Communication Serviceはクラウドベースのサービスです。用意されている REST API およびクライアント ライブラリ SDK を利用することで、通信を手軽にアプリケーションに組み込むことができます。 基になる技術 (メディアのエンコードやテレフォニーなど) の専門家でなくても、アプリケーションに通信を追加できます。 Azure Communication Service は、複数の Azure 地域と 政府機関向け Azure で利用できます。

https://learn.microsoft.com/ja-jp/azure/communication-services/overview

電話番号を取得してみたいと思い、公式ドキュメントを参考にしてみましたが、日本語の情報が少なかったので、今回は日本語で記事にしてみました。

https://learn.microsoft.com/ja-jp/azure/communication-services/quickstarts/telephony/get-phone-number?tabs=windows&pivots=platform-azcli

それでは、電話番号を取得してみましょう。

# Azure Communication Serviceセットアップ
まずは、Azure Communication Serviceをセットアップします。
Azureのポータル画面へログインしてください。

次に、画面上部の検索窓から```Communication```と検索すると、```通信サービス```が表示されるのでクリックしてください。

画面左側ペインの```電話番号```メニューを選択し、```取得```をクリックしてください。

![AzureCommunicationSservice](/images/azure_communication_get_phonenumber/main.png)

# いざ、電話番号の取得

以下のような画面が表示されます。

![AzureCommunicationSservice](/images/azure_communication_get_phonenumber/main2.png)

特別注文を要求するをクリックしてください。

:::message

2024年2月の時点では、検証用の電話番号はUSリージョンでのみ対応でした。

:::



以下の手順にて電話番号を取得できます。
![AzureCommunicationSservice](/images/azure_communication_get_phonenumber/main3.png)

電話番号ポータルをクリックすると、各種情報を入力出来るフォームが表示されます。

![AzureCommunicationSservice](/images/azure_communication_get_phonenumber/main4.png)

Azure Communication Serviceを選択し、各種情報を入力してください。

```Azure Subscription ID```は使用しているAzureポータルの検索窓よりサブスクリプションと検索して、取得してください。

```Azure Immutable Resource ID```はAzure Communication Serviceのリソースへ移動し、概要→jsonビューを選択してください。
その中に```immutableResourceId```の値がありますので、その値を使用してください。

Type of Numberは```National```を選択してください。

これで残りを入力してSubmitでOKです。

すると、以下のようなメールが来ます。

![AzureCommunicationSservice](/images/azure_communication_get_phonenumber/main5.png)

添付されているWordの申請書と運転免許書を提出するようにと言われたので、大人しく提出。

ちなみにワードは以下のような感じ。

![AzureCommunicationSservice](/images/azure_communication_get_phonenumber/main6.png)

(私は一番下の著名を忘れてて全然申請通らなかったので、皆様はご注意を)

申請後、3営業日ぐらいで電話番号が発行されます。

![AzureCommunicationSservice](/images/azure_communication_get_phonenumber/main7.png)

これにて電話番号の取得が完了です！

## お疲れ様でした