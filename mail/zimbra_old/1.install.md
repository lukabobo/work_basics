# Cài đặt mail zimbra

## Chuẩn bị 

Chuẩn bị 1 server / VPS có cấu hình:

- 8GB RAM. 
- Intel/AMD 2.0 GHZ+ 64-bit CPU
- 10GB Disk
- Hệ điều hành Centos 7 
- Có 1 IP public, thể truy cập internet và không cài thêm bất cứ phần mềm gì trên đó.
- Có 1 tên miền

Tôi có 1 IP public, tên miền của tôi trong bài viết này là doanbadung.xyz

Đầu tiên cần trỏ các bản ghi sau:

![Imgur](https://i.imgur.com/sUUyjfc.png)

Phần bị che đi chính là địa chỉ IP public

Trước khi cài kiểm tra có dịch vụ gì đang chạy không bằng lệnh

    netstat -tunlp

Stop các dịch vụ httpd, postfix, sendmail, iptable

    service sendmail stop
    service iptables stop
    service ip6tables stop
    chkconfig sendmail off
    chkconfig iptables off
    chkconfig ip6tables off
    service httpd stop
    chkconfig httpd off
    systemctl stop postfix
    systemctl disable postfix

Update 

    yum install epel-release -y
    yum update -y

Thiết lập Firewall và một số package cơ bản, tắt selinux và khởi đội lại:

    sudo systemctl disable firewalld
    sudo systemctl stop firewalld
    sed -i 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/sysconfig/selinux
    sed -i 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config
    yum install -y git wget byobu
    reboot

Cài các gói cần thiết:

    byou
    yum install unzip net-tools sysstat openssh-clients perl-core libaio nmap-ncat libstdc++.so.6 nano wget -y 

Tải Zimbra version 8.8.12 tại: https://zimbra.org/download/zimbra-collaboration/8.8.12

    wget https://files.zimbra.com/downloads/8.8.12_GA/zcs-8.8.12_GA_3794.RHEL7_64.20190329045002.tgz

    tar -xvf zcs-8.8.12_GA_3794.RHEL7_64.20190329045002.tgz
    cd zcs-8.8.12_GA_3794.RHEL7_64.20190329045002
    ./install.sh

![Imgur](https://i.imgur.com/gAaunEw.png)

Option này chọn `N`

`Install zimbra-dnscache [Y] N`

Còn lại gõ Enter ở tất cả các option khác

Xác nhận thay đổi tên domain và nhập domain

    It is suggested that the domain name have an MX record configured in DNS
    Change domain name? [Yes] Yes
    Create domain: [mail.doanbadung.xyz] doanbadung.xyz

Chọn 6 và tiếp tục chọ 4 để đặt mật khẩu quản trị

![Imgur](https://i.imgur.com/AxNA9H5.png)


![Imgur](https://i.imgur.com/1tvoO12.png)

Chọn r để quay lại và chọ a để áp dụng các thay đổi.

![Imgur](https://i.imgur.com/2KuJpSC.png)


![Imgur](https://i.imgur.com/Vyd48pI.png)

Chờ quá trình lưu cấu hình hoàn tất. Nhấn enter để kết thúc.

    Notify Zimbra of your installation? [Yes] Yes
    Configuration complete - press return to exit

Sau khi cài đặt xong, khởi động dịch vụ:

    su zimbra
    zmcontrol start 

Đảm bảo đầy đủ các bản ghi MX, PTR, SPF, DKIM, DMARC

- Thêm bản ghi A: Tên mail loại bản A giá trị là IP

- Thêm bản ghi MX: Tên @ loại bản ghi MX giá trị mail.doanbadung.xyz

- Thêm bản ghi _dmarc: _dmarc loại bản ghi txt giá trị v=DMARC1; p=none; rua=mailto:mailauth-reports@mail.doanbadung.xyz

- Thêm bản ghi SPF: @ loại bản ghi txt, giá trị v=spf1 +a +mx +ip4:<IP> ~all

- Thêm bản ghi PTR ở DNS server của hệ thống DNS

- Thêm bản ghi DKIM các thông số lấy từ email server

Active dkim cho domain doanbadung.xyz

    su - zimbra
    /opt/zimbra/libexec/zmdkimkeyutil -a -d doanbadung.xyz

![Imgur](https://i.imgur.com/oX40ZFM.png)

Bỏ các dấu ngoặc kép, nối 2 đoạn trên thành 1. Trỏ bản ghi dkim như sau:

![Imgur](https://i.imgur.com/piP4vVb.png)

## Kiểm tra

Truy cập https://mail.doanbadung.xyz:7071 để quản lý

Các user thường truy cập https://mail.doanbadung.xyz để sử dụng

![Imgur](https://i.imgur.com/W1sT1M5.png)

![Imgur](https://i.imgur.com/XUyGlTG.png)

Kiểm tra hoạt động các dịch vụ:

![Imgur](https://i.imgur.com/SOKYmY9.png)

Kiểm tra gửi và nhận mail

Gửi mail:

![Imgur](https://i.imgur.com/v6kwycq.png)

![Imgur](https://i.imgur.com/syrrxKf.png)

Nhận mail:


![Imgur](https://i.imgur.com/Oughtxk.png)



![Imgur](https://i.imgur.com/MmUGQ1h.png)

Nếu đã trỏ bản ghi rồi thì 

![Imgur](https://i.imgur.com/NuAYCde.png)

Nếu chưa trỏ bản ghi thì 

![Imgur](https://i.imgur.com/rIGExWq.png)

Nếu có dịch vụ nào đang stop thì dùng cấu trúc

    za(tên-service)ctl start

Ví dụ

    zmamtctl start

## Tips

Xử lý tài khoản spam

Vào phần `Quản lý tài khoản`. Chọn `Đã đóng`

Bắt tài khoản spam mail

Vào **Giám sát** - hàng thư đợi - thư đã gửi

Xem dung lượng

Vào Giám sát

Xem dung lượng

    

Nâng dung lượng file đính kèm


    postconf -e 'message_size_limit = 104857600'
    zmprov mcf zimbraMtaMaxMessageSize 50000000
    zmprov mcf zimbraFileUploadMaxSize 50000000
    zmprov mcf zimbraMailContentMaxSize 104857600

    postfix reload
    zmcontrol restart

Kiểm tra thư không nhận được. Vào phần forward xem có tick phần xóa thư local không. Nếu có thì bật lên

Cấu hình thiết lập chung ASAV. Bỏ tick chặn các tài liệu. lưu và restart dịch vụ antivirus 


## LOG

/var/log/zimbra.log

Gửi thành công:

    status 250 2.0.0 
    removed

Nếu khác tức là chưa gửi được


