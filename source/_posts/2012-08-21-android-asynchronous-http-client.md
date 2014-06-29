---
title: 'Androidの非同期通信ライブラリをつかう その１- Android Asynchronous Http Client'
author: fly1tkg
layout: post
permalink: /2012/08/android-asynchronous-http-client/
categories:
  - Android
tags:
  - android-async-http
---
**Androidで通信するのめんどくさいです。**めんどくさい理由が2つあります

*   そもそもHttpClientのコード書くのがめんどくさい。
*   非同期通信をするのがめんどくさい。

１つ目は僕の主観かもしれないですが、めんどくさい。だいたい毎回同じようなコードなので僕はコピペです。しかしコピペするならライブラリを使用するのがスマートですよね。

２つ目については、Androidで通信するときってメインのスレッドでやると、そこで描画の処理が止まってフリーズしているように見えるので別スレッドで非同期に通信するのが一般的ですよね。  
**Honeycomb以降だと標準でStrictModeからNetworkOnMainThreadExceptionが投げられます。**もちろん例外を投げられないように設定する事も可能ですが、やっぱり非同期のほうがユーザーに優しいです。  
じゃあAsyncTask使うか！ってなるのですが、ちょっとした通信も全部これで書くのは、いささかやりたくない。

そんなあなたに**Android Asynchronous Http Client**  
作者は**loopj**(James Smith)さん。Githubの写真がイケメンです。Apache2.0ライセンスで公開されています。  
あの***Instagram***もこのライブラリを使用しているそうです

# 特徴

*   Callbackベースで非同期通信の処理ができる
*   Multipartでファイルのアップロードができる(Android標準のライブラリでできないのです、ぷんぷん)
*   ライブラリは25KBと小さいので、オーバーヘッドが少ない
*   gzipの設定とかその他もろもろHttpClientにいい感じの設定をやってくれといてくれる
*   バイナリのダウンロードももちろん対応、画像ファイルのダウンロードも楽々です
*   etc…

# 使い方

Projectフォルダのlibsフォルダに <http://loopj.com/android-async-http/> からDownloadできるandroid-async-http-1.4.1.jar（現在の最新）を入れます。  
一昔前はビルドパスを通すという作業をしていましたが、最新のADTではlibsフォルダに入れるだけで大丈夫なようです。(実は最近知りました、ADTのアップデートもちゃんと追わないとだめですねorz)  
またGithubからcloneしてProject Libraryとして使用してもOKです。<https://github.com/loopj/android-async-http>

あとは通信したいところで以下のようなコードを書きます。

<div>
  <div id="highlighter_177966">
    <pre class="brush: java; gutter: true">AsyncHttpClient client = new AsyncHttpClient();
client.get("http://www.google.com", new AsyncHttpResponseHandler() {
    @Override
    public void onSuccess(String response) {　　
　　　　　// ここに通信が成功したときの処理をかく
        System.out.println(response);
    }
});</pre>
  </div>
</div>

通信に失敗したときの処理(ステータスコードが200じゃなとき)は、AsyncResponseHandlerのonFailureをOverrideしたメソッドに書く事ができます。

<div>
  <div id="highlighter_535988">
    <pre class="brush: java; gutter: true">AsyncHttpClient client = new AsyncHttpClient();
client.get("http://www.google.com", new AsyncHttpResponseHandler() {
    @Override
    public void onSuccess(String response) {　　
　　　　　// ここに通信が成功したときの処理をかく
        System.out.println(response);
    }
　　@Override
    public void onFailure(Throwable error, String response) {　　
　　　　　// ここに通信が失敗したときの処理をかく
        System.out.println(response);
    }　
});</pre>
  </div>
</div>

テキストデータだけではなくバイナリデータもロードする事ができます。これで画像などのファイルもダウンロードできます。

<div>
  <div id="highlighter_655726">
    <pre class="brush: java; gutter: true">AsyncHttpClient client = new AsyncHttpClient();
client.get("画像のアドレス", new BinaryHttpResponseHandler() {
    @Override
    public void onSuccess(byte[] binaryData) {　　
　　　　　// ここに通信が成功したときの処理をかく
    }
　　@Override
    public void onFailure(Throwable error, byte[] binaryData) {　　
　　　　　// ここに通信が失敗したときの処理をかく
    }　
});</pre>
  </div>
</div>

GETやPOSTのメソッドでパラメーターをつけたいときもこんな感じ。ちなみにAsyncHttpClient#PostでPostメソッドで通信できるし、PUTやDELETEも準備されています。

<div>
  <div id="highlighter_571037">
    <pre class="brush: java; gutter: true">RequestParams params = new RequestParams();
params.put("key", "value");
params.put("more", "data");
AsyncHttpClient client = new AsyncHttpClient();
client.post("POSTしたいURL", params, new AsyncHttpResponseHandler() {
    @Override
    public void onSuccess(String response) {　　
　　　　　// ここに通信が成功したときの処理をかく
        System.out.println(response);
    }
});</pre>
  </div>
</div>

RequestParamsは以下のようにも追加する事ができます。

<div>
  <div id="highlighter_992608">
    <pre class="brush: java; gutter: true">// パラメータが1つしかないとき
RequestParams params = new RequestParams("single", "value");
 
// Mapで指定するとき
HashMap&lt;String, String&gt; paramMap = new HashMap&lt;String, String&gt;();
paramMap.put("key", "value");
RequestParams params = new RequestParams(paramMap);</pre>
  </div>
</div>

基本的な使い方はこんな感じです。

このライブラリの僕なりの使い方はまた今度書きます。

(次回)[Androidの非同期通信ライブラリをつかう その2- Android Asynchronous Http Client][1]  

 [1]: http://blog.fly1ncu.com/2013/02/android-asynchronous-http-client-2/
