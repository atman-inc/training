#　ソフトウェアテスト

学習者：m.sasaki

##　まとめ

### なぜテストをするのか
- テストの必要性（JSTQB FL 1.1）

    - ソフトウェアはBtoBからBtoCまで、会社を構成する要素として必須
        - ソフトウェアが期待通りに動かない
            - 経済的な損失
            - 時間の浪費
            - 信用の失墜
            - 傷害や死亡事故
        - ソフトウェアテストは、ソフトウェアの品質を評価し、運用環境でのソフトウェアの故障発生のリスクを低減する1つの手段

<br>

- 用語
    - JSTQB
        - 日本におけるソフトウェアテスト技術者資格認定の運営組織
        - 各国のテスト技術者認定組織が参加しているISTQBの加盟組織

---

### 何をテストするのか
- テストは仕様検証のためではない
  - 指定している要件をシステムが満たすかを確認する
  - 加えて妥当性の確認も行う
    - 正しく作っているか（検証）
    - 正しいものを作っているか（妥当性の確認）

<br>

#### 感じた、考えたこと

- 正しく作っているか
  - 作っている途中途中で確認を行い、大きな戻りはしないように考慮したい

<br>

- 正しいものを作っているか
  - これは実装前と実装中、実装後など出来たら細かく確認したい
  - 完成してからの手戻りなど発生しかねないと感じた

#### 併せてステークホルダーへの情報提供も実施する

<br>

- TestingとChecking
  - Testing
    - プロダクトの探索と実験を通じてプロダクトを学習することによってプロダクトを評価するプロセス
    - 問いを立てたり、研究、モデル化、観察、推論することが含まれる

<br>

  - Checking
    - プロダクトのある側面に対して、アルゴリズムに基づく決定のルールを適用することで、評価を行うプロセス

---

### いつテストをするのか

<br>

#### テストは開発ライフサイクルのいつでもできる

<br>

- テストとは
  - テスト実行のことだけを指すわけではない
  - 実装前に行うテストもある（欠陥の作り込みの防止）
  - テストは様々な活動を含む
    - 計画
      - テストの目的とアプローチを決める
    - モニタリングとコントロール
      - テスト計画の内容と実際の進捗を継続的に比較する
    - 分析
      - テストのベースを分析し、「何をテストするか」を決定
      - 例：
        - 要件、仕様、ユーザーストーリー、ユースケース、etc...
        - 設計の情報、実装の情報、アーキテクチャ、etc...
        - 想定されるリスク
      - テスト設計技法(※1)を使用し見落としを防ぐ
    - 設計
      - テスト分析の結果を、ハイレベルのテストケースに落とし込む
        - テスト分析：何をテストするのか
        - テスト設計：それをどうテストするのか
      - 様々なテスト設計技法を使用する
    - 実装
      - テストを実行可能にする
        - テスト手順の作成（テストケースの具体化）
        - 優先度の割当
        - テスト環境の用意
        - テストデータの用意
    - 実行
      - スケジュールに従ってテストを実行
        - 不具合があれば、BTS（Bug Tracking System）に登録
    - 完了
      - 完了したテスト活動のデータを集め、まとめる
      - テストケースやテスト環境の整理・保管

<br>

  - 順番に行うこともあれば同時にいくつかを組み合わせて行うこともある
    - プロジェクトやプロダクトの状況に合わせる

<br>

- ※1・・・テスト設計技法
  - 振る舞いベースの技法（ブラックボックステスト）
    - 同値分割、境界値分析、デシジョンテーブルテスト、etc...
  - 内部構造ベースの技法（ホワイトボックステスト）
    - ステートメントテスト、デシジョンテスト
  - 経験ベースの技法
    - エラー推測、探索的テスト、チェックリストベーステスト

---

### スクラムチームの中でのテスト例

<br>

- スクラム導入前後
  - スクラム導入前
    - 開発フェーズとテストフェーズが明確に分かれている
  - スクラム導入後
    - リリース可能な部分（インクリメント）を作る小さなサイクル（スプリント）を繰り返す
    - スプリント内で設計からテストまでやる（チームもある）

- スクラムイベントとテスト
  - スクラムはいくつかのミーティングを定義
    - このことを「スクラムイベント」と言う

- リファインメント
  - テストに関する観点からバックログを見る（テスト分析）
    - ユーザーが実際に行う操作
    - テストしやすいか
    - 異常系、例外はなにか

- スプリントプランニング
  - 何を作るかの認識を合わせながら、テスト設計を簡単に行う（分析、設計）
    - 実装に関する議論を聞きながら、テストすべき箇所、テストしなくて良い箇所を洗い出す
    - テストの観点を出してみる（何をテストするか）
    - テストケースの型を考える（どうテストするか）

- スプリント内
  - 実装状況に応じてテスト設計をアップデート（テスト設計）
  - テスト設計に基づきテストケースを作成（テスト実装）
    - 手動テストケースの作成
    - 自動テストの作成
  - 実装が完了したらテスト開始（実行）
    - 不具合が見つかればすぐにフィードバック