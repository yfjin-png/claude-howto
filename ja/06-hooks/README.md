<picture>
  <source media="(prefers-color-scheme: dark)" srcset="../resources/logos/claude-howto-logo-dark.svg">
  <img alt="Claude How To" src="../resources/logos/claude-howto-logo.svg">
</picture>

# フック

フックは、Claude Codeセッション中の特定のイベントに応答して実行される自動化スクリプトです。自動化、検証、権限管理、カスタムワークフローを有効化。

## 概要

フックは、Claude Codeで特定のイベントが発生するときに自動的に実行される自動化アクション（シェルコマンド、HTTPウェブフック、LLMプロンプト、またはサブエージェント評価）です。JSONを入力として受け取り、終了コードとJSONアウトプットを通じて結果を通信します。

**主要機能:**
- イベント駆動型自動化
- JSONベースの入出力
- コマンド、プロンプト、HTTP、エージェントフックタイプのサポート
- ツール固有フック用のパターンマッチング

## コンフィグレーション

フックは設定ファイルで特定の構造で設定：

- `~/.claude/settings.json` - ユーザー設定（すべてのプロジェクト）
- `.claude/settings.json` - プロジェクト設定（共有可能、コミット可能）
- `.claude/settings.local.json` - ローカルプロジェクト設定（コミット不可）
- 管理ポリシー - 組織全体設定
- プラグイン`hooks/hooks.json` - プラグインスコープフック
- スキル/エージェントフロントマター - コンポーネント有効期間フック

### 基本的なコンフィグレーション構造

```json
{
  "hooks": {
    "EventName": [
      {
        "matcher": "ToolPattern",
        "hooks": [
          {
            "type": "command",
            "command": "your-command-here",
            "timeout": 60
          }
        ]
      }
    ]
  }
}
```

**主要フィールド:**

| フィールド | 説明 | 例 |
|-------|-------------|---------|
| `matcher` | ツール名にマッチするパターン（大文字小文字区別） | `"Write"`、`"Edit\|Write"`、`"*"` |
| `hooks` | フック定義の配列 | `[{ "type": "command", ... }]` |
| `type` | フックタイプ：`"command"`（bash）、`"prompt"`（LLM）、`"http"`（ウェブフック）、`"agent"`（サブエージェント） | `"command"` |
| `command` | シェルコマンド | `"$CLAUDE_PROJECT_DIR/.claude/hooks/format.sh"` |
| `timeout` | オプション　タイムアウト（秒）（デフォルト60） | `30` |
| `once` | `true`の場合、セッションあたり1回のみフックを実行 | `true` |

### マッチャー パターン

| パターン | 説明 | 例 |
|---------|-------------|---------|
| 正確な文字列 | 特定のツールにマッチ | `"Write"` |
| 正規表現パターン | 複数のツールにマッチ | `"Edit\|Write"` |
| ワイルドカード | すべてのツールにマッチ | `"*"`または`""` |
| MCPツール | サーバーとツールパターン | `"mcp__memory__.*"` |

**InstructionsLoaded マッチャー値:**

| マッチャー値 | 説明 |
|---------------|-------------|
| `session_start` | セッション開始時に指示がロード |
| `nested_traversal` | ネストされたディレクトリ走査中に指示がロード |
| `path_glob_match` | パスグロブパターンマッチング経由に指示がロード |

## フックタイプ

Claude Codeは4つのフックタイプをサポート：

### コマンドフック

デフォルトフックタイプ。シェルコマンドを実行し、JSONストディン/ストドアウトと終了コード経由で通信。

```json
{
  "type": "command",
  "command": "python3 \"$CLAUDE_PROJECT_DIR/.claude/hooks/validate.py\"",
  "timeout": 60
}
```

### HTTPフック

> v2.1.63で追加。

コマンドフック同じJSONインプットを受け取るリモートウェブフックエンドポイント。HTTPフックはJSONをURLにPOSTして、JSONレスポンスを受け取ります。HTTPフックはサンドボックス有効時にサンドボックス経由でルーティング。URLで環境変数補間にはセキュリティ用に明示的な`allowedEnvVars`リストが必須。

```json
{
  "hooks": {
    "PostToolUse": [{
      "type": "http",
      "url": "https://my-webhook.example.com/hook",
      "matcher": "Write"
    }]
  }
}
```

**主要プロパティ:**
- `"type": "http"` -- このHTTPフックであることを特定
- `"url"` -- ウェブフックエンドポイントURL
- サンドボックス有効時にサンドボックス経由でルーティング
- URLで環境変数補間のために明示的な`allowedEnvVars`リストが必須

### プロンプトフック

LLMで評価されるプロンプトで、フックコンテンツはClaudeが評価するプロンプト。主に`Stop`と`SubagentStop`イベント用に不智的タスク完了チェック。

