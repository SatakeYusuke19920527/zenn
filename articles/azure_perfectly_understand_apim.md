---
title: "【APIM ❤️ OpenAI】Azure API ManagementとAzure OpenAIを組み合わせて効率的な運用を実現する"
emoji: "💴"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["Azure", "APIManagement","Microsoft", "OpenAI", "AOAI"]
published: true
publication_name: "microsoft"
---

![Azure](/images/azure_perfectly_understand_apim/img2.gif)

昨今Azure OpenAI Serviceを用いた様々なアプリケーションが開発されており、非常にワクワクする日が続いている筆者です。

今日ご紹介するのは生成AIを活用したアプリケーションを開発する際に、非常に有益な方法です。

具体的には、Azure API Managementを活用することで、生成AIを活用したアプリケーションの課題を解決する方法をご紹介します。

API ManagementとAzure OpenAI Serviceを組み合わせて構築するシステムを、名付けて  **GenAI Gateway** と呼びます。(ｶｯｺｲｲ)

そんなGenAI Gatewayがどんなものかご紹介したいと思います。

是非、自社のシステムで導入できるかな？という観点で読んでいただけますと幸いです。

それでは見て行きましょう👍

![Azure](/images/azure_perfectly_understand_apim/img1.png)

# Azure OpenAI Serviceを活用したアプリケーションの課題

昨今、ChatGPTのようなチャット形式のアプリケーションから始まり、画像生成や音声認識など様々なアプリケーションが開発されています。

その中でよく生成AIにおけるスケーリングの課題がお客様より寄せられています。
具体的なお問い合わせとしてよくいただくのは以下。

- トークン利用数のトラッキングが出来ず、契約したクォータ数より利用数が超過してしまう
- 複数のAzure OpenAIエンドポイントがあり管理が複雑
- Azure OpenAI ServiceへアクセスするAPIの認証認可をどのようにすればよいかわからない
- トークン数を呼び出し制限をかけたい
- ...etc

# バックエンドプールの負荷分散
![Azure](/images/azure_perfectly_understand_apim/img3.gif)

## ユースケースとメリット
大量のトラフィックを処理するウェブアプリでは、単一のOpenAI ServiceのAPIで全てのリクエストを処理するのは非効率的であり、リソースの限界を超えてしまう場合があります。
負荷分散を使用することで、トラフィックを複数のサーバーに分散させ、各サーバーの負荷を均等に保つことができます。

API Managementでは負荷分散プールを定義することが出来、複数のAzure OpenAI Serviceのエンドポイントを登録することが出来ます。

デフォルトでラウンドロビン方式でリクエストを分散し、API毎に優先度を設定することも可能です。

また、高可用性が求められるシステムにおいて、API Managementのサーキットブレーカー機能を用いてフェイルオーバーが可能です。

サーキットブレーカー機能とは、上記のgif画像のように、なんらかの理由で、片方のAzure OpenAI Serviceのエンドポイントがダウンした場合、API Managementは自動的に別のエンドポイントにトラフィックを転送する機能です。(超便利ですね)


# アクセス制御
![Azure](/images/azure_perfectly_understand_apim/img6.gif)

## ユースケースとメリット
APIへのリクエストに対して、マネージドIDやEntraIDを用いて認証することが出来ます。

また、Azure OpenAI ServiceのAPIキーはAPI Managementに安全に格納することが出来ます。

validate-jwtポリシーを使用することで、Azure OpenAI ServiceのAPIキーをAPI Managementに格納し、API ManagementがAzure OpenAI Serviceにリクエストを送信する際に、APIキーを自動的に付与することが出来ます。

以下は、API Managementのポリシーの例です。

> 💡下記の例では、POSTメソッドで且つjwtのクレームに"finance"が含まれていない場合、HTTPステータスコード403（Forbidden）を返すポリシーです。

```xml
<validate-jwt header-name="Authorization" require-scheme="Bearer" output-token-variable-name="jwt">
    <issuer-signing-keys>
        <key>{{jwt-signing-key}}</key> <!-- signing key is stored in a named value -->
    </issuer-signing-keys>
    <audiences>
        <audience>@(context.Request.OriginalUrl.Host)</audience>
    </audiences>
    <issuers>
        <issuer>contoso.com</issuer>
    </issuers>
    <required-claims>
        <claim name="group" match="any">
            <value>finance</value>
            <value>logistics</value>
        </claim>
    </required-claims>
</validate-jwt>
<choose>
    <when condition="@(context.Request.Method == "POST" && !((Jwt)context.Variables["jwt"]).Claims["group"].Contains("finance"))">
        <return-response>
            <set-status code="403" reason="Forbidden" />
        </return-response>
    </when>
</choose>
```

# ログとモニタリング
![Azure](/images/azure_perfectly_understand_apim/img4.gif)

