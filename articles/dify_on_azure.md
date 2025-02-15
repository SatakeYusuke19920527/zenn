---
title: 'Difyで作ったLLM ApplicationをAzure Kubernetes Serviceにデプロイする方法'
emoji: '🚢'
type: 'tech' # tech: 技術記事 / idea: アイデア
topics: ['Azure', 'Kubernetes', 'Dify', 'Docker', 'openai']
published: true
---

![](https://storage.googleapis.com/zenn-user-upload/713dfc009ddf-20250215.png)

# はじめに

Dify はみなさまご存知でしょうか。

https://dify.ai/jp

LLM のアプリケーションがノーコード(ローコード)で簡単に作れるツールです。

このツールを使っている方が最近増えてきており、且つ Azure 上で動かしたいというご要望をいただいたので、今回は Dify を Azure Kubernetes Service(AKS)にデプロイして本番環境で使う手順をご紹介します。

# Dify 環境構築

まずは Dify を Web 上ではなく、ローカルで動かすために Docker を使って環境構築を行います。
以下のサイトへアクセスして、Dify のリポジトリを clone します。
https://github.com/langgenius/dify

```bash
git clone https://github.com/langgenius/dify.git
```

環境変数を使用する場合は
dify/docker/.env.sample → .env へ変更してください。

次は、clone した Dify のプロジェクトをローカルで起動します。

docker/ ディレクトリへ移動して Docker のコンテナを起動します。

以下のコマンドを実行してください。

```bash
docker compose up -d
```

![](https://storage.googleapis.com/zenn-user-upload/e9ec55f79420-20250214.png)

ちなみに、Docker 停止する場合は以下になります。

```bash
docker compose down
```

無事に起動できたら、以下の URL でブラウザからアクセス

http://localhost

Dify の画面が表示されます。
![](https://storage.googleapis.com/zenn-user-upload/7a2fa7c950b2-20250214.png)

SignIn すると、Dify の画面が表示されます。
![](https://storage.googleapis.com/zenn-user-upload/7439bcaf08c8-20250214.png)

ちなみに、ログイン情報を忘れたら...
以下のコマンドで再設定可能なので、ご心配なく。

```bash
docker exec -it docker-api-1 flask reset-password
```

# LLM の設定

次は Azure OpenAI Service の GPT-4o を用いて設定したいと思います。
画面右上の設定をクリックして、モデルプロバイダーを設定します。
![](https://storage.googleapis.com/zenn-user-upload/8003cd77f015-20250214.png)

モデルプロバイダーを選択してください。
今回は Azure OpenAI Service の GPT-4o を設定します。
![](https://storage.googleapis.com/zenn-user-upload/af41e033e331-20250214.png)

Azure での LLM の DeployGPT-4o の

モデルの追加で各種項目の値を設定します。
![](https://storage.googleapis.com/zenn-user-upload/5479b79b217f-20250214.png)

設定するとこんな感じ
![](https://storage.googleapis.com/zenn-user-upload/c365a0c35cad-20250214.png)

ワークフローを作成していく
![](https://storage.googleapis.com/zenn-user-upload/88c5726a97c1-20250214.png)

Dify で今回作成する LLM アプリは HTTP リクエスト →LLM に分析 → アウトプットを生成するワークフローとなります。

データを生成 AI で分析して整形して出力するよくあるワークフローですね。

![](https://storage.googleapis.com/zenn-user-upload/b1b63fe47d98-20250214.png)

流れは以下です。

1. 処理の開始
2. https://jsonplaceholder.typicode.com/users へ http リクエスト
3. JSON → 配列へ変換
4. 10 名の user 情報が取得されるので、イテレーションを用いて 1 名ずつ ID と Name を取得
5. string でくるデータを配列に整形
6. 処理の終了

Dify 上で実行すると以下のような画面が表示されます。
![](https://storage.googleapis.com/zenn-user-upload/fee665a21ec7-20250215.png)

画面右下に output が表示され、ちゃんとユーザー情報が出力されていますね。

# ngrok を用いて API サーバーの動作確認

ngrok を用いて、API サーバーとして動作確認を行います。

ngrok（エングロック） は、ローカルで動作している Web アプリケーションをインターネット上に一時的に公開するためのツールです。特に、外部アクセスが難しいローカル環境（NAT やファイアウォールの内側）から簡単にインターネット経由でアクセス可能な公開 URL を提供します。
https://ngrok.com/

💡 主な用途

- Webhook の開発・テスト:
  - Stripe や GitHub などの Webhook をローカル環境で受け取る際に利用。
- クライアントへのデモ:
  - ローカルで開発中の Web アプリを即座にクライアントに共有可能。
- リモートデバッグ:
  - 他のエンジニアやチームメンバーにローカル環境を簡単に見せる。
- IoT デバイスからの接続:
  - スマートホームや Raspberry Pi のプロジェクトで、外部からローカルデバイスへ接続。

ngrok を用いて動作確認をしていきたいと思います。

まずは Docker を install

https://dashboard.ngrok.com/get-started/setup/docker

```bash
docker pull ngrok/ngrok
docker run --net=host -it -e NGROK_AUTHTOKEN=2t3TnBNzCwhnn1UXwFYWxVaFV2l_56nrCnhCSjx3T4jhX7bzC ngrok/ngrok:latest http 80
```

以下のような表示がターミナル上で表示されると問題なく起動しているということになります。
![](https://storage.googleapis.com/zenn-user-upload/dfbd96a0ba02-20250215.png)

https://0b41-103-5-140-167.ngrok-free.app
へアクセスすると、ホスティングされた Dify へ Signin できます。

作成したワークフローの箇所へ行き、API アクセスから workflow の実行の箇所を確認してみましょう。
![](https://storage.googleapis.com/zenn-user-upload/0427a5bc8d10-20250215.png)

ワークフローの実行は/workflows/run で実行できます。
また、API キーは右上の箇所から発行出来ます。

URL は読者の方それぞれで書き換えていただく必要があるが、以下のようなコマンドで実行することができます。

```bash
curl -X POST 'https://0b41-103-5-140-167.ngrok-free.app/v1/workflows/run' \
--header 'Authorization: Bearer app-8ITfKQU1VPUhMY5qhISyMYLr' \
--header 'Content-Type: application/json' \
--data-raw '{
    "inputs": {},
    "response_mode": "streaming",
    "user": "abc-123"
}'
```

実行されて、最後の箇所に取得したいデータが return されていることがわかる
![](https://storage.googleapis.com/zenn-user-upload/94505850f309-20250215.png)

ユーザーが取得されていますね。

# Azure Kubernetes Service へ Deploy

ngrok での簡単な動作確認が終わったら、Azure Kubernetes Service(AKS) へ Deploy します。
https://azure.microsoft.com/ja-jp/products/kubernetes-service

以下コマンドで 必要なツールをインストールします。

```bash
brew install helm
brew install azure-cli
brew install kubectl
```

Azure にアクセスして、リソースプロバイダーより以下は登録しておいてください。

```
Microsoft.ContainerService
Microsoft.ContainerRegistry
Microsoft.ContainerInstance
Microsoft.Network
Microsoft.ServiceNetworking
```

まずは、以下のコマンドでクラスターを作成します。

```bash
az aks create --resource-group poc-dify-app --name aks-poc-dify-app --node-count 1 --enable-addons http_application_routing --generate-ssh-keys
```

Azure portal から確認すると、AKS が作成されましたね。
![](https://storage.googleapis.com/zenn-user-upload/93e2ce1e7a31-20250215.png)

続いては以下のコマンドで kubectl が打てるように AKS が作成できたタイミングで get-credentials を実行します。

```bash
az aks get-credentials --resource-group poc-dify-app --name aks-poc-dify-app
```

次は Azure Container Registory (ACR) を作成します。
https://azure.microsoft.com/ja-jp/products/container-registry

```bash
az acr create --resource-group poc-dify-app --name acrpocdifyapp --sku Basic
```

こんな形で作成されれば OK です。
![](https://storage.googleapis.com/zenn-user-upload/bb4f9a3f1e31-20250215.png)

以下のコマンドで ACR へ import を実行します。

```bash
az acr import --name acrpocdifyapp --source docker.io/langgenius/dify-web --image dify-web:latest
az acr import --name acrpocdifyapp --source docker.io/langgenius/dify-api --image dify-api:latest
```

次は values.yaml を作成して、ingress 部分の設定をします。

```yaml
global:
  host: 'hello-bnm.cloud'
  enableTLS: false

  image:
    # Set to the latest version of dify
    # Check the version here: https://github.com/langgenius/dify/releases
    # If not set, Using the default value in Chart.yaml
    tag: '0.15.3'
  extraBackendEnvs:
    - name: SECRET_KEY
      value: 'satakeyusuke19920527'
    - name: LOG_LEVEL
      value: 'DEBUG'
    - name: VECTOR_STORE
      value: 'milvus'

ingress:
  enabled: true
  className: 'nginx'

minio:
  embedded: true
```

helm コマンドを用いて、douban の Helm レポジトリを取得

```bash
helm repo add douban https://douban.github.io/charts/
helm repo update
```

Happy Helming! と表示されれば OK です。
![](https://storage.googleapis.com/zenn-user-upload/f5f882efae15-20250215.png)

先ほど作成した values.yaml を helm コマンドで指定して実行

```bash
helm upgrade dify douban/dify -f values.yaml --install --debug
```

AKS のサービスと Ingress に
以下のサービスが起動しています。

- dify-sandbox
- dify-redis-master
- dify-redis-headless
- dify-postgresql-hl
- dify-postgresql
- dify-minio
- dify-frontend
- dify-api-svc

![](https://storage.googleapis.com/zenn-user-upload/ce0d26346958-20250215.png)

以下のコマンドでも確認可能です。

```bash
kubectl get services -n default
```

![](https://storage.googleapis.com/zenn-user-upload/73d016ea0da9-20250215.png)

各 pod が正常に起動しているかどうかも確認しておきます。

```bash
kubectl get pods -n default
```

![](https://storage.googleapis.com/zenn-user-upload/3295becdf2e1-20250215.png)

すべての pod が Running になっていれば OK

# DB migration

Dify が利用する PostgreSQL に DB マイグレーションを実施します。
以下のコマンドを実行して、API サーバーの pod 名を確認しします。

```bash
kubectl get pods -n default
```

![](https://storage.googleapis.com/zenn-user-upload/b2483f302bec-20250215.png)

ここで API は `dify-api-c948d57d8-wkspr` という名前なので、以下のコマンドを実行します

```bash
kubectl exec -it dify-api-c948d57d8-wkspr -- flask db upgrade
```

コマンドがエラーなく完了すれば OK です。

# Load balancer Update

先ほど AKS クラスターを作成した際に http_application_routing で作成済みの LoadBarancer を `dify-ingress.yaml` を作成し、先ほど作成した http_application_routing に各サービスを紐づけて dify のイングレスとして組み込みます。

- dify-ingress.yaml

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: dify-ingress
  annotations:
    kubernetes.io/ingress.class: addon-http-application-routing
spec:
  rules:
    - http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: dify-frontend
                port:
                  number: 80
          - path: /console/api
            pathType: Prefix
            backend:
              service:
                name: dify-api-svc
                port:
                  number: 80
          - path: /api
            pathType: Prefix
            backend:
              service:
                name: dify-api-svc
                port:
                  number: 80
          - path: /v1
            pathType: Prefix
            backend:
              service:
                name: dify-api-svc
                port:
                  number: 80
          - path: /files
            pathType: Prefix
            backend:
              service:
                name: dify-api-svc
                port:
                  number: 80
```

以下のコマンドで更新です。

```bash
kubectl apply -f dify-ingress.yaml
```

これで Ingress が更新されたので、AKS 上に Deploy された Dify へアクセス出来ます。
(ただし、この時点でサインインやインストールはできません。Dify のフロントエンド(Next.js)が API サーバーへアクセスするドメインを上記の IP アドレスでなく、values.yaml に指定した global.host の値を利用するためです。)

AKS の画面で、外部 IP へアクセスしてみましょう。
![](https://storage.googleapis.com/zenn-user-upload/a4e0f8c116d3-20250215.png)

この画面で言うと、IP が `74.176.235.9`となっている箇所にアクセスですね。

以下のような画面になっていればとりあえずは OK です。
![](https://storage.googleapis.com/zenn-user-upload/74eaa8194d50-20250215.png)

# DNS の更新

お名前.com などで取得したドメインを Azure DNS を用いて名前解決して AKS に紐付けし、アクセスすれば Done です！

これで問題なく Dify on AKS にアクセスすることが出来ます！
![](https://storage.googleapis.com/zenn-user-upload/e9666d22eb59-20250215.png)

https リクエストが必要な場合は以下のサイトをご参照ください。
https://cert-manager.io/docs/

また、こちらの手順もご要望があれば詳細に記載しますので、コメント欄で教えてください。

# まとめ

いかがだったでしょうか。
Dify は簡単に LLM アプリケーションが作れ、且つ Azure 上で動かすと本番環境でも利用することが可能です。
是非色々な LLM アプリケーションを作成してみてください。

それでは 👋

# 参考文献

https://github.com/douban/charts/tree/master/charts/dify
