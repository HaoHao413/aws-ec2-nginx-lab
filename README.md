# AWS EC2 Nginx Web Server Lab

## 專案介紹

本專案使用 AWS EC2 建立 Ubuntu Linux 雲端主機，並部署 Nginx Web Server。

透過本專案練習 Linux 基礎操作、SSH 遠端連線、Security Group 網路規則、Nginx Web Server 部署，以及基本的伺服器問題排除流程。

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

```text
使用者 / 瀏覽器
       |
       v
    Internet
       |
       v
 EC2 Public IPv4
       |
       v
 Security Group
   HTTP TCP 80
   SSH  TCP 22
       |
       v
   AWS EC2
 Ubuntu Linux
       |
       v
 Nginx Web Server
       |
       v
/var/www/html/index.html
