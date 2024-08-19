---
title: "【APIM ❤️ OpenAI】Azure API ManagementとAzure OpenAIを組み合わせて効率的な運用を実現する"
emoji: "💴"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["Azure", "APIManagement","Microsoft", "OpenAI", "AOAI"]
published: false
publication_name: "microsoft"
---

![Azure](/images/azure_perfectly_understand_apim/img1.png)

昨今Azure OpenAI Serviceを用いた様々なアプリケーションが開発されており、非常にワクワクする日が続いている筆者です。

今日ご紹介するのはGenerativeAIを活用したアプリケーションを開発する際に、非常に有益な方法であるAzure API Managementを活用し、GenerativeAIを活用したアプリケーションの課題を解決する方法をご紹介します。

API ManagementとAzure OpenAI Serviceを組み合わせて構築するシステムを、**GenAI Gateway** と呼ぶらしいです。(ｶｯｺｲｲ)

そんなGenAI Gatewayがどんなものかご紹介します。

# Azure OpenAI Serviceを活用したアプリケーションの課題

![Azure](/images/azure_perfectly_understand_apim/img2.gif)

昨今、ChatGPTのようなチャット形式のアプリケーションから始まり、画像生成や音声認識など様々なアプリケーションが開発されています。

その中でよく生成AIにおけるスケーリングの課題がお客様より寄せられています。
具体例としては以下。
- トークン利用数のトラッキングが出来ず、契約したクォータ数より利用数が超過してしまう
- 複数のAzure OpenAIエンドポイントがあり管理が複雑
- Azure OpenAI ServiceへアクセスするAPIの認証認可をどのようにすればよいかわからない
- トークン数を呼び出し制限をかけたい
- ...etc



それでは👋

## 参考資料
- [https://github.com/Azure-Samples/AI-Gateway](https://github.com/Azure-Samples/AI-Gateway) 