# HTTP/1.1のシンタックス: 高速化と安全性を求めた拡張

本章で取り上げるHTTP/1.1のプロトコルシンタックスの変更点
- 通信の高速化
  - Keep-Aliveがデフォルトで有効に
- TLSによる暗号化通信のサポート
- 新メソッドの追加
  - PUTとDELETEが必須メソッドに
  - OPTION, TRACE, CONNECTメソッド追加
- プロトコルのアップグレード
- 名前を使ったバーチャルホストのサポート
- サイズが事前にわからないコンテンツのチャンク転送エンコーディングのサポート
- DATA URIスキーム

## 4.1 Keep-Aliveによる通信の高速化

引用元: https://atmarkit.itmedia.co.jp/ait/articles/1605/11/news030_3.html
> HTTPの基本的な動作では、「リクエストのたびに接続を確立し、1つのレスポンスを返したら接続を切る」ということを繰り返します。
実際のWebページを見ると、そこには文字以外に複数の要素（写真、画像、音楽など）が含まれており、1つの画面を表示するにはこれら全てをサーバから取得する必要があります。そのため、Webページを見るときには通常、HTTPのリクエストを何度か繰り返して、必要な全ての情報をサーバから取得します。
しかしながら、HTTPのリクエストとレスポンスを1つやりとりするたびに、いちいち接続を準備して切断することを繰り返すのは、明らかな無駄です。
そこで、現在最も多く使われているバージョンのHTTP（HTTP 1.1）では、この無駄を省く仕組みが用意されています。具体的には、特に指定がない限り、レスポンスを返した後に接続を維持し、次のリクエストを送るときは、保持している接続をそのまま再利用してリクエストを送ることができるようになっています。接続を切らずに保持しておくこの動作は、「Keep-Alive」と呼ばれます。
https://image.itmedia.co.jp/ait/articles/1605/11/l_tcpip7.jpg

- Keep-Aliveは、HTTPの下のレイヤーであるTCP/IPの通信を効率化する仕組み
  - https://www.itmanage.co.jp/column/tcp-ip-protocol/
- 接続を継続して通信を行うことで、TCP/IPは再接続までの待ち時間が減る
- 通信開始から終わりまでのことをRTT(ラウンドトリップタイム)と呼ぶ
  - TLSではハンドシェイクと呼ばれる通信開始前の情報交換に2RTTぶん時間がかかる
    - https://www.idcf.jp/rentalserver/aossl/basic/ssl-tls/
- Keep-Aliveはクライアントとサーバーの両方が、持続時間を持っており、その短い方で通信の切断が行われる

### 4.1.1 パイプランニング

- 最初のリクエストが完了する前に次のリクエストを送信し、レスポンスを待って次のリクエストを出すまでの待ち時間を排除
- ただクライアントとサーバーの環境によってうまく動作がしなかったり、パフォーマンスがあまり上がらなかった事象も
- リクエストの順序通りレスポンスを返さなければならないということは、レスポンス生成に時間がかかる処理があると、他のレスポンスを阻害してしまう。
  - Head-of-Line Blocking(HOLブロッキング, HOL)と呼ばれている問題

## 4.2 TLS(トランスポート・レイヤー・セキュリティ)

https://www.idcf.jp/rentalserver/aossl/basic/ssl-tls/
> SSL(Secure Socket Layer)は、インターネット上でやりとりされるデータの「盗聴」「改ざん」「なりすまし」を防止するための暗号化プロトコル(通信方法)です。
例えば、ショッピングサイトで買い物をする際には、ご自身の名前や住所、場合によってはクレジットカード情報などを入力します。
こういった重要な情報をインターネット上で通信し送りますが、通信中の情報を悪意のある第三者に盗み見られる恐れがあります。
ですので、この通信内容を暗号化（何が書いてあるか分からない状態にする）して、もし盗み見られても解読が困難な状態にする仕組みがSSLです。
WEBサイトの今見ているページがSSL化されているかどうかは、URLが「http」ではなく「https」になっているかどうかで見分けることができます。

