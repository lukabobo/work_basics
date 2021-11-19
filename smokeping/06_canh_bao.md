# Cấu hình cảnh báo qua email

## 1. Cài đặt và cấu hình postfix trên Centos 7

Thực hiện theo hướng dẫn:

https://news.cloud365.vn/huong-dan-gui-mail-dung-postfix/

Remove Sendmail

Trước tiên cần kiểm tra xem sendmail đã được cài đặt chưa bằng câu lệnh

    rpm -qa | grep sendmail

Nếu có kết quả trả về chứng tỏ sendmail đã được cài đặt. Ta cần remove nó

    yum remove sendmail*

Cài đặt postfix và một số gói liên quan

    yum -y install postfix cyrus-sasl-plain mailx

Đặt postfix như MTA mặc định của hệ thống

    alternatives --set mta /usr/sbin/postfix

Nếu câu lệnh bị lỗi và trả về output /usr/sbin/postfix has not been configured as an alternative for mta thì thực hiện lệnh sau:

    alternatives --set mta /usr/sbin/sendmail.postfix

Restart và enable postfix

    systemctl restart postfix
    systemctl enable postfix

Configure Postfix
Mở file main.cf để chỉnh sửa

vi `/etc/postfix/main.cf`

Thêm vào cuối file những dòng sau

```
myhostname = hostname.example.com
relayhost = [smtp.gmail.com]:587
smtp_use_tls = yes
smtp_sasl_auth_enable = yes
smtp_sasl_password_maps = hash:/etc/postfix/sasl_passwd
smtp_tls_CAfile = /etc/ssl/certs/ca-bundle.crt
smtp_sasl_security_options = noanonymous
smtp_sasl_tls_security_options = noanonymous
```


Và tìm, sửa lại 2 dòng sau:

    inet_interfaces = all
    inet_protocols = all

Configure Postfix SASL Credentials

Tạo file `/etc/postfix/sasl_passwd` và thêm vào dòng sau

    [smtp.gmail.com]:587 username:password

Trong đó:

- username: là địa chỉ email dùng để gửi mail
- password: là password của email dùng để gửi mail

Phân quyền cho file vừa tạo
```
postmap /etc/postfix/sasl_passwd
chown root:postfix /etc/postfix/sasl_passwd*
chmod 640 /etc/postfix/sasl_passwd*
systemctl reload postfix
```

Cho phép ứng dụng truy cập gmail
Nếu bạn sử dụng gmail làm địa chỉ người gửi thì bạn phải cho phép ứng dụng truy cập gmail của bạn

Đăng nhập bằng gmail để thực hiện gửi mail đã khai báo bên trên trên trình duyệt và truy cập vào địa chỉ sau

https://myaccount.google.com/lesssecureapps

Cho phép ứng dụng kém an toàn: BẬT

