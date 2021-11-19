# Cấu hình mail server Centos7

https://www.server-world.info/en/note?os=CentOS_7&p=mail&f=1

## Cài đặt postfix để gửi mail

 	
Install Postfix to configure SMTP server.

[1]	Postfix is installed even if CentOS system was installed with [Minimal Install], but if Postfix is not, Install it first like follows.

    [root@mail ~]# yum -y install postfix

[2]	This example shows to configure SMTP-Auth to use Dovecot's SASL function.

    [root@mail ~]# vi /etc/postfix/main.cf
    # line 75: uncomment and specify hostname
    myhostname = mail.srv.world
    # line 83: uncomment and specify domain name
    mydomain = srv.world
    # line 99: uncomment
    myorigin = $mydomain
    # line 116: change
    inet_interfaces = all
    # line 164: add
    mydestination = $myhostname, localhost.$mydomain, localhost, $mydomain
    # line 264: uncomment and specify your local network
    mynetworks = 127.0.0.0/8, 10.0.0.0/24
    # line 419: uncomment (use Maildir)
    home_mailbox = Maildir/
    # line 574: add
    smtpd_banner = $myhostname ESMTP
    # add follows to the end
    # limit an email size for 10M
    message_size_limit = 10485760
    # limit a mailbox for 1G
    mailbox_size_limit = 1073741824
    # for SMTP-Auth
    smtpd_sasl_type = dovecot
    smtpd_sasl_path = private/auth
    smtpd_sasl_auth_enable = yes
    smtpd_sasl_security_options = noanonymous
    smtpd_sasl_local_domain = $myhostname
    smtpd_recipient_restrictions = permit_mynetworks, permit_auth_destination, permit_sasl_authenticated, reject
    [root@mail ~]# systemctl restart postfix
    [root@mail ~]# systemctl enable postfix


[3]	If Firewalld is running, allow SMTP service. SMTP uses 25/TCP.


    [root@dlp ~]# firewall-cmd --add-service=smtp --permanent
    success
    [root@dlp ~]# firewall-cmd --reload
    success

## Cấu hình dovecot để nhận mail

 	
Install Dovecot to configure POP/IMAP server

[1]	Install Dovecot.

    [root@mail ~]# yum -y install dovecot

[2]	This example shows to configure to provide SASL function to Postfix.

    [root@mail ~]# vi /etc/dovecot/dovecot.conf
    # line 24: uncomment
    protocols = imap pop3 lmtp
    # line 30: uncomment and change ( if not use IPv6 )
    listen = *
    [root@mail ~]# vi /etc/dovecot/conf.d/10-auth.conf
    # line 10: uncomment and change ( allow plain text auth )
    disable_plaintext_auth = no
    # line 100: add
    auth_mechanisms = plain login
    [root@mail ~]# vi /etc/dovecot/conf.d/10-mail.conf
    # line 30: uncomment and add
    mail_location = maildir:~/Maildir
    [root@mail ~]# vi /etc/dovecot/conf.d/10-master.conf
    # line 96-98: uncomment and add like follows
    # Postfix smtp-auth
    unix_listener /var/spool/postfix/private/auth {
        mode = 0666
        user = postfix
        group = postfix
    }
    [root@mail ~]# vi /etc/dovecot/conf.d/10-ssl.conf
    # line 8: change (not require SSL)
    ssl = no
    [root@mail ~]# systemctl start dovecot
    [root@mail ~]# systemctl enable dovecot

[3]	If Firewalld is running, allow POP/IMAP services. POP uses 110/TCP, IMAP uses 143/TCP.


    [root@dlp ~]# firewall-cmd --add-service={pop3,imap} --permanent
    success
    [root@dlp ~]# firewall-cmd --reload
    success

## Thử gửi nhận mail

Tôi có 1 tên miền là doanbadung.xyz. Có 1 VPS có IP public. Tôi dùng VPS này làm mail server, trỏ bản ghi mail.doanbadung.xyz về VPS này.

### Gửi mail

Thử gửi mail đến dungz1207@gmail.com

    mail dungz1207@gmail.com

![Imgur](https://i.imgur.com/gVyyebb.png)

Mail nhận được

![Imgur](https://i.imgur.com/cDUTzzt.png)

### Nhận mail

Trả lời user cent@doanbadung.xyz

![Imgur](https://i.imgur.com/8w1Mter.png)

Kiểm tra Maildir

    cd /home/cent/Maildir/new
    ls
    cat 1601538688.Vfd01I60019M965890.mail.doanbadung.xyz

![Imgur](https://i.imgur.com/7tID7SZ.png)

## Đăng nhập vào thunderbird với user cent@doanbadung.xyz và kiểm tra

![Imgur](https://i.imgur.com/lVSpKXB.png)

![Imgur](https://i.imgur.com/71yVsep.png)

## 