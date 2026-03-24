# 軟體定義電信白牌平台 — 詳細技術規格書

**版本：** 1.0  
**建立日期：** 2026-03-24  
**文件狀態：** Draft  
**適用範圍：** 海外市場（不含台灣、中國大陸境內用戶）  
**付款模式：** 純 USDT 加密貨幣，不串接任何法幣金流閘道

---

## 目錄

1. [文件說明](#1-文件說明)
2. [法律合規聲明（修訂版）](#2-法律合規聲明修訂版)
3. [平台總覽](#3-平台總覽)
4. [商品目錄模組](#4-商品目錄模組)
5. [號碼選購與競拍系統](#5-號碼選購與競拍系統)
6. [代理商系統](#6-代理商系統)
7. [付款系統（USDT 主幣）](#7-付款系統usdt-主幣)
8. [通知與機器人系統](#8-通知與機器人系統)
9. [後台管理（Admin Dashboard）](#9-後台管理admin-dashboard)
10. [增值服務](#10-增值服務)
11. [裝置隱私保護功能](#11-裝置隱私保護功能)
12. [資料模型](#12-資料模型)
13. [API 規格](#13-api-規格)
14. [第三方整合清單](#14-第三方整合清單)
15. [安全架構](#15-安全架構)
16. [部署架構](#16-部署架構)
17. [非功能性需求](#17-非功能性需求)

---

## 1. 文件說明

本規格書針對「軟體定義電信（SDT）白牌平台」進行完整的功能、資料、流程與技術定義，作為開發團隊的單一事實來源（Single Source of Truth）。

### 1.1 核心定位

| 項目 | 說明 |
|------|------|
| 服務類型 | 海外電信服務轉售平台（白牌） |
| 付款模式 | 純加密貨幣（USDT TRC20 為主幣），**不串接任何法幣金流閘道** |
| 目標市場 | 亞太、東南亞、中東、非洲等海外地區 |
| 用戶類型 | 直客、代理商、企業批發商 |

### 1.2 核心產品線

| 產品代號 | 產品名稱 | 說明 |
|---------|---------|------|
| PRD-01 | 海外 eSIM 預付卡門號 | 多國、免 KYC、獨立號碼 |
| PRD-02 | 海外住家電話號碼（Residential Landline） | DID 號碼，住宅段 |
| PRD-03 | 海外市話（Business Landline） | DID 號碼，商業段 |
| PRD-04 | 海外手機號碼（Mobile） | 行動號碼 |
| PRD-05 | 海外流量數據池（IP Pool） | 動態 / 靜態 IP 數據包 |
| PRD-06 | VPN 租賃 | 加密隧道服務 |
| PRD-07 | 匿名電子信箱租賃 | 多網域隔離信箱 |

---

## 2. 法律合規聲明（修訂版）

> 本節已依「海外市場使用、不串接金流」定位進行調整，取代原始分析文件中之法律合規警示。

### 2.1 已移除之地域性法規風險

下列原始警示項目，因本平台明確定位為非台灣、非中國大陸境內服務，且**不串接任何法幣金流系統**，故不再適用：

| 已移除項目 | 移除原因 |
|-----------|---------|
| 台灣《電信管理法》符合性 | 平台非國內電信業者，無需向 NCC 申請執照 |
| 中國《電話用戶真實身份信息登記規定》 | 不服務中國境內用戶 |
| 台灣《消費者保護法》欺詐疑慮 | 非國內服務範疇 |
| 台灣《電子支付機構管理條例》 | 不串接法幣金流，不適用 |
| 支付寶 / 微信支付監管要求 | 代理商收款行為與平台主系統無技術整合 |
| PCI DSS 信用卡資料安全標準 | 平台不儲存、不處理信用卡卡號 |

### 2.2 仍適用之國際合規要求

| 風險項目 | 適用標準 | 緩解建議 |
|---------|---------|---------|
| eSIM / 門號轉售合規 | 各目標市場電信法（因國而異） | 確認批發商已完成 KYC；平台以「轉售商」身份操作；服務條款聲明用戶須遵守當地法規 |
| 加密貨幣收付（USDT TRC20） | FATF 旅行規則、各國 VASP 監管 | 收取 USDT 本身不違法；**嚴格遵守 OFAC 制裁名單**，禁止受制裁個人 / 實體 |
| 反洗錢義務（AML） | FATF Rec. 10–16、各國 AML 法規 | 建立風控規則；服務條款明確禁止洗錢用途；平台不主動招攬已知洗錢業務 |
| 裝置遠端資料清除功能 | 各國刑事訴訟法 | 功能重新定位為「隱私保護工具」；嚴禁任何「規避執法」相關描述；詳見 §11 |
| 匿名基礎設施 | 部署國家數位 / 網路服務法 | 優先選擇網路中立性立法友善的司法管轄區（如巴拿馬、塞席爾等）部署 |
| 隱私資料保護 | GDPR（如服務 EU 用戶）、各地個資法 | 最小化個人資料收集；部署隱私政策頁面 |
| 用戶行為責任 | 各目標市場刑事法規 | 服務條款明確「用戶自負行為責任」 |

### 2.3 無金流串接之合規影響

| 影響項目 | 說明 |
|---------|------|
| 不適用 PCI DSS | 平台不儲存、傳輸或處理任何信用卡資料 |
| 不適用各國電子支付法規 | 不作為支付機構，不處理法幣資金流動 |
| 代理收款行為切割 | 代理商使用微信 / 支付寶向其客戶收款，屬代理商自身行為，與平台主系統無技術整合 |
| U 卡充值路徑 | 若用戶持 Visa / MC U 卡充值，須由用戶自行將法幣換為 USDT 後存入，平台不接觸卡號 |

---

## 3. 平台總覽

### 3.1 系統架構圖

```
┌──────────────────────────────────────────────────────────────────┐
│                         用戶端入口                                │
│   PWA 主站 / H5 行動版   │   Telegram Bot   │   WhatsApp Bot     │
└──────────────────────┬───────────────────────────────────────────┘
                       │ HTTPS / WSS
┌──────────────────────▼───────────────────────────────────────────┐
│                    API Gateway / BFF                              │
│         Rate Limiting │ JWT Auth │ i18n │ WAF                    │
└──────┬──────────────────┬──────────────────┬──────────────────────┘
       │                  │                  │
┌──────▼──────┐  ┌────────▼────────┐  ┌──────▼────────────┐
│ Auth Service│  │  Order Service  │  │ Payment Monitor   │
│ (JWT/TOTP)  │  │  (門號 / eSIM)  │  │ (USDT 收款確認)   │
└─────────────┘  └────────┬────────┘  └──────┬────────────┘
                          │                  │
               ┌──────────▼──────────┐  ┌────▼──────────────┐
               │  eSIM Provider API  │  │  Wallet Rotation  │
               │  (50–300 家供應商)  │  │  (Tangem xPub)    │
               └─────────────────────┘  └───────────────────┘
       │                  │
┌──────▼──────┐  ┌─────────▼──────────┐
│ Agent       │  │ Notification       │
│ Service     │  │ Service            │
│ (分層抽傭)  │  │ (TG / WA / 站內)   │
└─────────────┘  └────────────────────┘
                          │
┌─────────────────────────▼────────────────────────────────────────┐
│                          資料層                                   │
│   PostgreSQL（主資料）  │  Redis（快取 / Session / BullMQ）       │
│   TimescaleDB（鏈上交易時序）  │  S3-compatible（文件 / 媒體）    │
└──────────────────────────────────────────────────────────────────┘
```

### 3.2 技術選型

| 層級 | 技術 | 選用理由 |
|------|------|---------|
| 前端框架 | Next.js 14（App Router） | SSR/SSG 兼顧 SEO 與效能 |
| 行動端 UI | Tailwind CSS + shadcn/ui | 共用元件庫，響應式設計 |
| 狀態管理 | Zustand | 輕量，適合電商流程 |
| 即時推播 | Socket.io over WSS | 競拍倒數、庫存更新 |
| API Gateway | Node.js + Fastify | 高效能路由 |
| 業務微服務 | TypeScript（Node.js） | 型別安全，生態完整 |
| 任務排程 | BullMQ + Redis | 可靠任務佇列，支援重試 |
| 爬蟲 | Playwright（無頭瀏覽器） | 可繞過 Cloudflare 保護 |
| AI 翻譯 | OpenAI GPT-4o / DeepL API | 即時多語翻譯 |
| 關聯資料庫 | PostgreSQL 16 | 主資料庫 |
| 時序資料庫 | TimescaleDB | 鏈上交易時序分析 |
| 快取 / 佇列 | Redis 7 | Session + 熱點快取 + 任務佇列 |

---

## 4. 商品目錄模組

### 4.1 產品資料結構

```typescript
interface Product {
  id: string;
  type: 'ESIM' | 'LANDLINE_RESIDENTIAL' | 'LANDLINE_BUSINESS'
      | 'MOBILE' | 'DATA_POOL' | 'VPN' | 'EMAIL';
  countryCode: string;        // ISO 3166-1 alpha-2
  carrierName: string;        // 顯示用電信商名稱（可設定白牌）
  phoneNumber?: string;       // E.164 格式
  eid?: string;               // eSIM EID
  iccid?: string;             // SIM ICCID
  status: 'AVAILABLE' | 'RESERVED' | 'ACTIVE' | 'EXPIRED';
  pricing: ProductPricing;
  dataQuota?: number;         // GB，數據卡適用
  validityDays: number;
  supplierId: string;
  supplierCost: number;       // 進貨成本（USDT）
}

interface ProductPricing {
  basePrice: number;          // USDT，批發底價
  retailPrice: number;        // USDT，建議零售價
  dataTopUpPrice: number;     // USDT per 10 GB，固定 8 U
  changeNumberFee: number;    // USDT，改號手續費
}
```

### 4.2 號碼展示規則

| 規則 | 說明 |
|------|------|
| 白牌電信商名稱 | 後台可為每個產品設定顯示名稱，不揭露真實供應商 |
| 多品牌管理 | 同一平台可設定多個白牌電信商名稱，按國家顯示不同品牌 |
| 號碼格式 | 統一標準化為 E.164（如 `+14155552671`） |
| 供應商隔離 | 前台不顯示任何 supplierId 或供應商資訊 |

---

## 5. 號碼選購與競拍系統

### 5.1 三種選號模式

#### 模式一：隨機發號

- 系統從庫存中隨機分配一個可用號碼
- 付款確認後立即鎖定，觸發開通流程
- 適合對號碼無要求的快速購買場景

#### 模式二：手動篩選

- 用戶輸入號段偏好（如：`+1 917 XXX-XXXX`）或關鍵字
- 系統返回符合條件的可用號碼列表
- 用戶選定後進入付款流程
- 支援按國家、號段、類型多維篩選

#### 模式三：競拍（Auction）

| 規則項目 | 說明 |
|---------|------|
| 最低押金 | 1,000 USDT（後台可調整） |
| 付款期限 | 得標後 1 小時內完成全額付款 |
| 逾時處理 | 押金沒收，號碼重新上架 |
| 加價單位 | 後台設定，如 10 USDT / 次 |
| 推播通知 | Telegram / WhatsApp 即時推送 |

**競拍狀態機：**

```
LISTED ──▶ BIDDING ──▶ WINNING ──▶ PAYMENT_PENDING ──▶ COMPLETED
                                          │
                                     逾時 │
                                          ▼
                                    FORFEITED ──▶ RELISTED
```

### 5.2 臨時特權租借

| 項目 | 說明 |
|------|------|
| 適用範圍 | 進入進階挑號區（稀缺號段、靚號） |
| 收費方式 | 按小時計費，從用戶預存額即時扣除 |
| 前提條件 | 預存餘額大於 0 |
| UI 設計 | 介面不主動顯示剩餘使用時間，使用戶專注於選號體驗 |
| 計費精度 | 以分鐘為單位計算實際使用時長 |

### 5.3 改號功能

| 步驟 | 說明 |
|------|------|
| 1 | 用戶在訂單頁按下「改號」按鈕 |
| 2 | 系統顯示改號費用，用戶確認扣款 |
| 3 | 舊號碼釋回庫存（標記為可用） |
| 4 | 用戶可選擇「重新隨機」或「進入挑號頁手動選」 |

### 5.4 數據加購

| 項目 | 說明 |
|------|------|
| 單位 | 10 GB = 8 USDT（固定費率） |
| 生效時間 | 付款確認後即時生效 |
| 適用對象 | 已持有啟用中數據卡的用戶 |
| 購買紀錄 | 計入帳單明細，可多次累加 |

### 5.5 門號到期通知流程

```
到期日前 7 天
      │
      ▼
系統排程（BullMQ）觸發
      │
      ├──▶ 推播通知用戶（TG / WA：提醒續費）
      └──▶ 推播通知對應代理商（TG / WA：客戶門號即將到期）

到期後
      │
      ▼
系統自動關閉門號（呼叫供應商 API 停用）
      │
      ▼
推播最後告知通知
```

---

## 6. 代理商系統

### 6.1 代理等級架構

| 等級 | 加盟金（人民幣） | 人數上限 | 抽傭比例 | 備註 |
|------|-------------|---------|---------|------|
| 一級代理 | 6,000 CNY | 34 位 | 20% | 最高等級 |
| 二級代理 | 4,000 CNY | 68 位 | 20% | — |
| 三級代理 | 2,000 CNY | 136 位 | 10% | — |
| 特殊團體 | 30,000 CNY | 另議 | 另議 | 大型機構 |

> 各等級之加盟金金額、人數上限及抽傭比例，後台管理員可即時調整，無需修改程式碼。

### 6.2 加盟金轉預存邏輯

```
代理繳交加盟金（以 USDT 換算入帳）
             │
             ▼
加盟金 100% 轉換為平台預存額度（USDT）
             │
         ┌───┴───────────────────────────────────────┐
         │                                           │
         ▼                                           ▼
   90 天內有業績                               90 天無業績
   代理持續運作                           沒收剩餘預存額度
   繼續累積抽傭                           帳號標記為 FORFEITED
```

### 6.3 集資代理

| 規則 | 說明 |
|------|------|
| 最多人數 | 5 人共同出資 |
| 平台認知 | 只認識一個代理帳號，不介入聯合代理間的分帳 |
| 責任切割 | 帳號附註記錄參與人數，免除平台因內部糾紛而承擔連帶責任 |

### 6.4 代理預存回饋機制

| 預存餘額（USDT） | 回饋比例 |
|----------------|---------|
| 0 – 1,000 | 0% |
| 1,001 – 5,000 | 2% |
| 5,001 – 20,000 | 5% |
| 20,001+ | 8% |

> 具體回饋階梯可於後台任意設定，支援增減梯級。

### 6.5 代理定價權

- 平台向代理顯示「批發底價」（參考值：160 USDT / 門號）
- 代理自行設定面對終端客戶的售價（建議售價：200 USDT）
- 平台不干涉代理與終端客戶之間的定價

### 6.6 代理後台可見資訊

| 資訊項目 | 可見性 |
|---------|-------|
| 預存餘額 | ✅ 可見 |
| 使用明細（扣款紀錄） | ✅ 可見 |
| 抽傭明細（自動計算） | ✅ 可見 |
| 個別客戶入帳金額 | ❌ 不顯示 |
| 供應商成本 | ❌ 不顯示 |

### 6.7 代理收款碼上傳

| 項目 | 說明 |
|------|------|
| 支援格式 | 微信收款碼 / 支付寶收款碼（圖片） |
| 儲存用途 | 供代理本人在後台管理訂單使用 |
| 技術切割 | 此圖片儲存於平台，但平台主系統**不讀取、不處理**任何法幣金流資訊 |
| 合規聲明 | 代理與其客戶間的法幣收付行為，由代理獨立負責 |

---

## 7. 付款系統（USDT 主幣）

> 本平台**不串接任何法幣金流閘道**，一切收款均以 USDT TRC20 為主幣進行。

### 7.1 支援幣種

| 幣種 | 網路 | 角色 | 說明 |
|------|------|------|------|
| USDT | TRC20 | **主幣** | 計價與結算單位 |
| TRX | TRON | 副幣 | 支付鏈上手續費（Energy） |
| USDC | TRC20 / ERC20 | 副幣 | 備選穩定幣 |
| BTC | Bitcoin | 副幣 | 大額 B2B 結算 |

> B2B 電匯：僅接受大額企業批量採購，另行簽訂合約，不在本規格自動化流程範圍內。

### 7.2 即時匯率換算流程

```
商品底價（USDT）
       │
       ▼
拉取幣安 API（主要）+  OKX API（備援，當幣安不可用時切換）
       │
       ▼
計算各國法幣對 USDT 匯率
取最高報價作為顯示售價
       │
       ▼
前端顯示多幣種參考價格
（僅供參考，結算以 USDT 為準）
```

**幣安 API Endpoint：**
```
GET https://api.binance.com/api/v3/ticker/price?symbol=USDT<FIAT>
```

**匯率快取策略：**
- Redis 快取匯率，TTL = 60 秒
- 幣安 API 失敗時，自動切換 OKX API，並告警管理員

### 7.3 Tangem 冷錢包架構

| 設定項目 | 規格 |
|---------|------|
| 冷錢包組數 | 7 組 |
| 每組幣種數 | 4 種（USDT、TRX、USDC、BTC） |
| 地址來源 | Tangem xPub 衍生地址（每日衍生新地址） |
| 輪替週期 | 每日更換一組當值主錢包，7 天一個完整循環 |
| 私鑰保管 | 私鑰永遠不離開 Tangem 硬體，系統只持有 xPub |

### 7.4 錢包地址輪替控制

後台控制面板支援三種輪替模式，可即時切換：

| 模式 | 說明 | 使用場景 |
|------|------|---------|
| 定時模式 | 可勾選 6 / 12 / 18 / 24 小時自動更換 | 日常穩定運行 |
| 閾值模式 | 每滿 5,000 USDT 或滿 50 筆交易自動更換 | 高流量時期 |
| 隨機偏差模式 | 設定基礎週期（如 12 小時），系統在 ±1 小時隨機偏差後執行 | 增加不可預測性 |

> 隨機偏差模式為最佳實踐：讓地址更換時間不固定，降低鏈上分析的可預測性。

### 7.5 TRC20 歸集架構

```
子錢包群（xPub 衍生地址，接收用戶付款）
              │
              │  TRC20 Approve 授權
              ▼
   主錢包 FeePayer（代付 TRX Energy）
              │
              │  觸發條件：定時 / 閾值 / 隨機偏差
              ▼
   Tangem 冷錢包（當日值班的一組）
```

**TRC20 Approve 歸集腳本核心邏輯：**

```typescript
// 概念性偽碼 — 非最終實作
async function collectFromSubWallet(
  subAddress: string,
  coldWalletAddress: string
): Promise<void> {
  const balance = await getUsdtBalance(subAddress);
  if (balance < MIN_COLLECTION_THRESHOLD) return;

  // 1. 主錢包代付 Energy，讓子錢包免費執行轉帳
  await delegateEnergy(FEEPAYER_WALLET, subAddress, REQUIRED_ENERGY);

  // 2. 執行 TRC20 transferFrom，將 USDT 歸集至當日冷錢包
  await usdtContract.methods
    .transferFrom(subAddress, coldWalletAddress, balance)
    .send({ from: FEEPAYER_WALLET });
}
```

**歸集排程（BullMQ）：**

```typescript
// 三種觸發條件同時監控
scheduler.addJob('collect:scheduled', '0 3 * * *');      // 每日凌晨 3–4 點
scheduler.addJob('collect:threshold', {                  // 滿 5000 U 或 50 筆
  event: 'payment.received',
  condition: (stats) => stats.totalAmount >= 5000 || stats.txCount >= 50
});
scheduler.addJob('collect:random', {                     // 隨機偏差模式
  baseInterval: adminConfig.walletRotationHours,
  jitter: 60 * 60 * 1000  // ±1 小時
});
```

### 7.6 開通觸發流程

```
TronGrid Webhook 推送（鏈上入帳事件）
              │
              ▼
Payment Monitor 驗證：
  ─ 入帳金額是否符合訂單金額（±0.001 USDT 容差）
  ─ 收款地址是否為本平台當值地址
  ─ 區塊確認數是否 ≥ 19
              │
              ▼
比對訂單（依收款地址 + 金額精確匹配訂單 ID）
              │
              ▼
向 Order Service 發送開通訊號
              │
              ▼
後台記錄付款哈希（txHash），門號狀態更新為 ACTIVE
              │
              ▼
呼叫供應商 API 啟用 eSIM / 門號
              │
              ▼
推播通知用戶（TG / WA / 站內訊息）
```

---

## 8. 通知與機器人系統

### 8.1 Telegram Bot（B-01）

| 指令 / 功能 | 說明 |
|-----------|------|
| `/start` | 歡迎訊息 + 帳戶綁定（輸入平台帳號 Token） |
| `/orders` | 查詢我的所有訂單列表 |
| `/order <id>` | 查詢指定訂單的詳細狀態 |
| `/balance` | 查詢預存餘額（USDT） |
| `/expiring` | 查詢 7 天內即將到期的門號 |
| `/topup` | 取得充值地址（今日當值冷錢包地址）|
| 開通成功推播 | 門號啟用後自動推送 |
| 到期前 7 天推播 | 用戶 + 代理各收一則通知 |
| 競拍通知 | 押金入帳確認、得標通知、付款倒數警告 |
| 代理推播 | 代理名下客戶訂單異動通知 |

### 8.2 WhatsApp Business API Bot（B-02）

- 功能同 Telegram Bot，覆蓋不使用 Telegram 的目標市場
- 使用 Meta WhatsApp Business API（雲端 API）
- 需申請 WhatsApp Business 帳號（WABA）及通過 Meta 審核

### 8.3 推播事件總表

| 事件 | 通知對象 | 渠道 | 訊息範本 |
|------|---------|------|---------|
| 門號開通成功 | 用戶 | TG + WA | `您的門號 {number} 已開通，有效期至 {date}` |
| 到期前 7 天 | 用戶 + 代理 | TG + WA | `門號 {number} 將於 {date} 到期，請提早續費` |
| 門號已到期 | 用戶 + 代理 | TG + WA | `門號 {number} 已到期，如需續用請重新購買` |
| 競拍押金入帳 | 用戶 | TG + WA | `押金 {amount} U 已確認，您已進入競拍` |
| 競拍得標 | 用戶 | TG + WA | `恭喜得標！請於 1 小時內完成付款` |
| 競拍付款逾時 | 用戶 | TG + WA | `付款逾時，押金已沒收，號碼重新上架` |
| 庫存低於 60% | 管理員 | TG 內部頻道 | `[庫存警報] {product_type} 庫存剩餘 {pct}%` |
| 比價爬蟲警報 | 管理員 | TG 內部頻道 | `[進貨建議] {supplier} 有低於成本的優惠` |

---

## 9. 後台管理（Admin Dashboard）

### 9.1 門號開關控制（D-01）

| 功能 | 說明 |
|------|------|
| 單一開通 / 關閉 | 點擊一鍵切換指定門號狀態 |
| 批次操作 | 勾選多筆門號，一次批次開通 / 關閉 |
| 操作日誌 | 記錄操作員 ID、時間戳、操作前後狀態 |
| 開通觸發 | 自動（付款確認後觸發）/ 手動（管理員強制覆蓋）兩種模式 |

### 9.2 庫存監控（D-02）

| 功能 | 說明 |
|------|------|
| 即時庫存水位 | 各類型商品的可用數量與佔比 |
| 閾值告警 | 低於 60% 時啟動告警（閾值後台可調整）|
| 告警渠道 | Telegram 管理員頻道 + 後台 Dashboard 紅色警示 |
| 庫存歷史趨勢 | 7 天 / 30 天庫存變化折線圖 |

### 9.3 風控系統（D-03）

| 風控規則 | 觸發條件 | 動作 |
|---------|---------|------|
| 同一裝置多帳號 | Device Fingerprint 重複 | 暫停帳號，通知管理員 |
| 短時大量訂單 | 同帳號 1 小時內訂單數超過閾值 | 暫停帳號 |
| 可疑 IP 段 | 命中已知詐騙 IP 清單 | 封鎖 IP，拒絕請求 |
| 異常充值行為 | 充值後立即大量下單再退款嘗試 | 凍結帳號 |
| 手動封鎖 | 管理員操作 | 立即封鎖指定帳號 / IP |

### 9.4 比價爬蟲（D-04）

| 項目 | 說明 |
|------|------|
| 爬取對象 | 主要 eSIM / DID 號碼供應商官網 |
| 觸發頻率 | 每日一次（可後台調整）|
| 比價邏輯 | 若供應商售價低於本平台進貨成本 → 立即告警 |
| 技術 | Playwright 無頭瀏覽器（可繞 Cloudflare JS Challenge）|
| 資料儲存 | 比價結果入庫，提供歷史趨勢報表 |

### 9.5 新供應商偵測（D-05）

- 定期透過搜尋引擎 API 掃描新出現的免 KYC eSIM / 流量供應商
- 維護一份目標關鍵字清單（如：`"no kyc esim"`, `"anonymous sim"` 等）
- 新供應商偵測後推播 Telegram 告知管理員評估洽談

### 9.6 EID / ICCID 資料庫（D-06）

| 欄位 | 說明 |
|------|------|
| EID | eSIM 的永久識別碼（20 位數字） |
| ICCID | SIM 卡識別碼（19–20 位） |
| 對應用戶 | 購買此 eSIM 的用戶 ID |
| 對應訂單 | 關聯訂單 ID |
| 狀態 | ACTIVE / EXPIRED / SUSPENDED |

> EID + ICCID 為掌握設備後續流量商機的核心資產，須完整紀錄。

### 9.7 全球節假日資料庫（D-07）

- 涵蓋主要目標市場（東南亞、中東、非洲等）的公眾假日
- 用於：排程任務避開維護時段 / 行銷活動精準投放
- 來源：Google Calendar API + 靜態假日資料庫
- 每年 Q4 自動更新下一年度資料

### 9.8 錢包地址輪替控制面板（D-08）

詳細規格見 §7.4。

後台頁面包含：
- 當前輪替模式顯示（定時 / 閾值 / 隨機偏差）
- 模式即時切換開關
- 參數設定（小時數、金額閾值、筆數閾值）
- 下次輪替時間預估
- 歷史輪替紀錄（時間戳 + 觸發原因）

### 9.9 代理系統後台設定（D-09）

| 設定項目 | 說明 |
|---------|------|
| 各級加盟金金額 | 每級獨立設定，即時生效 |
| 各級人數上限 | 可即時增減 |
| 各級抽傭比例 | 百分比，後台設定 |
| 預存回饋階梯 | 層級金額 + 對應回饋%，可增刪層級 |
| 加盟金轉預存匯率 | CNY→USDT 換算係數 |
| 業績考核寬限天數 | 預設 90 天，可調整 |

---

## 10. 增值服務

### 10.1 VPN 租賃（I-01）

| 項目 | 規格 |
|------|------|
| 租用方式 | 按月計費，平台預存扣款 |
| 覆蓋地區 | 依採購之批發 VPN 供應商而定 |
| 協議支援 | OpenVPN / WireGuard |
| 帳號分配 | 下單後系統自動分配帳號密碼，TG / WA 推送 |
| 到期通知 | 同門號邏輯，到期前 7 天通知 |

### 10.2 匿名電子信箱租賃（I-02）

| 項目 | 規格 |
|------|------|
| 主網域數 | 1–3 個 |
| 子網域架構 | 每主網域下多個子網域，互相隔離 |
| 隔離機制 | 不同子網域部署於獨立的信箱伺服器 Pod |
| 功能 | 收信 / 發信 / 自訂別名 |
| 計費 | 按月計費 |
| KYC | 無（符合平台匿名定位）|

**隔離架構示意：**

```
主網域 A（domain-a.com）
├── user1@sub1.domain-a.com  ──▶ 信箱伺服器 Pod A（獨立）
└── user2@sub2.domain-a.com  ──▶ 信箱伺服器 Pod B（獨立）

主網域 B（domain-b.net）
└── user3@sub1.domain-b.net  ──▶ 信箱伺服器 Pod C（獨立）
```

> 某一子網域被封鎖或出現問題，不影響其他 Pod 的正常服務。

### 10.3 訊號不佳時自動切換電信商（I-03）

| 項目 | 說明 |
|------|------|
| 監控指標 | RSSI / SINR 訊號強度 |
| 觸發閾值 | 低於 -100 dBm（後台可調整）|
| 切換動作 | 透過 eSIM 供應商 API 切換備援電信商 Profile |
| 前提條件 | eSIM 供應商 API 支援多電信商 Profile 管理 |

---

## 11. 裝置隱私保護功能

> **合規聲明：** 本功能定位為「用戶裝置個人資料自主保護工具」（與 Apple Find My、Android MDM 等功能性質相同），嚴禁任何涉及規避執法的宣傳描述。

### 11.1 功能規格

| 項目 | 說明 |
|------|------|
| 設定方式 | 用戶於 App 內自設數字 PIN（4–8 位）|
| 錯誤觸發閾值 | 用戶自定義（如連續錯誤 3 / 5 / 10 次觸發清除）|
| 清除範圍 | 用戶勾選（帳號登入資料 / 本地快取 / 聊天紀錄 / 全部）|
| 平台側動作 | 接收到有效的清除請求後，刪除伺服器端帳號相關資料（72 小時後永久清除）|
| 不可逆聲明 | 清除操作**無法復原**，平台不保留任何備份 |

### 11.2 技術實作

| 環節 | 實作方式 |
|------|---------|
| PIN 儲存 | 加密（bcrypt hash）儲存於本地裝置，**不上傳至伺服器** |
| 本地錯誤計數 | 儲存於 App 本地，不依賴網路連線 |
| 觸發訊號 | 本地計數達閾值 → 呼叫平台清除 API（Bearer Token 認證）|
| 伺服器清除 | 標記刪除用戶帳號資料，72 小時後執行硬刪除（GDPR 相容做法）|

### 11.3 使用者協議必要條款

在啟用本功能前，用戶**必須閱讀並主動勾選確認**以下聲明：

1. 本功能旨在保護個人隱私資料，禁止用於任何違法目的
2. 本操作一旦觸發**不可逆**，平台無法恢復任何資料
3. 用戶須自行牢記 PIN，遺失後平台**無任何備援手段**
4. 用戶對觸發清除後的一切後果負完全責任

---

## 12. 資料模型

### 12.1 核心資料表

**users（用戶）**
```sql
CREATE TABLE users (
  id              UUID        PRIMARY KEY DEFAULT gen_random_uuid(),
  username        VARCHAR(50) UNIQUE NOT NULL,
  password_hash   VARCHAR(255) NOT NULL,
  role            VARCHAR(20) NOT NULL DEFAULT 'CUSTOMER',  -- CUSTOMER / AGENT / ADMIN
  balance         DECIMAL(18,6) NOT NULL DEFAULT 0,         -- USDT 預存餘額
  telegram_id     BIGINT      UNIQUE,
  whatsapp_number VARCHAR(20),
  status          VARCHAR(20) NOT NULL DEFAULT 'ACTIVE',    -- ACTIVE / SUSPENDED / DELETED
  created_at      TIMESTAMPTZ NOT NULL DEFAULT NOW(),
  deleted_at      TIMESTAMPTZ
);
```

**products（商品）**
```sql
CREATE TABLE products (
  id              UUID        PRIMARY KEY DEFAULT gen_random_uuid(),
  type            VARCHAR(30) NOT NULL,
  country_code    CHAR(2)     NOT NULL,
  carrier_name    VARCHAR(100) NOT NULL,
  phone_number    VARCHAR(20),
  eid             VARCHAR(50),
  iccid           VARCHAR(30),
  status          VARCHAR(20) NOT NULL DEFAULT 'AVAILABLE',
  base_price      DECIMAL(10,4) NOT NULL,
  retail_price    DECIMAL(10,4) NOT NULL,
  data_quota_gb   INT,
  validity_days   INT         NOT NULL,
  supplier_id     UUID        NOT NULL REFERENCES suppliers(id),
  supplier_cost   DECIMAL(10,4) NOT NULL,
  created_at      TIMESTAMPTZ NOT NULL DEFAULT NOW()
);
```

**orders（訂單）**
```sql
CREATE TABLE orders (
  id              UUID        PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id         UUID        NOT NULL REFERENCES users(id),
  product_id      UUID        NOT NULL REFERENCES products(id),
  agent_id        UUID        REFERENCES users(id),
  status          VARCHAR(20) NOT NULL DEFAULT 'PENDING',
                              -- PENDING / PAID / ACTIVE / EXPIRED / CANCELLED
  amount_usdt     DECIMAL(10,4) NOT NULL,
  payment_tx_hash VARCHAR(100),
  started_at      TIMESTAMPTZ,
  expires_at      TIMESTAMPTZ,
  created_at      TIMESTAMPTZ NOT NULL DEFAULT NOW()
);
```

**wallet_addresses（錢包地址）**
```sql
CREATE TABLE wallet_addresses (
  id              UUID        PRIMARY KEY DEFAULT gen_random_uuid(),
  wallet_group    SMALLINT    NOT NULL CHECK (wallet_group BETWEEN 1 AND 7),
  coin_type       VARCHAR(10) NOT NULL,
  address         VARCHAR(100) NOT NULL UNIQUE,
  derivation_path VARCHAR(50),
  is_active       BOOLEAN     NOT NULL DEFAULT FALSE,
  activated_at    TIMESTAMPTZ,
  deactivated_at  TIMESTAMPTZ
);
```

**agent_accounts（代理帳號）**
```sql
CREATE TABLE agent_accounts (
  id              UUID        PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id         UUID        NOT NULL REFERENCES users(id) UNIQUE,
  tier            SMALLINT    NOT NULL CHECK (tier BETWEEN 1 AND 3),
  deposit_paid    DECIMAL(10,4) NOT NULL,
  prestore_balance DECIMAL(10,4) NOT NULL DEFAULT 0,
  commission_rate  DECIMAL(5,4) NOT NULL,
  joined_at       TIMESTAMPTZ NOT NULL DEFAULT NOW(),
  last_sale_at    TIMESTAMPTZ,
  member_count    SMALLINT    NOT NULL DEFAULT 1 CHECK (member_count BETWEEN 1 AND 5),
  status          VARCHAR(20) NOT NULL DEFAULT 'ACTIVE'
                              -- ACTIVE / SUSPENDED / FORFEITED
);
```

**auctions（競拍）**
```sql
CREATE TABLE auctions (
  id              UUID        PRIMARY KEY DEFAULT gen_random_uuid(),
  product_id      UUID        NOT NULL REFERENCES products(id),
  min_deposit     DECIMAL(10,4) NOT NULL DEFAULT 1000,
  min_bid_increment DECIMAL(10,4) NOT NULL DEFAULT 10,
  winner_id       UUID        REFERENCES users(id),
  winning_bid     DECIMAL(10,4),
  deposit_tx_hash VARCHAR(100),
  payment_deadline TIMESTAMPTZ,
  status          VARCHAR(20) NOT NULL DEFAULT 'LISTED',
                              -- LISTED / ACTIVE / PENDING_PAYMENT / COMPLETED / FORFEITED
  created_at      TIMESTAMPTZ NOT NULL DEFAULT NOW()
);
```

**payments（付款紀錄 — TimescaleDB 時序表）**
```sql
CREATE TABLE payments (
  time            TIMESTAMPTZ NOT NULL,
  tx_hash         VARCHAR(100) NOT NULL,
  from_address    VARCHAR(100),
  to_address      VARCHAR(100) NOT NULL,
  amount_raw      NUMERIC     NOT NULL,
  amount_usdt     DECIMAL(10,4) NOT NULL,
  coin_type       VARCHAR(10) NOT NULL,
  block_number    BIGINT,
  order_id        UUID        REFERENCES orders(id),
  status          VARCHAR(20) NOT NULL DEFAULT 'CONFIRMED'
);
SELECT create_hypertable('payments', 'time');
```

---

## 13. API 規格

### 13.1 認證機制

- 所有 API 端點使用 **JWT Bearer Token** 認證（除 `/auth/*`）
- Access Token 有效期：15 分鐘
- Refresh Token 有效期：7 天，支援輪替（Rotation）
- Admin 端點額外需要 **TOTP 二次驗證**

```http
Authorization: Bearer <access_token>
```

### 13.2 核心端點總覽

| 方法 | 端點 | 角色 | 說明 |
|------|------|------|------|
| POST | `/auth/login` | 公開 | 登入，取得 JWT |
| POST | `/auth/refresh` | 公開 | 刷新 Access Token |
| GET | `/products` | 用戶 | 取得商品列表（支援篩選：type, country, priceMin, priceMax）|
| GET | `/products/:id` | 用戶 | 取得單一商品詳情 |
| POST | `/orders` | 用戶 | 建立訂單（含隨機選號 / 手動選號）|
| GET | `/orders` | 用戶 | 查詢我的訂單列表 |
| GET | `/orders/:id` | 用戶 | 查詢指定訂單 |
| POST | `/orders/:id/topup` | 用戶 | 數據加購 |
| POST | `/orders/:id/change-number` | 用戶 | 申請改號 |
| GET | `/user/balance` | 用戶 | 查詢預存餘額 |
| POST | `/user/topup` | 用戶 | 充值（返回當日收款地址）|
| GET | `/auctions` | 用戶 | 競拍列表 |
| POST | `/auctions/:id/bid` | 用戶 | 出價 |
| GET | `/agent/dashboard` | 代理 | 代理後台概覽 |
| GET | `/agent/commission` | 代理 | 抽傭明細 |
| POST | `/admin/products/:id/toggle` | 管理員 | 開通 / 關閉門號 |
| GET | `/admin/inventory` | 管理員 | 庫存監控報表 |
| POST | `/admin/wallet/rotate` | 管理員 | 手動觸發錢包輪替 |
| GET | `/admin/users/:id` | 管理員 | 查詢用戶資料 |
| POST | `/admin/users/:id/block` | 管理員 | 封鎖用戶 |
| POST | `/webhooks/payment` | 系統 | TronGrid / 區塊鏈入帳 Webhook（HMAC 驗簽）|

### 13.3 付款 Webhook 資料格式

```typescript
// POST /webhooks/payment
// Header: X-Webhook-Signature: HMAC-SHA256(secret, body)
interface PaymentWebhookPayload {
  txHash:       string;   // 交易 Hash
  fromAddress:  string;
  toAddress:    string;
  amount:       string;   // TRC20 raw amount（需除以 10^6 得 USDT）
  tokenSymbol:  string;   // 'USDT' | 'USDC' | 'TRX'
  blockNumber:  number;
  timestamp:    number;   // Unix timestamp（秒）
  confirmations: number;  // 區塊確認數，需 ≥ 19 才處理
}
```

### 13.4 統一回應格式

```typescript
// 成功
{
  "success": true,
  "data": { /* 資料 */ },
  "meta": { "page": 1, "limit": 20, "total": 100 }  // 列表類才有
}

// 失敗
{
  "success": false,
  "error": {
    "code": "ORDER_NOT_FOUND",
    "message": "Order not found"
  }
}
```

---

## 14. 第三方整合清單

| 分類 | 服務商 | 用途 | 優先級 |
|------|--------|------|--------|
| eSIM 供應商 | Airalo Partner API / Eiotclub / Truphone | 號碼分配、eSIM Profile 下載 | P0 |
| DID 號碼 | Twilio / Vonage / DIDWW / VoIP.ms | Residential & Business Landline | P0 |
| 匯率 API（主） | 幣安 REST API | USDT ↔ 各國法幣換算 | P0 |
| 匯率 API（備） | OKX REST API | 幣安 API 不可用時的備援 | P1 |
| 鏈上監聽 | TronGrid Webhook | TRC20 入帳事件偵測 | P0 |
| 鏈上操作 | TronWeb / TronGrid | Approve 授權、歸集轉帳 | P0 |
| 冷錢包 | Tangem xPub | 衍生子錢包收款地址 | P0 |
| 通知（主） | Telegram Bot API | 用戶、代理及管理員推播 | P0 |
| 通知（副） | Meta WhatsApp Business API | 覆蓋非 Telegram 市場 | P1 |
| AI 翻譯（主） | OpenAI GPT-4o API | 即時多語翻譯 | P1 |
| AI 翻譯（備） | DeepL API | OpenAI 備援 | P2 |
| 爬蟲引擎 | Playwright（自部署） | 供應商比價、新供應商偵測 | P1 |
| 節假日資料 | Google Calendar API | 全球節假日資料庫 | P2 |

---

## 15. 安全架構

### 15.1 存取控制（RBAC）

| 角色 | 可存取資源 |
|------|-----------|
| CUSTOMER | 個人訂單、充值、挑號、競拍 |
| AGENT | 以上 + 代理後台（預存、抽傭、旗下訂單摘要）|
| ADMIN | 全部端點 + 管理後台（需 TOTP）|

### 15.2 API 防護措施

| 防護措施 | 規格 |
|---------|------|
| Rate Limiting | 60 req / min / IP（可依端點調整）|
| WAF | Cloudflare WAF，過濾 OWASP Top 10 攻擊向量 |
| HTTPS Only | 強制 TLS 1.3，HTTP 自動跳轉 |
| Input Validation | 所有入口參數使用 zod schema 驗證 |
| SQL Parameterization | 使用 ORM Prepared Statement，杜絕 SQL Injection |
| CORS | 嚴格限定白名單 Origin，不允許 wildcard `*` |
| Webhook HMAC 驗簽 | TronGrid Webhook 使用 HMAC-SHA256 驗簽，防偽造 |

### 15.3 金融安全

| 措施 | 說明 |
|------|------|
| 區塊確認等待 | 付款確認需等待 ≥ 19 個 TRON 區塊（約 60 秒）才觸發開通 |
| 私鑰隔離 | 私鑰永遠不離開 Tangem 硬體，系統只持有 xPub |
| 歸集授權控制 | 手動歸集需管理員後台授權；自動歸集有每日上限 |
| 雙重收款確認 | 金額 + 地址雙重比對，防止多付 / 少付誤觸發 |

### 15.4 資料安全

| 措施 | 說明 |
|------|------|
| 敏感欄位加密 | AES-256-GCM 加密儲存（PIN hash、錢包 xPub 等）|
| 日誌脫敏 | 日誌不記錄完整 IP、完整交易金額等敏感資訊 |
| 定期備份 | PostgreSQL 每日全量備份，AES 加密後存 S3，保留 30 天 |
| 帳號軟刪除 | 刪除操作為軟刪除，72 小時後永久清除（符合 GDPR 刪除權）|

---

## 16. 部署架構

### 16.1 基礎設施建議

| 元件 | 部署方式 | 說明 |
|------|---------|------|
| API Gateway + 微服務 | VPS（巴拿馬 / 塞席爾 / 英屬維京群島等）| 選擇對隱私立法友善的司法管轄區 |
| 資料庫（PostgreSQL） | 同 VPS 或私有雲 | 不使用中國 / 美國主流雲端（AWS、阿里雲等）|
| Redis | 同 VPS | |
| S3 儲存 | Backblaze B2 / Hetzner Object Storage | 隱私友善替代方案 |
| CDN / 反向代理 | Cloudflare（Proxy 模式，隱藏真實 IP）| |
| 爬蟲節點 | 分散多 IP VPS | 避免被目標網站封鎖 |

### 16.2 域名 & 匿名性

| 項目 | 建議 |
|------|------|
| 域名註冊商 | 使用支援 WHOIS 隱私保護的註冊商（如 Njalla、Epik）|
| 主站 / API 分離 | 主站域名與 API 子域名分離（如 `api.domain.com`）|
| 不使用記名 CDN | 避免使用境內或強制實名 CDN 服務 |

### 16.3 Container 化部署

```yaml
# docker-compose 概念示意
services:
  api-gateway:
    image: sdt-gateway:latest
    ports: ["443:3000"]
  auth-service:
    image: sdt-auth:latest
  order-service:
    image: sdt-order:latest
  payment-monitor:
    image: sdt-payment:latest
  notification-service:
    image: sdt-notify:latest
  postgres:
    image: timescale/timescaledb:latest-pg16
  redis:
    image: redis:7-alpine
```

---

## 17. 非功能性需求

| 指標 | 目標值 | 量測方式 |
|------|--------|---------|
| API 回應時間（P95） | ≤ 300 ms | APM 監控 |
| 系統可用性 | ≥ 99.5%（月均） | Uptime 監控 |
| 付款確認延遲 | ≤ 90 秒（19 TRON 區塊）| 鏈上監控 |
| 競拍即時更新延遲 | ≤ 2 秒 | WebSocket 壓測 |
| 前端首次載入（FCP） | ≤ 2.5 秒 | Lighthouse |
| 多語系覆蓋 | 繁中 / 簡中 / 英文（AI 即時補其他語言）| — |
| 24×7 門號自動開通 | 全自動，無需人工介入 | — |
| 資料備份週期 | 每日一次 | 備份監控告警 |
| 備份保留期限 | 30 天 | — |
| 最大並行用戶 | 1,000 同時在線（初期目標）| 負載測試 |

---

*本規格書版本 1.0，後續功能變更請依 Change Request 流程遞增版本號並記錄 Changelog。*
