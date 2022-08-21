---
title: "クラウド未経験から6ヶ月でExpertまで上り詰めた話"
emoji: "✨"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["Azure","AZ-900", "AZ-104", "AZ-500", "AZ-305"]
published: true
---

# クラウド未経験から6ヶ月でExpertまで上り詰めた話

こんにちは。ゆうじろうです。
つい先日、AZ-305を取得してMicrosoftからExpert認定されました。
光栄なことですよね。

今回は半年でExpertまで登りつめるために行った勉強方法やアウトプットについてお話ししていきます。

Azureに興味のある方、クラウドを極めたい駆け出しエンジニアの皆様は是非みてあげてください。


# Azureの資格を取得することになった経緯

まずは、ゆうじろうがAzureの資格を取得することになった経緯は以下。

***弊社社内にて***


社長  「今我が社はオンプレ中心だが、Azure上にアプリを構築させたい」<br />
上司① 「ですが、社長。Azureに詳しい人など社内にいません」<br />
社長  「じゃあ、今から詳しくなればいいじゃない。」<br />
上司②「ですが、なかなか時間も足りず...」<br />
社長  「言い訳っていいわけ？」<br />
上司③「うぐぐ...我々は20年もオンプレの身。今更クラウドなど...誰かやってくれる人はいないのか...」<br />
上司たち「ちらっ...」<br />

<br />

ゆうじろう「こっち見んな」

<br />


そんなこんなで、Azureの社内教育係になるために、先陣を切って資格取得の勉強が始まりました。

2月〜8月までに取得した資格はこんな感じ

- AZ-900  Azure Fundamentals
- AZ-104  Azure Administrator Associate
- AZ-500  Azure Security Engineer Associate
- AZ-305  Azure Solutions Architect Expert

6ヶ月で4つのペースで取得いたしました。
我ながらナイスペース。

では、ゆうじろうが実施した学習ロードマップをご紹介します！


# AZ試験学習ロードマップ

## AZ-900 Azure Fundamentals

https://docs.microsoft.com/ja-jp/certifications/azure-fundamentals/

まずはAZ-900からスタート。
Azure上で使用される機能が浅く広く出てくる感じです。

ゆうじろう特にクラウドをさわった経験もなかったので、Udemyで講座を二つポチりました。

+ 作りながら覚えるMicrosoft Azure入門講座（IaaS編）

https://www.udemy.com/course/microsoft-azure-iaas-part/

+ 作りながら覚えるMicrosoft Azure入門講座（PaaS編）

https://www.udemy.com/course/microsoft-azure-paas-part/

Azure登録して1ヶ月は無料枠が設けられているので、その期間の間に２つの講座をDone。
わからんところはそこまで深入りせず、将来の自分が解決してくれることを期待して、止まらずにどんどん進めていきます。

そして、テスト勉強はAZ-900のテキスト、MSLearningとUdemyの模試を実施しました。

このテキストがわかりやすかったので、オヌヌメ。

+ 最短突破 Microsoft Azure Fundamentals[AZ-900]合格教本 
![AZ-900-text](/images/azure_expert_road/az-900-text.png)

+ MSLearning

https://docs.microsoft.com/ja-jp/certifications/exams/az-900

+ 【2022年版】AZ-900 Microsoft Azure Fundamentals模擬問題集（7回分430問）

https://www.udemy.com/course/az-900-microsoft-azure-fundamentals4176/

やり方は簡単で、Udemyの模試を全て90%以上になるまで何回も受けるだけです。
ゆうじろう暗記科目が弱いので、全部5回ぐらい受験してました。

1月13日から勉強を開始して、ちょうど1ヶ月後の2月13日にめでたく合格しました。

![AZ-900DONE](/images/azure_expert_road/az-900.png)

合格して帰ってきた瞬間にAZ-104の予約をしました。



## AZ-104 Azure Administrator Associate

https://docs.microsoft.com/ja-jp/certifications/exams/az-104

