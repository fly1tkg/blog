---
title: Androidアプリ開発者向けBitmask入門
author: fly1tkg
layout: post
permalink: /2014/03/android-bitmask/
categories:
  - Android
tags:
  - bitmask
  - sqlite
---
# ビットマスク(Bitmask)とは

ビット演算の一つでいわゆるフラグ管理に利用されています。回路の仕組み上ビットマスクなどのビット演算はとても高速に処理できます。

ビットマスクはたとえばLinuxといったOSのファイルのアクセス権の管理に利用されています。ファイルの実行、読み込み、書き込みの権限は3bitで表現されます。(括弧内は10進数) 1桁目は実行権限、2桁目は読み込み権限、3桁目は書き込み権限を表しています。

*   000 (0) アクセス権がない
*   001 (1) 実行権限
*   010 (2) 読み込み権限
*   100 (4) 書き込み権限
*   011 (3) 読み込み+実行
*   111 (7) 書き込み+読み込み+実行

<!--more-->

## フラグをONにする

フラグをONにするときはビットマスクとの論理和(OR)で行えます。例えば読み込み権限のフラグをたてるには以下のようにします。

*   000 OR 010 = 010
*   101 OR 010 = 111

## フラグをOFFにする

フラグをOFFにするときはビットマスクを反転したものとの論理積(AND)で行えます。例えば読み込み権限のフラグを折るには、読み込み権限のビットマスクの反転したものは101なので、以下のようにします。

*   010 AND 101 = 000
*   111 AND 101 = 101
*   011 AND 101 = 001

## フラグの確認

フラグの確認は論理積(AND)で確認できます。論理積の結果は２つのビットの共通部分です。例えば読み込み権限を確認する時は以下のようになります。

*   011 AND 010 = 010 読み込み権限あり
*   010 AND 010 = 010 読み込み権限あり
*   100 AND 010 = 000 読み込み権限なし

このように読み込み権限のbitmaskとの論理積を計算すると読み込み権限のビットがONかOFFかがわかります。実際のコードでは、bitmaskとの論理積の結果が0か0でないかの判定でフラグのON, OFFを確認できます

# アプリでの利用

アプリで利用する際はSQLiteと一緒に使うとよいです。多くのフラグ管理をしなければいけないときでもテーブルをシンプルに設計することができます。またSQLiteはビット演算をサポートしているので、特定のフラグを持つカラムを高速に検索することもできます。

例えばタスク管理アプリをつくるとします。タイトルと複数選択できる曜日がSQLiteに保存できる簡単なものです。サンプルなので多少ハードコーディングしてあります。

<pre class="brush: java; gutter: true">public class Task {
    public interface DAY_OF_WEEK {
        int MON = 1;
        int TUE = 2;
        int WED = 4;
        int THU = 8;
        int FRI = 16;
        int SAT = 32;
        int SUN = 64;
    }

    private String title;
    private int day;

    public String getTitle() {
        return title;
    }

    public void setTitle(String title) {
        this.title = title;
    }

    public int getDay() {
        return day;
    }

    public void setDay(int day) {
        this.day |= day;
    }

    public void removeDay(int day) {
        this.day &= ~day;
    }

}</pre>

Javaでのビット演算では&で論理積、|で論理和、~で否定になります。例えば月曜と火曜に犬の散歩をするタスクの場合は以下のようになります。

<pre class="brush: java; gutter: true">Task task = new Task();
task.setTitle("犬の散歩");
task.setDay(DAY_OF_WEEK.MON);
task.setDay(DAY_OF_WEEK.TUE);</pre>

月曜を削除するときは以下の通り

<pre class="brush: java; gutter: true">task.removeDay(DAY_OF_WEEK.MON);</pre>

SQLiteにinsert

<pre class="brush: actionscript3; gutter: true">ContentValues cv = new ContentValues();
cv.put("title", task.getTitle());
cv.put("day", task.getDay());
db.insert("task", null, cv);</pre>

火曜日のタスクの検索は以下の通り

<pre class="brush: java; gutter: true">Cursor cursor = db.query("task", null, "day & 2 != 0", null, null, null, null);</pre>

以上のようにtaskのdayというカラムに月曜から日曜までの7種類のフラグの保存と検索ができるようになります。

## OrmLiteでの利用

OrmLiteはraw SQLもかけるので以下のように検索できます。

<pre class="brush: actionscript3; gutter: true">Where&lt;Task, Long&gt; where = q.where().raw("day & " + DAY_OF_WEEK.TUE + " != 0");
Log.d("SQL","sql: " + q.prepareStatementString());
List&lt;Task&gt; tasks = where.query();</pre>

