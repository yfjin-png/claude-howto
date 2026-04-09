<picture>
  <source media="(prefers-color-scheme: dark)" srcset="../resources/logos/claude-howto-logo-dark.svg">
  <img alt="Claude How To" src="../resources/logos/claude-howto-logo.svg">
</picture>

# MCP（モデルコンテキストプロトコル）

このフォルダは、Claude CodeでのMCPサーバー設定と使用に関する包括的なドキュメントと例を含みます。

## 概要

MCP（モデルコンテキストプロトコル）は、Claudeが外部ツール、API、リアルタイムデータソースにアクセスするための標準化された方法です。メモリとは異なり、MCPは変更データへのライブアクセスを提供します。

主要な特性：
- 外部サービスへのリアルタイムアクセス
- ライブデータ同期
- 拡張可能なアーキテクチャ
- セキュアな認証
- ツールベースのインタラクション

## MCPアーキテクチャ

```mermaid
graph TB
    A["Claude"]
    B["MCPサーバー"]
    C["外部サービス"]

    A -->|リクエスト：list_issues| B
    B -->|クエリ| C
    C -->|データ| B
    B -->|レスポンス| A

    A -->|リクエスト：create_issue| B
    B -->|アクション| C
    C -->|結果| B
    B -->|レスポンス| A

    style A fill:#e1f5fe,stroke:#333,color:#333
    style B fill:#f3e5f5,stroke:#333,color:#333
    style C fill:#e8f5e9,stroke:#333,color:#333
```

## MCPエコシステム

```mermaid
graph TB
    A["Claude"] -->|MCP| B["ファイルシステム<br/>MCPサーバー"]
    A -->|MCP| C["GitHub<br/>MCPサーバー"]
    A -->|MCP| D["データベース<br/>MCPサーバー"]
    A -->|MCP| E["Slack<br/>MCPサーバー"]
    A -->|MCP| F["Google Docs<br/>MCPサーバー"]

    B -->|ファイルI/O| G["ローカルファイル"]
    C -->|API| H["GitHubリポジトリ"]
    D -->|クエリ| I["PostgreSQL/MySQL"]
    E -->|メッセージ| J["Slackワークスペース"]
    F -->|ドキュメント| K["Google Drive"]

    style A fill:#e1f5fe,stroke:#333,color:#333
    style B fill:#f3e5f5,stroke:#333,color:#333
    style C fill:#f3e5f5,stroke:#333,color:#333
    style D fill:#f3e5f5,stroke:#333,color:#333
    style E fill:#f3e5f5,stroke:#333,color:#333
    style F fill:#f3e5f5,stroke:#333,color:#333
    style G fill:#e8f5e9,stroke:#333,color:#333
    style H fill:#e8f5e9,stroke:#333,color:#333
    style I fill:#e8f5e9,stroke:#333,color:#333
    style J fill:#e8f5e9,stroke:#333,color:#333
    style K fill:#e8f5e9,stroke:#333,color:#333
```

## MCP インストール方法

Claude Codeは、MCPサーバー接続のための複数のトランスポートプロトコルをサポート：

### HTTPトランスポート（推奨）

```bash
# 基本的なHTTP接続
claude mcp add --transport http notion https://mcp.notion.com/mcp

# 認証ヘッダー付きHTTP
claude mcp add --transport http secure-api https://api.example.com/mcp \
  --header "Authorization: Bearer your-token"
```

### Stdioトランスポート（ローカル）

ローカルで実行されるMCPサーバー用：

```bash
# ローカルNode.jsサーバー
claude mcp add --transport stdio myserver -- npx @myorg/mcp-server

# 環境変数付き
claude mcp add --transport stdio myserver --env KEY=value -- npx server
```

### SSEトランスポート（非推奨）

サーバー送信イベントトランスポートは`http`に有利に非推奨ですが、引き続きサポートされます：

```bash
claude mcp add --transport sse legacy-server https://example.com/sse
```

### Windows固有の注記

ネイティブWindows（WSLではなく）では、npxコマンド用に`cmd /c`を使用：

```bash
claude mcp add --transport stdio my-server -- cmd /c npx -y @some/package
```

### OAuth 2.0認証

Claude CodeはOAuth 2.0が必須のMCPサーバーをサポート。MCPサーバーがOAuth対応に接続する場合、Claude Codeは認証フロー全体を処理：

```bash
# OAuth対応MCPサーバーに接続（対話的フロー）
claude mcp add --transport http my-service https://my-service.example.com/mcp

# 非対話的セットアップ用にOAuth認証情報を事前設定
claude mcp add --transport http my-service https://my-service.example.com/mcp \
  --client-id "your-client-id" \
  --client-secret "your-client-secret" \
  --callback-port 8080
```

