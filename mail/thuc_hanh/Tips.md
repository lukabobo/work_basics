# Một vài tips

## Tự động khởi động lại dịch vụ postfix và dovecot

Nếu vì một lý do nào đó mà dịch vụ postfix, dovecot bị tắt. Cần khởi động lại dịch vụ postfix để tránh ảnh hưởng tới dịch vụ gửi nhận mail.

Tạo thư mục, file cấu hình khởi động lại postfix

    mkdir -p /etc/systemd/system/postfix.service.d/

    nano /etc/systemd/system/postfix.service.d/restart.conf

Thêm vào nội dung

    [Service]
    Restart=on-failure
    RestartSec=5s

Lưu và thoát file. Khởi động lại systemd

    systemctl daemon-reload

Làm tương tự với dịch vụ dovecot

    mkdir -p /etc/systemd/system/dovecot.service.d/

    nano /etc/systemd/system/dovecot.service.d/restart.conf

Nội dung file

    [Service]
    Restart=always
    RestartSec=5s

Lưu và thoát file. Khởi động lại systemd

    systemctl daemon-reload

Kiểm tra:

    pkill dovecot 
    pkill master

Kiểm tra trạng thái dịch vụ với lệnh

    systemctl status dovecot postfix 

5s sau đó nhập lại lênh trên sẽ thấy dịch vụ đã tự khởi động lại

## Tự động gia hạn chứng chỉ SSL Let's Enscrypt

Sửa crontab

    crontab -e

Nếu sử dụng apache thì thêm dòng này vào cuối trang

    @daily certbot renew --quiet && systemctl reload postfix dovecot httpd

Nếu sử dung nginx

    @daily certbot renew --quiet && systemctl reload postfix dovecot nginx

Lưu lại và khởi động lại dịch vụ crontab

    systemctl restart crond

## Tham khảo

https://www.linuxbabe.com/redhat/install-dovecot-centos-enable-tls-encryption

https://www.linuxbabe.com/redhat/run-your-own-email-server-centos-postfix-smtp-server

## Đọc thêm

https://www.linuxbabe.com/redhat/blocking-email-spam-postfix-centos