---
title: EViewGroupについて – AndroidAnnotations
author: fly1tkg
layout: post
permalink: /2013/10/eviewgroup/
categories:
  - Android
tags:
  - androidannotations
---
@EViewGroupはViewModelのバインディングでコードを整理出来るのでとても気に入っています

まずはViewのレイアウトを作成します。

<pre class="brush: xml; gutter: true">&lt;?xml version="1.0" encoding="utf-8"?&gt;
&lt;merge xmlns:android="http://schemas.android.com/apk/res/android" &gt;

    &lt;ImageView
        android:id="@+id/image"
        android:layout_alignParentRight="true"
        android:layout_alignBottom="@+id/title"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:src="@drawable/check" /&gt;

    &lt;TextView
        android:id="@+id/title"
        android:layout_toLeftOf="@+id/image"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:textColor="@android:color/white"
        android:textSize="12pt" /&gt;

    &lt;TextView
        android:id="@+id/subtitle"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_below="@+id/title"
        android:textColor="#FFdedede"
        android:textSize="10pt" /&gt;

&lt;/merge&gt;</pre>


TitleWithSubtitle_クラスは先ほど書いたsetTextsメソッドで簡単に文字列を変更できます

<div>
  <div id="highlighter_654929">
    <pre class="brush: java; gutter: true">public class Main extends Activity {
 
    @ViewById
    protected TitleWithSubtitle firstTitle, secondTitle, thirdTitle;
 
    @AfterViews
    protected void init() {
         
        firstTitle.setTexts("decouple your code",
                "Hide the component logic from the code using it.");
         
        secondTitle.setTexts("write once, reuse anywhere",
                "Declare you component in multiple " +
                "places, just as easily as you " +
                "would put a single basic View.");
         
        thirdTitle.setTexts("Let&#039;s get stated!",
                "Let&#039;s see how AndroidAnnotations can make it easier!");
    }
 
}</pre>
  </div>
</div>

setTexts(String, String)のように複数の引数で渡してあけることも出来るし, setModel(Model)みたいに、モデルごと渡してあげることもできるので、コントローラー側のコードがスッキリしてよい
