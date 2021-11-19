# Lệnh Tracert và Traceroute

Lệnh `tracert` trên Windows tương tự với lệnh `traceroute` trên Linux

Tracert là công cụ dựa trên nền tảng Windows cho phép bạn hỗ trợ chương trình kiểm tra cơ sở hạ tầng mạng. Trong bài này chúng tôi giới thiệu cách dùng Tracert để sửa chữa các vấn đề gặp trong thực tế. Điều này giúp tăng cường tính hữu ích của công cụ và chỉ cho bạn một số cách dùng khi làm việc với các mạng riêng của mình.

Tiện ích TCP/IP này cho phép bạn xác định các gói định hướng lưu chuyển trong toàn bộ mạng tới host cụ thể theo yêu cầu của bạn. Tracert hoạt động bằng cách tăng thêm giá trị "thời gian sống" (TTL) cho từng gói liên tiếp được gửi đi. Khi một gói đi qua một host, host này sẽ giảm TTL đi một giá trị và tiếp tục gửi nó sang host kế tiếp. Khi một gói có TTL đến được host cần tới, host sẽ loại bỏ gói và gửi thông báo thời gian ICMP quá hạn. Tracert nếu được dùng phù hợp và chính xác có thể giúp bạn tìm ra các điểm định tuyến không chính xác hoặc không tồn tại trong mạng của bạn.

`traceroute` là một công cụ xác định đường đi được sử dụng để đến đích của một gói tin. Công cụ này sử dụng giao thức ICMP, nhưng không giống như ping, xác định mọi bộ định tuyến trong đường đi. Lệnh traceroute rất hữu ích khi xử lý sự cố mạng vì nó có thể giúp chúng ta sác định vị trí các sự cố trong kết nối mạng. traceroute thường hoạt động bằng cách gửi một loạt các gói yêu cầu echo ICMP đến đích.

Trong Linux, theo mặc định traceroute sử dụng các gói UDP ở port (33434 - 33534). Lệnh traceroute hiển thị các tuyến đường thực hiện và chỉ IP và hostname của các router trên mạng

## Giới thiệu

Tracert là công cụ dòng lệnh nền tảng Windows dùng để xác định đường đi từ nguồn tới đích của một gói Giao thức mạng Internet (IP - Internet Protocol). Tracert tìm đường tới đích bằng cách gửi các thông báo Echo Request (yêu cầu báo hiệu lại) Internet Control Message Protocol (ICMP) tới từng đích. Sau mỗi lần gặp một đích, giá trị Time to Live (TTL), tức thời gian cần để gửi đi sẽ được tăng lên cho tới khi gặp đúng đích cần đến. Đường đi được xác định từ quá trình này.

## Hoạt động

Nhìn vào hình minh hoạ sau bạn có thể hình dung ra được cách thức Tracert hoạt động trong một mạng sản xuất.

