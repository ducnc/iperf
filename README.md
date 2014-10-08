- [Mục lục](#user-content-iperf)
- [1. Cài đặt](#user-content-1-c%C3%A0i-%C4%91%E1%BA%B7t)
- [2. Một số tham số phổ biến của iperf](#user-content-2-m%E1%BB%99t-s%E1%BB%91-tham-s%E1%BB%91-ph%E1%BB%95-bi%E1%BA%BFn-c%E1%BB%A7a-iperf)
- [3. Thực hiện các bài test với IPerf](#user-content-th%E1%BB%B1c-hi%E1%BB%87n-c%C3%A1c-b%C3%A0i-test-v%E1%BB%9Bi-iperf)
	- [Mô hình chung](#user-content-m%C3%B4-h%C3%ACnh-chung)
	- [Sử dụng TCP](#user-content-s%E1%BB%AD-d%E1%BB%A5ng-tcp)
	- [Sử dụng UDP](#user-content-s%E1%BB%AD-d%E1%BB%A5ng-udp)
- [4. Jperf = iperf + GUI](#user-content-jperf--iperf--gui)

iperf
=====

Hướng dẫn sử dụng iperf

Iperf là một công cụ hữu hiệu giúp chúng ta tính toán băng thông của mạng

[Trang chủ](https://iperf.fr)

### 1. Cài đặt

Trên Ubuntu:
    
    apt-get install iperf
    
Trên CentOS:

    yum install iperf
    
### 2. Một số tham số phổ biến của iperf

| Tham số | Tác dụng |
|---------|----------|
| -c | chỉ ra địa chỉ IP của server để iperf kết nối đến |
| -f, --format | Chỉ ra định dạng của kết quả hiển thị. 'b' = bps, 'B' = Bps, 'k' = Kbps, 'K' = KBps,... |
| -i, --interval | Thời gian lấy mẫu để hiển thị kết quả tại thời điểm đó ra màn hình |
| -p, --port | Định ra cổng để nghe, mặc định nếu không sử dụng tham số này là cổng 5001 |
| -u, --udp | Sử dụng giao thức UDP, mặc định iperf sử dụng TCP |
| -P, --parallel | Chỉ ra số kết nối song song được tạo, nếu là Server mode thì đây là giới hạn số kết nối mà server chấp nhận |
| -b | Định ra băng thông tối ta có thể truyền, chỉ sử dụng với UDP, client mode |
| -t | Tổng thời gian của kết nối, tính bằng giây |
| -M | Max segment size |
| -l | Buffer size |
| -w, --window | Trường Windows size của TCP |

### 3. Thực hiện các bài test với IPerf

#### Mô hình chung

<img src=http://i.imgur.com/izJHzno.png>

Để kiểm tra băng thông của mạng ta có thể sử dụng một trong hai giao thức TCP hoặc UDP, nhưng điểm chung giữa hai phương pháp này là đều cần 1 máy làm server để lắng nghe, một máy client kết nối đến giống như hình trên.
IPerf sẽ tính toán và đưa ra được băng thông của mạng giữa Server và client.

#### Sử dụng TCP

Cả máy server và client đều cần cài iperf.
Nếu sử dụng tham số cổng (-p) thì trên cả Server và client đều phải giống cổng nhau.

- Ví dụ một bài test đơn giản

Server:

    iperf -s
    
Client:

    iperf -c ip-server
    
Sau 10 giây kết quả sẽ trả về trên màn hình.

- Ví dụ bài test TCP với Buffer size: 16 MB, Window Size: 60 Mbps, Max segment size 5 trong thời gian 5 phút, kết quả hiển thị dưới dạng mbps

Server:

    iperf -s -P 0 -i 1 -p 5001 -w 60.0m -l 16.0M -f m
    
Client:
    
    iperf -c ip-server -i 1 -p 5001 -w 60.0m -M 1.0K -l 16.0M -f m -t 300

#### Sử dụng UDP

- Ví dụ một bài test đơn giản

Server:

    iperf -s -u
    
Client:

    iperf -c ip-server -u
    
Sau 10 giây kết quả sẽ trả về trên màn hình.

- Ví dụ bài test UDP với Bandwidth 600 Mbps Packet size 500 Bytes trong 300s

Server:

    iperf -s -u -P 0 -i 1 -p 5001 -f m

Client:

    iperf -c ip-server -u -i 1 -p 5001 -l 500B -f m -b 600m -t 300

- Kiểm tra tốc độ của một cổng mạng

Để làm việc này ta có thể đẩy tải liên lục bằng UDP tại máy chủ, do UDP truyền file mà không cần phải bắt tay 3 bước như TCP nên ta có thể đẩy UDP liên lục từ client, thay đổi băng thông và quan sát băng thông tối đa mà nó đạt được, đó cũng chính là giới hạn của card mạng.

Giả sử có một máy chủ card eth0 có ip 10.10.10.10 và tôi muốn kiểm tra xem tốc độ eth0 tối đa là bao nhiêu, tôi thực hiện như sau:

    iperf -c 10.10.10.1 -u -b 100m -t 100 -i 1
    iperf -c 10.10.10.1 -u -b 500m -t 100 -i 1
    iperf -c 10.10.10.1 -u -b 1g -t 100 -i 1
    iperf -c 10.10.10.1 -u -b 2g -t 100 -i 1
    
Quan sát kết quả thu được, lấy giá trị băng thông cao nhất do tham số -b là giới hạn băng thông UDP, nên ta có thể tăng giới hạn này lên để xác định tốc độ thật của card.

### 4. Jperf = iperf + GUI

Đây là một công cụ tương tự như iperf nhưng có thêm giao diện đồ họa, có thể download [tại](http://sourceforge.net/projects/jperf/)

Để sử dụng nó thì ta cần cài Java trên máy, sau đó chạy file jperf.bat (trên Windows) hay jperf.sh (trên Linux)

Giao diện của Jperf 

<img src=http://i.imgur.com/KNmcA7R.png>

Sau đó ta tích chọn vào những tham số cần dùng (bandwidth, windowsize,...), mode (server hay client),... Jperf cung cấp một biểu đồ để ta có thể theo dõi kết quả trực tiếp.
