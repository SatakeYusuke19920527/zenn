---
title: "Nginx Ingress Controller完全に理解した"
emoji: "📖"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["Kubernetes","Ingress", "Nginx","ingress controller"]
published: true
---

# Nginx Ingress Controller完全に理解した

ここはNginxIngressControllerってなんじゃいって言う人向けの記事になります。
いい感じにNginxIngressControllerをざっくり理解した気持ちになれる場所です。

以下の公式の内容を噛み砕いて、原型をなくしてやろうと思います。
https://www.nginx.co.jp/resources/glossary/kubernetes-ingress-controller/

## では、お時間ちょっといただくやで。

# NginxIngressControllerをざっくりと説明

公式より
*** 
Kubernetes Ingressコントローラーは、Kubernetes環境用ロードバランサーです。Kubernetesは、コンテナ化されたアプリケーションを管理するためのデファクトスタンダードです。多くの企業では、本番環境のワークロードをKubernetesに移行することで、アプリケーショントラフィック管理の課題が増え、複雑化が進展します。Ingressコントローラーは、Kubernetesアプリケーションのトラフィックルーティングを抽象化して複雑さを排除し、Kubernetesサービスと外部サービスを橋渡しします。
***

## 下の図のIngressのところにハマってええ感じにしてくれるやつです。

![ingress_overview](/images/kubernetes_ingress/ingress_overview.png)

機能の一覧は以下

Kubernetes Ingressコントローラーは、以下のように機能します。

- Kubernetesプラットフォーム外部からのトラフィックを受け入れ、プラットフォーム内部で稼働するポッド（コンテナ）にロードバランシングする
- クラスター外の他のサービスとの通信を必要とするサービス用に、クラスター内のEgressトラフィックを管理する
- 「Ingressリソース」と呼ばれるオブジェクトをデプロイするように、Kubernetes APIを使用して構成される
- Kubernetesで稼働するポッドを監視し、ポッドがサービスに追加されたりサービスから削除されたりした場合にはロードバランシングルールを自動的に更新する

## NGINXはどのように役立ちますか？

Ingressコントローラーの構成を頻繁に変更する場合や、Kubernetesサービスの保護が最優先事項である場合は、以下のような機能を提供する本番環境グレードのIngressコントローラーが必要です。

- 動的な再構成
- 最新の軽量なWebアプリケーションファイアウォール（WAF）
- OpenID Connect（OIDC）のような、実証済みの標準的シングルサインオン（SSO）ソリューションによる認可と認証
- 本番環境のワークロードをKubernetesに迅速に移行するための、24時間365日体制のサポート

NGINX Ingress Controllerは、Kubernetes環境でNGINX Open SourceやNGINX Plusインスタンスと一緒に動作する本番環境グレードのIngressコントローラー（デーモン）です。このデーモンは、NGINX IngressリソースとKubernetes Ingressリソースを監視して、Ingressロードバランシングが必要なサービスリクエストを検出します。また、NGINX App Protectと互換性があります。これは最新の軽量なWAFであり、Ingressコントローラーにデプロイすることも、サービスまたはポッドごとのプロキシとしてデプロイすることも可能です。

NGINX Ingress Controllerにより、Kubernetesのレイヤー4～7のネットワーキングを活用して、Kubernetesサービス間のセキュリティとトラフィックの制御を強化できます。

NGINXを使用するIngressコントローラーは数多くあるため、自社に適した選択肢の見極めに戸惑うことがあるかもしれません。意思決定でよくあることですが、選択肢はユースケースに左右されます。本番環境グレードのアプリケーションのデリバリーには、このブログで紹介している機能が推奨されます。これらは、NGINX PlusベースのバージョンのNGINX Ingress Controller独自の機能が最適です。


# 公式サイトとgithubのリポジトリも併せてどうぞ
1. Kubernetesコミュニティ版Ingressコントローラー（kubernetes/ingress-nginx on GitHub)
https://www.nginx.co.jp/products/nginx-ingress-controller/
2. NGINXオープンソース版Ingressコントローラーnginxinc/kubernetes-ingress on GitHub)
https://github.com/nginxinc/kubernetes-ingress
3. GINX PlusベースのNGINX Ingress Controller（NGINX Plusベースの商用バージョン)
https://github.com/kubernetes/ingress-nginx

# 最後に
完全に理解できましたか？

次はちょっとできるエンジニアを目指してがんばりましょう！


![ingress_overview](/images/kubernetes_ingress/kanzen_understand.png)