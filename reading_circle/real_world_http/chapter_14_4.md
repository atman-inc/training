# 第14章 セキュリティ: ブラウザを守るHTTPの機能

## 14.4 中間者攻撃 (MITM攻撃)

- Man-in-the-middle attack
  - https://developer.mozilla.org/ja/docs/Glossary/MitM
  - https://www.cloudflare.com/ja-jp/learning/security/threats/on-path-attack/
- Wifiを利用することが多い
  - 偽のアクセスポイントを作成する
  - 暗号化レベルの低いWifiスポットで傍受する
  - ルーターの脆弱性を利用する
  - https://www.itscom.co.jp/forbiz/column/vpn/4562/
- 中間者攻撃を避けるには、HTTPS(TLS)を利用するしかない
  - TLSでもノーリスクではない (HSTSで解説)

## 14.4.4.1 HTTP Strict Transport Security (HSTS)

https://developer.mozilla.org/ja/docs/Glossary/HSTS

- サーバー側から、"今後接続するときはHTTPSで接続して欲しい"と要請する仕組み
- 手法
  - サーバーがレスポンスを返すときにレスポンスヘッダーに以下を付与する
    - Strict-Transport-Security: max-age=<expire-time>
    - Strict-Transport-Security: max-age=<expire-time>; includeSubDomains
      - includeSubDomainsがあると、この規則がサイトのすべてのサブドメインにも適用される
- ブラウザは内部にこのヘッダーを送信したURLのデータベースを持ち、URLにアクセスする際に参照する
- 初回はどうするか: 
  - https://developer.mozilla.org/ja/docs/Web/HTTP/Headers/Strict-Transport-Security#strict_transport_security_%E3%81%AE%E3%83%97%E3%83%AA%E3%83%AD%E3%83%BC%E3%83%89
  - Chromeは以下から事前申請が可能
    - https://hstspreload.org/
    - サーバー側は以下の形式のレスポンスヘッダーを付けるようにしておく必要がある
      - `Strict-Transport-Security: max-age=63072000; includeSubDomains; preload`
        - max-ageは1年以上でないとだめ
- ただ、HTTPSならMITM攻撃に対して万全というわけではない
  1. 公的な認証局が発行した証明書ではなく、独自に作成したいわゆる「オレオレ証明書」を用いている場合、正当性がクライアント側で検証できない
      - https://eset-info.canon-its.jp/malware_info/term/detail/00049.html
  2. 認証局の設定ミスやクラックにより、不適切な証明書が発行されてしまうケースが発生

## 14.5 セッションハイジャッキング

- ウェブサービスのセッショントークンを盗み出しウェブサイトにログインする攻撃
- XSSや中間者攻撃を踏み台にして、クッキーを盗む

### 古のセッション管理とセッション固定化攻撃

- セッション管理: ユーザーのクライアントを識別するということ
- 昔は識別に、クライアントの固有のIDが使われていた
  - e.g.,
    - フィーチャーフォンの個体識別番号
    - 端末識別番号UDID (iOS7より前)
    - MACアドレス
  - プライバシーの問題がある
    - 固有のIDに紐づくデータを集められれば個人が特定できてしまう
  - セキュリティの問題
    - フィーチャーフォンではユーザーエージェント中やヘッダーにIDが書き込まれていたので、盗み出すのが簡単かつユーザーは変更できない
- セッショントークンのURL(クエリパラメータ)への埋め込み
  - ログイン前後でセッショントークンを変化させない実装をアプリでしているとセッション固定化攻撃が可能になってしまう
  - セッション固定化攻撃
    - 攻撃者がウェブサイトにアクセスしセッショントークンを得て、そのセッショントークンを含むURLにアクセスさせログインをさせることで、正規のセッショントークンにアップグレードされる
      - 攻撃者はセッショントークンを知っているので自由にアクセス出来てしまう
      - https://www.ubsecure.jp/blog/session_fixation

### クッキーインジェクション

