---
allowed-tools: Bash(git add:*), Bash(git status:*), Bash(git commit:*), Bash(git diff:*)
argument-hint: [message]
description: コンテキスト付きで git コミットを作成
---

## コンテキスト

- 現在の git ステータス: !`git status`
- 現在の git diff: !`git diff HEAD`
- 現在のブランチ: !`git branch --show-current`
- 最近のコミット: !`git log --oneline -10`

## タスク

上記の変更に基づいて、単一の git コミットを作成してください。

メッセージが引数を通じて提供された場合、それを使用してください: $ARGUMENTS

それ以外の場合、変更を分析し、従来のコミット形式に従って適切なコミットメッセージを作成してください：
- `feat:` 新機能の場合
- `fix:` バグ修正の場合
- `docs:` ドキュメント変更の場合
- `refactor:` コードリファクタリングの場合
- `test:` テスト追加の場合
- `chore:` メンテナンスタスクの場合

---
**最終更新**: 2026年4月9日
