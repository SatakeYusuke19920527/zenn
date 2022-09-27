---
title: "Azureで使う負荷分散の違いをまとめてみる"
emoji: "📖"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["Azure","FrontDoor", "Traffic Manager","Load Balancer", "Application Gateway"]
published: true
---

# 【Front Door/Traffic Manager】Azureで使う負荷分散の違いをまとめてみる【Load Balancer/Application Gateway】

## 一目でわかるようにざっくりとまとめてみました。

| サービス            | グローバルor地域 | 推奨されるトラフィック | 使いどころ                                                                                              |
| ------------------- | ---------------- | ---------------------- | ------------------------------------------------------------------------------------------------------- |
| Azure Front Door    | グローバル       | http(s)                | リージョンまたぎの負荷分散 & WAF & CDN<br>リージョンレベルの災害対策と高速化                            |
| Traffic Manager     | グローバル       | 非http(s)              | リージョンまたぎの負荷分散                                                                              |
| Application Gateway | 地域             | http(s)                | パスベースで負荷分散&TLS終端&WAFを追加できる<br>アプリケーションの一般公開&可用性ゾーンレベルの災害対策 |
| Load Balancer       | 地域             | 非http(s)              | IPアドレス&ポートで負荷分散<br>仮想ネットワーク内の負荷分散&可用性ゾーンレベルの災害対策                |


## AZ-305の試験対策などにどうぞ。