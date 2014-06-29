---
title: 基本的な使い方 AndroidAnnotations
author: fly1tkg
layout: post
permalink: /2013/04/helloworld-androidannotations/
categories:
  - Android
tags:
  - androidannotations
---
AndroidAnnotations(以下AA)の基本的な使い方を説明します。

AAの設定がされていない場合は以下のリンクを参考に設定してください。

[事前準備 AndroidAnnotations | b.fly1tkg.com][1]

[公式Wiki Get Started!][2]

# @EActivityをつける

AAをActivityで使用するにはクラスに@EActivityをつける必要があります。

<div>
  <div id="highlighter_482273">
    <pre class="brush: java; gutter: true">@EActivity
public class MyActivity extends Activity {</pre>
  </div>
</div>

そしてAndroidManifest.xmlへの**Activityの登録は_のsuffixが必要になります。**

<div>
  <div id="highlighter_284224">
    <pre class="brush: xml; gutter: true">&lt;activity android:name=".MyActivity_" /&gt;</pre>
  </div>
</div>

これは上記の例で言うとMyActivityを継承したMyActivity\_というクラスがAAによって生成され、MyActivity\_で色々と便利なコードが生成されるからです。

# AAでHelloWorld

次のようなコードでAAを使ったHello Worldを作成することが出来ます。

詳しくはEActivityのエントリーで説明します。

**MyActivity.java**

<div>
  <div id="highlighter_396985">
    <pre class="brush: java; gutter: true">@EActivity(R.layout.main)
public class MyActivity extends Activity {

    @ViewById(R.id.myInput)
    EditText myInput;

    @ViewById(R.id.myTextView)
    TextView textView;

    @Click
    void myButton() {
         String name = myInput.getText().toString();
         textView.setText("Hello "+name);
    }
}</pre>
  </div>
</div>

**main.xml**

<div>
  <div id="highlighter_83244">
    <pre class="brush: xml; gutter: true">&lt;?xml version="1.0" encoding="utf-8"?&gt;
&lt;LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    &gt;
    &lt;EditText 
        android:id="@+id/myInput"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        /&gt;
    &lt;Button 
        android:id="@+id/myButton"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="Click me!"
        /&gt;       
    &lt;TextView 
        android:id="@+id/myTextView"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        /&gt;   
&lt;/LinearLayout&gt;</pre>
  </div>
</div>

まず@EActivity(R.layout.main)でres/layout/main.xmlのレイアウトを適用出来ます。

また@ViewById(R.id.myTextView)でmain.xmlのidがmyTextViewのTextViewのインスタンスが自動的に代入されます。これでfindViewById(R.id.myTextView)といったコードを書かなくてもよいです。

@Clickはメソッド名と同じIDのViewがクリックされたときの処理を指定しています。ここではmyButtonがクリックされた時にmyTextViewにテキストを表示することをしています。@Click(R.id.myButton)のようにIDを指定することも出来ます。  

 [1]: http://b.fly1tkg.com/2013/04/preparation-androidannotations/
 [2]: https://github.com/excilys/androidannotations/wiki/Configuration
