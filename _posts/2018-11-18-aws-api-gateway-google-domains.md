---
layout: post
title: "AWS API GatewayでGoogle Domainsのドメインを設定する"
date: 2018-11-18 20:00:00 +0900
categories: [AWS]
---

## やりたいこと

AWS API Gatewayでカスタムドメインを設定する。
__Google Domains__ で取得したドメインのサブドメインを割り当てる。

例: `sub.yourdomain.app`


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
    - 名前: `_xxxxxxxxxxxxxxxxx.sub.yourdomain.app.`
    - 種類: CNAME
    - 名前: `_yyyyyyyyyyyyyyyyy.acm-validations.aws.`
- Google Domains (証明書紐付け)
  - カスタム リソース レコード
  - 名前: `_xxxxxxxxxxxxxxxxx.sub.yourdomain.app.`
  - タイプ: CNAME
  - データ: `_yyyyyyyyyyyyyyyyy.acm-validations.aws.`
  - 追加
- AWS API Gateway
  - ＋ カスタムドメイン名の作成
  - ドメイン名: `sub.yourdomain.app`
  - エッジ最適化
  - ACM 証明書: 上記で作ったものを指定
    - 初期化には最大40分かかる
  - 保存
  - 編集
    - ベースパスマッピング - マッピングの追加
    - パス: (任意)
    - 送信先: デプロイ済みのものから選択
  - ここで得られたターゲットドメイン名をコピー
    - `zzzzzzzzzzzzzz.cloudfront.net`
- Google Domains (CloudFront紐付け)
  - カスタム リソース レコード
  - 名前: `sub`
  - タイプ: CNAME
  - データ: `zzzzzzzzzzzzzz.cloudfront.net`
  - 追加

※ Google Domains は値入力を雑にやってもいい感じに直してくれる

__Done!__

### 余談

- [How To: Connecting Google Domains to Amazon S3 – Michelle – Medium](https://medium.com/@limichelle21/connecting-google-domains-to-amazon-s3-d0d9da467650)
