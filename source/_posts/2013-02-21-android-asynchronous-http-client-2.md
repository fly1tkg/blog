---
title: 'Androidの非同期通信ライブラリをつかう その2- Android Asynchronous Http Client'
author: fly1tkg
layout: post
permalink: /2013/02/android-asynchronous-http-client-2/
categories:
  - Android
tags:
  - android-async-http
---
続き書くとか言って、[半年も書いていなかった][1]。簡単ですが続きを書きます。  
  

# Android Asynchronous Http Clientとは？

Androidの非同期通信ライブラリの一つ、詳しくは[前回のエントリー記事][1]を参照してください。  
僕の調査によると**FxCamera**にも使われていますね。このライブラリ。(LICENSE.txtからの確認なので、ソースコードのみの利用かもしれませんが)

半年で変わったことと言えば、**同期通信が出来るようになったことですね**。(ライブラリ名とレポジトリ名に反してない？)。AsyncTaskなどとの相性で追加したようですが、、、

# MyAsyncHttpClientを作る

オススメの使い方としてMyAsyncHttpClientを作ることです。そうすることで、自分の好みにセッティングした通信を行うことが出来ます。

<div>
  <div id="highlighter_712246">
    <pre class="brush: java; gutter: true">public class MyAsyncHttpClient extends AsyncHttpClient {
    public MyAsyncHttpClient() {
        // ここに設定を書く
    }
}</pre>
  </div>
</div>

# userAgentを設定する

AsyncHttpClientのデフォルトのuserAgentは**Android Asynchronous Http Client/VERSION (http://loopj.com/android-async-http/)**です。デバッグ等のことを考えるとアプリのパッケージ名やバージョンといった文字列に変更した方が良さそうです。userAgentの変更はAsyncHttpClient#setUserAgent(String userAgent)で任意のものに変更することができます。

<div>
  <div id="highlighter_641411">
    <pre class="brush: java; gutter: true">setUserAgent("MyApp ver1.0");</pre>
  </div>
</div>

# Time out時間を設定する

Time outの時間もメソッドが準備されています。AsyncHttpClient#setTimeOut(int timeOut)です。こちらはデフォルトで10秒です

<div>
  <div id="highlighter_780440">
    <pre class="brush: java; gutter: true">setTimeOut(8);</pre>
  </div>
</div>

# Basic認証のためのユーザー名とパスワードをセットする

<div>
  <div id="highlighter_456551">
    <pre class="brush: java; gutter: true">setBasicAuth("user", "password");</pre>
  </div>
</div>

# 注意点

MyAsyncHttpClientを準備したはいいけど、いざ使うときに**AsyncHttpClientで実装しないようにしてください**（自戒）

(前回)[Androidの非同期通信ライブラリをつかう その1- Android Asynchronous Http Client][1]

 [1]: http://blog.fly1ncu.com/2012/08/android-asynchronous-http-client/