## ユースケースとメリット
上記の例では、API利用者によるトークン消費のトラッキングが可能です。
また、監査およびモデル改善の為の会話ログの取得もかなり有益です。

また、azure-openai-emit-token-metric ポリシーは、Azure OpenAI Service API でトークンの使用に関するメトリックを Application Insights に送信することも出来ます。
トークン数のメトリックには、合計トークン、プロンプト トークン、完了トークンが含まれます。

リクエスト/レスポンスの詳細やトークンの使用状況を簡単に追跡できるのがこの構成最大のメリットですね。

# トークンレート制限
![Azure](/images/azure_perfectly_understand_apim/img5.gif)

## ユースケースとメリット
ユーザがトークンの使用量を超えると、API Managementは 429 をreturnするように設定することが出来ます。

API利用者毎に接続キーを用いてTPM(token per minutes / 1分あたりのトークン数)を設定することが出来ます。

また、API Managementのポリシーを使用して、トークンの使用量を調整することも出来ます。
上記により、過剰なリクエストを防ぎ、サービスの安定性を保つことができます。

以下は、API Managementのポリシーの例です。
> 💡下記の例では、1分あたりのtokenの使用量を5000に制限しています。

```xml
<policies>
    <inbound>
        <base />
        <azure-openai-token-limit
            counter-key="@(context.Request.IpAddress)"
            tokens-per-minute="5000" estimate-prompt-tokens="false" remaining-tokens-variable-name="remainingTokens" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```

# GPT-4o 推論
![Azure](/images/azure_perfectly_understand_apim/img7.gif)

## ユースケースとメリット
API Managementはモデル **GPT-4o** を使うことが出来ます。GPT-4o（「o 」は 「omni 」の意）は、テキスト、オーディオ、ビデオの入力を組み合わせて扱うように設計されており、テキスト、オーディオ、イメージ形式の出力を生成することができます。

テキストだけでなく、画像や音声、映像も含めた多様なデータを扱うことができるため、より多様なアプリケーションを開発することが可能になります。

# コンテンツフィルタリング
![Azure](/images/azure_perfectly_understand_apim/img8.gif)

## ユースケースとメリット
Azure API ManagementとAzure AI Content Safetyを統合して、潜在的に攻撃的、危険、または望ましくないコンテンツをフィルタリングすることが出来ます。

上記画像の例では、リクエストをContent Safety APIを用いて、Azure AI Content Safetyサービスに送信し、コンテンツの安全性を確認してから、Azure OpenAI Serviceにリクエストを送信するケースになります。

Azure AI Content Safetyと使用すると、性的コンテンツ、暴力、憎悪、自傷行為に関するテキストや画像を検出することが出来ます。
そのようなリスクを排除したい場合には、上記の構成を使用することで、より安全なアプリケーションを開発することが可能です。

Azure AI Content Safetyでは以下の機能があります。
用途に合わせて使用することで、より安全なアプリケーションを開発することが可能です。

| 種類                                   | 機能                                                                                                                                                     |
|----------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------|
| プロンプト シールド (プレビュー)       | 大規模言語モデルに対するユーザー入力攻撃のリスクがないか、テキストをスキャンします。 クイックスタート                                                          |
| グラウンデッドネス検出 (プレビュー)     | 大規模言語モデル (LLM) のテキスト応答が、ユーザーが提供するソース資料に基づいているかどうかを検出します。 クイックスタート                                      |
| 保護対象テキストの検出 (プレビュー)     | 既知のテキスト コンテンツ (曲の歌詞、記事、レシピ、一部の Web コンテンツなど) がないか、AI によって生成されたテキストをスキャンします。 クイックスタート           |
| カスタム カテゴリ API (プレビュー)      | 独自のカスタム コンテンツ カテゴリを作成してトレーニングし、テキストの一致をスキャンできます。 クイックスタート                                               |
| カスタム カテゴリ (急速) API (プレビュー) | 新たな有害コンテンツ パターンを定義し、テキストと画像をスキャンして一致を確認できます。 攻略ガイド                                                         |
| テキスト分析 API                        | 性的コンテンツ、暴力、憎悪、自傷行為に関するテキストを複数の重大度レベルでスキャンします。                                                                     |
| 画像分析 API                            | 性的コンテンツ、暴力、憎悪、自傷行為に関する画像を複数の重大度レベルでスキャンします。                                                                       |


# 最後に

いかがだったでしょうか。
今回は、Azure API ManagementとAzure OpenAI Serviceを組み合わせて、生成AIを活用したアプリケーションの様々な課題を解決する方法をご紹介しました。

次は、実際にAzure API ManagementとAzure OpenAI Serviceを組み合わせて、GenAI Gatewayを構築するハンズオン記事を書いていきたいと思います。

それでは👋

## 参考資料
- [https://github.com/Azure-Samples/AI-Gateway](https://github.com/Azure-Samples/AI-Gateway) 