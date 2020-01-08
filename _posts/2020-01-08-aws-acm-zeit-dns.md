---
layout: post
title: "ZEIT DNS と ACM・API Gatewayをつなぐ"
date: 2020-01-08 20:00:00 +0900
categories: [AWS]
---

![](/til/static/img/posts/aws-acm-err.jpg)


## やりたいこと

ZEIT NowにデプロイしたアプリをGoogle Domainsで契約しているドメインに割り当てつつ、そのサブドメインをAWS API Gatewayに紐付ける。

例: `sub.yourdomain.app`

なお、Google Domains上のDNS設定ではネームサーバーが「カスタム ネームサーバーを使用する」とした上でZEITのものを設定しており、「カスタム リソース レコード」は設定しているもののおそらく参照されていない。


## 実現方法

※ 証明書の有効期限切れに伴う修正として実施。

おそらくACMの証明書は期間延長のようなことはできず、再度作り直す必要があると判断した。
削除しようとすると「証明書は使用中」エラーとなった。

> __証明書は使用中__  
> 証明書は sub.yourdomain.app (xxxxx) 使用中で（他の AWS リソースに関連付けられていて）削除することはできません。リスト内の各リソースからその証明書との関連付けを解除し、もう一度お試しください。

同じドメイン名の証明書を作成することはできるため、削除せず新しく証明書のリクエストを実行した。
API Gatewayのカスタムドメイン名から該当のものを削除することで対応した。もしかしたら削除する必要ないかも。
内部的な削除の完了までに15分ほど時間がかかるようなので注意。
これのあと、証明書のリクエストを進めると失敗した。

> __リクエストの失敗__  
> この証明書のリクエストの状況は "失敗" です。CAA (Certificate Authority Authentication) エラーにより、1 つまたは複数のドメイン名の検証に失敗しました。

ZEITはデフォルトで認証局を`letsencrypt.org`のみ許可しているので、AWSからは拒否される。
CAAレコードの状況は`now dns ls`で確認できる。[^1]
以下のコマンドでもともとあるCAAレコードが上書きされ、AWSを許可できる。[^2]

```bash
$ now dns add yourdomain.app @ CAA '0 issue "amazon.com"'
```

反映は24時間〜48時間以内とあるが、すぐに有効となった。
改めて証明書のリクエストを行った。作成後すぐは更新資格が使用不可ステータスとなるが、問題ない。

API Gatewayのカスタムドメイン名で、先程消したものを作り直す。それに伴いターゲットドメイン名となるCloudFrontのドメインが新しいものになる。このときDNSの状況が参照されるため、古いCNAMEレコードの`ddddddddddddd.croudfront.net.`があるとエラーとなる。`now dns rm`コマンドで削除してからAPI Gatewayのカスタムドメイン名を保存する。

ACM証明書「初期化しています…」となれば成功。この時点でCertificate Managerにおいて証明書の更新資格が使用可能ステータスになっている。ベースマッピングの設定も忘れず実施。

割り当てられたターゲットドメイン名をコピーし、以下の通りコマンドを実行してDNSにCNAMEレコードを登録。

```bash
$ now dns add yourdomain.app sub CNAME dddddddddddddd.cloudfront.net
```

ACM証明書の初期化が完了したのを確認して完了。

[^1]: [Now CLI Reference - ZEIT](https://zeit.co/docs/now-cli#commands/dns)

[^2]: [(オプション) CAA レコードの設定 - AWS Certificate Manager](https://docs.aws.amazon.com/ja_jp/acm/latest/userguide/setup-caa.html)
