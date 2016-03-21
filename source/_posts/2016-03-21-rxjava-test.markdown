---
layout: post
title: "RxJavaのテストコードを書く"
date: 2016-03-21 14:45:04 +0900
comments: true
categories:
  - rxjava
  - android
  - java
  - test
---

## はじめに

最近のAndroidアプリ開発では**Clean Architecture**など、設計の議論がすすみ、テストコードのためのツール群も増えたことで、テストコードを書くコストが減ってきているように感じます。

また、もう一つの流行としてRxJavaの採用事例が増えてきたように感じます。

JavaのJUnitテストやAndroidのテストにおいて、RxJavaのテストコードをどのように書くべきか、調べたことを書いていきたいと思います。
<!-- More -->
## TestSubscriberについて

Subscriptionは非同期処理なので、ユニットテストするには同期処理が必要になります。（非同期処理のままにしておくと、Observableから値が返ってくる前にテストが終了してしまいます。）

公式のテスト方法は**TestSubscriber**クラスを利用することです。これはRxJavaライブラリに含まれています。

## 正常系のテスト

以下がコードのサンプルです。

```java
Observable<String> observable = Observable.just("foo");

// Testsubscriberを作成する
TestSubscriber<String> testSubscriber = new TestSubscriber<>();

// テストしたObservableに先程のSubscriberをsubscribeさせます
observable.subscribe(testSubscriber);

// 処理が完了するのを待ちます
testSubscriber.awaitTerminalEvent();

// 検証します
testSubscriber.assertNoErrors();
testSubscriber.assertValue("foo");
```

**TestSubscriber#awaitTerminalEvent**はObservableSubscriberの**onCompleted**または**onError**を呼ぶまでブロックしてくれます。longとTimeUnitを引数に指定することで、タイムアウトも指定することができます

## 異常系のテスト

例外の発生をテストする場合は以下のようにかけます。

```java
Observable<String> observable = Observable.error(new IllegalStateException());

TestSubscriber<String> testSubscriber = new TestSubscriber<>();
observable.subscribe(testSubscriber);

testSubscriber.awaitTerminalEvent();

testSubscriber.assertError(IllegalStateException.class);
```

**TestSubscriber#assertError**では引数に例外クラスまたはインスタンスを指定することで発生を検証できます。

## ストリームとSubscriberの状態のテスト

**Testsubscriber#assertValue**はストリームの一番最後の値しか検証できません。1つの値しか返さない場合はこれでも良いのですが、複数の値が流れてくる場合は工夫する必要があります。

以下のコードでは説明を簡単にするためにTestSubscriberを直接操作しています。

```java
TestSubscriber<Integer> testSubscriber = new TestSubscriber<>();

// onNextに値が渡っていないことを検証できます
testSubscriber.assertNoValues();

testSubscriber.onNext(1);
// onNextに渡ってきた値を検証する
assert(1 == testSubscriber.getOnNextEvents().get(0));

testSubscriber.onNext(2);
testSubscriber.onNext(3);
// getOnNextEventsはonNextで受け取った値を全て保持しています
assert(1 == testSubscriber.getOnNextEvents().get(0));
assert(2 == testSubscriber.getOnNextEvents().get(1));
assert(3 == testSubscriber.getOnNextEvents().get(2));

// onNextで受け取った値の配列を検証できます
testSubscriber.assertReceivedOnNext(Arrays.asList(1, 2, 3));

// 受け取った値の数を検証できます
testSubscriber.assertValueCount(3);

// subscriberの状態も検証できる
testSubscriber.assertNotCompleted();
testSubscriber.assertNoTerminalEvent();

testSubscriber.onCompleted();
testSubscriber.assertTerminalEvent();

testSubscriber.unsubscribe();
testSubscriber.assertUnsubscribed();
```

**TestSubscriber#getOnNextEvents**はonNextで受け取った値を全て保持しています。これを利用すると特定の場所に特定の値が流れてくることを検証できます。

また**Testsubscriber#assertReceivedOnNext**でonNextで受け取った値をリストで比較して検証できます。

**Testsubscriber#assertValueCount**はonNextで受け取った値の数を検証できます。

**TestSubscriber#assertNotCompleted**はonCompletedが呼ばれていないこと、

**Testsubscriber#assertNoTerminalEvent**はそれに加えてonErrorも呼ばれていないことを検証できます。

**Testsubscriber#assertUnsubscribed**はunsubscribeされていることを検証できます。

## おわりに

**RxJava**にはテスト用の**TestSubscriber**クラスが準備されています。

RxJavaでは**Observable#flatMap**によって個々のObservableを簡単に連結できるので、小さい単位でObservableを作成するコードを作成し、それぞれの入出力のテストコードを書いておくことで、堅牢で柔軟なモジュールが作成できるのではと思いました。

## Gist

https://gist.github.com/fly1tkg/3c29dc34682b64076058

## 参考

http://reactivex.io/RxJava/javadoc/rx/observers/TestSubscriber.html
