# 8章 HTTP/2のセマンティクス：新しいユースケース

@dameleon

## 8.4 QRコード

- 開発のモチベーション
  - 読み取りの高速化
  - 大容量データ
  - 歪み, 欠けへの復元性
- モード
  - REF: https://codezine.jp/article/detail/8592?p=2
  - 数字モード
  - 日本語モード
  - 英字記号モード
  - バイナリモード


### 8.4.1 QRコードとスキーム

- スキームに関する統一規格はないがGoogleのドキュメントがデファクト
  - REF: https://github.com/zxing/zxing/wiki/Barcode-Contents
- examples
  - URL
  - Email address
  - Tel, SMS, MMS, Facetime
  - Wifi
  - vCard, iCalendar
  - TOTP secret key
  - 他にも、特定アプリケーション向けのスキーマも存在する


## 8.5 AMP(Accelerated Mobile Pages)

- 元々はWebサイト閲覧のパフォーマンスのためにGoogleが仕様を策定したもの
  - 昨今のコンテンツのリッチ化に伴い、描画ブロックなどが発生することを解消するため
  - 日本ではSEO観点で利用されることが多い
- 全てのサイト向けではない、ということ
  - 静的Webコンテンツ(ex. ニュース, レシピなど)
- サイト単位ではなく、個別の記事単位でのAMP化も可能
- リスト 8-12, 8-13の補足
  - JSON-LDでのタグ記述
  - AMP専用のカスタムタグでのメディアコンテンツの表示
    - この際にはカスタムタグ用のJSが必要になる
- `<link rel>`タグにより、通常サイトとAMPサイトの相互リンクを表す
- 仕組み
  - AMP化されたサイトは、GoogleのCDNにコンテンツキャッシュがなされ、配信されるような仕組み
  - タグの最適化、インライン化
  - 不要なコンテンツの削除
- AMPのサイトではなくとも、AMPのコンポーネントを利用することも可能


## 8.6 モバイルアプリケーションにより多様化するブラウズ環境

- モバイルデバイスの発展に伴い、HTTPの利用方法も多様化している
  - WebView
  - Push Notification
  - etc
- URLは、schemaも多様化し、モバイルデバイス内でアプリへのリンクを張るようなDeepLinkという仕組みも整備された
  - アプリ間でのセッション情報の渡し合いなども可能になる -> native appでのOAuth2等


### 8.6.1 iOSのDeepLink

- Universal Links
  - ウェブサイトオーナ向けの、自社アプリに対する連携を行うための仕組み
  - 自社のアプリのインストール用のリンクの設置など
    - REF: https://mobilelaby.net/images/2022/08/safari-universal-link-instagram.jpg
- カスタムスキーム
  - `line://`のような、アプリ専用のschemeを用意することで、インストールされたアプリを開くことが可能になる


### 8.6.2 AndroidのDeepLink

- intentフィルタ
  - インストール済みのアプリが対応しているとき、アプリの起動が可能になる
- App Indexing
  - Googleの検索結果からアプリを直接起動することが可能になる
