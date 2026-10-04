# jev-mobile-mcp 發想與待辦

（截至 2026-10-04）

狀態符號：⬜ 未開始 ｜ 🟡 進行中 ｜ ✅ 完成

優先：P1 已實測到的痛點或高效益且成本低到中 ｜ P2 值得做 ｜ P3 觸發條件窄或收益小

## 完成度總覽

| # | 項目 | 狀態 | 優先 | 備註 |
|---|---|---|---|---|
| A1 | OCR 補充無障礙樹（`list` 的 `ocr` 參數） | ✅ | — | macOS Vision，經 JXA 呼叫 |
| A2 | 精簡元素清單（畫面外、空容器、重複 label） | ✅ | — | `compactElements`：只留目前畫面範圍內的元素、去掉空容器、只對多行合併 label 去重；有無 TypeSafe key 都生效，首頁約 −60% |
| A3 | 由 Jev 依描述選擇點擊目標 | ✅ | — | `mobile_tap`，僅設定 `TYPESAFE_API_KEY` 時註冊；server 在 macOS 時先用 OCR 問 Jev，無把握才讀無障礙樹並合併已讀的 OCR（不重跑 OCR），仍無把握就不點、回傳候選並附上判斷所用的元素清單（樹＋OCR），agent 直接挑一個點擊、不必再 `list`，ref 過期時重讀一次；Flutter debug app 的 10 步實測由 122 秒降到約 80–85 秒 |
| A4 | 由 Jev 決定操作與目標，server 端自跑迴圈（`mobile_run_goal`） | ⬜ | — | 依 A3 的效果再評估；先要有 B5、B7 的逐步數據 |
| A5 | 等待條件（server 端輪詢直到畫面出現指定內容） | ⬜ | P1 | 已具體化為 `mobile_wait_for`：以 OCR 輪詢、可當 batch step、`timeoutMs=0` 即為斷言；單輪失敗視為未出現，涵蓋冷啟動等待 |
| A6 | `list` 加 `tree: false`，只跑 OCR、不 dump | ⬜ | P2 | 確認畫面時省掉 6–10 s 的 dump；與 A5 共用讀畫面原語 |
| A7 | `MobileDevice` 保留 mobilecli 的 `placeholder` | ⬜ | P2 | 預設路徑把輸入框 hint 丟掉了，legacy 反而有 |
| A8 | `mobile_tap` 樹狀路徑改用座標點擊，刪掉 ref 重試迴圈 | ⬜ | P1 | ref 點擊讓 mobilecli 再 dump 一次（6–10 s），節點數不變時還會點錯；淨刪程式碼 |
| A9 | `compactElements` 保留 iOS 空白的 TextView／Picker | ⬜ | P3 | 等 B4 確認空 TextView 確實沒有 name／value |
| B1 | 查清 `dump ui` 為何慢 | ✅ | — | 已查明：mobilecli 對可除錯的 Flutter app 改走 Dart VM service 走訪整棵 render tree（6–10 秒）；修正屬 mobilecli 範疇，profile build 的效果未量測 |
| B2 | 橫向畫面的座標與過濾 | ✅ | — | 畫面範圍改由 dump 的視窗根元素決定（mobilecli 開自動旋轉時回報錯誤方向）；Android 模擬器實測，iOS 見 B4 |
| B3 | canvas 畫面的 OCR 端到端點擊驗證 | ⬜ | P3 | 測試曾中斷 |
| B4 | iOS 模擬器驗證 | ⬜ | P2 | 已具體化為 7 步手動量測清單；結果決定 D4、A9 的去留 |
| B5 | `mobile_tap` 每次嘗試記錄分段耗時、被拒樣本、Jev usage；門檻可由環境變數調整 | ⬜ | P2 | 門檻 0.5 沒有資料可校準，A4 的評估也缺逐步耗時 |
| B6 | 在 macOS 上對真實 Vision OCR 跑 smoke test | ⬜ | P2 | JXA＋Vision 沒有任何測試；OCR 壞掉只會靜默退回 dump |
| B7 | README 的 10 步 field test 可重現 | ⬜ | P2 | prompt 與統計腳本納入版本控制，之後的改動有基準可比 |
| C1 | `compactElements` 以 `status_bar_container` 範圍濾掉 Android 狀態列 | ⬜ | P2 | 連帶擋掉通知摘要外流到 TypeSafe；實測 Jev 請求 −26% |
| C2 | OCR 截圖改用 JPEG | ⬜ | P3 | 每次 OCR 約省 235 ms；Vision 輸出 A/B 一致才切換 |
| C3 | 送 Jev 時去掉 resource-id 的 package 前綴 | ⬜ | P2 | `id` 佔請求 44%；只改 `describe` 一行，實測請求 −20% |
| D1 | 補回同步時遺失的 `.gitignore` 規則，同步流程加跑單元測試 | ⬜ | P1 | 7b1aecd 漏掉 `plugin/.claude-plugin/types` |
| D2 | fork 專用 CI | ⬜ | P1 | 上游 Build 卡在 self-hosted e2e；fork 的 48 個測試從沒在 CI 跑過 |
| D3 | `tap=` 改用裁到畫面內的中心 | ⬜ | P2 | 部分在畫面外的元素，`tap=` 可能落在畫面外 |
| D4 | `withOcrElements` 改為重用 `readScreenText` | ⬜ | P3 | 兩份 OCR 映射的方向判斷不同 |
| D5 | Android Flutter 輸入非 ASCII 可能沒貼進去，工具卻回報成功 | ⬜ | P2 | 程式碼推論，待實測；確認後升 P1、修在上游 |
| D6 | `mobile_tap` 失敗訊息補上「可能在畫面外，先捲動」 | ⬜ | P3 | 只改錯誤字串 |
| E1 | README.zh-TW 實測數據與英文版不一致 | ⬜ | P1 | zh-TW 仍是 df67774 之前的舊數據，結論相反 |
| E2 | 啟用 Jev 時補上 `mobile_tap` 優先的 server instructions | ⬜ | P2 | 上游 instructions 叫 agent 先 list，與 `mobile_tap` 矛盾 |
| E3 | README 寫明 repo 內附 plugin 裝的是上游 npm 套件 | ⬜ | P2 | plugin 沒有 `mobile_tap`、OCR、`compactElements` |
| E4 | README 揭露遙測去向，並修正 Jev 隱私說明 | ⬜ | P2 | 隱私段只寫 OCR 文字，實際還送 id、type、座標、target 等 |
| E5 | fork 打帶前綴的版本 tag，安裝 spec 改釘 tag | ⬜ | P3 | 目前固定 `#main`，無法判斷使用者跑的版本 |
| E6 | 修正「截圖慢又耗 token、list 快又便宜」的說法 | ⬜ | P1 | 實測相反：截圖 0.3–0.4 s、約 620 tokens；list 要 dump 9.4 s、4,329 字元 |

