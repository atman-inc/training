# HTTP/2、HTTP/3のシンタックス:プロトコルの再定義

## 7.2 HTTP/2

### 7.2.4 HTTP/2のアプリケーション層

![](https://www.sbbit.jp/article/image/12099/l_bit202006151641249159.jpg)

- メソッドとパス、ヘッダー、ボディ、ステータスの基本要素があるのはHTTP/1.0と同じ
  - 違い: 以下を疑似ヘッダーフィールド化してヘッダーの中に組み込む
    - メソッドとパス、ステータス、プロトコルバージョン(ステータス行に含まれていた)
    - よってヘッダーとボディしかない
- HTTP/2はバイナリ化されている
  - 処理を並列化しやすい
    - [Why is HTTP/2 binary?](https://http2.github.io/faq/#why-is-http2-binary)
    - レスポンスの先頭にフレームサイズが入っている
      - TCPソケットのレイヤーで、データをフレーム単位に簡単に切り替えられる

### 7.2.5 フローコントロール

- トランスポート層に近いものを内部で持っているのがHTTP/2の特徴の一つ
  - フローコントロールもTCPソケットで行っているのとほぼ同じ機能を実装している
    - OSスレッドとグリーンスレッドの関係に似ている
      - グリーンスレッドの例: goroutine
        - rubyは1.9からネイティブスレッド
- フローコントロール: ストリームを効率よく流すために用いられる通信量の制御処理
  - 速い方の危機が遅い方に、大量のパケットを送りつけて処理できなくなるような事態を防ぐのが目的
  - 受信側が、バッファに余裕が出ると、余裕が出来た分のバッファサイズをWINDOW_UPDATEフレームで送信側に返す
  - SETTINGSフレームで、初期ウィンドウサイズ、最大の並列ストリーム数、最大フレームサイズ、最大ヘッダーリストサイズなどをチューニング可能

### 7.2.6 サーバープッシュ

- 送信リクエストが来る前から事前にキャッシュに入れておいて、クライアントがそのファイルをリクエストしたら送信する
- [サーバープッシュを使う](https://knowledge.sakura.ad.jp/7735/#i)
- Chrome 106からはデフォルトで無効化されているとのこと
  - [Removing HTTP/2 Server Push from Chrome](https://developer.chrome.com/blog/removing-push/)
    - > only 1.25% of HTTP/2 sites making use of this feature.

### 7.2.7 HTTP/2とプリロードによるリソース取得の最適化

- リソースヒント: Linkヘッダー、linkタグの`dns-prefetch`, `preconnect`, `prefetch`, `prerender` link relation typesの定義
  - https://www.w3.org/TR/resource-hints/
    - > A resource hint link is a dns-prefetch, preconnect, prefetch, or prerender relationship that is used to indicate an origin or resource that should be connected to, or fetched, by the user agent.
- プリロード: ファイルが必要なことを事前に知らせる方法
  - Linkヘッダー化、linkタグを使って実現
  - 別のドメインのリソースにも使用可能
  - 利用用途: ウェブフォント

### 7.2.8 HPACKによるヘッダーの圧縮

- 圧縮アルゴリズムでは通常、データ圧縮時に、辞書と辞書のキーの配列を作成する
  - HTTPヘッダーは内容がある程度決まっているため、HPACKでは定義済み辞書を持っている(静的テーブル)
    - ヘッダー名とヘッダー値の組をテーブルに持っている

![](https://upload.wikimedia.org/wikipedia/commons/thumb/3/30/HTTP_2_Header_Compression_HPACK.svg/1280px-HTTP_2_Header_Compression_HPACK.svg.png)

https://syucream.github.io/hpack-spec-ja/rfc7541-ja.html#string.literal.representation

## 7.3 HTTP/3

QUICがRFC化される中でHTTP/3となっていった

- TCP:
  - 3-WAY-Handshakeでコネクションを確立する
    - https://hirotanoblog.com/tcp-sequence/2044/
  - データ転送時の順序を保証: 受信側でシーケンス番号を使って並べ替えを行う
    - https://hirotanoblog.com/tcp-sequence/2044/
  - 喪失パケットの再送: 確認応答のないセグメントは再送する
    - https://www.gatevidyalay.com/tcp-retransmission-tcp-computer-networks/  
- UDP
  - 音声や動画などパケットロスがあっても大きな問題がないものに向いている

![](https://www.gatevidyalay.com/wp-content/uploads/2018/09/Retransmission-after-receiving-3-duplicate-acknowledgements-TCP-Retransmission.png)

### 7.3.1 QUIC

- Googleが開発
- UDPソケット上にQUICというプロトコルを用意
  - TCPが行っている内容の多くを自前で実装
    - e.g., パケットがロストした時の再送処理、輻輳したときの制御
- HTTPS通信では、TCPのハンドシェイクを行った後、別途TLSのハンドシェイクが必要
  - QUICKでは統合していることで、通信を減らしている
- HTTP/2で行っていた処理とTCPで行っていた処理で重複しているものを簡略化している

### 7.3.2 HTTP/3への道

- 2013年: GoogleがQUICを発表
- 2015年: RFC化のための最初の提案を実施
  - Google版QUIC: gQUIC
  - IETF版QUIC: iQUIC
  - HTTP over QUICK: HTTP/3
- 2021年5月: IEFFがQUICプロトコルををRFC 9000として勧告
  - https://www.publickey1.jp/blog/21/http3quicietfrfc_9000.html
- 2022年6月: IETFがHTTP/3をRFC 9114として勧告
  - https://xtech.nikkei.com/atcl/nxt/news/18/13024/

### 7.3.3 HTTP/3のレイヤー

- QUICトランスポート: ストリーム制御などのTCPの上位互換のレイヤーとTLS1.3
  - TCPとTLSのレイヤーの置き換え
    - HTTP/2と異なり、TLSを使わないプロトコルが定義されていない
  - ストリーム内の順序の保証をするが、ストリーム間の保証はしない
- HTTP over QUIC: HTTP/2の機能から、QUICトランスポートと重複する機能を落としてシンプルにして、HPACKをQPACKに置き換えたもの

![](https://blog.redbox.ne.jp/images/quic-osi-layer.png)
