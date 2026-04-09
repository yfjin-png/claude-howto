<picture>
  <source media="(prefers-color-scheme: dark)" srcset="../../../resources/logos/claude-howto-logo-dark.svg">
  <img alt="Claude How To" src="../../../resources/logos/claude-howto-logo.svg">
</picture>

# PRレビュープラグイン

セキュリティ、テスト、ドキュメンテーションチェック付きの完全なPRレビューワークフロー。

## 機能

✅ セキュリティ分析
✅ テストカバレッジチェック
✅ ドキュメンテーション検証
✅ コード品質評価
✅ パフォーマンス影響分析

## インストール

```bash
/plugin install pr-review
```

## 含まれるもの

### スラッシュコマンド
- `/review-pr` - 包括的なPRレビュー
- `/check-security` - セキュリティ重点レビュー
- `/check-tests` - テストカバレッジ分析

### サブエージェント
- `security-reviewer` - セキュリティ脆弱性検出
- `test-checker` - テストカバレッジ分析
- `performance-analyzer` - パフォーマンス影響評価

### MCPサーバー
- PRデータ用GitHub統合

### フック
- `pre-review.js` - レビュー前検証

## 使用方法

### 基本的なPRレビュー
```
/review-pr
```

### セキュリティチェックのみ
```
/check-security
```

### テストカバレッジチェック
```
/check-tests
```

## 要件

- Claude Code 1.0+
- GitHubアクセス
- Gitリポジトリ

## 設定

GitHubトークンをセットアップ:
```bash
export GITHUB_TOKEN="your_github_token"
```

## ワークフロー例

```
ユーザー: /review-pr

Claude:
1. レビュー前フックを実行（gitリポジトリを検証）
2. GitHub MCPを使用してPRデータを取得
3. security-reviewerサブエージェントにセキュリティレビューを委譲
4. test-checkerサブエージェントにテストを委譲
5. performance-analyzerサブエージェントにパフォーマンスを委譲
6. すべての結果を合成
7. 包括的なレビューレポートを提供

結果:
✅ セキュリティ: 重大な問題は見つかりません
⚠️  テスト: カバレッジは65%、80%+を推奨
✅ パフォーマンス: 大きな影響はありません
📝 推奨事項: エッジケースのテストを追加
```
