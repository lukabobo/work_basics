# Lệnh MTR - Một công cụ chuẩn đoán mạng

`mtr` (my traceroute) là một công cụ chẩn đoán mạng mạnh mẽ cung cấp chức năng của cả hai lệnh `ping` và `traceroute`. Nó là một công cụ đơn giản và đa nền tảng in thông tin về toàn bộ tuyến đường mà các gói mạng thực hiện (giống `traceroute`). Tuy nhiên, lệnh `mtr` cho thấy nhiều thông tin hơn `traceroute`: nó xác định đường dẫn đến một máy từ xa trong khi in phần trăm phản hồi cũng như thời gian phản hồi của tất cả các bước nhảy mạng trong tuyến internet giữa hệ thống cục bộ và máy từ xa.

## Cách MTR hoạt động

Khi bạn chạy `mtr`, nó sẽ kết nối mạng giữa hệ thống cục bộ và máy chủ từ xa mà bạn đã chỉ định. Đầu tiên, nó thiết lập địa chỉ của từng hop mạng (cầu nối, bộ định tuyến và cổng, v.v.) giữa các máy chủ, sau đó ping (gửi một chuỗi yêu cầu ICMP ECHO tới) để xác định chất lượng của liên kết đến từng máy.

Trong quá trình hoạt động này, `mtr` đưa ra một số thống kê hữu ích về mỗi máy - được cập nhật theo thời gian thực theo mặc định.

## Cài đặt

Đối với bản phân phối Debian/Ubuntu

    apt update && apt upgrade
    apt install mtr-tiny

Đối với bản phân phối CentOS/RHEL

    yum update
    yum install mtr

## Sử dụng

**Hướng dẫn sử dụng**

    man mtr
    mtr --help

**Kiểm tra kết nối tới domain/IP**

    mtr [domainName/IP]

Ví dụ:

    mtr google.com
    mtr 8.8.8.8

