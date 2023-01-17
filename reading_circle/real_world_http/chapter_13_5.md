# 13 章 クラウド時代の HTTP：ウェブを強くするさまざまな技術

@myamamoto111

## 13.5 ロードバランサー

- ロードバランサーは、前段でリクエストを受け、背後にある複数のインスタンスにリクエストを振り分けるミドルウェア
  - ALB (AWS Application Load Balancer)
  - 参考 URL
    - [Application Load Balancer とは](https://docs.aws.amazon.com/ja_jp/elasticloadbalancing/latest/application/introduction.html)
    - [AWS の ALB とは？ELB との違いや特長](https://techmania.jp/blog/aws0005/)

### 13.5.1 接続ドレイン

- ロードバランサーへの設定変更を行うとき、サービス停止しないように以下のような制御を行わせることができる
  - 新規のリクエストは新しいバージョンの方に流す
  - 古いバージョンへのリクエストが完了するまで待つ

## 13.6 API ゲートウェイ

- 後段のサービスへのインターフェースとなるサービス（各種クラウドサービスが提供）
  - API の設計支援
  - API のバージョン管理
  - 流量制限
  - API 認証
  - ロギングやモニタリング
  - 参考 URL
    - [Amazon API Gateway](https://aws.amazon.com/jp/api-gateway/)

## 13.7 ヘルスチェック

- 動作しているアプリケーションが正しく動作しているかのチェック機能

### 13.7.1 Liveness Prove

- サービスが起動しているかどうかを確認します
- サーバープロセスが停止した場合、ヘルスチェックのエージェントはエラーを検知できる
  - CPU かメモリを使い尽くしてそれ以上リクエストが処理できないケース
    - Kubernetes ではサーバープロ ps エスの再起動が必要と判断し、Pod を再起動します

### 13.7.2 Readiness Prove

- サービスを提供できるかどうかを確認します
- DB や外部 API など、バックエンドが依存しているサービスも確認できる
  - サーバー自体は起動しているが、一時的にリソースを使い尽くしてレスポンスが返せない状態

    - Kubernetes は Pod を再起動します

- 参考 URL
  - [Kubernetes とは何か？](https://kubernetes.io/ja/docs/concepts/overview/what-is-kubernetes/)
  - [Kubernetes の LivenessProbe, ReadinessProbe, StartupProbe のベストプラクティス](https://zenn.dev/nekoshita/articles/4e838ae224ed56)

## 13.8 バーチャル・プライベート・クラウド（VPC）

- ソフトウェア定義でネットワークを作り出す機能
- クラウドサービスの中でプライベートなネットワーク空間を作ることができる
- セキュリティ
  - 接続経路の管理
  - 外部のインターネットから隠す
  - 同一社内で、責任区分をきっちり分けて運用する
- VPC と外部の接続
  - インターネットゲートウェイ：外部と接続
  - ピアリング：VPC 同士を接続
  - ダイレクトコネクト（インターネットコネクト）：専用線で接続

- VPC を活用することで

  1. DB, ストレージは外部のインターネットには公開しないが、クラウド上のウェブサーバーからのみ接続を許可する
  1. 管理用ウェブサーバーはインターネットには公開しないが、社内ネットワークからのみ接続を許可する
  1. バッチ処理のサーバーなどは外から見えないところで動かす

- 参考 URL
  - [Amazon VPC を「これでもか！」というくらい丁寧に解説](https://qiita.com/c60evaporator/items/2f24d4796202e8b06a77)
  - [GCP Google Cloud](https://cloud.google.com/docs/overview?hl=ja)

## 13.9 マイクロサービスと認証

- マイクロサービスで多段構成になっている場合、Json Web Token（JWT）が一般的に使われる。
  - 5.8.8 JWT(Json Web Token)
  - 負荷分散できる
  - 一方、JWT は情報量が必要以上に多いため、ユーザーの目に触れるとことに置いておくのはセキュリティ上望ましくない
  - BEF(フロント側のサーバーとブラウザ間でランダムな認証トークン)で JWT トークンを発行し、後段のマイクロサービスには JWT を送付するような組み合わせが利用されることもある
  - [トークンベースの認証とは？ 仕様とJWTのメリット、デメリット
](https://www.okta.com/jp/identity-101/what-is-token-based-authentication/)
- クラウドサービス
  - [IAM とは](https://docs.aws.amazon.com/ja_jp/IAM/latest/UserGuide/introduction.html)
  - [GCP（Google Cloud Platform）とは？](https://udemy.benesse.co.jp/development/system/gcp.html)

## 13.10 分散トレーシング

- トレーシングとは、どの処理がどの順番で行われているのか、どのくらいの処理時間がかかっているかをミクロに見ていくことによって、システムの状況を把握しやすくする手法
- **分散トレーシング**とは、そのようなシステムをまたいだ状況を把握しやすくする仕組み
  - 大元は Google の Dapper
    - Twitter : Zipkin
    - Uber : Jaeger
    - GCP : Stackdriver
    - AWS : X-Ray, New Relic APM, Datalog APM
  - Open Tracing プロジェクトの誕生
    - [The OpenTracing project](https://opentracing.io/)
  - Open Sensus (Google)
    - [Open Sensus](https://opencensus.io/)
  - Open Telemetry
    - [OpenTelemetry](https://opentelemetry.io/docs/)
    - [OpenTelemetry とは](https://cloud.google.com/learn/what-is-opentelemetry?hl=ja)

### 13.10.1 トレース情報を子タスクに伝搬する HTTPヘッダー

- 分散トレーシングのキモは「サブシステムをまたいだトレースができる点
- HTTP W3CのTrace Contextという規格が策定中
  - [Trace Context](https://www.w3.org/TR/trace-context/)
  - [Trace Context Level 2 github](https://w3c.github.io/trace-context/)
  - この規格では２つのヘッダを定義
    - traceparent : 分散トレースに必須な情報を含むヘッダー(必須) 
    - tracetate : 分散トレースのライブラリの実現依存の値などを含むKey-Valueの値（オプション）

### 13.10.2 サーバー内部の時間情報をブラウザに伝搬する HTTPヘッダー

- パフォーマンスのチューニングをするとき、スパンを表示して結果を表示するのに人気があるツールは、Google Chromeの開発者ツール
- Server-Timing
  - サーバーの結果をブラウザに返すヘッダー
  
## 13.11 その他の技術要素
- HTTP以外の技術
  - サービスメッシュ
    - 大規模なネットワークを抽象化
  - サービスディスカバリー
    - 内部のサービス間の密結合を減らすための機能のひとつ
  - メッセージキュー
    - 大規模なシステム、非同期なバッチ処理の負荷分散

## 13.12 本章のまとめ

- クラウドサービスを使ってシステム構築をする際の、大規模なシステムで登場するHTTPやその周辺の情報について説明
- クラウドにまつわる変化は大きく、一番変化の著しい分野