| 機能 | 説明 |
|---------|-------------|
| **対話的OAuth** | `/mcp`を使用してブラウザベースのOAuthフローをトリガー |
| **事前設定されたOAuthクライアント** | Notion、Stripe等の一般的なサービス用の組み込みOAuthクライアント（v2.1.30以降） |
| **事前設定認証情報** | `--client-id`、`--client-secret`、`--callback-port`フラグ自動セットアップ用 |
| **トークンストレージ** | トークンはシステムキーチェーンに安全に保存 |
| **ステップアップ認証** | 特権操作用のステップアップ認証をサポート |
| **ディスカバリキャッシング** | OAuth発見メタデータは再接続を高速化するためにキャッシング |
| **メタデータオーバーライド** | `.mcp.json`の`oauth.authServerMetadataUrl`でデフォルトOAuthメタデータ発見をオーバーライド |

#### OAuth メタデータディスカバリをオーバーライド

MCPサーバーが標準的なOAuthメタデータエンドポイント（`/.well-known/oauth-authorization-server`）でエラーを返すが、動作するOIDCエンドポイントを公開する場合、特定のURLからOAuthメタデータを取得するようにClaude Codeに指示できます。サーバー設定の`oauth`オブジェクトで`authServerMetadataUrl`を設定：

```json
{
  "mcpServers": {
    "my-server": {
      "type": "http",
      "url": "https://mcp.example.com/mcp",
      "oauth": {
        "authServerMetadataUrl": "https://auth.example.com/.well-known/openid-configuration"
      }
    }
  }
}
```

URLは`https://`を使用する必要があります。このオプションはClaude Code v2.1.64以降が必須です。

### Claude.ai MCPコネクタ

Claude.aiアカウントで設定されたMCPサーバーはClaude Codeで自動的に利用可能です。つまり、Claude.aiウェブインターフェースで設定したMCP接続は、追加設定なしでアクセス可能になります。

Claude.ai MCPコネクタは`--print`モード（v2.1.83以降）でも利用可能で、非対話的でスクリプト化された使用を可能化：

Claude Code内でClaude.ai MCPサーバーを無効化するには、`ENABLE_CLAUDEAI_MCP_SERVERS`環境変数を`false`に設定：

```bash
ENABLE_CLAUDEAI_MCP_SERVERS=false claude
```

> **注記**: この機能はClaude.aiアカウントでログインしたユーザーのみ利用可能です。

## MCP セットアップ処理

```mermaid
sequenceDiagram
    participant User
    participant Claude as Claude Code
    participant Config as 設定ファイル
    participant Service as 外部サービス

    User->>Claude: /mcpタイプ
    Claude->>Claude: 利用可能なMCPサーバーをリスト
    Claude->>User: オプションを表示
    User->>Claude: GitHub MCPを選択
    Claude->>Config: 設定を更新
    Config->>Claude: 接続をアクティベート
    Claude->>Service: 接続をテスト
    Service-->>Claude: 認証成功
    Claude->>User: ✅ MCP接続完了！
```

## MCPツール検索

MCPツール説明がコンテキストウィンドウの10%を超える場合、Claude Codeは自動的にツール検索を有効化してモデルコンテキストに圧倒されることなく正しいツールを効率的に選択。

| 設定 | 値 | 説明 |
|---------|-------|-------------|
| `ENABLE_TOOL_SEARCH` | `auto`（デフォルト） | ツール説明がコンテキストの10%を超える場合、自動的に有効化 |
| `ENABLE_TOOL_SEARCH` | `auto:<N>` | `N`ツール用カスタム閾値で自動的に有効化 |
| `ENABLE_TOOL_SEARCH` | `true` | ツール数に関わらず常に有効化 |
| `ENABLE_TOOL_SEARCH` | `false` | 無効化；すべてのツール説明をフルで送信 |

> **注記**: ツール検索はSonnet 4以上またはOpus 4以上が必須。Haikuモデルはサポートされません。

## 動的ツール更新

Claude CodeはMCP `list_changed`通知をサポート。MCPサーバーが動的にツールを追加、削除、修正する場合、Claude Codeは更新を受け取ってツールリストを自動的に調整 — 再接続またはリスタートは不要。

## MCPアプリ

MCP AppsはMCP Apps最初の公式拡張で、MCPツール呼び出しがチャットインターフェースに直接レンダリングされる対話的UIコンポーネントを返すを有効化。プレーンテキストレスポンスではなく、MCPサーバーはダッシュボード、フォーム、データ視覚化、マルチステップワークフロー — すべて会話を離れることなくインラインに表示 — を提供できます。

## MCPエリシテーション

MCPサーバーは対話的ダイアログ経由で構造化された入力をユーザーにリクエストできます（v2.1.49以降）。これにより、MCPサーバーがワークフロー中の追加情報を求めることが可能 — 例えば、確認をプロンプト、オプション一覧から選択、必須フィールドを記入 — MCPサーバーインタラクションに相互作用を追加。

## ツール説明と指示キャップ

v2.1.84現在、Claude Codeは、MCPサーバーあたり**2KB上限**をツール説明と指示に強制。個別サーバーがツール定義を冗長にしてコンテキストを過度に消費するのを防ぎ、相互作用を効率的に保つ。

## MCPプロンプトをスラッシュコマンドとして

MCPサーバーはClaude Codeでスラッシュコマンドとして表示されるプロンプトを公開できます。プロンプトは命名規約を使用してアクセス可能：

```
/mcp__<server>__<prompt>
```

例えば、`github`というサーバーが`review`というプロンプトを公開する場合、`/mcp__github__review`として起動できます。

