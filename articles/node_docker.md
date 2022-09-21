---
title: "【Let's ハンズオン🤙】node.jsをDocker化してDocker run❗️【Express.js】"
emoji: "🤙"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["node.js","Express.js", "Docker"]
published: true
---

# 【Let's ハンズオン🤙】node.jsをDocker化してDocker run❗️

## 目次
0. Dockerをインストール
1. アプリを準備
2. アプリをDocker化
3. Docker run!!

# Dockerをインストール
なにがともあれDockerを使用するので、自身の環境にDockerをインスト-ルしちゃいましょう！

## Macの方はこちら
https://docs.docker.com/desktop/install/mac-install/
## Windowsの方はこちら
https://docs.docker.com/desktop/install/windows-install/

下記のダウンロードのボタンをクリックでダウンロードが開始されます。

![hello-express](/images/node_docker/Docker-install.png)

# アプリを準備
Expressのアプリを作成しちゃいましょう！

まずは、Node.jsがインストールされているか確認です。

以下、二つのコマンドを実行して確認してみてください。

```bash
node -v
```

```bash
npm -v
```

ゆうじろうはこんな感じの表示がでました。
- node -v 結果
```bash
v16.14.0
```

- npm -v 結果
```bash
v8.3.1
```

では、アプリを作成していきましょう！

ExpressというNode.jsのフレームワークを使用するので、まずはインストールです。

```bash
npm install -g express-generator
```

これでExpressのアプリが簡単に作成できる様になりました。

では、作成していきましょう。

任意の場所にプロジェクト用のディレクトリを作成して、以下のコマンドを実行です。

```bash
express --view=ejs .
```

こんな感じの表示がでてきたら、yをクリックで続行です。

```bash
destination is not empty, continue? [y/N] 
```

こんな感じの出力がされましたか？

```bash
destination is not empty, continue? [y/N] y

   create : public/
   create : public/javascripts/
   create : public/images/
   create : public/stylesheets/
   create : public/stylesheets/style.css
   create : routes/
   create : routes/index.js
   create : routes/users.js
   create : views/
   create : views/error.ejs
   create : views/index.ejs
   create : app.js
   create : package.json
   create : bin/
   create : bin/www

   install dependencies:
     $ npm install

   run the app:
     $ DEBUG=express-react:* npm start
```

では、dependenciesをinstallしてほしいとのことなので、インストールしてあげましょう。

```bash
npm install
```

npm installができたら、アプリをローカル環境で実行しましょう！

```bash
npm start
```

```bash
> [ディレクトリ名]@0.0.0 start
> node ./bin/www
```

こんな感じの表示がでれば、以下へアクセスです！

+ http://localhost:3000

以下の様な画面が表示されればアプリ作成成功です！
![hello-express](/images/node_docker/hello-express.png)


# アプリをDocker化
次は作成したアプリをDocker Image化していきます。

プロジェクトのルート階層へ以下のファイル２つを作成してください。

- Dockerfile
- .dockerignore

Dockerfileの中身はこんな感じ

- Dockerfile
```Dockerfile
# 最初に調べたnodeのバージョンを記載
FROM node:16

# アプリケーションディレクトリを作成する
WORKDIR /usr/src/app

# アプリケーションの依存関係をインストールする
# ワイルドカードを使用して、package.json と package-lock.json の両方が確実にコピーされるようにします。
# 可能であれば (npm@5+)
COPY package*.json ./

RUN npm install

# アプリケーションのソースをバンドルする
COPY . .

# 開放するport番号を記載
EXPOSE 3000
CMD [ "npm", "start" ]
```

次は.dockerignoreファイルです。
これにより、ローカルモジュールとデバッグログが Docker イメージにコピーされたり、 イメージ内にインストールされているモジュールが上書きされたりするのを防ぐことができます。

- .dockerignore
```dockerignore
node_modules
npm-debug.log
```

ここまで作成したら、Dockerのイメージを作成します。

```bash
docker build . -t express-app:v1
```

express-appのv1が作成されます。

![hello-express](/images/node_docker/docker-image.png)

DockerDesktopを見てみましょう。
imagesの箇所へDockerイメージが作成されていれば完了です！
![hello-express](/images/node_docker/dockerdesktop.png)

以下のコマンドでも確認できます。

```bash
docker images
```

# Docker run!!
ここまでくればもう簡単です。
以下のコマンドを実行し、Docker上でExpressで作成されたアプリを実行してください！

```bash
docker run -p 3000:3000 -d express-app:v1
```

-dオプションで実行しているので、バックグラウンドで実行中となります。

では、再度以下へアクセスしましょう！
+ http://localhost:3000

![hello-express](/images/node_docker/hello-express.png)

見た目は変わりませんが、今度はDockerコンテナ上でのアプリの実行となります。

動作確認が終了したら、アプリを停止しておきましょう。

![hello-express](/images/node_docker/dockerstop.png)

stopボタンでアプリを停止できます。

ハンズオンは以上となります！

## お疲れ様でした。