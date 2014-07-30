---
layout: post
title: "mockitoをAndroid4.3以上で動かす"
date: 2014-07-30 10:37:59 +0900
comments: true
categories: android
---

mockitoがAndroid4.3以上で動かない。以下のようなエラーメッセージが出ます。

```
java.lang.IllegalArgumentException: dexcache == null (and no default could be found; consider setting the 'dexmaker.dexcache' system property)
at com.google.dexmaker.DexMaker.generateAndLoad(DexMaker.java:359)
```

dexmakerのAndroid4.3以上のバグのようなので(issueにあって対処法も書いてあるのにメンテされてないのかなあ）以下のようにすると利用できるようになります。
<!--more-->

## CustomInstrumentationTestRunnerを作成する

app/src/androidTest/java/your/package/Custominstrumentationtestrunner.javaを作成し、以下のように記述します。

```java
public class CustomInstrumentationTestRunner extends InstrumentationTestRunner {

    @Override public void onCreate (final Bundle arguments) {
        super.onCreate(arguments);

        // temporary workaround for an incompatibility in current dexmaker (1.1) implementation and Android >= 4.3
        // cf. https://code.google.com/p/dexmaker/issues/detail?id=2 for details
        System.setProperty("dexmaker.dexcache", getTargetContext().getCacheDir().toString());
    }
}
```

## build.gradleに記述を追加する

```
android {
    defaultConfig {
        testInstrumentationRunner "your.package.CustomInstrumentationTestRunner"
    }
}
```

以上で動くようになります

## 参考

https://code.google.com/p/dexmaker/issues/detail?id=2

http://stackoverflow.com/questions/12267572/mockito-dexmaker-on-android

http://tools.android.com/tech-docs/new-build-system/user-guide#TOC-Testing
