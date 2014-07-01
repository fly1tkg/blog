---
layout: post
title: werckerでAndroidエミュレータが動かなくなったのでメモ
date: 2014-07-01 10:22:40 +0900
comments: true
categories:
  - Android
  - wercker
---

昨日からこんなメッセージでwercker上のAndroidエミュレータが起動しなくなっていました。

```
Error: Unable to find a 'userdata.img' file for ABI armeabi-v7a to copy into the AVD folder.
```

<!--more-->
古いplatform-tools向けにAndroidエミュレータのイメージファイルを配布しなくなっているからみたいなので、platform-toolsを先に更新するようにと、filterの名前を変更して、wercker.ymlを更新しました。

```yaml
build:
  # The steps that will be executed on build
  steps:
    - script:
        name: show base information
        code: |
          gradle -v
          echo $ANDROID_HOME
          echo $ANDROID_SDK_VERSION
          echo $ANDROID_BUILD_TOOLS
          echo $ANDROID_UPDATE_FILTER
    - android-sdk-update:
        filter: tools,platform-tools
    - android-sdk-update:
        filter: sys-img-armeabi-v7a-android-18
```

またはboxであらかじめシステムイメージ入りのものを準備しておくのがいいと思いました。

とりあえずメモ書きです。
