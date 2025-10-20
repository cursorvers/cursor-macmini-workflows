# cursor-workflows

共通で利用できる再利用可能GitHub Actionsワークフロー群です。`workflow_call` で各リポから呼び出せます。

## 使い方

1. このリポジトリにタグ `v1` を作成（初回導入時）。
2. 各リポに `.github/workflows/ci.yml` を追加し、下記の例を参考に呼び出します。

```yaml
name: CI
on:
  push:
    branches: ["**"]
  pull_request: {}
  workflow_dispatch: {}
concurrency:
  group: ${{ github.workflow }}-${{ github.ref }}
  cancel-in-progress: true
jobs:
  ci:
    uses: <ORG>/cursor-workflows/.github/workflows/cursor-ci.yml@v1
    with:
      node-version: '20'
      run-tests: 'npm test --if-present'
      run-build: 'npm run build --if-present'
      working-directory: '.'
    secrets:
      CURSOR_API_KEY: ${{ secrets.CURSOR_API_KEY }} # Cursor CLI使用時のみ
```

## データ保全ガード

以下のディレクトリに対する変更や生成物が検出された場合、ジョブは失敗します：
- `@data/`（内部/ログ配置禁止）
- `30_Permanent/`（手動管理・自動操作禁止）
- `@backups/`（バックアップ生成禁止）

ワークフローは `permissions: contents: read` とし、`actions/checkout` では `persist-credentials: false` を設定してGitHubへの書き込み権限を持ちません。

## カスタマイズ

- Node以外のプロジェクトでも、`with:` の `run-tests` と `run-build` を適宜置き換えることで利用可能です。
- Cursor CLI を組み込む場合は `CURSOR_API_KEY` を各リポのSecretsに設定し、ワークフロー内の該当ステップを有効化してください（read-only用途推奨）。

## ライセンス

本ワークフロー定義は、必要に応じて組織内で再利用してください。
