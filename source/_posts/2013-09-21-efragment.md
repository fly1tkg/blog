---
title: EFragmentについて – AndroidAnnotations
author: fly1tkg
layout: post
permalink: /2013/09/efragment/
categories:
  - Android
tags:
  - androidannotations
---
fragmentでAndroidAnnotations(以下AA)を利用するには次のようにします

<div>
  <div id="highlighter_361599">
    <pre class="brush: java; gutter: true">@EFragment
public class MyFragment extends Fragment {
 
}</pre>
  </div>
</div>

fragmentもActivity同様に\_のsuffixが入ります。なので利用する時はMyFragment\_のようにアンダースコアを追加してください。

例えばXMLで利用する場合は次のようになります。

<div>
  <div id="highlighter_586003">
    <pre class="brush: xml; gutter: true">&lt;LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="horizontal" &gt;
 
    &lt;fragment
        android:id="@+id/myFragment"
        android:name="com.company.MyFragment_"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent" /&gt;
 
&lt;/LinearLayout&gt;</pre>
  </div>
</div>

またJavaのコード上では次のように利用できます

<div>
  <div id="highlighter_785306">
    <pre class="brush: java; gutter: true">MyFragment fragment = new MyFragment_();</pre>
  </div>
  
