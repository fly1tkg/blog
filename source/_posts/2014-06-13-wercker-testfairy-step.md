---
title: werckerのTestFairy stepを作った
author: fly1tkg
layout: post
permalink: /2014/06/wercker-testfairy-step/
categories:
  - Android
tags:
  - CI
---
werckerのTestFairy stepを作りました。これを利用することでTestFairyにAndroidのapkをアップロードできます。

<https://app.wercker.com/#applications/539a59212e6afc0632000196/tab/details>

ソースコード

<https://github.com/fly1tkg/wercker-step-testfairy>  
<!--more-->

## 使い方

### 1. wercker.ymlのdeploy stepにfly1tkg/testfairyを追加します。

必須のオプションは2つです。

*   api_key: TestFairyのAPI Key、後で設定する環境変数にしておくのがいいと思います。
*   apk_file: apkのパスを入れてください

API Keyは<span style="color: #636664;"> </span><a style="color: #368cb9;" href="https://app.testfairy.com/settings">https://app.testfairy.com/settings</a> から見ることができます。

例

<pre class="brush: actionscript3; gutter: true">deploy:
  steps:
    - fly1tkg/testfairy:
        api_key: $TESTFAIRY_API_KEY
        apk_file: app/build/outputs/apk/app-debug-unaligned.apk</pre>

### 2. deployの設定をする

Deploy TargetsのAdd Deploy Targetをクリックして、Custom Deployを選択します。

ここに環境変数でTestFairyのAPI Keyを入れておきます。

例

*   Deploy Target Name: testfairy
*   Environment Variable: TESTFAIRY\_API\_KEY: <TestFairyのAPIキー>

これで完了です。Auto deployの設定をしている場合は自動で、していない場合はdeploy to のボタンからdeployすると、ビルド結果のapkがTestFairyにアップロードされます。

## オプションについて

*   proguard_file: proguardのマッピングファイルのパス
*   testers_groups: カンマ区切りで新しいビルドを通知するテスターグループを指定できます
*   metrics: カンマ区切りで記録するメトリクスを指定できます
*   max_duration: 最大のセッションの長さを指定できます。デフォルトは&#8221;10m&#8221;(10分)
*   video: ビデオをレコードするかどうかを指定できます。 デフォルトは&#8221;on&#8221;
*   video_quality: ビデオの画質設定です。&#8221;high&#8221;, &#8220;medium&#8221;, &#8220;low&#8221;から選べます。デフォルトは&#8221;high&#8221;
*   video_rate: ビデオの1秒辺り何フレーム記録するか指定できます。デフォルトは&#8221;1.o&#8221;
*   icon_watermark: アプリのアイコンにウォータマークをつけるかどうか。デフォルトは&#8221;off&#8221;
*   comment: メールに載せるリリースノートを指定できます

利用できるメトリクス

*   cpu: userとkernelのCPU利用率
*   memory: プロセスのprivateとsharedのメモリ分析
*   network: プロセスのネットワーク利用率
*   phone-signal: 電波の強さ
*   logcat: プロセスのlogcatの出力(READ_LOGSのpermissionが追加される)
*   gps: アプリが利用してる場合のみ、位置情報
*   battery: バッテリーの状態(BATTERY_STASのpermissionが追加される)
*   mic: アプリで利用している場合のみ、マイクの音声

例

```yaml
deploy:
  steps:
    - fly1tkg/testfairy:
        api_key: $TESTFAIRY_API_KEY
        apk_file: app/build/outputs/apk/app-debug-unaligned.apk
        testers_groups: tester
        icon_watermark: on
```
