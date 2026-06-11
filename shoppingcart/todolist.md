# Shopping Cart 開發 To-Do List

## 並行執行說明
- **subagent-A**：後端專屬任務
- **subagent-B**：前端專屬任務
- **subagent-C**：資料庫專屬任務
- 無標記：需等前後端完成後的整合任務

---

## 階段 1 — 環境建置（A + B + C 並行）

- [ ] 【subagent-A】初始化後端專案結構（`server/` Node.js + Express + pg）
- [ ] 【subagent-B】初始化前端專案結構（`client/` React 18 + Vite + TypeScript）
- [ ] 【subagent-C】建立 PostgreSQL DB schema（Product、Cart、CartItem、Order init.sql）

---

## 階段 2 — 功能開發（A + B 並行）

### 後端 API（subagent-A）
- [ ] 實作 `GET /api/products`
- [ ] 實作 `GET /api/cart` + `POST /api/cart/items` + `PATCH /api/cart/items/:itemId`
- [ ] 實作 `POST /api/checkout`（伺服器計算合計、清空購物車）

### 前端頁面（subagent-B，使用 mock data）
- [ ] 實作商品列表頁面
- [ ] 實作購物車頁面（顯示明細、數量調整、移除）
- [ ] 實作結帳頁面（收件資料表單）

---

## 階段 3 — 整合與測試

- [ ] 前後端整合（替換 mock data 為真實 API）
- [ ] 【subagent-A】撰寫後端測試（Vitest + Supertest）
- [ ] 【subagent-B】撰寫 E2E 測試（Playwright）
