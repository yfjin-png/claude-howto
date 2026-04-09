---
name: Setup CI/CD Pipeline
description: 品質保証のためのプリコミットフックと GitHub Actions を実装
tags: ci-cd, devops, automation
---

# CI/CD パイプラインセットアップ

プロジェクトタイプに適応させた包括的な DevOps 品質ゲートを実装します：

1. **プロジェクトを分析**: 言語、フレームワーク、ビルドシステム、既存ツーリングを検出
2. **プリコミットフックを構成** (言語固有ツール)：
   - フォーマッティング: Prettier/Black/gofmt/rustfmt/など
   - リント: ESLint/Ruff/golangci-lint/Clippy/など
   - セキュリティ: Bandit/gosec/cargo-audit/npm audit/など
   - タイプチェック: TypeScript/mypy/flow (該当する場合)
   - テスト: 関連するテストスイートを実行
3. **GitHub Actions ワークフローを作成** (.github/workflows/)：
   - プッシュ/PR でプリコミットチェックをミラー
   - マルチバージョン/プラットフォーム行列 (該当する場合)
   - ビルドとテスト検証
   - デプロイメント ステップ (必要に応じて)
4. **パイプラインを検証**: ローカルでテスト、テスト PR を作成、すべてのチェックが成功することを確認

フリー/オープンソースツールを使用。既存の構成を尊重。実行を高速に保つ。

---
**最終更新**: 2026年4月9日