## サーバー重複排除

同じMCPサーバーが複数スコープ（ローカル、プロジェクト、ユーザー）で定義されている場合、ローカル設定が優先。これにより、紛争なしにローカルのカスタマイズでプロジェクトレベルまたはユーザーレベルのMCP設定をオーバーライドできます。

## MCPリソース@ メンション経由

`@`メンション構文を使用してプロンプト内でMCPリソースを直接参照：

```
@server-name:protocol://resource/path
```

例えば、特定のデータベースリソースを参照：

```
@database:postgres://mydb/users
```

これにより、Claudeが会話コンテキストの一部としてMCPリソースコンテンツをインラインで取得して含めることが可能。

## MCPスコープ

MCP設定は、異なる共有レベルを持つ異なるスコープに保存可能：

| スコープ | 場所 | 説明 | 共有対象 | 承認が必須 |
|-------|----------|-------------|-------------|------------------|
| **ローカル**（デフォルト） | `~/.claude.json`（プロジェクトパス下） | 現在のユーザー、現在のプロジェクトのみに限定（古いバージョンでは`project`と呼ばれた） | あなただけ | No |
| **プロジェクト** | `.mcp.json` | gitリポジトリにチェックイン | チームメンバー | Yes（初回使用時） |
| **ユーザー** | `~/.claude.json` | すべてのプロジェクト全体で利用可能（古いバージョンでは`global`と呼ばれた） | あなただけ | No |

### プロジェクトスコープを使用

プロジェクト固有のMCP設定を`.mcp.json`に保存：

```json
{
  "mcpServers": {
    "github": {
      "type": "http",
      "url": "https://api.github.com/mcp"
    }
  }
}
```

チームメンバーは初回利用時にプロジェクトMCP承認プロンプトを見ます。

## MCPコンフィグレーション管理

### MCPサーバーを追加

```bash
# HTTPベースサーバーを追加
claude mcp add --transport http github https://api.github.com/mcp

# ローカルstdioサーバーを追加
claude mcp add --transport stdio database -- npx @company/db-server

# すべてのMCPサーバーをリスト
claude mcp list

# 特定サーバーについて詳細を取得
claude mcp get github

# MCPサーバーを削除
claude mcp remove github

# プロジェクト固有の承認選択肢をリセット
claude mcp reset-project-choices

# Claude Desktopからインポート
claude mcp add-from-claude-desktop
```

## 利用可能MCPサーバー テーブル

| MCPサーバー | 目的 | 一般的なツール | 認証 | リアルタイム |
|------------|---------|--------------|------|-----------|
| **ファイルシステム** | ファイル操作 | read、write、delete | OSパーミッション | ✅ はい |
| **GitHub** | リポジトリ管理 | list_prs、create_issue、push | OAuth | ✅ はい |
| **Slack** | チーム通信 | send_message、list_channels | Token | ✅ はい |
| **データベース** | SQLクエリ | query、insert、update | 認証情報 | ✅ はい |
| **Google Docs** | ドキュメントアクセス | read、write、share | OAuth | ✅ はい |
| **Asana** | プロジェクト管理 | create_task、update_status | API Key | ✅ はい |
| **Stripe** | 支払いデータ | list_charges、create_invoice | API Key | ✅ はい |
| **メモリ** | 永続メモリ | store、retrieve、delete | ローカル | ❌ No |

## 実用例

### 例1：GitHub MCPコンフィグレーション

**ファイル**: `.mcp.json`（プロジェクトルート）

```json
{
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": ["@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_TOKEN": "${GITHUB_TOKEN}"
      }
    }
  }
}
```

**利用可能GitHub MCPツール:**

#### プルリクエスト管理
- `list_prs` - リポジトリ内のすべてのPRをリスト
- `get_pr` - diffを含むPR詳細を取得
- `create_pr` - 新しいPRを作成
- `update_pr` - PRの説明/タイトルを更新
- `merge_pr` - メインブランチにPRをマージ
- `review_pr` - レビューコメントを追加

**リクエスト例:**
```
/mcp__github__get_pr 456

# 戻り値：
タイトル：ダークモードサポートを追加
作成者：@alice
説明：CSSカスタムプロパティを使用してダークテーマを実装
ステータス：OPEN
レビュアー：@bob、@charlie
```

#### Issue管理
- `list_issues` - すべてのissueをリスト
- `get_issue` - issue詳細を取得
- `create_issue` - 新しいissueを作成
- `close_issue` - issueをクローズ
- `add_comment` - issueにコメントを追加

#### リポジトリ情報
- `get_repo_info` - リポジトリ詳細
- `list_files` - ファイルツリー構造
- `get_file_content` - ファイルコンテンツを読み取る
- `search_code` - コードベース全体を検索

#### コミット操作
- `list_commits` - コミット履歴
- `get_commit` - 特定コミット詳細
- `create_commit` - 新しいコミットを作成

**セットアップ**:
```bash
export GITHUB_TOKEN="your_github_token"
# またはCLIで直接追加：
claude mcp add --transport stdio github -- npx @modelcontextprotocol/server-github
```

### コンフィグレーション内の環境変数拡張

