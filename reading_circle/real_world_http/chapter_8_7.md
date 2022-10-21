# 8章 HTTP/2のセマンティクス：新しいユースケース

@m.sasaki

## 8.7 HTTPライブストリーミング(HLS)による動画のストリーミング再生

- HLSについて
  - Appleが2009年に提唱した動画のストリーミング再生の仕組み
  - Apple社内で改良が続けられ、2017年にRFC8216として標準化された
- 標準対応ブラウザ
  - モバイル
    - Safari
    - Chrome
  - デスクトップ
    - Safari
    - Edge
- 概要
  - 動画ファイルを細かく分割して少しづつHTTP通信でダウンロードしながら再生する方式
  - HTTPを使用するため、一般的なWebサーバで使用可能
    - 例：[[CentOS7.6][nginx][HLS]ライブストリーミングサーバ構築](https://trans-it.net/centos7-ngin-livestreaming/)
  - 対応していないブラウザでもMedia Source Extensions(MSE)という仕組みで再生可能
  - VODとライブ配信に対応している
    - ライブ配信
      - ほぼリアルタイム中継等のストリーミング配信に使用できる
  - 通信回線のビットレートに合わせて適切な解像度の動画が選択できる
    - 細切れにした動画ファイルで複数のビットレートの動画を準備する必要がある
    - 通信回線の状態を判断してダウンロードする動画のビットレートを変えることで可能になる機能
  - 内部はとてもシンプル
- Media Source Extensions(MSE)
  - HTTPダウンロードを利用してストリーミング再生するために作られたHTML5用のJavascriptAPI
  - [W3Cによって標準化](https://www.w3.org/TR/media-source-2/)されている
  - 参考サイト
    - [Media Source Extensions](https://qiita.com/tomoyukilabs/items/57ba8a982ab372611669)
    - [Media Source Extensions API](https://developer.mozilla.org/ja/docs/Web/API/Media_Source_Extensions_API)
- VOD
  - ビデオ・オン・デマンドのこと
  - 視聴者が観たい時に様々な映像コンテンツを視聴することができるサービスのこと
  - 参考サイト
    - [ビデオ・オン・デマンド-Wikipedia](https://ja.wikipedia.org/wiki/%E3%83%93%E3%83%87%E3%82%AA%E3%83%BB%E3%82%AA%E3%83%B3%E3%83%BB%E3%83%87%E3%83%9E%E3%83%B3%E3%83%89)

### 8.7.1 HLSのビデオタグ

- HLSを使うHTMLの紹介
  - WWDC 2016でHLSの紹介をしていたセッションページでは`<video>`タグを使用している
  - ブラウザ毎に使用できる動画フォーマットが異なる為、`<video>`タグ内で複数ソースを記述できる
- サンプルソースで使われているファイルは以下の2つ
  - HLSのマニフェストファイル（インデックスファイルとも呼ばれている）
  - 動画ファイル
  - 参考サイト
    - [HLS（HTTP Live Streaming）で動画を配信・再生してみよう](https://www.yaz.co.jp/tec-blog/web%E3%82%B5%E3%83%BC%E3%83%93%E3%82%B9/212)

```html
<video class="video center" controls="" autoplay data-id="1251">
  <source src="http://devstreaming.apple.com/videos/..(省略)../hls_vod_mvp.m3u8" /> <!-- HLSのマニフェストファイル -->
  <source src="/videos/images/ogg_bumper_no_tv.ogv" type='video/ogg'> <!-- 動画ファイル -->
</video>
```

### 8.7.2 マスターの.m3u8ファイル

- .m3u8ファイル
  - M3U形式(.M3Uファイル)の文字コードをUTF-8としたもの
    - M3Uとは
      - 動画や音声などを連続再生するためにファイルのURLを列挙している
      - マルチメディアプレイリストのファイルフォーマット
      - 正式な仕様は存在していなく、対応状況はまちまち
        - 参考
          - [M3U-Wikipedia](https://ja.wikipedia.org/wiki/M3U)
- サンプルコードは書籍参照(p274)
  - サンプルコードの解説
    - `#EXT-X-MEDIA:TYPE=SUBTITLES~`から始まるリスト
      - `LANGUAGE="English",URI="subtitles/eng/prog_index.m3u8"`
        - 字幕情報の`.m3u8`ファイルのパスを指定している
    - `EXT-X-STREAM-INF~`
      - 推奨の帯域幅と対応する.m3u8ファイルのリスト
      - `BANDWIDTH`: 帯域幅
      - `CODECS`: 映像と音声のコーデック
        - コーデック
          - 符号化方式を使ってデータのエンコード/デコードを双方向にできる装置やソフトウェアのこと
          - 参考サイト
            - [コーデック-Wikipedia](https://ja.wikipedia.org/wiki/%E3%82%B3%E3%83%BC%E3%83%87%E3%83%83%E3%82%AF)
- .m3u8ファイルが他の.m3u8ファイルを参照することもある
  - 今回のサンプルでは字幕用の.m3u8ファイルを参照している
- クライアントは最初に記述されたインデックスを使用
  - その後は回線状況の余裕を見て使用するインデックスを切り替える

### 8.7.3 字幕の.m3u8ファイル

- 字幕ファイル
  - 60秒毎に分割されたファイル
  - 字幕ファイルは`~.webvtt`というファイル
    - WEBVTT形式
      - 字幕用のスクリプト
      - 参考サイト
        - [Web ビデオテキストトラックフォーマット (WebVTT)](https://developer.mozilla.org/ja/docs/Web/API/WebVTT_API)
- 字幕の.m3u8ファイルは上記の字幕ファイルを束ねている

### 8.7.4 動画ファイル

- 代替インデックス
  - マスターインデックスの.m3u8ファイルから参照されるインデックスファイル
  - 動画ファイルが列挙されているファイル
  - 構成は字幕の.m3u8ファイルとほぼ同じ
- サンプルコードは書籍参照(p276)
  - `.ts`拡張子(TSファイル)
    - 動画データを記録するためのファイル形式の一つ
    - TransportStream(トランスポートストリーム)の略
    - サンプルだと12.012秒毎の動画ファイルになっている
  - 設定値
    - #EXT-X-PLAYLIST-TYPE:
      - ライブ配信
        - `EVENT`
      - VOD(ビデオ・オン・デマンド)
        - `VOD`
    - #EXT-X-ENDLIST:
      - ライブ配信を行った後にVODとして使用する場合にファイル末尾に付与する
      - この記述を付与する前に`#EXT-X-PLAYLIST-TYPE`の値を`VOD`に設定する必要がある
      - この指定は.m3u8ファイル内で動画リストの最後であることを示している
        - [HLS（HTTP Live Streaming）で動画を配信・再生してみよう](https://www.yaz.co.jp/tec-blog/web%E3%82%B5%E3%83%BC%E3%83%93%E3%82%B9/212)
        - [HLS で streaming](http://zairyo.susi.oita-u.ac.jp/wordpress/?p=5589)

### 8.7.5 HLSのメリットとデメリット

- HLSメリット
  - サーバとクライアント間の通信にHTTPを利用している点
    - 通常のWebサーバで利用できる
- HLSデメリット
  - プログレッシブダウンロード方式になっている
    - プログレッシブダウンロード方式
      - ダウンロードをしながら動画や音声を再生する仕組み
      - チャンク毎にダウンロードが完了していないと再生が始められない為遅延が発生する
  - サポートされない環境が多い
    - 例:
      - Chromeのデスクトップ版ではネイティブでサポートしていない
      - サードパーティ製のプラグインを使用する必要がある
        - 例：
          - [Native HLS Playback](https://chrome.google.com/webstore/detail/native-hls-playback/emnphkkblegpebimobpbekeedfgemhof?hl=ja)
  - コンテンツ保護(DRM)に制約がある

### 8.7.6 HLS前後の歴史

- ブラウザストリーミングが登場する前
  - 専用のプレイヤーアプリケーションを使ったストリーミングが使われていた
    - 例：
      - Windows Media Player　（Windows
      - QuickTime　（Apple
- ブラウザ上でのストリーミング
  - MacromediaのFlashプレイヤーがサポートしていたRTMP(リアルタイム・メッセージ・プロトコル)が広く普及
    - MacromediaはFlashを開発していた会社でAdobeに買収された
  - RTMPについて
    - HTTPとは異なる方式でリアルタイム性を強化したプロトコル
    - 2007年にはPCブラウザのストリーミング視聴環境のデファクトスタンダードになる
    - 参考サイト
      - [Real Time Messaging Protocol-Wikipedia](https://ja.wikipedia.org/wiki/Real_Time_Messaging_Protocol)
  - MicrosoftもブラウザプラグインのSilverlightで動画の再生をサポート
    - 各ブラウザで使えるようにしていた
  - AppleによるHLSリリース後
    - 各社がHTTPプログレッシブダウンロードを提供し始める

## 8.8 MPEG-DASHによる動画ストリーミング再生

- MPEG-DASH
  - HLSを大幅に拡張したもの
  - HTTPによるプログレッシブダウンロードを核としたストリーミング方式
    - video.jsというライブラリが提供されている
      - このライブラリを使用することでブラウザ上でMPEG-DASHが利用できる

### 8.8.1 MPEG-DASHとHLSの再生方法の違い

- 再生手法
  - HLS
    - ブラウザ自身にHLSの.m3u8ファイルを解釈して再生するシステムが組み込まれていた
  - MPEG-DASH
    - ブラウザ自身がプロトコルを直接解釈しない
    - データ解析はJavascriptで行う
    - 動画再生はHTML5 Media Source Extensionsを利用する
      - HTML5 Media Source Extensions
        - ブラウザのコーデックをJavascriptから扱うAPI
- 以下別途調査した内容
  - エンコードフォーマット
    - HLS
      - H.264またはH.265の仕様が必須
    - MPEG-DASH
      - 任意のエンコード標準を使用可能
  - デバイスサポート
    - HLS
      - Appleデバイスでサポートされている唯一の形式
    - MPEG-DASH
      - Apple製品だと再生ができない
  - セグメントの長さ
    - HLS
      - デフォルトの長さが6秒
    - MPEG-DASH
      - 通常2〜10秒
      - 最適な長さは2~4秒
  - 参考サイト
    - [MPEG-DASHとは？| HLSとDASH](https://www.cloudflare.com/ja-jp/learning/video/what-is-mpeg-dash/)
- 現在では両者の差はほとんどなくなったと見て問題ない
  - HLSも非対応ブラウザで再生できる動画プレイヤーが開発されている
  - さまざまなプログラムやライブラリがHLSにもMPEG-DASHにも対応している
- 今後の主流
  - 共通で使えるFragmented MPEG-4が主流になる
    - Fragmented MPEG-4
      - 細かく分割されたMP4形式のファイル
      - 参考サイト
        - [ストリーミングとは何か、、、調べてみた経験より](https://randd-inc.com/2021/06/05/%E3%82%B9%E3%83%88%E3%83%AA%E3%83%BC%E3%83%9F%E3%83%B3%E3%82%B0%E3%81%AE%E5%9F%BA%E7%A4%8E%E3%82%92%E8%AA%BF%E3%81%B9%E3%81%9F%E4%BB%B6%E3%81%AB%E3%81%A4%E3%81%84%E3%81%A6/)

### 8.8.2 Media Presentation Description(MPD)ファイルの構造

- 書籍にてサンプルを交えて解説(p279~)

## 8.9 CMAF(Common Media Application Format)

- CMAF(シーマフ)
  - HTTPストリーミングの新しい規格
    - AppleとMicrosoftで共通で策定
  - 2018年にISO国際標準化された
  - HLSやMPEG-DASHと同様にFragmented MPEG-4(fMP4)を使うフォーマット
  - 低遅延HTTPストリーミングを実現するフォーマットとして注目されている
  - 参考サイト
    - [動画配信キーワード集CMAF](https://www.smartstream.ne.jp/useful/keyword/common_media_application_format.html#:~:text=CMAF%E3%81%A8%E3%81%AF%E3%80%81HTTP%E3%82%B9%E3%83%88%E3%83%AA%E3%83%BC%E3%83%9F%E3%83%B3%E3%82%B0,%E6%B3%A8%E7%9B%AE%E3%82%92%E9%9B%86%E3%82%81%E3%81%A6%E3%81%84%E3%81%BE%E3%81%99%E3%80%82)
    - [Internet Infrastructure Review（IIR）Vol.33](https://www.iij.ad.jp/dev/report/iir/033/02.html)