---
title: "【Next.js14対応】Next.jsをAzure Static Web AppsにTerraformからデプロイする方法【Github Actions】"
emoji: "🚀"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["Azure", "Terraform", "Next.js", "Static Web Apps", "Github Actions"]
published: true
---

# 目次

0. 前提
1. 目標
2. Terraform実装
3. Next.jsアプリ実装
4. Github Actions実行
5. 動作確認

## 前提
今日は、Next.jsアプリをAzure Static Web AppsにTerraformからデプロイする方法を紹介します。
以下のセットアップが完了していることを前提とします。

- Azureアカウントの契約
- Terraformのインストール
- Next.js version14の実行環境

それでは頑張っていきましょう！

## 目標

目標としては以下のようなシンプルなアーキテクチャをTerraformを用いて構築し、Github Actionsを用いてCI/CDを実現します。

![Azure Portal](/images/azure_swa_terraform/ver1.png)

これから色々と付け加えて作っていけそうなのびしろしかないアーキテクチャ図になります。

Azureのリソースとしては、後述するTerraformで以下のリソースを作成します。

1. リソースグループ
2. Azure Static Web Apps

![Azure Portal](/images/azure_swa_terraform/pic1.png)

最終的なAzure Static Web AppsにDeployされているNext.jsの画面としてはこんな感じ。(シンプルですね。)

![Azure Portal](/images/azure_swa_terraform/pic2.png)

それでは実装していきたいと思います。

## Terraform実装

まずは、Terraformの実装から初めていきます。
任意のディレクトリに```environment```ディレクトリと```module```ディレクトリを作成します。

moduleディレクトリに雛形を実装して、environmentから呼び出す形で実装していきます。

![Azure Portal](/images/azure_swa_terraform/pic3.png)

まずは、moduleディレクトリの中に以下のファイルを作成してください。

![Azure Portal](/images/azure_swa_terraform/pic4.png)

ざっくりとした説明が以下です。
- rg.tf : リソースグループを作成するためのTerraformファイル
- swa.tf : Azure Static Web Appsを作成するためのTerraformファイル
- variables.tf : 変数を定義するためのTerraformファイル

それでは、まずはrg.tfから実装していきます。

- rg.tf
```terraform:rg.tf
# resource group application
resource "azurerm_resource_group" "rg_app" {
  name     = var.rg_name
  location = var.rg_location
  tags = {
    environment = "sat-web-app"
  }
}
```

次にswa.tfを実装していきます。
```terraform:swa.tf
# Azure Static Web App のリソース作成
resource "azurerm_static_web_app" "swa" {
  name = var.swa_name
  resource_group_name = azurerm_resource_group.rg_app.name
  location = var.swa_location
  tags = {
    environment = "sat-web-app"
  }
}
```

最後にvariables.tfを実装していきます。
```terraform:variables.tf
variable "rg_name" {
  type = string
}

variable "rg_location" {
  type = string
}

variable "swa_name" {
  type = string
}

variable "swa_location" {
  type = string
}
```

これでmoduleディレクトリの内容は完璧です。
次はenvironmentディレクトリに以下のファイルを作成してください。

![Azure Portal](/images/azure_swa_terraform/pic6.png)

ざっくりとした説明が以下です。
- data.tf ： static web appsの情報を取得するためのTerraformファイル
- main.tf : moduleを呼び出すためのTerraformファイル
- outputs.tf : 出力を定義するためのTerraformファイル
- provider.tf : Azureのプロバイダーを定義するためのTerraformファイル
- terraform.tfvars : 変数を定義するためのTerraformファイル
- variables.tf : 変数を定義するためのTerraformファイル

それでは、まずはprovider.tfから実装していきます。

- provider.tf
```terraform:provider.tf
terraform {
  required_providers {
    azurerm = {
      source  = "hashicorp/azurerm"
      version = "=3.96.0"
    }
  }
}

provider "azurerm" {
  skip_provider_registration = true
  subscription_id            = "xxxxxxxxxxxxxxxxx"
  features {}
}
```

次にvariables.tfを実装していきます。
```terraform:variables.tf
variable "rg_name" {
  type = string
}

variable "rg_location" {
  type = string
}

variable "swa_name" {
  type = string
  default = "eastasia"
}

variable "swa_location" {
  type = string
}
```

次にdata.tfを実装していきます。
```terraform:data.tf
data "azurerm_static_web_app" "swa" {
  name = "sat-web-app-swa"
  resource_group_name = "sat-web-app-rg"
}
```

次にmain.tfを実装していきます。
```terraform:main.tf
module "app" {
  source = "../module"
  rg_name = var.rg_name
  rg_location = var.rg_location
  swa_name = var.swa_name
  swa_location = var.swa_location
}
```

次にoutputs.tfを実装していきます。
```terraform:outputs.tf
output "swa_api_key" {
  value = data.azurerm_static_web_app.swa.api_key
  sensitive = true
}
```

最後にterraform.tfvarsを実装していきます。
```terraform:terraform.tfvars
rg_name = "sat-web-app-rg"
rg_location = "japaneast"
swa_name = "sat-web-app-swa"
swa_location = "eastasia"
```