MCP設定は、フォールバックデフォルト付きの環境変数拡張をサポート。`${VAR}`と`${VAR:-default}`構文は以下フィールドで機能：`command`、`args`、`env`、`url`、`headers`。

```json
{
  "mcpServers": {
    "api-server": {
      "type": "http",
      "url": "${API_BASE_URL:-https://api.example.com}/mcp",
      "headers": {
        "Authorization": "Bearer ${API_KEY}",
        "X-Custom-Header": "${CUSTOM_HEADER:-default-value}"
      }
    },
    "local-server": {
      "command": "${MCP_BIN_PATH:-npx}",
      "args": ["${MCP_PACKAGE:-@company/mcp-server}"],
      "env": {
        "DB_URL": "${DATABASE_URL:-postgresql://localhost/dev}"
      }
    }
  }
}
```

変数はランタイムで拡張：
- `${VAR}` - 環境変数を使用、設定されていない場合エラー
- `${VAR:-default}` - 環境変数を使用、設定されていない場合デフォルトにフォールバック

### 例2：データベース MCP セットアップ

**コンフィグレーション:**

```json
{
  "mcpServers": {
    "database": {
      "command": "npx",
      "args": ["@modelcontextprotocol/server-database"],
      "env": {
        "DATABASE_URL": "postgresql://user:pass@localhost/mydb"
      }
    }
  }
}
```

**使用例:**

```markdown
ユーザー：10を超えるオーダーを持つすべてのユーザーを取得

Claude：その情報についてデータベースをクエリします。

# MCPデータベースツールを使用：
SELECT u.*, COUNT(o.id) as order_count
FROM users u
LEFT JOIN orders o ON u.id = o.user_id
GROUP BY u.id
HAVING COUNT(o.id) > 10
ORDER BY order_count DESC;

# 結果：
- Alice：15オーダー
- Bob：12オーダー
- Charlie：11オーダー
```

**セットアップ**:
```bash
export DATABASE_URL="postgresql://user:pass@localhost/mydb"
# またはCLIで直接追加：
claude mcp add --transport stdio database -- npx @modelcontextprotocol/server-database
```

### 例3：マルチMCP ワークフロー

**シナリオ：日常レポート生成**

```markdown
# 複数MCPs使用した日常レポートワークフロー

## セットアップ
1. GitHub MCP - PR指標を取得
2. データベース MCP - 売上データをクエリ
3. Slack MCP - レポートをポスト
4. ファイルシステム MCP - レポートを保存

## ワークフロー

### ステップ1：GitHubデータを取得
/mcp__github__list_prs completed:true last:7days

アウトプット：
- 合計PR：42
- 平均マージ時間：2.3時間
- レビュー返答時間：1.1時間

### ステップ2：データベースをクエリ
SELECT COUNT(*) as sales, SUM(amount) as revenue
FROM orders
WHERE created_at > NOW() - INTERVAL '1 day'

アウトプット：
- 売上：247
- 収益：$12,450

### ステップ3：レポートを生成
データをHTMLレポートに結合

### ステップ4：ファイルシステムに保存
report.htmlを/reports/に書き込み

### ステップ5：Slackにポスト
#daily-reportsチャネルに概要を送信

最終アウトプット：
✅ レポート生成してポスト
📊 今週47のPRがマージされた
💰 日売上$12,450
```

**セットアップ**:
```bash
export GITHUB_TOKEN="your_github_token"
export DATABASE_URL="postgresql://user:pass@localhost/mydb"
export SLACK_TOKEN="your_slack_token"
# 各MCPサーバーをCLIで追加するか.mcp.jsonで設定
```

### 例4：ファイルシステム MCP操作

**コンフィグレーション:**

```json
{
  "mcpServers": {
    "filesystem": {
      "command": "npx",
      "args": ["@modelcontextprotocol/server-filesystem", "/home/user/projects"]
    }
  }
}
```

**利用可能操作:**

| 操作 | コマンド | 目的 |
|-----------|---------|---------|
| ファイルをリスト | `ls ~/projects` | ディレクトリコンテンツを表示 |
| ファイルを読み取る | `cat src/main.ts` | ファイルコンテンツを読み取る |
| ファイルを書き込む | `create docs/api.md` | 新しいファイルを作成 |
| ファイルを編集 | `edit src/app.ts` | ファイルを修正 |
| 検索 | `grep "async function"` | ファイルで検索 |
| 削除 | `rm old-file.js` | ファイルを削除 |

**セットアップ**:
```bash
# CLIで直接追加：
claude mcp add --transport stdio filesystem -- npx @modelcontextprotocol/server-filesystem /home/user/projects
```

## MCP vs メモリ：意思決定マトリックス

```mermaid
graph TD
    A["外部データが必須？"]
    A -->|No| B["メモリを使用"]
    A -->|Yes| C["頻繁に変更される？"]
    C -->|No/ほぼ| B
    C -->|Yes/多い| D["MCPを使用"]

    B -->|保存| E["設定<br/>コンテキスト<br/>履歴"]
    D -->|アクセス| F["ライブAPI<br/>データベース<br/>サービス"]

    style A fill:#fff3e0,stroke:#333,color:#333
    style B fill:#e1f5fe,stroke:#333,color:#333
    style C fill:#fff3e0,stroke:#333,color:#333
    style D fill:#f3e5f5,stroke:#333,color:#333
    style E fill:#e8f5e9,stroke:#333,color:#333
    style F fill:#e8f5e9,stroke:#333,color:#333
```

