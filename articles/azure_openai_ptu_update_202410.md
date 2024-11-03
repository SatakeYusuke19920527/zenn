---
title: "【Azure OpenAI Service 最新Update】 Deploy方法が増えてPTUが安くなったよ🚀"
emoji: "💰"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["microsoft", "azure", "azureopenai", "ptu", "cost"]
published: false
publication_name: "microsoft"
---

# はじめに
2024/10/28 にAzure OpenAI Serviceに大幅なUpdateが入りました。
その中で、Deploy方法が増えて、PTUが安くなったので、その変更点をまとめてみました。

# 前提 ~ PTUってそもそもなに？ ~
PTUがそもそもなに？という方も多いと思います。
以下の記事にがっつりまとめてみましたので、詳しく知りたい方はこちらの記事をご参照ください。

https://zenn.dev/microsoft/articles/azure_perfectly_understand_ptu

ざっくりまとめますと以下です。

- PTUはProvisioned Throughput Unitの略で、Azure OpenAI ServiceのモデルのDeploy方法の一つ。Batch, Standard, **PTU**というDeploy方法がある。
- Deploy段階で、今月どれぐらい使うかを指定して専用のコンピューティングリソースを確保してDeployするので、OpenAIのパフォーマンスが安定する。
- 事前に予約したユニット数支払う為、コスト予測が簡単

StandardとPTUの違いをまとめると以下です。


|                 | Standard                                | PTU                                      |
|-----------------|-----------------------------------------|------------------------------------------|
| **スケーラビリティ** | トークン数の上限値あり                   | 必要な量を事前に確保                    <br> 毎月予約量を変更可能                 |
| **パフォーマンス**  | 共有のコンピューティングリソースを利用     <br> レイテンシーのばらつき・遅れの可能性あり | 専用のコンピューティングリソースを利用   <br> レイテンシーの安定性が高い           |
| **コスト**       | 毎月のトークン利用数に応じた支払い         <br> コスト予測 = 複雑                    | 事前に予約したユニット数支払い          <br> コスト予測 = 簡単                   |

:::message

PTUはDeploy時にAOAIをどれだけ使用するか、必要な量を事前に予約しておいて、専用のコンピューティングリソースを利用することが出来る。
レイテンシーの安定性が高く、コスト予測も簡単になるものと理解出来ていればOKです。

:::

# Updateその① ~ Deploy方法が増えました ~

![Azure](/images/azure_openai_ptu_update_202410/img2.png)

DataZonesという新しいDeploy方法が追加されました。
まずは、Standard, PTUの下にあるそれぞれのDeploy方法について確認していきましょう。

## Deploy方式
### Regional
AOAIをDeployして、データ・推論共にDeployしたリージョンのGPUリソースを使用して行われます。

### Global
AOAIをDeployして、データはDeployしたリージョンに留まりますが、推論はGlobalの空いているGPUリソースを使用して行われます。これにより、Regional よりも高いクォータとより多くのモデルを利用できます。

### Data Zones
AOAIのDeploy場所は指定して、推論は指定した地域の空いているGPUリソースを使用して行われます。Regional よりも高いクォータを確保出来、地理的な要件がある方にも対応出来ます。


## それぞれの使い所
```Regional```方式はデータと推論を同じリージョンで行いたい場合に使用します。
国外にデータを出してはいけない要件を持つ方におすすめです。

:::message
💡こんなあなたに...
→AOAIのDeployは日本で。推論ももちろん日本で。情報を海外に出せないやで。
:::


```Global```方式は、データはリージョン内に留めたいが、推論はGlobalで行うことで、Standardよりよりクォータの引き上げが可能です。国外にデータを出してもOKな方はクォータの許容量もこちらの方が大きいので、こちらを選択すると良いでしょう。

:::message
💡こんなあなたに...
→AOAIのDeployは日本で。推論は別にどこでもええやで。空いているGPUリソース使ってクォータ上がるなら嬉しいやで。
:::

```Data Zones```方式は、データはリージョン内に留めたいが、クォータ制限を引き上げたい場合に使用します。データはリージョン内に留めることが出来るので、国外にデータを出してはいけない要件を持つ方におすすめです。

:::message
💡こんなあなたに...
→AOAIのDeployは日本で。推論はアメリカかEUやったら許したるやで。でもRegionalよりはクォータは欲しいやで。
:::

```現在はEUとUSのリージョンのみが指定可能ですが、これからどんどん増えていく予定とのこと。```


# Updateその② ~ PTUがお安くなりました。 ~

![Azure](/images/azure_openai_ptu_update_202410/img1.png)

# まとめ

# 参考文献
https://azure.microsoft.com/en-us/blog/accelerate-scale-with-azure-openai-service-provisioned-offering/

https://learn.microsoft.com/ja-jp/azure/ai-services/openai/concepts/provisioned-throughput