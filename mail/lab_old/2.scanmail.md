# Lọc mail với Amavisd và quét virus cho thư với Clamav

https://www.server-world.info/en/note?os=CentOS_7&p=mail&f=6

## Cài  Clamav

    yum --enablerepo=epel -y install clamav clamav-update 

    sed -i -e "s/^Example/#Example/" /etc/freshclam.conf 

    freshclam

## Cài Amavisd

    yum --enablerepo=epel -y install amavisd-new clamav-scanner clamav-scanner-systemd 

    setsebool -P antivirus_can_scan_system on 

Sửa file `/etc/amavisd/amavisd.conf` 

    vi /etc/amavisd/amavisd.conf 

Dòng 20 sửa 

    $mydomain = 'doanbadung.xyz'; 

Dòng 152 sửa

    $myhostname = 'mail.doanbadung.xyz'; 

DÒng 154: uncomment

$notify_method = 'smtp:[127.0.0.1]:10025';
$forward_method = 'smtp:[127.0.0.1]:10025'; 

Dòng 383, đảm bảo nội dung giống như sau

    ['ClamAV-clamd',
        \&ask_daemon, ["CONTSCAN {}\n", "/var/run/clamd.amavisd/clamd.sock"],
        qr/\bOK$/m, qr/\bFOUND$/m,
        qr/^.*?: (?!Infected Archive)(.*) FOUND$/m ],

Khởi động dịch vụ

    [root@mail ~]# systemctl start clamd@amavisd amavisd spamassassin

    [root@mail ~]# systemctl enable clamd@amavisd amavisd spamassassin 

Cấu hình Postfix: Sửa file `/etc/postfix/main.cf` 

    [root@mail ~]# vi /etc/postfix/main.cf 

Thêm vào cuối file 

    content_filter=smtp-amavis:[127.0.0.1]:10024 

Sửa file `/etc/postfix/master.cf` 

    [root@mail ~]# vi /etc/postfix/master.cf 

Thêm vào cuối file 

```
smtp-amavis unix -    -    n    -    2 smtp
    -o smtp_data_done_timeout=1200
    -o smtp_send_xforward_command=yes
    -o disable_dns_lookups=yes
127.0.0.1:10025 inet n    -    n    -    - smtpd
    -o content_filter=
    -o local_recipient_maps=
    -o relay_recipient_maps=
    -o smtpd_restriction_classes=
    -o smtpd_client_restrictions=
    -o smtpd_helo_restrictions=
    -o smtpd_sender_restrictions=
    -o smtpd_recipient_restrictions=permit_mynetworks,reject
    -o mynetworks=127.0.0.0/8
    -o strict_rfc821_envelopes=yes
    -o smtpd_error_sleep_time=0
    -o smtpd_soft_error_limit=1001
    -o smtpd_hard_error_limit=1000
```

Khời động lại dịch vụ

    [root@mail ~]# systemctl restart postfix 

## Kiểm tra:

Gửi 1 mail đến cent.doanbadung.xyz để kiểm tra

Trước khi cài đặt thì view source thấy như sau:

![Imgur](https://i.imgur.com/KSApBFO.png)

Sau khi cài đặt thì view source thấy nhhuw sau:

![Imgur](https://i.imgur.com/uwoV8wN.png)