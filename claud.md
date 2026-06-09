# Shopping Cart 專案規範

## 技術棧
- 後端：Node.js 20 + Express + PostgreSQL
- 前端：React 18 + Vite + TypeScript
- 測試：Vitest + Supertest（後端）、Playwright（E2E）

## 命名規範
- API 路徑：小寫 kebab-case（/api/cart-items）
- 後端模組：檔名 kebab-case；函式與變數 camelCase
- React 元件：PascalCase；hooks：use 前綴

## 禁止行為
- 禁止在 Node.js 程式碼中 hardcode secret key 或直接信任客戶端傳入金額
- 禁止直接修改 spec.md，需先與人類確認

## 常用指令
- 啟動後端：`npm run server`
- 啟動前端：`cd frontend && npm run dev`
- 執行測試：`npm run test:server`