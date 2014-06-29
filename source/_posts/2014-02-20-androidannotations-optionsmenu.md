---
title: 'Options menuをハンドリングする &#8211; AndroidAnnotations -'
author: fly1tkg
layout: post
permalink: /2014/02/androidannotations-optionsmenu/
categories:
  - Android
tags:
  - androidannotations
---
AndroidAnnotations(以下AA)を使うとシンプルにオプションメニューを実装することができます。

*   @OptionMenuでメニューのリソースを指定します。
*   @OptionItemでイベントを受け取るメソッドを指定します。

<!--more-->
サンプルコード

<pre class="brush: java; gutter: true">@EActivity
@OptionsMenu(R.menu.my_menu)　// res/menu/my_menu.xmlを指定します
public class MyActivity extends Activity {

    @OptionMenuItem // MenuItemのインスタンスを取得したい場合はこう書きます。利用しない場合は不必要です
    MenuItem menuSearch;

    @OptionsItem(R.id.menuShare) 
        void myMethod() {
          // R.id.menuShareのメニューが選択されたときの処理を書きます。
        }

    @OptionsItem
    void homeSelected() {
　　　　　　// リソースIDが指定されていない場合はメソッド名から判定されます(Selectedは無視されます)
          // R.id.homeのメニューが選択された時に実行されます
    }

    @OptionsItem
    boolean menuSearch() {
          menuSearch.setVisible(false);
          // R.id.menuSearchが選択された時に実行されます
          // 返り値はvoidとbooleanを選択できます(falseの場合はmenuの処理を継続できます)

          return true;
    }

    @OptionsItem({ R.id.menu_search, R.id.menu_delete })
    void multipleMenuItems() {
      // 複数のリソースIDを指定できます
    }

    @OptionsItem
    void menu_add(MenuItem item) {
      // パラメータにMenuItemを指定し利用することもできます
      // itemにはR.id.menu_addのMenuItemが入ります
    }
}</pre>

フラグメントでも利用することができます

<pre class="brush: java; gutter: true">@EFragment
@OptionsMenu(R.menu.my_fragment_menu)
public class MyFragment extends Fragment {

    @OptionsItem
    void menuRefreshSelected() {
    }

}</pre>

参考

*   <https://github.com/excilys/androidannotations/wiki/Handling%20Options%20Menu>

