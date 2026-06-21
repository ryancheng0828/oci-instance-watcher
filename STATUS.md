# OCI Instance Watcher — 狀態

## 是什麼
自動搶 Oracle Cloud A1 免費 VM（2 OCPU / 12 GB）的 GitHub Actions 腳本。
每 5 分鐘跑一次，搶到後自動停止。

## 目標配置
- Region：US West
- Shape：VM.Standard.A1.Flex
- OCPU：2 / RAM：12 GB / Storage：200 GB

## 目前狀態（2026-06-21 更新）
- [x] GitHub Secrets 填寫完成（OCI 相關 8 個 + MAIL_USERNAME + MAIL_PASSWORD，共 10 個）
- [x] Workflow 已啟用
- [x] Email 通知功能已設定（僅在搶到 VM 時觸發，發送至 ryandotcheng@gmail.com）
- [x] actions/checkout 升級至 v6（Node 24 原生支援，消除 deprecation warning）
- [x] concurrency 設定已加入（cancel-in-progress: true，避免 scheduler 混亂）
- [x] Loop 縮短至 4 次 × 50s（確保每次 run < 5 分鐘，符合 cron 間隔）
- [ ] VM 搶到（持續中）
- [x] tests.yml 升級（checkout@v4、cache@v4、set-output 改 GITHUB_OUTPUT）

## 執行紀錄
| Run | 觸發方式 | 時間（UTC） | 結果 |
|-----|----------|------------|------|
| #1 | 手動 | 2026-06-21 | 15s 結束（初始配置問題） |
| #2 | 手動 | 2026-06-21 | 8m 37s，workflow 正常，無 capacity（5 次嘗試均 Out of host capacity） |
| #3 | 手動 | 2026-06-21 | 正常執行 |
| #4 | **Schedule** | 2026-06-21 08:44 UTC | 第一次 cron 自動觸發成功 |

**Cron 狀態**：`*/5 * * * *` 已啟用。Run #4 後排程出現延遲（run 超過 5 分鐘導致 scheduler 跳過），已修正 loop 縮短執行時間，待下次觸發確認恢復正常。

## Workflow 目前版本（oci-watcher.yml）
- `actions/checkout@v6`（Node 24 原生）
- `dawidd6/action-send-mail@v17`（Node 24 相容）
- `shivammathur/setup-php@v2`（floating tag，不需更新）
- `concurrency: { group: oci-watcher, cancel-in-progress: true }`
- Loop：4 次嘗試 × 50s sleep ≈ 總計 3.5 分鐘（< 5 分鐘 cron 間隔）

## Email 通知設定
搶到 VM 時自動發 email 到 ryandotcheng@gmail.com（dawidd6/action-send-mail@v17 + Gmail SMTP）

Secrets 已設定：`MAIL_USERNAME`（Gmail 帳號）、`MAIL_PASSWORD`（Gmail App Password）

## 已知待辦
- [ ] `tests.yml` 中 `actions/cache@v2` 需升級至 `@v4`（有 Node 20 deprecation warning）
- [ ] 持續監控 cron 排程是否穩定每 5 分鐘觸發

## 注意事項
- Workflow job 顯示「succeeded」不代表 VM 已建立，成功的唯一依據是 log 出現 `"lifecycleState"` 或 `✅ SUCCESS`
- 本地 oci-watcher.yml 已修改（concurrency + 4×50s），需 commit 到 GitHub 才生效

## 搶到 VM 後的步驟
1. 收到 email 通知 → 前往 OCI Console 指派 Public IP
2. `ssh -i ~/.ssh/id_rsa opc@<ip>` 連線確認
3. 更新此檔案，勾選「VM 搶到」
