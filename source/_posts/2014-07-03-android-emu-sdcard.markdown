---
layout: post
title: "werckerのAndroidエミュレータでSDカードを使う"
date: 2014-07-03 21:30:33 +0900
comments: true
categories:
  - Android
  - wercker
---

werckerには公式のStepでAndroidのエミュレータをセットアップするすることができます

<https://app.wercker.com/#applications/5241920d4f6b6b786f000586/tab/details>

しかし、このStepだとSDカードがマウントされていないため、SDカードを利用するユニットテストができません。
<!--more-->
SDカードをマウントしたエミュレータは以下の様なコードで作成できます。

```bash
android create avd --force -n wercker -t android-19 --abi armeabi-v7a -c 256M
```

そこでSDカードをマウントしたエミュレータを起動するStepを公式からForkして作りました。

<https://app.wercker.com/#applications/539e93962e6afc0632000d9e/tab/details>

そのうちSDカードの容量を指定できるようにオプションを追加したりしようと思います。
