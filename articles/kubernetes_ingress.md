---
title: "Kubernetes Ingressをわかりやすく"
emoji: "📖"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["Kubernetes","Ingress", "負荷分散","SSL終端", "仮想ホスティング"]
published: true
---

# Kubernetes Ingress完全に理解した

ここはIngressってなんじゃいって言う感じのKubernetesを手取り早く理解した気持ちになれる場所です。

以下の公式の内容を噛み砕いて、原型をなくしてやろうと思います。
https://kubernetes.io/ja/docs/concepts/services-networking/ingress/#ingress%E3%81%A8%E3%81%AF%E4%BD%95%E3%81%8B

## では、お時間ちょっといただくやで。

# 目次
1. Ingressをざっくりと説明
2. yamlファイルの書き方
3. Ingressのルール
4. TLS化もできちゃう
5. 負荷分散もできちゃう

# Ingressをざっくりと説明

Ingressは公式が言うには、Kubernetesで作成したSeviceへ外部からのアクセス(主にHTTP)を管理するAPIオブジェクトやでって言うてます。

Serviceを外部に公開しパスベースでアクセス先となるServiceを切り替えることができます。L7層レベルの制御ができたり色々便利やでとのこと。

端的に言うと、

### httpとかhttpsでクラスターにアクセスできるようにするやつやでって言うてます

![ingress_overview](/images/kubernetes_ingress/ingress_overview.png)

主要な機能として以下。
- 負荷分散
- SSL終端
- 名前ベースの仮想ホスティングの機能

ここで公式より1点注意事項
## Ingressを使用する上での前提条件として、Ingressを提供するためにはIngressコントローラーが必要です。Ingressリソースを作成するのみでは何の効果もありません。

```
イングレス コントローラーは、リバース プロキシ、構成可能なトラフィック ルーティング、および Kubernetes サービスの TLS 終端を提供するソフトウェアです。 個別の Kubernetes サービスのイングレス ルールとルートを構成するには、Kubernetes イングレス リソースが使われます。 
```

IngressとセットでIngressコントローラが必要なんだなぁ( ͡° ͜ʖ ͡°)
ぐらいの理解でOKです！

Ingressコントローラは色々種類があるようで、以下にまとまってます。
https://kubernetes.io/ja/docs/concepts/services-networking/ingress-controllers/

有名なのは <b> ingress-nginx </b> みたいですね。



# yamlファイルの書き方

デプロイする時のyamlファイルの書き方はこんな感じ

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: minimal-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  ingressClassName: nginx-example
  rules:
  - http:
      paths:
      - path: /testpath
        pathType: Prefix
        backend:
          service:
            name: test
            port:
              number: 80
```

IngressにはapiVersion、kind、metadataやspecフィールドが必要です。

Ingressオブジェクトの名前は、有効なDNSサブドメイン名である必要があります。

Ingress Specは、ロードバランサーやプロキシーサーバーを設定するために必要な全ての情報を持っています。最も重要なものとして、外部からくる全てのリクエストに対して一致したルールのリストを含みます。<b>IngressリソースはHTTP(S)トラフィックに対してのルールのみサポートしています。</b>

ingressClassNameが省略された場合、デフォルトのIngressClassを定義する必要があります。

```
デフォルトのIngressClassを定義しなくても動作するIngressコントローラーがいくつかあります。
例えば、Ingress-NGINXコントローラーはフラグ --watch-ingress-without-classで設定できます。ただし、下記のようにデフォルトのIngressClassを指定することを推奨します。
```

ですって。

# Ingressのルール
## 使う時にはホスト名指定したり、パスを指定したり、サービス名とポート名を組み合わせてみてねって言うてます。

各HTTPルールは以下の情報を含みます。

- オプションで設定可能なホスト名。上記のリソースの例では、ホスト名が指定されていないので、そのルールは指定されたIPアドレスを経由する全てのインバウンドHTTPトラフィックに適用されます。ホスト名が指定されていると(例: foo.bar.com)、そのルールは指定されたホストに対して適用されます。

- パスのリスト(例: /testpath)。各パスにはservice.nameとservice.port.nameまたはservice.port.numberで定義されるバックエンドが関連づけられます。ロードバランサーがトラフィックを関連づけられたServiceに転送するために、外部からくるリクエストのホスト名とパスが条件と一致させる必要があります。

- バックエンドはServiceドキュメントに書かれているようなService名とポート名の組み合わせ、またはCRDによるカスタムリソースバックエンドです。Ingressで設定されたホスト名とパスのルールに一致するHTTP(とHTTPS)のリクエストは、リスト内のバックエンドに対して送信されます。
Ingressコントローラーでは、defaultBackendが設定されていることがあります。これはSpec内で指定されているパスに一致しないようなリクエストのためのバックエンドです。

さも、理解したかのような顔をして次に進みましょう。

# Ingressのタイプ
## いろんなタイプのルーティング方法があるよって言うてます。

## 単一ServiceのIngress
単一のServiceを公開する時のタイプ

## リクエストのシンプルなルーティング
/fooとか/barとかpathベースでルーティングするタイプです。
![ingress_overview](/images/kubernetes_ingress/ingress_simple.png)

# 名前ベースのバーチャルホスティング
ホスト名でルーティングしていくタイプです。
![ingress_overview](/images/kubernetes_ingress/ingress_name_base_routing.png)

# TLS化もできちゃう
## https通信もいけるよって言うてます。

```
TLSの秘密鍵と証明書を含んだSecretを指定することにより、Ingressをセキュアにできます。Ingressは単一のTLSポートである443番ポートのみサポートし、IngressでTLS終端を行うことを想定しています。IngressからServiceやPodへのトラフィックは平文です。IngressのTLS設定のセクションで異なるホストを指定すると、それらのホストはSNI TLSエクステンション(IngressコントローラーがSNIをサポートしている場合)を介して指定されたホスト名に対し、同じポート上で多重化されます。TLSのSecretはtls.crtとtls.keyというキーを含む必要があり、TLSを使用するための証明書と秘密鍵を含む値となります。
```

# 負荷分散もできちゃう
## http(s)のアクセス集中してきたらええ感じにばらけさしたるでって言うてます。
```
Ingressコントローラーは、負荷分散アルゴリズムやバックエンドの重みスキームなど、すべてのIngressに適用されるいくつかの負荷分散ポリシーの設定とともにブートストラップされます。発展した負荷分散のコンセプト(例: セッションの永続化、動的重み付けなど)はIngressによってサポートされていません。代わりに、それらの機能はService用のロードバランサーを介して利用できます。

ヘルスチェックの機能はIngressによって直接には公開されていませんが、Kubernetesにおいて、同等の機能を提供するReadiness Probeのようなコンセプトが存在することは注目に値します。コントローラーがどのようにヘルスチェックを行うかについては、コントローラーのドキュメントを参照してください(例えばnginx、またはGCE)。
```

# 最後に
完全に理解できましたか？

次はIngressがちょっとできるエンジニアを目指してがんばりましょう！


![ingress_overview](/images/kubernetes_ingress/kanzen_understand.png)