![Imgur](https://i.imgur.com/llVcsDr.png)

**Hiển thị IP thay vì hostname dùng flag `-n`**

    mtr -n [domainName/IP]

Ví dụ:

    mtr -n google.com

![Imgur](https://i.imgur.com/TLlE5UU.png)

**Hiển thị địa chỉ IP và hostname tương ứng của các điểm kết nối với flag `-b`**

    mtr -b [domainName/IP]

Ví dụ:

    mtr -b google.com

![Imgur](https://i.imgur.com/pGHdtbF.png)

**Giới hạn số lượng gói tin ping gửi đi với flag `-c`**

    mtr -c [n] [domainname/IP]

Ví dụ:

    mtr -c 10 google.com

![Imgur](https://i.imgur.com/ieKarPd.png)

**Sử dụng chế độ báo cáo**

    mtr –r -c [n] [domainname/IP]

Ví dụ:

    mtr -r -c 20 google.com

![Imgur](https://i.imgur.com/W0yERU1.png)


**Xuất kết quả ra file với flag `-r`**

Thay vì hiển thị kết quả lên trên màn hình command line bạn có thể xuất ra file để lưu và theo dõi.

    mtr –r -c [n] [domainname/IP] >[report-name]

Ví dụ:

    mtr -r -c 20 google.com >mtr-google

![Imgur](https://i.imgur.com/UhM3PV0.png)

Thêm flag `-w` sẽ cho kết quả hiển thị dễ nhìn hơn (rộng hơn)

    mtr -rw -c 20 google.com >mtr-google

**Sử dụng gói TCP SYN hoặc datagram UDP**

Chúng ta có thể sử dụng TCP SYN hoặc UDP datagram để yêu cầu mtr thay vì các yêu cầu ICMP ECHO mặc định bằng cach chúng ta sử dụng flag `--udp` và `--tcp` như bên dưới:

    mtr -r --tcp google.com

    mtr -r --udp google.com

![Imgur](https://i.imgur.com/OtPDGI7.png)

**Chỉnh khoảng thời gian gửi mỗi gói tin ICMP và ECHO với flag `i`**

Mặc định khoảng thười gian liên tiếp giữa mỗi gói tin ICMP, ECHO là 1 giây bạn có thể thay đổi lại tham số này.

    mtr -i [time-in-seconds] “domainName/IP”

Ví dụ:

    mtr -i 10 google.com

**Chỉ định package size bytes với flag `-s`**

Sử dụng tùy chọn -s trong lệnh mtr chỉ định kích thước đơn vị là byte thực hiện như sau:

    mtr –r -s [packetsize] “domainName/IP”

Ví dụ:

    mtr -r -s 60 google.com

![Imgur](https://i.imgur.com/Tcuz4Eg.png)

**Chỉ định số hop tối đa giữa host mtr và host đích với flag `-m` (mặc định là 30)**

    mtr -m [timeInSeconds] “domainName/IP”

Vd:

    mtr -m 2 google.com

![Imgur](https://i.imgur.com/gtwglPT.png)

**Xuất file kết quả ra dạng cvs với flag `csv`**

    mtr –-csv “domainName/IP”

Vd:

    mtr --csv google.com

![Imgur](https://i.imgur.com/B2x60xU.png)

**Xuất file kết quả ra dạng xml với flag `xml`**

    mtr -–xml “domainName/IP”

Vd:

    mtr --xml google.com

![Imgur](https://i.imgur.com/mRKZyWJ.png)

## Sử dụng `mtr` trên Windows

Download tại đây: https://sourceforge.net/projects/winmtr/

MTR trên windows thuộc dạng file `.exe` chạy không cần cài đặt lên máy

![Imgur](https://i.imgur.com/Oj0BfNy.png)

### Các option

![Imgur](https://i.imgur.com/ZQPVB61.png)

- Interval (sec): Khoảng thời gian giữa mỗi package mtr gửi đi.

- Ping size (bytes): Kích thức gói tin mtr gửi đi.

- Max hosts in LRU list: Số hop đi qua.

- Reslove name: Hiển thị hoặc không hiển thị hostname các hop đi qua.

Bạn chỉ cần nhập IP/Domain và click `Start`

![Imgur](https://i.imgur.com/RqsPcnE.png)

MTR trên windows hỗ trợ export kết quả ra dạng `text`, `html`.

![Imgur](https://i.imgur.com/fgnC2o8.png)

### Đọc kết quả

Đọc thêm: https://blogd.net/linux/chuan-doan-cac-van-de-ve-mang-bang-cong-cu-mtr/#4-%C4%91%E1%BB%8Dc-b%C3%A1o-c%C3%A1o-mtr

![Imgur](https://i.imgur.com/LXvUhOG.png)

- Hostname: Các hope package đi qua trước khi tới được host đích cần kiểm tra.

- Lost %: Tỷ lệ phần trăm các package bị mất khi truyền tới hope đó (Thông tin quan trọng cần thu thập).

- Sent: Số package gửi đi.

- Recv: Số package phản hồi về.

- Avrg: Thời gian phản hổi trung bình (ms).

- Best: Thời gian phản rồi nhanh nhất (ms).

- Worst: Thời gian phản hồi lâu nhất (ms).

- Last: Thời gian phản hồi của gói tin gần đây nhất (ms).

- StDev: Độ lệch chuẩn.

## Các vấn đề thường gặp

### Mạng máy chủ đích bị cấu hình không đúng cách

![Imgur](https://i.imgur.com/e2s5WFn.png)

Khi đến hop cuối cùng thì bị mất 100% gói tin. Khả năng cao là do tường lửa.

### Home Router 

Home Router  đôi khi gây ra các báo cáo sai lệch như bên dưới

![Imgur](https://i.imgur.com/x18RWJs.png)

Tỷ lệ mất gói tin tại hop thứ 2 là 100% được báo cáo không cho thấy có vấn đề. Bạn có thể thấy rằng không có mất mát trên các hop tiếp theo.

### Router nhà mạng cấu hình sai

Các gói tin không bao giờ đến đích

![Imgur](https://i.imgur.com/2S9iuxY.png)

Đôi khi còn dẫn đến loop

![Imgur](https://i.imgur.com/EQYIspK.png)

Giải pháp là liên hệ nhà cung cấp mạng

### ICMP rate limit

Về vấn đề mất gói. Nếu chỉ thấy mất gói ở 1 hop thì khả năng là do bị rate limit. Nếu mất gói ở nhiều hop liên tục thì khả năng là bị mất gói thật. ICMP rate limit cũng khiến cho độ trễ tăng cao. Nếu độ trễ chỉ tăng ở 1 hop duy nhất thì khả năng cao là do rate limit. Nếu độ trễ ở các hop liên tục nhau đều tăng cao thì khả năng là có vấn đề về mạng ngay tại hop đầu tiên bị tăng độ trễ.

![Imgur](https://i.imgur.com/m9tnk8I.png)

Cách block ping ICMP tới hệ thống: https://www.tecmint.com/block-ping-icmp-requests-to-linux/

### Timeout 

Có nhiều lý do dẫn đến timeout

Vài router discard ICMP và các gói reply bị absent sẽ được hiển thị trên output là timout (???). Timeout không nhất định là dấu hiệu mất gói. Các gói vẫn đến đích mà không bị mất hoặc trễ gói đáng kể. 

Timeout có thể do router drop các gói tin vì các mục đích liên quan đến chất lượng dịch vụ (QoS) hoặc có thể có vấn đề ở tuyến đường trở về.

![Imgur](https://i.imgur.com/LRP49QM.png)

### Giải pháp

Phần lớn các vấn đề trên là tạm thời. Nên liên hệ nhà cung cấp dịch vụ nếu tình trạng xuất hiện nhiều lần.

**Chú ý chụp lại kết quả mtr khi có sự cố và gửi cho nhà cung cấp mạng để họ đưa ra giải pháp**

Mặc dù các lỗi và sự cố định tuyến chiếm tỷ lệ phần trăm chậm liên quan đến mạng, nhưng chúng không phải là nguyên nhân duy nhất của hiệu suất bị suy giảm. Sự tắc nghẽn mạng, đặc biệt là trong khoảng cách xa trong thời gian cao điểm, có thể trở nên nghiêm trọng. Lưu lượng xuyên Đại Tây Dương và xuyên Thái Bình Dương có thể khá thay đổi và có thể bị tắc nghẽn mạng chung. Trong những trường hợp này, bạn nên đặt máy chủ và tài nguyên càng gần về mặt địa lý với đối tượng mục tiêu của họ càng tốt.

## Tham khảo

Cách sử dụng:

https://blog.cloud365.vn/linux/huong-dan-su-dung-mtr/

https://blogd.net/linux/chuan-doan-cac-van-de-ve-mang-bang-cong-cu-mtr/

https://www.tecmint.com/mtr-a-network-diagnostic-tool-for-linux/

Hướng dẫn đọc kết quả: https://www.linode.com/docs/networking/diagnostics/diagnosing-network-issues-with-mtr/