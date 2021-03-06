# データベースを制する者はシステムを制す

学習者：m.sasaki

---

## 1.システムとデータベース
- データベース
  - データの集まり
- DBMS( Database Management System) 
  - データベースを管理するためのシステム

<br>

#### データ
- ある形式に揃えられた事実
- 情報はこのデータを文脈を組み合わせたもの　

---

## 2.データベースあれこれ
- データベースの種類
  1. リレーショナルデータベース（Relational Database：RDB）
     - 二次元表で管理
     - 現在最も一般的なデータベース 
  2. オブジェクト指向データベース（Object Priented Database：OODB）
     - オブジェクト形式でデータを保存するデータベース 
  3. XMLデータベース（XML Database：XMLDB）
     - XML形式でデータを扱うデータベース 
  4. キー・バリュー型ストア（Key-Value Store：KVS）
     - キーとバリューの組み合わせのデータベース
     - 単純なデータの問い合わせを高速化
  5. 階層型データベース（Hierarchical Database：略称なし）
     - 階層構造で管理
     - RDBの前の主流だった

<br>

#### DBMSの違いは設計に影響するか
- 基本的には設計方法は影響を受けない

---
## 3.システム開発の工程と設計
- システム開発の設計工程
  1. 要件定義
     - 要件を決める工程
     - 顧客、または社内のシステムを利用する人などと要件の定義を行う 
  2. 設計
     - 定義された要件から設計を行う
  3. 開発（実装）
     - 設計書に従って実際にシステムを作る工程(実装)
  4. テスト
     - 実装されたシステムの動作テスト
     - 機能品質、性能や信頼性のテストを行う

---
### 設計工程と開発モデル
- ウォーターフォールモデル
  - 要件定義　→　設計　→　開発　→　テスト
  - 上記のように上から下に降りていくイメージの開発手法
  - 基本的に逆流するようなことはない
  - 手戻りが難しく、改修が高コスト
  - 大規模開発でよく採用される

<br>

- プロトタイピングモデル
  - 小さい試作品を作って顧客に見せながら開発を進める
  - 早期から顧客と具体的なイメージを共有できる
  - 変更を繰り返すうちに取り返しがつかなくなることも

<br>

- アジャイルモデル
  - プロトタイプ開発よりもさらに細かく区切って顧客と密に連携する
  - 開発の大枠のみを取り決め進める
  - 大規模開発には向いていない

---

## 4.設計工程とデータベース

### データベース設計が重要な理由
- データ設計とデータベース設計は同義
- データ設計がシステムの品質を左右する

---

### DOAとPOA
- データ中心アプローチ(Data Oriented Approach：DOA)
  - プログラムよりも前にデータ設計を行う方法
  - データ中心の考え方
  - データ　→　プログラム

<br>

- プロセス中心アプローチ(Process Oriented Approach：POA)
  - DOAの反対でプログラム設計を先に行う
  - プログラム中心の考え方
  - 今は時代遅れの考え方
  - プログラム　→　データ

<br>

#### データ設計はシステムの品質を決める最も重要な要因

---

### 3層スキーマ
> **スキーマとは**
> データ構造やフォーマットの事を指す

<br>

- 外部スキーマ
  - テーブルやビュー（画面やデータ）
- 概念スキーマ
  - テーブル定義（データの要素やデータ同士の関係性）
  - データ独立性を保証するために存在する
- 内部スキーマ
  - データの物理的配置（テーブルやインデックスの物理的な定義）


