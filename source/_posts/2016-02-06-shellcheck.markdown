---
layout: post
title: "ShellCheckでshellscriptの静的チェックをする"
date: 2016-02-06 16:06:05 +0900
comments: true
categories:
  - shellscript
---

最近Rubocopでrubyコードの静的チェックをCIでしているのですが、シェルスクリプトについても同様にできないかと探してみると、ShellCheckというものを見つけました。

## ShellCheckとは

シェルスクリプトを静的チェックして、より良い書き方を提案してくれます。

https://github.com/koalaman/shellcheck

> {%img https://raw.githubusercontent.com/koalaman/shellcheck/master/doc/terminal.png %}

> reference: https://github.com/koalaman/shellcheck

<!--more-->

Webからも簡単に試せます。 http://www.shellcheck.net/

コマンドラインのインストールは各種パッケージマネージャーが対応しているので簡単にできます。

https://github.com/koalaman/shellcheck#installing

## ShellCheckをWerckerで使う

gitにpushしたコードをCIサービスでチェックしたいので、Werckerで試してみます。

既にShellCheckを実行するStepが公開されているので、**wercker.yml**に組み込むだけです。

https://github.com/wercker/step-shellcheck

**files**の部分にチェックしたいコードを指定します。
指定しない場合は**\*.sh**が入ります

```yml
build:
  steps:
    - shellcheck:
        files: run.sh
```

## おわりに

僕はあまりシェルスクリプトを本格的に書いたことがありませんが、こういった静的チェックが先生になって添削してくれることでよりシェルスクリプトを理解できるようになりました。

シェルスクリプト以外にもCIで静的チェックをするのはとても良いと思いました。
