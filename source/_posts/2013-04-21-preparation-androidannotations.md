---
title: 事前準備 AndroidAnnotations
author: fly1tkg
layout: post
permalink: /2013/04/preparation-androidannotations/
categories:
  - Android
tags:
  - androidannotations
---
AndroidAnnotations**(以下AA)**のライブラリをAndroidのプロジェクトに適用する方法を説明します。

公式のWikiにはMavenやAnt、Gradleといったビルドツールを用いた方法も書かれていますが、ここでは普通のビルドツールを使わない方法を説明します。  

## 1. ダウンロード

以下のリンクから最新版をダウンロード出来ます

<https://github.com/excilys/androidannotations/wiki/Download>

## 2. Eclipseに設定する

事前にAndroidプロジェクトのJavaの設定がjava 1.6になっていることを確認してください。

確認方法はEclipseのPackage Exploreで確認したいAndroidプロジェクトで右クリックしてPropertiesをクリック、Java compilerの項目でCompiler compliance levelの部分が1.6になっていればOKです。

先ほどのダウンロードリンクからダウンロードしたファイルのうち**androidannotations-X.X.X-api.jar**をlibsフォルダに置きます

また**androidannotations-X.X.X.jar**はcompile-libsなどlibsフォルダとは違うフォルダを作成して、そのフォルダに置きます

Package Exploreで確認したいAndroidプロジェクトで右クリックしてPropertiesをクリックし、Propertiesを表示します。

Java Compiler -> Annotation Processingを選択しEnable annotation processingにチェックを入れます

Java Compiler -> Annotation Processing -> Factory Pathで**Add jars**ボタンをクリックし、**androidannotations-X.X.X.jar**のパスをを入力します

ADT16以前であればJava Build Path -> Librariesで**androidannotations-X.X.X-api.jar**のパスを追加してください。ADTが最新であれば問題ありません。

# 準備完了

これで全ての準備が完了です。AAが使えるようになります。  

