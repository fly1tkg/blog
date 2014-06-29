---
title: SimpleでXMLをParseする
author: fly1tkg
layout: post
permalink: /2013/02/simple-xml-parse/
categories:
  - Android
tags:
  - simple
  - xml
---
JavaでXMLをParseするライブラリの一つにSimpleというものがあります。こいつはAndroidにも使えるので紹介します。

# Simpleとは

POJOなオブジェクトに特定のannotationをつけると、そのオブジェクトとXMLを簡単に相互変換出来るようになる。すごくいいやつ。

<http://simple.sourceforge.net/>

# インストール

Downloadのページから最新版をダウンロードします。

<http://simple.sourceforge.net/download.php>

ダウンロードした中身のjarフォルダの中身をAndroid Projectのlibsフォルダに入れればOK

Mavenを利用しているならpom.xmlに

<div>
  <div id="highlighter_869051">
    <pre class="brush: xml; gutter: true">&lt;dependency&gt;
    &lt;groupId&gt;org.simpleframework&lt;/groupId&gt;
    &lt;artifactId&gt;simple-xml&lt;/artifactId&gt;
    &lt;version&gt;2.6.9&lt;/version&gt;
&lt;/dependency&gt;</pre>
  </div>
</div>

を記述すればOK

サンプル

[本家][1]のTutorialから、XML -> POJOオブジェクトの方法について。

例えば以下のようなXMLがあった場合、

<div>
  <div id="highlighter_657849">
    <pre class="brush: xml; gutter: true">&lt;root id="123"&gt;
   &lt;message&gt;Example message&lt;/message&gt;
&lt;/root&gt;</pre>
  </div>
</div>

これを以下のPOJOの中にいれたい

<div>
  <div id="highlighter_187164">
    <pre class="brush: java; gutter: true">@Root(name = "root")
public class Example {
 
    @Element(name = "message")
    private String text;
 
    @Attribute(name = "id")
    private int index;
 
    public String getMessage() {
        return text;
    }
 
    public int getId() {
        return index;
    }
}</pre>
  </div>
</div>

この変換処理はSimpleを使えばこんなに簡単

<div>
  <div id="highlighter_121500">
    <pre class="brush: java; gutter: true">String xml = "&lt;root id="123"&gt;&lt;message&gt;Example message&lt;/message&gt;&lt;/root&gt;";
        Serializer serializer = new Persister();
        try {
            Example example = serializer.read(Example.class, xml);
        } catch (Exception e) {
            e.printStackTrace();
        }</pre>
  </div>
</div>

ネストしたオブジェクトに変換する

XMLが深くネストしている場合はこんな感じ。要素名がクラス名やメンバ変数名を一致している場合はnameのプロパティを省略出来ます。

<div>
  <div id="highlighter_187111">
    <pre class="brush: xml; gutter: true">&lt;configuration id="1234"&gt;
   &lt;server port="80"&gt;
      &lt;host&gt;www.domain.com&lt;/host&gt;
      &lt;security ssl="true"&gt;
         &lt;keyStore&gt;example keystore&lt;/keyStore&gt;
      &lt;/security&gt;
   &lt;/server&gt;
&lt;/configuration&gt;</pre>
  </div>
</div>

<div>
  <div id="highlighter_832560">
    <pre class="brush: java; gutter: true">@Root
public class Configuration {
 
   @Element
   private Server server;
 
   @Attribute
   private int id;
 
   public int getIdentity() {
      return id;
   }
 
   public Server getServer() {
      return server;          
   }
}
 
public class Server {
 
   @Attribute
   private int port;
 
   @Element
   private String host;
 
   @Element
   private Security security;
 
   public int getPort() {
      return port;          
   }
 
   public String getHost() {
      return host;          
   }
 
   public Security getSecurity() {
      return security;          
   }
}
 
public class Security {
 
   @Attribute
   private boolean ssl;
 
   @Element
   private String keyStore;
 
   public boolean isSSL() {
      return ssl;          
   }
 
   public String getKeyStore() {
      return keyStore;          
   }
}</pre>
  </div>
</div>

# 同じ要素が連続しているときはListに変換できる

<div>
  <div id="highlighter_349824">
    <pre class="brush: xml; gutter: true">&lt;propertyList name="example"&gt;
   &lt;list&gt;
      &lt;entry key="one"&gt;
         &lt;value&gt;first value&lt;/value&gt;
      &lt;/entry&gt;
      &lt;entry key="two"&gt;
         &lt;value&gt;first value&lt;/value&gt;
      &lt;/entry&gt;
      &lt;entry key="three"&gt;
         &lt;value&gt;first value&lt;/value&gt;
      &lt;/entry&gt;
      &lt;entry key="four"&gt;
         &lt;value&gt;first value&lt;/value&gt;
      &lt;/entry&gt;
   &lt;/list&gt;
&lt;/propertyList&gt;</pre>
  </div>
</div>

<div>
  <div id="highlighter_600736">
    <pre class="brush: java; gutter: true">@Root
public class PropertyList {
 
   @ElementList
   private List&lt;Entry&gt; list;
 
   @Attribute
   private String name;
 
   public String getName() {
      return name;
   }
 
   public List getProperties() {
      return list;
   }
}
 
@Root
public class Entry {
 
   @Attribute
   private String key;
 
   @Element
   private String value;
 
   public String getName() {
      return name;
   }
 
   public String getValue() {
      return value;
   }
}</pre>
  </div>
</div>

# 必須では無いノードを指定する

XMLの中に必須ではないノードがある場合、required = falseのオプションの指定が必要です。

<div>
  <div id="highlighter_404723">
    <pre class="brush: xml; gutter: true">&lt;optionalExample id="10"&gt;
   &lt;address&gt;Some example address&lt;/address&gt;
&lt;/optionalExample&gt;</pre>
  </div>
</div>

<div>
  <div id="highlighter_186675">
    <pre class="brush: java; gutter: true">@Root
public class OptionalExample {
 
   @Attribute(required=false)
   private int version;
 
   @Attribute
   private String id;
 
   @Element(required=false)
   private String name;  
 
   @Element
   private String address;
 
   public int getId() {
      return id;
   }
 
   public int getVersion() {
      return version;
   }
 
   public String getName() {
      return name;
   }
 
   public String getAddress() {
      return address;
   }
}</pre>
  </div>
</div>

その他の使い方等

本家サイトに大量に乗っているので参考にしてください。

<http://simple.sourceforge.net/download/stream/doc/tutorial/tutorial.php>

[http://simple.sourceforge.net/download/stream/doc/examples/examples.php][1]

 [1]: http://simple.sourceforge.net/download/stream/doc/tutorial/tutorial.php