### 4.2.1 ハッシュ関数

ハッシュ関数 h()
入力されたデータ A,B...
算出されたハッシュ値 X,Y...

- h(A) = X
  - 同じアルゴリズムと同じ入力データであればハッシュ値は同じ
- ハッシュ値はアルゴリズムが同じであれば同じ
- h(A) = XのXからAを探すのは困難(弱衝突耐性)
- h(A) = h(B)となる入力データを探すのは困難(強衝突耐性)

### 4.2.2 共通鍵暗号と公開鍵暗号とデジタル署名

- 共通鍵暗号
施錠と解錠が同じ暗号
- 公開鍵暗号
施錠: 公開鍵
解錠: 秘密鍵
- デジタル署名
施錠: 秘密鍵
解錠: 公開鍵

### 4.2.3 鍵交換

- クライアントで共通鍵を生成し、サーバー証明書の公開鍵を使って暗号化して送る方法

- 鍵交換専用のアルゴリズム
DH(ディフィー・ヘルマン)鍵交換アルゴリズム
鍵そのものを交換せず、クライアントとサーバーでそれぞれ鍵の材料を作り、お互いに交換しあって、それぞれの場所で計算して同じ鍵を得る。

### 4.2.4 共通鍵方式と公開鍵方式を使い分ける理由

TLSは、共通鍵方式と公開鍵方式を組み合わせている。
通信ごとに一度だけ使われる共通鍵を作り出し、公開鍵方式を使って通信相手へ厳重に鍵を受けわたし、その後は共通鍵で高速に通信を行うという二段方式。
公開鍵の方が安全性が高いが、鍵を持っていたとしても暗号化と復号化に時間がかかる。

p.118 表4-2参照 暗号化と復号化の速度
RSA(公開鍵)は、良い性能のPCでもPHS程度の速度。
AES(共通鍵)では、15000倍に

### 4.2.5 TLSの通信手順

#### TLSの通信は主に3つ
- ハンドシェイクプロトコル
  通信を確立するステップ
- レコードプロトコル
  通信時のステップ
- 再接続時のハンドシェイク
Session Ticketという仕組みを使う。高速。

#### サーバーの信頼性を確認

- 公開鍵基盤(Public Key Infrastructure)
サーバーの信頼性を保証する仕組みは、公開鍵を保証する仕組みでもある。

1. ブラウザはサーバーから、SSLサーバー証明書を取得
2. SSLサーバー証明書を認証局と検証し、サーバーがその認証局に承認されたものであるかを確認する

#### 鍵交換と通信の開始

- 公開鍵を使う方法
サーバー証明書に添付されている公開鍵を使って通信用の共通鍵を暗号化し、その鍵をサーバーに送付。サーバーは証明書の公開鍵に対応した秘密鍵を持っているため、送付されたデータを復号して共通鍵を取り出す。

- 鍵交換専用アルゴリズムを使う場合
どちらかが安全な鍵を使って渡すのではなく、鍵の種をクライアントとサーバーで一つずつ作る。その種をお互いに交換して、計算した結果が秘密鍵になる。鍵の種をお互いに交換する際に公開鍵暗号が併用。


鍵交換専用アルゴリズムの方が、Forward Secrecy(前方秘匿性)に優れている。公開鍵の場合、秘密鍵が漏れると後から共通鍵が解読され通信内容からも解読される。

#### 通信

機密性と完全性(改竄を防ぐ)ために暗号化して通信。使用されるアルゴは、共通鍵暗号方式。

#### 通信の高速化

通信の時間において、電気信号がサーバーに届いてレスポンスが返ってくるまでの時間はとてつもなく大きい。そのため往復時間(回数)を減らすことが大切。
- Keep Alive
通信を切断しないため、RTTは1に
- セッション再開機能
TLS1.2にあり、最初のハンドシェイクでは以前使用していたセッションIDを送ることでその後の鍵交換を省略しセッションを再開。
- 事前共有鍵(PSK)
TLS1.3には、PSKの共有により0-RTTで最初のリクエストから情報を送信可能に