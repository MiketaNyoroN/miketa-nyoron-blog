+++
description = "Hugoの公開テーマを改造します"
author = "MiketaNyoroN"
tags = [
  "Hugo",
  "テーマ",
  "改造"
]
categories = [
  "Hugo",
]
date = "2016-11-17T18:53:20+09:00"
title = "Hugoのテーマを自分好みに改造したった"

+++

Octopressで生成した旧ブログですが、放置[^1]していたので[Hugo](https://gohugo.io/)で作り直しました。

なぜかは思い出せないですが、前のやつはなんか…いまいち好きになれなかったです。本体の更新と初期設定が面倒臭かったのが主な要因な気がしますが。

さて、「Hugoでブログ作るよ！」なんて記事を書くと「また構築記事なんでしょ？もういいよ」とか言われそうなほど[構築記事](https://www.google.co.jp/webhp?hl=ja&sa=X#hl=ja&q=hugo+%E3%83%96%E3%83%AD%E3%82%B0)がある[^2]のですが、みんなと同じことやるのがあまり好きじゃないタイプなので、タイトル通りニッチ（？）なところを攻めていきます。

<!--more-->

## テーマの改造 is 何

Hugoの好きなところは、テーマの追加・適用・上書きが簡単なところです。

追加は`themes`ディレクトリに`git clone`（個人的には`git submodule add`）するだけで、

```no-highlight
$ cd themes
$ git clone [Theme URL]
```

適用は`config.toml`の`theme`の行をいじるだけ、

```no-highlight
theme = "hugo-example"
```

上書きは元ファイルと同じ構造のディレクトリと、同じ名前のファイルを別の場所に追加するだけです。

```no-highlight
[Siteのルート]/themes/hugo-example/static/css/example-design.css
↓
[Siteのルート]/static/css/example-design.css
```

上書きしまくることで自分が好きなようにテーマをいじくることができます。


## 実際にやってみた

ベースにしたテーマは[Phlat Theme](http://themes.gohugo.io/hugo-phlat-theme/)で、[改造箇所](https://github.com/MiketaNyoroN/miketa-nyoron-blog/compare/5decc16814285146d0852f905b42c602f2266b8a...e5fa73a87ea3b39fa09eb54ce7c1691d8b9e0caf)を軽くまとめると下記のような感じ。

- 日本で馴染みのなさそうなシェアアイコンを削除
- はてなブックマークのアイコン[^3]とシェアボタンを追加
- [highlight.js](https://highlightjs.org/)の配色を`monokai-sublime`に変更
- シンタックスに`no-highlight`を指定したときの文字色を変更
- コードブロックのフォントを変更
- ページ最下部のSNSリンクを新しいタブで開くように変更
- ページ右側のDonateを削除
- 画像に枠を追加

箇条書きに載ってない改造箇所は`config.toml`の変更で対応可能です。

本当はテーマに使われている[Bootstrap](http://getbootstrap.com/)をMarkdownでも最大限生かせるような改造を目指していたのですが、途中で面倒くさくなってHTML直書きすることで対応することにしました。

ブラウザの互換性問題がこの世から完全に無くなるのはいつになるのか…。


## ということで

気合とやる気によっては、元のテーマがわからなくなる程度に改造することも可能です。

もっとも、それほどまでに自分がほしいテーマが固まっていたら、テーマを自作したほうが早そうな気もします。

Hugoは[ドキュメント](https://gohugo.io/overview/introduction/)が結構しっかりしていたり、`hugo`コマンドで空っぽのテーマをサクッと作れたりするので、Webデザインが大好きな方はチャレンジしてみてはどうでしょうか。

テーマづくりにはプログラミング要素がちょこっとありますが、ドキュメントを読んだり[既存テーマ](http://themes.gohugo.io/)を参考にするとテーマづくりも捗ると思います。

僕はやりません。はい。


[^1]: 最初で最後の投稿が2013/12/30。ほぼ3年も放置である
[^2]: 2016年11月現在: 約 4,160,000 件
[^3]: はてブアイコンについては[こちら](http://hayashikejinan.com/webwork/css/913/)のCSSをコピペしました。感謝感謝！
