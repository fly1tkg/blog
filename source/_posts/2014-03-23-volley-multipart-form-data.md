---
title: Volleyでmultipart/form-dataを送信する
author: fly1tkg
layout: post
permalink: /2014/03/volley-multipart-form-data/
categories:
  - Android
---
はじめに書きますが、Volleyは**画像ファイル等の大きいファイルを送ることには適していません**。が、ちょっとファイルを送りたいときに、そこだけapacheのhttpclient等を使うのも微妙な気がするので、このエントリーではVolleyを使ったmultipart/form-dataの送り方を紹介します。

<!--more-->

# はじめに

multipart/form-dataを利用するために**最新のhttpcoreとhttpmineを利用するのがよいです**。<http://hc.apache.org/downloads.cgi> から最新のHttpclientをダウンロードしてlibsのhttpcore.jarとhttpmine.jarを利用したり、maven、gradle経由でアプリに組み込んでください。

# シンプルな実装

<http://stackoverflow.com/questions/16797468/how-to-send-a-multipart-form-data-post-in-android-with-volley>

上記のリンクのようにmultipart/form-data用のリクエストを作成すると割りと簡単に実装できます。ポイントはgetBodyのメソッドでmultipart/form-data形式のバイナリをVolleyのRequestに渡す設計になっている所だと思います。

もう少し実用的な設計にすると以下のようになります。第４引数と第５引数にはそれぞれkey-valueで送信するリクエストを渡します。

```java
public class MultipartRequest extends Request<String> {

    private MultipartEntity entity = new MultipartEntity();

    private final Response.Listener<String> mListener;
    private final Map<String, String> mStringParts;
    private final Map<String, File> mFileParts;

    public MultipartRequest(String url, Response.Listener<String> listener,
            Response.ErrorListener errorListener,
            Map<String, String> stringParts, Map<String, File> fileParts) {
        super(Method.POST, url, errorListener);

        mListener = listener;
        mStringParts = stringParts;
        mFileParts = fileParts;
        buildMultipartEntity();
    }

    private void buildMultipartEntity() {
        for (Map.Entry<String, String> entry : mStringParts.entrySet()) {
            try {
                entity.addPart(entry.getKey(), new StringBody(entry.getValue()));
            } catch (UnsupportedEncodingException e) {
                VolleyLog.e("UnsupportedEncodingException");
            }
        }

        for (Map.Entry<String, File> entry : mFileParts.entrySet()) {
            entity.addPart(entry.getKey(), new FileBody(entry.getValue()));
        }
    }

    @Override
    public String getBodyContentType() {
        return entity.getContentType().getValue();
    }

    @Override
    public byte[] getBody() throws AuthFailureError {
        ByteArrayOutputStream bos = new ByteArrayOutputStream();
        try {
            entity.writeTo(bos);
        } catch (IOException e) {
            VolleyLog.e("IOException writing to ByteArrayOutputStream");
        }
        return bos.toByteArray();
    }

    @Override
    protected Response<String> parseNetworkResponse(NetworkResponse response) {
        return Response.success("Uploaded", getCacheEntry());
    }

    @Override
    protected void deliverResponse(String response) {
        mListener.onResponse(response);
    }
}
```

# 独自のHurlStackと共に利用する

先のシンプルな実装ではgetBodyで**OOM(Out of memory)**が発生する場合があります。というのも送信するファイルサイズが大きい場合Multipart entityをByteArrayInputStreamに書き込む処理が問題になるからです。簡単に言うとここで送信したいデータ分のメモリが確保できなければ、メモリ不足で強制終了するというわけです。

Volleyの通信の処理はHurlStackというクラスにかかれています。VolleyのRequest queueを作成する際に、独自のHurlStackを渡すことができるようになっているのを利用して、Multipart/form-dataの時はファイルをストリームで渡せるようにカスタマイズしたHurlStackを渡して、OOMを防ぎます。

MultipartRequestクラス、ここでMultipart entityを外部へ渡せるようにしておきます。

