---
title: ViewのInjectについて – AndroidAnnotations
author: fly1tkg
layout: post
permalink: /2013/10/view-inject/
categories:
  - Android
tags:
  - androidannotations
---
# @ViewById

Viewクラスが型のメンバ変数に対して@ViewByIdをつけるとレイアウトからfindViewByIdのメソッドが代入された状態になります。

<!--more-->
<pre class="brush: java; gutter: true">@EActivity
public class MyActivity extends Activity {

  /*
   * findViewById(R.id.myEditText)の
   * 結果が自動的に注入されます
   * R.id.&lt;メンバ変数の名前&gt;のルールです
   */
  @ViewById
  EditText myEditText;

  /*
   * findViewById(R.id.myTextView)の
   * 結果が自動的に注入されます
   * このようにカッコ内でIDを指定することも出来ます
   */
  @ViewById(R.id.myTextView)
  TextView textView;
}</pre>

<div>
  <div id="highlighter_866783">
    <div>
      <strong><span style="font-size: 2em; color: #333333; line-height: 1.5em;">@AfterViews</span></strong>
    </div>
    
    <p>
      @AfterViewsがついたメソッドは@ViewByIdでViewが注入された直後に呼ばれます。Viewの初期化などをここで行います。
    </p>
    
    <p>
      (Viewが注入される前に初期化などの処理をしようとすると、NullPoになります。これを使うと安全です)
    </p>
    
    <div>
      <div id="highlighter_988390">
        <pre class="brush: java; gutter: true">@EActivity(R.layout.main)
public class MyActivity extends Activity {

    @ViewById
    TextView myTextView;

    @AfterViews
    void updateTextWithDate() {
        myTextView.setText("Date: " + new Date());
    }
}</pre>
      </div>
    </div>
    
  </div>
</div>
