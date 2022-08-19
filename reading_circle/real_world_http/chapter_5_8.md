# 5.8 から

@dameleon

## ウェブサイト間で共通の認証・認可プラットフォーム

- ((ID, Password形式)(MFA)(秘密の質問)(etc)プラットフォーム形式)
- 認証(Authentication, AuthN): 利用者の識別
- 認可(Authorization, AuthZ): 権限の判定
- フェデレーテットログイン(ソーシャルログイン): 外部サービスのIDによるログイン 

## シングルサインオン(SSO)

- プロトコルではなく概念
  - 実際には後段のプロトコルが利用される
- 主に企業向けの、一度ログインしたら各サービスがログインセッションを認証, 認可へ利用する仕組み
- 大まかな方式
  - サービスから認証サーバに対して各自がログインセッションを発行する
    - つまりID, Passwordはそれぞれ入れる必要があるが、同じモノを利用出来る
  - プロキシサーバ方式
    - サービスの前段に配置されたプロキシサーバがログインセッションの管理を行う
  - エージェント方式
    - 各サービスのログイン時にログインセッションの管理をアプリケーション側で行う

## kerberos認証

- LDAP: ユーザ, 組織, サーバなどのリソースを管理するデータベース
  - https://www.okta.com/jp/identity-101/what-is-ldap/
- 仕組み&シーケンス: https://pkiwithadcs.com/basic_knowledge_about_kerberos_authentication/

## SAML

- WEB系技術が前提のSSO
  - Cookie
  - リダイレクト
- 仕組み&シーケンス: https://manual.iij.jp/iid/faq/19644038.html
- 証明書の交換などSPとIdPの事前作業を行えば、直接的にSPとIdPが通信するようなことがない

## OpenID

- ユーザが登録しているウェブサービスのユーザ情報を用いて多サービスにログイン出来る仕組み
- 後発のOpenID Connectに取って代わられている
- 仕組み&シーケンス: https://www.tdk.com/ja/tech-mag/knowledge/096

## OpenSocial

- 認証だけはなく、会員情報, 交友情報等も含むプロトコル
- 仕組み&シーケンス: https://gihyo.jp/dev/serial/01/opensocial/0001

## OAuth

- 認可のためのプロトコル
- 1.0 -> 2.0 -> 2.1と辿る中で、様々な拡張や非推奨が繰り返された
- スコープにより、クライアントがリソースサーバに対してどこまでの情報を得ることが出来るか、を管理できる
- 仕組み&シーケンス: https://qiita.com/TakahikoKawasaki/items/200951e5b5929f840a1f
  - Client Credentials Grant: サーバ向け
  - Authorization Code: Webアプリケーション向け
    - SPA, Native Appの場合は、合わせてPKCEを利用
  - Device Code Grant: キーボードがないようなIoT機器向け

### Proof Key for Code Exchange(PKCE)

- クライアント, サーバの双方がhash等の計算値をお互いに算出し、一致の確認をする
- https://qiita.com/naoya_matsuda/items/992c67b803ff460818ec

## OpenID Connect

- OAuth2.0をベースに、認証でも利用出来る拡張を行ったプロトコル
  - 現在はこれが主流
- 仕組み&フロー: https://qiita.com/TakahikoKawasaki/items/4ee9b55db9f7ef352b47

## JWT

- 認証トークンの形式
  - = IDトークン
- ヘッダ, ペイロード, 署名のデータをbase64エンコードした文字列
- 