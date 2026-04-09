<picture>
  <source media="(prefers-color-scheme: dark)" srcset="../resources/logos/claude-howto-logo-dark.svg">
  <img alt="Claude How To" src="../resources/logos/claude-howto-logo.svg">
</picture>

<p align="center">
  <a href="https://github.com/trending">
    <img src="https://img.shields.io/badge/GitHub-🔥%20%231%20Trending-purple?style=for-the-badge&logo=github"/>
  </a>
</p>

[![GitHub Stars](https://img.shields.io/github/stars/luongnv89/claude-howto?style=flat&color=gold)](https://github.com/luongnv89/claude-howto/stargazers)
[![GitHub Forks](https://img.shields.io/github/forks/luongnv89/claude-howto?style=flat)](https://github.com/luongnv89/claude-howto/network/members)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](../LICENSE)
[![Version](https://img.shields.io/badge/version-2.3.0-brightgreen)](../CHANGELOG.md)
[![Claude Code](https://img.shields.io/badge/Claude_Code-2.1+-purple)](https://code.claude.com)

🌐 **言語 / Ngôn ngữ / 语言:** [English](../README.md) | [Tiếng Việt](../vi/README.md) | [中文](../zh/README.md) | **日本語**

# Claude Code を週末でマスターする

`claude`とタイプすることから始めて、エージェント、フック、スキル、MCP サーバーをオーケストレーションするまで — ビジュアルチュートリアル、コピーペーストテンプレート、ガイド付きの学習パスで。

**[15分で始める](#15分で始める)** | **[自分のレベルを見つける](#どこから始めたら良いかわからない場合)** | **[機能カタログを見る](../CATALOG.md)**

---

## 目次

- [問題点](#問題点)
- [Claude How To の解決策](#claude-how-to-の解決策)
- [仕組み](#仕組み)
- [どこから始めたら良いかわからない場合](#どこから始めたら良いかわからない場合)
- [15分で始める](#15分で始める)
- [これで何を作ることができるか](#これで何を作ることができるか)
- [よくある質問](#よくある質問)
- [貢献](#貢献)
- [ライセンス](#ライセンス)

---

## 問題点

Claude Code をインストールしました。いくつかのプロンプトを試しました。次は?

- **公式ドキュメントは機能について説明していますが、それらを組み合わせる方法は示していません。** スラッシュコマンドが存在することは知っていますが、それらをフック、メモリ、サブエージェントで組み合わせて実際に時間を節約するワークフローを作成する方法は知りません。
- **明確な学習パスがありません。** MCP の前にフックを学ぶべきか？スキルの前にサブエージェント？結果的に何でもざっと見て何もマスターできません。
- **例が基本的すぎます。** 「hello world」スラッシュコマンドは、メモリを使用し、専門のエージェントに委譲し、セキュリティスキャンを自動的に実行する本番用のコードレビューパイプラインを作成するのに役に立ちません。

Claude Code のパワーの 90% を活用していません — そして自分が何を知らないかを知りません。

---

## Claude How To の解決策

これは別の機能リファレンスではありません。それは**構造化されたビジュアル化された、例に基づいたガイド**です。これはすべての Claude Code 機能を、今日あなたのプロジェクトにコピーペーストできる本番用のテンプレートで使用する方法を教えています。

| | 公式ドキュメント | このガイド |
|--|---------------|------------|
| **形式** | リファレンスドキュメント | Mermaid ダイアグラムを使用したビジュアルチュートリアル |
| **深さ** | 機能説明 | 内部でどのように機能するか |
| **例** | 基本的なスニペット | すぐに使用できる本番用テンプレート |
| **構造** | 機能別に整理 | 段階的な学習パス（初心者から上級者まで） |
| **オンボーディング** | 自主的 | ガイド付きロードマップと時間の見積もり |
| **自己評価** | なし | インタラクティブなクイズでギャップを見つけ、パーソナライズされたパスを構築 |

### 得られるもの:

- **10 つのチュートリアルモジュール** — スラッシュコマンドからカスタムエージェントチームまですべての Claude Code 機能をカバー
- **コピーペースト設定** — スラッシュコマンド、CLAUDE.md テンプレート、フックスクリプト、MCP 設定、サブエージェント定義、完全なプラグインバンドル
- **Mermaid ダイアグラム** — 各機能の内部動作を示し、「どのように」だけでなく「なぜ」を理解する
- **ガイド付きの学習パス** — 初心者からパワーユーザーになるまで 11～13 時間
- **組み込みの自己評価** — Claude Code で直接 `/self-assessment` または `/lesson-quiz hooks` を実行してギャップを特定

**[学習パスを開始する  ->](../LEARNING-ROADMAP.md)**

---

## 仕組み

### 1. 自分のレベルを見つける

[自己評価クイズ](../LEARNING-ROADMAP.md#-自分のレベルを見つける)を受けるか、Claude Code で `/self-assessment` を実行してください。既に知っていることに基づいてパーソナライズされたロードマップを取得します。

### 2. ガイド付きパスに従う

10 個のモジュールを順番に進めます — 各モジュールは前のモジュールの上に構築されます。学習するときにプロジェクトに直接テンプレートをコピーします。

### 3. 機能をワークフローに組み合わせる

本当のパワーは機能の組み合わせにあります。スラッシュコマンド + メモリ + サブエージェント + フックを自動パイプラインに結合し、コードレビュー、デプロイメント、ドキュメント生成を処理する方法を学びます。

### 4. 理解をテストする

各モジュールの後で `/lesson-quiz [topic]` を実行します。クイズは見逃した部分を特定し、ギャップを素早く埋めることができます。

**[15分で始める](#15分で始める)**

---

## 21,800人以上の開発者に信頼されている

- **21,800+ GitHub スター** — Claude Code を毎日使用する開発者から
- **2,585+ フォーク** — このガイドを自分たちのワークフローに適応させるチーム
- **積極的にメンテナンス** — 毎回の Claude Code リリース（最新: v2.3.0、2026 年 4 月）と同期
- **コミュニティ駆動** — 本番環境の設定を共有する開発者からの貢献

[![Star History Chart](https://api.star-history.com/svg?repos=luongnv89/claude-howto&type=Date)](https://star-history.com/#luongnv89/claude-howto&Date)

---

## どこから始めたら良いかわからない場合

自己評価を受けるか、自分のレベルを選択してください:

| レベル | できること | ここから開始 | 時間 |
|-------|-----------|------------|------|
| **初心者** | Claude Code を起動してチャット | [スラッシュコマンド](ja/01-slash-commands/) | 約 2.5 時間 |
| **中級者** | CLAUDE.md とカスタムコマンドを使用 | [スキル](ja/03-skills/) | 約 3.5 時間 |
| **上級者** | MCP サーバーとフックを設定 | [高度な機能](ja/09-advanced-features/) | 約 5 時間 |

**すべての 10 モジュールを含む完全な学習パス:**

| 順序 | モジュール | レベル | 時間 |
|-------|--------|-------|------|
| 1 | [スラッシュコマンド](ja/01-slash-commands/) | 初心者 | 30 分 |
| 2 | [メモリ](ja/02-memory/) | 初心者+ | 45 分 |
| 3 | [チェックポイント](ja/08-checkpoints/) | 中級者 | 45 分 |
| 4 | [CLI 基本](ja/10-cli/) | 初心者+ | 30 分 |
| 5 | [スキル](ja/03-skills/) | 中級者 | 1 時間 |
| 6 | [フック](ja/06-hooks/) | 中級者 | 1 時間 |
| 7 | [MCP](ja/05-mcp/) | 中級者+ | 1 時間 |
| 8 | [サブエージェント](ja/04-subagents/) | 中級者+ | 1.5 時間 |
| 9 | [高度な機能](ja/09-advanced-features/) | 上級者 | 2～3 時間 |
| 10 | [プラグイン](ja/07-plugins/) | 上級者 | 2 時間 |

**[完全な学習ロードマップ ->](../LEARNING-ROADMAP.md)**

---

## 15分で始める

```bash
# 1. ガイドをクローン
git clone https://github.com/luongnv89/claude-howto.git
cd claude-howto

# 2. 最初のスラッシュコマンドをコピー
mkdir -p /path/to/your-project/.claude/commands
cp 01-slash-commands/optimize.md /path/to/your-project/.claude/commands/

# 3. 試す — Claude Code で次のように入力:
# /optimize

# 4. さらに進む準備? プロジェクトメモリを設定:
cp 02-memory/project-CLAUDE.md /path/to/your-project/CLAUDE.md

# 5. スキルをインストール:
cp -r 03-skills/code-review ~/.claude/skills/
```

完全な設定が必要ですか？ここは**1 時間の本質的なセットアップ**です:

```bash
# スラッシュコマンド（15 分）
cp 01-slash-commands/*.md .claude/commands/

# プロジェクトメモリ（15 分）
cp 02-memory/project-CLAUDE.md ./CLAUDE.md

# スキルをインストール（15 分）
cp -r 03-skills/code-review ~/.claude/skills/

# 週末の目標: フック、サブエージェント、MCP、プラグインを追加
# ガイド付きセットアップについては学習パスに従う
```

**[完全なインストールリファレンスを表示](#15分で始める)**

---

## これで何を作ることができるか

| ユースケース | 組み合わせる機能 |
|----------|------------------------|
| **自動コードレビュー** | スラッシュコマンド + サブエージェント + メモリ + MCP |
| **チームオンボーディング** | メモリ + スラッシュコマンド + プラグイン |
| **CI/CD オートメーション** | CLI リファレンス + フック + バックグラウンドタスク |
| **ドキュメント生成** | スキル + サブエージェント + プラグイン |
| **セキュリティ監査** | サブエージェント + スキル + フック（読み取り専用モード） |
| **DevOps パイプライン** | プラグイン + MCP + フック + バックグラウンドタスク |
| **複雑なリファクタリング** | チェックポイント + 計画モード + フック |

---

## よくある質問

**これは無料ですか？**
はい。MIT ライセンス、永遠に無料。個人プロジェクト、職場、チーム — ライセンス通知を含める以外に制限なく使用できます。

**これはメンテナンスされていますか？**
積極的にメンテナンスされています。ガイドは毎回の Claude Code リリースと同期されています。現在のバージョン: v2.3.0（2026 年 4 月）、Claude Code 2.1+ と互換性があります。

**これは公式ドキュメントとどう違いますか？**
公式ドキュメントは機能リファレンスです。このガイドはダイアグラム、本番用テンプレート、段階的な学習パスを含むチュートリアルです。互いに補完します — ここで学び、詳細な場合は公式ドキュメントを参照してください。

**すべてを終えるのにどのくらいかかりますか？**
完全なパスで 11～13 時間。しかし、スラッシュコマンドテンプレートをコピーして試すだけで 15 分で即座に価値を得ることができます。

**Claude Sonnet / Haiku / Opus で使用できますか？**
はい。すべてのテンプレートは Claude Sonnet 4.6、Claude Opus 4.6、Claude Haiku 4.5 で動作します。

**貢献できますか？**
もちろんです。ガイドラインについては [CONTRIBUTING.md](../CONTRIBUTING.md) を参照してください。新しい例、バグ修正、ドキュメント改善、コミュニティテンプレートを歓迎しています。

**オフラインで読めますか？**
はい。`uv run scripts/build_epub.py` を実行して、すべてのコンテンツとレンダリングされたダイアグラム含む EPUB 電子書籍を生成します。

---

## 今日から Claude Code をマスターし始める

Claude Code はすでにインストール済みです。10 倍の生産性とあなたの間の唯一のものは、それを使用する方法を知ることです。このガイドは、構造化されたパス、ビジュアル説明、そしてそこに到達するためのコピーペーストテンプレートを提供します。

MIT ライセンス。永遠に無料。クローンして、フォークして、あなたのものにしてください。

**[学習パスを開始する ->](../LEARNING-ROADMAP.md)** | **[機能カタログを参照](../CATALOG.md)** | **[15分で始める](#15分で始める)**

---

<details>
<summary>クイックナビゲーション — すべての機能</summary>

| 機能 | 説明 | フォルダ |
|---------|-------------|--------|
| **機能カタログ** | インストールコマンド付きの完全なリファレンス | [CATALOG.md](../CATALOG.md) |
| **スラッシュコマンド** | ユーザー起動型ショートカット | [ja/01-slash-commands/](ja/01-slash-commands/) |
| **メモリ** | 永続的なコンテキスト | [ja/02-memory/](ja/02-memory/) |
| **スキル** | 再利用可能な機能 | [ja/03-skills/](ja/03-skills/) |
| **サブエージェント** | 専門の AI アシスタント | [ja/04-subagents/](ja/04-subagents/) |
| **MCP プロトコル** | 外部ツールへのアクセス | [ja/05-mcp/](ja/05-mcp/) |
| **フック** | イベント駆動のオートメーション | [ja/06-hooks/](ja/06-hooks/) |
| **プラグイン** | バンドルされた機能 | [ja/07-plugins/](ja/07-plugins/) |
| **チェックポイント** | セッションスナップショット＆巻き戻し | [ja/08-checkpoints/](ja/08-checkpoints/) |
| **高度な機能** | 計画、思考、バックグラウンドタスク | [ja/09-advanced-features/](ja/09-advanced-features/) |
| **CLI リファレンス** | コマンド、フラグ、オプション | [ja/10-cli/](ja/10-cli/) |
| **ブログ投稿** | 実世界の使用例 | [Blog Posts](https://medium.com/@luongnv89) |

</details>

<details>
<summary>機能比較</summary>

| 機能 | 呼び出し | 永続性 | 最適な用途 |
|---------|-----------|------------|----------|
| **スラッシュコマンド** | 手動（`/cmd`） | セッションのみ | クイックショートカット |
| **メモリ** | 自動ロード | クロスセッション | 長期学習 |
| **スキル** | 自動呼び出し | ファイルシステム | 自動ワークフロー |
| **サブエージェント** | 自動委譲 | 分離されたコンテキスト | タスク分散 |
| **MCP プロトコル** | 自動クエリ | リアルタイム | ライブデータアクセス |
| **フック** | イベントトリガー | 設定済み | オートメーション＆検証 |
| **プラグイン** | 1 つのコマンド | すべての機能 | 完全なソリューション |
| **チェックポイント** | 手動/自動 | セッションベース | 安全な実験 |
| **計画モード** | 手動/自動 | 計画フェーズ | 複雑な実装 |
| **バックグラウンドタスク** | 手動 | タスク期間 | 長時間実行操作 |
| **CLI リファレンス** | ターミナルコマンド | セッション/スクリプト | オートメーション＆スクリプティング |

</details>

<details>
<summary>インストールクイックリファレンス</summary>

```bash
# スラッシュコマンド
cp 01-slash-commands/*.md .claude/commands/

# メモリ
cp 02-memory/project-CLAUDE.md ./CLAUDE.md

# スキル
cp -r 03-skills/code-review ~/.claude/skills/

# サブエージェント
cp 04-subagents/*.md .claude/agents/

# MCP
export GITHUB_TOKEN="token"
claude mcp add github -- npx -y @modelcontextprotocol/server-github

# フック
mkdir -p ~/.claude/hooks
cp 06-hooks/*.sh ~/.claude/hooks/
chmod +x ~/.claude/hooks/*.sh

# プラグイン
/plugin install pr-review

# チェックポイント（自動有効、設定で設定）
# ja/08-checkpoints/README.md を参照

# 高度な機能（設定で設定）
# ja/09-advanced-features/config-examples.json を参照

# CLI リファレンス（インストール不要）
# 使用例については ja/10-cli/README.md を参照
```

</details>

<details>
<summary>01. スラッシュコマンド</summary>

**場所**: [ja/01-slash-commands/](ja/01-slash-commands/)

**説明**: Markdown ファイルとして保存されるユーザー起動型ショートカット

**例**:
- `optimize.md` - コード最適化分析
- `pr.md` - プルリクエスト準備
- `generate-api-docs.md` - API ドキュメントジェネレータ

**インストール**:
```bash
cp 01-slash-commands/*.md /path/to/project/.claude/commands/
```

**使用方法**:
```
/optimize
/pr
/generate-api-docs
```

**詳細**: [Claude Code スラッシュコマンドの発見](https://medium.com/@luongnv89/discovering-claude-code-slash-commands-cdc17f0dfb29)

</details>

<details>
<summary>02. メモリ</summary>

**場所**: [ja/02-memory/](ja/02-memory/)

**説明**: セッション間の永続的なコンテキスト

**例**:
- `project-CLAUDE.md` - チーム全体のプロジェクト標準
- `directory-api-CLAUDE.md` - ディレクトリ固有のルール
- `personal-CLAUDE.md` - 個人の好み

**インストール**:
```bash
# プロジェクトメモリ
cp 02-memory/project-CLAUDE.md /path/to/project/CLAUDE.md

# ディレクトリメモリ
cp 02-memory/directory-api-CLAUDE.md /path/to/project/src/api/CLAUDE.md

# 個人メモリ
cp 02-memory/personal-CLAUDE.md ~/.claude/CLAUDE.md
```

**使用方法**: Claude によって自動的にロード

</details>

<details>
<summary>03. スキル</summary>

**場所**: [ja/03-skills/](ja/03-skills/)

**説明**: 命令とスクリプトを備えた再利用可能で自動呼び出しされる機能

**例**:
- `code-review/` - スクリプト付きの包括的なコードレビュー
- `brand-voice/` - ブランドボイス一貫性チェッカー
- `doc-generator/` - API ドキュメントジェネレータ

**インストール**:
```bash
# 個人スキル
cp -r 03-skills/code-review ~/.claude/skills/

# プロジェクトスキル
cp -r 03-skills/code-review /path/to/project/.claude/skills/
```

**使用方法**: 関連する場合に自動呼び出し

</details>

<details>
<summary>04. サブエージェント</summary>

**場所**: [ja/04-subagents/](ja/04-subagents/)

**説明**: 分離されたコンテキストとカスタムプロンプトを備えた専門 AI アシスタント

**例**:
- `code-reviewer.md` - 包括的なコード品質分析
- `test-engineer.md` - テスト戦略とカバレッジ
- `documentation-writer.md` - テクニカルドキュメント
- `secure-reviewer.md` - セキュリティ重視のレビュー（読み取り専用）
- `implementation-agent.md` - 完全な機能実装

**インストール**:
```bash
cp 04-subagents/*.md /path/to/project/.claude/agents/
```

**使用方法**: メインエージェントによって自動委譲

</details>

<details>
<summary>05. MCP プロトコル</summary>

**場所**: [ja/05-mcp/](ja/05-mcp/)

**説明**: 外部ツール API にアクセスするための Model Context Protocol

**例**:
- `github-mcp.json` - GitHub 統合
- `database-mcp.json` - データベースクエリ
- `filesystem-mcp.json` - ファイル操作
- `multi-mcp.json` - 複数の MCP サーバー

**インストール**:
```bash
# 環境変数を設定
export GITHUB_TOKEN="your_token"
export DATABASE_URL="postgresql://..."

# CLI を介して MCP サーバーを追加
claude mcp add github -- npx -y @modelcontextprotocol/server-github

# または、プロジェクト .mcp.json に手動で追加（例については ja/05-mcp/ を参照）
```

**使用方法**: MCP ツールは設定後、Claude に自動的に利用可能になります

</details>

<details>
<summary>06. フック</summary>

**場所**: [ja/06-hooks/](ja/06-hooks/)

**説明**: Claude Code イベントに応答して自動的に実行されるイベント駆動シェルコマンド

**例**:
- `format-code.sh` - 書き込み前に自動フォーマット
- `pre-commit.sh` - コミット前にテストを実行
- `security-scan.sh` - セキュリティ問題をスキャン
- `log-bash.sh` - すべての bash コマンドをログに記録
- `validate-prompt.sh` - ユーザープロンプトを検証
- `notify-team.sh` - イベントでチームに通知を送信

**インストール**:
```bash
mkdir -p ~/.claude/hooks
cp 06-hooks/*.sh ~/.claude/hooks/
chmod +x ~/.claude/hooks/*.sh
```

`~/.claude/settings.json` でフックを設定:
```json
{
  "hooks": {
    "PreToolUse": [{
      "matcher": "Write",
      "hooks": ["~/.claude/hooks/format-code.sh"]
    }],
    "PostToolUse": [{
      "matcher": "Write",
      "hooks": ["~/.claude/hooks/security-scan.sh"]
    }]
  }
}
```

**使用方法**: フックはイベントで自動的に実行

**フックタイプ**（4 タイプ、25 イベント）:
- **ツールフック**: `PreToolUse`、`PostToolUse`、`PostToolUseFailure`、`PermissionRequest`
- **セッションフック**: `SessionStart`、`SessionEnd`、`Stop`、`StopFailure`、`SubagentStart`、`SubagentStop`
- **タスクフック**: `UserPromptSubmit`、`TaskCompleted`、`TaskCreated`、`TeammateIdle`
- **ライフサイクルフック**: `ConfigChange`、`CwdChanged`、`FileChanged`、`PreCompact`、`PostCompact`、`WorktreeCreate`、`WorktreeRemove`、`Notification`、`InstructionsLoaded`、`Elicitation`、`ElicitationResult`

</details>

<details>
<summary>07. プラグイン</summary>

**場所**: [ja/07-plugins/](ja/07-plugins/)

**説明**: コマンド、エージェント、MCP、フックのバンドルされたコレクション

**例**:
- `pr-review/` - 完全な PR レビューワークフロー
- `devops-automation/` - デプロイメントと監視
- `documentation/` - ドキュメント生成

**インストール**:
```bash
/plugin install pr-review
/plugin install devops-automation
/plugin install documentation
```

**使用方法**: バンドルされたスラッシュコマンドと機能を使用

</details>

<details>
<summary>08. チェックポイントと巻き戻し</summary>

**場所**: [ja/08-checkpoints/](ja/08-checkpoints/)

**説明**: 会話の状態を保存し、異なるアプローチを探索するために前のポイントに巻き戻す

**重要な概念**:
- **チェックポイント**: 会話状態のスナップショット
- **巻き戻し**: 前のチェックポイントに戻す
- **分岐ポイント**: 同じチェックポイントから複数のアプローチを探索

**使用方法**:
```
# チェックポイントはすべてのユーザープロンプトで自動作成
# 巻き戻すには、Esc を 2 回押すか、次のコマンドを使用:
/rewind

# その後、5 つのオプションから選択:
# 1. コードと会話を復元
# 2. 会話を復元
# 3. コードを復元
# 4. ここから要約
# 5. やめる
```

**ユースケース**:
- 異なる実装アプローチを試す
- ミスから回復する
- 安全な実験
- 代替ソリューションを比較する
- 異なるデザインの A/B テスト

</details>

<details>
<summary>09. 高度な機能</summary>

**場所**: [ja/09-advanced-features/](ja/09-advanced-features/)

**説明**: 複雑なワークフローとオートメーションの高度な機能

**含まれるもの**:
- **計画モード** — コーディング前に詳細な実装計画を作成
- **拡張思考** — 複雑な問題の深い推論（`Alt+T` / `Option+T` で切り替え）
- **バックグラウンドタスク** — ブロッキングなしに長い操作を実行
- **パーミッションモード** — `default`、`acceptEdits`、`plan`、`dontAsk`、`bypassPermissions`
- **ヘッドレスモード** — CI/CD で Claude Code を実行: `claude -p "テストを実行してレポートを生成"`
- **セッション管理** — `/resume`、`/rename`、`/fork`、`claude -c`、`claude -r`
- **設定** — `~/.claude/settings.json` で動作をカスタマイズ

完全な設定については [config-examples.json](../09-advanced-features/config-examples.json) を参照してください。

</details>

<details>
<summary>10. CLI リファレンス</summary>

**場所**: [ja/10-cli/](ja/10-cli/)

**説明**: Claude Code の完全なコマンドラインインターフェイスリファレンス

**クイック例**:
```bash
# インタラクティブモード
claude "explain this project"

# 印刷モード（非対話的）
claude -p "review this code"

# ファイルコンテンツを処理
cat error.log | claude -p "explain this error"

# スクリプト用の JSON 出力
claude -p --output-format json "list functions"

# セッションを再開
claude -r "feature-auth" "continue implementation"
```

**ユースケース**: CI/CD パイプラインの統合、スクリプトオートメーション、バッチ処理、マルチセッションワークフロー、カスタムエージェント設定

</details>

<details>
<summary>ワークフロー例</summary>

### 完全なコードレビューワークフロー

```markdown
# 使用: スラッシュコマンド + サブエージェント + メモリ + MCP

ユーザー: /review-pr

Claude:
1. プロジェクトメモリをロード（コーディング標準）
2. GitHub MCP 経由で PR をフェッチ
3. コードレビュアーサブエージェントに委譲
4. テストエンジニアサブエージェントに委譲
5. 調査結果を統合
6. 包括的なレビューを提供
```

### 自動ドキュメント生成

```markdown
# 使用: スキル + サブエージェント + メモリ

ユーザー: "認証モジュールの API ドキュメントを生成"

Claude:
1. プロジェクトメモリをロード（ドキュメント標準）
2. ドキュメント生成リクエストを検出
3. doc-generator スキルを自動呼び出し
4. API ドキュメンテーター サブエージェントに委譲
5. 例を含む包括的なドキュメントを作成
```

### DevOps デプロイメント

```markdown
# 使用: プラグイン + MCP + フック

ユーザー: /deploy production

Claude:
1. デプロイ前フックを実行（環境を検証）
2. デプロイメント専門家サブエージェントに委譲
3. Kubernetes MCP 経由でデプロイメントを実行
4. 進捗を監視
5. デプロイ後フックを実行（ヘルスチェック）
6. ステータスを報告
```

</details>

<details>
<summary>ディレクトリ構造</summary>

```
├── 01-slash-commands/
│   ├── optimize.md
│   ├── pr.md
│   ├── generate-api-docs.md
│   └── README.md
├── 02-memory/
│   ├── project-CLAUDE.md
│   ├── directory-api-CLAUDE.md
│   ├── personal-CLAUDE.md
│   └── README.md
├── 03-skills/
│   ├── code-review/
│   │   ├── SKILL.md
│   │   ├── scripts/
│   │   └── templates/
│   ├── brand-voice/
│   │   ├── SKILL.md
│   │   └── templates/
│   ├── doc-generator/
│   │   ├── SKILL.md
│   │   └── generate-docs.py
│   └── README.md
├── 04-subagents/
│   ├── code-reviewer.md
│   ├── test-engineer.md
│   ├── documentation-writer.md
│   ├── secure-reviewer.md
│   ├── implementation-agent.md
│   └── README.md
├── 05-mcp/
│   ├── github-mcp.json
│   ├── database-mcp.json
│   ├── filesystem-mcp.json
│   ├── multi-mcp.json
│   └── README.md
├── 06-hooks/
│   ├── format-code.sh
│   ├── pre-commit.sh
│   ├── security-scan.sh
│   ├── log-bash.sh
│   ├── validate-prompt.sh
│   ├── notify-team.sh
│   └── README.md
├── 07-plugins/
│   ├── pr-review/
│   ├── devops-automation/
│   ├── documentation/
│   └── README.md
├── 08-checkpoints/
│   ├── checkpoint-examples.md
│   └── README.md
├── 09-advanced-features/
│   ├── config-examples.json
│   ├── planning-mode-examples.md
│   └── README.md
├── 10-cli/
│   └── README.md
└── README.md (このファイル)
```

</details>

<details>
<summary>ベストプラクティス</summary>

### すること
- スラッシュコマンドで簡単に始める
- 機能を段階的に追加
- チーム標準にメモリを使用
- 設定をローカルで最初にテスト
- カスタム実装を文書化
- プロジェクト設定をバージョン管理
- チームとプラグインを共有

### しないこと
- 冗長な機能を作成しない
- 認証情報をハードコードしない
- ドキュメント化をスキップしない
- シンプルなタスクを複雑にしない
- セキュリティのベストプラクティスを無視しない
- 機密データをコミットしない

</details>

<details>
<summary>トラブルシューティング</summary>

### 機能がロードされない
1. ファイルの場所と命名を確認
2. YAML フロントマターの構文を検証
3. ファイルのアクセス許可を確認
4. Claude Code のバージョン互換性を確認

### MCP 接続に失敗した
1. 環境変数を検証
2. MCP サーバーのインストールを確認
3. 認証情報をテスト
4. ネットワーク接続を確認

### サブエージェントが委譲されない
1. ツールのアクセス許可を確認
2. エージェントの説明の明確性を検証
3. タスクの複雑さを確認
4. エージェントを独立してテスト

</details>

<details>
<summary>テスト</summary>

このプロジェクトには包括的な自動テストが含まれています:

- **ユニットテスト**: pytest を使用した Python テスト（Python 3.10、3.11、3.12）
- **コード品質**: Ruff を使用したリンティングとフォーマット
- **セキュリティ**: Bandit を使用した脆弱性スキャン
- **型チェック**: mypy を使用した静的型分析
- **ビルド検証**: EPUB 生成テスト
- **カバレッジ追跡**: Codecov 統合

```bash
# 開発依存関係をインストール
uv pip install -r requirements-dev.txt

# すべてのユニットテストを実行
pytest scripts/tests/ -v

# カバレッジレポート付きテストを実行
pytest scripts/tests/ -v --cov=scripts --cov-report=html

# コード品質チェックを実行
ruff check scripts/
ruff format --check scripts/

# セキュリティスキャンを実行
bandit -c pyproject.toml -r scripts/ --exclude scripts/tests/

# 型チェックを実行
mypy scripts/ --ignore-missing-imports
```

テストは `main`/`develop` へのすべてのプッシュおよび `main` への PR ごとに自動実行されます。詳細については [TESTING.md](../.github/TESTING.md) を参照してください。

</details>

<details>
<summary>EPUB 生成</summary>

オフラインでこのガイドを読みたいですか？EPUB 電子書籍を生成してください:

```bash
uv run scripts/build_epub.py
```

これにより、すべてのコンテンツとレンダリングされた Mermaid ダイアグラムを含む `claude-howto-guide.epub` が作成されます。

詳細については [scripts/README.md](../scripts/README.md) を参照してください。

</details>

<details>
<summary>貢献</summary>

問題を見つけたか、例を投稿したいですか？あなたの助けを歓迎します！

**詳細なガイドラインについては [CONTRIBUTING.md](../CONTRIBUTING.md) をお読みください:**
- 貢献の種類（例、ドキュメント、機能、バグ、フィードバック）
- 開発環境をセットアップする方法
- ディレクトリ構造とコンテンツを追加する方法
- ライティングガイドラインとベストプラクティス
- コミットと PR プロセス

**私たちのコミュニティ基準:**
- [CODE_OF_CONDUCT.md](../CODE_OF_CONDUCT.md) - 互いの扱い方
- [SECURITY.md](../SECURITY.md) - セキュリティポリシーと脆弱性報告

### セキュリティ問題の報告

セキュリティ脆弱性を発見した場合は、責任を持って報告してください:

1. **GitHub プライベート脆弱性報告を使用**: https://github.com/luongnv89/claude-howto/security/advisories
2. **または読む** [.github/SECURITY_REPORTING.md](../.github/SECURITY_REPORTING.md) 詳細な手順
3. **しない** セキュリティ脆弱性に対してパブリック問題を開く

クイックスタート:
1. リポジトリをフォークしてクローン
2. 説明的なブランチを作成（`add/feature-name`、`fix/bug`、`docs/improvement`）
3. ガイドラインに従って変更を加える
4. 明確な説明で PR を送信

**ヘルプが必要ですか？** イシューまたはディスカッションを開くと、プロセスをガイドします。

</details>

<details>
<summary>追加リソース</summary>

- [Claude Code ドキュメント](https://code.claude.com/docs/en/overview)
- [MCP プロトコル仕様](https://modelcontextprotocol.io)
- [スキルリポジトリ](https://github.com/luongnv89/skills) - すぐに使える便利なスキルのコレクション
- [Anthropic Cookbook](https://github.com/anthropics/anthropic-cookbook)
- [Boris Cherny の Claude Code ワークフロー](https://x.com/bcherny/status/2007179832300581177) - Claude Code の作成者は、彼の体系的なワークフロー：並列エージェント、共有 CLAUDE.md、計画モード、スラッシュコマンド、サブエージェント、自律的な長時間実行セッションの検証フックを共有しています。

</details>

---

## 貢献

貢献を歓迎します！開始方法の詳細については [Contributing Guide](../CONTRIBUTING.md) を参照してください。

---

## ライセンス

MIT ライセンス - [LICENSE](../LICENSE) を参照。使用、変更、配布は自由です。唯一の要件はライセンス通知を含めることです。

---

**最終更新**: 2026 年 4 月 9 日
**Claude Code バージョン**: 2.1.97
**互換モデル**: Claude Sonnet 4.6、Claude Opus 4.6、Claude Haiku 4.5
