---
title: "【APIM ❤️ OpenAI】Azure API ManagementとAzure OpenAIを組み合わせて効率的な運用を実現する"
emoji: "💴"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["Azure", "APIManagement","Microsoft", "OpenAI", "AOAI"]
published: false
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

# [バックエンドプールの負荷分散](https://github.com/Azure-Samples/AI-Gateway/blob/main/labs/backend-pool-load-balancing/backend-pool-load-balancing.ipynb)
![Azure](/images/azure_perfectly_understand_apim/img3.gif)



# [ログとモニタリング](https://github.com/Azure-Samples/AI-Gateway/blob/main/labs/built-in-logging/built-in-logging.ipynb)
![Azure](/images/azure_perfectly_understand_apim/img4.gif)




# []()



# []()

それでは👋

## 参考資料
- [https://github.com/Azure-Samples/AI-Gateway](https://github.com/Azure-Samples/AI-Gateway) 