```java
public class MultipartRequest extends Request<String> {

    private MultipartEntity entity = new MultipartEntity();

    private final Response.Listener<String> mListener;
    private final Map<String, String> mStringParts;
    private final Map<String, File> mFileParts;

    public MultipartRequest(String url, Response.Listener<String> listener,
            Response.ErrorListener errorListener,
            Map<String, String> stringParts, Map<String, File> fileParts) {
        super(Method.POST, url, errorListener);

        mListener = listener;
        mStringParts = stringParts;
        mFileParts = fileParts;
        buildMultipartEntity();
    }

    private void buildMultipartEntity() {
        for (Map.Entry<String, String> entry : mStringParts.entrySet()) {
            try {
                entity.addPart(entry.getKey(), new StringBody(entry.getValue()));
            } catch (UnsupportedEncodingException e) {
                VolleyLog.e("UnsupportedEncodingException");
            }
        }

        for (Map.Entry<String, File> entry : mFileParts.entrySet()) {
            entity.addPart(entry.getKey(), new FileBody(entry.getValue()));
        }
    }

    @Override
    public String getBodyContentType() {
        return entity.getContentType().getValue();
    }

    public MultipartEntity getEntity() {
        return entity;
    }

    @Override
    protected Response<String> parseNetworkResponse(NetworkResponse response) {
        return Response.success("Uploaded", getCacheEntry());
    }

    @Override
    protected void deliverResponse(String response) {
        mListener.onResponse(response);
    }
}
```

独自のHurlStack、MultiPartRequestの時だけ独自の通信処理を利用するようにします

```java
public class MultiPartStack extends HurlStack {
    private final static String HEADER_CONTENT_TYPE = "Content-Type";

    @Override
    public HttpResponse performRequest(Request<?> request, Map<String, String> additionalHeaders)
            throws AuthFailureError, IOException {
        if (!(request instanceof MultipartRequest)) {
            return super.performRequest(request, additionalHeaders);
        }
        HttpPost httpRequest = new HttpPost(request.getUrl());
        httpRequest.addHeader(HEADER_CONTENT_TYPE, request.getBodyContentType());
        setMultiPartBody(httpRequest, request);
        addHeaders(httpRequest, additionalHeaders);
        addHeaders(httpRequest, request.getHeaders());
        HttpParams httpParams = httpRequest.getParams();
        int timeoutMs = request.getTimeoutMs();
        HttpConnectionParams.setConnectionTimeout(httpParams, 5000);
        HttpConnectionParams.setSoTimeout(httpParams, timeoutMs);

        SchemeRegistry registry = new SchemeRegistry();
        registry.register(new Scheme("http", new PlainSocketFactory(), 80));
        registry.register(new Scheme("https", SSLSocketFactory.getSocketFactory(), 443));

        ThreadSafeClientConnManager manager = new ThreadSafeClientConnManager(httpParams, registry);
        HttpClient httpClient = new DefaultHttpClient(manager, httpParams);

        return httpClient.execute(httpRequest);
    }

    private void addHeaders(HttpUriRequest httpRequest, Map<String, String> headers) {
        for (String key : headers.keySet()) {
            httpRequest.setHeader(key, headers.get(key));
        }
    }

    private static void setMultiPartBody(HttpEntityEnclosingRequestBase httpRequest,
            Request<?> request) throws AuthFailureError {
        if (request instanceof MultipartRequest) {
            httpRequest.setEntity(((MultipartRequest) request).getEntity());
        }
    }
}
```

このMultiPartStackを利用したRequestQueueは以下のように利用できます

```java
RequestQueue queue = Volley.newRequestQueue(context, new MultiPartStack());
```

以上のようなコードで大きいファイルの送信もOOMになることなくできます。

# 補足: multipart/form-dataとは？

<http://d.hatena.ne.jp/satox/20110726/1311665904>

上記のリンク等が参考になるのですが、基本的にはHeaderでboundaryと呼ばれる仕切り文字列を指定して、Bodyで送信したいデータをboundaryで区切ってまとめて送信してしまう通信方法です。もっといい仕様はなかったのか、、、  
