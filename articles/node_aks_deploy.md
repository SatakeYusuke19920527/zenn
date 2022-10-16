---
title: "【Let's ハンズオン🤙】node.jsで作成したアプリをAKSでデプロイ❗️【Azure Kubernetes Service】"
emoji: "🤙"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["Azure","node.js","Express.js", "Docker"]
published: true
---

# 【Let's ハンズオン🤙】node.jsで作成したアプリをAKSでデプロイ❗️【Azure Kubernetes Service】

## 目次
0. Docker imageを作成
1. Azure Container Instanceへpush
2. yamlファイルを準備
3. AKSを準備
4. AKSへデプロイ

# Docker imageを作成

今回はローカルで作成したDocker imageを使用致しますので、以下の記事でExpress.jsで作成されたアプリをDocker イメージ化してください！

[【Let's ハンズオン🤙】node.jsをDocker化してDocker run❗️【Express.js】](https://zenn.dev/yusu29/articles/node_express_docker)

# Azure Container Instanceへpush
続いて、Azureポータルの画面からAzureContainerService(以下、ACR)を作成します。

ざっくりACRを説明するとコンテナイメージを置いておく倉庫みたいなところと思ってくれればOKです。

Azureポータルの検索エリアに[コンテナーレジストリ]と入力すると、出てくるかと思います。

コンテナーレジストリをクリックし、[作成]をクリックです！

![acr](/images/node_aks_deploy/acr.png)

リソースグループ・リージョン・場所は任意の値でOKです。
SKUはStandardにしておきましょう！

後はデフォルトで作成をクリックです。

ACRができましたか？


まずは、Dockerイメージがちゃんとできていることを確認です。

```bash
docker images
```

こんな感じに表示されましたか？

```bash
$ docker images
REPOSITORY                                         TAG       IMAGE ID       CREATED        SIZE
sampleapp                                     v1        21ad6769ce90   1 days ago    923MB
```

上記のようにいい感じにACRが作成されたら、Dockerイメージにtagをつけます。

```bash
docker tag sampleapp:v1 [acrのログインサーバーに記載されているurl]/sampleapp:v1
```
ログインサーバに記載されているurlは[acrの名前].azurecr.ioとなっているものです！

tag付けができたらやっちゃいましょう。
docker pushコマンドでACRへpushです！

```bash
docker push [acrのログインサーバーに記載されているurl]/sampleapp:v1
```
Azureポータルへ戻り、ACR→サービスのリポジトリの箇所にこんな感じでpushされていれば最高です！


![ripository](/images/node_aks_deploy/ripository.png)

ここまでで、ACRへ作成したDockerイメージが格納された形になりました😎

# yamlファイルを準備
次はいよいよデプロイ...!と行きたいところですが、デプロイの設定ファイルであるyamlファイルを作成する必要があります。

作成したnode.jsのアプリをエディタで開き、ルート階層へ以下のyamlファイルを追加してください。


- deploy.yaml
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sampleapp
spec:
  replicas: 1
  selector:
    matchLabels:
      app: sampleapp
  strategy:
    rollingUpdate:
      maxSurge: 1
      maxUnavailable: 1
  minReadySeconds: 5
  template:
    metadata:
      labels:
        app: sampleapp
    spec:
      nodeSelector:
        'kubernetes.io/os': linux
      containers:
        - name: webapp
          image: [ACRに配置したDockerイメージを配置:v1も忘れずに。]
          ports:
            - containerPort: 8080
          resources:
            requests:
              cpu: 250m
            limits:
              cpu: 500m
---
apiVersion: v1
kind: Service
metadata:
  name: express-load-balancer
  labels:
    run: express-load-balancer
spec:
  type: LoadBalancer
  ports:
    - port: 80
      targetPort: 8080
      protocol: TCP
  selector:
    app: sampleapp

```

作成できましたか？

# AKSを準備
Azureポータルを開き、検索エリアへ[aks]と入力してみてください。

Azure Kubernetes Service(以下、AKS)の表示が出てくるはずです。

クリックし、AKSを作成しちゃってください！

Kubernetes クラスターの作成です。

リソースグループは任意を値を、クラスタープリセット構成はDev/TestでOKです。

後はデフォルトの設定でOK

AKSが作成されましたか？

# AKSへデプロイ
さぁ、ここまでくれば後はデプロイを残すのみです！

初回の人は以下を実施です。
```bash
az aks install-cli
```

そして、ACRへログインしておきましょう。

```bash
az acr login --name [ACR名]
```

次はkubectl を使用し、クラスターへの接続します。
以下のコマンドを実行です。

```bash
az aks get-credentials --resource-group [リソースグループ名] --name [AKS名]
```
上記がでてきたら、以下のコマンドを実行してみてください。
```bash
kubectl get nodes
```
以下の様な感じになりましたか？
```bash
$ kubectl get nodes

NAME                                STATUS   ROLES   AGE     VERSION
aks-nodepool1-37463671-vmss000000   Ready    agent   2m37s   v1.18.10
```

いい感じにnodeが作成されていたら、以下コマンドでyamlファイルを使ってデプロイです！

```bash
kubectl apply -f deploy.yaml
```

AKS上にデプロイされたアプリを確認してみましょう。

サービスとイングレスの箇所から、ロードバランサーのIPアドレスをコピーし、お好みのブラウザへ貼り付けて結果をみてみましょう！

![hello-express](/images/node_docker_image/hello_express.png)

Expressのデフォルトの画面が確認できましたか？

ここまでくればデプロイ完了です！

## お疲れ様でした！


ps. AKSはつけっぱなしだと意外と値段するので、しっかり停止しておいてね。