# 10章 クライアント視点で見るRESTful API

## 10.1 RESTfulAPI

- マッシュアップ
  - 各サービスで提供されているAPIを用いて、複数のWEBサービスを組み合わせて新たなサービスを作り出す
- REST (REpresentational State Transfer)
  - Roy Fieldingの博士論文で提唱
    - [Architectural Styles and the Design of Network-based Software Architectures](https://www.ics.uci.edu/~fielding/pubs/dissertation/top.htm)
  - 特性
    - APIをウェブサーバーを通じて提供
    - パスに対してメソッドを送ることでサービスを得る
    - APIの成功可否はステータスとしてクライアントに通知される
    - URLはリソースの場所を表す表現である
    - 必要に応じてGETパラメータ、POSTのボディーを用いる
    - 返り値としては、JSONやXMLのような構造化テキスト、あるいは画像データなどが返ってくることが多い
  - REST原則に従ったものをRESTfulと表現する
    - [本当にRESTは「4つの原則」？原文に当たって分かった驚きの事実](https://xtech.nikkei.com/atcl/nxt/column/18/00138/092100882/)

### 10.1.1 REST APIとRPCの違い

- RPC:
  - URLは1つで、リクエスト時にサービス名とメソッド名を渡すことで何をするかを指定する
  - POSTのみ
    - ボディにメソッドや引数を入れて送信
  - e.g., gRPC
    - https://cloud.google.com/apis/design/standard_methods

### 10.1.2 Web APIとトランザクション

- RESTfulの場合以下を満たす必要がある
  - クライアント側では管理するべきステートが存在せず、1介護とのリクエストは独立して発行できる
- RESTfulにするためには、1リクエストをAtomicにするしかない
  - しかし、多くのユーザーが使うAPIではユースケースが汎用的になるので粒度が細かいAPIセットにせざるを得ない
    - その場合のAPIサーバー側で出来る方法:
      1. 連続して呼び出す
      2. ソフトウェアでトランザクションを自作する
      3. 後からチェックプログラムをバッチで走らせる

### 10.1.3 HATEOAS

Hypermedia As The Engine Of Application Set

- インターフェースの一般化を仕様化したもの
- [REST成熟度モデルの３レベル](https://www.infoq.com/jp/news/2010/03/RESTLevels/)の最上位
  - レベル1: リソース
  - レベル2: HTTP verb
  - レベル3: HATEOAS
- レスポンスに、リクエストで求めたオブジェクトに対して実行できる操作と関連オブジェクトのリンクを含む
  - よって、
    - リンクをたどればAPIの機能に何があるかを知り活用することが出来る
      - 通常のウェブサイトと近いイメージ
    - URLの変更に強い
- 活用例は少ない
  - https://techlife.cookpad.com/entry/2014/09/08/093000
  - おそらく以下のような理由
    - レスポンス構造が複雑になる
    - 人が見るなら良いが、レスポンスを機械的に判断するのが結局難しい

### 10.1.4 RESTfulとREST-ish

REST APIは階層化されたリソースを持ち、HTTPのシンタックスとセマンティクスを守ったAPI構造を備えることを理想としている

- 本来RESTでは良くない例: リソースの住所であるべきURLに余計な情報が入っている
  - URLに動詞が入っている
  - /apiの文字が入っている
  - フォーマットを表す文字(/json)が入っている
  - バージョンが入っている
- RESTなら、バージョンやフォーマットはAcceptヘッダーに入れるべき
- REST風なのでRESTish

## 10.2 メソッド

- メソッドの分類には、安全と冪等の2指標がある
  - 安全: 実行してもリソースを変化させない
    - GET, HEAD, OPTIONS
  - 冪等: 何度実行しても結果が変わらない
    - PUT, DELETE: 冪等
- XMLHttpRequestはGETとHEAD時にボディーを指定しても無視する
- オーバーロードPOST
  - ヘッダーなどにPUTやDELETEであることを入れ、メソッド自体はPOSTにする
- [Custom methods](https://cloud.google.com/apis/design/custom_methods)
  - They should only be used for functionality that cannot be easily expressed via standard methods

## 10.3 ステータス

- [HTTP レスポンスステータスコード](https://developer.mozilla.org/ja/docs/Web/HTTP/Status)
- どこまで厳密に適用するかは判断が分かれる
  - e.g., 一覧を返すAPIでエンティティが0個の場合に、404にするか200で空のリストを返すか

## 10.4 ボディー

- RESTful APIのルールに従うなら、`Accept: application/json`を付与する
  - ただ、付与しなくてもレスポンスを返してくれるケースが多い
- リクエストボディーをJSONにする場合、Content-Type: application/jsonを付与しないとライブラリ次第では読み込みを拒否する場合がある

## 10.5 実際のREST APIを見てみる (PAY.jpの例)

- アクセストークンを使用するメリット
  - トークンが漏れてもトークン単位で無効にすることが出来る
- PAY.jp
  - https://pay.jp/docs/api/
- BASIC認証
  - ユーザー名とパスワードを:で連結したものをbase64でエンコードしてAuthorizationヘッダーに入れる
    - e.g., `echo -n "$user_name:$password" | base64`
  - curlなら-uオプションで指定可能
    - `curl -u user:password $URL`

## 10.6 実際のREST APIを見てみる (GitHubの例)

- GitHubのAPIの認証として、トークンを使う方法とOAuth2を使う方法がある
- [Authorizing OAuth Apps](https://docs.github.com/en/developers/apps/building-oauth-apps/authorizing-oauth-apps)
  - [Web application flow](https://docs.github.com/en/developers/apps/building-oauth-apps/authorizing-oauth-apps#web-application-flow)

ユーザー目線での流れ

1. Users are redirected to request their GitHub identity
2. Users are redirected back to your site by GitHub
3. Your app accesses the API with the user's access token


## 10.7 REST APIにアクセスする時の細かいトピック

- タイムアウト
  - ctxの生成に`context.WithTimeout`を使用する
    - https://pkg.go.dev/context#WithTimeout
- アクセス数制限
  - ウェブサービスは1秒間の呼び出し回数上限を決めている
    - 10rpsのことが多い
    - Slackの例
      - https://api.slack.com/docs/rate-limits
  - Goのライブラリ:
    - https://pkg.go.dev/golang.org/x/time/rate


