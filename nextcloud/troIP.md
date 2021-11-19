# Trỏ IP

Đầu tiên, trỏ bản ghi:

nextcloud.chodatserver.vn   @   14.225.16.182

Khi truy cập http://nextcloud.chodatserver.vn/nextcloud sẽ thấy kết quả.

![Imgur](https://i.imgur.com/mXZaJBi.png)

Chúng ta cần cấu hình trusted domain để sử dụng.

Sửa file `/var/www/html/nextcloud/config/config.php`

    vi /var/www/html/nextcloud/config/config.php
```
  'trusted_domains' =>
  array (
    0 => '14.225.16.182',
  ),

```
thành 

```
  'trusted_domains' =>
  array (
    0 => '14.225.16.182',
    1 => 'nextcloud.chodatserver.vn',
  ),
```

Truy cập lại

![Imgur](https://i.imgur.com/ryKm82O.png)