## リクエスト/レスポンスパターン

```mermaid
sequenceDiagram
    participant App as Claude
    participant MCP as MCPサーバー
    participant DB as データベース

    App->>MCP: リクエスト："SELECT * FROM users WHERE id=1"
    MCP->>DB: クエリを実行
    DB-->>MCP: 結果セット
    MCP-->>App: 解析されたデータを返す
    App->>App: 結果を処理
    App->>App: タスクを続行

    Note over MCP,DB: リアルタイムアクセス<br/>キャッシングなし
```

## 環境変数

環境変数に機密認証情報を保存：

```bash
# ~/.bashrcまたは~/.zshrc
export GITHUB_TOKEN="ghp_xxxxxxxxxxxxx"
export DATABASE_URL="postgresql://user:pass@localhost/mydb"
export SLACK_TOKEN="xoxb-xxxxxxxxxxxxx"
```

MCPコンフィグで参照：

```json
{
  "env": {
    "GITHUB_TOKEN": "${GITHUB_TOKEN}"
  }
}
```

## Claude をMCP サーバーとして（`claude mcp serve`）

Claude Code自体が他のアプリケーション用のMCPサーバーとして機能できます。これにより、外部ツール、エディタ、自動化システムが標準MCPプロトコルを通じてClaudeの機能を活用可能。

```bash
# Claude CodeをstdioでMCPサーバーとして開始
claude mcp serve
```

他のアプリケーションは、他のstdioベースMCPサーバーと同じようにこのサーバーに接続できます。例えば、別のClaude Codeインスタンス内にClaude Codeをマスサーバーとして追加：

```bash
claude mcp add --transport stdio claude-agent -- claude mcp serve
```

これは、1つのClaudeインスタンスが別のインスタンスを調整するマルチエージェントワークフロー構築に有用。

## 管理されたMCP設定（エンタープライズ）

エンタープライズデプロイメント用に、IT管理者は`managed-mcp.json`設定ファイルを通じてMCPサーバーポリシーを実施できます。このファイルは許可または禁止されるMCPサーバー上で、組織全体の排他的制御を提供。

**場所:**
- macOS：`/Library/Application Support/ClaudeCode/managed-mcp.json`
- Linux：`~/.config/ClaudeCode/managed-mcp.json`
- Windows：`%APPDATA%\ClaudeCode\managed-mcp.json`

**機能:**
- `allowedMcpServers` -- 許可されるサーバーのホワイトリスト
- `deniedMcpServers` -- 禁止されるサーバーのブロックリスト
- サーバー名、コマンド、URLパターン別マッチングをサポート
- ユーザー設定の前に強制される組織全体MCPポリシー
- 権限のないサーバー接続を防止

**コンフィグレーション例:**

```json
{
  "allowedMcpServers": [
    {
      "serverName": "github",
      "serverUrl": "https://api.github.com/mcp"
    },
    {
      "serverName": "company-internal",
      "serverCommand": "company-mcp-server"
    }
  ],
  "deniedMcpServers": [
    {
      "serverName": "untrusted-*"
    },
    {
      "serverUrl": "http://*"
    }
  ]
}
```

> **注記**: `allowedMcpServers`と`deniedMcpServers`の両者がサーバーにマッチしたら、deny ルールが優先。

## プラグイン提供MCPサーバー

プラグインは独自MCPサーバーをバンドルでき、プラグインがインストールされたら自動的に利用可能。プラグイン提供MCPサーバーは2つの方法で定義可能：

1. **スタンドアローン`.mcp.json`** -- プラグインルートディレクトリに`.mcp.json`ファイルを配置
2. **`plugin.json`内インライン** -- プラグインマニフェスト内にMCPサーバーを直接定義

`${CLAUDE_PLUGIN_ROOT}`変数を使用してプラグインのインストールディレクトリに対するパスを参照：

```json
{
  "mcpServers": {
    "plugin-tools": {
      "command": "node",
      "args": ["${CLAUDE_PLUGIN_ROOT}/dist/mcp-server.js"],
      "env": {
        "CONFIG_PATH": "${CLAUDE_PLUGIN_ROOT}/config.json"
      }
    }
  }
}
```

## サブエージェント スコープMCP

MCPサーバーはエージェントフロントマター内で`mcpServers:`キーを使用してインライン定義され、プロジェクト全体ではなく特定のサブエージェントにスコープされることができます。これは、エージェントが他のエージェントが必須としない特定MCPサーバーにアクセスが必要な場合に有用。

```yaml
---
mcpServers:
  my-tool:
    type: http
    url: https://my-tool.example.com/mcp
---

あなたは特化した操作用my-toolにアクセスするエージェント。
```

サブエージェントスコープMCPサーバーはそのエージェント実行コンテキスト内のみで利用可能で、親またはシブリングエージェントと共有されません。

