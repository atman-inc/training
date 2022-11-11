# JavaScriptによるブラウザからの動的なHTTPリクエスト
本性ではJavaScript環境で提供されているAPIについて解説。

## 11.1 ブラウザのHTTPとライフサイクル
ブラウザがHTTPアクセスをする箇所は2箇所
1. websiteのload
URLをアドレスバーに入れる、<a>タグのリンクをクリックする、フォームの送信などの操作で、HTTP通信が走り、帰ってきたContent-Typeがブラウザで表示可能であれば、画面をクリアして全画面で表示される。
ブラウザの画面をリセットするリクエストを、'グローバルナビゲーション'と呼ぶ
Content-Disposition: attachmentとヘッダーがついている場合、ダウンロード

2. JavaScriptを使ったアクセス
画面表示を終えJavaScriptで通信を行う機能群が使われると、通信が発生。XMLHttpRequest、Fetch、Server Sent EventsであればHTTPが、WebSocketやWebRTCもある。

## 11.2 XMLHttpRequest
XHRの出自については、p.147を参照。
使い方については。p.324のcode例を参照。

memo: 昔はXHRでのみ、progressが使えた

## 11.3 FetchAPI
使い方については。p.326のリスト11-1を参照。
- HTTP通信を行い、サーバーからのレスポンスが返ってくるのを待ち、最初のヘッダー部分のパースを完了する
- その後ボディを最後まで読み込む

### 11.3.2 Fetchのオプション
Fetch APIはデフォルトで厳格な設定がされており、必要に応じて明示的に解除するという設計。

#### 対応データ
- ArrayBuffer
- Blob
- FormData
  - https://developer.mozilla.org/ja/docs/Web/API/FormData/FormData
- Object
- string

#### Fetch APIのCORSモード
- cors XHRデフォルト
- same-origin
- no-cors Fetchデフォルト

#### Fetch APIのCredentials
- omit Fetchデフォルト
- same-origin XHRデフォルト
- include

### 11.3.3 クエリーパラメータの作成と解析
URLSearchParamsを使用して作成と解析を行う。
疑問点: amazonの長いURLもこれで作成している？

### 11.3.4 ボディの送信

- application/x-www-form-urlencoded
  - ファイル送信はできない。1つのフォーム名に対して1つの情報しか送信できないから。
- multipart/form-data
- JSON

### 11.3.5 Fetch APIにしかできないこと

#### キャッシュの制御
- default
- no-store
- reload
- no-cache
  - これだと304を返したい場合でも、casheが使用されてしまう?
- force-cache
- only-if-cached

#### リダイレクトの制御
- follow
  - httpsからhttpにリダイレクトする際はエラーになるらしい
- manual
- error

#### Service Worker対応
Service Workerから外部サービスへの接続にはFetch APIのみ可能
https://qiita.com/poster-keisuke/items/00056b8d5d6275afdb1a

### 11.3.6 Fetch APIを使うときのよくある間違い
- ヘッダーのContent-Typeにapplication/jsonを設定し忘れる
- bodyに当てる際に、JSON.stringifyをつけ忘れる
- デフォルトではクッキを送信しない
- Responseのstatus codeをチェックしていない場合、403で文字列が帰ってくるため、JSONをparseしようとしてもエラー

### 11.3.7 ブラウザ以外のJavaScript環境からのFetch
Node.jsからもnpmにあるnode-fetchパッケージを使用することで、fetchを利用可能に。
ただNode.jsはロケーションがないため、ドメイン名を含むURLが必須

## 11.4 JavaScriptからブラウザのリロードを伴うHTTPアクセス
リンクをクリックした時と同じ挙動にしたい場合は、location.hrefに遷移先のURLを設定する。この場合GETのみ。
JavaScriptで動的にformを生成してからsubmitを呼ぶ方法だとPOSTも可能。

## 11.5 ファイルのダウンロード
ダウンロードが走る場合は下記の2ケース
- Content-Typeが表示不可能なものである場合。ex) audio/mpeg
- Content-Disposition: attachmentと指定がある場合。(Content-Disposition: inlineの場合は、表示可能)

### 11.5.1 動的にコンテンツを作成してダウンロード
JavaScriptでファイルを生成して、<a>タグをクリックさせる挙動を行わせることで、リモートからダウンロードしたかのような挙動が再現可能

## 11.6 Server-Sent Events
XHRやFetchと違い、サーバーから通信を行うためのプロトコル
疑問: 実際の用途

## 11.7 WebSocket
p.230参照
