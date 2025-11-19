# LINE 訊息行程整理 Demo（n8n Cloud + Azure OpenAI + Google 日曆）

> 本教學示範如何在 n8n Cloud 透過 LINE Webhook 接收使用者聊天內容，交由 Azure OpenAI 擷取旅遊或工作行程資訊，最後自動建立 Google 日曆事件。文件採繁體中文並聚焦 n8n Cloud 設定。

## 目錄

- [LINE 訊息行程整理 Demo（n8n Cloud + Azure OpenAI + Google 日曆）](#line-訊息行程整理-demon8n-cloud--azure-openai--google-日曆)
  - [目錄](#目錄)
  - [簡介與流程概觀](#簡介與流程概觀)
    - [資料流](#資料流)
  - [先備條件](#先備條件)
  - [LINE Messaging API 設定](#line-messaging-api-設定)
  - [n8n Cloud 準備工作](#n8n-cloud-準備工作)
    - [Credentials 建議](#credentials-建議)
  - [Workflow 節點配置](#workflow-節點配置)
    - [節點列表](#節點列表)
    - [Azure OpenAI Prompt 範例](#azure-openai-prompt-範例)
    - [Google Calendar 節點映射](#google-calendar-節點映射)
  - [測試資料與驗證流程](#測試資料與驗證流程)
    - [測試案例 1：週末旅遊](#測試案例-1週末旅遊)
    - [測試案例 2：會議週期任務](#測試案例-2會議週期任務)
    - [n8n Execution 驗證清單](#n8n-execution-驗證清單)
  - [匯出 Workflow JSON](#匯出-workflow-json)
    - [JSON 範例片段](#json-範例片段)
  - [常見問題與排除](#常見問題與排除)
  - [延伸資源](#延伸資源)

## 簡介與流程概觀

- 使用者於 LINE Bot 中貼上討論串、簡報重點或文章內容。
- LINE 將訊息透過 Webhook POST 至 n8n Cloud 公開 URL。
- n8n Workflow 先進行基本清洗，交給 Azure OpenAI Chat Completion 擷取行程結構，再將結果映射至 Google 日曆事件，最後回傳摘要給 LINE 用戶（可選）。

### 資料流

1. LINE Webhook -> n8n HTTP Request Trigger。
2. Function/Set 節點萃取文字、加註來源、建立 `contextId`。
3. Azure OpenAI（Chat Model 或 Prompt Node）將自然語言轉為 JSON 結構（日期、地點、備註）。
4. IF/Code 節點檢查必要欄位與時區。
5. Google Calendar 節點依欄位建立事件，寫入指定行事曆。
6. LINE Reply API（可選）回覆結果；同時將供查詢的 workflow JSON 匯出或備份。

## 先備條件

| 項目 | 內容 |
| --- | --- |
| LINE Developers | 已建立 Messaging API channel，取得 Channel Secret、Channel Access Token。|
| Azure OpenAI | 建立 `gpt-4o-mini` 或 `gpt-4o` 部署，記下 Endpoint、API Key、Deployment Name。|
| Google Cloud | 啟用 Calendar API，建立 Service Account，下載 JSON 金鑰並將 service account 加入目標日曆的共同編輯者。|
| n8n Cloud | 具備可建立 workflow 與 credentials 的 workspace，建議切換到專案用 Project。|

## LINE Messaging API 設定

1. 在 LINE Developers Console > Messaging API > Webhook settings：
   - Webhook URL 填入 n8n HTTP Request Trigger 產生的 URL（`https://<workspace>.app.n8n.cloud/webhook/...`）。
   - 启用 Webhook、Use webhook。
2. Messaging API > Channel access token > Issue，並複製長期 token。
3. 在 LINE > Messaging API > LIFF / Rich Menu 非必要，本 demo 可略。

## n8n Cloud 準備工作

1. 建立 Project (選用) 方便管理 credential 與 workflow。
2. 前往 **Credentials**：
   - **LINE Messaging API**：選 HTTP Request Credential（Bearer Token 放 Channel Access Token）。
   - **Azure OpenAI**：使用官方 Azure OpenAI Credential，填 Endpoint、Key、Deployment Name、API Version。
   - **Google Calendar OAuth**：若用 Service Account，於 Credential 選 Google Service Account JSON，貼上金鑰內容。
3. 建立 **Environment Variables**：如 `LINE_CHANNEL_SECRET`、`DEFAULT_CALENDAR_ID` 供節點引用。

### Credentials 建議

- n8n Cloud 支援 **shared** 與 **project** scope，建議僅對 demo workflow 開放最小權限。
- 若需支援多個 LINE Bot，可透過 multiple credentials + Switch node 選擇。

## Workflow 節點配置

### 節點列表

| 節點名稱 | 類型 | 角色 | 主要設定 |
| --- | --- | --- | --- |
| `LINE Webhook` | HTTP Request Trigger | 接收 LINE 訊息 | Method `POST`、Response `200 OK`、允許 Binary `false`、Webhook URL 貼至 LINE Console |
| `Verify Signature` | Function | 驗證 `X-Line-Signature` | 透過 Crypto HMAC-SHA256 與 `LINE_CHANNEL_SECRET` 比對，失敗則回傳 403 |
| `Parse Payload` | Set | 萃取文字 | `={{$json["events"][0].message.text]}}`、加上 `userId`、`replyToken` |
| `Azure Extractor` | Azure OpenAI Chat | 擷取行程 | Model 設 `gpt-4o-mini`，溫度 0.2，輸出 JSON 模板見下節 |
| `Validate Slots` | IF | 確認欄位存在 | 判斷 `items[0].start`, `items[0].title` 是否存在，不足則走錯誤分支 |
| `Format Date` | Function | 對齊時區 | 使用 Luxon 將自然語言日期轉為 ISO（引用 workflow timezone） |
| `Google Calendar` | Google Calendar | 建立事件 | Operation: `Create -> Event`，Title/Description/Start/End 對應 Azure 回傳欄位 |
| `Reply LINE` (可選) | HTTP Request | 回覆結果 | POST `https://api.line.me/v2/bot/message/reply`，Body 包含回饋摘要 |
| `Logger` | Respond to Webhook | 回應 | 回傳 `status:success` 與 eventId 便於除錯 |

### Azure OpenAI Prompt 範例

```text
你是一個行程整理助手，請從使用者訊息擷取所有明確的活動。
輸出 JSON，格式如下：
{
  "items": [
    {
      "title": "string",
      "start": "ISO 8601",
      "end": "ISO 8601",
      "location": "string",
      "note": "string",
      "timezone": "Asia/Taipei"
    }
  ],
  "context": {
    "confidence": 0-1,
    "source": "LINE"
  }
}
若訊息沒有具體行程，items 為空陣列。
```

### Google Calendar 節點映射

| Calendar 欄位 | 來源 |
| --- | --- |
| Summary | `{{$json["items"][0].title}}` |
| Description | `{{$json["items"][0].note}}` + 原始訊息截斷版 |
| Start | `{{$json["items"][0].start}}` |
| End | `{{$json["items"][0].end || $json["items"][0].start}}`（無結束時間時給 1 小時） |
| Location | `{{$json["items"][0].location}}` |
| Color (Optional) | 依 `context.confidence` 設不同顏色 |

## 測試資料與驗證流程

### 測試案例 1：週末旅遊

- **LINE 輸入**：
  > 週六想去台中歌劇院下午兩點的展演，晚上六點要回台北跟朋友吃飯，幫我塞進行事曆。
- **預期 Azure OpenAI JSON**：
  ```json
  {
    "items": [
      {
        "title": "台中歌劇院展演",
        "start": "2025-11-22T14:00:00+08:00",
        "end": "2025-11-22T16:00:00+08:00",
        "location": "台中歌劇院",
        "note": "晚餐後需回台北與朋友聚餐",
        "timezone": "Asia/Taipei"
      },
      {
        "title": "台北朋友聚餐",
        "start": "2025-11-22T18:00:00+08:00",
        "end": "2025-11-22T20:00:00+08:00",
        "location": "台北",
        "note": "回台北後立即前往",
        "timezone": "Asia/Taipei"
      }
    ],
    "context": {
      "confidence": 0.86,
      "source": "LINE"
    }
  }
  ```
- **Google 日曆**：建立兩筆同日事件，顏色依信心值顯示。

### 測試案例 2：會議週期任務

- **LINE 輸入**：
  > 11/25 起每週二 09:30 開 VS Code AI 專案檢討，地點線上 Teams，預計 45 分鐘。
- **預期處理**：
  - Function 節點將「每週二」拆成序列或建立單次事件並通知人工確認。
  - Google Calendar 可選 `recurrence` 欄位：`RRULE:FREQ=WEEKLY;BYDAY=TU`。
  - 備註加入 Teams 連結。

### n8n Execution 驗證清單

- `LINE Webhook` 200 OK，且 `Verify Signature` 路徑為 `true`。
- `Azure Extractor` 輸出 `items` 陣列非空；若空則走錯誤分支並回覆「沒有偵測到行程」。
- `Google Calendar` 節點 Response 包含 `id` 與 `htmlLink`。
- `Reply LINE`（若啟用）回應成功，HTTP 200。

## 匯出 Workflow JSON

1. 進入 n8n Editor，點擊右上角 `Share` -> `Download workflow`（n8n v1.52 之後可在 Workflow 標題旁 `...` 選 `Export`）。
2. 選擇是否包含 Credentials 參照；建議僅勾選 Workflow 與 Node 設定，避免秘鑰外洩。
3. 下載後可放在版本庫（例如 `demo/workflows/line-travel.json`）供夥伴匯入；匯入路徑：Dashboard `Workflows` -> `Import from file`。
4. 本範例已提供完整 Workflow 檔案：`demo/workflows/line-azure-openai-google-calendar.json`，直接於 n8n 選擇 **Import from file** 指向該 JSON 即可快速重建流程（記得重新綁定 Credentials 與環境變數）。
5. 若需自動備份，可建立第二條 workflow 透過 `Download Workflows` API 週期匯出並存至 S3/Drive。

### JSON 範例片段

```json
{
  "name": "line-travel-openai-calendar",
  "nodes": [
    {
      "parameters": {
        "path": "line-itinerary",
        "responseMode": "onReceived"
      },
      "id": "LINE Webhook",
      "type": "n8n-nodes-base.httpRequest",
      "typeVersion": 3
    },
    {
      "parameters": {
        "resource": "event",
        "operation": "create",
        "calendar": "={{$env[\"DEFAULT_CALENDAR_ID\"]}}",
        "additionalFields": {
          "description": "={{$json[\"items\"][0].note}}",
          "location": "={{$json[\"items\"][0].location}}"
        }
      },
      "id": "Google Calendar",
      "type": "n8n-nodes-base.googleCalendar",
      "typeVersion": 4
    }
  ],
  "pinData": {},
  "settings": {
    "timezone": "Asia/Taipei"
  },
  "staticData": {}
}
```

> 匯出檔案務必覆核 Credentials 區塊（`credentials`）沒有硬編碼秘密值；若需要共用，可改為環境變數或重建 credentials。

## 常見問題與排除

| 問題 | 排除方式 |
| --- | --- |
| LINE Webhook 回傳 400 | 確認簽章驗證程式引用正確 Channel Secret，或暫時停用驗證調試。|
| Azure OpenAI 回應慢/逾時 | 降低輸入文本長度、設定 `maxTokens` 或改成 `gpt-4o-mini`，並於 n8n 設 Timeout 60 秒以上。|
| Google Calendar 建立失敗 (403) | 檢查 Service Account 是否被加入日曆共享，或改用 OAuth2 代替。|
| 行程時區錯誤 | 在 `Format Date` 節點統一轉為 `Asia/Taipei`，亦可允許使用者輸入城市並透過 Timezone DB 解析。|

## 延伸資源

- [LINE Messaging API Webhook](https://developers.line.biz/en/docs/messaging-api/building-bot/#webhook)
- [Azure OpenAI 在 n8n 的使用方式](https://docs.n8n.io/integrations/builtin/app-nodes/n8n-nodes-langchain.openai/)
- [Google Calendar Node 說明](https://docs.n8n.io/integrations/builtin/app-nodes/n8n-nodes-base.googlecalendar/)
- [n8n Cloud Workflow 匯出官方指南](https://docs.n8n.io/manage-cloud/download-workflows/)