建議順序：E1 → E6 → D1 → D2（都是 S，先把文件與 CI 的地基補好）→ A8（S、淨刪程式碼）→ A5（含 A6）→ B5 → 其餘 P2。

## 項目說明

### A2 精簡元素清單

`list` 的輸出包含畫面外的列與被推到背後的前一頁、沒有內容的容器，以及 Flutter 在每個子節點重複的合併 label。精簡後可降低 agent 每次讀取畫面的 token。

### A3 由 Jev 依描述選擇點擊目標

agent 只給一句描述，server 端把元素表交給 Jev 選擇，agent 不必讀整份清單。前提是元素表夠精簡（A2）。

### A4 `mobile_run_goal`

jev-ultrafast 快的主因：每一步由 Jev 同時決定操作與目標。工作量大，且需獨立驗證完成狀態。

2026-10-04 評估：照搬 jev-ultrafast 的單請求多頭推測式 Choice、以 Noul 驗證 DONE 的方案先擱置。目前沒有 A3 的逐步數據（每步耗時、agent 往返佔比、信心不足的失敗率），無法判斷 agent 往返是不是瓶頸。B5、B7 有數據後再談停止條件、成本上限，以及與 A5／batch 的關係。

### A5 等待條件 → `mobile_wait_for`

流程中「等待畫面載入」目前靠 agent 反覆 `list` 判斷，耗 token 也多一次往返。11 步流程中 2 次 `list` 約佔 8 成字元（jev-tap §8），Flutter debug app 每次 dump 要 6.3–10.2 s。batch 也沒有等待步驟，`listElementsAtEnd` 在最後一步完成後立刻 dump（`server.ts:1270-1272`）。

第一版：

- `mobile_wait_for(device, text, timeoutMs=0)`，用 `tool()` 註冊，自動可當 batch step，「點擊＋等待＋點擊」一次 batch 完成。
- macOS 用 `readScreenText` 輪詢（約 1–1.5 s／輪），比對正規化後的子字串（`ocr.ts:72` 的 `normalize` 要 export）；非 macOS 退回 dump＋`compactElements`。
- 命中回一行含 `tap=` 的結果；逾時丟 `ActionableError` 並附上 `formatElements(elements, "text")`。
- `timeoutMs=0` 就是「畫面上有沒有這段文字」的斷言。
- 不依賴 Jev，任何設定都註冊。

