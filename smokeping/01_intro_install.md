# Cài đặt smokeping trên Centos 7

## Giới thiệu

- Smokeping là một phần mềm mã nguồn mở được viết bằng ngôn ngữ Perl
- Tác giả: Tobi Oetiker
- SmokePing là một công cụ đo độ trễ. SmokePing có thể đo lường, lưu trữ, hiển thị độ trễ và sự mất gói tin trên biểu đồ. 
- SmokePing sử dụng RRDtool để duy trì lưu trữ dữ liệu lâu dài và hiển thị lên biểu đồ một cách dễ nhìn, lấy thông tin chính xác về trạng thái của từng kết nối mạng.
- Có thể cấu hình cảnh báo qua email

## Chuẩn bị

1 server cài đặt hệ điều hành Centos 7

## Cài đặt

Khai báo repos và các gói cần thiết :

    yum -y update 
    yum groupinstall "Development tools" -y
    yum -y install epel-release wget curl httpd postfix

Cài đặt:

    yum -y install smokeping

## Cấu hình web server 

Backup file config:

    mv /etc/httpd/conf.d/smokeping.conf /etc/httpd/conf.d/smokeping.conf.bak

Sửa file `/etc/httpd/conf.d/smokeping.conf` thành nội dung như sau:

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

Sửa file cấu hình `/etc/httpd/conf/httpd.conf`:

Tìm dòng `DirectoryIndex index.html` (dòng 164) trong file /`etc/httpd/conf/httpd.conf` và thay bằng dòng :

    DirectoryIndex index.html smokeping.fcgi

Tìm dòng `DocumentRoot "/var/www/html"` (dòng 119) trong file /`etc/httpd/conf/httpd.conf` và thay bằng dòng:

    DocumentRoot "/usr/share/smokeping/cgi"

## Cấu hình smokeping

Chúng ta cấu hình ở file `/etc/smokeping/config`

    vi /etc/smokeping/config

Ví dụ tôi có 2 site cần theo dõi là Viettel và FPT. Ở mỗi site có 2 host. Ta thêm host để theo dõi ở mục `*** Targets ***` (ở cuối file cấu hình) như sau:
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
```

Chúng ta có thế thêm các host quốc tế để theo dõi:

```
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
```

Ngoài ra, cũng có thể đặt 2 hoặc nhiều host trên cùng 1 biểu đồ để so sánh với nhau. Ví dụ ở đây tôi muốn so sánh độ trễ giữa google và cloudflare khi ping đến từ host smokeping của tôi.

```
++ MultiHost

menu = Multihost example
title = CloudflareDNS and Google DNS
host = /GW/CloudflareDNS /GW/GoogleDNS
```

Sau khi cấu hình, chúng ta thực hiện tiếp các bước sau:

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

## Kết quả:

Truy cập vào IP 10.10.34.175 trên trình duyệt và kiểm tra kết quả.

![Imgur](https://i.imgur.com/zebwRQF.png)

![Imgur](https://i.imgur.com/rfpKi2c.png)

![Imgur](https://i.imgur.com/MKAQ9z2.png)