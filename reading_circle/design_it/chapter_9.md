
# 9章 アーキテクチャデザインスタジオを開く

担当: @koushisa

## まとめ

_グループの総合的な知恵と経験を活かすためのプラクティスを学ぶ_
  
### デザインスタジオを計画する

- グループが多くのアイデアを出せるように、探求作業に厳しい時間的制約を課す
  - これを実現するために
    - 探求段階でアイデアの妥当性までは見ない
    - アイデアをブラッシュアップするまでの流れを各ステージに分ける
    - 進め方を合意する
- 3つのF
  - 早く(fast)、効果的で(effective)、楽しいもの(fun)
  - 意欲を増幅して各アクティビティのスピードや効果を増強する
- 主催者はこれらを考慮して実用的なアイディアが出るワークショップを計画する

### ワークショップのステージ

- ワークショップでは、主に3種類のアイデアが生まれる
  - **作るもののアイデア**
    - 次に作るもののアイデア
    - 次にやること: モデルやプロトタイプを作成して詳細を具体化する
  - **もっと探求が必要なアイデア**
    - 広い前提から成り立っているものや重要な情報を欠いているアイデア
    - 次にやること: 実験や研究を行い、アイデアを深堀りする 
  - **新しい疑問としてのアイデア**
    - 解決しようとしている問題への新しい疑問が生まれた
      - コーディングする前に気づけたのはむしろいい事
    - 次にやること: 疑問をステークホルダと話し、問題に対する理解を深める

- 典型的なワークショップは数時間から1日あれば十分
  - 時間をかけたからと言ってより効果的なわけではない
- より時間をかける場合でも、全てのワークショップは同じ基本構造に従う

### ワークショップの構造

- **準備**
  - 招待する人を決める(後述)
  - 品質特性、ASR(アーキテクチャ上重要な要求)を洗練させる
  - 問題とコンテキストについて十分に理解しておくこと
    - ステークホルダと話す、調査する、ビジネス目標を定義することに務める...etc
  - 有効なワークショップの目標を1つか2つ定義し、参加者へ議論の方向性を示す
    - 参加者全員に適切なコンテキストが共有できる状態を目指す
    - P.140(表9-1)ワークショップの目標例 参照

- **キックオフ**
  - 参加者が適切なコンテキストを持っていることを確認する
    - グループの背景知識はどれくらいあるのか
    - コンテキストに対し理解の薄い参加者がいれば、より説明に時間をかける
  - ワークショップの目標を説明する
    - ワークショップ全体を通して、グループは同じ目標に焦点をあわせ続ける

- **作成 - 共有 - 批評**
  - ワークショップの大部分は、このサイクルに費やす
  - 素早い思考の発散と収束を促進するプロセス
  - **作成**
    - 参加者を1人か少数のグループに分ける
      - アイデアをスケッチしたりモデル化する
        - 正確さはいらないのでアナログで行うのがおすすめ
        - アイデア > 完成度 
    - 時間的制約
      - 5〜7分程度
  - **共有**
    - 作ったアイデアを大きなグループと共有する
    - 要点だけを示し、完全に説明することは避ける
    - 参加者は聴くことを意識し、質問は行わない
    - 時間的制約
      - 3〜5分程度
  - **批評**
    - 共有したアイデアに対しフィードバックを行う
    - ワークショップの目標に関する設計のメリットに焦点を当てる
    - 具体的に、事実に焦点をあわせることを奨励する
    - P.143(表9-2)批評ですべきこと、避けるべきこと 参照
    - 時間的制約
      - 明確には定義しない
      - 後述の繰り返しを考慮した時間設定を行う

- **繰り返す**
  - ワークショップの目標について、作成 - 共有 - 批評のサイクルを少なくとも3回繰り返す
  - 繰り返しの中で各メンバーの特性やこれまでのステージを振り返り、ルールを微調整していく

- **次にやることを決定する**

  - 議論の中で出てきたテーマについてまとめる
  - 次に具体的にやるべきことを決定する
  - 次の段階へと確実に進められるように後片付けを行う
  - 出てきたアイデアを議事録に残す
  - 写真を撮る

## 適切なデザインアクティビティ

- システム全体について考えるときは、アーキテクチャに焦点を当てた効果的なアクティビティを選択するようにする
- P.145(表9-3)ワークショップの進行例 参照

## 適切な参加者を招く

- 主催者は、ワークショップに招待する人数と多様性のバランスを取る必要がある

- **適切な大きさのワークショップ**
  - グループが7人以上になると管理が難しい
    - コミュニケーションに時間がかかる
    - スケジュールの調整ができない
  - 1人のファシリテーターにつき、3〜4つのサブグループが限界
  - 最大でも約10人程度
  - 筆者の経験上、アーキテクチャ設計のワークショップでは3〜5人のグループが最適
- **多様な聴衆を招待する**
  - 異なる背景や別の視点を持っている参加者がいるとひらめきを生む機会を高める事ができる
    - 重要なステークホルダ
    - テスター
    - プロダクトマネージャー
    - サーバ、フロント
  - 誰もが独自の視点を持っており、より幅広く設計を探求することに役立つ
- **グループの力を賢く利用する**
  - 集団思考に注意
    - 個性を失い、調和と合意について心配する現象
  - P.147(表9-4)グループがうまく連携しているかどうか 参照

## グループを管理する## 

- 参加者の意欲をより引き出すような対話を行う
- 積極的にグループに関わり、質問や共感を通してワークショップを動かし続ける
- (以下はP149〜153までで重要そうなポイントを抜粋)
- **ワークショップに十分な時間をとる**
  - 時間がなかったりなどで急いでしまうと結果に対し、参加者の自信が低下する恐れがある
  - 目標が多い場合や、問題の理解が十分でないときはセッションを小さく分ける
- 参加者をジャストインタイムで育てる
  - 重要なアーキテクチャと設計の概念はワークショップ中にジャストインタイムでおしえるための時間を撮っておく
  - 参加することこそ、ワークショップの成功の鍵
  - 必要な知識を参加者がもてるようにする
- パーキングロットを使う
  - 今すぐに話し合うべきではない話題は一旦保留にし、本質的な議題に集中できるようにする

## 分かってないこと

- 特にないです

## ディスカッション

- 特にないです