第二步才在 `isJevEnabled()` 時接受語意條件（Jev Noul 判斷，`chooseElement` 的 fetch 抽成 `askJev`）。「文字消失」條件等真的需要再加。注意轉場時舊頁可能殘留同名文字。`skills/mobile-automation/SKILL.md` 第 4 步的「重新 list」要同步改寫。

冷啟動補充（第二輪研究實測：Pixel 9a 模擬器、FindRestaurant debug、load average 約 25）：`mobile_launch_app`（`mobile-device.ts:226-233`、`server.ts:571-572`）與 `mobile_open_url` 送出 intent 就回傳；Displayed 出現在 +47.8 s 與 +1m45s，+40 s 的 dump 回 `no XML content found`，約 65 s 內 foreground 與 dump 都還是前一個 app。所以：

- 每一輪讀取包 try/catch，失敗視為「尚未出現」，逾時的 `ActionableError` 附上最後一次錯誤；加 `ponytail:` 註解標明永久性錯誤要等到 deadline 才報。
- dump 走 `executeCommand` 不帶 timeout（`mobilecli.ts:84-91`），30 s 的 TIMEOUT 只套在截圖；description 要註明單輪 dump 可能讓實際等待超過 `timeoutMs`，冷啟動建議數十秒的 timeout。
- SKILL.md 加 1–2 句：launch 後先用 `mobile_get_foreground_app`（約 0.25 s）確認 package，再用 `mobile_wait_for` 等首頁文字。冷啟動數據只對 launch_app 實測過。
- 不改用 `am start -W`（見「評估後不做」）。

### A6 `list` 的 `tree: false`

`list` handler 一定先 `robot.getElementsOnScreen()`（`server.ts:718-728`），`ocr: true` 只是 dump 之後再合併 OCR；ocr-first 規格 :124 已點名「確認畫面時的 list」不在範圍內。加 `tree: z.boolean().optional()`，預設 `true`，既有行為不變；`false` 時回傳 `formatElements((await readScreenText(robot)).elements, format)`，不支援 OCR 時丟 `ActionableError`。結果沒有 ref，只能照 `tap=` 點，要寫進 description。每次確認畫面約省 5–9 s。

### A7 `placeholder`

mobilecli 1.0.17 的 dump 有 `placeholder` 欄位（binary 內有 `json:"placeholder,omitempty"` 與 `setPlaceholderFromHint`），但 `UIElementResponse` 與 `flattenUIElement`（`mobile-device.ts:32-51`、`83-97`）沒有讀它。legacy `AndroidRobot` 會把 hint 填進 label（`android.ts:343`），預設路徑反而少了這項資訊。改兩行：加 `placeholder?: string`，`label: element.label || element.placeholder`。代價是 `mobile-device.ts` 目前與上游完全相同，改了會多一個同步衝突點，可考慮同時送 PR 給上游。驗收：空白 TextField 的畫面上，list 出現 `label="<hint>"`，並補單元測試。

### A8 `mobile_tap` 樹狀路徑改用座標點擊

`tapByDescription` 對帶 ref 的樹元素呼叫 `robot.tapByRef`（`jev.ts:224-238`）。mobilecli 1.0.17 的 `resolveRefTapPoint` 每次都重新 DumpSource、依位置編號、沒有過期檢查，並取整個 rect 的中心（`commands/input.go:146-152`、`165-180`）。結果是 Flutter debug app 每次 ref 點擊多付一次 6–10 s 的 dump；畫面變了但節點數相同時，還會悄悄點到別的元素。

改成一律 `robot.tap(...centerOf(element, screen))`，刪掉 `tapByRef` 分支、`MAX_TAP_ATTEMPTS` 與重試迴圈，回傳訊息仍用 `describeElement` 帶 ref。`test/jev.test.ts` 的 :183、:274、:303、:315、:323-331、:340、:351 斷言 `taps` 為 `["@e2"]`，要改成裁過的中心座標。這也解掉「評估後不做」裡 server 端 dump 快取與重讀設計的衝突。驗收前先在 FindRestaurant 量 `io tap @eN` 與 `io tap x,y` 的耗時差，確認約等於一次 dump。

### A9 iOS 空白 TextView／Picker

mobilecli 的 devicekit 刻意保留沒有 label 的 TextView、Picker、PickerWheel（`source.go` 的 `alwaysIncludedSourceTypes`），但 `INTERACTIVE_TYPE`（`compact-elements.ts:4`）沒有這些型別，`hasContent`（:53-54）會把它們丟掉，原生 iOS 的空白多行輸入框因此從 list 與 Jev 選項表消失。不能直接在不分大小寫的子字串 regex 加 `TextView`，否則會留下 Android 大量空的 `android.widget.TextView` 排版元件。改為對整個 type 錨定比對 `TextView|Picker|PickerWheel`，測試補「iOS 空 TextView 保留、Android 空 `android.widget.TextView` 仍濾掉」兩案。等 B4 實測確認空 TextView 確實沒有 name／value；有的話就不做。

