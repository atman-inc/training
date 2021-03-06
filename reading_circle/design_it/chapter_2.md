# 2章 デザイン思考の基礎

担当: @y-kaneda

## まとめ

_この章では、ソフトウェアアーキテクチャにデザイン思考を適用する方法を学んでいく_  
  
### デザイン思考の4つの原則(**HART**)
ソフトウェアアーキテクチャにも適用することのできる、デザインに関する4つの普遍的な原則

1. 人間性の規則(The **H**uman Rule)
    - 全ての設計は人のために人と共に行われる
    - ステークホルダー、プログラマー、テスター、マネージャーなどと協力し、理解・共感しなければならない
    - *象牙の塔のアーキテクト*は誤った考え方であり、チームから離れてしまうとアーキテクトとその他の人々の人間的な繋がりは失われてしまう
2. 曖昧性の規則（The **A**mbiguity Rule）
    - 曖昧さは危険を伴うが、判断を固める前であれば曖昧さを許容し選択肢を残しておくことができる
    - アーキテクトは必要最小限のアーキテクチャを設計すべき
        - 優先すべき品質特性の満たし方とそれに関するリスクをどう減らすかだけを示し、それ以外の(具体的な)設計判断は後続の設計者に委ねる
        - 必要以上に詰めないアーキテクチャ設計により後続の設計者のために詳細な設計についての判断を安全な状態で保留しておくことができ、状況の変化に対して対応できる幅を作れる
3. 再デザインの規則（The **R**e-design Rule）
    - 既存のパターンや過去の設計で参考になる、利用できるものは多い
    - 頑張って車輪の再発明をすることはない
4. 触感性の規則（The **T**angibility Rule）
    - アーキテクチャは他人が理解・共感・議論ができるような「触れやすい」形で共有する必要がある
        - 図に描く、プロトタイプを構築する、制御フローの一部を身振りで伝える、など

### デザインマインドセットを身に付ける

アーキテクチャを考える上で適切なタイミングで適切な詳細に意識を集中するための考え方や考え方の軸となるもの

1. 理解(Understand)
    - ステークホルダー・ユーザーなどから情報を引き出し、問題や必要としていることを理解し、重要なビジネス目標と品質特性を調査する
    - 自分たちのチームの機能・状態を考え、設計判断時の優先順位とトレードオフをより深く理解する
2. 探究(Explore)
    - 複数のデザインコンセプトを作り、種々の問題をそれぞれ解決する最もうまくいく組み合わせが見つかるまで探究する
    - 最適な組み合わせを見つけるには様々なパターン、技術、開発方法を調査する必要がある
3. 作成(Make)
    - 素晴らしいアイディアも人に伝えられなければ意味がない
    - アイディアを元にプロトタイプやドキュメントなどを作ることにより、人に伝えられる以外にもアイディアを試して分析できたり、実際の成果物の一部になり得たりもするというメリットがある
4. 評価(Evaluate)
    - 現在の理解を基準に設計判断が妥当かを判断する。
    - アーキテクチャまるごと「すべてあり」か「すべてなし」かだけの0/1 の判断ではなく、一部分ずつ評価することができる
    - アーキテクチャの一部を様々なシナリオに当てはめてみたり、実験したり判断に関わるリスクを調査することにより設計判断を検査できる
    - アーキテクチャだけではなく成果物の検査にも役立つ

### Think、Do、Check

ソフトウェアシステムに携わっていて日々得られる新しい学びを常に把握してマインドセットを頻繁に更新するのに役立つ、素早いフィードバックループを持った設計アプローチ。サイクルを回す主題を変えながら繰り返し、サイクルによって各ステップでふさわしいマインドセットを使い分ける

1. Think
    - 何を明らかにすべきか、どんなリスクがあるかなどを考え、計画する
    - エンジニアリングリスク、ビジネス目標、品質特性、トレードオフ
2. Do
    - 実際に計画を実行(作成)してみて、可視化や確認をする
    - モデル、プロトタイプ、計画、代替案のリスト
3. Check
    - Doで得られたことを検査し、Thinkにつなげる
    - シナリオウォークスルー、代替案の比較、直接的テスト、包括的テスト

## 分かってないこと

- 特にないです

## ディスカッション

- 問題を理解するために取り組んだ協働は？
- ステークホルダーやチームメンバーとのやりとりで、何か作ったものによって変わったことは？