```json
{
  "type": "prompt",
  "prompt": "Claudeが要求されたすべてのタスクを完了したかを評価。",
  "timeout": 30
}
```

LLMはプロンプトを評価して構造化決定を返します（詳細は[プロンプトベースフック](#プロンプトベースフック)を参照）。

### エージェントフック

条件を評価または複雑なチェックを実行するために専用エージェントを生成するサブエージェントベース検証フック。プロンプトフック（シングルターンLLM評価）とは異なり、エージェントフックはツールを使用してマルチステップ推論を実行できます。

```json
{
  "type": "agent",
  "prompt": "コード変更がアーキテクチャガイドラインに従うことを検証。関連デザインドキュメントをチェックして比較。",
  "timeout": 120
}
```

**主要プロパティ:**
- `"type": "agent"` -- エージェントフックであることを特定
- `"prompt"` -- サブエージェント用のタスク説明
- エージェントはツール（Read、Grep、Bash等）を使用して評価を実行可能
- プロンプトフック同じような構造化決定を返す

## フックイベント

Claude Codeは**26のフックイベント**をサポート：

| イベント | いつトリガー | マッチャー入力 | ブロック可能 | 一般的な使用 |
|-------|---------------|---------------|-----------|------------|
| **SessionStart** | セッション開始/再開/クリア/コンパクト | startup/resume/clear/compact | No | 環境セットアップ |
| **InstructionsLoaded** | CLAUDE.mdまたはルールファイル読み込み後 | （なし） | No | 指示を修正/フィルタ |
| **UserPromptSubmit** | ユーザープロンプトを送信 | （なし） | Yes | プロンプトを検証 |
| **PreToolUse** | ツール実行前 | ツール名 | Yes（許可/拒否/質問） | インプット検証、修正 |
| **PermissionRequest** | 権限ダイアログを表示 | ツール名 | Yes | 自動承認/拒否 |
| **PermissionDenied** | ユーザーが権限プロンプトを拒否 | ツール名 | No | ログ、分析、ポリシー実施 |
| **PostToolUse** | ツール成功後 | ツール名 | No | コンテキスト追加、フィードバック |
| **PostToolUseFailure** | ツール実行失敗 | ツール名 | No | エラー処理、ログ |
| **Notification** | 通知を送信 | 通知タイプ | No | カスタム通知 |
| **SubagentStart** | サブエージェント生成 | エージェント種別名 | No | サブエージェントセットアップ |
| **SubagentStop** | サブエージェント終了 | エージェント種別名 | Yes | サブエージェント検証 |
| **Stop** | Claudeがレスポンド完了 | （なし） | Yes | タスク完了チェック |
| **StopFailure** | APIエラーターンを終了 | （なし） | No | エラー復旧、ログ |
| **TeammateIdle** | エージェントチームチームメイト待機 | （なし） | Yes | チームメイト調整 |
| **TaskCompleted** | Task via TaskCreate完了とマーク | （なし） | Yes | ポストタスク処理 |
| **TaskCreated** | TaskCreate経由タスク作成 | （なし） | No | タスク追跡、ログ |
| **ConfigChange** | コンフィグファイルが変更 | （なし） | Yes（ポリシー除く） | コンフィグ更新に反応 |
| **CwdChanged** | 作業ディレクトリが変更 | （なし） | No | ディレクトリ固有セットアップ |
| **FileChanged** | 監視ファイルが変更 | （なし） | No | ファイル監視、リビルド |
| **PreCompact** | コンテキストコンパクション前 | manual/auto | No | プリコンパクト処理 |
| **PostCompact** | コンパクション完了後 | （なし） | No | ポストコンパクト処理 |
| **WorktreeCreate** | Worktreeが作成中 | （なし） | Yes（パス返す） | Worktree初期化 |
| **WorktreeRemove** | Worktreeが削除中 | （なし） | No | Worktreeクリーンアップ |
| **Elicitation** | MCPサーバーがユーザーインプットをリクエスト | （なし） | Yes | インプット検証 |
| **ElicitationResult** | ユーザーがエリシテーションに応答 | （なし） | Yes | レスポンス処理 |
| **SessionEnd** | セッション終了 | （なし） | No | クリーンアップ、最終ログ |

### PreToolUse

ツール実行前にClaudeがツールパラメータを作成してから処理。ツールインプットを検証または修正する場合に使用。

**コンフィグレーション:**
```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": "$CLAUDE_PROJECT_DIR/.claude/hooks/validate-bash.py"
          }
        ]
      }
    ]
  }
}
```

**一般的なマッチャー:** `Task`、`Bash`、`Glob`、`Grep`、`Read`、`Edit`、`Write`、`WebFetch`、`WebSearch`

**アウトプット制御:**
- `permissionDecision`：`"allow"`、`"deny"`、または`"ask"`
- `permissionDecisionReason`：決定の説明
- `updatedInput`：修正されたツールインプットパラメータ

### PostToolUse

ツール完了直後に実行。検証、ログ、またはClaudeへのコンテキスト返却用。

**コンフィグレーション:**
```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write|Edit",
        "hooks": [
          {
            "type": "command",
            "command": "$CLAUDE_PROJECT_DIR/.claude/hooks/security-scan.py"
          }
        ]
      }
    ]
  }
}
```

**アウトプット制御:**
- `"block"`決定はClaudeにフィードバックをプロンプト
- `additionalContext`：Claudeに追加されるコンテキスト

### UserPromptSubmit

ユーザープロンプトを送信した時、Claudeが処理する前に実行。

**コンフィグレーション:**
```json
{
  "hooks": {
    "UserPromptSubmit": [
      {
        "hooks": [
          {
            "type": "command",
            "command": "$CLAUDE_PROJECT_DIR/.claude/hooks/validate-prompt.py"
          }
        ]
      }
    ]
  }
}
```

**アウトプット制御:**
- `decision`：`"block"`で処理を防止
- `reason`：ブロック場合説明
- `additionalContext`：プロンプトに追加されるコンテキスト

### Stop と SubagentStop

Claudeがレスポンド完了（Stop）またはサブエージェント完了（SubagentStop）時に実行。不智的タスク完了チェック用のプロンプトベース評価をサポート。

**追加インプットフィールド:** `Stop`と`SubagentStop`フック両者は、停止前のClaudeまたはサブエージェントの最終メッセージを含むJSONインプット内に`last_assistant_message`フィールドを受け取ります。これはタスク完了を評価するのに有用。

**コンフィグレーション:**
```json
{
  "hooks": {
    "Stop": [
      {
        "hooks": [
          {
            "type": "prompt",
            "prompt": "Claudeが要求されたすべてのタスクを完了したかを評価。",
            "timeout": 30
          }
        ]
      }
    ]
  }
}
```

### SubagentStart

サブエージェント実行を開始するときに実行。マッチャー入力はエージェント種別名で、特定のサブエージェント種別にターゲットするフック。

**コンフィグレーション:**
```json
{
  "hooks": {
    "SubagentStart": [
      {
        "matcher": "code-review",
        "hooks": [
          {
            "type": "command",
            "command": "$CLAUDE_PROJECT_DIR/.claude/hooks/subagent-init.sh"
          }
        ]
      }
    ]
  }
}
```

### SessionStart

セッション開始またはレジューム時に実行。環境変数を永続化できます。

**マッチャー:** `startup`、`resume`、`clear`、`compact`

**特別機能:** `CLAUDE_ENV_FILE`を使用して環境変数を永続化（`CwdChanged`と`FileChanged`フックでも利用可能）：

```bash
#!/bin/bash
if [ -n "$CLAUDE_ENV_FILE" ]; then
  echo 'export NODE_ENV=development' >> "$CLAUDE_ENV_FILE"
fi
exit 0
```

### SessionEnd

セッション終了時にクリーンアップまたは最終ログを実行。終了をブロックできません。

**理由フィールド値:**
- `clear` - ユーザーがセッションをクリア
- `logout` - ユーザーがログアウト
- `prompt_input_exit` - ユーザーがプロンプトインプット経由で終了
- `other` - その他の理由

**コンフィグレーション:**
```json
{
  "hooks": {
    "SessionEnd": [
      {
        "hooks": [
          {
            "type": "command",
            "command": "\"$CLAUDE_PROJECT_DIR/.claude/hooks/session-cleanup.sh\""
          }
        ]
      }
    ]
  }
}
```

### 通知イベント

通知イベント用に更新されたマッチャー：
- `permission_prompt` - 権限リクエスト通知
- `idle_prompt` - 待機状態通知
- `auth_success` - 認証成功
- `elicitation_dialog` - ユーザーに表示されたダイアログ

## コンポーネント スコープフック

フックはスキル、エージェント、コマンドのフロントマターに特定コンポーネントにアタッチ可能：

**SKILL.md、agent.md、またはcommand.md内:**

```yaml
---
name: secure-operations
description: セキュリティチェック付き操作を実行
hooks:
  PreToolUse:
    - matcher: "Bash"
      hooks:
        - type: command
          command: "./scripts/check.sh"
          once: true  # セッションあたり1回のみ実行
---
```

**コンポーネントフック対応イベント:** `PreToolUse`、`PostToolUse`、`Stop`

これは関連コードをコンポーネントが使用するコンポーネント内で定義するフックを保つことを可能化。

### サブエージェント フロントマターでフック

サブエージェントのフロントマターで`Stop`フックを定義する場合、メインセッション停止時ではなく、そのサブエージェント完了時のみフックを火するように自動的に`SubagentStop`フックに変換。

```yaml
---
name: code-review-agent
description: 自動コードレビューサブエージェント
hooks:
  Stop:
    - hooks:
        - type: prompt
          prompt: "コードレビューが徹底的で完全であることを検証。"
  # 上記のStopフックはこのサブエージェント用にSubagentStopに自動変換
---
```

## PermissionRequest イベント

カスタムアウトプット形式で権限リクエストを処理：

```json
{
  "hookSpecificOutput": {
    "hookEventName": "PermissionRequest",
    "decision": {
      "behavior": "allow|deny",
      "updatedInput": {},
      "message": "カスタムメッセージ",
      "interrupt": false
    }
  }
}
```

## フックインプット と アウトプット

### JSONインプット（stdin経由）

すべてのフックはstdin経由でJSONインプットを受け取ります：

```json
{
  "session_id": "abc123",
  "transcript_path": "/path/to/transcript.jsonl",
  "cwd": "/current/working/directory",
  "permission_mode": "default",
  "hook_event_name": "PreToolUse",
  "tool_name": "Write",
  "tool_input": {
    "file_path": "/path/to/file.js",
    "content": "..."
  },
  "tool_use_id": "toolu_01ABC123...",
  "agent_id": "agent-abc123",
  "agent_type": "main",
  "worktree": "/path/to/worktree"
}
```

**一般的フィールド:**

| フィールド | 説明 |
|-------|-------------|
| `session_id` | 一意のセッション識別子 |
| `transcript_path` | 会話トランスクリプトファイルへのパス |
| `cwd` | 現在の作業ディレクトリ |
| `hook_event_name` | フックをトリガーしたイベント名 |
| `agent_id` | このフックを実行するエージェントの識別子 |
| `agent_type` | エージェント種別（`"main"`、サブエージェント種別名等） |
| `worktree` | エージェントが実行されているgit worktreeへのパス |

### 終了コード

| 終了コード | 意味 | 動作 |
|-----------|---------|----------|
| **0** | 成功 | 続行、JSONストドアウト解析 |
| **2** | ブロッキングエラー | オペレーション　ブロック、ステラーをエラーとして表示 |
| **その他** | 非ブロッキングエラー | 続行、ステラーを冗長モードで表示 |

### JSONアウトプット（stdout、終了コード0）

```json
{
  "continue": true,
  "stopReason": "停止中場合オプションメッセージ",
  "suppressOutput": false,
  "systemMessage": "オプション警告メッセージ",
  "hookSpecificOutput": {
    "hookEventName": "PreToolUse",
    "permissionDecision": "allow",
    "permissionDecisionReason": "ファイルは許可されたディレクトリ内",
    "updatedInput": {
      "file_path": "/modified/path.js"
    }
  }
}
```

## 環境変数

| 変数 | 利用可能性 | 説明 |
|----------|-------------|-------------|
| `CLAUDE_PROJECT_DIR` | すべてのフック | プロジェクトルートへの絶対パス |
| `CLAUDE_ENV_FILE` | SessionStart、CwdChanged、FileChanged | 環境変数を永続化するファイルパス |
| `CLAUDE_CODE_REMOTE` | すべてのフック | リモート環境で実行している場合`"true"` |
| `${CLAUDE_PLUGIN_ROOT}` | プラグインフック | プラグインディレクトリへのパス |
| `${CLAUDE_PLUGIN_DATA}` | プラグインフック | プラグインデータディレクトリへのパス |
| `CLAUDE_CODE_SESSIONEND_HOOKS_TIMEOUT_MS` | SessionEnd フック | SessionEnd フックの設定可能タイムアウト（ミリ秒）（デフォルトをオーバーライド） |

## プロンプト ベースフック

`Stop`と`SubagentStop`イベント用に、LLMベース評価を使用可能：

```json
{
  "hooks": {
    "Stop": [
      {
        "hooks": [
          {
            "type": "prompt",
            "prompt": "すべてのタスクが完了しているかをレビュー。決定を返す。",
            "timeout": 30
          }
        ]
      }
    ]
  }
}
```

**LLM レスポンス スキーマ:**
```json
{
  "decision": "approve",
  "reason": "すべてのタスクが正常に完了しました",
  "continue": false,
  "stopReason": "タスク完了"
}
```

## 例

### 例1：Bash コマンド バリデーター（PreToolUse）

**ファイル**: `.claude/hooks/validate-bash.py`

```python
#!/usr/bin/env python3
import json
import sys
import re

BLOCKED_PATTERNS = [
    (r"\brm\s+-rf\s+/", "危険なrm -rf /コマンドをブロック"),
    (r"\bsudo\s+rm", "sudo rmコマンドをブロック"),
]

def main():
    input_data = json.load(sys.stdin)

    tool_name = input_data.get("tool_name", "")
    if tool_name != "Bash":
        sys.exit(0)

    command = input_data.get("tool_input", {}).get("command", "")

    for pattern, message in BLOCKED_PATTERNS:
        if re.search(pattern, command):
            print(message, file=sys.stderr)
            sys.exit(2)  # 終了2 = ブロッキングエラー

    sys.exit(0)

if __name__ == "__main__":
    main()
```

**コンフィグレーション:**
```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": "python3 \"$CLAUDE_PROJECT_DIR/.claude/hooks/validate-bash.py\""
          }
        ]
      }
    ]
  }
}
```

### 例2：セキュリティスキャナー（PostToolUse）

**ファイル**: `.claude/hooks/security-scan.py`

```python
#!/usr/bin/env python3
import json
import sys
import re

SECRET_PATTERNS = [
    (r"password\s*=\s*['\"][^'\"]+['\"]", "ハードコードされたパスワード可能性"),
    (r"api[_-]?key\s*=\s*['\"][^'\"]+['\"]", "ハードコードされたAPIキー可能性"),
]

def main():
    input_data = json.load(sys.stdin)

    tool_name = input_data.get("tool_name", "")
    if tool_name not in ["Write", "Edit"]:
        sys.exit(0)

    tool_input = input_data.get("tool_input", {})
    content = tool_input.get("content", "") or tool_input.get("new_string", "")
    file_path = tool_input.get("file_path", "")

    warnings = []
    for pattern, message in SECRET_PATTERNS:
        if re.search(pattern, content, re.IGNORECASE):
            warnings.append(message)

    if warnings:
        output = {
            "hookSpecificOutput": {
                "hookEventName": "PostToolUse",
                "additionalContext": f"{file_path}セキュリティ警告: " + "; ".join(warnings)
            }
        }
        print(json.dumps(output))

    sys.exit(0)

if __name__ == "__main__":
    main()
```

### 例3：自動フォーマットコード（PostToolUse）

**ファイル**: `.claude/hooks/format-code.sh`

```bash
#!/bin/bash

# 標準入力からJSONを読み取る
INPUT=$(cat)
TOOL_NAME=$(echo "$INPUT" | python3 -c "import sys, json; print(json.load(sys.stdin).get('tool_name', ''))")
FILE_PATH=$(echo "$INPUT" | python3 -c "import sys, json; print(json.load(sys.stdin).get('tool_input', {}).get('file_path', ''))")

if [ "$TOOL_NAME" != "Write" ] && [ "$TOOL_NAME" != "Edit" ]; then
    exit 0
fi

# ファイル拡張子に基づいてフォーマット
case "$FILE_PATH" in
    *.js|*.jsx|*.ts|*.tsx|*.json)
        command -v prettier &>/dev/null && prettier --write "$FILE_PATH" 2>/dev/null
        ;;
    *.py)
        command -v black &>/dev/null && black "$FILE_PATH" 2>/dev/null
        ;;
    *.go)
        command -v gofmt &>/dev/null && gofmt -w "$FILE_PATH" 2>/dev/null
        ;;
esac

exit 0
```

### 例4：プロンプト バリデーター（UserPromptSubmit）

**ファイル**: `.claude/hooks/validate-prompt.py`

```python
#!/usr/bin/env python3
import json
import sys
import re

BLOCKED_PATTERNS = [
    (r"delete\s+(all\s+)?database", "危険：データベース削除"),
    (r"rm\s+-rf\s+/", "危険：ルート削除"),
]

def main():
    input_data = json.load(sys.stdin)
    prompt = input_data.get("user_prompt", "") or input_data.get("prompt", "")

    for pattern, message in BLOCKED_PATTERNS:
        if re.search(pattern, prompt, re.IGNORECASE):
            output = {
                "decision": "block",
                "reason": f"ブロック：{message}"
            }
            print(json.dumps(output))
            sys.exit(0)

    sys.exit(0)

if __name__ == "__main__":
    main()
```

### 例5：不智的 Stop フック（プロンプトベース）

```json
{
  "hooks": {
    "Stop": [
      {
        "hooks": [
          {
            "type": "prompt",
            "prompt": "Claudeが要求されたすべてのタスクを完了したかをレビュー。チェック：1）すべてのファイルが作成/修正されたか？2）解決されなかったエラーはあるか？不完全な場合、欠落しているものを説明。",
            "timeout": 30
          }
        ]
      }
    ]
  }
}
```

### 例6：コンテキスト使用トラッカー（フックペア）

`UserPromptSubmit`（プリメッセージ）と`Stop`（ポストレスポンス）フック一緒に使用してリクエストあたりのトークン消費を追跡。

**ファイル**: `.claude/hooks/context-tracker.py`

```python
#!/usr/bin/env python3
"""
コンテキスト使用トラッカー - リクエストあたりのトークン消費を追跡。

UserPromptSubmitを「プリメッセージ」フック、Stopを「ポストレスポンス」フックとして
使用して、各リクエストのトークン使用量のデルタを計算。

トークンカウントメソッド：
1. 文字推定（デフォルト）：~4文字/トークン、依存なし
2. tiktoken（オプション）：より正確（~90-95%）、必須：pip install tiktoken
"""
import json
import os
import sys
import tempfile

# コンフィグレーション
CONTEXT_LIMIT = 128000  # Claudeのコンテキストウィンドウ（モデル用に調整）
USE_TIKTOKEN = False    # tiktoken インストール済みの場合Trueに設定してより正確さ


def get_state_file(session_id: str) -> str:
    """プリメッセージトークンカウント保存用テンプファイルパスを取得、セッションで分離。"""
    return os.path.join(tempfile.gettempdir(), f"claude-context-{session_id}.json")


def count_tokens(text: str) -> int:
    """
    テキストのトークンをカウント。

    利用可能（~90-95%正確）ならp50k_baseエンコードでtiktokenを使用、
    そうでない場合は文字推定（~80-90%正確）にフォールバック。
    """
    if USE_TIKTOKEN:
        try:
            import tiktoken
            enc = tiktoken.get_encoding("p50k_base")
            return len(enc.encode(text))
        except ImportError:
            pass  # 推定にフォールバック

    # 文字ベース推定：英語で~4文字/トークン
    return len(text) // 4


def read_transcript(transcript_path: str) -> str:
    """トランスクリプトファイルからすべてのコンテンツを読んで連結。"""
    if not transcript_path or not os.path.exists(transcript_path):
        return ""

    content = []
    with open(transcript_path, "r") as f:
        for line in f:
            try:
                entry = json.loads(line.strip())
                # 各種メッセージ形式からテキストコンテンツを抽出
                if "message" in entry:
                    msg = entry["message"]
                    if isinstance(msg.get("content"), str):
                        content.append(msg["content"])
                    elif isinstance(msg.get("content"), list):
                        for block in msg["content"]:
                            if isinstance(block, dict) and block.get("type") == "text":
                                content.append(block.get("text", ""))
            except json.JSONDecodeError:
                continue

    return "\n".join(content)


def handle_user_prompt_submit(data: dict) -> None:
    """プリメッセージフック：リクエスト前に現在のトークンカウントを保存。"""
    session_id = data.get("session_id", "unknown")
    transcript_path = data.get("transcript_path", "")

    transcript_content = read_transcript(transcript_path)
    current_tokens = count_tokens(transcript_content)

    # 後での比較用にテンプファイルに保存
    state_file = get_state_file(session_id)
    with open(state_file, "w") as f:
        json.dump({"pre_tokens": current_tokens}, f)


def handle_stop(data: dict) -> None:
    """ポストレスポンスフック：トークンデルタを計算してレポート。"""
    session_id = data.get("session_id", "unknown")
    transcript_path = data.get("transcript_path", "")

    transcript_content = read_transcript(transcript_path)
    current_tokens = count_tokens(transcript_content)

    # プリメッセージカウントを読み込む
    state_file = get_state_file(session_id)
    pre_tokens = 0
    if os.path.exists(state_file):
        try:
            with open(state_file, "r") as f:
                state = json.load(f)
                pre_tokens = state.get("pre_tokens", 0)
        except (json.JSONDecodeError, IOError):
            pass

    # デルタを計算
    delta_tokens = current_tokens - pre_tokens
    remaining = CONTEXT_LIMIT - current_tokens
    percentage = (current_tokens / CONTEXT_LIMIT) * 100

    # 使用をレポート
    method = "tiktoken" if USE_TIKTOKEN else "estimated"
    print(f"コンテキスト（{method}）：~{current_tokens:,}トークン（{percentage:.1f}%使用、~{remaining:,}残）", file=sys.stderr)
    if delta_tokens > 0:
        print(f"このリクエスト：~{delta_tokens:,}トークン", file=sys.stderr)


def main():
    data = json.load(sys.stdin)
    event = data.get("hook_event_name", "")

    if event == "UserPromptSubmit":
        handle_user_prompt_submit(data)
    elif event == "Stop":
        handle_stop(data)

    sys.exit(0)


if __name__ == "__main__":
    main()
```

**コンフィグレーション:**
```json
{
  "hooks": {
    "UserPromptSubmit": [
      {
        "hooks": [
          {
            "type": "command",
            "command": "python3 \"$CLAUDE_PROJECT_DIR/.claude/hooks/context-tracker.py\""
          }
        ]
      }
    ],
    "Stop": [
      {
        "hooks": [
          {
            "type": "command",
            "command": "python3 \"$CLAUDE_PROJECT_DIR/.claude/hooks/context-tracker.py\""
          }
        ]
      }
    ]
  }
}
```

**仕組み:**
1. `UserPromptSubmit`がプロンプト処理前に火る - 現在のトークンカウントを保存
2. `Stop`がClaudeレスポンス後に火る - デルタを計算してレポート
3. 各セッションはテンプファイル名の`session_id`経由で分離

**トークンカウントメソッド:**

| メソッド | 正確さ | 依存関係 | 速度 |
|--------|----------|--------------|-------|
| 文字推定 | ~80-90% | なし | <1ms |
| tiktoken（p50k_base） | ~90-95% | `pip install tiktoken` | <10ms |

> **注記**: Anthropicはオフライントークナイザーをリリースしていません。両者のメソッドは近似です。トランスクリプトはユーザープロンプト、Claudeレスポンス、ツールアウトプットを含みますが、システムプロンプトや内部コンテキストではなく。

### 例7：シード自動モード権限（ワンタイムセットアップスクリプト）

Claude Codeの自動モードベースラインと等価な~67安全権限ルールで`~/.claude/settings.json`をシード化するワンタイムセットアップスクリプト — フック、フューチャー選択肢を記憶することなし。1回実行すること；再実行は安全（既存ルールをスキップ）。

**ファイル**: `09-advanced-features/setup-auto-mode-permissions.py`

```bash
# 追加されるものをプレビュー
python3 09-advanced-features/setup-auto-mode-permissions.py --dry-run

# 適用
python3 09-advanced-features/setup-auto-mode-permissions.py
```

**追加されるもの:**

| カテゴリ | 例 |
|----------|---------|
| 組み込みツール | `Read(*)`、`Edit(*)`、`Write(*)`、`Glob(*)`、`Grep(*)`、`Agent(*)`、`WebSearch(*)` |
| Git読み | `Bash(git status:*)`、`Bash(git log:*)`、`Bash(git diff:*)` |
| Git書き（ローカル） | `Bash(git add:*)`、`Bash(git commit:*)`、`Bash(git checkout:*)` |
| パッケージマネージャー | `Bash(npm install:*)`、`Bash(pip install:*)`、`Bash(cargo build:*)` |
| ビルド & テスト | `Bash(make:*)`、`Bash(pytest:*)`、`Bash(go test:*)` |
| 一般的なシェル | `Bash(ls:*)`、`Bash(cat:*)`、`Bash(find:*)`、`Bash(cp:*)`、`Bash(mv:*)` |
| GitHub CLI | `Bash(gh pr view:*)`、`Bash(gh pr create:*)`、`Bash(gh issue list:*)` |

**意図的に除外**（このスクリプトで追加されない）:
- `rm -rf`、`sudo`、フォースプッシュ、`git reset --hard`
- `DROP TABLE`、`kubectl delete`、`terraform destroy`
- `npm publish`、`curl | bash`、本番デプロイ

## プラグイン フック

プラグインは`hooks/hooks.json`ファイルにフックを含める可能：

**ファイル**: `plugins/hooks/hooks.json`

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": "${CLAUDE_PLUGIN_ROOT}/scripts/validate.sh"
          }
        ]
      }
    ]
  }
}
```

**プラグインフック内の環境変数:**
- `${CLAUDE_PLUGIN_ROOT}` - プラグインディレクトリへのパス
- `${CLAUDE_PLUGIN_DATA}` - プラグインデータディレクトリへのパス

これにより、プラグインカスタム検証と自動化フックを含める。

## MCPツール フック

MCPツールは`mcp__<server>__<tool>`パターンに従う：

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "mcp__memory__.*",
        "hooks": [
          {
            "type": "command",
            "command": "echo '{\"systemMessage\": \"メモリ操作がログに記録\"}'"
          }
        ]
      }
    ]
  }
}
```