次はAZ-104の試験。試験内容は主に設計や管理者に必要な知識がAZ-900より、幅狭く、深く出てくる感じです。
AzureMonitorのLogAnalyticsの設定順序など、各種操作順序を覚えるのがなかなか辛い試験です。

こちらもひたすらAZ-104のテキストとUdemyの模試で戦いました

+ 合格対策Microsoft認定試験AZ-104:Microsoft Azure Administratorテキスト&演習問題  
![AZ-104-text](/images/azure_expert_road/az-104-text.png)

+ AZ-104:Microsoft Azure Administrator模擬試験問題集

https://www.udemy.com/course/az-104azure-administrator-associate/

+ 【2022年版】AZ-104 Microsoft Azure Administrator模擬試験問題（4回分260問)

https://www.udemy.com/course/az-104-microsoft-azure-administrator4260/


やっぱりテキストがあると勉強がやりやすい。
上記のテキストはオヌヌメです。

こちらも1ヶ月ほどの勉強で合格。
AZ-900の知識が結構活躍したので、連続で受けた方がよいかも。

![AZ-104DONE](/images/azure_expert_road/az-104.png)

合格し、帰ったその足でAZ-500の試験を申し込みました。

## AZ-500 Azure Security Engineer Associate

https://docs.microsoft.com/ja-jp/certifications/exams/az-500

個人的にはこちらが鬼門。
Azureのセキュリティに関する試験です。

まず、教科書が英語のものしかありません。
なくなく最初のインプットはMSLearningを使用することに。

+ MSLearning

https://docs.microsoft.com/ja-jp/certifications/exams/az-500

紙媒体でガンガン書き込んで学習したいオールドタイプのゆうじろうはかなり苦戦を強いられました。

後はUdemyの公式模試で全て90%以上になるまで解きまくりました。

+ Practice Tests | AZ-500: Microsoft Azure Security Exam 2022

https://www.udemy.com/course/microsoft-azure-security-technologies-exam-practice-tests/


4月から勉強を開始して6月末に受かったので、3ヶ月弱苦しめられました。(1回落ちたことは秘密です。)

![AZ-500DONE](/images/azure_expert_road/az-500.png)

またまた、受かって帰宅したそのタイミングでAZ-305の試験を予約

## AZ-305 Azure Solutions Architect Expert

https://docs.microsoft.com/ja-jp/certifications/exams/az-305

AZ-900、AZ-104、AZ-500の知識をフル活用して受けた試験でした。
個人的には今までの勉強の甲斐があってか、楽しく勉強できた気がします。

こちらも、MSLearningとUdemyの模試で進めました。

+ MSLearning

https://docs.microsoft.com/ja-jp/certifications/exams/az-305

+ AZ-305: Designing Azure Infrastructure Solutions Exam 2022

https://www.udemy.com/course/az-304-microsoft-azure-architect-design-exam-practice-tests/


Expert試験ということもあり、全ての模試を95%以上にしてから臨んだ結果、過去最高得点で合格することができました。

![AZ-305DONE](/images/azure_expert_road/az-305.png)

# 色々受験してみて見出した、AZ試験のコツ
とにかく試験を予約してしまって、退路を断って勉強することだと思います。
ゆうじろうは知識ゼロの状態で1ヶ月先に試験を予約してしまって、そこに向けて頑張るという形式を取ってました。

この予約を先にするスタイルで資格試験の勉強が潤滑に進めたと思います。

色々忙しい社会人たちに是非オヌヌメの勉強方法となります。

# 受験してみてよかったこと
シンプルに社内の評価が上がりました。
また、Azure系の仕事をどんどん振ってもらえるようになりました。(というか社内のオンプレの仕事をクラウド使えば簡単にできるよみたいな提案を色々するようになりました。)

正直、最初はAzureなど全然興味がありませんでしたが、やってみると色々楽しいものです。

毛嫌いせずにいろいろチャレンジしてみることの大切さを知った気がします。

ゆうじろう、次はAZ-400を受験します。
こちらも合格次第、ロードマップに追加するので、是非お楽しみに！

皆様のエンジニア人生にちょっとでも参考になればと願って終わりにします。

## これからもがんばりましょう！