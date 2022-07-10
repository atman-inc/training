# 2章 2.7以降

担当: @nmurakami0

## 2.7 プロキシ

- プロキシ: 通信内容をサーバーが理解する
  - なので、キャッシュやフィルタリングなどが出来る
- ゲートウェイ
  - 通信内容をそのまま転送し、内容を改変してはいけない
  - リバースプロキシと同義

![](https://www.ntt.com/content/dam/nttcom/hq/jp/bizon/images/glossary/proxy-server.jpg)

ref: https://www.ntt.com/bizon/glossary/j-h/proxy-server.html

![](https://eset-info.canon-its.jp/files/user/malware_info/images/special/201021/2.jpg)

ref: https://eset-info.canon-its.jp/malware_info/special/detail/201021.html

- 図でいう”インターネット”の位置が重要
  - プロキシは全ての通信がプロキシ経由でないと意味がないので、内部ネットワークから出るときにプロキシサーバーを経由するよう設定することが多い
    - 全ての通信がプロキシ経由になるので信頼出来ないクライアントには悪用される危険性があるので認証を掛けることも多い
      - > Because forward proxies allow clients to access arbitrary sites through your server and to hide their true origin, it is essential that you secure your server so that only authorized clients can access the proxy before activating a forward proxy.
        - https://httpd.apache.org/docs/2.4/en/mod/mod_proxy.html
      - 昔: X-Forwarded-Forを使用
      - 今: Forwardedを使用 (2014年にRFC7239で標準化された)
  - リバースプロキシはリバースプロキシ役のサーバーから、内部ネットワーク内のサーバーに接続することが多い
    - "インターネット"の位置が逆なので"リバース"
- ただ、設定・用途次第なので、プロキシサーバーの間にインターネットを通ることや、リバースプロキシの先にインターネット経由で繋ぐこともある

## 2.8 キャッシュ

- すでにダウンロード済みで内容に変化がなければダウンロードを抑制し、それによってパフォーマンスを上げるメカニズムのこと
  - 本では通信コスト的な意味合いが強調されているが、サーバーでの処理時間的な意味合いもあるはず
- 2種類ある
  1. ブラウザ上のキャッシュ
  2. ウェブサービスとブラウザの間にたつ、プロキシサーバーやCDN (Content Delivery Network)

以降では導入時期順に解説

1. 更新日時によるキャッシュ
    - HTTP/1.0のキャッシュの仕組み
    - 手順:
      1. サーバーから`Last-Modified`レスポンスヘッダーが付与される
          - e.g., `Last-Modified: Wed, 08 Jun 2016 15:23:45 GMT`
      2. クライアントは、その日時を`If-Modified-Since`ヘッダーに入れてリクエスト
      3. ウェブサーバーはリクエストヘッダーの日時とサーバー上のコンテンツを比較
      4. 変更がなければ`304 Not Modified`を返し、あれば`200 OK`でコンテンツを返す
2. Expiresヘッダー
    - HTTP/1.0で導入
    - 1の仕組みだとサーバーへ通信はどのみちする
    - Expiresレスポンスヘッダーの場合、次のリクエスト時にそこに記載の日時の期限内だとそもそもサーバーにアクセスしないでキャッシュを利用する
      - e.g., `Expires Wed, 08 Jun 2016 15:23:45 GMT`
    - 更新がめったにされない静的コンテンツに向いている
    - https://developer.mozilla.org/en-US/docs/Web/HTTP/Caching#expires_or_max-age
      - 日付のパースが難しくてバグが多かったのと、システムクロックを変えて無理やりアクセスすることも可能なのでmax-ageが導入されたとのこと
3. Pragma: no-cache
    - HTTP/1.0で導入
      - Cache-Controlの後方互換性のために1.1でも残っている
    - Pragmaヘッダーのペイロードとして定義されているのは`no-cache`のみ
      - `Cache-Control: no-cache`と同義
    - キャッシュがあったとしても、サーバーにリクエストをしてvalidationしてからそのキャッシュを使うように、クライアントが指示するためのヘッダー
    - https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Pragma
4. 確実にキャッシュしない条件
    - 以下を1つでも満たせばキャッシュされることはない
      1. GETとHEAD以外のメソッド
      2. Cache-Controlヘッダーにprivateが設定されている
      3. Cache-Controlヘッダーにno-storeが設定されている
      4. Authorizationヘッダーがあるが、Cache-Controlヘッダーにpublicがない
          - https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Cache-Control
          - publicがあるということは、shared cacheに保存しても内容的に問題がないということを意味するため
      5. ステータスコードが200, 203, 207, 302, 410, 404以外
          - 203
            - https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/203
          - 207 Multi-Status (WebDAV)
          - 410
            - https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/410
5. ETagの追加
    - 単一の日時を基準にキャッシュを使うか判断すると、どのエンティティのどの項目の日時に基づいて判断するかが困難または決められないケースがあり得る
    - ETagはファイルに関連するハッシュ値を使って比較するので、コンテンツが変化したかで判断できる
    - https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/ETag
    - 手順
      1. サーバーはETagレスポンスヘッダーを付与
          - e.g., `ETag: "33a64df551425fcc55e4d42a148795d9f25f89d4"`
      2. クライアントはIf-None-Matchリクエストヘッダーを付けてリクエスト
          - `If-None-Match: "33a64df551425fcc55e4d42a148795d9f25f89d4"`
      3. サーバーは送る予定のファイルのETagと比較し、If-None-Matchリクエストヘッダーの値と一緒なら304を返す
          - 単にファイルを返す場合というより、CDNやサーバー側で処理がある場合に有用と思われる
            - Railsの例: https://railsguides.jp/caching_with_rails.html#%E6%9D%A1%E4%BB%B6%E4%BB%98%E3%81%8Dget%E3%81%AE%E3%82%B5%E3%83%9D%E3%83%BC%E3%83%88
    - Weak ETagsとStrong ETagsの2種類がある
      - Weak ETags
        - byte単位で同一でなくても、セマンティク的に同一ならキャッシュを返す
        - `ETag: W/"0815"`
      - https://support.cloudflare.com/hc/en-us/articles/218505467-Using-ETag-Headers-with-Cloudflare
6. Cache-Control
    - https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Cache-Control
    - shared cache: Proxy, CDNなど
    - 主要なdirectives
      - response directives
        - public
          - shared cacheに保存して良いと指示
        - private
          - shared cacheに保存してはだめと指示
        - max-age=n
          - origin serverでレスポンスが生成されてからn秒はfreshということ
            - Ageレスポンスヘッダーが入っているとその分差し引かれる
        - no-cache
          - `Pragma: no-cache`と同義
        - no-store
          - キャッシュしてはいけないと指定
        - immutable
          - reloadしてもserverにrevalidateのリクエスト(e.g., If-None-Match or If-Modified-Since)を送らない
            - https://hacks.mozilla.org/2017/01/using-immutable-caching-to-speed-up-the-web/
            - https://developers.cloudflare.com/cache/about/cache-control/#other
          - chromeは非対応
      - request directives
        - no-cache
          - > Browsers usually add no-cache to requests when users are force reloading a page
          - スーパーリロードするか、開発者ツールでDisable Cacheにチェックを入れると付与される
    - [Prevent unnecessary network requests with the HTTP Cache](https://web.dev/http-cache/)
    - cache busting
      - ずっとキャッシュを使わせたいが、新しいバージョンをリリースしたらそちらを読み込ませたいときに利用する
        - e.g., webpackがハッシュを埋め込んだファイル名を使っている
        - e.g., `<script src=https://example.com/react.0.0.0.js></script>`
7. Vary
    - Varyレスポンスヘッダーは、同じURLでもクライアントによって返す結果が異なることを示す
    - どのヘッダーによってコンテンツが変わるかを示すことで、それに応じて正しくキャッシュすることが出来るようになる
    - [Content negotiation](https://developer.mozilla.org/en-US/docs/Web/HTTP/Content_negotiation)
    - ただし、現在Googleのガイドラインで推奨されているのは、同一のコンテンツを全てのブラウザに配信し、ブラウザ側で必要な設定を選ばせる
      - これがresponsive design
        - https://developers.google.com/search/mobile-sites/mobile-seo/responsive-design

## 2.9 リファラー

- Refererリクエストヘッダー: ユーザーが当該URLに到達したリンクがある、直前のページのURLを表すヘッダー
  - referrerのタイポ
- GETパラメータも、Refererヘッダーを通して外部のサービスに送信されてしまう
  - よってプライバシー情報流出の危険性がある
  - アクセス元がHTTPSで、アクセス先がHTTPの場合はリファラーヘッダーを送信しない
    - リンクがあるページがHTTPSで、リンクの飛び先がHTTPということ

## 2.10 検索エンジン向けのコンテンツのアクセス制御

- robots.txt
  - サーバーのコンテンツ提供者が、クローラーに対してアクセスの許可・不許可を伝えるためのプロトコル
    - blocklistの役割
  - [Google による robots.txt の指定の解釈](https://developers.google.com/search/docs/advanced/robots/robots_txt?hl=ja)
  - RFCにはなっていない
    - ドラフトは提出されている
      - [Robots Exclusion Protocol](https://datatracker.ietf.org/doc/html/draft-koster-rep)
    - parserはGoogleが公開
      - [Google Robots.txt Parser and Matcher Library](https://github.com/google/robotstxt)
- サイトマップ
  - ウェブサイトに含まれるページ一覧とそのメタデータを提供するXMLファイル
    - allowlistの役割
  - Flashコンテンツや動的ページからのリンクなど、クローラーによってはページが発見出来ない場合でもサイトマップで補完可能となる
    - Googlebotはheadless Chromiumでレンダリングするので、動的ページでも影響はおそらくほぼない
      - [Googlebot が JavaScript を処理する仕組み](https://developers.google.com/search/docs/advanced/javascript/javascript-seo-basics?hl=ja#how-googlebot-processes-javascript)
  - Googlebotでもサイトマップは無駄にはならないとのこと
    - [サイトマップが必要かどうか](https://developers.google.com/search/docs/advanced/sitemaps/overview?hl=ja#do-i-need-a-sitemap)

## 2.11 ユーザーエージェント

- ブラウザなどの通信を代行するプログラムのことをユーザーエージェントと呼ぶ
  - UAとよく略される
- User-Agent リクエストヘッダー
  - サーバーにリクエストしているUAの情報を伝えるためのヘッダー
  - e.g., `Mozilla/5.0 (Macintosh; Intel Mac OS X x.y; rv:42.0) Gecko/20100101 Firefox/42.0`
    - 自分よりも前に普及した、古いブラウザの名前を含めるという慣習がある
- 近年の活用例: 認証機能を提供するプラットフォームで、どのデバイスからいつログインしたかという情報の一覧を出すのに利用されている
  - e.g., Twitter, Google
  - e.g., Adobe Creative Cloudだとデバイス数が制限されていて、それを利用している
    - https://helpx.adobe.com/download-install/using/device-activation-limit.html
