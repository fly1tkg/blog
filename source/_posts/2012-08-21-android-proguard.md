---
title: AndroidでProguardによる難読化
author: fly1tkg
layout: post
permalink: /2012/08/android-proguard/
categories:
  - Android
tags:
  - proguard
---
AndroidアプリはJavaで書かれていますが、**Javaはデコンパイルが容易です。**そのため簡単にソースコードが丸見えになってしまいます。そのためにソースコード難読化がです。ProguardはJavaのソースコードを難読化するツールで、最新のADTにはAndroidで簡単に使えるようになっています。以下に現在最新の**ADT20**で行った方法をメモしておきます。  

# 使い方

project.propertiesのproguard.configの行をアンコメント(先頭の#を消す)します。  
その後 File > Export > Export Android Applicationでリリースビルドをします。

<div>
  <div id="highlighter_590513">
    <pre class="brush: text; gutter: true"># To enable ProGuard to shrink and obfuscate your code, uncomment this (available properties: sdk.dir, user.home):
proguard.config=${sdk.dir}/tools/proguard/proguard-android.txt:proguard-project.txt</pre>
  </div>
</div>

たったこれだけでソースコードの難読化ができます。

難読化の結果はprojectフォルダの中に生成されたproguardフォルダの中の4つのテキストファイルに出力されます。

*   dump.txt -> apkファイルの構成
*   mapping.txt -> 難読化されたクラスやメソッドなどの履歴
*   seeds.txt -> 難読化されていないクラスなどのリスト
*   usage.txt -> 削除されたコードのリスト

mapping.txtを開いてみるとクラス名やメソッド名がどのように変更されたかを見る事ができます。

<div>
  <div id="highlighter_750225">
    <div>
      <a href="http://b.fly1tkg.com/2012/08/android-proguard/#">?</a>
    </div>
    
    <table border="0" cellspacing="0" cellpadding="0">
      <tr>
        <td>
          <div>
            1
          </div>
          
          <div>
            2
          </div>
          
          <div>
            3
          </div>
          
          <div>
            4
          </div>
          
          <div>
            5
          </div>
          
          <div>
            6
          </div>
          
          <div>
            7
          </div>
          
          <div>
            8
          </div>
          
          <div>
            9
          </div>
          
          <div>
            10
          </div>
          
          <div>
            11
          </div>
          
          <div>
            12
          </div>
          
          <div>
            13
          </div>
          
          <div>
            14
          </div>
          
          <div>
            15
          </div>
          
          <div>
            16
          </div>
          
          <div>
            17
          </div>
          
          <div>
            18
          </div>
          
          <div>
            19
          </div>
          
          <div>
            20
          </div>
          
          <div>
            21
          </div>
          
          <div>
            22
          </div>
        </td>
        
        <td>
          <div>
            <div>
              <code>android.support.v4.app.ActivityCompatHoneycomb -&gt; android.support.v4.app.a:</code>
            </div>
            
            <div>
              <code>void invalidateOptionsMenu(android.app.Activity) -&gt; a</code>
            </div>
            
            <div>
              <code>android.support.v4.app.BackStackRecord -&gt; android.support.v4.app.b:</code>
            </div>
            
            <div>
              <code>android.support.v4.app.FragmentManagerImpl mManager -&gt; a</code>
            </div>
            
            <div>
              <code>android.support.v4.app.BackStackRecord$Op mHead -&gt; b</code>
            </div>
            
            <div>
              <code>android.support.v4.app.BackStackRecord$Op mTail -&gt; c</code>
            </div>
            
            <div>
              <code>int mNumOp -&gt; d</code>
            </div>
            
            <div>
              <code>int mEnterAnim -&gt; e</code>
            </div>
            
            <div>
              <code>int mExitAnim -&gt; f</code>
            </div>
            
            <div>
              <code>int mPopEnterAnim -&gt; g</code>
            </div>
            
            <div>
              <code>int mPopExitAnim -&gt; h</code>
            </div>
            
            <div>
              <code>int mTransition -&gt; i</code>
            </div>
            
            <div>
              <code>int mTransitionStyle -&gt; j</code>
            </div>
            
            <div>
              <code>boolean mAddToBackStack -&gt; k</code>
            </div>
            
            <div>
              <code>boolean mAllowAddToBackStack -&gt; l</code>
            </div>
            
            <div>
              <code>java.lang.String mName -&gt; m</code>
            </div>
            
            <div>
              <code>boolean mCommitted -&gt; n</code>
            </div>
            
            <div>
              <code>int mIndex -&gt; o</code>
            </div>
            
            <div>
              <code>int mBreadCrumbTitleRes -&gt; p</code>
            </div>
            
            <div>
              <code>java.lang.CharSequence mBreadCrumbTitleText -&gt; q</code>
            </div>
            
            <div>
              <code>int mBreadCrumbShortTitleRes -&gt; r</code>
            </div>
            
            <div>
              <code>java.lang.CharSequence mBreadCrumbShortTitleText -&gt; s</code>
            </div>
          </div>
        </td>
      </tr>
    </table>
  </div>
</div>

このようにクラス名やメソッド名が1~2文字程度の英字に置き換わります。

# Proguardの仕組み

Proguardには**コードの短縮、最適化、難読化、事前検証**の4つのステップがあります。

コードの短縮や最適化のステップで、使用していないクラスやメソッドを削除したり変数の折りたたみを行ったりする事で、ソースコードの圧縮を行います。

難読化のステップではクラス名やメソッド名などを1~2文字の英数字で置き換え、難読化をさせます。

事前検証のステップではProguardでの処理で問題が無いかチェックします。

proguardの設定ファイルは２カ所に分かれておいてあって、１つは<android sdkのパス>/tools/proguard/proguard-android.txt  
こちらはapkにするときのproguard全体の設定が書かれています。

もう一つはprojectフォルダの中のproguard-project.txt　こちらにprojectごとの設定を書きます

# 注意点

ProguardはピュアなJavaのコードに対してはきちんと動くようですが、Androidの様にxmlなどの静的なファイルを参照したり、いろいろしてると、誤作動で使用しているクラスやメソッドが消されてしまうことがあります。  
基本的にはusage.txtと見比べながら、必要なコードが消されていないかや、mapping.txtを見てリネームしてはいけないものがリネームされていないかをチェックする必要があります。

## ライブラリとしてjarを使用している場合

-libraryjar　<jarのパス>　のオプションをproguard-project.txtに追加します

例

<div>
  <div id="highlighter_510546">
    <pre class="brush: text; gutter: true">-libraryjars ./libs/actionbarsherlock-plugin-maps-4.1.0.jar
-libraryjars ./libs/android-async-http-1.4.0.jar
-libraryjars ./libs/jsonpullparser-core-1.4.jar
-libraryjars ./libs/twitter4j-core-android-2.2.6.jar</pre>
  </div>
</div>

また一般的なオープンソースのライブラリであれば、StackOverFlowとかで推奨の設定が書かれていたりします。  
例えばtwitter4jであればこれ　[http://stackoverflow.com/questions/7721397/how-to-make-proguard-ignore-external-libraries][1]

<div>
  <div id="highlighter_458258">
    <pre class="brush: text; gutter: true">-keep class javax.** { *; }
-keep class org.** { *; }
-keep class twitter4j.** { *; }</pre>
  </div>
</div>

-keep　オプションは指定したクラスを難読化しないというオプションです

## ライブラリプロジェクトを使用している場合

library projectを参照している場合は<library projectのパッケージ名>.R.classがよく消されます。  
特にlibrary projectの中のdrawableなどのresourceを参照している場合です。

-keep class <パッケージ名>.R { *; }

などで消されないようにしましょう

## Android NDKでJNIのメソッドを読んでいる場合

ラッパーのクラスを-keepメソッドで難読化しないようにしましょう。  
nativeを読んでいるメソッドはProguard側で使用されていないメソッドとして消される場合があります

## 最後に

Proguardを使用したからといって、定数の中身まで難読化はされません。パスワードなどを平文でapkに含めないようにしましょう。

 [1]: http://stackoverflow.com/questions/7721397/how-to-make-proguard-ignore-external-libraries "http://stackoverflow.com/questions/7721397/how-to-make-proguard-ignore-external-libraries"
