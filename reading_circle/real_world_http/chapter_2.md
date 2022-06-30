# HTTP/1.0のセマンティクス：ブラウザの基本機能の裏側

担当: m.sasaki

## 2.1 シンプルなフォームの送信(x-www-form-urlencoded)
- フォームを使ったPOST送信の一番シンプルな送信方式
  - ヘッダーとして`Content-Type: application/x-www-form-urlencoded`設定する
  - Webフォームとcurlコマンドではボディを異なるフォーマットで変換する
    - ブラウザ(Webフォーム):RFC1866
    - curl(--data-urlencode指定時): RFC3986
    - curl(`-d`オプション): 指定された文字列をそのまま接続する
      - キーと値は`=`、各項目を`&`で接続

<br>

- 形式
  - RFC1866
    - ブラウザが変換する際の変換フォーマット
    - スペースを`+`に変換
  - RFC3986
    - RFC1866と多少異なる文字種を扱う
      - スペースを`%20`に変換
    - パーセントエンコーディングと呼ばれている
  - 共通
    - どちらの変換方式でも同じアルゴリズムで復元可能
    - 大雑把にURLエンコーディングと呼んでひとくくりにすることがほとんど

<br>

- `method="GET"`
  - ボディではなくクエリーとしてURLに付与される
    - RFC1866で定義されている
    - 形式は前章HTTP/0.9時代の検索機能として紹介したものと同じ

---

## 2.2 フォームを使ったファイルの送信(multipart/form-data)
- **マルチパートフォーム形式**
  - RFC1867で定義されている
  - ファイル送信することができる
  - 一度のリクエストで複数のファイル送信が可能
    - 受け取り側でファイルを区切る必要がある
    - `Content-Type`にMIMEタイプと、区切り文字列(boundary:境界、しきり)の指定が必須
      - 区切られた文字列の中でも「ヘッダー+空行+コンテンツ」と分かれている
      - ヘッダーにある`Content-Disposition`は`Content-Type`のようなもの

<br>

- 実際にRailsで行った内容
```
--------------------------1b5d7b2bf91e5cbd

Content-Disposition: form-data; name="title"


The Art of Community

--------------------------1b5d7b2bf91e5cbd

Content-Disposition: form-data; name="author"


Jono Bacon

--------------------------1b5d7b2bf91e5cbd--
```

<br>

