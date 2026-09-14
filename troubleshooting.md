# Troubleshooting Notes

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
