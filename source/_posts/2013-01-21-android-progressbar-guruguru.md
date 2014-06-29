---
title: ProgressBarのぐるぐるをカスタマイズ
author: fly1tkg
layout: post
permalink: /2013/01/android-progressbar-guruguru/
categories:
  - Android
tags:
  - view
---
AndroidのProgressBarのぐるぐるをカスタマイズする方法全然見つからなかったのでメモ

drawableフォルダにこれを入れる。android:drawableには変えたいぐるぐるの画像を入れます。

<pre class="brush: xml; gutter: true">&lt;?xml version="1.0" encoding="utf-8"?&gt;
&lt;animated-rotate xmlns:android="http://schemas.android.com/apk/res/android"
    android:drawable="@drawable/progress"
    android:pivotX="50%"
    android:pivotY="50%" /&gt;</pre>

で、これをlayoutのProgressBarに適用。android:interminateDrawableの部分ですね。

<div>
  <div id="highlighter_947093">
    <pre class="brush: xml; gutter: true">&lt;ProgressBar
           android:id="@+id/mProgressBar"
           android:layout_width="wrap_content"
           android:layout_height="wrap_content"
           android:indeterminateDrawable="@drawable/progressbar_bg" /&gt;</pre>
  </div>
</div>

回転のスピードはDurationとか変えてみたんだけど、いまいち分からず。