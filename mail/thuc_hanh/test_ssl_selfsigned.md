# Note lại các bước cài đặt SSL selfsigned

Cài đặt nginx

    yum install epel-release -y
    yum -y update
    yum install nginx -y

Bật dịch vụ

    systemctl start nginx
    systemctl enable nginx
    systemctl status nginx

Add rule firewall

    sudo firewall-cmd --add-service=http
    sudo firewall-cmd --add-service=https
    sudo firewall-cmd --runtime-to-permanent

    sudo iptables -I INPUT -p tcp -m tcp --dport 80 -j ACCEPT
    sudo iptables -I INPUT -p tcp -m tcp --dport 443 -j ACCEPT

Tạo chứng chỉ SSL

    mkdir /etc/ssl/private
    chmod 700 /etc/ssl/private

    openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/private/nginx-selfsigned.key -out /etc/ssl/certs/nginx-selfsigned.crt

Nhập các thông tin cần thiết

    Country Name (2 letter code) [VN]:vn
    State or Province Name (full name) [Some-State]:Ha Noi
    Locality Name (eg, city) []:Ha Noi
    Organization Name (eg, company) [Internet Widgits Pty Ltd]:Viettelco
    Organizational Unit Name (eg, section) []:Support Department
    Common Name (e.g. server FQDN or YOUR name) []:server_IP_address
    Email Address []:admin@your_domain.com

Các file được tạo trong thư mục `/etc/ssl`

Tạo Diffie-Hellman group

    openssl dhparam -out /etc/ssl/certs/dhparam.pem 2048

Cấu hình nginx

    vi /etc/nginx/conf.d/ssl.conf

Nhập nội dung

```
server {
    listen 443 http2 ssl;
    listen [::]:443 http2 ssl;

    server_name server_IP_address;

    ssl_certificate /etc/ssl/certs/nginx-selfsigned.crt;
    ssl_certificate_key /etc/ssl/private/nginx-selfsigned.key;
    ssl_dhparam /etc/ssl/certs/dhparam.pem;
}
```

Thêm option SSL. Tham khảo các trang như https://raymii.org/s/static/About.html hoặc https://cipherli.st/

Sửa file `/etc/nginx/conf.d/ssl.conf`

```
server {
    listen 443 http2 ssl;
    listen [::]:443 http2 ssl;

    server_name server_IP_address;

    ssl_certificate /etc/ssl/certs/nginx-selfsigned.crt;
    ssl_certificate_key /etc/ssl/private/nginx-selfsigned.key;
    ssl_dhparam /etc/ssl/certs/dhparam.pem;

    ########################################################################
    # from https://cipherli.st/                                            #
    # and https://raymii.org/s/tutorials/Strong_SSL_Security_On_nginx.html #
    ########################################################################

    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
    ssl_prefer_server_ciphers on;
    ssl_ciphers "EECDH+AESGCM:EDH+AESGCM:AES256+EECDH:AES256+EDH";
    ssl_ecdh_curve secp384r1;
    ssl_session_cache shared:SSL:10m;
    ssl_session_tickets off;
    ssl_stapling on;
    ssl_stapling_verify on;
    resolver 8.8.8.8 8.8.4.4 valid=300s;
    resolver_timeout 5s;
    # Disable preloading HSTS for now.  You can use the commented out header line that includes
    # the "preload" directive if you understand the implications.
    #add_header Strict-Transport-Security "max-age=63072000; includeSubdomains; preload";
    add_header Strict-Transport-Security "max-age=63072000; includeSubdomains";
    add_header X-Frame-Options DENY;
    add_header X-Content-Type-Options nosniff;

    ##################################
    # END https://cipherli.st/ BLOCK #
    ##################################

    root /usr/share/nginx/html;

    location / {
    }

    error_page 404 /404.html;
    location = /404.html {
    }

    error_page 500 502 503 504 /50x.html;
    location = /50x.html {
    }
}
```

Redirect http sang https

    vi /etc/nginx/default.d/ssl-redirect.conf

Thêm dòng

    return 301 https://$host$request_uri/;

Kiểm tra 

    nginx -t

Kết quả trả lại OK là thành công

Khởi động lại dịch vụ

    systemctl restart nginx

Truy cập và kiểm tra chứng chỉ

![Imgur](https://i.imgur.com/Uqs5BM4.png)

