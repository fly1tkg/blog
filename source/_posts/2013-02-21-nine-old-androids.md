---
title: AndroidのアニメーションBackportライブラリ「NineOldAndroids」を使う
author: fly1tkg
layout: post
permalink: /2013/02/nine-old-androids/
categories:
  - Android
tags:
  - animation
---
AndroidのアニメーションのパッケージってちゃんとAPI Level 1から準備されていて、１年前僕が始めて受託でアプリ作ったときは、iPhoneに負けまいと壮絶なコードを書いたものです。あのときは若かったなー、受託のコードにどれだけ力入れてるんだって。（いや、いまでも受託アプリに大分力入ったコード書いてるぞ、、、こういう性格なんだな）

で、その昔っからあるanimationだと色々不都合で、ちょっと前につ部のミーティングで発表したスライドがこれ。  


<div style="margin-bottom: 5px;">
  <strong> <a title="AndroidのアニメーションをちょっとだけLT" href="https://www.slideshare.net/shoichitakagi/androidlt-15647425" target="_blank">AndroidのアニメーションをちょっとだけLT</a> </strong> from <strong><a href="http://www.slideshare.net/shoichitakagi" target="_blank">Shoichi Takagi</a></strong>
</div>

<div style="margin-bottom: 5px;">
  <p>
    このなかでAnimationのBackportライブラリについて一言書いてあるのだけれど、僕は<strong>NineOldAndroids</strong>というライブラリが良いんじゃないかなと思います。
  </p>
  
  <p>
    NineOldAndroids<br /> <a href="http://nineoldandroids.com/">http://nineoldandroids.com/</a>
  </p>
  
  <p>
    こいつはHoneyCombで実装されたAnimationをBackPortするライブラリです。
  </p>
  
  <p>
    これをつくったのはJakeWhartonさん。そう、あの<strong>ActionBarSherlock</strong>を公開している人ですね。
  </p>
  
  <p>
    簡単に使い方を説明します。
  </p>
  
  <h1>
    まずはダウンロード
  </h1>
  
  <p>
    NineOldAndroids – DownLoad<br /> <a href="http://nineoldandroids.com/#download">http://nineoldandroids.com/#download</a>
  </p>
  
  <p>
    ZIPでもTGZでもよいので、ダウンロードして展開してください。
  </p>
  
  <p>
    WorkSpaceにNineOldAndroidsを追加
  </p>
  
  <p>
    Eclipseを開いて、左側のPackage Exploreの上で右クリック -> import -> Android -> Existing Android code into Workspaceと選択
  </p>
  
  <p>
    先ほどダウンロードしたファイルのlibraryフォルダとsampleフォルダを選択すればOK。これでサンプルが動くようになります。
  </p>
  
  <h1>
    サンプル以外でNineOldAndroidsを使う
  </h1>
  
  <p>
    Package Exploreで使いたいProjectを右クリック -> Propertiesを選択。
  </p>
  
  <p>
    ウインドウが開いたら左側のメニューからAndroidを選択
  </p>
  
  <p>
    Libraryの項目で先ほどのLibrary ProjectをAddすればOK。これで使えるようになります。
  </p>
  
  <h1>
    使い方
  </h1>
  
  <p>
    基本的にはHoneyComb以降のandroid.animationパッケージのクラスがそのまま使えるようになります。android.animation.*のかわりにcom.nineoldandroids.*をimportすれば使えます。sampleコードがいろんなアニメーションのデモをしているのでそちらを参考にしながらかくといいです。
  </p>
  
  <p>
    参考) android.animation<br /> <a href="http://developer.android.com/reference/android/animation/package-summary.html">http://developer.android.com/reference/android/animation/package-summary.html</a>
  </p>
</div>
