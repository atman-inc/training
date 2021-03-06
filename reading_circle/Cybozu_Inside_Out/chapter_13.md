# Kubernetes

学習者：m.sasaki

※購入していた書籍があったため、そちらも利用しています

---

### Kubernetesを使う理由
- アプリケーションとサーバーを疎結合にする
  - アプリケーションチームは物理サーバーの構成やメンテナンスを気にする必要がなくなる
- 導入しても物理サーバーの障害、メンテナンスをなくすことは出来ない
- 障害やメンテナンスによってアプリケーションに影響が生じうる事実も変わらない
- アプリケーションが障害から復旧する為のハンドリングは楽に行える
- サーバーに直接依存しないので、サーバー側の変化も受けない

---

### Kubernetesの環境を整える
- Macを使用している場合は「Docker for Mac」に付属しているKubernetesが利用できる
  - Preference　→　Kubernetesタブから有効化が可能
- ローカル学習用のKubernetesクラスタを立ち上げる場合
  - Minikubeを使用するのがおすすめ
    - [Minikube](https://kubernetes.io/docs/tasks/tools/install-minikube/)
    - インストール後に`minikube start`でシングルノードのKubernetesクラスタが立ち上がる

---

### Kubernetesとは
- Kubernetes(クーべネティス)、k8sと略す
  - オーケストレーションツール
  - 複数の物理マシン、または仮想マシンがあることが前提
  - そのマシンの中に複数のコンテナが存在
  - 複数マシンの作成や管理の煩雑さをうまくやってくれるツール
  - 定義ファイル（マニフェストファイル）に従って作成、管理を行う

<br>

- 用語
  - オーケストレーションツール
    - システム全体を統括して複数コンテナを管理できるもの

---

### マスターノードとワーカーノード
- マスターノード・・・コントロールを司るノード
  - ワーカーノード上のコンテナを管理
- ワーカーノード・・・実際に動かすノード
  - コンテナが実際に動くノード

<br>

- クラスターは自律的に動く
- 人間が命令して動くのではなくマスターノードに設定された内容に従ってマスターノードが自律的にワーカーノードを管理する


<br>

- 用語
  - クラスター・・・マスターとワーカーで構成されたKubernetesシステムの1群

---

### Kubernetesは常に望ましい状態に保つ
- YAMLファイルの形式で管理する
  - YAMLファイル通りに自動でコンテナを作成、削除を行いながら維持する
- Docker-composeとの違い
  - 作って終わりではなく、作った物を維持する
  - マシンを超えてシステムを構成することが可能
  - 厳密にはDocker-composeでもオプション設定で維持可能

---

### Kubernetesの構成と用語
- Pod
  - コンテナとボリュームがセットになったもの
  - 基本的には1Podに1コンテナ（複数もできる）
  - Pod単位で管理される
  - ボリュームは複数コンテナで情報共有のために使用する
  - ボリュームを作らないことも多い
- サービス
  - Podをまとめて管理するもの
  - 同一の構成のPodを管理する
  - ロードバランサーの役割を担う
  - サービス毎に固定のIPアドレスが割り当てられる
- レプリカセット
  - Podの数を管理する
  - 単独で使うことは少なく、デプロイメントと使うことが多い
- レプリカ
  - レプリカセットに管理されている同一構成のPodの事
- デプロイメント
  - Podのデプロイを管理する
  - Podに関する情報を保持
- リソース
  - 上記のようなPod、サービス、レプリカセットなどのこと
  - 50種類くらい存在する

#### 基本的に学習用で使用するKubernetesは本番とは大きく違うことを理解しておく

---

### 定義ファイルの書き方
- Podやサービスに関する設定・・・マニフェスト
- マニフェストを記述したファイル・・・マニフェストファイル（定義ファイル）
- YAML形式、もしくはJSON形式で記述
- ファイル名は任意で決められる
- 定義ファイルはリソース単位で記述
  - Pod項目は基本的には書かない
    - Podは「自動的に設定した数に保たれる」機能がない為
    - Podの数の管理を担う「デプロイメント」を作成する
  - レプリカセットもPodと同じで項目は使わない
  - デプロイメント・・・Podとサービスを内包している
- 定義ファイルは分けてもいい
  - まとめて書く場合は「---」でリソースを区切る必要がある

<br>

**定義ファイルに記述する内容**

| 大項目名 | 説明 |
| :----: | :----:|
| apiVersion: | APIグループとそのバージョン |
| kind: | リソースの種類 |
| metadata: | メタデータ |
| spec: | リソースの中身 |

- リソースを指定するときは「apiVersion」と「kind」で指定

**よく使うリソースのAPIグループと種類**
| リソース | APIグループ / バージョン | 種類（kind） |
| :----: | :----: | :----: |
| Pod | core/v1(v1と略せる) | Pod |
| サービス | core/v1(v1と略せる) | Service |
| デプロイメント | apps/v1 | Deployment |
| レプリカセット | apps/v1 | ReplicaSet |

- 基本的には公式サイトで確認するべき [](https://kubernetes.io/ja/docs/reference/kubectl/overview/#resource-types)

<br>

**メタデータとスペック**
- メタデータ
  - リソース名、ラベルを記述する
  - その他もあるが慣れてきたら覚える
- スペック
  - リソースの内容を記述する
    - どんなリソースを作るか

<br>

**主なメタデータ**

| 項目 | 内容 |
| :----: | :----: |
| name | リソースの名前<br>一意に識別される文字列 |
| namespace | リソースが細分化されるDNS互換ラベル |
| uid | 一意に識別する番号 |
| resourceVersion | リソースバージョン |
| generation | 生成した順序を表す番号 |
| creationTimestamp | 作成日時 |
| deletionTimestamp | 削除日時 |
| labels | 任意のラベル |
| anotation | リソースに対して設定したい値<br>選択対象にならない |

<br>

**ラベルとセレクター**

- ラベル
  - リソースに任意で付けられる
  - キーと値のペア
  - メタデータとして設定する
  - セレクターを使用して特定のリソースを選択可能に

<br>

---

### メタデータとスペックの書き方

**Podのメタデータとスペック（最低限の内容）**

| 項目名 | 項目分類 | 説明 |
| :----: | :----: | :----: |
| name: | metadata/中項目 | Podの名前 |
| labels: | metadata/中項目 | ラベル |
| containers: | spec/中項目 | コンテナ構成 |
| - name: | spec/小項目 | コンテナ名 |
| image: | spec/小項目 | 元にするイメージ |
| ports: | spec/小項目 | 使うポート |

- コンテナの情報は「containers」項目にぶら下げる

---

**デプロイメント　定義ファイル**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata: 
  name: apa000dep
spec: 
  selector:
    matchLabels:
      app: apa000kube
  replicas: 3
  template:
    metadata:
      labels:
        app: apa000kube
    spec:
      containers:
        - name: apa000ex91
          image: httpd
          ports: 
          - containerPort: 80

```

---

**サービス　定義ファイル**

```yaml
apiVersion: v1
kind: Service
metadata:
  name: apa000ser
spec:
  type: NodePort
  ports: 
  - port: 8099
    targetPort: 80
    protocol: TCP
    nodePort: 30080
  selector:
    app: apa000kube
```

---
### Kubernetesのコマンド
- kubectlを使用して操作


**kubectl主なコマンド**

| コマンド | 説明 |
| :----: | :----: |
| create | リソースを作成 |
| edit | リソースを編集 |
| delete | リソースを削除 |
| get | リソースの状態を表示 |
| set | リソースの値を設定 |
| apply | リソースの変更を反映 |
| describe | 詳細情報を確認 |
| diff | 「望ましい状態」と「現在の状態」の差分を確認 |
| expose | リソースを生成するための定義ファイルを作成 |
| scacle | レプリカ数を変更 |
| autoscale | オートスケールを設定 |
| rollout | ロールアウトを設定 |
| exec | コンテナでコマンドを実行 |
| run | コンテナでコマンドを一回実行 |
| attach | コンテナにアタッチ |
| cp | コンテナにファイルをコピー |
| logs | コンテナのログを表示 |
| cluster-info | クラスターの詳細を表示 |
| top | CPU、メモリ、ストレージのリソースを確認 |

