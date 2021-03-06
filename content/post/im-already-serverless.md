+++
date = "2017-02-01T21:02:23+09:00"
title = "サーバーレスアーキテクチャはじめました"
description = "最近サーバーレスアーキテクチャってよく聞くけど、ブログ環境がすでにそうだった"
categories = ["AWS","健忘録"]
tags = ["AWS","サーバーレスアーキテクチャ","S3","Route53","CircleCI","GitHub"]
author = "MiketaNyoroN"

+++

三日坊主ではなかったけど、結局Contributionが緑じゃないにょろんです。

コードもブログも、書きたいときに書くのが一番ですね。義務感が出てきちゃうと継続が辛くなります。

さてさて、最近「サーバーレスアーキテクチャ」なる単語をよく聞きます。最近ではないかもしれませんがよく聞きます。

要は「サーバを自前で用意せず、マネージドサービスを活用してシステムを構築する」[^1]ようなアーキテクチャですが…。

よく考えたらこのブログ、それですわ。

自慢したくなったのでこのブログの構成についてちょっと詳しく書いてみます。

<!--more-->


## 静的ページだし、システムじゃ無いよね？
静的ページを公開するだけなら、そこら辺に転がっている「無料ホームページ公開サービス」みたいなものを使えば一発です。

ですが、ページの生成やソースコードの保管、HTML等のファイルをサーバーに配置するところまでは、そんじょそこらのサービスでは自動的にやってくれません。

ブログサービスはアリですが、僕はそれよりももっと自由で使いやすい環境が欲しかったので、こんなことになっちまいました。

勝手に広告が入らないですし、AWSによほどの障害が発生しなければいつでも見れますし、何をやっても安い方なのが魅力的です。

あと勉強になります。


## まずは概要
![このブログの構成図](/img/2017/02/blog_architecture.png)

上図がこのブログの構成図です。ちょっとカッコつけて[Cloudcraft](https://cloudcraft.co/)使ってみました。

見ての通りサーバーレスです。API経由で静的ページが公開できるサービスと、DNS鯖を置けるところがあれば、どこでも動きます。

ぶっちゃけ、AWSじゃなくても動きます。AWSのことが好きなので使ってるだけです。


## 利用サービス詳説
静的ページの公開に利用しているのは[S3](https://aws.amazon.com/jp/s3/)。Website Hostingを有効にして、誰でもバケットに読み取りアクセスできるように設定してあります。

DNS鯖を担当しているのは[Route 53](https://aws.amazon.com/jp/route53/)。miketa.nyoron.jpとかnyoron.jpの管理はこいつです。

ソースコード管理は[GitHub](https://github.com)。別にプライベートにするような内容もなかったので、公開リポジトリが無料なとこで、一番使い慣れてるところにソースをぶん投げただけです。

デプロイは[CircleCI](https://circleci.com/)に[押し付け](/2016/11/play-with-circle-ci/)ています。自動化最高。

デプロイの際に使用しているツールは[AWS CLI](https://aws.amazon.com/jp/cli/)です。CircleCIにアクセスキー等を仕込んでいるので簡単に使えます。

静的ページの生成は[Hugo](https://gohugo.io/)。いい子です。

記事を書くときに使う書式は、(たぶん)みんな大好きMarkdownです。


## ブログを公開するまでのワークフロー

楽をできるように作ったので、記事の公開はかなり楽です。

1. 記事を書く
2. `git add`
3. `git commit`
4. `git push`

もし、集団でブログを管理することになれば下記のような感じでしょうか。

1. `git checkout -b [ブランチ名]`
2. 記事を書く
3. `git add`
4. `git commit`
5. `git push`
6. プルリクエスト提出
7. 誰かがレビューする
8. OKならmasterブランチにマージしておわり

基本的に、全ての作業を黒い画面で効率よく完結できちゃいます。これはブログサービスではほぼ無理じゃないかと思ってます。


## このシステムの良いところ

- 黒い画面だけでブログ執筆・公開ができる
- もちろんGUIでもできる
- Gitが使えない時はGitHubに直書きすれば記事公開ができる
- 最適化(minifyとか)し放題
- CDNも挟み放題
- 難易度低そうにみえて結構勉強になる
- 記事公開が楽ちんすぎてブログ執筆に抵抗がなくなった


## おわり

今回の記事も酔っ払った勢いだけで作りました。てへぺろ。

黒い画面 + 好きなエディタだけでここまで捗るものかと自分でもびっくりです。


[^1]: http://blog.serverworks.co.jp/tech/2016/06/06/what_is_serverless/
