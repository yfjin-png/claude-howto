# プロジェクト設定

## プロジェクト概要
- **名前**: e コマース プラットフォーム
- **技術スタック**: Node.js、PostgreSQL、React 18、Docker
- **チームサイズ**: 5 人の開発者
- **期限**: 2025 年 Q4

## アーキテクチャ
@docs/architecture.md
@docs/api-standards.md
@docs/database-schema.md

## 開発標準

### コードスタイル
- Prettier を使用してフォーマットする
- ESLint と airbnb config を使用する
- 最大行長: 100 文字
- 2 スペースのインデントを使用する

### 命名規約
- **ファイル**: ケバブケース (user-controller.js)
- **クラス**: PascalCase (UserService)
- **関数/変数**: camelCase (getUserById)
- **定数**: UPPER_SNAKE_CASE (API_BASE_URL)
- **データベーステーブル**: snake_case (user_accounts)

### Git ワークフロー
- ブランチ名: `feature/description` または `fix/description`
- コミットメッセージ: 従来のコミットに従う
- マージ前に PR が必要
- すべての CI/CD チェックが必須
- 最小 1 つの承認が必要

### テスト要件
- 最小 80% コードカバレッジ
- すべてのクリティカルパスはテストが必要
- ユニットテストに Jest を使用
- E2E テストに Cypress を使用
- テストファイル名: `*.test.ts` または `*.spec.ts`

### API 標準
- REST ful エンドポイントのみ
- JSON リクエスト/レスポンス
- HTTP ステータスコードを正しく使用
- API エンドポイントをバージョン管理: `/api/v1/`
- すべてのエンドポイントを例で文書化

### データベース
- スキーマ変更にはマイグレーションを使用
- 認証情報をハードコードしない
- コネクションプーリングを使用
- 開発でクエリログを有効化
- 定期的なバックアップが必要

### デプロイ
- Docker ベースのデプロイ
- Kubernetes オーケストレーション
- ブルーグリーンデプロイメント戦略
- 障害時の自動ロールバック
- デプロイ前にデータベースマイグレーションを実行

## 一般的なコマンド

| コマンド | 目的 |
|---------|------|
| `npm run dev` | 開発サーバーを開始 |
| `npm test` | テストスイートを実行 |
| `npm run lint` | コードスタイルをチェック |
| `npm run build` | 本番用にビルド |
| `npm run migrate` | データベースマイグレーションを実行 |

## チーム連絡先
- 技術リーダー: Sarah Chen (@sarah.chen)
- プロダクトマネージャー: Mike Johnson (@mike.j)
- DevOps: Alex Kim (@alex.k)

## 既知の問題と対処法
- ピーク時のPostgreSQL コネクションプーリングが 20 に制限されている
- 対処法: クエリキューイングを実装
- async ジェネレーターでの Safari 14 互換性の問題
- 対処法: Babel トランスパイラーを使用

## 関連プロジェクト
- 分析ダッシュボード: `/projects/analytics`
- モバイルアプリ: `/projects/mobile`
- 管理者パネル: `/projects/admin`

---
**最終更新**: 2026 年 4 月 9 日
