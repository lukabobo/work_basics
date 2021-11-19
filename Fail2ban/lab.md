# Fail2ban để chống brute force attack

## Mô hình

Server cài Fail2ban:

- IP: 10.10.10.129
- OS: Centos 7
- Đã update, cài đặt openssh-server

Server cài SSH client
- IP: 10.10.10.128

## Theo dõi ssh login failed trên Server

Đứng trên server 10.10.10.129 kiểm tra:

    cat /var/log/secure | grep 'Failed password' | sort | uniq -c

Kết quả:

    [root@localhost ~]# cat /var/log/secure | grep 'Failed password' | sort | uniq -c
      1 Mar 28 19:45:39 localhost sshd[1468]: Failed password for root from 10.10.10.128 port 45634 ssh2
      1 Mar 28 19:45:50 localhost sshd[1468]: Failed password for root from 10.10.10.128 port 45634 ssh2

Server 10.10.10.128 có thể liên tục SSH vào 10.10.10.129 đến khi đăng nhập thành công. Cách này gọi là tấn công brute force.

Fail2ban là 1 chương trình được viết bằng python có nhiệm vụ chặn các ip kết nối đến server dựa vào các dấu hiệu nghi ngờ mà ip đó để lại trong file log của hệ thống hoặc dịch vụ. Ví dụ như có quá nhiều lần sử dụng mật khẩu không hợp lệ, truy cập không hợp lệ hoặc các dấu hiệu bất thường khác mà có thể nhận biết được.

## Cài đặt và cấu hình Fail2ban

Cài đặt:

    yum -y install epel-release
    yum -y install fail2ban fail2ban-systemd

Cấu hình:

Backup file cấu hình

    cp -pf /etc/fail2ban/jail.conf /etc/fail2ban/jail.local

Cấu hình fail2ban

    > /etc/fail2ban/jail.conf
    cat << EOF >> /etc/fail2ban/jail.conf
    [DEFAULT]
    ignoreip = 127.0.0.1
    bantime = 600
    findtime = 600
    maxretry = 3
    EOF

Trong đó

- ignoreip: không block những địa chỉ này
- bantime: khoảng thời gian block mặc định (giây)
- findtime: khoảng thời gian (giây) một IP phải login thành công
- maxretry: số lần login false tối đa

Cấu hình fail2ban cho ssh

    cat << EOF >> /etc/fail2ban/jail.local
    [sshd]
    enabled  = true
    filter   = sshd
    action   = iptables[name=SSH, port=ssh, protocol=tcp]
    logpath  = /var/log/secure
    maxretry = 5
    bantime = 3600
    EOF

Trong đó

- enabled: kích hoạt bảo vệ
- filter: giữ mặc định để sử dụng file cấu hình `/etc/fail2ban/filter.d/sshd.conf`
- action: fail2ban sẽ ban địa chỉ IP nếu match filter `/etc/fail2ban/action.d/iptables.conf`
- logpath: đường dẫn file log fail2ban
- maxretry: số lần login fail tối đa
- bantime: thời gian ban IP 3600 giây = 1 giờ

Start fail2ban

    systemctl enable fail2ban
    systemctl start fail2ban

## Kiểm tra

Đứng trên máy client thực hiện SSH sai mật khẩu 5 lần và kiểm tra trên máy đã cài fail2ban

    fail2ban-client status sshd

Kết quả:
```
[root@localhost ~]# fail2ban-client status sshd
Status for the jail: sshd
|- Filter
|  |- Currently failed: 1
|  |- Total failed:     6
|  `- Journal matches:  _SYSTEMD_UNIT=sshd.service + _COMM=sshd
`- Actions
   |- Currently banned: 1
   |- Total banned:     1
   `- Banned IP list:   10.10.10.128
```

IP 10.10.10.128 bây giờ sẽ bị cấm login SSH trong 1h

Để xóa IP đã bị cấm ra khỏi danh sách

    fail2ban-client set sshd unbanip 10.10.10.218

Kiểm tra lại:

```
[root@localhost ~]# fail2ban-client set sshd unbanip 10.10.10.128
0
[root@localhost ~]# fail2ban-client status sshd
Status for the jail: sshd
|- Filter
|  |- Currently failed: 1
|  |- Total failed:     6
|  `- Journal matches:  _SYSTEMD_UNIT=sshd.service + _COMM=sshd
`- Actions
   |- Currently banned: 0
   |- Total banned:     1
   `- Banned IP list:
[root@localhost ~]#
```

Bây giờ client lại có quyền có thể SSH vào server 10.10.10.129 

## Một số phương pháp chống brute force attack

Brute force attack là phương pháp tấn công dò mật khẩu đến khi đăng nhập được

Một vài cách đề phòng cách tấn công này:

- Đặt mật khẩu mạnh
- Đổi port SSH. Sửa file `/etc/ssh/sshd_config`:
```
Port 2345
```
- Cấu hình timeout cho 1 session SSH. Trong khoảng thời gian cho phép mà không đăng nhập thành công thì sẽ mất phiên đăng nhập. Sửa file `/etc/ssh/sshd_config`:
```
    ClientAliveInterval 360 #360 giây
    ClientAliveCountMax 0
```
- Không cho phép password trống. Sửa file `/etc/ssh/sshd_config`:
```
PermitEmptyPasswords no
```
- Giới hạn các user được phép SSH. Sửa file `/etc/ssh/sshd_config`:
```
AllowUsers user1 user2
```
- Không cho phép user root login. Sửa file `/etc/ssh/sshd_config`:
```
PermitRootLogin no
```

- Sử dụng mặc định Protocol2 cho SSH. Sửa file `/etc/ssh/sshd_config`:
```
Protocol 2
```
- Cho phép ssh từ một số IP nhất đinh, cấu hình iptables:
```
iptables -A INPUT -p tcp -s YourIP --dport 2345 -j ACCEPT

iptables -A INPUT -p tcp --dport 2345 -m state --state NEW -m recent --set --name ssh --rsource

iptables -A INPUT -p tcp --dport 2345 -m state --state NEW -m recent ! --rcheck --seconds 90 --hitcount 3 --name ssh --rsource -j ACCEPT
```
- Dùng SSH key pair: https://github.com/lukabobo/DoanBaDung_NhanHoa/blob/master/linux/SSH/2.%20lab.md

- Xác thực 2 lớp: https://www.linux.com/training-tutorials/securing-ssh-two-factor-authentication-using-google-authenticator/
- Sử dụng VPN

## Tham khảo

https://blog.cloud365.vn/linux/fail2ban-cai-dat-cau-hinh/