### B1 `dump ui` 速度

在 FindRestaurant 上一次可達數秒，原因未明。

### B2–B4 驗證缺口

橫向畫面、canvas 繪製的文字、iOS 模擬器都還沒有實測。

### B4 iOS 模擬器最小驗證清單

讀 mobilecli 1.0.17 原始碼推得：iOS 預設路徑的座標單位一致（points），Retina 不會造成點偏。風險在：

- 原生 iOS dump 濾掉 Application／Window／Other（`source.go` 的 `acceptedSourceTypes`），通常沒有 0,0 根元素，`currentViewport`（`compact-elements.ts:34-50`）多半退回 `getOrientation`；例外是帶 accessibilityIdentifier 的元素，以及帶 label／name 的 Table、CollectionView、ScrollView。
- devicekit 對 PORTRAIT／LANDSCAPE 以外的值一律回 portrait（`orientation.go`），D4 的觸發條件在原生 iOS 上大致等於「橫向」。
- `formatElements`（`format-elements.ts:21-27`）在 name==label 時重複輸出。

手動量測，只寫 docs、不做自動化：

1. `device info` 直、橫向的 points 尺寸與 scale
2. 設定 app 的 dump：有無 0,0 全螢幕元素、rect 單位、name==label 佔比
3. 橫向時 list 右半邊是否被濾掉（只記結果，修法交給 D4）
4. Flutter debug app 的 dump 耗時、根元素、merged label 去重
5. `list ocr:true` 與 `mobile_tap` OCR 路徑，直、橫向各點一次
6. `mobile_tap` 樹狀路徑耗時（對照 A8）
7. 空白 UITextView 是否出現在 list（對照 A9）

### B5 `mobile_tap` 的決策紀錄與門檻

`jev.ts:12-13` 的 ponytail 註解要求在實測紀錄上調整門檻，實測值剛好落在 0.5 附近（0.46 被拒、0.57 通過、選單按鈕 0.60／0.66）。`server.ts:140` 的 trace 只記成功的回應，缺少：

- 被拒的樣本（`ActionableError` 走 `:147-150`，不 trace）
- OCR 階段被判無把握時第一輪的 confidence（`jev.ts:200-205` 丟掉了）
- ocr／dump／jev／tap 的分段毫秒數與重試次數
- Jev 回應中的 usage（`jev.ts:159` 丟掉了）

在 `choose`／`tapByDescription` 內用既有 `trace()` 每次嘗試寫一行（有設 `LOG_FILE` 才寫檔）。`MIN_CONFIDENCE` 比照 `TYPESAFE_MODEL` 讀 `TYPESAFE_MIN_CONFIDENCE`，預設 0.5。不另開 log、不送 posthog，以免畫面文字外流。標註正確與否仍要人工做。

### B6 Vision OCR smoke test

`VISION_SCRIPT` 與 `recognizeText`（`ocr.ts:19-56`）沒有任何測試，`jev.test.ts` 注入的是假 recognize；`choose()` 在 OCR 拋例外時靜默退回 `chooseFromTree`。給 `readScreenText` 一個假 robot（`getScreenshot` 回傳 `docs/screenshots/mobile-mirror.png`、`getScreenSize` 回 2000x1173），就能測到 bridge、JSON 解析、座標映射整條路徑。非 darwin 用 `test.skip`。斷言非空、至少一筆含 MILLIWAYS（不分大小寫）、座標在畫面內；不斷言總筆數，免得 Vision 改版就 flaky。

### B7 field test 可重現

`README.md:141-190` 是 fork 最主要的效益主張，但 :159、:166 自己承認 jev 的 prompt 多給了座標、jev 欄位也沒有重跑，input-equivalent 照 :155 的公式手算。把兩個 server 共用的 prompt 存成 `test/field/findrestaurant-10step.md`（拿掉只給 jev 的座標），新增 `scripts/field-usage.mjs` 讀 subagent transcript JSONL，輸出時間、請求數、cache read/write、output、input-equivalent。不做自動執行框架。之後 A4、A5、門檻調整、上游同步都用它比較。

### C1 Android 狀態列

`hasContent` 只要有 identifier 就保留（`compact-elements.ts:53-54`），狀態列元素每次都進 list、`mobile_tap` 失敗清單和 Jev 選項表。第二輪實測狀態列（`status_bar_container` 0,0,1080x142）有 25 個元素，其中通知圖示的 content-desc 是「<app> 通知：<標題>」，會把其他 app 的通知標題（多半是寄件人）送到 TypeSafe。原本「identifier 以 `com.android.systemui:` 開頭且沒有 text、label 才濾掉」的規則濾不到這些帶 label 的元素。

