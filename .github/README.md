# GitHub Actions ワークフロー設定

このディレクトリには、リポジトリ間でコンテンツを同期するための GitHub Actions ワークフローが含まれています。

## 必要な設定

### GitHub App の作成と設定

1. GitHub App を作成する
   - GitHub の Settings > Developer settings > GitHub Apps > New GitHub App に移動
   - App 名: `TechiroReleaseBot` (または任意の名前)
   - Homepage URL: リポジトリの URL
   - Webhook: 無効化可能
   - Repository permissions:
     - Contents: Read & write
     - Pull requests: Read & write
   - 「Create GitHub App」をクリック

2. GitHub App をインストール
   - 作成した App の設定ページで「Install App」をクリック
   - `techiro` 組織を選択
   - `release-test` と `release-test-server` リポジトリへのアクセスを許可

3. 秘密鍵の生成
   - App の設定ページで「Generate a private key」をクリック
   - ダウンロードされた秘密鍵ファイルを保存

### リポジトリシークレットの設定

以下のシークレットを両方のリポジトリに設定する必要があります:

1. `APP_ID`: GitHub App の ID (App の設定ページで確認可能)
2. `APP_PRIVATE_KEY`: 生成した秘密鍵の内容 (PEM ファイルの内容をそのまま貼り付け)

## ワークフローファイル

### 1. `sync-to-release-test.yml`

`release-server.md` ファイルが変更されたときに、`techiro/release-test` リポジトリに repository_dispatch イベントを送信します。

### 2. `update-server-release.yml`

repository_dispatch イベントを受け取り、以下の処理を行います:

1. GitHub App トークンの生成
2. リポジトリのチェックアウト
3. 古いブランチとプルリクエストの整理
4. サーバーリポジトリの操作
5. 生成コードの反映と整備
6. プルリクエストの作成

## 手動実行

ワークフローは手動でも実行できます:

1. GitHub リポジトリの「Actions」タブに移動
2. 「サーバーリリース更新ワークフロー」を選択
3. 「Run workflow」をクリック
4. 必要に応じてブランチ名を指定
5. 「Run workflow」をクリック