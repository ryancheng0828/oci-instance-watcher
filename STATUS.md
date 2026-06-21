# OCI Instance Watcher — 狀態

## 是什麼
自動搶 Oracle Cloud A1 免費 VM（4 OCPU / 24 GB）的 GitHub Actions 腳本。
每 5 分鐘跑一次，搶到後自動停止。

## 目標配置
- Region：US West
- Shape：VM.Standard.A1.Flex
- OCPU：4 / RAM：24 GB / Storage：200 GB

## 目前狀態
- [ ] GitHub Secrets 填寫完成
- [ ] Workflow 啟用
- [ ] VM 搶到

## 需要填入的 Secrets（見下方收集清單）
OCI_REGION / OCI_USER_ID / OCI_TENANCY_ID / OCI_KEY_FINGERPRINT /
OCI_PRIVATE_KEY / OCI_SUBNET_ID / OCI_IMAGE_ID / OCI_SSH_PUBLIC_KEY