![Imgur](https://i.imgur.com/8A6qVIK.png)

https://accounts.google.com/DisplayUnlockCaptcha

Click tiếp tục

![Imgur](https://i.imgur.com/ydgTO0U.png)

![Imgur](https://i.imgur.com/b2tmuFm.png)

Gửi mail test:

    echo "Đã gửi thành công" | mail -s "Mail kiểm tra" hocchudong2021@gmail.com

Kết quả test:

![Imgur](https://i.imgur.com/903fH6Z.png)


## 2. Cấu hình trong file `/etc/smokeping/config`

    vi /etc/smokeping/config

Sửa phần `*** Alerts ***` thành như sau:

Tôi gửi cảnh báo đến dungdb@gmail.com.

Email dùng để gửi cảnh báo là hocchudong2021@gmail.com@gmail.com

```
*** Alerts ***
to = dungdb@gmail.com
from = hocchudong2021@gmail.com@gmail.com

+someloss
type = loss
# in percent
pattern = >0%,*12*,>0%,*12*,>0%
comment = mất gói trong 3 lần ping liên tiếp

+hostdown_with_state
type        = loss
pattern     = >50%
edgetrigger = yes
comment     = host down

+lossdetect
type        = loss
pattern     = ==0%,==0%,==0%,==0%,==0%,>0%,>0%,>0%
comment     = mất gói đột ngột
```

Trên đây là nhiều loại cảnh báo, sử dụng loại cảnh báo nào cho host nào là tùy bạn.

Để thêm alert vào các host cần gửi cảnh báo, chúng ta chỉ cần thêm dòng `alerts = tên-loại-cảnh báo` vào dưới host cần giám sát. Để thêm nhiều loại cảnh báo vào 1 host, ta thêm dấu phẩy vào giữa các loại cảnh báo.

Ví dụ:

Trước khi sửa:

```
 + Site1

 menu = Site1
 title = Site1

 ++ Host1-128

 menu = Host1
 title = 10.10.10.128
 host = 10.10.10.128
```

Sau khi sửa:

```
 + Site1

 menu = Site1
 title = Site1

 ++ Host1-128
 menu = Host1
 title = 10.10.34.173
 host = 10.10.34.173
 alerts = someloss,hostdown_with_state,lossdetect
```

Sau khi sửa file cấu hình cần phải khởi động lại dịch vụ httpd và smokeping

    systemctl restart httpd
    systemctl restart smokeping

Thử tắt host 10.10.10.128 thử xem có nhận được cảnh báo không.

**Kết quả:**

![Imgur](https://i.imgur.com/mXmud5a.png)

Nhận được cảnh báo không ping được đến host 10.10.10.128 (lost 100%, host down)

Các bạn chỉnh sửa các loại cảnh báo để sử dụng tùy theo nhu cầu giám sát của mình.

Một vài mẫu cảnh báo để tham khảo.

```
+someloss
type = loss
in percent
pattern = >0%,*12*,>0%,*12*,>0%
comment = Mất gói 3 lần liên tiếp

+someloss2
type = loss
edgetrigger = yes
pattern = >0%,>5%,>5%,>5%,>5%,>5%,
comment = Mất gói ít liên tục trong 5 lần ping liên tiếp

+offlineatstartup
type = rtt
pattern = ==S,==U
comment = host đã offline trước khi giám sát

#+hostdown_with_state
#type        = loss
#pattern     = >50%,>50%,>50%
#edgetrigger = yes
#comment     = Mất hơn 50% gói tin 3 lần liên tục sẽ báo host down

+hostdown
type = loss
edgetrigger = yes
pattern = ==0%,==0%,==0%, ==U
comment = host down!

+lossdetect
type        = loss
pattern     = ==0%,==0%,==0%,==0%,==0%,>20%,>20%,>20%
comment     = 3 lần ping liên tục bị mất 20% gói tin 

+lossdetect_with_state
type        = loss
edgetrigger = yes
pattern     = ==0%,==0%,==0%,==0%,==0%,>0%,>0%,>0%
comment     = sudden packet loss

+rttdetect
type    = rtt
pattern = <100,<100,<100,<100,<100,>100,>100,>100
comment = độ trễ trong 3 lần ping liên tục cao hơn 100ms

+lost_5_from_20_with_state
type        = matcher
edgetrigger = yes
pattern     = CheckLoss(l => 5,x => 20)
comment     = Số lần mất gói trong 2 lần ping thử lớn hơn 5

+rtt_avg_increased
type        = matcher
pattern     = Avgratio(historic => 20, current => 2, comparator=>'>', percentage => 1500)
comment     = độ trễ trong 2 lần gần nhất tăng hơn 1500% so với 20 lần ping thử trước đó

+loss80
type = loss
priority = 2
edgetrigger = yes
pattern = ==0%,==0%,>80%,>80%,>80%
comment = mất hơn 80% gói tin đột ngột 

+recover80
type = loss
priority = 1
edgetrigger = yes
pattern = >80%,==0%,==0%
comment = Trở lại trạng thái bình thường sau khi mất gói hơn 80%
```

Tham khảo: 

https://blog.sleeplessbeastie.eu/2017/09/11/how-to-keep-track-of-network-latency/

https://www.m00nie.com/how-to-configure-smokeping-alerts/

hiroom2.com/2017/05/14/centos-7-send-mail-with-postfix/