## セキュリティ考慮

### 免責事項

**自己責任で使用**: フックは任意のシェルコマンドを実行します。以下について全責任：
- 設定するコマンド
- ファイルアクセス/修正権限
- 潜在的なデータ損失またはシステム破損
- 本番前に安全環境でフックをテスト

### セキュリティノート

- **ワークスペース信頼が必須:** `statusLine`と`fileSuggestion`フックアウトプットコマンドは、効果を取得前にワークスペース信頼受け入れが必須。
- **HTTPフックと環境変数:** HTTPフックは、URLで環境変数補間のために明示的な`allowedEnvVars`リストが必須。これは機密環境変数のリモートエンドポイントへの意図的なリークを防止。
- **管理設定ヒエラルキー:** `disableAllHooks`設定は、個別ユーザーがオーバーライドできない組織レベル設定を実施できる管理設定ヒエラルキーを尊重。

### ベストプラクティス

| する | しない |
|-----|-------|
| すべてのインプットを検証してサニタイズ | インプットデータを盲目的に信頼 |
| シェル変数をクォート：`"$VAR"` | クォートなし：`$VAR` |
| パストラバーサルをブロック（`..`） | 任意パスを許可 |
| `$CLAUDE_PROJECT_DIR`で絶対パスを使用 | パスをハードコード |
| 機密ファイルをスキップ（`.env`、`.git/`、キー） | すべてのファイルを処理 |
| 分離でフックを最初にテスト | テストされていないフックをデプロイ |
| HTTPフック用に明示的な`allowedEnvVars` | すべての環境変数をウェブフックに公開 |