これでTerraformの実装は完了です。

environmentディレクトリで以下のコマンドを実行してください。
```bash
az login
az account set --subscription "xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
```

こちらで指定したAzureテナントへのログインが完了となります。
では、Terraformを実行していきます。
```bash
terraform init
terraform plan
terraform apply
```

上記applyのコマンド実行後、yesを入力してください。

しばらく経てば、Azureリソースが作成されています。

![Azure Portal](/images/azure_swa_terraform/pic2.png)

ここまで出来たら、Github Actionsで使うStatic Web AppsのAPIキーを取得しておきます。

```bash
terraform output -raw swa_api_key
```

こちらで出力されるAPIキーをコピーしておいてください。

これでTerraformの実装は完了です。

## Next.jsアプリ実装
まずはgithubでリポジトリを作り、ローカルへクローンして準備してください。

準備できたディレクトリに以下のコマンドを実行してください。
```bash
npx create-next-app . --ts
```

色々質問されるので、全部Yesで回答
```bash
✔ Would you like to use ESLint with this project? … No / Yes
✔ Would you like to use Tailwind CSS with this project? … No / Yes
✔ Would you like to use `src/` directory with this project? … No / Yes
✔ Use App Router (recommended)? … No / Yes
✔ Would you like to customize the default import alias? … No / Yes
✔ What import alias would you like configured? … @/*
Creating a new Next.js app in /Users/s.y/prj/04.webapp/sat-web-repo.
```

出来上がったプロジェクトをVSCodeで開いてください。
src/app/page.tsxを以下のように実装してください。
```tsx:src/app/page.tsx

export default function Home() {
  return (
    <main className="flex min-h-screen flex-col items-center justify-between p-24">
      <p>test web site</p>
    </main>
  )
}

```

次に、node.jsのversionを指定しておきます。
以下を一番下に追記してください。

```json:package.json
"engines": {
    "node": "18.19.0"
}
```

次に、next.config.jsをoutput: "standalone"を追記してください
```js:next.config.js
/** @type {import('next').NextConfig} */
const nextConfig = {
  output: "standalone"
}

module.exports = nextConfig
```

次にGithub Actionsの設定を行います。
ルート階層に、.github/azure-static-web-apps.ymlを作成してください。

```yml:.github/workflows/azure-static-web-apps.yml
name: Azure Static Web Apps CI/CD

on:
  push:
    branches:
      - main
  pull_request:
    types: [opened, synchronize, reopened, closed]
    branches:
      - main

jobs:
  build_and_deploy_job:
    if: github.event_name == 'push' || (github.event_name == 'pull_request' && github.event.action != 'closed')
    runs-on: ubuntu-latest
    name: Build and Deploy Job
    steps:
      - uses: actions/checkout@v3
        with:
          submodules: true
          lfs: false
      - uses: actions/setup-node@v4
        with:
          node-version: '18.19.0'
      - name: Build And Deploy
        id: builddeploy
        uses: Azure/static-web-apps-deploy@v1
        with:
          azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN }}
          repo_token: ${{ secrets.GITHUB_TOKEN }} # Used for Github integrations (i.e. PR comments)
          action: "upload"
          ###### Repository/Build Configurations - These values can be configured to match your app requirements. ######
          # For more information regarding Static Web App workflow configurations, please visit: https://aka.ms/swaworkflowconfig
          app_location: "/" # App source code path
          api_location: "" # Api source code path - optional
          output_location: "" # Built app content directory - optional
          ###### End of Repository/Build Configurations ######

  close_pull_request_job:
    if: github.event_name == 'pull_request' && github.event.action == 'closed'
    runs-on: ubuntu-latest
    name: Close Pull Request Job
    steps:
      - name: Close Pull Request
        id: closepullrequest
        uses: Azure/static-web-apps-deploy@v1
        with:
          azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN }}
          action: "close"

```

これでNext.jsの実装は完了です。

## Github Actions実行
それでは、Github Actionsを実行していきます。
先ほどのAPIキーをGithubのリポジトリのSecretsに登録していきます。

Githubリポジトリへアクセスし、下記画像のSettingsをクリックしてください。
![Azure Portal](/images/azure_swa_terraform/pic7.png)

画面左側ペインより、Secrets and variablesを選択して、Actionsをクリックしてください。
![Azure Portal](/images/azure_swa_terraform/pic9.png)

Repository secretsへ以下を登録してください。
Name : AZURE_STATIC_WEB_APPS_API_TOKEN
Secret : 先ほど取得したAPIキー

ここまでくればOKです！
先ほどのNext.jsのコードをpushしてください！

Github Actionsが実行され、Azure Static Web Appsへデプロイされます。

![Azure Portal](/images/azure_swa_terraform/pic10.png)

## 動作確認

こちらでデプロイが完了していれば、Azure Static Web AppsのURLへアクセスしてください。


![Azure Portal](/images/azure_swa_terraform/pic11.png)

こんな感じでデプロイされていればOKです！

![Azure Portal](/images/azure_swa_terraform/pic2.png)

お疲れ様でした！