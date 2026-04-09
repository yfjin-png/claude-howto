---
name: api-documentation-generator
description: ソース コードから包括的で正確なAPIドキュメンテーションを生成します。APIドキュメンテーションを作成または更新する場合、OpenAPI仕様を生成する場合、またはユーザーがAPIドキュメント、エンドポイント、またはドキュメンテーションについて言及する場合に使用します。
---

# APIドキュメンテーション ジェネレーター スキル

## 生成

- OpenAPI/Swagger仕様
- APIエンドポイント ドキュメンテーション
- SDK使用例
- 統合ガイド
- エラー コード リファレンス
- 認証ガイド

## ドキュメンテーション構造

### 各エンドポイント

```markdown
## GET /api/v1/users/:id

### 説明
このエンドポイントが実行する内容の簡潔な説明

### パラメーター

| 名前 | タイプ | 必須 | 説明 |
|------|------|----------|-------------|
| id | string | はい | ユーザーID |

### レスポンス

**200成功**
```json
{
  "id": "usr_123",
  "name": "John Doe",
  "email": "john@example.com",
  "created_at": "2025-01-15T10:30:00Z"
}
```

**404見つかりません**
```json
{
  "error": "USER_NOT_FOUND",
  "message": "ユーザーが存在しません"
}
```

### 例

**cURL**
```bash
curl -X GET "https://api.example.com/api/v1/users/usr_123" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

**JavaScript**
```javascript
const user = await fetch('/api/v1/users/usr_123', {
  headers: { 'Authorization': 'Bearer token' }
}).then(r => r.json());
```

**Python**
```python
response = requests.get(
    'https://api.example.com/api/v1/users/usr_123',
    headers={'Authorization': 'Bearer token'}
)
user = response.json()
```
```
