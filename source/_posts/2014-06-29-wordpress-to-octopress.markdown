--- 
layout: post
title: "WordpressからGitHub pages+Octopressに移行した"
date: 2014-06-29 20:34:45 +0900
categories:
  - Octopress
tags:
  - wercker
  - Octopress
---

今日WordpressからOctopressに移行しました。

Octopressとは、JekyllというRuby製の静的サイト生成ツールを使って静的なブログを生成するフレームワークです。

<http://octopress.org/docs/>

移行は以下の手順で行いました。

* Octopressの導入
* Wordpressの記事をJekyll向けにexport
* GitHub pagesにDeploy
* 独自ドメインを設定する
* werckerを使ってDeployの自動化

<!--more-->

## Octopressの導入

Octopressの導入については以下のリンクで説明されています。

<http://octopress.org/docs/setup/>

OctopressはRuby1.9.3以上がインストールされている必要があります。rbenvやRVMでインストールをしておきましょう。

以下のようにoctopressをcloneします

```bash
git clone git://github.com/imathis/octopress.git octopress
cd octopress
```

次にbundlerのインストールと必要Gemのインストール

```bash
gem install bundler
bundle install
```

最後にデフォルトのテーマのインストールです

```bash
rake install
```

_config.ymlにブログの情報を書きます。

```yaml
url: http://b.fly1tkg.com
title: fly1tkg blog
subtitle: いろいろ
author: Shoichi Takagi
simple_search: https://www.google.com/search
description:
```

## Wordpressの記事をJekyll向けにexport

Wordpress to Jekyll exporterというWordpressのプラグインを利用しました。

<https://github.com/benbalter/wordpress-to-jekyll-exporter>

### 使い方

* このリポジトリの中身を/wp-content/plugins/フォルダに入れる
* php.iniに`extention=zip.so`のコメントアウトが外れていることを確認
* WordpressのダッシュボードからPluginを有効化
* ToolsのメニューからExport to Jekyllを選択する

ダウンロードしたExportファイルを_config.yml以外を先程のOctopressのフォルダに入れればOKです

基本的にはちゃんと変換されますが、Wordpressのプラグインを利用した記述は時々おかしくなっているので修正します。
僕は、Syntax highlighterの部分がおかしくなっていました。

またiframeを利用したyoutubeの貼り付けなどがおかしくなっていたので手動で修正しました。


## GitHub pagesにDeploy

Github pagesの作り方については以下のリンクに説明があります。

<https://pages.github.com/>

ユーザネーム.github.ioでリポジトリを作り、そこにhtmlをpushするとユーザネーム.github.ioのアドレスで配信されるようになります。

Deploy先を以下のコマンドで設定できます。

```bash
rake setup_github_pages
```

途中でリポジトリのURLを聞かれるので、入力します。
例) git@github.com:fly1tkg/fly1tkg.github.io.git

そうすると以下のコマンドでdeployできるようになります。

```bash
rake generate
rake deploy
```

## 独自ドメインを設定する

独自ドメインの設定については以下のリンクに説明があります。

<https://help.github.com/articles/setting-up-a-custom-domain-with-github-pages>

CNAMEというファイルを作成して、中身は設定したい独自ドメインのみを記述します。
このブログだったらb.fly1tkg.comをCNAMEに書きます。

そしてそのCNAMEファイルをGitに追加してGitHubにpushします。

次にDNSプロバイダで独自ドメインのDNS設定をします。

CNAMEレコードでユーザネーム.github.ioに転送するように設定します。詳しくは各DNSプロバイダのドキュメントを参照すると良いと思います。

僕はRoute53を利用しているので、さくっとCNAMEレコードを追加しました。

## werckerを使ってDeployの自動化

Octopressのコードはgithub.ioのリポジトリとは別で管理するので、記事を書くたびに両方にデプロイするのは面倒なので、masterにコミットした内容を自動でgithub.ioにデプロイするようにします。

デプロイの自動化はCircle CIやwerckerを利用するといいと思います。今回はwerckerを利用しました。

<http://wercker.com>

GitHub pagesへのDeployについては以下のリンクが参考になります。

<http://blog.wercker.com/2013/07/25/Using-wercker-to-publish-to-GitHub-pages.html>

werckerでアプリを作成して、wercker.ymlをリポジトリに追加します。

僕は以下の様な設定をしました。

```yaml
box: wercker/rvm@2.0.1
build:
  steps:
    - wercker/bundle-install@1.1.1
deploy:
  steps:
    - script:
        name: configure
        code: |
          git config --global user.email "fly1tkg@gmail.com"
          git config --global user.name "wercker"
          bundle ex rake setup_github_pages\["$GIT_REMOTE"\]
    - script:
        name: deploy to github
        code: |
          bundle ex rake generate
          bundle ex rake deploy
```

### Deployの設定をする

werckerのアプリの設定ページでDeployの設定ができます。

Add Deploy targetのボタン -> Custom deployを選択して、

* Deploy target name: github-pages
* Auto deploy: チェック, master

と設定しました。

Deploy pipelineの項目でAdd new variableというボタンを選択することでデプロイ時の環境変数を設定することができます。

以下のように設定しました。

* name: GIT_REMOTE
* value: `https://GitHubのアプリケーションTOKEN@github.com/ユーザ名/リポジトリ名.git`


アプリケーションTOKENについてはGitHubのSettingのApplicationのセクションで新規作成できます。
wercker用のものを発行しておきましょう。

Deployはrake deployで行うのですが、デプロイを簡単にするためにgitのforceオプションでコミットするように変更します。

Rakefileを開き、pushタスクの`git push`に`-f`をつけます

```ruby
desc "deploy public directory to github pages"
multitask :push do
  puts "## Deploying branch to Github Pages "
  puts "## Pulling any updates from Github Pages "
  cd "#{deploy_dir}" do 
    system "git pull"
  end
  (Dir["#{deploy_dir}/*"]).each { |f| rm_rf(f) }
  Rake::Task[:copydot].invoke(public_dir, deploy_dir)
  puts "\n## Copying #{public_dir} to #{deploy_dir}"
  cp_r "#{public_dir}/.", deploy_dir
  cd "#{deploy_dir}" do
    system "git add -A"
    message = "Site updated at #{Time.now.utc}"
    puts "\n## Committing: #{message}"
    system "git commit -m \"#{message}\""
    puts "\n## Pushing generated #{deploy_dir} website"
    system "git push -f origin #{deploy_branch}"
    puts "\n## Github Pages deploy complete"
  end
end
```

これでDeployの設定が完了です。masterブランチに変更をpushすると自動でgithub-pagesにdeployされるようになります。
