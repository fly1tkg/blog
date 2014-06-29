---
title: 書いたコードはどんどんBitbucketのプライベートリポジトリにpushする
author: fly1tkg
layout: post
permalink: /2013/12/bitbucket-private/
categories:
  - Backup
---
ちょっと試しで書いたコードとかは皆さんどうしていますか？

僕はBitbucketのプライベートリポジトリにどんどん貯めるようにしています。

実験的にコードを書いているとディスクの容量を食っていくので、ちょくちょく消すのですが、いざ昔書いたコードを使いたいって時に便利だからです。

bitbucketのプライベートリポジトリなら無料だし、パブリックリポジトリとは違って、よくかんがえなくっても何かのキーとかパスワードとかを大公開してしまうことはないので気軽です。実験的なコードに限ってキーとかハードコートしてますし。

気軽にプライベートリポジトリにpushするために、クライアントツールで作れる準備をしておくのも便利です。もちろん、いちいちbitbucketのサイトを開いて作ってもそんなに手間ではないですが。

<!--more-->
# コマンドラインからBitbucketのプライベートリポジトリを作成する

Bitbucketのプライベートリポジトリを作成するのに、Rest APIを利用することができます。.bachrcや.zshrcに以下のような記述をすると便利です。

<pre class="brush: bash; gutter: true">function prepo() { curl -X POST -u BitBucketの登録メールアドレス:パスワード -H "Content-Type: application/json" https://api.bitbucket.org/2.0/repositories/Bitbucketのユーザー名/$@ -d &#039;{"scm":"git", "is_private": true}&#039; };</pre>

これで

<pre class="brush: bash; gutter: true">prepo test-repo</pre>

のコマンドでtest-repoというリポジトリが作成されます  

# .gitignoreを生成する

gitignoreはgitignore.ioというサイトでRest APIが準備されており、一般的なものを取得することができます。

まず以下を.bash_profileや.zshrcに記述します

<pre class="brush: bash; gutter: true">function gi() { curl http://gitignore.io/api/$@ ;}</pre>

例えばAndroidの.gitignoreのファイルを生成するには

<pre class="brush: bash; gutter: true">gi android &gt;&gt; .gitignore</pre>

でできます。

# プライベートリポジトリへpushする

<pre class="brush: bash; gutter: true">git remote add origin git@bitbucket.org:Bitbucketのユーザー名/リポジトリ名
git push -u origin master</pre>

これでpushできます  
