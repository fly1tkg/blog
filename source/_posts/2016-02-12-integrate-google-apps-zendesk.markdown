---
layout: post
title: ZendeskでGoogle Apps管理ドメインのメールアドレスを送受信する
date: 2016-02-12 21:25:24 +0900
comments: true
categories:
  - google-apps
  - zendesk
---

この記事はGoogle Appsで管理しているメールアドレスをZendeskで使えるようにする設定のメモ書きです。

## Zendeskでメールを受け取る

### 管理コンソールから アプリ > Google Apps > Gmailを選択します

{% img /images/2016/02/integrate-google-apps-zendesk/0.png %}
<!--more-->
{% img /images/2016/02/integrate-google-apps-zendesk/1.png %}
{% img /images/2016/02/integrate-google-apps-zendesk/2.png %}

### デフォルトの転送を開き、設定を追加をクリックする

{% img /images/2016/02/integrate-google-apps-zendesk/3.png %}

### 宛先にZendeskで使用したいメールアドレスを入力する

{% img /images/2016/02/integrate-google-apps-zendesk/4.png %}

### 受信者を追加し、受信者のメールアドレスにZendeskのメールアドレスを追加する

{% img /images/2016/02/integrate-google-apps-zendesk/5.png %}

### この操作は認識されないアドレスと認識されたアドレスに対して実行しますを選択して保存する

{% img /images/2016/02/integrate-google-apps-zendesk/5.png %}

### Zendeskにメールアドレスを追加する

右下の歯車マークをクリックして設定を開き、チャネルのメールをクリックするとメールの設定画面が開きます。

サポートアドレスのアドレスを追加で使用したいメールアドレスを追加します

{% img /images/2016/02/integrate-google-apps-zendesk/7.png %}

以上でZendeskの受信設定は終わりです。

## Zendeskからメールを送信する

このままでもZendeskのメールは送信できますが、SPFレコードをドメインに追加することで迷惑メールフォルダに入りにくくなります。

ドメインに以下のレコードを追加します。

    v=spf1 include:mail.zendesk.com ?all

参考: https://support.zendesk.com/hc/ja/articles/203683886
