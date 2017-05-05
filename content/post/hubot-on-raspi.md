+++
author = "MiketaNyoroN"
categories = ["Raspberry Pi","Hubot"]
date = "2017-05-05T22:55:02+09:00"
description = "Hubotを初代Raspberry Piにインストールします"
tags = ["Hubot", "Arch Linux", "Raspberry Pi", "Archやっぱ大好き♡"]
title = "Hubotを初代ラズパイにぶち込んだ"

+++

最近なぜか艦これを再開したにょろんです。

イベントとかは全く関係なく、「天ちゃんとRJちゃんと比叡ちゃんに会いてぇなぁ」とかそんなくだらない理由です。

んで、それ用のAPIのSDKとかDocs作れねぇかなぁと妄想中に、Botのフレームワーク(というかライブラリかAPI？)であるHubotさんの話題がTwitterに出てきて見事脱線。

脱線ついでのGWの暇つぶしに、HubotさんをRaspberry Pi(以下RasPi)に生息させてみます。

<!--more-->

## Hubotさんの生息予定地

まずRasPiですが、手持ちは[RasPi 1 B](https://www.raspberrypi.org/products/model-b/)しかないので、こいつを使います。

RAMが512MBで、USB端子が2個で、microSDじゃなくてSDカードをつかうあの子です。「+」ですらありません。

CPU性能で言えば5ドルのRasPi Zeroに負けてます。僕の8,000円はいずこへ。

OSはArch Linux ARMを使ってます。GUIすら入れてないので超軽量。超おすすめ。みんな使って（露骨な布教）


## 下準備

[公式のGetting Started](https://hubot.github.com/docs/)を参考にして下準備をします。


### 必要なパッケージのインストール

Node.jsと、npm(Node.jsのパッケージマネージャー)をインスコします。

```plain
# pacman -S nodejs npm
```

Fedora等々のRHEL系だったら下記で。バージョンによっては `--enablerepo=epel` しないとダメかもです。

```plain
# yum install nodejs npm
```

Raspbianだと下記の方法かと思います。Debian系はまださっぱりですが。

```plain
# apt-get install nodejs
```

Archだと、インストールが終わったらだいたい最新版です。

```plain
[nyoron@nyoropi ~]$ node --version
v7.10.0
[nyoron@nyoropi ~]$ npm --version
4.5.0
```

他のディストリだと、Node.jsのバージョンがv6系とかv5系になるかと思いますが、Hubotさんはその辺を気にせず動いてくれるので大丈夫です。v7のほうが動かない罠があったりしますし。

もし「意地でも最新版にしたい」という方がいれば、[この辺を参考](http://parashuto.com/rriver/tools/updating-node-js-and-npm)にするとアップデートが楽です。


### 使うかもしれないパッケージのインストール

Redisをインスコします。Hubotがなんか記憶するときに使うインメモリDB(Brain)になります。

```plain
# pacman -S redis
```

プラグインによってはBrainをガンガン使ったりするので、インストールしておいて損はないでしょう。

ただあんまり使われると困るんですけどね。RasPiのRAMが512MBですし。


### Hubotの下準備

次に、npmをつかってHubotさんをインスコします。

コマンドは下記。公式ドキュメント通りです。

```plain
# npm install -g yo generator-hubot
```

npm installはもともと早くないので、RasPiのCPUだとアニメ1本見れる程度には時間がかかります。

インストールに失敗する場合は、もしかしたらGCC等の開発系パッケージのインストールが必要かもです。

よくやらかすのは権限不足ですね。 `sudo` を忘れたばっかりにくっそくっそ。

<blockquote class="twitter-tweet" data-lang="ja"><p lang="ja" dir="ltr">くっそくっそ。 <a href="https://t.co/1Ckav3aO2Y">pic.twitter.com/1Ckav3aO2Y</a></p>&mdash; 三毛田 にょろん (@MiketaNyoroN) <a href="https://twitter.com/MiketaNyoroN/status/860517439297028097">2017年5月5日</a></blockquote>
<script async src="//platform.twitter.com/widgets.js" charset="utf-8"></script>


## Hubotのインストール

やっと本体というかテンプレートのインストールです。

下記コマンドで、Botの名前をつけたディレクトリを作成して、テンプレート的なものを作成します。

```plain
$ mkdir nyorobot
$ cd nyorobot
$ yo hubot
```

`yo hubot` すると、Hubotさんになんかいろいろ聞かれます。

僕はとりあえずTwitterBotっぽいものが欲しかったので、下記のように答えました。

```plain
? Owner User <user@example.com>
? Bot name NyoroBot
? Description etc etc ...
? Bot adapter twitter
```

Bot adapterについては、[この辺を探す](https://hubot.github.com/docs/adapters/)と使いそうなのが見つかると思います。

僕の場合はTwitterしか使いそうになかったので、 hubot-**twitter** の `twitter` の部分をBot adapterに入力しました。

もちろんあとで追加も可能なので、面倒な場合はEnter連打でもいいです。たぶん。

質問に答え終わるとまたインストールタイムです。アニメの感想をTwitterにつぶやいて時間を潰しましょう。


## エラーこわい

インストールも終わって、RasPi上でひとまず実行可能な状態になりました。…が、実行してもエラーが出てきて鬱陶しいので直します。

まずは、将来的に消え去ってしまう、不要なファイルを消しておきます。

```plain
$ rm hubot-scripts.json
```

その次、さっぱり使わないプラグインが `external-scripts.json` に書いてあるので消します。

僕は下記のようになりました。

```json
[
  "hubot-diagnostics",
  "hubot-help",
  "hubot-redis-brain",
  "hubot-rules"
]
```

各プラグインの詳細については解説しませんが、ひとまず上記のプラグインがあれば、ローカルでのテストには困りません。


## やっと実行

`bin/hubot` を実行するとプロンプトが出てきます。

`[Botの名前] help` とか `[Botの名前] ping` とか話しかけてみましょう。無機質な返事が返ってきます。

```plain
$ bin/hubot
[Fri May 05 2017 16:45:06 GMT+0000 (UTC)] INFO hubot-redis-brain: Using default redis on localhost:6379
NyoroBot> nyorobot help
Shell: NyoroBot adapter - Reply with the adapter
NyoroBot echo <text> - Reply back with <text>
NyoroBot help - Displays all of the help commands that Hubot knows about.
NyoroBot help <query> - Displays all help commands that match <query>.
NyoroBot ping - Reply with pong
NyoroBot the rules - Make sure hubot still knows the rules.
NyoroBot time - Reply with current time

NyoroBot> nyorobot ping
PONG

NyoroBot>
```

個人的に好きなのは `the rules` です。ロボットらしく3原則を答えてくれます。教育(スクリプト)によっては触るもの全てに害を及ぼしてくれるので、さっぱり当てになりませんけどね。

ちなみに、起動直後に「Redisつかうべ〜」と言われますが、Redisサーバーを起動しなくても大丈夫です。Brainを使うプラグインがさっぱり入っておりませんので。


## インストール終わり

以上でインストールはおわりです。あっさりでしたね。

正直なところ、RasPiにHubotをぶっこむメリットはそんなに無いです。LAN内に生息してることを活かして、「NAS生きてる？」とか「RTX再起動して」とか「AP再起動して」お願いするのが精一杯でしょうか。

それはそれでちょっと便利か。

アイディアとかがあれば、ちょっと実装してみます。
