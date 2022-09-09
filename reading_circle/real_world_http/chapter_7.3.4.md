# HTTP/2、HTTP/3のシンタックス:プロトコルの再定義

## HTTP Alternative Servicesによるアップグレード
- HTTP/2
ALPNによるプロトコルネゴシエーションが行える。
  プロトコルネゴシエーションとは、プロトコルがHTTP/1.1なのかHTTP/2なのかを、クライアントとサーバー間で合意を取ること

  - NPNとALPNの解説記事 https://techblog.yahoo.co.jp/infrastructure/http2/ats_http2_pn/

- HTTP/3
ALPNが使用できない。
RFC7838を応用して、HTTP/3で再接続できることをクライアントに伝える方法がある。
(RFC7838の中身は不明)



### HTTP/2とHTTP/3の比較
https://gihyo.jp/admin/serial/01/http3/0003

- ストリームIDのビット数が31->62に増え、オーバーフローの危険性が減少

  - ストリームIDとは、QUICの単方向ストリームと双方向ストリームをどのように使うかのid
[ストリーム](https://eng-blog.iij.ad.jp/archives/10710#%E3%82%B9%E3%83%88%E3%83%AA%E3%83%BC%E3%83%A0)
[ストリームID](https://eng-blog.iij.ad.jp/archives/10710#%e3%82%b9%e3%83%88%e3%83%aa%e3%83%bc%e3%83%a0id)

- 各フレームはストリームIDとフラグを持たなくなった

- フラグによって増減するフィールドがなくなった
- ヘッダー圧縮のアルゴリズムが、HPACKから、QPACKという別の方式に変わった
https://eng-blog.iij.ad.jp/archives/11419
  - HPACK
  p.221参照

  - QPACK
  どこのインデックスに登録するかという情報も追加で送信。
  ただ未登録のインデックスを利用側が参照しようとした場合、待ちが発生するため送受信の順番待ちに起因する遅延が完全に無くなるわけではない。



# 7.4 新たなJavaScript用の通信API
## 7.4.1 Fetch API
- XMLHttpRequestよりもオリジンサーバー外へのサクセスなど、CORSの取り扱いが制御しやすい

  - https://ja.javascript.info/fetch-api#ref-544

- Javascriptのモダンな非同期処理の記述方法であるPromiseに準拠している
Promiseがreturn

- キャッシュを制御できる

  - https://ja.javascript.info/fetch-api#ref-546

- リダイレクトを制御できる

  - https://ja.javascript.info/fetch-api#ref-547

- リファラのポリシーを設定できる

  - https://ja.javascript.info/fetch-api#ref-543

- Service Worker内から利用できる

  - オンライン時にrequestのresponseをキャッシュし、オフライン時にキャッシュを返すことが可能
  - https://webbibouroku.com/Blog/Article/serviceworker
