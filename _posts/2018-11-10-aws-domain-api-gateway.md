---
layout: post
title: "AWS API Gatewayでカスタムドメインを設定する"
date:   2018-11-10 11:00:00 +0900
categories: [AWS]
---

## やりたいこと

AWS API Gatewayでカスタムドメインを設定する。
取得したドメインのサブドメインを割り当てる。

例: `sub.yourdomain.com`


## 実現方法

- [API GatewayをカスタムドメインでHTTPS化する ｜ DevelopersIO](https://dev.classmethod.jp/cloud/api-gateway-custom-domain-ssl/)
- [エッジ最適化のカスタムドメイン名を作成する方法 - Amazon API Gateway](https://docs.aws.amazon.com/ja_jp/apigateway/latest/developerguide/how-to-edge-optimized-custom-domain-name.html)

- AWS Certficate Manager
  - `us-east-1` バージニア北部
  - 証明書のリクエスト
  - パブリック証明書のリクエスト
  - ステップ1: ドメイン名に Route 53 で定義予定のサブドメインを指定
  - ステップ2: DNSの検証
  - ▶ を展開
  - Route 53 でのレコードの作成 ボタン
  - Route 53 に `_xxxxxxxxxxxxxxxxx.yourdomain.com` CNAME ができる
- AWS API Gateway
  - ＋ カスタムドメイン名の作成
  - ドメイン名: `sub.yourdomain.com`
  - エッジ最適化
  - ACM 証明書: 上記で作ったものを指定
    - 初期化には最大40分かかる
  - 保存
  - 編集
    - ベースパスマッピング - マッピングの追加
    - パス: (任意)
    - 送信先: デプロイ済みのものから選択
  - ここで得られたターゲットドメイン名をコピー
    - `xxxxxxxxxxxxxx.cloudfront.net`
- AWS Route 53
  - Hosted zones
  - Create Record Set
  - Name: `sub`.yourdomain.com
  - Type: A - IPv4 address
  - Alias: Yes
  - Alias Target: `xxxxxxxxxxxxxx.cloudfront.net`
    - 警告は無視
  - Create

__Done!__
