# GitHub Actions ワークフロー

このディレクトリには、リポジトリ間でコンテンツを同期するための GitHub Actions ワークフローが含まれています。

## ワークフローファイル

### 1. `sync-to-release-test.yml`

このワークフローは、`techiro/release-test-server` リポジトリの `release-server.md` ファイルに変更が加えられたときにトリガーされます。`techiro/release-test` リポジトリに repository_dispatch イベントを送信します。

**要件:**
- リポジトリ設定に `REPO_ACCESS_TOKEN` という名前のシークレットを設定する必要があります。このトークンは、`repo` スコープを持ち、両方のリポジトリにアクセスできる GitHub パーソナルアクセストークンである必要があります。

### 2. `receive-server-updates.yml` (techiro/release-test リポジトリに配置する必要があります)

このワークフローは、`techiro/release-test-server` リポジトリからの repository_dispatch イベントによってトリガーされます。以下の処理を行います:
1. `make sync server-release` コマンドを実行します
2. `release-server.md` ファイルへの変更をコミットします
3. これらの変更を含むプルリクエストを作成します

**要件:**
- リポジトリには、サーバーリポジトリから最新の `release-server.md` を取得する `sync server-release` ターゲットを持つ Makefile が必要です。
- GitHub Actions がブランチとプルリクエストを作成する権限を持っている必要があります。

## セットアップ手順

1. `techiro/release-test-server` リポジトリの設定に `REPO_ACCESS_TOKEN` シークレットを追加します。
2. `receive-server-updates.yml` ファイルを `techiro/release-test` リポジトリの `.github/workflows` ディレクトリに配置します。
3. `techiro/release-test` リポジトリに適切な同期コマンドを持つ Makefile があることを確認します。