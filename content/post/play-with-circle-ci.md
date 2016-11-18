+++
title = "面倒くさい作業をCircleCIに押し付けたお話"
tags = [
  "CircleCI",
  "CircleCIかわいい",
  "Minify"
]
categories = [
  "健忘録",
  "便利ツール"
]
description = "CircleCIにMinifyやらデプロイをやらせます"
author = "MiketaNyoroN"
date = "2016-11-18T16:44:23+09:00"
+++

三日坊主のにょろんです。

昨日から引き続きブログの改善に一日を使っています。

1日1本ペースで記事を書けるほどのネタはないので、とりあえずGitHubのContributionが緑っぽく見えるように何か続けていきたい所存です。

今日は面倒くさくてサボりがちな、ブログのデプロイ作業を[CircleCI](https://circleci.com/)に押し付けます。

<!--more-->


## 何が面倒か

突然ですが、このブログはAWS上に置いています。そしてAWSアカウントの利用料は僕のお財布から抜かれていきます。

AWS最大のメリットである「使った分だけ請求」は、逆に言うと「無駄なリソースはすべてお財布へのダメージ」となります。[^1]

「ブログは自動生成してるんだからそんなにムダもないでしょ」という感じもします。しかし、テーマによっては無駄にCSSやらJSをローカルに持っていたり、無駄な空行があったり、コメントがやたらくっついていたりします。

新しい記事を書いてページを生成するたびに、それらのファイルを削除したり、エディタで開いて改行を全部消したり、特定の記述を置換していたりすると、また心が折れてブログを放置…なんてことになります。

ということで、心が折れそうな面倒な作業(Minifying)のすべてとデプロイをCircleCIに押し付けます。


## CircleCIってなんぞや

[継続的インテグレーション(CI)](https://ja.wikipedia.org/wiki/%E7%B6%99%E7%B6%9A%E7%9A%84%E3%82%A4%E3%83%B3%E3%83%86%E3%82%B0%E3%83%AC%E3%83%BC%E3%82%B7%E3%83%A7%E3%83%B3)を簡単に、しかもむちゃくちゃ強力に支援してくれるサービスです。

要は、みんなが大嫌いなテスト作業とかデプロイ作業を、コードが更新されるたびに自動的にやってくれます。

CircleCIには結構いろいろな設定がデフォルトで入っているので、WebUIからの設定だけで終わったり、そうでなければ`circle.yml`を数十行ちょちょいと書くだけでCIできます。

ちなみに無料。[有料プランもある](https://circleci.com/pricing/)けど使いみちがまだみえｎ（ｒｙ

アカウント作るだけ作って、GitHubと連携するだけして放置すると、中の人から「大丈夫？設定手伝ったほうが良い？」というメールが飛んできたりします。その優しさに涙が出そう。

この記事では登録方法とか細かい使い方とかの説明はしませんが、その辺に関しては中の人の優しさが溢れている[公式ドキュメント](https://circleci.com/docs/getting-started/)をご覧ください。


## さっそく作業を押し付ける

CircleCIに登録して、プロジェクトの設定を済ませて、特にエラーもなく(テストがないから赤いけど)↓のような感じにビルドが済むようであれば、やらせたい作業を`circle.yml`に書くお時間です。

![CircleCI build result](/img/2016/11/circleci_build_result.png)

出来上がった`circle.yml`は[こちら](https://github.com/MiketaNyoroN/miketa-nyoron-blog/blob/3b22f5fad69ea6a6295e149a08cefa6fc92df74c/circle.yml)になります。リポジトリのルートディレクトリに置いてください。

ここから下は作った`circle.yml`の解説をしていきます。

### 何となくタイムゾーン設定

```yaml
machine:
  timezone: Asia/Tokyo
```

Hugoがどのように日時を扱うのかは知りませんが、とりあえず日本に住んでいるのでタイムゾーンを設定します。

「タイムゾーンの一覧は[この辺](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones)を見ると良いよ」って[公式ドキュメント](https://circleci.com/docs/configuration/#timezone)に書いてありました。

### 存在をよく忘れるsubmodules

```yaml
checkout:
  post:
    - git submodule sync
    - git submodule update --init --recursive
```

僕はHugoのテーマを`git submodule add`で追加したので、上記の記述が必要になります。これを忘れるとビルドが終わっても寂しい見た目になるので気をつけましょう。

テーマの説明通りにインストール(`git clone`を使う方)した場合は必要ありません。

### 必要なソフトウェアをインストール

```yaml
dependencies:
  pre:
    - go get -v github.com/spf13/hugo github.com/tdewolff/minify/cmd/minify
    - sudo pip install awscli
```

今回はHugoでページを生成して、生成されたものをMinifyしたいので、[Hugo](https://github.com/spf13/hugo)と[Minifyのコマンドライン版](https://github.com/tdewolff/minify/tree/master/cmd/minify)をインストールします。

あと、生成したページをS3にデプロイしたいので、[AWS CLI](https://aws.amazon.com/jp/cli/)を入れておきます。

### ステータスが赤いとなんか許せない

```yaml
test:
  override:
    - hugo
```

上記の記述で、Hugoを使ってページを生成します。

`test:`配下に入れなくても良いような気もしますが、テストが何もないとビルド結果が黄色とか赤色になりそうなのでここに入れました。

`override`にしたのはCircleCIが用意してくれてるテストが別に必要ないからです。てへぺろ。

### どこまで押し付けるかはアナタ次第

```yaml
deployment:
    production:
        branch: master
        commands:
            - rm -rf ./public/js ./public/fonts
            - ls -d ./public/css/* | grep -v -E 'phlat.css$' | xargs rm -r
            - minify -v -a -r --html-keep-document-tags --match '\.(htm|html)$' -o ./public/ ./public
            - minify -v -a -r --match '\.xml$' -o ./public/ ./public
            - minify -v -a -r --match '\.css$' -o ./public/ ./public
            - aws s3 sync ./public/ s3://${s3_bucket}/ --delete --region ${aws_region}
```

ここが本体。デプロイ処理の内容を記述します。

`production:`はラベルみたいなものです。名前はなんでもいいですが、同じ名前を2回以上使うことはできません。

`branch:`にGitのブランチ名を指定することで「`production`ではこのブランチを使うぜ！」的なことを指定します。今回は`master`です。記事を書いたりブログいじったりするのは今のところ一人ですし。

`commands:`には処理をどんどん書いていきます。

- 1-2行目: 余分なファイルの削除
- 3-5行目: Minifyで処理するところ
- 6行目: S3へファイルをデプロイ

6行目を見るとわかりますが、環境変数を利用することが出来ます。

![Environment variables](/img/2016/11/circleci_environment_vars.png)

これを使うことでS3のバケット名が変わったり、リージョンが変わったりしても、CircleCIの設定画面から対応できます。

また、CircleCIの設定画面にAWSのアクセスキーを設定することで、AWSへのアクセス周りをいろいろ良い感じに処理してくれます。

![AWS access keys](/img/2016/11/circleci_aws_access_key.png)

環境変数もアクセスキーも、上記の画像のように自動的にマスキングしてくれるのでスクリーンショットを撮るときや、複数人でCircleCIを利用する際は非常に助かりますねー。[^2]

ついでに`$ twitter "なんかつぶやく内容"`みたいなコマンドを作ってデプロイ処理に挟めば、「ブログの更新情報をTwitterでつぶやく」という作業もCircleCIに押し付けられますね。

しかもリアルタイム。夢が広がる。


## あとはブログを書きまくるだけ

以上で設定は終わりです。

`hugo new post/[ファイル名].md`してページを作成し、

![Create new post](/img/2016/11/hugo_new_post.png)

お気に入りのエディタを使って記事をMarkdownでゴリゴリと書いて、

![Writing article](/img/2016/11/writing_article.png)

書き終わったら`git add` `git commit` `git push`するだけで、

![Add article to GitHub](/img/2016/11/add_article_to_github.png)

CircleCIがビルドとMinifyingとデプロイをやってくれて、

![CircleCI building](/img/2016/11/circleci_building.png)

ブログが更新されます。

![Blog updated](/img/2016/11/blog_updated.png)

これは捗りますわ…。やべぇ…やべぇ…。


## おまけ

今回の頑張りのおかげで、ページ表示速度は結構いいスコアがでます。

![PageSpeed Insights Result](/img/2016/11/pagespeed_insights_first.png)

たのしい。ごっさ楽しい。


[^1]: GitHub Pagesに置けばタダじゃね？というツッコミはなしの方向で
[^2]: とはいえ、SSHすると覗けるんじゃないかな
