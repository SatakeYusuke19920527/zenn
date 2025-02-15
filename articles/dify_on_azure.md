---
title: 'DifyをAzure Kubernetes Service(AKS)にデプロイして本番環境で使う手順'
emoji: '🚢'
type: 'tech' # tech: 技術記事 / idea: アイデア
topics: ['Azure', 'Kubernetes', 'Dify', 'Docker']
published: false
---

![](https://storage.googleapis.com/zenn-user-upload/296104da0c4f-20250215.png)

# はじめに

Dify はみなさまご存知でしょうか。
https://dify.ai/jp

LLM のアプリケーションがノーコード(ローコード)で簡単に作れるツールです。
このツールを使っている方が最近増えてきており、且つ Azure 上で動かしたいというご要望をいただいたので、今回は Dify を Azure Kubernetes Service(AKS)にデプロイして本番環境で使う手順をご紹介します。

# Dify 環境構築

https://github.com/langgenius/dify

```bash
git clone https://github.com/langgenius/dify.git
```

dify/docker/.env.sample → .env へ変更

docker/ ディレクトリへ移動して Docker のコンテナを起動

```bash
docker compose up -d
```

![](https://storage.googleapis.com/zenn-user-upload/e9ec55f79420-20250214.png)

ちなみに、Docker 停止する場合は以下

```bash
docker compose down
```

http://localhost を起動

Dify の画面が表示される
![](https://storage.googleapis.com/zenn-user-upload/7a2fa7c950b2-20250214.png)

SignIn すると、Dify の画面が表示されます。
![](https://storage.googleapis.com/zenn-user-upload/7439bcaf08c8-20250214.png)

ちなみに、ログイン情報を忘れたら...

```bash
docker exec -it docker-api-1 flask reset-password
```

# LLM の設定

Azure OpenAI Service の GPT-4o を用いて設定したいと思います。
設定を押して
![](https://storage.googleapis.com/zenn-user-upload/8003cd77f015-20250214.png)

モデルプロバイダーを設定します。
今回は Azure OpenAI Service の GPT-4o を設定します。
![](https://storage.googleapis.com/zenn-user-upload/af41e033e331-20250214.png)

モデルの追加で各種項目の値を設定します。
![](https://storage.googleapis.com/zenn-user-upload/5479b79b217f-20250214.png)

設定するとこんな感じ
![](https://storage.googleapis.com/zenn-user-upload/c365a0c35cad-20250214.png)

ワークフローを作成していく
![](https://storage.googleapis.com/zenn-user-upload/88c5726a97c1-20250214.png)

Dify で今回作成する LLM アプリは HTTP リクエスト →LLM に分析 → アウトプットを生成するワークフローとなります。

データを生成 AI で分析して整形して出力するよくあるワークフローですね。

![](https://storage.googleapis.com/zenn-user-upload/b1b63fe47d98-20250214.png)

# ngrok を用いて API サーバーの動作確認

ngrok を用いて Docker を install
https://dashboard.ngrok.com/get-started/setup/docker

```bash
docker pull ngrok/ngrok
docker run --net=host -it -e NGROK_AUTHTOKEN=2t3TnBNzCwhnn1UXwFYWxVaFV2l_56nrCnhCSjx3T4jhX7bzC ngrok/ngrok:latest http 80
```

以下のような表示がターミナル上で表示される
![](https://storage.googleapis.com/zenn-user-upload/dfbd96a0ba02-20250215.png)

https://0b41-103-5-140-167.ngrok-free.app へアクセスすると、ホスティングされた Dify へ Signin 出来る

作成したワークフローの箇所へ行き、API アクセスから workflow の実行の箇所を確認
![](https://storage.googleapis.com/zenn-user-upload/0427a5bc8d10-20250215.png)

API キーは右上の箇所から発行出来る
URL は読者の方それぞれで書き換えていただく必要があるが、以下のようなコマンドで実行

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

# AKS へ Deploy

ngrok での簡単な動作確認が終わったら、AKS へ Deploy します。
https://azure.microsoft.com/ja-jp/products/kubernetes-service

```bash
brew install helm
brew install azure-cli
brew install kubectl
```

リソースプロバイダーにて、以下は登録しておく

```
Microsoft.ContainerService
Microsoft.ContainerRegistry
Microsoft.ContainerInstance
Microsoft.Network
Microsoft.ServiceNetworking
```

以下のコマンドでクラスターを作成

```bash
az aks create --resource-group poc-dify-app --name aks-poc-dify-app --node-count 1 --enable-addons http_application_routing --generate-ssh-keys
```

AKS が作成されましたね。
![](https://storage.googleapis.com/zenn-user-upload/93e2ce1e7a31-20250215.png)

続いては以下のコマンドで kubectl が打てるように AKS が作成できたタイミングで get-credentials を実行します。

```bash
az aks get-credentials --resource-group poc-dify-app --name aks-poc-dify-app
```

次は ACR を作成します。
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

values.yaml を作成

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

Happy Helming! と表示されれば OK
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

# 参考文献

https://github.com/douban/charts/tree/master/charts/dify
