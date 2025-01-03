---
title: "GitHub各プランの機能について徹底解説"
emoji: "🧑‍🎄"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["GitHub", "GitHubCopilot", "microsoft", "githubactions", "githubenterprise"]
published: true
publication_name: microsoft
---

![](https://storage.googleapis.com/zenn-user-upload/634835d97a3a-20241225.png)

# はじめに
最近よくお客様から**GitHub Enterpriseにすると何がいいの？**とか、**GitHub Copilot使ってみたい**というお声が多くなってきました。
GitHubみなさん使われていますが、使ってない機能がいっぱいあるかと存じます。

今回はGitHubの料金プラン別に使える機能についてまとめてみました。

是非ご参考になれば幸いです。

# そもそもGitHubとは？
![](https://storage.googleapis.com/zenn-user-upload/9eb5f8880bc1-20241225.png)

GitHubとは開発者のためのプラットフォームでオープンソースプロジェクトやビジネスユースまで、GitHub上にソースコードをホスティングすることで数百万人もの他の開発者と一緒にコードのレビューを行ったり、プロジェクトの管理をしながら、ソフトウェアの開発を行うことができます。

GitHubとMicrosoftはもともと別の会社でしたが、2018年にMicrosoftがGitHubを75億ドルで買収しました。
詳細は以下のリンクを参照してください。

https://news.microsoft.com/ja-jp/2018/06/05/microsoft-to-acquire-GitHub-for-7-5-billion/?msockid=08206c1cd8ec67cb10f378e6d9ce6608

# GitHubの料金プランまとめ

GitHubでは、個人・チーム・企業など利用規模や目的に合わせて複数のプランが用意されています。

大きく分けて3つのプランが存在します。
1. Free : 個人向けの基本プラン
2. Team : 個人および小規模プロジェクト用の高度なコラボレーションが出来るプラン
3. Enterprise : 大規模組織向けのセキュリティやコンプライアンスを満たすプラン

以下は主要なプランの概要と特徴です。


# 各プランの特徴について

Free、Team、Enterpriseの各プランで出来ることをそれぞれの項目毎に以下にまとめました。

## 価格
| #          | Free          | Team                                  | Enterprise                                                                                                                                        |
|:---------------:|:--------------------------------------:|--------------------------------------------------------------------------------------------------------------------------------------------------|----|
| **価格(1ユーザあたり)**        | 無料                                   | $4/month<br/>$48/year | $21/month<br/>$252/year|


### コードの管理

| #          | Free          | Team                                  | Enterprise                                                                                                                                        |
|:---------------:|:--------------------------------------:|--------------------------------------------------------------------------------------------------------------------------------------------------|----|
| **public repository**        | 無制限のパブリック/プライベートリポジトリ | 無制限のパブリック/プライベートリポジトリ | 無制限のパブリック/プライベートリポジトリ|
| **private repository**        | 無制限のパブリック/プライベートリポジトリ | 無制限のパブリック/プライベートリポジトリ | 無制限のパブリック/プライベートリポジトリ|


## コードワークフロー

| #          | Free          | Team                                  | Enterprise                                                                                                                                        |
|:---------------:|:--------------------------------------:|--------------------------------------------------------------------------------------------------------------------------------------------------|----|
| **GitHub Codespaces**  | -            | 32コア<br/>1時間あたり$0.18から開始  | 32コア<br/>1時間あたり$0.18から開始  |
| **GitHub Actions**  | 2,000分/month (public repositoryでは無料)            | 3,000分/month (public repositoryでは無料)  | 50,000分/month (public repositoryでは無料) |
| **GitHub Packages**  | 500MBのGitHub Packagesストレージ (public repositoryでは無料)            | 2GBのGitHub Packagesストレージ (public repositoryでは無料)   | 50GBのGitHub Packagesストレージ (public repositoryでは無料)  |
| **コードレビュー**  | 可能            | 可能   | 可能  |
| **pull request**  | 可能            | 可能   | 可能  |
| **保護されたブランチ**  | **public repositoryでのみ** 選択したコラボレータによるレビューを要求したり、特定のコントリビュータのみが特定のブランチで作業できるようにするなど、コードブランチのマージ方法に制限をかけることが可能            | 選択したコラボレータによるレビューを要求したり、特定のコントリビュータのみが特定のブランチで作業できるようにするなど、コードブランチのマージ方法に制限をかけることが可能  | 選択したコラボレータによるレビューを要求したり、特定のコントリビュータのみが特定のブランチで作業できるようにするなど、コードブランチのマージ方法に制限をかけることが可能  |
| **コードの所有者**  | **public repositoryでのみ**所有するコードに変更が加えられた場合に、選択したコントリビュータによるレビューや承認を自動で要求可能            | 所有するコードに変更が加えられた場合に、選択したコントリビュータによるレビューや承認を自動で要求可能  | 所有するコードに変更が加えられた場合に、選択したコントリビュータによるレビューや承認を自動で要求可能   |
| **pull requestのドラフト作成**  | **public repositoryでのみ**正式なレビューに提出する前に、簡単にPull Requestについての議論やコラボレーションを行うことが可能            | 正式なレビューに提出する前に、簡単にPull Requestについての議論やコラボレーションを行うことが可能  | 正式なレビューに提出する前に、簡単にPull Requestについての議論やコラボレーションを行うことが可能   |
| **複数人にpull requestをアサイン**  | **public repositoryでのみ**複数人にPull Requestをアサイン可能            | 複数人にPull Requestをアサイン可能  | 複数人にPull Requestをアサイン可能   |
| **pull requestに複数のレビュー担当者**  | **public repositoryでのみ**複数のユーザまたはTeamにPull Requestのレビューをアサイン可能            | 複数のユーザまたはTeamにPull Requestのレビューをアサイン可能  | 複数のユーザまたはTeamにPull Requestのレビューをアサイン可能   |
| **リポジトリインサイト**  | **public repositoryでのみ**リポジトリ内のアクティビティとコントリビューションに関するデータを、傾向を含めて確認できます。このデータを使用して、コラボレーションを改善し、開発をより迅速かつ効果的に行うことができます。            | リポジトリ内のアクティビティとコントリビューションに関するデータを、傾向を含めて確認できます。このデータを使用して、コラボレーションを改善し、開発をより迅速かつ効果的に行うことができます。  | リポジトリ内のアクティビティとコントリビューションに関するデータを、傾向を含めて確認できます。このデータを使用して、コラボレーションを改善し、開発をより迅速かつ効果的に行うことができます。   |
| **定期リマインダー**  | **public repositoryでのみ**指定した時間に、あなたやあなたのチームにメッセージを送信します。            | 指定した時間に、あなたやあなたのチームにメッセージを送信します。  | 指定した時間に、あなたやあなたのチームにメッセージを送信します。   |
| **コードレビューアサインの自動化**  | **public repositoryでのみ**2つのアルゴリズムのいずれかに基づいて、チームのメンバーにコードレビューを自動的にアサインします。            | 2つのアルゴリズムのいずれかに基づいて、チームのメンバーにコードレビューを自動的にアサインします。  | 2つのアルゴリズムのいずれかに基づいて、チームのメンバーにコードレビューを自動的にアサインします。   |

## コラボレーション
| #          | Free          | Team                                  | Enterprise                                                                                                                                        |
|:---------------:|:--------------------------------------:|--------------------------------------------------------------------------------------------------------------------------------------------------|----|
| **public repositoryのコラボレータ**        | 無制限 | 無制限 | 無制限|
| **private repositoryのコラボレータ**        | 無制限 | 1ユーザあたり $48/year | 1ユーザあたり $252/year|
| **issues**        | バグ、機能拡張、その他のリクエストを追跡し、作業に優先順位を付け、変更が提案およびマージされた時に関係者と連絡を取り合います。 | バグ、機能拡張、その他のリクエストを追跡し、作業に優先順位を付け、変更が提案およびマージされた時に関係者と連絡を取り合います。 | バグ、機能拡張、その他のリクエストを追跡し、作業に優先順位を付け、変更が提案およびマージされた時に関係者と連絡を取り合います。|
| **プロジェクトテーブルとプロジェクトボード**        | ワークフローに合わせてアレンジできるカスタムフィールドを使用して、Issue、Pull Request、メモを可視化して管理できます。 | ワークフローに合わせてアレンジできるカスタムフィールドを使用して、Issue、Pull Request、メモを可視化して管理できます。 | ワークフローに合わせてアレンジできるカスタムフィールドを使用して、Issue、Pull Request、メモを可視化して管理できます。|
| **マイルストーン**        | リポジトリ内のIssueまたはPull Requestのグループの進捗状況を追跡し、グループをプロジェクト全体の目標にマッピングします。| リポジトリ内のIssueまたはPull Requestのグループの進捗状況を追跡し、グループをプロジェクト全体の目標にマッピングします。| リポジトリ内のIssueまたはPull Requestのグループの進捗状況を追跡し、グループをプロジェクト全体の目標にマッピングします。|
| **チームディスカッション**        | 特定のプロジェクトやIssueに関係なく、どんなトピックでも議論できます。チームディスカッションへのアクセスは制限できます。また、ディスカッション参加者に更新情報を通知できるほか、どこからでもリンクできます。| 特定のプロジェクトやIssueに関係なく、どんなトピックでも議論できます。チームディスカッションへのアクセスは制限できます。また、ディスカッション参加者に更新情報を通知できるほか、どこからでもリンクできます。| 特定のプロジェクトやIssueに関係なく、どんなトピックでも議論できます。チームディスカッションへのアクセスは制限できます。また、ディスカッション参加者に更新情報を通知できるほか、どこからでもリンクできます。|
| **OrganizationおよびTeamの管理**        | プロジェクトへのアクセスをチーム単位、または個人ユーザ単位で管理できます| プロジェクトへのアクセスをチーム単位、または個人ユーザ単位で管理できます| プロジェクトへのアクセスをチーム単位、または個人ユーザ単位で管理できます|
| **GitHub Pagesとwiki**  | **public repositoryでのみ**プロジェクトのためのドキュメントや簡単なウェブサイトをwiki形式でホストし、コントリビュータはウェブやコマンドラインで簡単に編集可能            | プロジェクトのためのドキュメントや簡単なウェブサイトをwiki形式でホストし、コントリビュータはウェブやコマンドラインで簡単に編集可能  | プロジェクトのためのドキュメントや簡単なウェブサイトをwiki形式でホストし、コントリビュータはウェブやコマンドラインで簡単に編集可能   |
| **issueを複数人にアサイン**  | **public repositoryでのみ**1つのIssueに複数人をアサインできます。            | 1つのIssueに複数人をアサインできます。  | 1つのIssueに複数人をアサインできます。 |

### セキュリティとコンプライアンス
| #          | Free          | Team                                  | Enterprise                                                                                                                                        |
|:---------------:|:--------------------------------------:|--------------------------------------------------------------------------------------------------------------------------------------------------|----|
| **GitHub Code Scanning**  | **public repositoryでのみ**実稼働環境に展開する前に脆弱性を自動的に検知し、修正します。セキュリティ研究コミュニティと世界最先端のセマンティックコード分析エンジンを原動力としています。            | **public repositoryでのみ**実稼働環境に展開する前に脆弱性を自動的に検知し、修正します。セキュリティ研究コミュニティと世界最先端のセマンティックコード分析エンジンを原動力としています。 | GitHub Advanced Securityで利用可能|
| **シークレットスキャン**  | **public repositoryでのみ**シークレットトークンががコードベースで誤って公開された場合に、通知を受けることができます。            | **public repositoryでのみ**シークレットトークンががコードベースで誤って公開された場合に、通知を受けることができます。 | GitHub Advanced Securityで利用可能|
| **依存関係のレビュー**  | **public repositoryでのみ**Pull Request中に新たに導入される依存関係のセキュリティ上の影響をマージ前に把握できます。            | **public repositoryでのみ**Pull Request中に新たに導入される依存関係のセキュリティ上の影響をマージ前に把握できます。 |GitHub Advanced Securityをオプション購入することで、GitHub Enterprise Cloud環境で利用可能|
| **Dependabotアラート**  | リポジトリ内の依存関係に影響を与える新しい脆弱性があった場合に通知を受けることができます。 | リポジトリ内の依存関係に影響を与える新しい脆弱性があった場合に通知を受けることができます。 | リポジトリ内の依存関係に影響を与える新しい脆弱性があった場合に通知を受けることができます。 |
| **Dependabotセキュリティアップデート**  | 脆弱な依存関係を安全なバージョンに更新するPull Requestを自動的に開くことで、プロジェクトを安全な状態に維持します。 | 脆弱な依存関係を安全なバージョンに更新するPull Requestを自動的に開くことで、プロジェクトを安全な状態に維持します。 | GitHub Enterprise Cloudで利用可能 |
| **Dependabotバージョンアップデート**  | 古くなった依存関係を更新するPull Requestを自動的に開くことで、プロジェクトを最新の状態に維持します。 | 古くなった依存関係を更新するPull Requestを自動的に開くことで、プロジェクトを最新の状態に維持します。 | GitHub Enterprise Cloudで利用可能 |
| **必須レビュー**  | **public repositoryでのみ**Pull Requestが特定の数のレビュー承認を得るまでは、保護されたブランチをコラボレータが変更できないようにします。 | Pull Requestが特定の数のレビュー承認を得るまでは、保護されたブランチをコラボレータが変更できないようにします。 | Pull Requestが特定の数のレビュー承認を得るまでは、保護されたブランチをコラボレータが変更できないようにします。 |
| **必須ステータスチェック**  | **public repositoryでのみ**必須のCIテストにすべてパスするまでは、保護されたブランチをコラボレータが変更できないようにします。 | 必須のCIテストにすべてパスするまでは、保護されたブランチをコラボレータが変更できないようにします。 | 必須のCIテストにすべてパスするまでは、保護されたブランチをコラボレータが変更できないようにします。 |
| **GitHub Security Advisories**  | **public repositoryでのみ**リポジトリで検出されたセキュリティの脆弱性に関する情報を非公開で議論し、修正して公開します。 | **public repositoryでのみ**リポジトリで検出されたセキュリティの脆弱性に関する情報を非公開で議論し、修正して公開します。 | GitHub Enterprise Cloudで利用可能 |
| **ロールベースのアクセス制御**  | コード、データ、および設定へのユーザのアクセスレベルを制限できます。 | コード、データ、および設定へのユーザのアクセスレベルを制限できます。 | コード、データ、および設定へのユーザのアクセスレベルを制限できます。 |
| **2段階認証の強制**  | GitHubにログインする際には、2要素認証 (2FA) を利用することで、セキュリティをさらに強化できます。 | GitHubにログインする際には、2要素認証 (2FA) を利用することで、セキュリティをさらに強化できます。 | GitHubにログインする際には、2要素認証 (2FA) を利用することで、セキュリティをさらに強化できます。 |
| **監査ログ**  | Organizationのメンバーが実行したアクションを迅速にレビューします。 | Organizationのメンバーが実行したアクションを迅速にレビューします。 | Organizationのメンバーが実行したアクションを迅速にレビューします。 |
| **監査ログAPI**  | -            | -  | GitHub Enterprise CloudのOrganization管理者は、GraphQL APIを使用してログイベントにアクセスしたり、 Organization内のアクティビティを監視したりできるようになりました。   |
| **GitHub Connect**  | -            | -  | GitHub Enterprise ServerインスタンスとGitHub Enterprise Cloudの間で機能とワークフローを共有できます。   |
| **SAMLシングルサインオン**  | -            | -  | アイデンティティプロバイダを使用して、GitHubのユーザやアプリケーションのIDを管理します。   |
| **LDAP**  | -            | -  | 既存のアカウントを使用してGitHub Enterprise Serverにアクセスし、リポジトリへのアクセスを一元管理します。   |
| **IP許可リスト**  | -            | -  | 許可されている既知のIPアドレスだけにアクセスを制限します。   |


> **GitHub Advanced Security:** 実稼働環境に展開する前に脆弱性を自動的に検知し、修正します。コード内のシークレットトークンが誤って公開された場合には、通知を受けることができます。

## GitHub Marketplaceとインテグレーション
| #          | Free          | Team                                  | Enterprise                                                                                                                   |
|:---------------:|:--------------------------------------:|--------------------------------------------------------------------------------------------------------------------------------------------------|----|
| **GitHub Apps**  | 無制限  | 無制限  | 無制限   |
| **ステータスチェック**  | リポジトリにコミットされたコードに対してGitHubが自動的に実行するテストを定義し、失敗やその原因についての詳細を取得します。  | リポジトリにコミットされたコードに対してGitHubが自動的に実行するテストを定義し、失敗やその原因についての詳細を取得します。  | リポジトリにコミットされたコードに対してGitHubが自動的に実行するテストを定義し、失敗やその原因についての詳細を取得します。   |
| **pre-receiveフック**  | -  | - | GitHub Enterprise Serverで利用可能。<br/>プッシュの内容に基づいて、プッシュを自動的に承認または拒否するための要件を作成できます。   |

## サポートと導入

| #          | Free          | Team                                  | Enterprise                                                                                                                                        |
|:---------------:|:--------------------------------------:|--------------------------------------------------------------------------------------------------------------------------------------------------|----|
| **GitHubコミュニティによるサポート**  | 利用可能 | 利用可能   | 利用可能  |
| **スタンダードサポート**  | - | 利用可能   | 利用可能  |
| **プレミアムおよびプレミアムプラスサポート**  | - | -  | オプション購入で利用可能 |
| **電話サポート**  | - | -  | プレミアムサポート購入で利用可能 |
| **請求書**  | - | -  | クレジットカードを使用せず、請求書で支払いが可能 |
| **自己ホスト型導入**  | - | -  | オンプレミスアプライアンスや、自己管理型クラウドテナント向けの自己ホスト型GitHubをGitHub Enterprise Serverにて利用可能 |


> **GitHubコミュニティによるサポート:** GitHubに関する質問や問題のほとんどは、GitHubコミュニティフォーラムで解決できます。

> **スタンダードサポート:** GitHubの使用時に発生した問題をWebフォームでサブミットすることでGitHub Supportが対応します。本サポートは英語のみです。日本語でのサポートが必要な場合は、GitHub Enterpriseをご利用ください。

> **プレミアムサポート:** プレミアムでは、日本語でのお問合せに対応いたしますが、30分間のSLAと24時間365日のウェブおよび電話サポートを利用する場合は英語での対応のみとなります。プレミアムプラスでは、プレミアムのすべての機能に加えて、専任のサポートアカウントマネージャーが日本語で対応します。


## その他、Enterpriseで利用可能な機能
| #          | Free          | Team                                  | Enterprise                                                                                                                                        |
|:---------------:|:--------------------------------------:|--------------------------------------------------------------------------------------------------------------------------------------------------|----|
| **SCIMを仕様したユーザプロビジョニング**  | -            | -  | GitHub Enterprise Cloudには、エンタープライズアカウントを作成するオプションが含まれています。これにより、複数のOrganization間でのコラボレーションが可能になり、管理者は一元的に可視化および管理することができます。また、同じユーザが複数のOrganizationに属している場合、ライセンスコストを節約することができます。   |
| **複数のOrganizationを一元管理するEnterprise Account**  | -            | -  | IdPでアクセス権を付与すると、Organizationに参加するようにメンバーを自動的に招待します。SAML IdPでGitHub Organizationへのメンバーのアクセス権を削除した場合、メンバーはそのGitHub Organizationから自動的に削除されます。   |
| **Enterprise Managed Users**  | -            | -  | アイデンティティプロバイダ (IdP) を通じて、企業のメンバーのユーザアカウントを所有および管理可能   |
| **環境保護ルールとシークレット**  | -            | -  | ワークフロージョブが環境を参照する場合、そのジョブはすべての環境保護ルールにパスするまで開始されません。また、すべての環境保護ルールの条件を満たすまで、ジョブは環境内で定義されているシークレットにアクセスすることもできません。   |
| **SOC1, SOC2のType 2年次レポート**  | -            | -  | AICPA System and Organization Controls (SOC) 1 Type 2およびSOC 2 Type 2レポートを提供します。これらは、IAASB国際保証業務基準のISAE 3000およびISAE 3402に準拠しています。   |
| **FedRAMP Tailored Authority to Operate(ATO)**  | -            | -  | GitHubのプラットフォームは、米国連邦政府パートナーによって設定されたセキュリティ基準の、低インパクトSoftware as a Service (SaaS) ベースラインを満たしています。その認証の下、政府機関のユーザはプロジェクトをGitHub Enterprise Cloudにホストすることができます。   |
| **高度な監査**  | -            | -  | Organizationのメンバーが実行したアクションを迅速にレビューします。監査ログデータのコピーを保持することで、IPを確実に保護し、Organizationのコンプライアンスを維持します。   |


## GitHub Copilotについて
![](https://storage.googleapis.com/zenn-user-upload/94b9363f30e7-20241225.png)

GitHub CopilotについてはFreeプランからでも利用可能

| #          | Free          | Team                                  | Enterprise                                                                                                                                        |
|:---------------:|:--------------------------------------:|--------------------------------------------------------------------------------------------------------------------------------------------------|----|
| **オプション**  | GitHub Copilotの利用可能            | GitHub Copilotの利用可能   | GitHub Copilotの利用可能  |


## 割引について
ディスカウントについても言及されています。

**割引価格は、クレジットカードまたはPayPalで支払う新規の年間契約にのみ適用されます。2年目からディスカウントは適用されません。契約更新の30日前までに、初年度のディスカウント価格から変更されることをメールにてお知らせします。**

月間契約ではなく、年間契約でディスカウントをもらって契約するのが良さそうですね。


# GitHubの支払いをAzure Subscriptionに紐づける
Githubの組織アカウントをAzure Subscriptionに紐づけることで、AzureのサブスクリプションでGitHubの支払いを行うことができます。

GitHub アカウントを Azure にリンクすると、その時点から開始する使用量ベースのコストは Azure を通じて請求され、毎月 1 日に請求されます。ただし、GitHub プランの料金など、残りの GitHub 料金は、通常の請求日に請求されます。

たとえば、6 月 16 日に Azure サブスクリプションを組織アカウントにリンクし、GitHub Copilot Business サブスクリプションも持っているとします。その日以降、Copilot Business の使用コストは Azure の請求書に含まれ、7 月 1 日に請求されます。ただし、6 月 16 日より前に Copilot Business で発生した料金は、アカウントの通常の請求日に GitHub を通じて個別に請求されます。

AzureとGitHub両方使っていて、請求を一つにまとめたい場合に便利です。(支払いが楽)


# GitHub Enterprise Metered Billingとは？
現在、Microsoft は "従量課金制" モデルを拡大し、GitHub Enterprise (GHE) と GitHub Advanced Security (GHAS) を含め、GitHub 製品ポートフォリオを従量制サービスとして統合しています。

これにより、お客様はストレスなく請求の対応が出来、セルフプロビジョニングと従量課金制の価格設定による柔軟性を高め、Microsoft を通じて GitHub 製品を購入することができます。

2024 年 8 月 1 日以降に作成された GitHub.com のエンタープライズ アカウントは、GHE と GHAS の両方で使用量ベースの従量制課金モデルをサポートし、その月の前限で事前購入するのではなく、月末に特定の月に消費したライセンスに対して支払うことができます。

さらに、このリリースの一部として、従量課金制の企業は以下を利用できます。

- GHE と GHAS の拡張されたセルフプロビジョニング のユーザーエクスペリエンスの向上
- Enterprise Managed Users (EMU) 構成を設定するオプション
- Azureの支払いとまとめれるので支払いが超楽
- Azure サブスクリプションに接続している場合の Microsoft Azure Consumption Commitments (MACC) および Azure Commitment Discounts (ACD) の適格性

すでに GitHub Enterprise (GHE) をご利用いただいているお客様の場合、プランと既存の請求方法はそのままです。
Free プランまたは Team プランから GitHub Enterprise 試用版にアップグレードする場合、新しいエンタープライズでは、GHE と GHAS の使用量ベースの従量制課金がすぐにサポートされます。

# まとめ
今回はGitHubについてまとめてみました。
普段使っていない機能から、請求方法がAzureと紐づける事が出来ることまで世間ではあまり知られていない情報を記載出来たと思います。

是非素敵なGitHubライフを送ってください。

それでは👍


# 参考文献
https://docs.GitHub.com/ja

https://github.blog/changelog/2024-08-01-introducing-metered-billing-for-github-enterprise-and-github-advanced-security/#:~:text=Enterprise%20accounts%20on%20GitHub.com%2C%20created%20on%20or%20after,you%20to%20pay%20for%20the%20licenses%20you%20consum
