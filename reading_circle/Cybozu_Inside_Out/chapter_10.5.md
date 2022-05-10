# GitHub Actions

### Github Actionsとは
- Githubが提供するCI/CDのサービス

- 格納先
  - 基本的に全て「.github/workflow」の配下に配置
  - 形式はYAML

### SAMPLE CODE
Githubの任意のリポジトリでActionsを選択。
「Simple workflow」の「Configure」ボタンで自動生成される「blank.yml」
**blank.yml**
```yaml
# workflow名
name: CI

# 「Push」や「Pull Request」時にこのワークフローを実行する
# 「branches」で対象のブランチを指定する
on:
  # プッシュやプルリクエストのイベントをトリガーに、develop ブランチのみを対象としたワークフローを実行
  push:
    branches: [ develop ]
  pull_request:
    branches: [ develop ]

  # Actions タブから手動でワークフローを実行することができます。
  workflow_dispatch:

# ワークフローの実行は、順次または並行して実行される1つまたは複数のジョブで構成されます。
jobs:
  # このワークフローには、"build "という1つのジョブが含まれています。
  build: # job名は任意の名前
    # ジョブが実行されるランナーの種類（Virtual machineの形式を指定）
    runs-on: ubuntu-latest

    # ステップは、ジョブの一部として実行される一連のタスクを表します。
    steps: # jobのステップ
      # $GITHUB_WORKSPACE以下にリポジトリをチェックアウトし、ジョブがアクセスできるようにします。
      - uses: actions/checkout@v3

      # ランナーシェルを使って一つのコマンドを実行する
      - name: Run a one-line script # Step名
        run: echo Hello, world!

      # ランナーズシェルを使って一連のコマンドを実行する
      # 複数のコマンドは「|（パイプ）」で繋ぐ
      - name: Run a multi-line script
        run: |
          echo Add other actions to build,
          echo test, and deploy your project.
```