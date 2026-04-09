---
description: コードをクリーンアップし、変更をステージし、プルリクエスト準備
allowed-tools: Bash(git add:*), Bash(git status:*), Bash(git diff:*), Bash(npm test:*), Bash(npm run lint:*)
---

# プルリクエスト準備チェックリスト

PR を作成する前に、以下のステップを実行してください：

1. リント実行: `prettier --write .`
2. テスト実行: `npm test`
3. git diff をレビュー: `git diff HEAD`
4. 変更をステージ: `git add .`
5. 従来のコミット形式に従ってコミットメッセージを作成：
   - `fix:` バグ修正の場合
   - `feat:` 新機能の場合
   - `docs:` ドキュメント向け
   - `refactor:` コード再構成向け
   - `test:` テスト追加向け
   - `chore:` メンテナンス向け

6. 以下を含む PR 概要を生成：
   - 何が変わったか
   - なぜ変わったか
   - 実施されたテスト
   - 潜在的な影響

---
**最終更新**: 2026年4月9日