![Imgur](https://i.imgur.com/HXbIIGG.png)

Chúng ta sẽ gửi lưu lượng từ trạm kiểm tra bên B (Site B) tới một server ở bên A (Site A). Các gói tin sẽ truyền đi trong mạng diện rộng (WAN), phân tách thành hai phía nối với nhau qua liên kết T1 và một liên kết dự phòng ISDN (Integrated Services Digital Network). Để dùng tiện ích Tracert, đơn giản bạn chỉ cần biết địa chỉ IP của máy đích muốn gửi đến, cách sử dụng Tracert chính xác và bạn cần tìm cái gì trong kết quả.

Tracert hoạt động dựa vào thao tác với trường Time to Live (TTL). Bằng cách tăng TTL và sau mỗi lần gặp router, giá trị của nó lại giảm đi một, gói tin được gửi tới router tiếp theo. Mỗi lần gói tin được gửi từ router này đến router khác, người ta gọi là nó đã thực hiện một bước nhảy (hop). Khi trường TTL có giá trị trở về 0, router sẽ gửi thông báo "Time Exceeded" ICMP (hết thời gian) tới máy nguồn. Bạn có thể xem ví dụ với mạng mẫu sau của chúng tôi trong phần minh hoạ bên dưới. Với địa chỉ IP nguồn và đích,… chúng ta sẽ dùng trạm làm việc ở Site B và server bên Site A để thực hiện bài kiểm tra.

![Imgur](https://i.imgur.com/Z2OBEQL.png)

Từ minh hoạ này bạn có thể thấy, IP nguồn là 10.1.2.4 và IP đích (ví dụ) có thể là 10.1.1.6. Việc định tuyến thông thường diễn ra từ Site B sang Site A, qua liên kết có dung lượng cao hơn là T1 (1.544 Mbqs). Liên kết ISDN có dung lượng 128 Kbqs chỉ được dùng dự phòng trong trường hợp liên kết chính gặp lỗi. Tracert sẽ chỉ cho bạn thấy các gói tin được gửi từ Site B, tại máy có địa chỉ 10.1.2.4, qua liên kết T1 tới máy có địa chỉ 10.1.1.1. như thế nào. Bạn còn có thể biết được cách gửi các gói tin tới mạng LAN cục bộ (10.1.1.0) và cuối cùng là 10.1.1.6 như thế nào.

Khi các gói tin đã được gửi đi, Tracert sẽ dùng interface đầu tiên trên router nó nhìn thấy để thông báo lại các bước nhảy router. Vì thế, hãy xem xét toàn bộ đường đi hoàn chỉnh của chúng ta trước khi gửi các gói tin đi.

![Imgur](https://i.imgur.com/V8cTVB5.png)

Đường đi (được tô màu đỏ trên hình) là danh sách các router nằm giữa host nguồn và host đích. Một điểm rất quan trọng cần nhớ là các interface ở phía bên trái sẽ được dùng khi mô tả. Interface bên trái là interface của router gần nhất với host gửi tin trong đường đi. Trong ví dụ này bạn có thể thấy đường đi qua T1, từ phía B (Site B) sang phía A (Site A). Bây giờ chúng ta hãy cùng xem tại sao điều này lại quan trọng.

Vậy các cách làm việc của tracert là gì? Khi khởi chạy và sử dụng, tracert sẽ báo cáo (in ra) danh sách đã được sắp xếp các địa chỉ của từng host mà nó đã đi qua trên đường đến đích. Điều này thực sự hay vì bạn có thể biết được nhiều hơn về đường đi này. Nếu bạn thu được phần gần mặt phân cách, bạn sẽ thấy một thiết lập mới của địa chỉ IP trong hình minh họa tiếp theo (192.168.10.1 và 192.168.11.1) 10.1 được sử dụng cho liên kết ISDN và 11.1 sử dụng cho liên kết T1. Vậy tại sao điều này lại quan trọng?

Khi nhận được kết quả từ Tracert, một số người không thạo làm việc với công cụ này sẽ thấy lúng túng. Router cổng vào 10.1.1.1 mặc định của Site A được thay thế bằng địa chỉ WAN. Chỉ là một router nhưng interface khác. Điều này mang tính chất bắt buộc khi muốn kiểm tra với Tracert bởi vì nếu bạn nhầm, bạn sẽ không biết được mình đang đọc gì.

Ví dụ, đường đi bạn thấy ở hình minh hoạ trên là từ 10.1.2.4 tới 10.1.2.1 (cổng vào mặc định của mạng LAN). Sau đó nó sẽ qua mạng WAN tới 10.1.1.1. Chỉ có một vấn đề xuất hiện ở đây là bạn sẽ không thấy địa chỉ xuất hiện. Sau khi T1 có interface trên router (11.1) của phía A (Site A) và thực hiện liên kết ISDN (10.1) thì hai địa chỉ IP này là quan trọng nhất trong kết quả trả ra của Tracert. Đó là do trong ví dụ này T1 có thể bị lỗi và đường đi bây giờ là qua ISDN.

![Imgur](https://i.imgur.com/JEiTKtv.png)

Có thể đôi khi phần thông tin hiển thị ra ngoài không rõ ràng khiến bạn không hiểu. Chẳng hạn như khi xuất hiện các dấu hoạ thị bạn sẽ làm gì? Như đã đề cập đến ở phần trên, dấu hoa thị có thể hiển thị sai, vì gói ICMP có thể đã được chuyển đến nơi nhưng có cái gì đó đã cản trở quá trình thông báo lại, thường là một nguyên tắc nào đó trong tường lửa hoặc danh sách điều khiển truy cập (Access Control List).

Bạn có thể dùng Tracert để tìm ra chỗ gói tin bị ngừng lại trên mạng. Trong ví dụ sau, cổng mặc định đã tìm ra rằng không có đường đi nào hợp lệ ở bất kỳ host nào. Điều này có nghĩa là cả hai liên kết (T1 và ISDN) đều đã "sập" và không có đích nào có thể đến.

```
C:\>tracert 10.1.1.6 
Tracing route to 22.110.0.1 over a maximum of 30 hops 
----------------------------------------------------- 
1 10.1.2.1 reports: Destination net unreachable. 
Trace complete.
```

Từ ví dụ này bạn có thể thấy, khi bạn gửi yêu cầu kiểm tra Tracert tới địa chỉ 10.1.1.6, cổng LAN mặc định báo lại rằng nó không thể tìm thấy đường đi. Nhìn vào sơ đồ cụ thể sau có thể giúp bạn hiểu vấn đề rõ ràng hơn.

![Imgur](https://i.imgur.com/TbvV7Fh.png)

## Chú ý quan trọng về Tracert

Dưới đây là một số chú ý quan trọng giúp bạn hiểu sâu hơn về Tracert.

- Không phải lúc nào Tracert cũng giúp bạn tìm kiếm 'latency' (độ trễ). Để xác định đường đi và cung cấp độ trễ cũng như các gói tin thất lạc cho từng router và liên kết trong đường đi, sủ dụng lệnh "pathping".
- Tracert chỉ có thể dùng được nếu giao thức Internet Protocol (TCP/IP) đã được cài đặt như là thành phần trong các thuộc tính của một bộ điều hợp mạng (network adapter) ở Network Connections. Đây là một tiện ích TCP/IP sử dụng ICMP, một giao thức nằm trong bộ giao thức TCP/IP.
- Trong phiên bản Linux hiện đại, tiện ích traceroute (không phải là tracert mặc dù một số hệ thống Linux cũng cho phép bạn sử dụng tracert) dùng UDP datagram với mã số cổng là 33434. Windows dùng yêu cầu báo hiệu lại ICMP (kiểu 8) được biết đến nhiều hơn với các gói ping.

## Sử dụng

### Đối với Windows

Gõ lệnh trên cmd

    tracert <domain hoặc IP>

Ví dụ:

    tracert google.com
    hoặc
    tracert 8.8.8.8

![Imgur](https://i.imgur.com/7Z33GKY.png)

Các option:

    tracert [-d] [-h MaximumHops] [-j HostList] [-w Timeout] [TargetName]

- `-d`: Ngăn Tracert xử lý địa chỉ IP của các router ở giữa với tên của chúng. Điều này có thể giúp nâng cao tốc độ hiển thị kết của của Tracert.
- `-h`: Số lượng lớn nhất các hop (bước nhảy) trong đường đi đến host đích. Giá trị mặc định là 30 hop
- `-j`: Bạn có thể dùng tuỳ chọn này với một danh sách host (HostList). Các thông báo Echo Request (yêu cầu báo hiệu lại) dùng tuỳ chọn Loose Source Route trong phần header của địa chỉ IP với tập hợp các đích trung gian được mô tả trong HostList. Việc sử dụng tuỳ chọn Loose Source Route giúp các đích trung gian lần lượt được tách riêng bởi môt hoặc nhiều router. Số lượng lớn nhất địa chỉ hay tên trong danh sách host list là 9. HostList là một loạt địa chỉ IP (là các số thập phân liền nhau với các dấu chấm đan xen) cách nhau bởi một khoảng trắng.
- `-w`: Khoảng thời gian (tính theo mili giây) chờ thông báo ICMP Time Exceeded hoặc Echo Reply đáp lại tương ứng với thông báo Echo Request. Nếu vượt quá khoảng thời gian quy định mà không có thông báo gì, dấu hoa thị (*) sẽ được hiển thị. Thời gian mặc định là 4000 (tức 4 giây)
- `-?`: Phần trợ giúp ở màn hình lệnh.

VD:

![Imgur](https://i.imgur.com/N45H7Ag.png)

### Đối với Linux

Đầu tiên cần phải cài đặt `traceroute`

- Đối với bản phân phối Debian/Ubuntu:
```
    apt-get install traceroute
```
- Đối với với bản phân phối RHEL/CentOS:
```
    yum install traceroute
```

Gõ lệnh

    traceroute <domain hoặc IP>

Ví dụ:

    traceroute google.com
    hoặc
    traceroute 8.8.8.8

![Imgur](https://i.imgur.com/fX5Butd.png)

Kết quả cho thấy:

Dòng đầu tiên hiển thị tên máy chủ và ip đích, số bước nhảy tối đa đến máy chủ traceroute sẽ thử và kích thước của các gói byte được gửi.

Sau đó, mỗi dòng liệt kê một bước nhảy để đến đích. Tên máy chủ được cung cấp, theo sau là ip của tên máy chủ, theo sau là thời gian roudtrip cần cho một gói để đến máy chủ và quay lại máy tính khởi tạo.

Ví dụ traceroute không đến đích:

```
[root@test1 ~]# traceroute google.com
traceroute to google.com (216.58.200.14), 30 hops max, 60 byte packets
 1  gw-102-96-126-130.static.host.vn (102.96.126.130)  1.135 ms  1.337 ms  1.587 ms
 2  210.211.114.109 (210.211.114.109)  2.986 ms  3.047 ms  3.129 ms
 3  210.211.112.241 (210.211.112.241)  0.825 ms  0.814 ms 210.211.112.237 (210.211.112.237)  0.765 ms
 4  115.84.180.41 (115.84.180.41)  1.994 ms 115.84.180.45 (115.84.180.45)  0.806 ms 115.84.180.41 (115.84.180.41)  1.942 ms
 5  localhost (27.68.236.110)  0.944 ms  0.944 ms  0.872 ms
 6  localhost (27.68.236.109)  29.499 ms  32.649 ms  32.589 ms
 7  localhost (27.68.250.210)  28.877 ms  28.868 ms  28.841 ms
 8  * * *
 9  * * *
10  * * *
11  * * *
12  * * *
13  * * *
14  * * *
...
```

Trong ví dụ này, các dấu sao hiển thị mất gói. Điều này có thể có nghĩa là mất mạng, lưu lượng lớn dẫn đến tắc nghẽn mạng hoặc tường lửa làm giảm lưu lượng. Lệnh traceroute cho thấy không đến đích được.

**Các option:**

- `-4`: Để theo dõi tuyến đến máy chủ mạng bằng giao thức IPv4 (như mặc định)
- `-6`: Để theo dõi tuyến đến máy chủ mạng bằng giao thức IPv6. Ví dụ: `traceroute -6n ipv6.google.com`
- `-n`: Tắt tính năng ánh xạ địa chỉ IP và tên miền. Ví dụ: `traceroute -n google.com`

![Imgur](https://i.imgur.com/a3JpLMd.png)

- `-w`: Cấu hình thời gian chờ phản hồi. Ví dụ: `traceroute -w 0.01 google.com` Thời gian phản hồi là 0.01s

![Imgur](https://i.imgur.com/PqxxVd2.png)

- `-q`: Cấu hình số lượng truy vấn trên mỗi hop, mặc định là 3. Ví dụ: `traceroute -n -q 6 google.com`

![Imgur](https://i.imgur.com/VzzOJnw.png)

- `-f`: Chỉ hiển thị các hop từ dòng thứ x. Ví dụ x=4: `traceroute -n -f 4 google.com`

![Imgur](https://i.imgur.com/SwXZ0yF.png)

- `-m`: Chỉ hiển thị tối đa các hop = x. Ví dụ x=5: `traceroute -n -m 5 google.com`

![Imgur](https://i.imgur.com/ZcieBS9.png)

- `-F`: không phân mảnh gói tin

- `-n`: Không resolve các địa chỉ IP thành tên miền

- `-p`: Đặt port đích để dùng. Mặc định là port 33434. Ví dụ: `traceroute  -p 20292 google.com`

- Cấu hình full packet lenght. (Mặc định 1 packet 60 bytes). `traceroute  google.com 100`
- `--help`: Trợ giúp về lệnh. `traceroute --help`
## Tham khảo:

https://quantrimang.com/su-dung-tracert-35053

https://blogd.net/linux/huong-dan-dung-traceroute-trong-linux/

Các option:

https://www.geeksforgeeks.org/traceroute-command-in-linux-with-examples/