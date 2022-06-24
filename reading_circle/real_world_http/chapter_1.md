# HTTP/1.0の世界: 基礎となる4つの要素

## 1.1 HTTPの歴史

- HTMLのドキュメントを要求 -> 取得するプロトコルは、バージョンを経て機能が追加されてきた
- 管理団体
  - IETF
  - ISOC
  - IANA
  - W3C
  - WHATWG
- RFC
  - IETFが中心となり管理する仕様書集
  - プロトコル, 処理など様々な仕様があり、この仕様に準拠することでシステムはお互いに疎通できる
  - `RFC+${数字}`の形式で、様々な仕様が定義される
    - NOTE:
      - IEEE, ECMAなど、RFC以外の外部の仕様を参照することもある
  - ソフトウェアの実装がなされてから、RFCとして策定されるケースも多々ある
- 管理団体と変移
  - メディアタイプ(MIME Type)
    - 全体はIANAの管理
    - 個別のRFCも存在
  - HTML
    - W3C HTML5 -> WHATWG HTML Living Standardへ移行
      - NOTE:
        - WHATWGはブラウザの開発団体(Mozilla, Apple, Opera, ...)が主軸になり立ち上げた団体。それまでのW3Cによる仕様策定の状況を改善するために立ち上げた。
    - アプリケーション開発者向け: https://developer.mozilla.org/ja/docs/Web

---

- インターネットに関わる仕様は、その管理団体の発行する仕様を参照すること
- 管理団体の歴史や仕様の変移にも目を向けてみる

## 1.2 HTTP/0.9でできることを試す

本書参照のこと。

- `<isindex>`
  - REF: https://www.tohoho-web.com/html/isindex.htm
  - 検索ワード入力, 処理用のタグ
    - 現在で言う`<form method="get"><input /></form>`に近しい
  - `GET http://example.com/?search+word`のようなURLとしてリクエストされ、サーバは該当のHTMLを返却する

## 1.3 HTTP/0.9から1.0への道のり

- HTTP/0.9での課題
  - 1つのドキュメントを送る機能のみ
  - 通信内容は全てHTMLである想定
  - 検索のリクエストを送る以外のリクエストは送信できない
  - リクエスト, レスポンスの状態を知る術がない
- HTTP/1.0までの成長
  - 1.0未満ではあるが、多数の機能を取り込み、ほぼ現在のHTTPと同じようなプロトコルとして成り立つ
    - 1992年版
      - シンプル版: HTTP/0.9互換
      - フル機能版: ほぼHTTP/1.0
  - リクエストでの変更点
    - リクエスト時のメソッド追加
    - HTTPバージョンの追加
    - ヘッダの追加
  - レスポンスでの変更点
    - HTTPバージョンを含むステータスの返却
    - リクエストと同形式のヘッダの追加

## 1.4 HTTPの先祖(1)電子メール

- ヘッダは電子メールのプロトコルからの由来
- 分類
  - 一般ヘッダ: https://developer.mozilla.org/ja/docs/Glossary/General_header
  - エンティティヘッダ: https://developer.mozilla.org/ja/docs/Glossary/Entity_header
  - リクエストヘッダ: https://developer.mozilla.org/ja/docs/Glossary/Request_header
  - レスポンスヘッダ: https://developer.mozilla.org/ja/docs/Glossary/Response_header
  - エンドツーエンドヘッダ, ホップバイホップヘッダ: https://developer.mozilla.org/ja/docs/Web/HTTP/Headers
  - `X-`ヘッダ: 自アプリケーション独自のヘッダ
    - https://zenn.dev/ys/articles/a58b02e3cbc2f839f7f1
- MIME Type
  - OSがファイルを区別する方式は、OS毎に管理されている
  - ブラウザは、OSとは別にファイルの種類毎の動作を管理する
  - `大項目/詳細`のような形式
- Content-Typeとセキュリティ
  - `<img src="/cgi-bin/counter.cgi" />`が画像として表示されるのは、Content-Typeのおかげ
  - Content Sniffing
    - MIME Typeではなく、ファイルの中身を検査してファイルの種類を推測する
    - 思わぬJSの実行などが行われるため、セキュリティホールとなりやすい

---

- HTMLとメールを比較する
  - ヘッダ + 本文の構造は同一
  - HTTPリクエストでは、先頭にメソッド+パスが追加される
  - HTTPレスポンスでは先頭にステータスが追加される

## 1.5 HTMLの先祖(2)ニュースグループ

- 記事を購読するためのプラットフォーム(= 巨大掲示板)
- マスタ - スレーブ(時代的に: main - replica)構成隣、そのサーバ間でNNTPというプロトコルが定められる
- HTMLはニュースグループから、メソッドとステータスの機能を参考にする
- 非標準のステータスコード
  - RFC外でアプリケーション独自のhttp statusが返却されることもある

## 1.6 リダイレクト

- http status codeで300番台はリダイレクトを示す
- 300 Multiple Choices
  - リダイレクトが複数の選択肢を持つケースのstatus code
  - 筆者も私も見たことありません
  - `Location`ヘッダで推奨を返すケースもあり
- 分類
  - メソッドが変更可能か
  - 恒久的 or 一時的
  - キャッシュするか
- 欠点
  - リダイレクトが発生する毎に、通信が発生する
  - 回数に関するガイドラインも存在する

## 1.7 URL(Uniform Resource Locators)

- URLとURI
  - URIにはURNの仕様が付随する
  - URLは住所、URNは名前そのもの
- URLの形式は、`schema://hostname/path`
  - 全要素: `schema://user:password@hostname:port/path?query#fragment`
- クエリ
  - `?`から始まる`key=value`を`&`で繋げた文字列
  - 利用可能文字は限定されており、エンコーディングする必要がある
  - 同名の場合に`key[]=value`のような形式を用いないと、一部WEBサーバは最後の値のみが利用されるケースもある
- 情報化されたURL
  - URLの構成はヒューマンリーダブルであるほど良いケースが多々ある
    - 検索ランキングの向上
    - 人間が見て直感的にリソースを把握できる
  - 日本語は、元来エンコーディング済みのものが表示されていたが、最近のブラウザはデコードされた日本語を表示してくれる
- URLとしての長さは大体2000文字が慣例
- punycode
  - 国際化ドメイン名(IDN)のため、半角文字以外の文字をURLとして用いることができる
- 正規URL
  - 検索エンジンやBOT等が、ノイズの入っていないURLを扱うことで情報の分散や同一性を正しく認知できる
- Protocol-Relative URL
  - scheme部を省略すると、ブラウザは現在のページのschemeに従ってリソースを取得する
  - なるべく利用はしないようなガイドラインがある

## 1.8 Body

- HTTPにおいて、ヘッダとの間に空行を挟んで、以降はBodyとなる
- 1回のレスポンスでは1ファイルしか返すことがない
  - NOTE: ちなみにメールのbodyは、複数のcontentを扱える
- GETリクエストのBody
  - 基本的に`GET`リクエストにBodyは付与できない。クエリパラメータで表現するべき。
    - "想定内だが、推奨されない使い方"
  - Elasticsearchさんは、Bodyの付与が推奨されないメソッドでもBodyを受け取ってくれる
