---
title: "GitHub Enterpriseプランにするメリットって何？"
emoji: "📚"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["GitHub", "GitHub Enterprise", "Microsoft", "Azure"]
published: true
publication_name: microsoft
---

![](https://storage.googleapis.com/zenn-user-upload/dec11cb49efa-20250914.png)

# はじめに

最近GitHubってTeamプランからEnterpriseプランに変えると何がいいの？と聞かれることが多くなったので、本記事にまとめてみました。

この記事では、普段皆さんがお使いのGitHubについて、TeamプランからEnterpriseプランにアップグレードして出来ることや、どのような企業がEnterpriseプランへのUpgradeを検討した方が良いかを解説します。

開発時にGitHub使うことはもちろん、契約形態やどれぐらいコストかかるんやろ？みたいな興味がある人は是非確認ください🚀

# GitHub Enterpriseプランとは？

![](https://storage.googleapis.com/zenn-user-upload/5bac1334a670-20250914.png)

GitHub Enterpriseは、GitHubが提供する大規模組織・企業向けの上位プランです。
TeamプランやFreeプランと比べて、セキュリティ・管理性・サポートが強化されており、数百〜数千人規模の開発組織やグローバルに展開する企業に最適化されています。

https://docs.github.com/ja/enterprise-cloud@latest/admin/overview/about-github-for-enterprises

金額は1userあたり月額21ドルで、Teamプランの4ドルと比べると高価ですが、その分セキュリティや管理機能が充実しているプランとなります。

特に、**セキュリティコンプライアンスが重視される業界（金融・公共・医療・通信・インフラ..etc）**や、クラウドネイティブに大規模な開発を進めるSaaS企業で多く導入されています。

ご興味ある方は是非チェックしてみてください🚀

# GitHub 各種プランの比較

GitHubにはFree・Team・Enterpriseの3つのプランがあり、それぞれに特徴があります。
まずは比較表を見てみましょう！

https://github.co.jp/pricing.html#compare-features

| 機能カテゴリ | Free | Team ($4/ユーザー/月) | Enterprise ($21/ユーザー/月) |
|---|---|---|---|
| **コード管理** | | | |
| パブリックリポジトリ | 無制限 | 無制限 | 無制限 |
| プライベートリポジトリ | 無制限 | 無制限 | 無制限 |
| **コードワークフロー** | | | |
| GitHub Codespaces | 利用不可 | 最大32コア（従量課金 $0.18/時間〜） | 最大32コア（従量課金 $0.18/時間〜） |
| GitHub Actions (CI/CD時間/月) | 2,000分 | 3,000分 | 50,000分 |
| GitHub Packages ストレージ | 500 MB | 2 GB | 50 GB |
| コードレビュー機能 | ✅ | ✅ | ✅ |
| Pull requests | ✅ | ✅ | ✅ |
| 保護されたブランチ | パブリックリポジトリのみ | ✅ | ✅ |
| 必須レビュー担当者 | パブリックリポジトリのみ | ✅ | ✅ |
| **コラボレーション** | | | |
| パブリックリポジトリ上の共同作業 | 無制限 | 無制限 | 無制限 |
| プライベートリポジトリ上の共同作業 | 無制限 | $4/ユーザー/月 | $21/ユーザー/月 |
| Issue & プロジェクトボード | ✅ | ✅ | ✅ |
| コードオーナー | – | ✅ | ✅ |
| Wiki | ✅ | ✅ | ✅ |
| 複数人でのドラフトPull Request | ✅ | ✅ | ✅ |
| **セキュリティ & コンプライアンス** | | | |
| GitHub Security Advisories | パブリックリポジトリ | パブリックリポジトリ | GitHub Advanced Security（別途ライセンス） |
| シークレットスキャン | パブリックリポジトリ | パブリックリポジトリ | GitHub Advanced Security |
| Dependabotアラート | ✅ | ✅ | ✅ |
| Dependabotセキュリティアップデート | ✅ | ✅ | ✅ |
| コードスキャン | パブリックリポジトリ | パブリックリポジトリ | GitHub Advanced Security |
| 依存関係レビュー | ✅ | ✅ | ✅ |
| SAML SSO | – | – | ✅ |
| 監査ログ API | – | – | ✅ |
| IP許可リスト | – | – | ✅ (Enterprise Cloud) |
| GitHub Connect | – | – | ✅ |
| **Marketplace & インテグレーション** | | | |
| GitHub Apps | 無制限 | 無制限 | 無制限 |
| スポンサシップ | ✅ | ✅ | ✅ |
| サードパーティアプリ利用 | ❌ | ✅ | ✅ |
| **サポート & 導入** | | | |
| GitHubコミュニティ＆Docs | ✅ | ✅ | ✅ |
| メンテナンスサポート | – | ✅ | ✅ |
| プレミアム／プレミアムプラスサポート | – | – | ✅ |
| 専任担当サポートエンジニア | – | – | ✅ |
| 請求書払い | – | – | ✅ |

上記の表を眺めていると、**不正アクセスの防御**や**専任のサポート**がついたり、**請求書払い**に出来ることは企業としては嬉しいですね。

それではこれから私が思う、特にEnterpriseプランの良いところにDeepDiveしていきましょう。

# GitHub Enterpriseプランにするメリット
個人的に思う、企業がEnterpriseプランを使うメリットを記載します。

## 不正アクセスに対しての防御
管理者によるアカウント管理とセキュリティ制御がTeamプランと比べて強化されています。

GitHubでID／パスワードの漏洩、パーソナルアクセストークンやSSHキーの漏洩、シャドーIT（自宅PCなど企業が管理していないデバイスを用いる等）は、昨今のフルリモート勤務環境では特に気を付けたいところですが、なかなか管理だったり個人のセキュリティ意識の差があったりで徹底が難しい場面が出てきます。

そのような状況をシステム的に制御し、不正アクセスや情報漏洩リスクを回避することが可能になります。
特に以下の項目はTeamプランには無いので、Enterpriseプランにする大きなメリットになります。
- SAML認証  
- SCIMによるユーザプロビジョニング  
- IDP連携による多要素認証  
- IP制限  

### 機能比較

| 機能 | Team Plan | Enterprise Plan |
|---|---|---|
| SAML認証 | × | ○ |
| SCIMによるユーザプロビジョニング | × | ○ |
| IDP連携による多要素認証 | × | ○ |
| IP制限 | × | ○ |
| Enterprise Managed Users | × | ○ |

## ID・アクセス管理の徹底

**SAMLを有効化すると、Organizationにアクセスする際にIDP側の認証が必ず要求されるようになります。**

### ポイント
- 万一社員の **GitHubのID／パスワード、パーソナルアクセストークン、SSHキー** が攻撃者の手に渡った場合でも、  
  **SAML IdPの認証が通らない限りGitHub上の企業データへアクセス不可**。
- 管理者が当該ユーザーの **ログイン禁止（無効化）** を即時適用可能。
- **IdP側の監査ログ** で、ユーザーがどこからログインしているか（シャドーIT含む）の監視が可能。
- **SCIM対応IdP** の場合、**新入社員のアカウント発行／退職者のアカウント停止を自動化**。

## Enterprise Managed Users (EMU)機能
この機能が個人的にはEnterpriseプランの一番の目玉機能だと思っています。

https://docs.github.com/ja/enterprise-cloud@latest/admin/managing-iam/understanding-iam-for-enterprises/about-enterprise-managed-users

**EMU**は、GitHubのユーザーアカウントを企業が完全に管理するモードです。
通常のGitHubアカウントはユーザーが自由に作成・管理できますが、EMUを有効化すると IDプロバイダ（IdP：Azure AD, Oktaなど）でのみアカウントを作成・管理 できるようになります。

通常のアカウントとの違いをまとめると以下の通り。

#### 通常（Free/Team/Enterprise標準）
- アカウントは「個人」が所有
- 個人のメールアドレスでGitHubにサインアップ可能
- 退職してもその人のアカウント自体は残る
#### EMUあり（Enterprise限定）
- アカウントは「企業」が所有
- IdP（Azure AD, Oktaなど）でのみ発行・ログイン可能
- 退職・異動したらIdPでアカウント削除→GitHubアクセス即停止
- Publicリポジトリ作成は禁止（情報漏洩防止のため）

EMUを利用すると、通常の GitHub.com で公開されていない、ご契約企業専用の GitHub Enterprise Cloud 環境となり、IdP で直接ログインが可能です。EMU あり／なし、どちらの環境を利用するか選択できます。

EMUの機能としては以下になります。
- IdP と緊密に連携された企業専用の GitHub 環境  
- アカウント発行・管理は全て IdP と完全結結  
- Public で公開用の内容は一切作成不可  
- IdP 側のポリシーを適用可能  

EMU の利用条件には以下があります。
- 対応 IdP : Entra ID, Okta, PingFederate  
- IdP で SAML 連携および SCIM/OIDC 連携  
- EMU環境とNon-EMU環境の併用は不可、どちらか一方を選択して利用  

EMU あり／なしを比較すると以下です。

| 項目 | EMU あり | EMU なし |
|---|---|---|
| アカウント所有者 | 企業 | 個人 |
| 利用できる環境 | 企業専用の "EMUテナント" の中でのみ有効なアカウント | 招待された環境／役割の設定に応じて利用可能なアカウント |
| Public リポジトリ作成可否 | 不可（Enterprise 配下 Organization 配下／個人配下 で一切不可） | 可能（Organization配下：Organization Member には作成不可／個人アカウント配下では作成可能。アカウント削除後もPublicリポジトリは残存） |

つまり、EMUは
**社員や外部委託者が勝手にGitHubアカウントを作って使うことを禁止し、すべて企業が用意したマネージドアカウントでのみ開発を行わせる仕組み**
です。

これにより、
- 不正アクセス防止
- 情報漏洩リスク低減
- コンプライアンス遵守
- アカウントライフサイクル（発行・停止）の自動化
が可能になります。

# Organizationの上位概念にあたるEnterprise Accountが作成可能に
Enterprise Accountの作成、複数Organizationの作成し管理することが可能になります。

以下の画像のようなイメージ。Organizationの上にEnterprise Accountが作成出来るイメージですね。

![](https://storage.googleapis.com/zenn-user-upload/02c3c71eb8a8-20250914.png)

Team PlanではOrganizationごとに契約が必要で、複数Organizationを集約管理することは不可という縛りがありました。

Enterprise Planでは、上位概念として **Enterprise Account** を提供され、1つのEnterprise Account配下で複数Organizationを一括管理可能になり、外部協力会社との共同開発案件では、Organizationを分けて管理しつつ、特定コンポーネントだけを共有する等が可能でよりセキュアな開発管理を実現することが可能です。

機能比較としては以下のようになります。

| 機能 | Team Plan | Enterprise Plan |
|---|---|---|
| Enterprise Accountの作成 | × | ○ |
| 複数Organizationの作成 | × | ○ |

Enterprise Accountの管理者が実施できる設定などなどは以下。

- 新規Organizationの作成や複数Organizationの一元管理  
- 所属するユーザ全員の管理（社内外含む、アクセス権を付与したユーザの管理）  
- GitHub Actions, Copilotなどの利用状況の可視化  
- SSO、多要素認証、IP制限などセキュリティ項目の一括設定  
- 全Organizationに対する共通ポリシーの強制  
  - Publicリポジトリ作成禁止  
  - MemberによるOutside Collaborator招待禁止  
  - Memberによるリポジトリ可視性変更の禁止  
  - リポジトリのFork禁止  
- 監査ログの取得（API経由／定期的保存／異常行動の監視）  
- Enterprise Server（オンプレ）利用時のライセンスファイル管理  

また、Enterpriseプランでは、リポジトリの可視性を制御することが可能です。
具体的にはPublic／Privateリポジトリに加えて **Internal Repository** を作成可能になります。

特徴は以下。
- **Private Repository**: 外部パートナーやクライアントのOutside Collaboratorにも公開可能  
- **Internal Repository**: 社員のみに公開  
これにより、リポジトリの可視性を柔軟に制御し、機密情報をセキュアに保護可能。

#### 機能比較

| 機能 | Team Plan | Enterprise Plan |
|---|---|---|
| Public Repositoryの作成 | ○ | ○ |
| Private Repositoryの作成 | ○ | ○ |
| Internal Repositoryの作成 | × | ○ |

個人的には社員のみに公開など、可視性を柔軟に制御できるInternal Repositoryは気に入っています。

# 情報漏洩対策
他にもEnterpriseプランでは、情報漏洩対策が強化されています。

publicリポジトリの作成制限や外部コラボレータをOrganizationのOwnerのみが招待出来るように制限したりなど、オペレーションミスによる情報漏洩のリスクを管理者が制御可能です。

# ソフトウェア品質 & 開発スピードの向上 – GitHub Advanced Security

開発や運用保守の観点からもEnterpriseプランにするとメリットがあります。
ここからはGitHub Advanced Security (GHAS)の機能を紹介します。
GHASはEnterpriseプランの有償オプション製品で、セキュリティとコード品質を向上させるための高度なツールセットを提供します。

GitHub Advanced Securityの中でもEnterpriseプランでのみ利用可能な機能もあるので、チェックが必要です。

## Dependabot
- セキュリティパッチ・バージョンアップのPRを自動作成  
- 豊富な脆弱性データ（GitHub Advisory Databaseなど）  
- Privateリポジトリでの **Dependency Review** 有効化  

| 機能 | Team Plan | Enterprise Plan |
|---|---|---|
| Dependabot | ○ | ○ |

## Code Scanning
- コードを自動解析し脆弱性を検出 (CodeQL)  
- 2000以上のパターンに対応、カスタムクエリも可能  
- 他の静的解析ツールとの拡張連携も可能  

| 機能 | Team Plan | Enterprise Plan |
|---|---|---|
| Code Scanning | × | ○ |

## Secret Scanning
- 認証用トークンや秘密鍵がコードに含まれていないか自動検出  
- Azure, AWS, GCP, Dropbox, Slackなどに対応  
- 全てのgitヒストリをスキャン可能  
- Push Protection機能で危険なトークンを防止  

| 機能 | Team Plan | Enterprise Plan |
|---|---|---|
| Secret Scanning | × | ○ |

## 組織横断的なセキュリティリスクの可視化
- 既知の脆弱性や検出されたシークレット情報を横断的に閲覧可能  
- 各セキュリティ機能の有効状況を確認可能  

| 機能 | Team Plan | Enterprise Plan |
|---|---|---|
| 組織横断的な脆弱性リスクの可視化 | × | ○ |

👉 各Organizationごとに **セキュリティリスクを可視化** することが可能であり、複数のOrganizationを跨いだセキュリティ統制も実現可能となります。


# Azureとの連携について
GitHubとAzureなんの関係あんねんと思われる方も多くいらっしゃるかと思いますが、実は両方ともMicrosoft製品だったりします。
今回紹介するのは**GitHub Metered Billing**というGitHub Enterpriseのライセンス形態で、GitHubとAzureのお会計を紐づけることで、従量課金のようにGitHub Metered Billingが利用可能になる契約形態です。

## GitHub Metered Billingとは？
通常、GitHubの料金は「ユーザー数 × プラン料金」で計算されますが、
それに加えて一部のリソースや機能については 利用した分だけ追加請求 されます。
この「追加分の従量課金」の仕組みを Metered Billing（従量課金課金制） と呼びます。

![](https://storage.googleapis.com/zenn-user-upload/47947a776528-20250914.png)

## Metered Billing がおすすめのシナリオ

### 💰 余分なライセンスを減らしてコスト最適化したい
GitHub Enterpriseのユーザーライセンスは通常年間契約のため、契約期間中の開発者の増加を考慮して少し多めに調達をしておく場合があります。  
Metered Billingの場合には毎月必要なライセンス数分だけを購入すればよいため、余分なライセンスコストを減らすことができます。

### 👥 スモールスタートで GitHub Enterprise を試してみたい
GitHub Enterpriseに興味はあるが価格がネックで試しづらいという方にはMetered Billingがおすすめです。  
小規模からはじめて、検証に関わる人の分だけライセンスを用意すればよいので短期間でのお試しに最適です。  
一次的なプロジェクトで外部の方にライセンス付与するケースもあります。

### 💻 Azure と GitHub のコストをまとめて管理したい
マイクロソフト製品のコストをできるだけ一括で管理したいという方にもMetered Billingがおすすめです。  
Metered BillingではAzure Subscriptionに請求をまとめることができるため、Azureポータルで簡単に開発環境のコストを確認することができるようになります。

メリットとしてはGitHubの請求も全部Azureのポータル画面から一元管理出来るので、管理者の負担が減るのがGoodなこともポイントです。
![](https://storage.googleapis.com/zenn-user-upload/6a36cbe58598-20250914.png)

# まとめ
改めてGitHub TeamプランとEnterpriseプランの違いを表形式でまとめると以下となります。

## GitHub Team / Enterprise 機能比較表

| 機能 | Team Plan | Enterprise Plan |
|---|---|---|
| Enterprise Accountの作成 | × | ○ |
| 複数Organizationの作成 | × | ○ |
| Public Repositoryの作成 | ○ | ○ |
| Private Repositoryの作成 | ○ | ○ |
| Internal Repositoryの作成 | × | ○ |
| Public Repositoryの作成禁止 | × | ○ |
| Organization memberによるOutside Collaboratorの招待禁止 | × | ○ |
| SAML認証 | × | ○ |
| SCIMによるユーザプロビジョニング | × | ○ |
| IDP連携による多要素認証 | × | ○ |
| IP制限 | × | ○ |
| Repository/Organization全体の依存関係のダッシュボード (Dependency Insights) | × | ○ |
| OSSの脆弱性の自動検知・通知・修正 | ○ | ○ |
| 組織横断的脆弱性リスクの可視化 (Security Overview) | × | ○ |
| ライセンス情報の一覧の可視化 | × | ○ |
| GitHub Actions (CI/CD) | 3,000分/月 *注1 | 50,000分/月 |
| GitHub Packages (パッケージ管理) *注2 | 2GB/月 *注1 | 50GB/月 |
| Code Scanning | × | ○ |
| Secret Scanning | × | ○ |
| Audit Log API | × | ○ |
| 99.95% 稼働率SLA | × | ○ |
| Enterprise Support (英語・日本語) | × | ○ |
| 監査ログのストリーミング | × | ○ |

また、Enterpriseプランを小規模に試したいという企業様は、Metered Billingでの契約がおすすめです。

私が感じる導入したらいいのにと思うタイミングは以下です。
- 社員数が数百〜数千規模の大企業(セキュリティ大事)
- セキュリティや監査が厳格に求められる業界（金融・公共...などなど。情報漏洩する前に対策必要）  
- グローバル展開しており、複数リージョンで統一的に開発基盤を運用したい企業(海外支店などあれば管理者の負担及びセキュリティホールの心労軽減)
- Azureとの連携を強化してDXを推進したい企業  

セキュリティの強化やGitHubアカウントの管理、Azureとのお会計紐づけなど様々なメリットがあるので、是非検討してみてください。

それでは👍

# 筆者情報

AzureやGitHubに関する情報を発信しておりますので、ご興味あればチェックしてみてください。
Enterpriseプランの相談も是非お気軽にどうぞ！
https://x.com/fe_js_engineer
https://www.linkedin.com/in/satyus/

# 参考情報
https://docs.github.com/ja/enterprise-cloud@latest/admin/managing-iam/understanding-iam-for-enterprises/about-enterprise-managed-users