改成一條規則：找到 identifier 為 `com.android.systemui:id/status_bar_container` 的元素，就移除整個落在它 rect 內的元素；找不到（iOS、通知欄拉下）就不做，加 `ponytail:` 註解標明只看矩形。放在 `compactElements`，list、失敗清單與 Jev 樹路徑一起生效。OCR 優先路徑（`jev.ts` 約 187-206）不經過樹，狀態列的時間仍會送出，敏感度低，暫不處理。實測 Jev 請求 11,528 → 8,526 bytes。實作前送一則真實訊息通知，確認標題確實出現在 content-desc。

### C2 OCR 截圖改 JPEG

OCR-first 之後每次 `mobile_tap` 都截圖，PNG 中位約 480 ms、JPEG q90 約 245 ms。`readScreenText`（`ocr.ts:108`）與 `withOcrElements`（`:133`）寫死 PNG；改成 `getScreenshot({format: "jpeg", quality: 90})`，尺寸改用既有 `getJpegDimensions`（`jpeg.ts:14`），依 magic bytes 選 parser（legacy robot 可能忽略 format 回 PNG）。驗收：FindRestaurant 的側選單、清單、設定頁各截 PNG／JPEG，Vision 輸出完全一致才切換；q80 已出現小字錯讀，有差異就不做並記進「評估後不做」。

### C3 送 Jev 時去掉 resource-id 前綴

`describe`（`jev.ts:51-60`）直接送 `element.identifier`，Android resource-id 都帶 `com.xxx:id/` 前綴。實測一次請求 11,528 bytes，`id` 佔 5,038，83 個元素裡 62 個只有 id。改為 `id: element.identifier?.replace(/^[\w.]+:id\//, "") || undefined`，list 的 `id=` 不變，`jev.test.ts` 的 buildRequest 測試補 `com.x:id/foo` → `foo`。不同 package 的同名 id 會變成同一字串，但 center、size、type 仍可區分。實測請求 −20%；C1 落地後剩餘的 id 會變少，對延遲或費用的實際效果等 B5 有數據再確認。

### D1 `.gitignore` 與同步流程

HEAD 的 `.gitignore` 以 `.claude/workflow-state` 結尾，`7b1aecd^2` 的兩行（註解與 `plugin/.claude-plugin/types`）不見了，plugin 載入時產生的 types 可能被誤 commit；改一行取兩邊聯集。`gen-sync-mobile-mcp` SKILL.md 的解衝突表只列 README.md 與 LICENSE，補三條規則：

- `.gitignore` 取聯集
- `README.ja.md`、`README.zh-CN.md` 出現 modify/delete 衝突時維持刪除
- fork 改過的上游檔一律保留雙方改動，清單用 `git diff upstream/main --stat` 即時產生

第 6 步改成必跑不需裝置的子集：明列 10 個 `*.test.ts`，或排除 `android.ts`、`ios.ts`、`iphone-simulator.ts`（`playwright.config.ts` 的 testMatch 是 `"*.ts"`）。

### D2 fork 專用 CI

`build.yml` 與上游相同，`e2e_test` 是 `runs-on: self-hosted`，fork 沒有 runner，最近 10 次 run 全部 cancelled 或失敗。build job 只跑 audit、lint、build（`build.yml:39-48`），jev、ocr、compact-elements、format-elements 的 48 個測試從沒在 CI 跑過。`.c8rc.json` 的 include 是 `lib/**/*.js`，測試卻 import `../src`，覆蓋率顯示 Unknown (0/0)。

不碰上游檔：`gh workflow disable Build` 停用上游 workflow，另加 `.github/workflows/fork-ci.yml`，在 ubuntu 跑 lint、build，用明確檔案清單跑 `*.test.ts`，c8 用命令列 `--include 'src/**'` 覆寫。第一次跑要確認 `server-*.test.ts` 在沒有裝置時不會在 module 載入階段 throw。

### D3 `tap=` 裁到畫面內

`compactElements` 保留部分可見的元素（`compact-elements.ts:11-14`），`formatElements` 的 `tap=` 卻取整個 rect 的中心（`format-elements.ts:40-43`）。`mobile_tap` 失敗訊息的 Closest 用 `centerOf`（`jev.ts:29-46`）裁過，同一則訊息裡的清單沒有裁；沒有 ref 的元素只能照 `tap=` 點，照抄就點空。tap-failure 規格 §8 曾記為「本案不修」。

