---
title: "【Azure OpenAI】PTUについて完全に理解する【2024年8月14日最新版】"
emoji: "🚀"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["Azure", "OpenAI","Microsoft", "PTU", "AOAI"]
published: false
publication_name: "microsoft"
---

![Azure](/images/azure_perfectly_understand_ptu/img1.png)

# 本記事の目標はPTU(Provisioned throughput units)について完全に理解することです🚀

本記事では、Azure OpenAIを利用する上で理解しておくべき概念である **PTU** について、完全に理解する為の記事です。
**PTU** とは何か、どのように活用できるのか、利用するとどのようなメリットがあるのか詳細に解説します。

また、Azure OpenAI の PTUは、購入モデルと Azure 標準との調整や、モデルに依存しないクォータへの移行を含めて、2024 年 8 月 12 日に大規模な更新がありました。
本記事はこの更新によって何が変わるのかも記載したいと思います。

## 目次

1. [PTUとは何か？](#ptuとは何か)
2. [PTUが使用出来るモデルとリージョン](#PTUが使用出来るモデルとリージョン)
3. [PTUの利用方法](#PTUの利用方法)
4. [PTUのコスト](#PTUのコスト)
5. [最新Updateで何が変わったのか](#最新Updateで何が変わったのか)
6. [まとめ](#まとめ)

## PTUとは何か？

### 定義と役割

**PTU（Provisioned Throughput Units）**とは、Azure OpenAIサービスで使用される単位で、指定されたスループット（一定時間内に処理できるリクエストの量）を予約する仕組みのことです。
Azure VMで言うところのリザーブドインスタンスみたいな感じですね。

PTUを利用することで、スケーラブルなAIサービスを提供し、リクエストの高負荷時にも安定したパフォーマンスを維持することができます。

**PTU**で処理能力を事前に予約することで、応答速度の向上と安定稼働を実現することが出来、主なメリットとして以下のような点があります。


| メリット | 解説 |
| ---- | ---- |
| **予測可能なパフォーマンス** | Azure OpenAIをDeployした処理リソースの占有的な利用が可能なので、均一なワークロードに対して安定した最大待ち時間とスループットを出すことが出来る |
| **予約済み処理機能** | 処理能力を事前に予約することが出来るので、応答速度の向上と安定稼働を実現することが出来る |
| **コスト削減** | 高速なレスポンスや高い可用性が求められる用途に適しているPTUは、高スループットワークロードを実現出来、トークンベースの使用と比較したときのコスト削減につながる場合がある |


要するに、**PTU**を使うことで、パフォーマンスが安定出来、普通に使うよりコスト的にも抑えることが出来るというものです。


### 背景と必要性

通常、Azureのようなクラウドサービスでは、リクエストの量に応じて自動的にリソースがスケールされます。

しかし、急にリクエストが増えると、対応が遅れたり、パフォーマンスが低下することがあります。

そこでPTUを使うと、事前に1秒あたりの処理リクエスト数（スループット）を予約しておくことができるので、リクエストが集中しても安定したパフォーマンスを保てます。

なので、AIサービスの提供者は予測可能なパフォーマンスを確保するために、PTUを活用することが重要です。

## PTUが使用出来るモデルとリージョン

PTUが利用出来るモデルとリージョンは以下の通りです。

- **PTUが使用出来るモデルとリージョン一覧**

| リージョン | gpt-4、0613 | gpt-4、1106-Preview | gpt-4、0125-Preview | gpt-4、turbo-2024-04-09 | gpt-4o、2024 年 5 月 13 日 | gpt-4-32k、0613 | gpt-35-turbo、1106 | gpt-35-turbo、0125 |
|-------------------------|-------------|---------------------|---------------------|-------------------------|--------------------------|------------------|---------------------|---------------------|
| australiaeast            | ✅          | ✅                  | ✅                  | ✅                      | ✅                       | ✅               | ✅                  | ✅                  |
| brazilsouth              | ✅          | ✅                  | ✅                  | -                       | ✅                       | ✅               | ✅                  | -                   |
| canadacentral            | ✅          | -                   | -                   | -                       | -                        | ✅               | -                   | ✅                  |
| canadaeast               | ✅          | ✅                  | -                   | ✅                      | ✅                       | -                | ✅                  | -                   |
| eastus                   | ✅          | ✅                  | ✅                  | ✅                      | ✅                       | ✅               | ✅                  | ✅                  |
| eastus2                  | ✅          | ✅                  | ✅                  | ✅                      | ✅                       | ✅               | ✅                  | ✅                  |
| francecentral            | ✅          | ✅                  | ✅                  | -                       | ✅                       | ✅               | -                   | ✅                  |
| germanywestcentral       | ✅          | ✅                  | ✅                  | ✅                      | ✅                       | ✅               | ✅                  | -                   |
| japaneast                | -           | ✅                  | ✅                  | ✅                      | ✅                       | -                | -                   | ✅                  |
| koreacentral             | ✅          | -                   | -                   | ✅                      | ✅                       | ✅               | ✅                  | -                   |
| northcentralus           | ✅          | ✅                  | ✅                  | ✅                      | ✅                       | ✅               | ✅                  | ✅                  |
| norwayeast               | ✅          | -                   | ✅                  | -                       | -                        | ✅               | -                   | -                   |
| polandcentral            | ✅          | ✅                  | ✅                  | -                       | -                        | ✅               | ✅                  | ✅                  |
| southafricanorth         | ✅          | ✅                  | -                   | ✅                      | -                        | ✅               | ✅                  | -                   |
| southcentralus           | ✅          | ✅                  | ✅                  | ✅                      | ✅                       | ✅               | ✅                  | ✅                  |
| southindia               | ✅          | ✅                  | ✅                  | -                       | ✅                       | ✅               | ✅                  | ✅                  |
| swedencentral            | ✅          | ✅                  | ✅                  | ✅                      | ✅                       | ✅               | ✅                  | ✅                  |
| switzerlandnorth         | ✅          | ✅                  | ✅                  | ✅                      | ✅                       | ✅               | ✅                  | ✅                  |
| switzerlandwest          | -           | -                   | -                   | -                       | -                        | -                | -                   | ✅                  |
| uksouth                  | ✅          | ✅                  | ✅                  | ✅                      | ✅                       | ✅               | ✅                  | ✅                  |
| westus                   | ✅          | ✅                  | ✅                  | ✅                      | ✅                       | ✅               | ✅                  | ✅                  |
| westus3                  | ✅          | ✅                  | ✅                  | ✅                      | ✅                       | ✅               | ✅                  | ✅                  |


リージョン毎にPTUが使えないモデルもあるので、契約時点ではよく確認してから契約する必要がありそうです。

## PTUの利用方法
### Deploy方法について

Azure OpenAI Studio でPTUを用いてデプロイを実施する場合、[デプロイの作成] から ダイアログのデプロイの種類は Provisioned-Managed です。

CLI または API を使用して Azure OpenAI でPTUデプロイを用いてデプロイする場合は、**sku-name** を ProvisionedManaged に設定し、**sku-capacity** はPTU の数を指定します。

```bash
az cognitiveservices account deployment create \
  --name <myResourceName> \
  --resource-group  <myResourceGroupName> \
  --deployment-name MyDeployment \
  --model-name gpt-4 \
  --model-version 0613  \
  --model-format OpenAI \
  --sku-capacity 100 \
  --sku-name ProvisionedManaged 
```

PTU は、プロンプトの処理と補完の生成のため必要なスループットを実現するようにプロビジョニングされたデプロイのサイズ指定に使用できるモデル処理容量の一般の単位です。 

プロビジョニング スループット ユニットは、リージョンごとにクォータとしてサブスクリプションに付与されます。これにより、そのサブスクリプションとリージョンのデプロイに割り当てることができる PTU の最大数が定義されます。

### PTUの計算方法

ここで気になってくるのが、**自分たちが作るアプリケーションがどのくらいのPTUを必要とするのか**ということです。

それはAzure OpenAI Studio の Capacity Plannerで簡単に測定することが出来ます。
ワークロードの見積もりを簡単に取得するには、Azure OpenAI Studio の画面左側ペインより[共有リソース¥クォータ] > [AzureOpenAIプロビジョニング済み] > [容量計算ツール]を開きます。 
これで、Capacity Planner が開き、必要なPTUの数を計算することができます。

https://oai.azure.com/

![Azure](/images/azure_perfectly_understand_ptu/img2.png)

PTUの計算において入力が必要なのは、以下の項目です。
- peak calls per min : モデルに対して予想される 1 分あたりの呼び出し回数
- Tokens in prompt calls : モデルへの各呼び出しのプロンプト内のトークン数
- Tokens in model response : モデルへの各呼び出しから生成されるトークンの数

では、具体的に計算してみましょう。
以下の様に設定してみます。
- peak calls per min : 100回
- Tokens in prompt calls : 300 tokens
- Tokens in model response : 300 tokens

※ちなみに300tokensは264文字ぐらいです。
![Azure](/images/azure_perfectly_understand_ptu/img3.png)

それでは計算結果を見てみましょう。

![Azure](/images/azure_perfectly_understand_ptu/img4.png)

これぐらいのtoken数とリクエスト回数であれば、PTUは50で大丈夫ということがわかりました。


契約時点で不要なPTUを設定することなく数値を検証出来るので、非常に便利ですね。

## PTUのコスト

Azure OpenAI PTUは、プロビジョニングされたスループットのデプロイにおいて、従量課金制の価格に比べて大幅な割引を提供することが出来ます。
これにより、Azure OpenAI のプロビジョニングされたスループットユニットを、1年間または1か月間の期間で事前購入することが可能になります。

(事前にまとめて買うなら、お安くしておきますよ〜という認識でOK)

### 現時点でどれぐらいの費用か(2024/08/18 時点)
PTUは個人のサブスクリプションでも購入が可能です。
Azureの検索窓より **予約** → **作成** → **Azure OpenAI Service Provisioned** を選択してください。
![Azure](/images/azure_perfectly_understand_ptu/img5.png)
![Azure](/images/azure_perfectly_understand_ptu/img6.png)

現在、個人用のサブスクリプションでは、以下の３パターンのPTUを購入することが可能です。
![Azure](/images/azure_perfectly_understand_ptu/img7.png)

それぞれ見てみましょう。

- 1ヶ月契約で前払いする場合
  - 260USDで82%Offのようですね。もうpay-as-you-go(従量課金)で使うのがもったいないと感じるセールっぷりですね。
![Azure](/images/azure_perfectly_understand_ptu/img8.png)

- 1年間契約で月毎の支払いをする場合
  - 221USDで84%Offのようですね。やはり長期で契約した方がお得のようです。
![Azure](/images/azure_perfectly_understand_ptu/img9.png)

- 1年間契約で前払いをする場合
  - 2,652USDで84%Offのようですね。
![Azure](/images/azure_perfectly_understand_ptu/img10.png)

## 最新Updateで何が変わったのか



## まとめ


## 参考資料
- [プロビジョニングされたスループットとは](https://learn.microsoft.com/ja-jp/azure/ai-services/openai/concepts/provisioned-throughput) 
- [Azure OpenAI プロビジョニング 2024 年 8 月の更新プログラム](https://learn.microsoft.com/ja-jp/azure/ai-services/openai/concepts/provisioned-migration)
- [Azure OpenAI で API 管理による PTU/TPM を使用する - スケーリングの特別なソースを使用する](https://github.com/Azure/aoai-apim/blob/main/README.md)
- [Azure OpenAI Service でプロビジョニングされたデプロイの使用を開始する](https://learn.microsoft.com/ja-jp/azure/ai-services/openai/how-to/provisioned-get-started)