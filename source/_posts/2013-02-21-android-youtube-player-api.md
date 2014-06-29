---
title: Android YouTube Player APIをつかったアプリ内YouTube再生
author: fly1tkg
layout: post
permalink: /2013/02/android-youtube-player-api/
categories:
  - Android
tags:
  - youtube
---
以前[WebViewを使ったYoutubeの再生方法][1]は紹介しましたが、アプリ内でYoutubeを再生する方法がもう一つあります。それはAndroid YouTube Plugin APIを利用した方法です。

Googleの日本の人のエントリーがこちら  
<http://googledevjp.blogspot.jp/2013/01/android-youtube-player-api-webview.html>

Android YouTube Player APIを利用してみてメリットとデメリットを以下にまとめました。

**メリット**

*   JavaのネイティブコードでYouTubeの再生制御ができる。
*   フルスクリーン表示や画面回転の制御が簡単

他にも上記リンクの人が言及しています。

**デメリット**

*   端末のYouTubeアプリが最新になっている必要がある。(時間がたてば解決しますが)
*   基底クラスにcom.google.android.youtube.player.YouTubeBaseActivityを使う必要があるのでActionBarSherlockとか3rdのライブラリとの菱形継承問題(まあもう片方がオープンソースならばどうにでもなりますが)

で、使い方が以下リンクに書かれているのですが、英語じゃ分かんねえよって人のために、簡単にサンプルコードを動かすまでの手順を紹介します。

<p itemprop="name">
  YouTube Android Player API<br /> <a href="https://developers.google.com/youtube/android/player/">https://developers.google.com/youtube/android/player/</a>
</p>

<h1 itemprop="name">
  アプリの登録
</h1>

<p itemprop="name">
  まずはGoogle APIs Consoreでプロジェクトを作成します。これを利用するにはGoogleのアカウントが必要です。
</p>

<p itemprop="name">
  以下リンクを開いて、Create Projectをクリックしてください
</p>

<p itemprop="name">
  Google APIs Consore<br /> <a href="https://code.google.com/apis/console">https://code.google.com/apis/console</a>
</p>

<p itemprop="name">
  <p itemprop="name">
    もし他のプロジェクトが既にある場合は、左側のメニューからCreateをクリックすると新たにProjectを作成することができます。
  </p>
  
  <h1 itemprop="name">
    YouTube Data API v3をONにする
  </h1>
  
  <p itemprop="name">
    左メニュのServiceの項目を選択し、下の方にあるYouTube Data API v3をONにします。
  </p>
  
  <h1 itemprop="name">
    API Keyの発行
  </h1>
  
  <p itemprop="name">
    左側のメニューからAPI Accessをクリックします
  </p>
  
  <p>
    Create Android Keyを押します
  </p>
  
  <p>
    ダイアログが開きますが、何も入力せずCreateボタンを押すと全てのアプリで利用できるKeyを発行できます。(ここについては詳しくは後日かけたらいいな)
  </p>
  
  <p>
    この部分のKeyを利用します。
  </p>
  
  <h1 itemprop="name">
    ライブラリとサンプルコードを入手する
  </h1>
  
  <p itemprop="name">
    ライブラリは以下リンクの右側にあるdownloadってかいてあるところからダウンロードできます。
  </p>
  
  <p itemprop="name">
    YouTube Android Player API – Download<br /> <a href="https://developers.google.com/youtube/android/player/downloads/">https://developers.google.com/youtube/android/player/downloads/</a>
  </p>
  
  <h1 itemprop="name">
    サンプルコードをインポートする
  </h1>
  
  <p itemprop="name">
    Eclipseを起動して Package Exploreの上で右クリック、import -> Android -> Existing Android code into workspaceを選択します。
  </p>
  
  <p itemprop="name">
    先ほどダウンロードした中身のsampleを選択してインポートします。
  </p>
  
  <p itemprop="name">
    srcフォルダのcom.examples.youtubeapidemoパッケージのDepeloperKey.javaを開きます
  </p>
  
  <div>
    <div id="highlighter_983196">
      <pre class="brush: java; gutter: true">public static final String DEVELOPER_KEY = null;</pre>
    </div>
  </div>
  
  <p>
    となっている所を先ほどのDeveloper Keyを入力してください。
  </p>
  
  <p>
    これでアプリを起動すれば、YouTube Android Player APIのサンプルコードを動かすことができます。<br /> <br /> <!-- blog_widget -->
    
    <br /> <ins class="adsbygoogle"<br /> style="display:inline-block;width:300px;height:250px"<br /> data-ad-client="ca-pub-6228702140385127"<br /> data-ad-slot="3243171800"></ins><br />
  </p>

 [1]: http://blog.fly1ncu.com/2013/01/android-webview-youtube/ "WebViewをつかったYouTubeの再生方法"