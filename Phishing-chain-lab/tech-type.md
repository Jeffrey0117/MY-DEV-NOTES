---
模組名稱：腳本託管與外部載入器
模組類型：傳輸層
可重用性：✅ 可搭配任何有 XSS 注入點的攻擊環境
依賴條件：需有 GitHub 帳號、能打 release、可插入 <script> 載入 URL
關鍵技術：GitHub Release + jsDelivr CDN 載入
對應階段：第 2 階段（攻擊鏈中）
適用範圍：反射型 XSS、釣魚站、跨域腳本載入需求
可擴展點：未來可與 loader 模組、payload 架構自動生成工具結合
---
