<picture>
  <source media="(prefers-color-scheme: dark)" srcset="../../../resources/logos/claude-howto-logo-dark.svg">
  <img alt="Claude How To" src="../../../resources/logos/claude-howto-logo.svg">
</picture>

# ドキュメンテーションプラグイン

プロジェクトの包括的なドキュメンテーション生成と保守です。

## 機能

✅ API ドキュメンテーション生成
✅ README作成と更新
✅ ドキュメンテーション同期
✅ コメント改善
✅ 例生成

## インストール

```bash
/plugin install documentation
```

## 含まれるもの

### スラッシュコマンド
- `/generate-api-docs` - APIドキュメンテーションを生成
- `/generate-readme` - READMEを作成または更新
- `/sync-docs` - ドキュメンテーションをコード変更と同期
- `/validate-docs` - ドキュメンテーションを検証

### サブエージェント
- `api-documenter` - APIドキュメンテーション専門家
- `code-commentator` - コードコメント改善
- `example-generator` - コード例作成

### テンプレート
- `api-endpoint.md` - APIエンドポイントドキュメンテーションテンプレート
- `function-docs.md` - 関数ドキュメンテーションテンプレート
- `adr-template.md` - アーキテクチャ決定記録テンプレート

### MCPサーバー
- ドキュメンテーション同期用GitHub統合

## 使用方法

### APIドキュメンテーションを生成
```
/generate-api-docs
```

### READMEを作成
```
/generate-readme
```

### ドキュメンテーションを同期
```
/sync-docs
```

### ドキュメンテーションを検証
```
/validate-docs
```

## 要件

- Claude Code 1.0+
- GitHubアクセス（オプション）

## ワークフロー例

```
ユーザー: /generate-api-docs

Claude:
1. /src/api/内のすべてのAPIエンドポイントをスキャン
2. api-documenterサブエージェントに委譲
3. 関数シグネチャとJSDocを抽出
4. モジュール/エンドポイント別に整理
5. api-endpoint.mdテンプレートを使用
6. 包括的なmarkdownドキュメンテーションを生成
7. curl、JavaScript、Pythonの例を含める

結果:
✅ APIドキュメンテーションが生成されました
📄 作成されたファイル:
   - docs/api/users.md
   - docs/api/auth.md
   - docs/api/products.md
📊 カバレッジ: 23/23エンドポイント文書化
```

## テンプレート使用

### APIエンドポイントテンプレート
完全な例でRESTAPIエンドポイントを文書化するために使用します。

### 関数ドキュメンテーションテンプレート
個々の関数/メソッドを文書化するために使用します。

### ADRテンプレート
アーキテクチャ決定を文書化するために使用します。

## 設定

ドキュメンテーション同期用GitHubトークンをセットアップ:
```bash
export GITHUB_TOKEN="your_github_token"
```

## ベストプラクティス

- ドキュメンテーションをコードに近く保つ
- コード変更でドキュメンテーションを更新
- 実践的な例を含める
- 定期的に検証
- 一貫性のためテンプレートを使用
