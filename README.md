# AWS EC2 Nginx Web Server Lab

## 專案簡介

此專案使用 AWS EC2 建立 Ubuntu Linux Server，
並安裝 Nginx Web Server 對外提供 HTTP 網頁服務。

透過此專案練習 Linux Server 管理、AWS EC2、
Security Group、SSH、Nginx 與基本 Troubleshooting。

## 使用技術

- AWS EC2
- Ubuntu Linux
- Nginx
- SSH
- Security Group
- HTTP / TCP Port 80
- Linux File Permissions
- Git / GitHub

## 專案架構

Client
↓
Internet
↓
EC2 Public IPv4
↓
Security Group
↓
AWS EC2 Ubuntu
↓
Nginx
↓
HTML Web Page

## 實作內容

- 建立 AWS EC2 Ubuntu Instance
- 使用 SSH Key 登入 EC2
- 設定 Security Group
- 開放 SSH TCP 22
- 開放 HTTP TCP 80
- 安裝並啟動 Nginx
- 建立自訂 HTML 首頁
- 使用 systemctl 查看服務狀態
- 使用 ss 檢查 Port 80
- 使用 curl 測試 HTTP
- 查看 Nginx access log / error log
- 測試 HTTP 404 / 403
- 使用 chmod 處理 Linux 檔案權限

## Troubleshooting

此專案中實際遇到並排查：

- SSH `Permission denied (publickey)`
- EC2 Stop / Start 後 Public IPv4 改變
- Nginx 網頁路徑輸入錯誤
- HTTP 404 Not Found
- HTTP 403 Forbidden
- Linux Permission denied
