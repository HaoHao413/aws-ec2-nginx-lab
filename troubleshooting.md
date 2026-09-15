# 問題排解紀錄 (Troubleshooting)

## 1. SSH Permission denied (publickey)

### 問題

使用 SSH 連線 EC2 時出現：

```text
Permission denied (publickey)
```

### 原因

SSH 登入使用者名稱輸入錯誤。

Ubuntu EC2 預設使用者應為：

```text
ubuntu
```

當時輸入成錯誤的使用者名稱，因此無法使用原本的 SSH Key 登入該 Linux 帳號。

### 解法

使用正確的 Ubuntu 使用者名稱重新連線：

```bash
ssh -i ~/.ssh/cloud-lab-key.pem ubuntu@<EC2_PUBLIC_IP>
```

### 學到的重點

`Permission denied (publickey)` 不一定代表 Public IP 或 Security Group 有問題。

如果已經成功連到 SSH Server，但登入被拒絕，可以檢查：

- SSH 使用者名稱是否正確
- Private Key 是否正確
- 該 Linux 使用者是否有對應的 Public Key 授權

## 2. EC2 Public IPv4 改變

### 問題

EC2 Stop 後重新 Start，原本使用的 Public IPv4 無法連線。

### 原因

使用 AWS 自動分配 Public IPv4 的 EC2，在 Stop / Start 後，Public IPv4 可能會改變。

EC2 Instance 本身仍然是同一台，但對外使用的 Public IPv4 不一定固定。

### 解法

重新到 AWS EC2 Console 查看目前的 Public IPv4，並使用新的 IP 重新連線。

例如：

```bash
ssh -i ~/.ssh/cloud-lab-key.pem ubuntu@<NEW_EC2_PUBLIC_IP>
```

若是開啟網站，則使用：

```text
http://<NEW_EC2_PUBLIC_IP>
```

### 學到的重點

EC2 Instance 與 Public IPv4 是不同的概念。

Stop / Start 後，如果沒有使用固定 IP，重新操作前應先確認目前的 Public IPv4。

## 3. Nginx 首頁沒有更新

### 問題

修改網頁內容後，瀏覽器仍然顯示原本的 Nginx 頁面。

### 原因

檔名輸入錯誤，導致內容被寫入另一個新建立的檔案，而不是 Nginx 實際使用的首頁檔案。

例如原本應修改：

```text
index.nginx-debian.html
```

但實際輸入成：

```text
index.nginx-debin.html
```

Linux 會把不同檔名視為完全不同的檔案，因此新的錯誤檔案被建立。

### 排查方式

先查看網站目錄裡有哪些檔案：

```bash
ls -l /var/www/html
```

再確認真正首頁檔案的內容：

```bash
cat /var/www/html/index.nginx-debian.html
```

### 解法

確認正確檔名後，再修改真正的首頁檔案。

### 學到的重點

Linux 的檔名與路徑必須完全正確。

只差一個字母，就會被視為不同的檔案。

## 4. HTTP 404 Not Found

### 問題

使用 curl 存取不存在的路徑：

```bash
curl http://127.0.0.1/abc123
```

Nginx 回傳：

```text
404 Not Found
```

### 原因

Nginx 本身正常運作，但要求的 `/abc123` 資源不存在。

404 不代表 Web Server 掛掉，而是代表 Server 有收到 Request，只是找不到指定資源。

### 排查方式

查看 Nginx access log：

```bash
sudo tail -n 10 /var/log/nginx/access.log
```

可以看到類似：

```text
"GET /abc123 HTTP/1.1" 404
```

### 學到的重點

HTTP 404 代表：

- Web Server 有收到 Request
- Nginx 有正常回應
- 但指定的資源不存在

排查時不能把 404 跟「Server 完全連不上」混在一起。

## 5. HTTP 403 Forbidden 與 Linux 檔案權限

### 問題

網站檔案存在，但 Nginx 回傳：

```text
403 Forbidden
```

### 原因

Linux 檔案權限設定錯誤，導致 Nginx 沒有權限讀取網站檔案。

例如將檔案權限設定為：

```bash
sudo chmod 000 /var/www/html/secret.html
```

此時檔案權限會變成：

```text
----------
```

代表 Owner、Group、Others 都沒有讀寫執行權限。

### 排查方式

先查看檔案權限：

```bash
ls -l /var/www/html/secret.html
```

再查看 Nginx error log：

```bash
sudo tail -n 10 /var/log/nginx/error.log
```

可以看到類似：

```text
Permission denied
```

### 解法

將檔案權限調整回可讀取，例如：

```bash
sudo chmod 644 /var/www/html/secret.html
```

### 學到的重點

HTTP 403 不代表檔案不存在。

它可能代表：

- 檔案存在
- Nginx 有收到 Request
- 但 Nginx 沒有權限讀取該檔案

因此排查 403 時，要檢查 Linux 檔案權限與 Nginx error log。

## 6. Basic Troubleshooting Flow

當網站無法正常開啟時，可以依照以下順序排查。

### 1. 確認 Nginx 服務是否正常

```bash
systemctl status nginx
```

如果顯示：

```text
active (running)
```

代表 Nginx 服務目前正在運作。

### 2. 確認 Port 80 是否有在監聽

```bash
ss -tuln | grep ':80'
```

如果看到：

```text
0.0.0.0:80
```

代表有服務正在監聽 HTTP Port 80。

### 3. 從 EC2 本機測試網站

```bash
curl http://127.0.0.1
```

如果本機可以正常取得網頁內容，代表 Nginx 與網站內容大致正常。

### 4. 檢查外部連線

如果本機測試成功，但外部瀏覽器無法連線，檢查：

- EC2 目前的 Public IPv4 是否正確
- Security Group 是否允許 TCP Port 80
- 是否使用 `http://` 連線

### 5. 查看 Nginx Logs

Access log：

```bash
sudo tail -n 20 /var/log/nginx/access.log
```

Error log：

```bash
sudo tail -n 20 /var/log/nginx/error.log
```

透過 log 可以確認 Request 是否有進入 Nginx，以及 Server 是否發生錯誤。

### 排查觀念

```text
Service
↓
Listening Port
↓
Local Test
↓
External Network
↓
Logs
```

先確認哪一層正常，再往下一層排查，避免一次修改太多設定。
