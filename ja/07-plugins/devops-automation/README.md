<picture>
  <source media="(prefers-color-scheme: dark)" srcset="../../../resources/logos/claude-howto-logo-dark.svg">
  <img alt="Claude How To" src="../../../resources/logos/claude-howto-logo.svg">
</picture>

# DevOps自動化プラグイン

デプロイ、モニタリング、インシデント対応のための完全なDevOps自動化です。

## 機能

✅ 自動デプロイ
✅ ロールバック手順
✅ システム健全性モニタリング
✅ インシデント対応ワークフロー
✅ Kubernetes統合

## インストール

```bash
/plugin install devops-automation
```

## 含まれるもの

### スラッシュコマンド
- `/deploy` - 本番またはステージングへのデプロイ
- `/rollback` - 前のバージョンへのロールバック
- `/status` - システム健全性をチェック
- `/incident` - 本番インシデント処理

### サブエージェント
- `deployment-specialist` - デプロイメント操作
- `incident-commander` - インシデント調整
- `alert-analyzer` - システム健全性分析

### MCPサーバー
- Kubernetes統合

### スクリプト
- `deploy.sh` - デプロイメント自動化
- `rollback.sh` - ロールバック自動化
- `health-check.sh` - ヘルスチェックユーティリティ

### フック
- `pre-deploy.js` - デプロイ前検証
- `post-deploy.js` - デプロイ後タスク

## 使用方法

### ステージングにデプロイ
```
/deploy staging
```

### 本番にデプロイ
```
/deploy production
```

### ロールバック
```
/rollback production
```

### ステータスチェック
```
/status
```

### インシデント処理
```
/incident
```

## 要件

- Claude Code 1.0+
- Kubernetes CLI (kubectl)
- クラスターアクセス設定済み

## 設定

Kubernetesの設定をセットアップ:
```bash
export KUBECONFIG=~/.kube/config
```

## ワークフロー例

```
ユーザー: /deploy production

Claude:
1. デプロイ前フックを実行（kubectlの検証、クラスター接続）
2. deployment-specialistサブエージェントに委譲
3. deploy.shスクリプトを実行
4. Kubernetes MCPを使用してデプロイ進捗を監視
5. デプロイ後フックを実行（Pod待機、スモークテスト）
6. デプロイメント概要を提供

結果:
✅ デプロイ完了
📦 バージョン: v2.1.0
🚀 ポッド: 3/3準備完了
⏱️  時間: 2分34秒
```
