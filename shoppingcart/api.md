# Shopping Cart API 文件

## 通用規則

- Base URL：`http://localhost:3000`
- 所有請求與回應皆使用 `Content-Type: application/json`
- 購物車透過 Cookie（`session_id`）識別使用者，無需登入
- 錯誤回應格式統一為 `{ "error": "錯誤訊息" }`

---

## 端點總覽

| 方法 | 路徑 | 說明 |
|------|------|------|
| GET | /api/products | 取得商品列表 |
| GET | /api/cart | 取得購物車內容 |
| POST | /api/cart/items | 加入商品至購物車 |
| PATCH | /api/cart/items/:itemId | 更新購物車明細數量 |
| POST | /api/checkout | 結帳 |

---

## 商品

### GET /api/products

取得所有商品列表。

**Request**
無需 body。

**Response 200**
```json
[
  {
    "id": 1,
    "name": "商品A",
    "price": "100.00",
    "stock": 10
  },
  {
    "id": 2,
    "name": "商品B",
    "price": "250.00",
    "stock": 5
  }
]
```

---

## 購物車

### GET /api/cart

取得目前使用者的購物車內容，合計由伺服器計算。

**Request**
無需 body。

**Response 200**
```json
{
  "id": 1,
  "items": [
    {
      "id": 3,
      "productId": 1,
      "name": "商品A",
      "price": "100.00",
      "quantity": 2
    }
  ],
  "total": "200.00"
}
```

---

### POST /api/cart/items

加入商品至購物車。若該商品已存在，自動累加數量。

**Request Body**
| 欄位 | 型別 | 必填 | 說明 |
|------|------|------|------|
| productId | integer | 是 | 商品 ID |
| quantity | integer | 是 | 加入數量（須 > 0） |

```json
{
  "productId": 1,
  "quantity": 2
}
```

**Response 200**
```json
{
  "id": 3,
  "cartId": 1,
  "productId": 1,
  "quantity": 2
}
```

**錯誤**
| 狀態碼 | 說明 |
|--------|------|
| 400 | 缺少 `productId` 或 `quantity`，或 `quantity` <= 0 |
| 404 | 商品不存在 |

---

### PATCH /api/cart/items/:itemId

更新購物車明細數量。數量設為 0 時自動移除該明細。

**路徑參數**
| 參數 | 說明 |
|------|------|
| itemId | CartItem ID |

**Request Body**
| 欄位 | 型別 | 必填 | 說明 |
|------|------|------|------|
| quantity | integer | 是 | 新數量（0 表示移除） |

```json
{
  "quantity": 3
}
```

**Response 200（更新數量）**
```json
{
  "id": 3,
  "quantity": 3
}
```

**Response 200（數量為 0，移除明細）**
```json
{
  "removed": true
}
```

**錯誤**
| 狀態碼 | 說明 |
|--------|------|
| 400 | 缺少 `quantity` |
| 404 | 明細不存在 |

---

## 結帳

### POST /api/checkout

建立訂單。伺服器重新計算金額後清空購物車明細。

**Request Body**
| 欄位 | 型別 | 必填 | 說明 |
|------|------|------|------|
| recipientName | string | 是 | 收件人姓名 |
| recipientAddress | string | 是 | 收件地址 |
| recipientPhone | string | 是 | 收件人電話 |

```json
{
  "recipientName": "王小明",
  "recipientAddress": "台北市信義區松仁路100號",
  "recipientPhone": "0912345678"
}
```

**Response 201**
```json
{
  "orderId": 1,
  "total": "200.00"
}
```

**錯誤**
| 狀態碼 | 說明 |
|--------|------|
| 400 | 缺少收件資料，或購物車為空 |
