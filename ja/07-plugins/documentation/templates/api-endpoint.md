# [METHOD] /api/v1/[endpoint]

## 説明
このエンドポイントが何をするかの簡潔な説明。

## 認証
必要な認証方法（例：ベアラートークン）。

## パラメータ

### パスパラメータ
| 名前 | 型 | 必須 | 説明 |
|------|-----|------|------|
| id | string | はい | リソースID |

### クエリパラメータ
| 名前 | 型 | 必須 | 説明 |
|------|-----|------|------|
| page | integer | いいえ | ページ番号（デフォルト: 1） |
| limit | integer | いいえ | ページあたりのアイテム数（デフォルト: 20） |

### リクエストボディ
```json
{
  "field": "value"
}
```

## レスポンス

### 200 OK
```json
{
  "success": true,
  "data": {
    "id": "123",
    "name": "Example"
  }
}
```

### 400 Bad Request
```json
{
  "success": false,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Invalid input"
  }
}
```

### 404 Not Found
```json
{
  "success": false,
  "error": {
    "code": "NOT_FOUND",
    "message": "Resource not found"
  }
}
```

## 例

### cURL
```bash
curl -X GET "https://api.example.com/api/v1/endpoint" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Content-Type: application/json"
```

### JavaScript
```javascript
const response = await fetch('/api/v1/endpoint', {
  headers: {
    'Authorization': 'Bearer token',
    'Content-Type': 'application/json'
  }
});
const data = await response.json();
```

### Python
```python
import requests

response = requests.get(
    'https://api.example.com/api/v1/endpoint',
    headers={'Authorization': 'Bearer token'}
)
data = response.json()
```

## レート制限
- 認証されたユーザー: 1時間あたり1000リクエスト
- パブリックエンドポイント: 1時間あたり100リクエスト

## 関連エンドポイント
- [GET /api/v1/related](#)
- [POST /api/v1/related](#)
