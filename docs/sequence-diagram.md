# リポジトリ同期ワークフローのシーケンス図

このドキュメントでは、`techiro/release-test-server` リポジトリと `techiro/release-test` リポジトリ間の同期ワークフローを説明するシーケンス図を提供します。

## リポジトリ同期のシーケンス図

```mermaid
sequenceDiagram
    participant Developer
    participant ReleaseTestServer as techiro/release-test-server
    participant GitHub as GitHub Actions
    participant ReleaseTest as techiro/release-test
    
    Developer->>ReleaseTestServer: release-server.md を更新
    Developer->>ReleaseTestServer: 変更をコミット＆プッシュ
    
    ReleaseTestServer->>GitHub: push イベントをトリガー
    Note over GitHub: sync-to-release-test.yml<br/>ワークフローが実行される
    
    GitHub->>ReleaseTest: repository_dispatch イベントを送信
    Note over ReleaseTest: receive-server-updates.yml<br/>ワークフローが実行される
    
    ReleaseTest->>ReleaseTest: make sync server-release を実行
    Note over ReleaseTest: 最新の release-server.md を<br/>取得して更新
    
    ReleaseTest->>ReleaseTest: 変更を検出
    ReleaseTest->>ReleaseTest: 新しいブランチを作成
    ReleaseTest->>ReleaseTest: 変更をコミット
    
    ReleaseTest->>GitHub: プルリクエストを作成
    GitHub->>Developer: プルリクエスト通知
    
    Developer->>ReleaseTest: プルリクエストをレビュー
    Developer->>ReleaseTest: プルリクエストをマージ
```

## 処理の流れ

1. 開発者が `techiro/release-test-server` リポジトリの `release-server.md` ファイルを更新します
2. 変更が main ブランチにプッシュされると、GitHub Actions の `sync-to-release-test.yml` ワークフローが実行されます
   - または、開発者が手動でワークフローをトリガーすることもできます（`workflow_dispatch`）
   - 手動トリガーの場合、更新内容のメモ、日付、時刻を追加するオプションがあります
3. このワークフローは `techiro/release-test` リポジトリに repository_dispatch イベントを送信します
4. `techiro/release-test` リポジトリでは `receive-server-updates.yml` ワークフローが実行されます
5. このワークフローは `make sync server-release` コマンドを実行して、最新の `release-server.md` ファイルを取得します
6. 変更が検出されると、新しいブランチが作成され、変更がコミットされます
7. 自動的にプルリクエストが作成され、開発者に通知されます
8. 開発者はプルリクエストをレビューし、問題がなければマージします

この自動化されたワークフローにより、両方のリポジトリ間でサーバーリリース情報を常に最新の状態に保つことができます。