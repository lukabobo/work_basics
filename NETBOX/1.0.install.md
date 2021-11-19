# Note lại cách cài đặt Netbox

Làm theo hướng dẫn: 

https://github.com/domanhduy/ghichep/blob/master/DuyDM/Netbox/Thuc-hanh/docs/1.Cai-dat-netbox-tren-CentOS7.md

## Cài SSL:

    yum -y install certbot
    service nginx stop
    certbot certonly --standalone

Ở đây tôi cài cho subdomain netbox.suncloud.vn nên sẽ khai netbox.suncloud.vn

Sau khi chạy xong lệnh trên. Vào file `/etc/nginx/conf.d/netbox.conf`, sửa thành:

```
server {
    listen 443 ssl;

    # CHANGE THIS TO YOUR SERVER'S NAME
    server_name netbox.suncloud.vn;

        # SSL
        ssl_certificate /etc/letsencrypt/live/netbox.suncloud.vn/fullchain.pem;
        ssl_certificate_key /etc/letsencrypt/live/netbox.suncloud.vn/privkey.pem;
        ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
        ssl_prefer_server_ciphers on;
        ssl_ciphers EECDH+CHACHA20:EECDH+AES128:RSA+AES128:EECDH+AES256:RSA+AES256:EECDH+3DES:RSA+3DES:!MD5;

        # Improve HTTPS performance with session resumption
        ssl_session_cache shared:SSL:50m;
        ssl_session_timeout 1d;

        # Enable HSTS
        add_header Strict-Transport-Security "max-age=31536000" always;

    client_max_body_size 25m;

    location /static/ {
        alias /opt/netbox/netbox/static/;
    }

    location / {
        proxy_pass http://127.0.0.1:8001;
        proxy_set_header X-Forwarded-Host $http_host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}

server {
    # Redirect HTTP traffic to HTTPS
    listen 80;
    server_name netbox.suncloud.vn;
    return 301 https://$host$request_uri;
    rewrite ^(.*) https://netbox.suncloud.vn$1 permanent;
}
```


## Cấu hình basic authentication

    yum -y install httpd-tools
	
    htpasswd -c /etc/nginx/.htpasswd admin

Đặt password cho user admin

Sau đó vào file `/etc/nginx/conf.d/netbox.conf`, sửa phần

```
    location /static/ {
        alias /opt/netbox/netbox/static/;
        # Basic authentication
        auth_basic "Restricted area - This system is for the use of authorized users only!";
        auth_basic_user_file /opt/netbox/netbox/static/.htpasswd;
    }
```


Nội dung đầy đủ file `/etc/nginx/conf.d/netbox.conf`

```
server {
    listen 443 ssl;

    # CHANGE THIS TO YOUR SERVER'S NAME
    server_name netbox.suncloud.vn;

        # SSL
        ssl_certificate /etc/letsencrypt/live/netbox.suncloud.vn/fullchain.pem;
        ssl_certificate_key /etc/letsencrypt/live/netbox.suncloud.vn/privkey.pem;
        ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
        ssl_prefer_server_ciphers on;
        ssl_ciphers EECDH+CHACHA20:EECDH+AES128:RSA+AES128:EECDH+AES256:RSA+AES256:EECDH+3DES:RSA+3DES:!MD5;

        # Improve HTTPS performance with session resumption
        ssl_session_cache shared:SSL:50m;
        ssl_session_timeout 1d;

        # Enable HSTS
        add_header Strict-Transport-Security "max-age=31536000" always;

    client_max_body_size 25m;

    location /static/ {
        alias /opt/netbox/netbox/static/;
        # Basic authentication
        auth_basic "Restricted area - This system is for the use of authorized users only!";
        auth_basic_user_file /opt/netbox/netbox/static/.htpasswd;
    }

    location / {
        proxy_pass http://127.0.0.1:8001;
        proxy_set_header X-Forwarded-Host $http_host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}

server {
    # Redirect HTTP traffic to HTTPS
    listen 80;
    server_name netbox.suncloud.vn;
    return 301 https://$host$request_uri;
    rewrite ^(.*) https://netbox.suncloud.vn$1 permanent;
}

```

Kiểm tra lại và restart nginx

    nginx -t
    systemctl restart nginx