# Cài đặt smokeping trên Centos 7

Thực hiện theo hướng dẫn:

https://gist.github.com/MinhKMA/d9eaf4e5835bdb000bad5946955133b3

Khai báo repos và các gói cần thiết :

    yum -y update 
    yum groupinstall "Development tools" -y
    yum -y install epel-release wget curl httpd postfix

Cài đặt:

    yum -y install smokeping

Tải file cấu hình của smokeping và thay vào file cấu hình của webserver:

    mv /etc/httpd/conf.d/smokeping.conf /etc/httpd/conf.d/smokeping.conf.bak

    vi /etc/httpd/conf.d/smokeping.conf

Sửa thành như sau:

```
<Directory "/usr/share/smokeping" >
  Options +ExecCGI
  Require all granted
</Directory>

<Directory "/var/lib/smokeping" >
  Require all granted
</Directory>

ScriptAlias /smokeping/sm.cgi /usr/share/smokeping/cgi/smokeping.fcgi
ScriptAlias /smokeping/smokeping.cgi /usr/share/smokeping/cgi/smokeping.fcgi

Alias /smokeping/images /var/lib/smokeping/images
Alias /smokeping /usr/share/smokeping/htdocs

RedirectMatch permanent "^/smokeping/?$" "/"
RedirectMatch permanent "^/smokeping/smokeping.cgi" "/"
RedirectMatch permanent "^/smokeping/sm.cgi" "/"
```

Sửa file cấu hình /`etc/httpd/conf/httpd.conf`:

Tìm dòng `DirectoryIndex index.html` (dòng 164) trong file /`etc/httpd/conf/httpd.conf` và thay bằng dòng :

    DirectoryIndex index.html smokeping.fcgi

Tìm dòng `DocumentRoot "/var/www/html"` (dòng 119) trong file /`etc/httpd/conf/httpd.conf` và thay bằng dòng:

    DocumentRoot "/usr/share/smokeping/cgi"

Thêm host:

Mở file cấu hình:

    vi /etc/smokeping/config

Xuống cuối file, thêm 1 thư mục mới :

```
 + Site1-Viettel

 menu = Site1-Viettel
 title = Site1-Viettel

 ++ Host1-175

 menu = Host1
 title = 10.10.34.175
 host = 10.10.34.175

 ++ Host2-176
 menu = Host2
 title = 10.10.34.176
 host = 10.10.34.176

 + Site2-FPT

 menu = Site2-FPT
 title = Site2-FPT

 ++ Host1-173

 menu = Host1
 title = 10.10.34.173
 host = 10.10.34.173

 ++ Host2-174
 menu = Host2
 title = 10.10.34.174
 host = 10.10.34.174

+ GW
menu = GW
title = GW

++ CloudflareDNS

menu = Cloudflare DNS
title = Cloudflare DNS server
host = 1.1.1.1

++ GoogleDNS

menu = Google DNS
title = Google DNS server
host = 8.8.8.8

++ MultiHost

menu = Multihost example
title = CloudflareDNS and Google DNS
host = /GW/CloudflareDNS /GW/GoogleDNS
```


Cấu hình firewalld:

    systemctl start firewalld
    systemctl enable firewalld
    sudo firewall-cmd --permanent --zone=public --add-service=http 
    sudo firewall-cmd --permanent --zone=public --add-service=https
    sudo firewall-cmd --reload

Khởi đông lại dịch vụ và kiểm tra kết quả trên web :

    systemctl restart httpd
    systemctl restart smokeping
    systemctl enable httpd
    systemctl enable smokeping

Kết quả:

![Imgur](https://i.imgur.com/zebwRQF.png)

![Imgur](https://i.imgur.com/rfpKi2c.png)

![Imgur](https://i.imgur.com/MKAQ9z2.png)