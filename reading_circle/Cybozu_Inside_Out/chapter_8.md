# Chrome Developer Toolsの使い方

学習者：m.sasaki

### Chrome Developer Toolsについて
- Chrome Developer Tools
  - Google Chromeブラウザに組み込まれた開発者向けツール
  - Web開発を行う上で便利な機能がたくさんある
  - 他のブラウザにも同様にDeveloper Toolsは存在する

- 使い方
  - 開きたいブラウザ画面で右クリック　→　「検証」を選択
  - ショットカットキー
    - Mac:`Command + Option + J`、Windows:`Control + Shift + J`・・・Elementパネル
    - Mac:`Command + Option + C`、Windows:`Control + Shift + C`・・・Consoleパネル
    - Mac:`Command + Option + I`、Windows:`Control + Shift + I`・・・最後に開いたパネル


### Chrome Developer ToolsのPanel
- Elementsパネル
  - 要素の確認、変更、削除、追加などが行える
    - 要素がどのような状態か確認
    - 編集や要素の追加をすると、見た目にどのような変化があるか確認
    - HoverやFocusの状態を作成
    - Capture node screenshotで要素のスクショを撮影
    - CSSの確認
    - CSSの変更と追加で変化の確認
    - Computedタグで適用されているCSSを確認
    - アクセシビリティの確認
    - Flexbox、Grid Layoutを可視化できる
    - 要素にブレイクポイントを設定できる

<br>

- Consoleパネル
  - Javascriptを実行可能
  - console.logの出力確認
  - ライブラリなどのエラー表示
  - 開発中のデバックに使用
  - `$0`で現在フォーカス中の要素を取得
  - `copy()`の引数に渡したものをクリップボードに反映
  - `monitorEvents(window, "click")`でクリックした箇所の情報を取得
  - Live Expressions（目のアイコン）で指定した値の監視

<br>

- Lighthouseパネル
  - LighthouseはChromeが開発しているOSS
  - [Lighthouse](https://github.com/GoogleChrome/lighthouse)
  - Developer Toolsに組み込まれた
  - Generate reportをクリックすると計算開始
  - 各項目の点数と課題点が表示される
  - 表示されている課題を解決すれば品質向上が見込める

<br>

- Sourceパネル
  - 使用しているファイルの中身を確認
  - ブレークポイントの設定で処理を追うことが可能
  - ソースコードに`debugger`と指定すると起動可能に

<br>

- Networkパネル
  - リソースのアップロード・ダウンロードを可視化
  - リソースのサイズやリクエストにかかった時間などを確認
  - ファイルのフィルタリング検索
  - ネットワークの状態を変更
  - Preserve logにチェックを入れるとページ遷移してもログ確認が可能
  - Disable cacheにチェックでcacheがない状態を確認可能


### その他便利な機能

- スマホ画面のエミュレート
- 画面のスクリーンショット
- etc...