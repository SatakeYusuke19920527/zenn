---
title: 'Microsoft Learn をわかりやすくしてみた【Azure App Service 編】'
emoji: '🧑‍🏫'
type: 'tech' # tech: 技術記事 / idea: アイデア
topics: ['Azure', 'Azure App Service', 'Microsoft learn']
published: false
---

# はじめに

この記事は以下の Microsoft Learn の解説ページになります。

AZ-204: Azure App Service Web Apps を実装する
https://learn.microsoft.com/ja-jp/training/paths/create-azure-app-service-web-apps/

# 目次

1. Azure App Service を試す
2. Web アプリの設定を構成する
3. Azure App Service でアプリをスケーリングする
4. Azure App Service デプロイ スロットについて詳しく知る

# Azure App Service を試す

## ざっくり概要

![app service](/images/explain_azure_app_service/appservice.png)

Azure App Service は、Web アプリが作れたり、API が作れたりするやつと覚えてもらったら OK です。
いろんなプログラミング言語で使用でき、Windows ベースで作るか Linux ベースで作るかを選択できます。

## 特徴

#### 組み込みの自動スケール

Azure App Service は、スケールアップとスケールダウンまたはスケールアウトとスケールインの機能が搭載されています。
Web アプリの使用量に応じて、Web アプリをホスティングしている基盤のマシンのリソースをスケールアップまたはスケールダウンできちゃいます。

#### CI/CD

- 自動デプロイ
  - Azure DevOps Services, GitHub, Bitbucket やらにコードを push して自動 Deploy することが可能(やっぱり主流)
- 手動デプロイ
  - CLI, zip, FTP のサービスを使って Deploy することも可能(やってる人あんまりみたことない)

## 価格レベル

価格レベルはざっくり以下。
ざっくり特徴を押さえておきましょう。

> Free、Shared、Basic、Standard、Premium、PremiumV2、PremiumV3、Isolated、IsolatedV2

| 価格レベル                                     | 特徴                                                                                                                                                                                                                                                                                                    |
| ---------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Free, Shared                                   | 共有コンピューティング: 2 つの基本レベルである Free と Shared は、他のお客様のアプリを含む他の App Service アプリと同じ Azure VM 上でアプリを実行します。 これらのレベルは共有リソースで実行される各アプリに CPU クォータを割り当て、リソースはスケールアウトできません。                               |
| Basic、Standard、Premium、PremiumV2、PremiumV3 | 専用のコンピューティング: Basic、Standard、Premium、PremiumV2、PremiumV3 の各レベルの場合、アプリは専用の Azure VM 上で実行されます。 同じ App Service プラン内のアプリのみが同じコンピューティング リソースを共有します。 レベルが高いほど、スケールアウトに使用できる VM インスタンスが多くなります。 |
| Isolated, IsolatedV2                           | Isolated: Isolated レベルと IsolatedV2 レベルでは、専用の Azure VM が専用の Azure Virtual Network で実行されます。 アプリに対して、コンピューティングの分離に加え、ネットワークの分離がされます。 最大のスケールアウト機能を提供します。                                                                |

## App Service の認証と承認について
