+++
author = "MiketaNyoroN"
categories = ["ネットワーク機器","小ネタ"]
date = "2017-06-03T12:34:06+09:00"
description = "WLX202のコンソールポートって何？繋いでみます"
tags = ["WLX202","(保守用)","ヤマハ","好きな子はいじめたくなるタイプ","保証切れたりしないよねこれ"]
title = "WLX202のCONSOLE ポート（保守用）で遊ぶ"

+++

[おちんぎんを溶かした](/post/wlx202-review/)にょろんです。

いろいろ設定をいじいじし終わって安心して無線に依存できるので、前から余りまくってた有線ポートがさらに余ってます。

<blockquote class="twitter-tweet" data-lang="ja"><p lang="ja" dir="ltr">我ながら今回の配線はスッキリ仕上げられたけど、無線が強化されたおかげで有線接続が少ないのと、ケーブルの長さと色が統一できてないのがちょっとモヤモヤ。 <a href="https://t.co/BzPn9cRmKe">pic.twitter.com/BzPn9cRmKe</a></p>&mdash; 三毛田 にょろん (@MiketaNyoroN) <a href="https://twitter.com/MiketaNyoroN/status/870646073173266432">2017年6月2日</a></blockquote>
<script async src="//platform.twitter.com/widgets.js" charset="utf-8"></script>

[WLX202](http://amzn.to/2r4RC7q)の設定をいじいじする際に、[説明書とか仕様書](http://www.rtpro.yamaha.co.jp/RT/manual.html)をいろいろ読み込むタイプなのですが、[製品ページの外観図](http://jp.yamaha.com/products/network/wireless_lan/wlx202/)を見ていたら気になるものがありました。

「CONSOLE ポート（保守用）」だって。保守用？ほほう。

**繋ぐしかあるまい。**

<!--more-->


## 何も考えず接続

エンジニアなら誰でも、(RJ-45の)穴があったら(コンソールケーブルを)挿し込みたいと思うに違いない。

と言うわけで、保護カバーを外して、Ciscoのコンソールケーブルを挿し込んでみました。

![WLX202 CONSOLE ポート（保守用）](/img/2017/06/wlx202_console_port.jpg)

そして、 `screen /dev/cu.usbserial-FTxxxxxx` でちょちょいと接続すると…。

![反応なし](/img/2017/06/no_response.png)

うんともすんとも言わない。ほほう。

Enterキーを何回ひっぱたいても、ログインプロンプト等が表示されません。

ヤマハさんが保守に使うためにあるので簡単に使えるとは思っていませんでしたが、なんか寂しいですね。


## 起動ログだけでもみたい

何も操作できないのであれば仕方ない。せめて再起動して起動ログを見てみます。

電源ぶつ切りで再起動させるとWLXちゃんの健康に悪そうなので、管理UIから再起動させます。

そうするとターミナルに何か…。

![文字化けら](/img/2017/06/baudrate_mismatch.png)

うん。[WLX402](http://amzn.to/2rPdNQd)のボーレート(9600)に合わせてみましたが、WLX202は違うようです。


## 再チャレンジ

総当りで正しいボーレート(115200)を探し当てつつ…。

```plain
$ screen /dev/cu.usbserial-FTxxxxxx 115200
```

もう一回再起動すると今度は出てきました。やったぜ。

![WLX202 起動ログ](/img/2017/06/wlx202_bootlog.png)

でもやっぱり起動後にEnterをひっぱたいてみてもプロンプト等は出てきません。残念。

全文はこんな感じ。

```plain

YAMAHA BootRom Ver.1.10 (Dec 30 2015 - 13:24:36)

WLX202 - CPU :Scorpion 1.0 , CPU:720MHZ, DRAM:300MHZ, ABH:200MHZ
DRAM:  32bit, ddr2 init, 128 MB
Top of RAM usable for U-Boot at: 88000000
Reserving 360k for U-Boot at: 87fa4000
Reserving 192k for malloc() at: 87f74000
Reserving 44 Bytes for Board Info at: 87f73fd4
Reserving 36 Bytes for Global Data at: 87f73fb0
Reserving 128k for boot params() at: 87f53fb0
Stack Pointer at: 87f53f98
Now running in RAM - U-Boot at: 87fa4000
Memory Test Start
Test start(0x80000000) end(87f00000) size(7f00000)
Pattern: 55555555
Pattern: AAAAAAAA
Pattern: 00000000
Memory Test Pass

SPI Flash:  8 MB
In:    serial
Out:   serial
Err:   serial
Net:   ath_gmac_enet_initialize...
QCA8344 Switch ID is xxxx
Mac: ac:44:f2:xx:xx:xx

Atheros on-chip NAND FLash Controller Driver, Version 0.1 (c) 2010 Atheros Communications, Ltd.
Ath Nand ID[87fea17c]: xx:xx:xx:xx:xx
ONFI MICRON      MT29F2G08ABAEAWP
Micron NAND 256MiB 3,3V 8-bit [256MB]
Hit specific key to stop autoboot:  0

Check Main image:

NAND read: device 0 offset 0x80000, size 128 ...  128 bytes read: OK
Kernel size:0x140000, Rootfs size: 0xe60000, Total size 0xfa0081

NAND read: device 0 offset 0x80080, size 1310720 ...  1310720 bytes read: OK

NAND read: device 0 offset 0x280000, size 15073281 ...  15073281 bytes read: OK

Check Backup image:

NAND read: device 0 offset 0x2080000, size 128 ...  128 bytes read: OK
Kernel size:0x140000, Rootfs size: 0xe60000, Total size 0xfa0081

NAND read: device 0 offset 0x2080080, size 1310720 ...  1310720 bytes read: OK

NAND read: device 0 offset 0x2280000, size 15073281 ...  15073281 bytes read: OK

Main image is good ,checksum is 0x9b
Backup image is good ,checksum is 0x9b
Main image the same as Backup image

Loading from device 0: ath-nand (offset 0x80080)
   Image Name:   WLX202-Rev.16.00.09
   Created:      2017-03-24   1:57:25 UTC
   Image Type:   MIPS Linux Kernel Image (lzma compressed)
   Data Size:    1103366 Bytes =  1.1 MB
   Load Address: 80060000
   Entry Point:  80291c90
## Booting image at 81000000 ...
   Image Name:   WLX202-Rev.16.00.09
   Created:      2017-03-24   1:57:25 UTC
   Image Type:   MIPS Linux Kernel Image (lzma compressed)
   Data Size:    1103366 Bytes =  1.1 MB
   Load Address: 80060000
   Entry Point:  80291c90
   Verifying Checksum at 0x81000040 ...OK
   Uncompressing Kernel Image ... OK
No initrd
## Transferring control to Linux (at address 80291c90) ...
## Giving linux memsize in bytes, 134217728

Starting kernel ...

```

`Hit specific key to stop autoboot: ` のような気になる行はありますが、やらかした時に元に戻せる自信がさっぱり無いのでEnter連打は控えておきます。

WLX202の中身をちょっと知れて面白かった。


## おまけ

Linuxの文字がちょこちょこ出てくるので、なんか危なげなサービスが開けっぱだったりしないかな？と `nmap` してみましたが、TCP 80番以外は全部叩き落されました。お見事。

某N社のAなんとかは無駄にTelnet等々が開いてて気持ち悪かったので、余計なポートが開いてないだけでも大好きです。WLXえらい。

