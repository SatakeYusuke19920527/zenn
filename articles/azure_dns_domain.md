---
title: '自分のサイトに独自のドメイン名をつけよう🌏'
emoji: '⚠️'
type: 'tech' # tech: 技術記事 / idea: アイデア
topics: ['Azure', 'DNS', 'Next.js', 'DevOps', 'StaticWebApps']
published: true
---

# 技術ブログを作った時に思ったこと

最近、技術ブログを作ってみました。
[tech blog](https://zenn.dev/yusu29/articles/azuredevops_staticapps_next)

でも、何か物足りない...
それはなにだろう...

(ﾟﾛﾟ;)

ドメイン名デフォルトのままやった...

`red-pebble-032ae8200.3.azurestaticapps.net`

ということで今回は上記のドメインをわかりやすい文字列に変えてみようと思います。

# では、Let's ハンズオン！

## 目次

1. 事前のご準備
2. お名前.com でのドメイン購入
3. Azure DNS の構築
4. Azure Static Web Apps へドメインの適用
5. 動作確認

# 事前のご準備

WEB サイトは事前に以下の手順で構築してみてください。
[tech blog](https://zenn.dev/yusu29/articles/azuredevops_staticapps_next)

# お名前.com でのドメイン購入

まずは何がともあれ自分のドメインが必要なので、ドメインを購入しましょう。
私は`hello-bnm.cloud`というドメインをお名前.com 購入してみました。

[お名前.com](https://www.onamae.com/)

200 円ぐらいだったと思います。

もうほんとにとにかく安くで大丈夫です。
そして、他に色々購入しますか？とか聞かれますが、ドメイン購入だけで OK です。

購入できたらお名前.com の管理画面へ移動し、利用ドメイン一覧を表示してください。

![お名前.com](/images/azure_dns_domain/onamae.png)

ドメイン一覧に先ほど購入したドメインが表示されていれば OK です。

# Azure DNS の構築

では、DNS サーバは Azure へ構築します。
Azure portal へログインして DNS と調べてください。
そうすると、DNS ゾーンと表示されるので、そこをクリックして移動です。

![AzureDNS](/images/azure_dns_domain/azuredns.png)

DNS ゾーンの作成から DNS サーバを作成します。

リソースグループは任意のグループを選択してください。

ここ注意なのですが、

> インスタンスの詳細の名前の箇所は先ほど購入したドメイン名としてください。

筆者はここでしっかりハマりました。

![makeDNS](/images/azure_dns_domain/makedns.png)

後は作成をして、DNS ゾーンを作成してください。

こんな感じで DNS ゾーンができれば OK です。

![makeDNS](/images/azure_dns_domain/dnszone.png)

## お名前.com で購入したドメインのネームサーバの変更

ここで、お名前.com で購入したドメインのネームサーバを Azure DNS へ向くように設定します。

Azure DNS へ移動してネームサーバ 1~4 までの文字列をコピーしておいてください。

お名前.com へ移動します。

ドメイン設定のネームサーバの変更より、他のネームサーバを仕様をチェック。
1~4 の箇所へ先ほどコピーしたネームサーバ情報を入力してください。

![makeDNS](/images/azure_dns_domain/nameserver.png)

Azure からコピーしたネームサーバの情報には最後にピリオドがついているので、削除してあげてください。

![makeDNS](/images/azure_dns_domain/settingnameserver.png)

こんな感じの画面が出てきたら設定するをクリックして完了です。

![settingDNS](/images/azure_dns_domain/dnssetting.png)

お名前.com での作業は以上となります。
お疲れ様でした。

# Azure Static Web Apps へドメインの適用

では、AzureDNS に戻り作業を続きを実施します。

作業としては、cname と txt レコードを追加します。
レコードセットをクリックしてください。
![settingDNS](/images/azure_dns_domain/recordset.png)

名前：www
種類：CNAME
エイリアス：WEB サイトの URL

OK をクリックすると、CNAME のレコードが追加されます。

次に Azure Static Web Apps へ移動し、メニュー項目`カスタムドメイン`をクリック
![azure static web apps](/images/azure_dns_domain/azurestaticwebapps.png)

`＋追加`より Azure DNS へカスタムドメインを追加するを選択
![custom domain](/images/azure_dns_domain/customdomain.png)

こんな感じで検証済みと表示されれば OK です！
![custom domain](/images/azure_dns_domain/finish.png)

# 動作確認

では、アクセスしてみましょう。

[https://www.hello-bnm.cloud/](https://www.hello-bnm.cloud/)

アクセスできましたか？
![tech blog](/images/azure_dns_domain/techblog.png)

これで独自ドメイン名をつけた自分の WEB サイトの完成です！

お疲れ様でした。

`fin`
