# OCI Instance Watcher — 狀態

## 是什麼
自動搶 Oracle Cloud A1 免費 VM（4 OCPU / 24 GB）的 GitHub Actions 腳本。
每 5 分鐘跑一次，搶到後自動停止。

## 目標配置
- Region：US West
- Shape：VM.Standard.A1.Flex
- OCPU：4 / RAM：24 GB / Storage：200 GB

## 目前狀態
- [x] GitHub Secrets 填寫完成（8 個 secrets 全部設定）
- [x] Workflow 啟用並正常運行
- [ ] VM 搶到（持續中）

## 執行紀錄
| Run | 觸發方式 | 時間 | 結果 |
|-----|----------|------|------|
| #1 | 手動 | 2026-06-21 | 15s 結束（可能初始配置問題） |
| #2 | 手動 | 2026-06-21 | 8m 37s 完成，workflow 正常，無 capacity（5 次嘗試均回傳 Out of host capacity） |

**Cron 狀態**：`*/5 * * * *` 已啟用，會持續每 5 分鐘自動跑，直到搶到 VM。

## 注意事項
- Run #2 耗時 8m 37s 符合預期（5 次 × 1 分鐘間隔）
- Workflow job 狀態顯示「succeeded」——這只代表腳本正常執行完畢，不代表 VM 已建立
- VM 建立成功的唯一判斷依據：log 出現 `"lifecycleState"` 或 `✅ SUCCESS`
- Node.js 20 deprecation warning（非問題，不影響執行）

## Email 通知設定
搶到 VM 時自動發 email 到 ryandotcheng@gmail.com（使用 dawidd6/action-send-mail@v3 + Gmail SMTP）

需額外新增的 GitHub Secrets：
- `MAIL_USERNAME`：Gmail 帳號（例如 ryandotcheng@gmail.com）
- `MAIL_PASSWORD`：Gmail App Password（非登入密碼，見下方說明）

**取得 Gmail App Password 步驟：**
1. 前往 https://myaccount.google.com/security
2. 確認已開啟「兩步驟驗證」（必須先開啟才能用 App Password）
3. 搜尋「應用程式密碼」或前往 https://myaccount.google.com/apppasswords
4. 選擇應用程式：「郵件」、裝置：「其他」，輸入名稱 `OCI Watcher`
5. 點擊「產生」，複製顯示的 16 字元密碼
6. 將此密碼貼到 GitHub Secret `MAIL_PASSWORD`

## 下一步
1. **新增 2 個 GitHub Secrets**（`MAIL_USERNAME` / `MAIL_PASSWORD`）後，email 通知即生效
2. 等待 cron 自動執行直到搶到 VM
3. 搶到後收到 email → 前往 OCI Console 指派 Public IP
4. 用 `ssh -i ~/.ssh/id_rsa opc@<ip>` 連線確認
5. 更新此檔案，勾選「VM 搶到」
