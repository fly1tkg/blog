---
title: ResourcesのInject – AndroidAnnotations
author: fly1tkg
layout: post
permalink: /2013/10/resources-androidannotations/
categories:
  - Android
tags:
  - androidannotations
---
@XXXResのアノテーションはresフォルダのリソースをメンバ変数にInjectすることが出来ます。

Injectされるリソースはアノテーションの後ろにidを書くことで指定できます。idの指定が無い場合はメンバ変数名を元に決定されます。

またメンバ変数はprivate修飾子をつけるとうまく動作しません。

# @StringRes

指定したIDのStringリソースをメンバ変数にInjectします

<pre class="brush: java; gutter: true">@EActivity
public class MyActivity extends Activity {
 
  // R.string.helloがmyHelloStringにInjectされます
  @StringRes(R.string.hello)
  String myHelloString;
 
  // IDの指定が無い場合メンバ変数名のhelloを元に、R.string.helloがInjectされます
  @StringRes
  String hello;
 
}

</pre>

<div>
  <div id="highlighter_868710">
      <li>
        @ColorRes
      </li>
      <li>
        @AnimationRes
      </li>
      <li>
        @DimentionRes
      </li>
      <li>
        @DimentionPixelOffsetRes
      </li>
      <li>
        @DimentionPixelSizeRes
      </li>
      <li>
        @BooleanRes
      </li>
      <li>
        @ColorStateListRes
      </li>
      <li>
        @DrawableRes
      </li>
      <li>
        @IntArrayRes
      </li>
      <li>
        @LayoutRes
      </li>
      <li>
        @MovieRes
      </li>
      <li>
        @TextRes
      </li>
      <li>
        @TextArrayRes
      </li>
      <li>
        @StringArrayRes
      </li>
    </ul>
  </div>