- 第3のフォーム送信エンコード `text/plain`
  - エスケープせずに改行区切りで値を並べて送信する
  - サービス側のセキュリティ実装が甘いとセキュリティ問題を引き起こす可能性がある
    - [CSRFの危険性がある](https://spring.pleiades.io/spring-security/site/docs/5.0.9.RELEASE/reference/html/csrf.html)
  - あまり使うことがない
<br>

- フォーム送信時のレスポンス
  - POST送信された情報を受け取ったら処理を行う
    - サーバから一旦302 Foundが返却される
    - その後送信結果が表示されるページに遷移させるのがよい
  - リダイレクトさせずに新しいページの内容をリクエストを送ってきたURLに返す方法もある
    - 余計な通信が発生しないがそれ以上のデメリットがある
      - デメリット
        - URLを共有するときに正しく表示できない
        - ブラウザリロードで再度フォームの更新を行おうとしてしまう
    - 送信と表示は分けるほうが良い

---

## 2.3 フォームを利用したリダイレクト
- `<input type="hidden">`が記載されたHTMLを返却
  - フォームの送信先がリダイレクト先
  - このHTMLをブラウザが読み込み、ロード直後にイベント発火させてリダイレクトさせる
    - メリット
      - IEでもデータ量に制限がなくなる
    - デメリット
      - 一瞬白紙のページが表示される
      - Javascriptが無効だと自動遷移できない
    - SAMLという認証プロトコルや、OpenID Connectなどで使用の一部として利用されている(5章で紹介)

---

## 2.4 コンテントネゴシエーション
- ヘッダーを利用してお互いの設定を共有する仕組み
  - リンクは`mdn web docs`
<br>

| リクエスト | レスポンス | ネゴシエーション対象 |
| :----: | :----: | :----: |
| [Accept](https://developer.mozilla.org/ja/docs/Web/HTTP/Headers/Accept) | [Content-Typeヘッダ](https://developer.mozilla.org/ja/docs/Web/HTTP/Headers/Content-Type) | [MIMEタイプ](https://developer.mozilla.org/ja/docs/Web/HTTP/Basics_of_HTTP/MIME_types) |
| [Accept-Language](https://developer.mozilla.org/ja/docs/Web/HTTP/Headers/Accept-Language) | [Content-Languageヘッダ](https://developer.mozilla.org/ja/docs/Web/HTTP/Headers/Content-Language)<br>または<br>HTMLタグ | 表示言語 |
| [Accept-Charset](https://developer.mozilla.org/ja/docs/Web/HTTP/Headers/Accept-Charset) | [Content-Typeヘッダ](https://developer.mozilla.org/ja/docs/Web/HTTP/Headers/Content-Type) | 文字のキャラクターセット |
| [Accept-Encoding](https://developer.mozilla.org/ja/docs/Web/HTTP/Headers/Accept-Encoding) | [Content-Encodingヘッダ](https://developer.mozilla.org/ja/docs/Web/HTTP/Headers/Content-Encoding) | ボディの圧縮 |

---

## 2.5 Cookie
- Webサイトの情報をブラウザ側に保存する仕組み
  - HTTPヘッダーをインフラとして実装されている
  - サーバ　→　クライアントに指示する
    - `Set-Cookie: 名前=値`
  - ステートレスなHTTPでステートフルのように見える実装が可能になる

<br>

- Cookieとユーザー承認
  - EU一般データ保護規則(**GDPR**)
  - 個人情報に対する規制(2018年5月~施行)
  - 課されている制約
    - 厳密に必要なCookieを除き、Cookie使用前にユーザーの同意を得ること
    - 同意前に各Cookieの内容と、目的をわかりやすく明示すること
    - ユーザーの同意を保存すること
    - 特定Cookieの使用をユーザーが拒否してもサービスにはアクセスできるようにすること
    - ユーザーが同意を取り消すのを同意を与えるのと同じくらい簡単にすること
  - [日本への影響と対応](https://service.plan-b.co.jp/blog/marketing/11567/)
    - EU圏内にいる人（旅行で一時滞在している日本人含む）もGDPRの範囲に含まれる
    - 英語サイトの運営、EU圏内に向けたサービス展開でEU圏内からのアクセスにも注意する必要がある
      - 今の時代どこからアクセスが有るかわからないので注意が必要だと感じる


<br>

- Cookieの分類(3種類)
  - Cookieの発行元
    - ユーザーが訪れたサイト(ファーストパーティ)かそれ以外(サードパーティ)か
  - 使用期間
    - ブラウザを閉じたら消える(セッションCookie)か、消えない(永続的Cookie)か
  - 目的
    - 厳密に必要なCookie
      - 機能で必要な情報(カート機能の為のCookieなど)
    - 環境設定Cookie
      - 言語設定、ユーザー名、パスワードなど
    - 統計Cookie
      - アクセスしたページ、クリックしたリンクなどサイト改善に利用するもの
    - マーケティングCookie
      - ユーザーの行動をトラッキング、広告の精度の向上や表示回数のコントロールなどに利用
      - サードパーティの永続的Cookieを利用する

<br>

- Cookieの間違った使い方
  - 無くなって困るデータの格納
    - Cookieの保存はブラウザの設定、端末自体など確実に保存、保存し続けるわけではない
  - データサイズ
    - ヘッダーとして通信に付与するため、通信量が増える
    - ブラウザによって許容されている容量を考慮して4キロバイトという容量は守るべき

<br>

- Cookieに制約を与える
  - Cookieを本来必要としないサーバに送信するのはセキュリティリスクを高めることになる
    - 送信先の制御、寿命の設定などで制約を与える
  - RFC6265:HTTP CookieとSet-Cookieヘッダの定義を行っている文書

<br>

- Expires、Max-Age属性
  - Cookie寿命の設定
    - Max-Age: Cookieが失効するまでの秒数で指定
    - Expires: Cookieが失効する日時を指定
  - どちらもなければブラウザを閉じると消えるセッションCookieになる
- Domain属性
  - クライアントからCookieを送信する対象のサーバ
  - 省略時はCookieを発行したサーバ
- Path属性
  - クライアントからCookieを送信する対象のサーバのパス
  - 省略時はCookieを発行したサーバのパス
- Secure属性
  - HTTPSでの安全な接続以外でクライアントからサーバにCookieを送信しない
- HttpOnly属性
  - JavascriptエンジンからCookieを隠す
  - XSSなどの悪意のあるJavascriptが実行されるセキュリティリスクに対する防護になる
- SameSite属性
  - None(なし)、Lax(緩い)、Strict(厳密)が設定可能
  - RFCには存在しない
    - Chromeバージョン51で導入した属性
    - 現在は全ブラウザが対応、RFCではドラフトの状態
  - 同じオリジンのドメインに対して送信するようになる
  - Chromeでは特に指定がない場合デフォルトで`Lax`にCookieを変更する

<br>

- オリジン
  - 以下の組み合わせ
    - URIスキーム(`http://` | `https://`)
    - ホスト名(webサーバのドメイン名)
    - ポート番号(デフォルトが`http`で`TCP80番`、`https`で`TCP443番`)
---

## 2.6 認証とセッション
- 認証の種類
  - Basic認証
    - ユーザー名とパスワードを「`:`」でつなぎ、Base64エンコーディングした物で認証を行う
    - ヘッダーに`Authorization: Basic <エンコードした文字列>`を指定
    - SSL/TLS通信を使用していないと傍受された時に簡単に漏洩する
  - Digest認証
    - サーバから渡されたデータと、ユーザー名/パスワードを使用してハッシュ計算を行う
      - 計算した値とユーザー名等を渡しサーバ側でユーザー名からパスワードを探し、同じ計算を行う
      - 計算した値が送られてきた値で認証を行う
  - 今では上記どちらもあまり使われていない

<br>

- Cookieを使ったセッション管理
  - フォームとCookieを使ったセッション管理
    - ユーザーIDとパスワードを送信して認証する
      - SSL/TLSの利用が必須
    - サーバで受け取った情報が正しければセッショントークンを発行
      - 発行したトークンはDBに保持
        - 二度目以降はCookieの再送でログイン済みかどうかサーバが判断する
  - CSRFは14章にて

<br>

- 署名付きCookieによるセッションデータの保存
  - サーバから電子署名済みのデータをクライアントに送る
  - クライアントからサーバにCookieを再送
    - サーバは送られてきたCookieから署名を確認
      - 電子署名の仕組みは4章TLSの暗号化にて
  - 署名を行うのも署名の確認もサーバで行う
    - クライアントは鍵が必要ない
  - サーバメリット
    - データ保存の仕組みを用意しなくて良い
    - マイクロサービスでもセッションストレージの暗号化を共通化すれば別のデータストアを用意しなくてもセッションデータを読み書きできる
  - クライアントから見るとCookieを持っている限り一時データの保持ができる
    - 但し端末が変わるとデータの共有ができなくなる
