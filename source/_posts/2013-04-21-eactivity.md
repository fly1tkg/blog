---
title: 'EActivityについて &#8211; AndroidAnnotations'
author: fly1tkg
layout: post
permalink: /2013/04/eactivity/
categories:
  - Android
tags:
  - androidannotations
---
@EActivityのアノテーションをつけることでそのActivity内でAndroidAnnotations(**以下AA**)が使えるようになります。以下のようにActivityに@EActivityをつけます。

<pre class="brush: java; gutter: true">@EActivity(R.layout.main)
public class MyActivity extends Activity {

}</pre>

ListActivityなどレイアウトを指定しない場合はそのままつければOKです。

<div>
  <div id="highlighter_711435">
    <pre class="brush: java; gutter: true">@EActivity
public class MyListActivity extends ListActivity {

}</pre>
  </div>
</div>

@EActivityをつけると**元のクラスを継承し、_のsuffixがついたコードが生成されます。**そのためAndroidManifest.xmlに登録する際は最後に_をつけます。

<div>
  <div id="highlighter_604962">
    <pre class="brush: java; gutter: true">&lt;activity android:name="MyActivity_"/&gt;</pre>
    
    <p>
      <!-- blog_widget -->
      
      <ins class="adsbygoogle" style="display: inline-block; width: 300px; height: 250px;" data-ad-client="ca-pub-6228702140385127" data-ad-slot="3243171800"></ins>
    </p>
    
    <h1>
      IntentBuilder
    </h1>
    
    <p>
      EActivityではAAが使える以外に特徴的なこととして、<strong>IntentBuider</strong>というものが自動で生成されるということです。
    </p>
    
    <p>
      例えば通常ActivityにIntentする時は以下のようなコードを書くと思います。
    </p>
    
    <div>
      <div id="highlighter_112382">
        <pre class="brush: java; gutter: true">Intent i = new Intent(this, MyActivity.class);
startActivity(i);</pre>
      </div>
    </div>
    
    <p>
      このIntentBuilderを使うと次のような書き方が出来ます。
    </p>
    
    <div>
      <div id="highlighter_730509">
        <pre class="brush: java; gutter: true">new MyActivity_.IntentBuilder_(this).start();</pre>
      </div>
    </div>
    
    <p>
      IntentBuilderよってIntentでの値の受け渡しは、@Extraがついたデフォルトのメンバ変数に対してsetterが準備されます。
    </p>
    
    <p>
      以下のようなMainActivityに遷移使用とする時
    </p>
    
    <div>
      <div id="highlighter_108005">
        <pre class="brush: java; gutter: true">@EActivity(R.layout.activity_main)
public class MainActivity extends Activity {
    @Extra String name;
    @Extra int id;
}</pre>
      </div>
    </div>
    
    <p>
      Intentするためのコードは以下のようになります。
    </p>
    
    <div>
      <div id="highlighter_882471">
        <pre class="brush: java; gutter: true">new MainActivity_.IntentBuilder_(this)
        .name("answer")
    .id(42)
    .start();</pre>
      </div>
    </div>
    
    <p>
      IntentのFlagもセットすることも出来ます
    </p>
    
    <div>
      <div id="highlighter_802953">
        <pre class="brush: java; gutter: true">new MainActivity_.IntentBuilder_(this)
        .flags(Intent.FLAG_ACTIVITY_CLEAR_TOP)
        .start();</pre>
      </div>
    </div>
  </div>
</div>
