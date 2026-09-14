# Architecture

```text
User / Browser
      |
      v
   Internet
      |
      v
EC2 Public IPv4
      |
      v
Security Group
- TCP 22 SSH
- TCP 80 HTTP
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

```text
