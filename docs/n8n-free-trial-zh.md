# n8n Cloud 免費試用申請教學

> 本文件使用繁體中文，說明如何從零開始申請並管理 n8n Cloud 免費試用帳號。

## 目錄

- [n8n Cloud 免費試用申請教學](#n8n-cloud-免費試用申請教學)
  - [目錄](#目錄)
  - [簡介](#簡介)
    - [亮點](#亮點)
  - [申請流程](#申請流程)
  - [驗證與啟用](#驗證與啟用)
  - [試用限制](#試用限制)
  - [管理與維運](#管理與維運)
  - [常見問題](#常見問題)
    - [試用要怎麼取消？](#試用要怎麼取消)
    - [超過 14 天或 1000 次執行怎麼辦？](#超過-14-天或-1000-次執行怎麼辦)
    - [無法登入 Admin Dashboard？](#無法登入-admin-dashboard)
    - [試用到期後還能取回資料嗎？](#試用到期後還能取回資料嗎)

## 簡介

n8n Cloud 免費試用提供 14 天 Pro 方案等級的完整功能（包含 Global Variables、Insights Dashboard、Execution Search 與 5 天 Workflow History 回溯），並以 Starter 方案同級運算資源與 1,000 次執行上限協助您在雲端安全驗證自動化價值。(來源：[n8n Cloud free trial](https://docs.n8n.io/manage-cloud/cloud-free-trial/))

### 亮點

- 14 天自動到期且無需手動取消。
- Pro 功能搭配 Starter 資源與 1,000 次執行上限。
- 試用結束後 90 天內可下載工作流程備份。(來源：[n8n Cloud free trial](https://docs.n8n.io/manage-cloud/cloud-free-trial/))

## 申請流程

![Screenshot Placeholder – Step 1](./images/placeholder-step1.png)

前往 [n8n Cloud](https://n8n.io/) 並點選「Get started for free」。

![Screenshot Placeholder – Step 2](./images/placeholder-step2.png)

在建立精靈中設定 Workspace 名稱、地區與首位管理者帳號等資訊，選擇安全密碼、勾選條款，最後點擊「Create workspace」即可觸發部署。

![Screenshot Placeholder – Step 3](./images/placeholder-step3.png)

收到確認信後，使用信中的 magic link 啟用帳號並自動登入工作區；首次登入可依需求載入官方範本或匯入既有 workflow JSON。

## 驗證與啟用

若部署完成後無法從 app 進入，可再次登入並選擇「Send magic link」以透過電子郵件安全開啟 Admin Dashboard。進入後建議立即設定時區與通知，並於 Settings 中啟用 2FA 或 SSO 以保護試用環境。(來源：[Cloud admin dashboard](https://docs.n8n.io/manage-cloud/cloud-admin-dashboard/))

## 試用限制

- 期間：14 天，逾期自動關閉並刪除工作區。
- 功能：享 Pro 方案所有特性（Global Variables、Insights Dashboard、Execution Search、5 天 Workflow History）。
- 資源：計算資源與 Starter 方案相同，最多 1,000 次 workflow 執行；到期後資料將被清除。
- 匯出：試用結束後 90 天內可從 Admin Dashboard 下載 workflow 以便在自架或正式方案繼續使用。
- 付費：任何時間皆可透過右上角「Upgrade」按鈕升級並選擇月付或年付方案。(來源：[n8n Cloud free trial](https://docs.n8n.io/manage-cloud/cloud-free-trial/))

## 管理與維運

透過 Admin Dashboard 可檢視版本、啟用或停用工作流程並設定時區；若前端無法登入，重新請求 magic link 仍可直接存取。同時建議定期在「Executions」中檢查 1,000 次配額使用狀況，並透過「Upgrade」提早轉為付費方案，或利用 [Download workflows](https://docs.n8n.io/manage-cloud/download-workflows/) 將自動化備份至本地以防到期刪除。(來源：[Cloud admin dashboard](https://docs.n8n.io/manage-cloud/cloud-admin-dashboard/))

## 常見問題

### 試用要怎麼取消？

無需手動取消，14 天到期後工作區會自動失效且不收費，僅需在期間內下載需要保留的 workflow。(來源：[n8n Cloud free trial](https://docs.n8n.io/manage-cloud/cloud-free-trial/))

### 超過 14 天或 1000 次執行怎麼辦？

點擊介面右上角「Upgrade」即可立即轉成付費方案；若您需要測試 Enterprise 功能（如多環境、SSO、外部密鑰庫），可在定價頁面點選「Contact」洽詢銷售取得 Enterprise 試用。(來源：[n8n Cloud free trial](https://docs.n8n.io/manage-cloud/cloud-free-trial/))

### 無法登入 Admin Dashboard？

在登入頁選擇「Send magic link」，從電子郵件開啟即可繞過前端故障直接進入 Dashboard，適用於主站維護或網路異常時。(來源：[Cloud admin dashboard](https://docs.n8n.io/manage-cloud/cloud-admin-dashboard/))

### 試用到期後還能取回資料嗎？

官方提供 90 天寬限期讓您下載 workflow JSON，逾期後工作區與資料會被刪除，建議提前在 Admin Dashboard 的 Download workflows 工具中匯出。(來源：[n8n Cloud free trial](https://docs.n8n.io/manage-cloud/cloud-free-trial/))