## デバッグ

### デバッグモードを有効化

詳細なフックログ用にデバッグフラグ付きClaudeを実行：

```bash
claude --debug
```

### 冗長モード

Claude Code内で`Ctrl+O`を使用して冗長モードを有効化してフック実行進捗を確認。

### フックを独立でテスト

```bash
# サンプルJSONインプットでテスト
echo '{"tool_name": "Bash", "tool_input": {"command": "ls -la"}}' | python3 .claude/hooks/validate-bash.py

# 終了コードをチェック
echo $?
```

## 完全なコンフィグレーション例

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": "python3 \"$CLAUDE_PROJECT_DIR/.claude/hooks/validate-bash.py\"",
            "timeout": 10
          }
        ]
      }
    ],
    "PostToolUse": [
      {
        "matcher": "Write|Edit",
        "hooks": [
          {
            "type": "command",
            "command": "\"$CLAUDE_PROJECT_DIR/.claude/hooks/format-code.sh\"",
            "timeout": 30
          },
          {
            "type": "command",
            "command": "python3 \"$CLAUDE_PROJECT_DIR/.claude/hooks/security-scan.py\"",
            "timeout": 10
          }
        ]
      }
    ],
    "UserPromptSubmit": [
      {
        "hooks": [
          {
            "type": "command",
            "command": "python3 \"$CLAUDE_PROJECT_DIR/.claude/hooks/validate-prompt.py\""
          }
        ]
      }
    ],
    "SessionStart": [
      {
        "matcher": "startup",
        "hooks": [
          {
            "type": "command",
            "command": "\"$CLAUDE_PROJECT_DIR/.claude/hooks/session-init.sh\""
          }
        ]
      }
    ],
    "Stop": [
      {
        "hooks": [
          {
            "type": "prompt",
            "prompt": "停止前にすべてのタスクが完了したことを検証。",
            "timeout": 30
          }
        ]
      }
    ]
  }
}
```

## フック実行詳細

| 側面 | 動作 |
|--------|----------|
| **タイムアウト** | 60秒デフォルト、コマンドごとに設定可能 |
| **並列化** | マッチするすべてのフックが並列実行 |
| **重複排除** | 同一フックコマンドが重複排除 |
| **環境** | 現在のディレクトリ内でClaude Codeの環境で実行 |

## トラブルシューティング

### フックが実行されない
- JSONコンフィグレーション構文が正しいことを検証
- マッチャーパターンがツール名にマッチすることをチェック
- スクリプトが存在して実行可能であることを確認：`chmod +x script.sh`
- `claude --debug`を実行してフック実行ログを確認
- フックがステディン（コマンド引数ではなく）からJSONを読むことを検証

### フックが予期せずブロック
- サンプルJSONでフックをテスト：`echo '{"tool_name": "Write", ...}' | ./hook.py`
- 終了コードをチェック：許可用に0、ブロック用に2であるべき
- ステラーアウトプットをチェック（終了コード2で表示）

### JSONパースエラー
- 常にステディンから読む、コマンド引数からではなく
- 適切なJSONパース（文字列操作ではなく）を使用
- 欠落フィールドを優雅に処理

## インストール

### ステップ1：フックディレクトリを作成
```bash
mkdir -p ~/.claude/hooks
```

### ステップ2：フック例をコピー
```bash
cp 06-hooks/*.sh ~/.claude/hooks/
chmod +x ~/.claude/hooks/*.sh
```

### ステップ3：設定で設定
上記に示されるフックコンフィグレーションで`~/.claude/settings.json`または`.claude/settings.json`を編集。

## 関連概念

- **[チェックポイントと巻き戻し](../08-checkpoints/)** - 会話状態を保存して復元
- **[スラッシュコマンド](../01-slash-commands/)** - カスタムスラッシュコマンドを作成
- **[スキル](../03-skills/)** - 再利用可能な自律機能
- **[サブエージェント](../04-subagents/)** - 委託されたタスク実行
- **[プラグイン](../07-plugins/)** - バンドルされた拡張パッケージ
- **[高度な機能](../09-advanced-features/)** - Claude Code高度な機能を探索

## 追加リソース

- **[公式フックドキュメント](https://code.claude.com/docs/en/hooks)** - 完全なフックリファレンス
- **[CLIリファレンス](https://code.claude.com/docs/en/cli-reference)** - コマンドラインインターフェースドキュメント
- **[メモリガイド](../02-memory/)** - 永続コンテキスト設定

---
**最終更新**: 2026年4月9日
**Claude Codeバージョン**: 2.1.97
**互換モデル**: Claude Sonnet 4.6、Claude Opus 4.6、Claude Haiku 4.5