- HTTPSで秘匿されたドメインのクッキーに対して、HTTPSではない別のサブドメインから上書きしたり、よりURLの詳細なクッキーを設定することで、元のHTTPSで指定されたドメインのクッキーを無効化し、セッション固定化攻撃の踏み台にする
  - ログイン前後でセッショントークンが変わらなければ、クッキーを攻撃者のものに置き換えることでURLのときと同様のことが出来る
- 2020/3時点では、ChromeとFirefoxで対策が取られている
  - サブドメインからのクッキーの再設定が出来ない
  - 同一ドメインでもsecure付きのクッキーはHTTPから上書きできない

## 14.6 クロスサイトリクエストフォージェリ

https://developer.mozilla.org/ja/docs/Web/Security/Types_of_attacks#%E3%82%AF%E3%83%AD%E3%82%B9%E3%82%B5%E3%82%A4%E3%83%88%E3%83%AA%E3%82%AF%E3%82%A8%E3%82%B9%E3%83%88%E3%83%95%E3%82%A9%E3%83%BC%E3%82%B8%E3%82%A7%E3%83%AA%E3%83%BC_csrf
https://www.ipa.go.jp/security/vuln/websecurity-HTML-1_6.html

対策

- CSRF対策トークン: フォームの生成時にtypeがhiddenなフィールドに、ユーザーごとに一意でかつ予測できないトークンを埋め込み、サーバー側はリクエストを受け取った際にそのトークンを検証する
  - ただし、セッショントークンをこの目的に使ってはいけない
    - 隠しフィールドにCSRF対策トークンを用いるので、javascriptからアクセス出来てしまうため
- クッキーにSameSite属性をつける: リクエストを送信する時のページが同一サイトにない限りはクッキーを送信しなくなる
  - そうすれば認証が通らないのでリクエストが成功しない
- > CSRF トークンと SameSite Cookie の両方を導入する必要があります。これにより、すべてのブラウザーが保護され、SameSite のクッキーでは対応できない場合 (別のサブドメインからの攻撃など) にも保護されます
- ウェブアプリケーションフレームワークが用意している仕組みを使うのが一番安全

## 14.7 クリックジャッキング

https://www.ipa.go.jp/security/vuln/websecurity-HTML-1_9.html

- 2種類あり、どちらもiframeを使用している
  1. 利用者が多いウェブサイト(ウェブサイトA)を透明にして、悪意のあるページの上に重ねる
      - ユーザーには悪意のあるページが見えている
        - そのページのボタンの位置に、ウェブサイトAのログアウトやシェアボタンを重ねる
          - そのボタンをユーザーがクリックしようとすると、実際にはウェブサイトAのアクションが実施されてしまう
  2. ウェブサイトAの上に、悪意のあるページを透明なレイヤーで表示する
      - ユーザーにはウェブサイトAが見えている
        - ユーザーはAに対して操作したつもりが、実際は悪意のあるページに対するアクションが実施されてしまう
- ウェブサイトAでマウス操作で出来ることはクリックジャッキングで実行させることが出来てしまう
- ブラウザは、ページがiframe内で利用されることを防ぐ必要がある
  - Content-Security-Policyヘッダーのframe-ancestorsを使ってブラウザに悪用を防いでもらうことが出来る
    - Content-Security-Policy: frame-ancestors 'none'
      - フレーム内で使われることを拒否する
    - Content-Security-Policy: frame-ancestors 'self'
      - 同一URL以外のフレーム内で使われることを拒否する
  - X-Frame-Optionsでも対策できるが、今はCSPで設定すべき
    - e.g., `X-Frame-Options: SAMEORIGIN`
    - https://developer.mozilla.org/ja/docs/Web/HTTP/Headers/X-Frame-Options
      - > メモ: Content-Security-Policy ヘッダーに frame-ancestors ディレクティブがあり、対応しているブラウザーにおいてこのヘッダーを置き換えています。
    - [CSPを利用してRailsアプリから安全にiframeを制限する](https://product.st.inc/entry/2021/06/24/104436)
- セキュリティ関連のヘッダーを一括確認するウェブサービス
  - https://securityheaders.com/