`centerOf` 移到 `compact-elements.ts`，`formatElements` 多收一個可選的 `screen`；`server.ts:729` 把 viewport 抽成 const 再傳，`jev.ts:220` 直接傳。不要在 `compactElements` 裡裁 rect，會改變 `at=`、`size=` 與 JSON 座標語意。保留 `screen-1` clamp。測試：y=-100、h=300 時 `tap=` 的 y 為 100。

### D4 `withOcrElements` 重用 `readScreenText`

`readScreenText`（`ocr.ts:107-115`）依截圖寬高定方向；`withOcrElements`（`ocr.ts:122-135`）用 `currentViewport`，沒有根元素時退回 robot 回報的方向，B2 已記錄這個值可能錯。縮成 `mergeOcrElements(elements, (await readScreenText(robot)).elements)`，淨刪約 6 行，`recognize` 改可注入並補測試。要徹底解決，還得把 `readScreenText` 回傳的 screen 交給 `server.ts` 的 `compactElements`，`jev.ts:180-184` 的 `chooseFromTree` 也有同樣問題。只在「沒有根元素又是橫向」時觸發，所以 P3。

### D5 Android Flutter 輸入非 ASCII

`mobile_type_keys`（`server.ts:827-835`）呼叫 `sendKeys`（`mobile-device.ts:251-253`，`mobilecli io text`）後不做確認就回 `Typed text`。mobilecli 1.0.17 Android 的 `SendKeys` 對非 ASCII 先 SetClipboard、送 `KEYCODE_PASTE`，再 defer 清空剪貼簿；Flutter 的 `DefaultTextEditingShortcuts` 只把貼上綁到 ctrl/meta+V 與 shift+Insert，engine 的 `InputConnectionAdaptor.handleKeyEvent` 對 unicodeChar 為 0 的鍵回 false。推論：中文進不了 Flutter `TextField`，使用者剪貼簿還被清掉；原生 `EditText` 不受影響。

先實測：FindRestaurant 搜尋框輸入「台北」，dump 讀欄位值，並以原生 EditText 對照。確認失效就升 P1：修正送上游（改送 ctrl+v，並處理清空剪貼簿早於 Flutter 非同步 `Clipboard.getData` 的時序）；fork 先在 SKILL.md 寫繞道（`mobile_clipboard` 設文字後長按貼上，會覆寫剪貼簿）；上游遲遲不修才在 `MobileDevice.sendKeys` 加約 5 行 patch。實測貼得進去就記進「評估後不做」。

### D6 失敗訊息補捲動提示

目標在畫面外時，`tapByDescription` 的錯誤（`jev.ts:218-221`）只列可見元素並寫「Pick one and tap it」；`compactElements` 刻意丟掉畫面外元素（`compact-elements.ts:9-14`、`64-66`），原生 app 的 dump 也看不到未 attach 的列，agent 無從得知要捲動。結尾改成「...; if the target is not among them it may be off screen, swipe with mobile_swipe_on_screen and call mobile_tap again」，elements 為空的 ` none` 分支也補上；`test/jev.test.ts:224`、`:243` 一起改。不做 server 端自動捲動（屬 A4）。目前沒有 agent 因此點錯的紀錄，所以 P3。

### E1 README.zh-TW 數據落後

df67774 把 `README.md` 的 field test 重跑成上游 1.0.8（137.2 s 對 70.0 s、295.3k 對 166.3k、重試 2 次），`README.zh-TW.md:145-161` 仍是 69.6 秒、201.8k、重試 4 次，還寫著「瓶頸不在工具」。要同步：請求數、output、穩定期、註腳 ¹ 的原始總量、一般 input 30–56，以及「Why upstream took longer」整段（`README.md` 約 141–189 行）。在 `gen-sync-docs-by-branchs` 或 `gen-update-publish-info` 的檢查清單加一條：改到 README.md 使用者看得到的內容時，同一個 commit 一起改 zh-TW。

### E2 Jev 的 server instructions

`server.ts:73-90` 的 `SERVER_INSTRUCTIONS`（上游原文）叫 agent 用 `mobile_list_elements_on_screen` 讀畫面，沒提 `mobile_tap`；`mobile_tap` 的 description（`server.ts:742`）卻寫 no need to list elements first。不動上游常數，`server.ts:98` 改成 `SERVER_INSTRUCTIONS + (isJevEnabled() ? JEV_INSTRUCTIONS : "")`，`JEV_INSTRUCTIONS` 放 `jev.ts`，2–3 行：有文字的目標直接用 `mobile_tap`，也可放進 batch（例：`[mobile_tap, mobile_type_keys, mobile_tap]`）。SKILL.md 第 3 步加一句「若有 `mobile_tap`」。效益未量測，保留前用 B7 比較改動前後的請求數。