## MCPアウトプット制限

Claude Codeはコンテキストオーバーフローを防ぐためMCPツールアウトプットに制限を強制：

| 制限 | 閾値 | 動作 |
|-------|-----------|----------|
| **警告** | 10,000トークン | アウトプットが大きいという警告が表示される |
| **デフォルト最大** | 25,000トークン | この制限を超えるアウトプットが切り詰められる |
| **ディスク永続性** | 50,000文字 | 50K文字を超えるツール結果はディスクに永続化 |

最大アウトプット制限は`MAX_MCP_OUTPUT_TOKENS`環境変数で設定可能：

```bash
# 最大アウトプットを50,000トークンに増加
export MAX_MCP_OUTPUT_TOKENS=50000
```

## コンテキストブロートを解決するコード実行

MCPの採用がスケールするにつれ、数十のサーバーから数百または数千のツールに接続すると、大きなチャレンジが生成：**コンテキストブロート**。これはおそらくMCPスケールでの最大の問題で、Anthropic's エンジニアリングチームが洗練されたソリューションを提案 — ダイレクトツール呼び出しの代わりにコード実行を使用。

> **ソース**: [MCPコード実行：より効率的なエージェント構築](https://www.anthropic.com/engineering/code-execution-with-mcp) — Anthropic エンジニアリングブログ

### 問題：トークン廃棄の2つのソース

**1. ツール定義がコンテキストウィンドウにオーバーロード**

ほとんどMCPクライアントはすべてのツール定義を事前に読み込みます。数千のツールに接続したら、モデルは、ユーザーのリクエストを読む前に数十万のトークンを処理する必須。

**2. 中間結果は追加トークンを消費**

ツール結果ごとにモデルのコンテキストを通じて渡されます。Google DriveからSalesforceへの会議トランスクリプト転送を検討 — フル文書は**2回**コンテキストを流れます：読むと書くとき。2時間会議トランスクリプトは50,000トークン以上の余分な意味かもしれません。

```mermaid
graph LR
    A["モデル"] -->|"ツール呼び出し：getDocument"| B["MCPサーバー"]
    B -->|"フル文書（50Kトークン）"| A
    A -->|"ツール呼び出し：updateRecord<br/>（フル文書を再送）"| B
    B -->|"確認"| A

    style A fill:#ffcdd2,stroke:#333,color:#333
    style B fill:#f3e5f5,stroke:#333,color:#333
```

### 解決：MCPツールをコードAPI として

ツール定義と結果をコンテキストウィンドウを通じて渡すのではなく、エージェントが、MCPツールをAPIとして呼び出す**コードを書く**。コードはサンドボックス実行環境で実行され、最終結果のみがモデルに返されます。

```mermaid
graph LR
    A["モデル"] -->|"コードを書く"| B["コード実行<br/>環境"]
    B -->|"ツールを直接呼び出す"| C["MCPサーバー"]
    C -->|"データは実行環境に<br/>留まる"| B
    B -->|"最終結果のみ<br/>（最小トークン）"| A

    style A fill:#c8e6c9,stroke:#333,color:#333
    style B fill:#e1f5fe,stroke:#333,color:#333
    style C fill:#f3e5f5,stroke:#333,color:#333
```

#### 仕組み

MCPツールは型指定関数のファイルツリーとして提示：

```
servers/
├── google-drive/
│   ├── getDocument.ts
│   └── index.ts
├── salesforce/
│   ├── updateRecord.ts
│   └── index.ts
└── ...
```

各ツールファイルは型指定ラッパーを含む：

```typescript
// ./servers/google-drive/getDocument.ts
import { callMCPTool } from "../../../client.js";

interface GetDocumentInput {
  documentId: string;
}

interface GetDocumentResponse {
  content: string;
}

export async function getDocument(
  input: GetDocumentInput
): Promise<GetDocumentResponse> {
  return callMCPTool<GetDocumentResponse>(
    'google_drive__get_document', input
  );
}
```

エージェントはツールを調整するコードを書く：

```typescript
import * as gdrive from './servers/google-drive';
import * as salesforce from './servers/salesforce';

// データはツール間で直接流れます — モデルを通じてはなく
const transcript = (
  await gdrive.getDocument({ documentId: 'abc123' })
).content;

await salesforce.updateRecord({
  objectType: 'SalesMeeting',
  recordId: '00Q5f000001abcXYZ',
  data: { Notes: transcript }
});
```

**結果：トークン使用が~150,000から~2,000に下がります — 98.7%削減。**

### 主要な利点

| 利点 | 説明 |
|---------|-------------|
| **プログレッシブ開示** | エージェントはファイルシステムを参照してすべてのツール事前読み込みではなく、必要なツール定義のみを読み込む |
| **コンテキスト効率結果** | データはアウトプットの前に実行環境でフィルタ/変換される |
| **強力な制御流** | ループ、条件文、エラーハンドリングがモデルにラウンドトリップするなしでコードで実行 |
| **プライバシー保護** | 中間データ（PII、機密記録）は実行環境に留まり、モデルコンテキストに入らない |
| **状態永続性** | エージェントは中間結果をファイルに保存し、再利用可能なスキル関数を構築できます |

#### 例：大きなデータセット フィルタリング

```typescript
// コード実行なし — すべての10,000行がコンテキストを流れます
// ツール呼び出し：gdrive.getSheet(sheetId: 'abc123')
//   -> コンテキストで10,000行を返す

// コード実行で — 実行環境でフィルタ
const allRows = await gdrive.getSheet({ sheetId: 'abc123' });
const pendingOrders = allRows.filter(
  row => row["Status"] === 'pending'
);
console.log(`Found ${pendingOrders.length} pending orders`);
console.log(pendingOrders.slice(0, 5)); // モデルに届くのは5行のみ
```

#### 例：ラウンドトリップなしループ

```typescript
// デプロイ通知をポーリング — 完全にコードで実行
let found = false;
while (!found) {
  const messages = await slack.getChannelHistory({
    channel: 'C123456'
  });
  found = messages.some(
    m => m.text.includes('deployment complete')
  );
  if (!found) await new Promise(r => setTimeout(r, 5000));
}
console.log('Deployment notification received');
```

### トレードオフを考慮

コード実行は独自の複雑性を導入。エージェント生成コード実行には以下が必須：

- 適切なリソース制限を備えた**セキュアなサンドボックス実行環境**
- 実行されたコードの**モニタリングとログ**
- ダイレクトツール呼び出しと比較して**追加インフラストラクチャオーバーヘッド**

利点 — 削減トークンコスト、低レイテンシ、改善ツール組成 — これらの実装コストに対して重量をつけるべき。少数MCPサーバーを持つエージェント用、ダイレクトツール呼び出しはよりシンプルでありうる。エージェントスケール用（数十のサーバー、数百のツール）、コード実行は大きな改善。

### MCPorter：MCP ツール組成ランタイム

[MCPorter](https://github.com/steipete/mcporter)はボイラープレートなしでMCPサーバー呼び出しを実用化するTypeScript ランタイムとCLI ツールキット — そして、選択的ツール公開とセット型ラッパーを通じてコンテキストブロートを削減に役立ちます。

**解決対象**: すべてのツール定義をすべてのMCPサーバーから事前読み込みする代わりに、MCPorterは発見、検査、オンデマンドで特定ツール呼び出しを可能 — コンテキストをリーンに保つ。

**主要機能:**

| 機能 | 説明 |
|---------|-------------|
| **ゼロコンフィグ発見** | Cursor、Claude、Codex、またはローカル設定からMCPサーバーを自動発見 |
| **型指定ツール クライアント** | `mcporter emit-ts`は`.d.ts`インターフェースと実行可能ラッパーを生成 |
| **組成可能API** | `createServerProxy()`は`.text()`、`.json()`、`.markdown()`ヘルパーを備えたcamelCase メソッドとしてツールを公開 |
| **CLI生成** | `mcporter generate-cli`すべてのMCPサーバーを`--include-tools` / `--exclude-tools`フィルタリング付きスタンドアロンCLIに変換 |
| **パラメータ隠蔽** | オプションパラメータはデフォルトで隠蔽状態、スキーマ詳細度を削減 |

**インストール:**

```bash
npx mcporter list          # インストール不要 — サーバーを即座に発見
pnpm add mcporter          # プロジェクトに追加
brew install steipete/tap/mcporter  # macOS Homebrewで
```

**例 — TypeScript でツールを組成：**

```typescript
import { createRuntime, createServerProxy } from "mcporter";

const runtime = await createRuntime();
const gdrive = createServerProxy(runtime, "google-drive");
const salesforce = createServerProxy(runtime, "salesforce");

// データはモデルコンテキストを通過することなくツール間で流れます
const doc = await gdrive.getDocument({ documentId: "abc123" });
await salesforce.updateRecord({
  objectType: "SalesMeeting",
  recordId: "00Q5f000001abcXYZ",
  data: { Notes: doc.text() }
});
```

**例 — CLIツール呼び出し：**

```bash
# 特定ツールを直接呼び出し
npx mcporter call linear.create_comment issueId:ENG-123 body:'Looks good!'

# 利用可能なサーバーとツールをリスト
npx mcporter list
```

MCPorterはコード実行アプローチをコンプリメント、MCPツールを型指定APIとして呼び出すランタイム インフラストラクチャを提供 — 中間データをモデルコンテキストから外へ保つのが簡単。

## ベストプラクティス

### セキュリティ考慮

#### する ✅
- 制御情報用に環境変数を使用
- トークンとAPIキーを定期的にローテーション（月間推奨）
- 可能な限り読み取り専用トークンを使用
- MCPサーバーアクセススコープを最小限に制限
- MCPサーバー使用とアクセスログを監視
- 利用可能な場合、外部サービスにはOAuthを使用
- MCPリクエストにレート制限を実装
- 本番前にMCP接続をテスト
- すべてのアクティブMCP接続をドキュメント化
- MCPサーバーパッケージを最新に保つ

#### しない ❌
- 設定ファイルで認証情報をハードコード
- トークンまたはシークレットをgitにコミット
- チャットまたはメールでトークンを共有
- チームプロジェクトに個人トークンを使用
- 不要な権限を付与
- 認証エラーを無視
- MCPエンドポイントを公開でさらす
- MCPサーバーをroot/adminパーミッションで実行
- ログで機密データをキャッシュ
- 認証メカニズムを無効化

### コンフィグレーション ベストプラクティス

1. **バージョン管理**: gitに`.mcp.json`を保つがシークレット用に環境変数を使用
2. **最小権限**: 各MCPサーバーに必要な最小権限を付与
3. **分離**: 可能な限り異なるプロセスで異なるMCPサーバーを実行
4. **モニタリング**: 監査追跡用にすべてのMCPリクエストとエラーをログ
5. **テスト**: 本番にデプロイ前にすべてのMCP設定をテスト

### パフォーマンス Tips

- アプリケーションレベルで頻繁にアクセスされるデータをキャッシュ
- データ転送を削減するためにMCPクエリが特定にさせる
- MCP操作のレスポンス時間を監視
- 外部API用にレート制限を検討
- 複数操作実行時にバッチ処理を使用

## インストール手順

### 前提条件
- Node.jsとnpmがインストール
- Claude Code CLIがインストール
- 外部サービス用のAPIトークン/認証情報

### ステップバイステップセットアップ

1. **最初のMCPサーバーを追加** CLIを使用して（例：GitHub）:
```bash
claude mcp add --transport stdio github -- npx @modelcontextprotocol/server-github
```

   またはプロジェクトルートに`.mcp.json`ファイルを作成：
```json
{
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": ["@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_TOKEN": "${GITHUB_TOKEN}"
      }
    }
  }
}
```

2. **環境変数を設定:**
```bash
export GITHUB_TOKEN="your_github_personal_access_token"
```

3. **接続をテスト:**
```bash
claude /mcp
```

4. **MCPツールを使用:**
```bash
/mcp__github__list_prs
/mcp__github__create_issue "タイトル" "説明"
```

### 特定サービス用インストール

**GitHub MCP:**
```bash
npm install -g @modelcontextprotocol/server-github
```

**データベース MCP:**
```bash
npm install -g @modelcontextprotocol/server-database
```

**ファイルシステム MCP:**
```bash
npm install -g @modelcontextprotocol/server-filesystem
```

**Slack MCP:**
```bash
npm install -g @modelcontextprotocol/server-slack
```

## トラブルシューティング

### MCPサーバーが見つからない
```bash
# MCPサーバーがインストール されているか検証
npm list -g @modelcontextprotocol/server-github

# 欠落している場合はインストール
npm install -g @modelcontextprotocol/server-github
```

### 認証失敗
```bash
# 環境変数が設定されているか検証
echo $GITHUB_TOKEN

# 必要に応じて再エクスポート
export GITHUB_TOKEN="your_token"

# GitHubトークンスコープが正しいことを確認
# GitHub設定でチェック：https://github.com/settings/tokens
```

### 接続タイムアウト
- ネットワーク接続を確認：`ping api.github.com`
- APIエンドポイントがアクセス可能か検証
- APIのレート制限をチェック
- 設定でタイムアウトを増加を試す
- ファイアウォールまたはプロキシの問題をチェック

### MCPサーバーがクラッシュ
- MCPサーバーログをチェック：`~/.claude/logs/`
- すべての環境変数が設定されていることを検証
- 適切なファイルパーミッションを確認
- MCPサーバーパッケージを再インストールを試す
- 同じポートの競合プロセスをチェック

## 関連概念

### メモリ vs MCP
- **メモリ**: 永続的で変わらないデータを保存（設定、コンテキスト、履歴）
- **MCP**: ライブで変わるデータにアクセス（API、データベース、リアルタイムサービス）

### いつそれぞれを使用
- **メモリ使用**: ユーザー設定、会話履歴、学習したコンテキスト
- **MCP使用**: 現在のGitHub issue、ライブデータベースクエリ、リアルタイムデータ

### 他のClaude機能への統合
- メモリとMCPを組み合わせて豊富なコンテキスト
- プロンプトでMCPツールを活用してより良い推論
- 複雑なワークフロー用に複数MCPを活用

## 追加リソース

- [公式MCPドキュメント](https://code.claude.com/docs/en/mcp)
- [MCPプロトコル仕様](https://modelcontextprotocol.io/specification)
- [MCPGitHubリポジトリ](https://github.com/modelcontextprotocol/servers)
- [利用可能MCPサーバー](https://github.com/modelcontextprotocol/servers)
- [MCPorter](https://github.com/steipete/mcporter) — MCPサーバーをボイラープレートなしで呼び出すTypeScript ランタイム & CLI
- [MCPコード実行](https://www.anthropic.com/engineering/code-execution-with-mcp) — コンテキストブロート解決のAnthropicエンジニアリングブログ
- [Claude Code CLIリファレンス](https://code.claude.com/docs/en/cli-reference)
- [Claude APIドキュメント](https://docs.anthropic.com)

---
**最終更新**: 2026年4月9日
**Claude Codeバージョン**: 2.1.97
**互換モデル**: Claude Sonnet 4.6、Claude Opus 4.6、Claude Haiku 4.5
