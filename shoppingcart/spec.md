# Shopping Cart 規格文件

## 資料模型

### Product（商品）
| 欄位 | 型別 | 說明 |
|------|------|------|
| id | SERIAL PRIMARY KEY | 商品 ID |
| name | VARCHAR(255) NOT NULL | 商品名稱 |
| price | NUMERIC(10,2) NOT NULL | 單價 |
| stock | INTEGER NOT NULL DEFAULT 0 | 庫存數量 |
| created_at | TIMESTAMPTZ DEFAULT NOW() | 建立時間 |

### Cart（購物車）
| 欄位 | 型別 | 說明 |
|------|------|------|
| id | SERIAL PRIMARY KEY | 購物車 ID |
| session_id | VARCHAR(255) NOT NULL UNIQUE | 對應使用者 session |
| created_at | TIMESTAMPTZ DEFAULT NOW() | 建立時間 |

### CartItem（購物車明細）
| 欄位 | 型別 | 說明 |
|------|------|------|
| id | SERIAL PRIMARY KEY | 明細 ID |
| cart_id | INTEGER REFERENCES cart(id) | 所屬購物車 |
| product_id | INTEGER REFERENCES product(id) | 商品 |
| quantity | INTEGER NOT NULL CHECK (quantity > 0) | 數量 |

### Order（訂單）
| 欄位 | 型別 | 說明 |
|------|------|------|
| id | SERIAL PRIMARY KEY | 訂單 ID |
| cart_id | INTEGER REFERENCES cart(id) | 來源購物車 |
| recipient_name | VARCHAR(255) NOT NULL | 收件人姓名 |
| recipient_address | TEXT NOT NULL | 收件地址 |
| recipient_phone | VARCHAR(20) NOT NULL | 收件人電話 |
| total | NUMERIC(10,2) NOT NULL | 訂單金額（伺服器計算） |
| created_at | TIMESTAMPTZ DEFAULT NOW() | 建立時間 |

---

## API 規格

### 商品

#### `GET /api/products`
取得商品列表。

**Response 200**
```json
[
  { "id": 1, "name": "商品A", "price": "100.00", "stock": 10 }
]
```

---

### 購物車

#### `POST /api/cart/items`
加入商品至購物車。相同商品自動合併數量。

**Request**
```json
{ "productId": 1, "quantity": 2 }
```

**Response 200**
```json
{ "id": 3, "cartId": 1, "productId": 1, "quantity": 2 }
```

**錯誤**
- `400` — 缺少必要欄位
- `404` — 商品不存在

---

#### `GET /api/cart`
取得目前購物車內容，合計由伺服器計算。

**Response 200**
```json
{
  "id": 1,
  "items": [
    { "id": 3, "productId": 1, "name": "商品A", "price": "100.00", "quantity": 2 }
  ],
  "total": "200.00"
}
```

---

#### `PATCH /api/cart/items/:itemId`
更新商品數量。數量為 0 時自動移除該明細。

**Request**
```json
{ "quantity": 0 }
```

**Response 200**
```json
{ "removed": true }
```

**Response 200（更新數量）**
```json
{ "id": 3, "quantity": 3 }
```

**錯誤**
- `404` — 明細不存在

---

### 結帳

#### `POST /api/checkout`
送出訂單，伺服器重新計算合計後清空購物車。

**Request**
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
- `400` — 缺少收件資料或購物車為空

---

## 商業邏輯規則

1. **合併數量** — `POST /api/cart/items` 若該商品已在購物車中，累加數量而非新增一筆。
2. **自動移除** — `PATCH /api/cart/items/:itemId` 數量更新為 0 時刪除該 CartItem。
3. **伺服器計算合計** — 購物車合計與訂單金額一律由伺服器依 `product.price × quantity` 加總，不接受客戶端傳入。
4. **結帳清空** — 訂單建立後清除所有 CartItem（Cart 本身保留）。