### E3 plugin 裝的是上游

`marketplace.json` 指向 `./plugin`，`plugin.json` 的 mcpServers 卻是 `npx -y @mobilenext/mobile-mcp@latest`。用 `claude mcp add` 裝了 fork、又啟用 plugin 的人，`/mobile-mirror` 會連到上游 server（`register.tsx:9`），兩個 process 搶同一台裝置。第一步：兩份 README 的安裝段落加一行說明。等真的需要 fork 版的 `/mobile-mirror`，再把 mcpServers 改成 `github:Yomiamy/jev-mobile-mcp#<tag>` 並加 `TYPESAFE_API_KEY`，sync skill 註明這段保留本地版本；改之前先查文件確認 `${VAR:-}` 展開是否支援。

### E4 遙測與隱私說明

`server.ts` 的 `posthog()` 寫死上游 api_key 與 `Product: "mobile-mcp"`，每次工具呼叫送 `tool_invoked`／`tool_failed`，加上啟動時的 launch（:222）與 scarf ping（:210），`MOBILEMCP_DISABLE_TELEMETRY=1` 全部關閉。兩份 README 的隱私段加一行，`claude mcp add` 範例示範 `-e MOBILEMCP_DISABLE_TELEMETRY=1`。不改 Product 欄位（要動上游行，價值低）。

同一段修正 TypeSafe 的說明：`README.md:127` 只寫送出 OCR 文字，實際上 `describe`（`jev.ts:51-60`）送 type、text、label、name、value、id、center、size，`buildRequest`（:61-86）再加螢幕寬高與 target 原句；非 macOS、OCR 失敗或無把握時改送無障礙樹（:189-199）。密碼欄寫「本 server 不另外遮蔽任何欄位」，不替平台背書；狀態列通知摘要寫成「可能」送出（C1 落地後刪掉這句）。兩份 README 各 2–3 句。因為現行說明寫錯，由 P3 升為 P2。

### E5 版本 tag

`README.md:122、218、232` 的安裝 spec 都是 `#main`，:235-241 要使用者手動刪 `~/.npm/_npx`；origin 沒有 tag，`package.json` 是上游的 0.0.1。發版打 `jev-0.1.0` 這類帶前綴的 tag，用 `git push origin <tag>` 推（本機有上游 tag，不要 `--tags`）。README 的 spec 改 `#jev-x.y.z`，npx 依 spec 分開快取這點先實測。fork 的變更紀錄另寫，不進上游 `CHANGELOG.md`。

### E6 截圖與 list 的成本說法

預設截圖是 jpeg q75、maxSize 1024（`server.ts:891-898`），Pixel 9a 上輸出 456x1024、19–61 KB、0.3–0.4 s，約 620 tokens；FindRestaurant 的 list 經 compact 後 51 個元素、4,329 字元，單獨 dump 9.4 s。文件卻寫相反，把 agent 推向慢的路。要改：

- `README.md:12-13`、`README.zh-TW.md:13-14`：list 改寫成「有 ref 與精確座標」，截圖的缺點改成「沒有 ref，座標靠模型目測」
- `SKILL.md:22-24` 刪掉 faster, cheaper
- `SKILL.md:33-34` 第 4 步加一句「只需確認畫面切換時用截圖較快」，引用 9.4 s 並註明測試環境

這是過渡寫法，A5 上線後改以 `mobile_wait_for` 為主。上游 `SERVER_INSTRUCTIONS`（`server.ts:73-90`）的同類說法不動，交給 E2 的 `JEV_INSTRUCTIONS`。

## 評估後不做

研究中被實測或審查否決的方向，記下來避免重複投入：

