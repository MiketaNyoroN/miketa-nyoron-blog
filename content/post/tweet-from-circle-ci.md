+++
title = "CircleCIにブログの更新を呟いてもらう"
date = 2017-12-01T21:59:45+09:00
description = "Cicle Ciを使って、Hugoで生成したブログの更新情報を自動的にTwitterへ投稿します"
author = "Miketa NyoroN"
tags = [ "CircleCI", "Twitter", "自動化", "衝動で実装した", "ネタ", "用途不明" ]
categories = [ "健忘録" ]
+++

「[CircleCIにブログの更新情報ツイートも押し付ければ楽じゃね？](/2016/11/play-with-circle-ci/)」と書いてからはや1年ちょい。

Twitterに更新情報つぶやく作業とかしないだろう思って放置していました。

しかし、書いておいてやり方も書かずに放置するのも気持ち悪いなぁと思って、やっぱり自動化することにしました。

事前にネタバレしておきますが、一番簡単なのはIFTTTのような自動化サービスを使ったRSS→Twitter連携。

僕みたいに「とりあえず実装したい気分」ってだけの理由で、手間をかけたい方におすすめの記事となっております。

<!--more-->


## レシピ

今回使うのは下記2つ。

- [CircleCI](https://circleci.com/)
- [tw](https://shokai.github.io/tw/)

Twitterに投稿する時に使うツールは、設定とコマンドがシンプルな `tw` を選びました。CircleCIは `gem` コマンドが使えるのでインストールの手間もあまりないです。

Twitter APIを `curl` でぶっ叩くだけでツイートできればもっと楽なのですが、そんな美味しい話は無かった。


## tw の設定ファイル生成

まずはローカルにちょちょいと `tw` をインストールします。

```bash
$ gem install tw
```

インストールしたらちょちょいと初期設定します。PINを入れると直近のリプライがいくつか出てくるので、動作確認いらず。

```bash
$ tw
open https://api.twitter.com/oauth/authorize?oauth_token=o2auxxxxxxxxxxxxxxxxxxxxxbc
input PIN Number: 3xxxxx1
add "@MiketaNyoroN"
[10/15 Sun] (13:33:36) @xxxxxxx : @MiketaNyoroN xxxxxxxxxxxxxxxxx
[10/15 Sun] (15:06:40) @xxxxxxx : @MiketaNyoroN xxxxxxxxxxxxxxxxx
[10/17 Tue] (21:16:02) @xxxxxxx : @MiketaNyoroN xxxxxxxxxxxxxxxxx
....
```

設定が終わるとホームディレクトリに設定ファイル(`~/.tw.yml`)が作られます。

```yaml
---
version: 1.2.0
consumer_key: Axxxxxxxxxxxxxxxxxxw
consumer_secret: 2xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxU
default_user: MiketaNyoroN
users:
  MiketaNyoroN:
    access_token: 1xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx2
    access_secret: txxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxp
    id: 125472562
```

この設定ファイルを誰でも見れるGitHubとかブログとかに載せちゃうと、**誰でも勝手にTwitterアカウントを操作できてしまう**ので、「うっかり設定ファイル公開しちゃった」とかが無いように気をつけましょう。


## CircleCIの設定

とはいえ、非公開な場所に設定ファイルを置くのもなかなか色々あって難しいです。

ってことで、まずは設定ファイルをBase64にエンコードして…

```bash
$ base64 ~/.tw.yml
```

↓の `tw_conf` みたいな感じにCircleCIの環境変数にぽいっと投げ込みます。

![CircleCI Environment variables setting](/img/2017/11/circle_ci_env_setting.png)

あとはCircleCIが環境変数をお漏らししないことを祈ります。

なんでBase64かというと僕が好きだからです。Base64はいいぞ。


## circle.yml の設定

次は `tw` をCircleCIのビルド環境へインストールします。

といってもRubyもGemも入っているので別に難しいことはなく、 `dependencies` にインストールするためのコマンドをペペっと書くだけです。

```bash
gem install tw
```

最後に、`circle.yml`にTwitterへ投稿するコードを仕込みます。

Base64文字列をデコード→最新の記事の名前を取得→URL生成してツイートという面倒な流れです。

[実際のコード](https://github.com/MiketaNyoroN/miketa-nyoron-blog/blob/6d18e2ed6e2b25d08b0c758ab30aa9c7bcdfc05f/circle.yml)はこんな感じ。

```bash
echo $tw_conf | base64 -d > ~/.tw.yml
tw -yes ブログ更新したった！ http://miketa.nyoron.jp/`date +'%Y/%m'`/`git diff HEAD~ --name-only | grep '^content/post/' | awk -F'[/.]' '{print $(NF-1)}'`/
```

予想より短く済みました。んっふー。


## 2行目がながーい

```bash
tw -yes ブログ更新したった！ http://miketa.nyoron.jp/`date +'%Y/%m'`/`git diff HEAD~ --name-only | grep '^content/post/' | awk -F'[/.]' '{print $(NF-1)}'`/
```

やっている事は単純で、

1. 最後のコミットで更新されたファイルで
2. かつ`content/post/`から始まるファイルの
3. ディレクトリ名と拡張子を除いたファイル名を取り出して
4. ブログのURLにくっつけてツイートする

というコマンドです。

だいたい1回のコミットで1記事しか更新しないので、 `head` とか `last` で1行だけにするのは省きました。

また、記事以外の更新でいちいちツイートされては鬱陶しいので、 `content/post/` 内のファイルだけを見るようにしています。

変数をつかって2行に分けようとしたのですが、なぜか記事名が表示されずに悲しい思いをしたので1行にしました。

![Wrong Tweet](/img/2017/12/wrong_tweet.png)

<br>

ちなみに、ぱっと見で上記コマンドの意味が分かった人は比較的変態と思われます。

解説したことすら恥ずかしいので可及的速やかに忘れていただくか、Twitterに「こっちの方が短いぞｵﾗｧ」とご指摘ください。

GitHub大好きっ子な方は、ツイートの代わりにPRをいただけると無視しづらいのでおすすめです。


## 成果

CircleCIがブログ記事をビルドして、

![CircleCI Tweeting](/img/2017/12/circleci_tweet.png)

Twitterにツイートしてくれます。

![Tweet](/img/2017/12/tweet.png)

問題なく動きました。さて、設定消すか。


## 最後に

良い子はIFTTTとかZapierをご利用ください。今回の用途だと間違いなく、簡単・確実・安全です。

セキュリティ的にちょっと嫌な顔になるショートリンクとかも、ちゃんと設定で無効化できます。

ただ、つぶやく速度的には最速な方法かと思います。アップロード完了直後に自動ツイートですので。

我ながらなんでこんなものを作ったのか分からないのですが、こんなものを作ってしまったのは初めてではないので後悔もしていません。



## P.S.

今更ながら、IFTTTの収益の稼ぎ方が素敵だなぁって思いました。

特に、サービスを利用するユーザーからではなく、連携するアプリケーション側から利益をもらっているのが面白いですねー。

これまでその辺の自動化サービスは（自分で作れるので）興味がなくて触ってなかったため気が付かなかったのです。

お金を持ってる方からもらうというのも感覚的にすごくしっくりきたので、マネタイズにもいろいろな方策があるもんだとすこし感動したこの頃です。

IFTTTはブランドイメージとUXの向上が収益の元なのでそのへんも面白そうな。

Zapierは（収益化部分が）実に普通なのであんまり興味は沸かないっす。
