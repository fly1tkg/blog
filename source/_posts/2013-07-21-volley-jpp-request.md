---
title: JsonPullParserのJsonModelを取得するVolleyのRequestをつくってみた
author: fly1tkg
layout: post
permalink: /2013/07/volley-jpp-request/
categories:
  - Android
tags:
  - jsonpullparser
  - volley
---
JsonPullParserのJsonModelを取得するVolleyのRequestをつくってみました。

# Volleyとは？

Google I/O 2013で発表されたすごいAndroidの通信ライブラリ。どうすごいかは以下のリンクが参考になります。

[Google I/O 2013 – Volley: Easy, Fast Networking for Android][1]

[Y.A.Mの雑記帳: Google I/O 2013 – Volley: Easy, Fast Networking for Android][2]

# JsonPullParserとは？

JSONとPOJOを相互変換するライブラリです。APTの仕組みを利用しています。

<https://github.com/vvakame/JsonPullParser>

# つくったもの

<https://github.com/fly1tkg/volley-jpp-request>

# 使い方

Volleyと上記のレポジトリのjarにパスをとおして、あとはこんな感じです。

<div>
  <div id="highlighter_297764">
    <pre class="brush: java; gutter: true">mQueueRequest = Volley.newRequestQueue(this);
mQueueRequest.add(new JsonModelRequest&lt;Sample, SampleGen&gt;(JSON_URL, new Listener&lt;Sample&gt;() {
    @Override
    public void onResponse(Sample sample) {
        mJsonTextView.setText(sample.toString());
    }
}, new ErrorListener() {
    @Override
    public void onErrorResponse(VolleyError error) {
        error.printStackTrace();
    }
}));</pre>
  </div>
</div>

# パフォーマンスとか

このライブラリは内部でリフレクションを利用しているので、若干パフォーマンスが悪いです。（気になるレベルでは無いと思いますが）

リフレクションを利用した理由としてJPPでinterfaceのimplementやクラス継承が使われておらず、Genクラスのget(string json)メソッドを呼び出しつつ、ジェネリクスを利用した柔軟なクラスを作れなかったからです。JPPのアプローチから言うと、おそらくマーカーアノテーションで指定したJsonModelに対して、VolleyのRequestをAPTで生成するというアプローチがよいと思うのですが、、、、めんどくさかったんです、、、。

ジェネリクスからリフレクションを利用する方法として、黒魔術ですが、[Java変態文法最速マスター][3]の4を利用しました。  
  
<!-- blog_widget -->

  
<ins class="adsbygoogle"  
style="display:inline-block;width:300px;height:250px"  
data-ad-client="ca-pub-6228702140385127"  
data-ad-slot="3243171800"></ins>

 [1]: http://www.youtube.com/watch?v=yhv8l9F44qo&feature=player_embedded
 [2]: http://y-anz-m.blogspot.jp/2013/05/google-io-2013-android-volley-easy-fast.html
 [3]: http://d.hatena.ne.jp/Nagise/20100202/1265131791