| 方向 | 不做的理由 |
|---|---|
| 常駐 Swift／JXA OCR helper | `osascript` 啟動＋`import Vision` 約 40 ms，不是瓶頸 |
| Vision fast 等級（`recognitionLevel=1`） | 不支援中文 |
| 縮小截圖再 OCR | 2000 寬的 Mac 截圖只快 30–90 ms，辨識筆數從 20 掉到 11 且出現錯讀；裝置截圖（1080→540）尚未重測 |
| viewport 偵測快取 | `MobileDevice` 的 device info 熱啟動約 0.1 s，相對 dump 可忽略（Pixel 9a 模擬器、mobilecli 1.0.17） |
| server 端 dump 快取 | server 不知道畫面何時改變，與 `jev.ts:224-236` 的「ref 過期就重讀」衝突（A8 落地後衝突消失，但「不知道畫面何時改變」仍成立） |
| OCR 與樹的去重改字元重疊比例 | 提案的門檻擋不住它自己舉的錯讀例子，現有正規化比對已足夠 |
| OCR 改非同步 `execFile` | OCR 跑完才建立 Jev 的 timeout，兩者不重疊；卡住 event loop 的情境沒有實例 |
| `mobile_tap` 加 `expect` 點擊後驗證 | 與 A5 重疊；點擊後才驗證也擋不住過期點擊 |
| `mobile_read`（Jev 讀值工具） | 需求是推測的；每次多一次付費呼叫＋OCR，只省約 3k 字元 |
| 為 `mobile_tap` 當 batch step 補測試 | batch 走同一張 `toolCallbacks` Map，既有 batch 測試已涵蓋 |
| 預先追蹤上游 ROADMAP「Flutter UI support」 | 只標 Planned、沒有格式可對照；格式變了 `compactElements` 也會平順降級 |
| A4 照搬 jev-ultrafast 多頭 Choice | 時機未到，見 A4 說明 |
| 非 macOS OCR 後端（Windows.Media.Ocr／tesseract） | 找不到非 macOS 使用者（Dockerfile 來自上游 a06b9e1），非 macOS 已正確降級為只讀樹；有回報再評估 |
| mobilecli 指令加預設逾時（如 120 s） | 沒有無限卡住的實例；`execFileSync` 加上限仍會凍住 event loop，還可能誤殺 `apps install`；改的是上游檔 |
| 用 dump 的畫面外元素提示「目標在下面」 | 只有 Flutter debug 會列出少數預渲染列，原生畫面看不到，多數是假陰性 |
| `mobile_launch_app` 改用 `am start -W` | Flutter debug 冷啟動超過 mobilecli 的 30 s TIMEOUT，launch 本身會逾時；改由 A5 等待 |
| fork 自行遮蔽密碼欄 | mobilecli dump 沒有 password 欄位，fork 無從判斷；各平台本身已把值換成遮罩字元 |
| `mobile_type_keys` 前自動清除文字或先聚焦 | mobilecli 已支援 BACKSPACE，描述也寫明作用於已聚焦元素，沒有實際痛點 |
| 修改 `plugin/`（`/mobile-mirror`） | 與上游逐位元相同，要修應送 PR 給上游 |
| E4 補 `/mobile-mirror` 每張畫面送 PostHog | plugin 啟動的是上游套件，屬上游遙測；fork 自己發佈 plugin 時再補 |

## 第二輪研究結論（2026-10-04）

第一輪列為「尚未檢視」的 10 個範圍，各自的結論：

| 範圍 | 結論 |
|---|---|
| iOS 路徑 | 座標單位一致（points），Retina 不會點偏；風險在原生 iOS 通常沒有根元素、方向未知值一律當 portrait，以及 `INTERACTIVE_TYPE` 少了 TextView。全部未實測，量測步驟寫進 B4；順帶發現 ref 點擊會多一次 dump（A8） |
| 畫面外目標與捲動 | 只改失敗訊息（D6）。用 dump 的畫面外元素判斷「目標在下面」不可靠，原生 RecyclerView 不列，實測 deskclock 沒有任何畫面外元素 |
| 文字輸入 | 主要風險是 Android Flutter 輸入非 ASCII（D5，程式碼推論，待實測）。清除文字、先聚焦、鍵盤遮擋沒有實際痛點 |
| mobilecli 子程序錯誤與逾時 | fork 沒改過的上游程式碼，整體健康；唯一缺口是 `runCommand` 不帶 timeout（dump 實測 1.6–73 s），只在 A5 輪詢中處理 |
| 截圖 token 成本 | 縮放已處理好（jpeg q75、maxSize 1024，約 620 tokens），不需改程式；文件說法相反，列為 E6 |
| remote 裝置與 Streamable HTTP | 不需要改：OCR 在 server 所在的 Mac 上跑，remote 裝置也適用；HTTP 模式每個請求重建 server，`isJevEnabled()` 結果一致 |
| 非 macOS 的 OCR | 已正確降級，README 也寫明僅 macOS；找不到非 macOS 使用者，替代後端列入「評估後不做」 |
| 送往 Jev 的資料 | 密碼欄由平台遮罩，fork 不需另外處理。實際問題是冗餘與外流：id 前綴（C3）、狀態列通知摘要（C1）、README 漏寫欄位（E4） |
| `/mobile-mirror` hook | 與上游逐位元相同，且啟動的是上游套件；雙 server 情境已由 E3 涵蓋，要修應送 PR 給上游 |
| App 啟動後何時可操作 | launch／open_url 送出就回傳，Flutter debug 冷啟動實測 48 s 到 1m45s；併入 A5 規格與 SKILL.md 說明 |
