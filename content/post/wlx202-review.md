+++
author = "MiketaNyoroN"
categories = ["ネットワーク機器","レビュー"]
date = "2017-05-26T21:28:11+09:00"
description = "WLX202 + SWX2200-8PoEを組み合わせてみます"
tags = ["IYH","WLX202","SWX2200-8PoE","ヤマハ","控えめに言って最高","ヤマハだいしゅきいいぃぃぃ！！"]
title = "ヤマハの無線APを自宅に導入したった！"

+++

おちんぎんと保険金が入ってきたのを良いことに調子に乗っているにょろんです。

「ふへへ…欲しいものが買えるぜ…」なんて考えていたら、酔っ払った勢いで前から欲しかったものをIYHしてしまいました。

ということで、IYHした[WLX202](http://amzn.to/2r4RC7q)と[SWX2200-8PoE](http://amzn.to/2roy0wc)を自宅に設置して遊んでみます。

<!--more-->

## なにそれ？

無線LANアクセスポイント(WLX202)とPoEスイッチ(SWX2200-8PoE)です。

<blockquote class="twitter-tweet" data-lang="ja"><p lang="ja" dir="ltr">電源を入れる前に重ねてパシャり。 <a href="https://t.co/2y9suLjQXd">pic.twitter.com/2y9suLjQXd</a></p>&mdash; 三毛田 にょろん (@MiketaNyoroN) <a href="https://twitter.com/MiketaNyoroN/status/867570487378890752">2017年5月25日</a></blockquote>
<script async src="//platform.twitter.com/widgets.js" charset="utf-8"></script>

一般的なご家庭でも、超絶安定した動作をしてくれる素敵な子たちです。


## 設置環境

ルーターに[RTX1200](http://amzn.to/2r57cQt)が居るだけの、ごくごく一般的な環境です。

初期設定をすれば、その辺に転がってるルーターにくっつけても動きます。


## まずはちゃちゃっと配線

RTX -> SWX2200 -> WLX202と、とりあえず適当に繋げます。

SWX2200-8PoE等のIEEE802.3af対応の機器がある場合は、WLXの電源ケーブル等々は必要ありません。Cat5e以上のケーブル1本で終了です。

IEEE802.3af対応の機器がない場合は、WLX用の電源ケーブルや、その辺にあるPoEインジェクターをご購入ください。


## 電源を入れて設定

L2MS対応なヤマハ機器を使っているのであれば、スイッチ制御から配下のヤマハ機器を設定できます。

<blockquote class="twitter-tweet" data-lang="ja"><p lang="ja" dir="ltr">とうとう…スイッチ制御にRTXちゃん以外の機器が表示されるようになったよ…！ <a href="https://t.co/R7esQkg88B">pic.twitter.com/R7esQkg88B</a></p>&mdash; 三毛田 にょろん (@MiketaNyoroN) <a href="https://twitter.com/MiketaNyoroN/status/867599327866245120">2017年5月25日</a></blockquote>
<script async src="//platform.twitter.com/widgets.js" charset="utf-8"></script>

GUIを使った設定については、設定画面にくっついてるヘルプを見たほうが早いので割愛します。


## パフォーマンスはどんなもんよ？

特に何もいじらず、5GHzで繋げたMBAからローカルのWindows Serverに対して `iperf` したら150Mbpsくらいでした。iPadから外部につなげても150Mbps。

まぁ、前に使っていた某At○rmの4,5倍くらいの速度が出ているので満足です。

速度については僕の設定がほぼ問題な気がしますけど。

安定性についてですが、前のアイツと比べるのが失礼な程度に良いですね。壁を3,4枚挟んでも速度・安定性共に影響無し。

2.4GHzと5GHzを両方出しっぱなしで、クライアントが5台くらい繋がりっぱなしの状態でこれだけ安定してるので、どこで使っても問題なさそうですね。


## で、いくらしたの？

「**うっかり**」新品で購入したので…下記画像を参考にしていただければ…。

<blockquote class="twitter-tweet" data-lang="ja"><p lang="ja" dir="ltr">ｲｲｨｨｨｲｲｨｨﾔｯｯｯﾎｵｵｵｫｵｵｫｨ!! <a href="https://t.co/PvMWDfksi5">pic.twitter.com/PvMWDfksi5</a></p>&mdash; 三毛田 にょろん (@MiketaNyoroN) <a href="https://twitter.com/MiketaNyoroN/status/867352472309743620">2017年5月24日</a></blockquote>
<script async src="//platform.twitter.com/widgets.js" charset="utf-8"></script>

後悔も反省もしていないどころか、性能と快適さに大満足です。実家にも置きたいですねぇ…。

WLX202自体はそこまでお高くないので、チャレンジしてみてはいかがでしょうか？
