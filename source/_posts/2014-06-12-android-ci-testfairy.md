---
title: 無料でAndroidアプリの自動テスト+テスト配信
author: fly1tkg
layout: post
permalink: /2014/06/android-ci-testfairy/
categories:
  - Android
tags:
  - CI
---
以下のサービスを使って、Androidアプリの自動テストとテスト配信をしました。

*   [GitHub][1] (Gitのホスティングサービス)
*   [wercker][2] (自動テスト、デプロイのサービス、読み方はworker)
*   [TestFairy][3] (Androidのβテスト配信サービス)

Androidアプリの開発環境は以下のものを利用しました

*   Android Studio 0.6.0
*   Android SDK 4.4.2
*   Android Build Tool v19.1.0

<!--more-->

## 1. Androidアプリの作成とテストを書く

Android Studioを使ってアプリケーションを作成し、テストを書きます。

コードはGitHubにpushしておきます。

また、リポジトリのCoraboratorにwerckerbotを追加する必要があります。

## 2. wercker.ymlを作成する

werckerから公式でチュートリアルがあるので基本的にはそれに沿っていけばOKです、

<http://wercker.com/android/>

しかし、ハマりどころがあります。最新のAndroid Studio (0.6.0)ではBuild Toolが19.1.0以上でなければいけないのでその関係で以下の2点に気をつける必要があります。

*   ビルド結果を配置するPathがBuild Tool v19.0.3と違う
*   werckerから提供されているAndroidのテスト環境(Box)がBuild Tool v19.1.0がインストールされていない

前者はwercker.ymlのPathを調整することで解決できます。具体的には以下のようにしました。

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
        filter: sysimg-18
    - setup-android-emulator:
        target: android-18
    - script:
        name: run gradle connectedCheck
        code: |
          gradle --project-cache-dir=$WERCKER_CACHE_DIR connectedCheck
    # A step that executes `gradle build` command
    - script:
        name: run gradle
        code: |
          gradle --full-stacktrace -q --project-cache-dir=$WERCKER_CACHE_DIR build

  after-steps:
    # Use the build results
    - script:
        name: inspect build result
        code: |
          cp app/build/outputs/apk/*.apk $WERCKER_REPORT_ARTIFACTS_DIR
```

後者の問題は、werckerのテスト環境であるBoxを自分でビルドすることで解決できます。werckerのAndroidのBoxはGitHub上で公開されているので、forkしてBuild Tool v19.1.0をインストールしました。wercker.ymlの先頭に以下のように記述することで利用することができます。

```yaml
box: fly1tkg/android
```

## 3. werckerにアプリを追加する

ログインして、左のサイドバーからAdd Appをクリックするとアプリを追加できます

ガイドにそっていけば大丈夫だと思います。

## 4. TestFairyにログイン、APIキーを取得、テスターの登録

TestFairyにログインして、上タブのAccount -> Settingsをクリックすると、下の方にAPI Keyが表示されていると思います。これをメモしておきます。

また、テスターを登録します。上タブのTestersをクリックして、テスターの管理画面を開きます。ここで、テスターとグループを設定してください。あとで設定したグループに対してテストアプリを配信するように設定します。

## 5 werckerからTestFairyにデプロイする設定をする

アプリの詳細画面を開いて、Settingsタブをクリックして設定画面を開きます。

Deploy TargetsのAdd Deploy Targetをクリックして、Custom Deployを選択します。

以下のように設定しました。

*   Deploy Target Name: testfairy
*   auto deploy: チェック
*   branches: master
*   Environment Variable: TESTFAIRY\_API\_KEY: <TestFairyのAPIキー>
*   Protected: チェック

{% img /images/2014/06/wercker.jpg %}

## 6. wercker.ymlにデプロイ設定を追加する

以下のように設定しました。tester_groupsにはTestFairyで設定したテスターグループ名を入れてください。(複数の場合はカンマ区切り)

```yaml
deploy:
  steps:
    - script:
        name: upload to testfairy
        code: |
          curl https://app.testfairy.com/api/upload/ -F apk_file=@app/build/outputs/apk/app-debug-unaligned.apk -F api_key="$TESTFAIRY_API_KEY" -F comment="Deploy of commit: $WERCKER_GIT_COMMIT from branch: $WERCKER_GIT_BRANCH" -F testers_groups=&#039;me&#039;</pre>
```

以上で完了です。この設定だとmasterブランチにコードがコミットされるたびに、テストとアプリ配信が行われることになります。

## 今回のソースコード

<https://github.com/fly1tkg/hello-wercker-android>


 [1]: https://github.com/
 [2]: http://wercker.com/
 [3]: https://www.testfairy.com/
 [4]: http://192.168.33.10/wp-content/uploads/2014/06/wercker.jpg
 [5]: https://github.com/fly1tkg/hello-wercker-android
