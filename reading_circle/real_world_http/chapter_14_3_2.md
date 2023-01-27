# 14章 セキュリティ:ブラウザを守るHTTP機能 - その2

@dameleon

## 14.3.2 Content-Security-Policyヘッダ

REF: https://developer.mozilla.org/ja/docs/Web/HTTP/CSP

- Webサイトで利用できる機能のON/OFFを"サーバから"設定するとことで切り替える
  - htmlのresponse headerに設定するもの、ということ
- 設定リストは本参照
  - 大まかに、外部ファイルの読み込みに対するオリジンの制約を設定するのもになる
- この設定に応じて、違反を検知するのは"ブラウザ"自身
  - ex. iframeで外部サイトの表示が不可の条件をブラウザがチェックし、iframe自体のcontentsが表示されなくなり、developer consoleに警告がでる
  - `report-uri`ディレクティブによる違反検知のレポート機能もある
- 制約が強くなりすぎないように注意。実は利用されているユースケースなどが阻害されてしまう
  - ex. `script-src`ディレクティブで`self`を指定したがために、Google Analyticsが動かなくなった、など
  - `Content-Security-Policy-Report-Only`ヘッダによって、動作は止めないがレポートはする、ということも可能

## 14.3.3 Content-Security-PolicyとJavaScript製テンプレートエンジン

- JavaScriptで用いるテンプレートエンジンで、`new Function`を用いたテンプレート生成を行うケースがCSPに引っかかることがある
  - `unsafe-eval`ディレクティブを設定しているケースなど
- そういったケースでテンプレートエンジンを用いる場合は、CSP対応版のものを利用する

## 14.3.4 Mixed Contentへの対応

- http, httpsが混在しているサイトで起きる
  - ちなみにもうChromeはこうなってるはず。Firefox等は分からず。
- Mixed Contentを検知すると、ブラウザで警告が表示されるようになる
- `Content-Security-Policy`(meta tagへの指定も含む)の設定で以下が可能となる
  - httpでもhttpsで取得する
  - Mixed Contentをエラーにする(何も指定してなければ標準動作)

## 14.3.5 クロスオリジンリソースシェアリング(CORS)

REF: https://developer.mozilla.org/ja/docs/Web/HTTP/CORS

- オリジン(サブドメイン部を含めたフルドメイン)を跨いで(= クロスオリジン)、リソース(htmlでもjsonでも画像でも)をリクエストする際の権限設定
  - ex. フロント: `https://example.com`, API: `https://api.example.com`のような構成で、フロントからAPIを呼ぶケースなど
- CSPの`connect-src`ディレクティブでは、"接続することの可否"は設定できるが、CORSは更にアクセス制限を細かく設定できる
- セキュリティのために考えられた仕様であり、APIサーバのようなサーバ側を守るためによく利用される
  - 公開APIを任意のサイトからリクエストされるようなただ乗りを防ぐ
- この制約も"ブラウザ"が暗黙的に検知するものになる
- CORS利用に関しては407ページの図参照
  - 大事なポイント
    - CORSの要求とレスポンスのチェック: 暗黙的にブラウザ自身が行う
    - CORSのレスポンス: サーバが返すもの
  - 現実的なユースケースで、simple cross-origin requestになるものは少ない
    - ex. 認証が何もない外部サイトのendpointに対して、GETで何か取ってくる、とかそのくらい
  - クロスオリジンではCookieがデフォルトでは付与されない点にも注意
    - ex. APIはCookieでセッションを持ってる、などのときに認証エラーになる