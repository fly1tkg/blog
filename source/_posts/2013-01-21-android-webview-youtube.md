---
title: WebViewでYoutubeを再生する
author: fly1tkg
layout: post
permalink: /2013/01/android-webview-youtube/
categories:
  - Android
tags:
  - youtube
---
初期状態のWebViewではYouTubeのiframeを埋め込んだサイトを表示できません。

# マニフェストの設定を行う

Youtubeの再生には以下の設定が必要です。

*   user-permissionのInternet
*   hardware accelarationの有効化

<div>
  <div id="highlighter_80230">
    <pre class="brush: xml; gutter: true">&lt;uses-permission
        android:name="android.permission.INTERNET"/&gt;
 
&lt;application
        android:allowBackup="true"
        android:hardwareAccelerated="true"
        android:icon="@drawable/ic_launcher"
        android:label="@string/app_name"
        android:theme="@style/AppTheme" &gt;</pre>
  </div>
</div>

&nbsp;

# WebViewのJavascriptとPluginをONにする

以下ソースを参考にJavascriptとPluginをONにします。mWebViewはWebViewのメンバ変数名です。

<div>
  <div id="highlighter_973509">
    <pre class="brush: java; gutter: true">private void initWebView() {
        mWebView = (WebView) findViewById(R.id.webView);
 
        // WebViewの設定
        WebSettings settings = mWebView.getSettings();
        settings.setJavaScriptEnabled(true);
        if (Build.VERSION.SDK_INT &gt; 7) {
            settings.setPluginState(PluginState.ON);
        } else {
            settings.setPluginsEnabled(true);
        }
 
        String html = "";
        html += "&lt;html&gt;&lt;body&gt;";
        html += "&lt;iframe width="560" height="315" src="http://www.youtube.com/embed/MU9Srs04sFU?rel=0" frameborder="0" allowfullscreen&gt;&lt;/iframe&gt;";
        html += "&lt;/body&gt;&lt;/html&gt;";
 
        mWebView.loadData(html, "text/html", null);
    }</pre>
  </div>
</div>

# 再生中に他のアクティビティに移動すると再生されっぱなしになってしまう

以下のコードを追加する

<div>
  <div id="highlighter_258286">
    <pre class="brush: java; gutter: true">@Override
    protected void onResume() {
        super.onResume();
        try {
            WebView.class.getMethod("onResume").invoke(mWebView);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
 
    @Override
    protected void onPause() {
        super.onPause();
        try {
            WebView.class.getMethod("onPause").invoke(mWebView);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }</pre>
  </div>
</div>

# サンプルコード

GitHub上に公開してあります。

<https://github.com/fly1tkg/android-youtube-webview-sample>

追記) Youtube Android Player APIをつかった方法を書きました

[http://blog.fly1ncu.com/2013/02/android-youtube-player-api/ ‎][1]

 [1]: http://blog.fly1ncu.com/2013/02/android-youtube-player-api/%20%E2%80%8E