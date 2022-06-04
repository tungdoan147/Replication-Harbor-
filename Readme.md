# Tổng quan 

## Harbor 
Harbor là một open source cloud native registry, dùng để lưu trữ, đánh dấu, scan các container images để phát hiện các lỗ hổng bảo mật.

Harbor giải quyết các thách thức phổ biến bằng cách cung cấp sự tin cậy (trust), sự tuân thủ, hiệu suất và khả năng tươnng tác.

## Harbor Replication
Replication cho phép người sử dụng nhân bản các resource giữa các Harbor với Harbor hoặc giữa Harbor với những nơi lưu trữ khác (Git Hub, Git Lab, Docker Registry, Docker Hub...) với 2 mode (pull và push). Tính năng này sẽ rất hữu ích trong việc bảo toàn các images khỏi những sự cố (Khi 1 server chạy Harbor sập vẫn có thể lấy image từ server Harbor backup) 

Khi người quản trị hệ thống Harbor thiết lập replication rule, tất cả các resources match với rule sẽ được nhân bản đến hệ thống Backup . Nếu namespace trên backup không có thì sẽ được tạo 1 cách tự động. Nếu nó đã tồn tại người quản trị cấu hình replication policy không được ghi đè, tiến trình sẽ bị thất bại. Thông tin thành viên sẽ không được nhân bản


Trong quá trình nhân bản sẽ có xuất hiện delay do network. Nếu quá trình replication failed, nó sẽ được lên lịch lại trong vài phút tiếp theo và lặp lại nhiều lần 

# Thiết lập 

Quá trình thiết lập gôm 3 bước chính 
- Tạo Replication Endpoint 
- Tạo Replication Rule 
- Thiết lập chạy Replication (Thủ công hoặc tự động)

### Chuẩn bị 
- 2 Server chạy 2 Harbor (2 hệ thống độc lập). Việc thiết lập trên Docker (Thiết lập sẽ viết ở 1 phần khác )
- 2 Server cần được kết nối mạng với nhau 
- IP 192.168.71.137 (Harbor Chính)
- IP 192.168.18.138 (Harbor Backup)

				Hình 1: Thiết lập các Harbor (Trên Docker)
![alt text](https://s3-ap-southeast-1.amazonaws.com/kipalog.com/l0uj9ncb1j_image.png)

				Hình 2: Giao diện Harbor Backup 
![alt text](https://s3-ap-southeast-1.amazonaws.com/kipalog.com/58npqgaa2f_image.png)

				Hình 3: Giao diện Harbor chính (Có sử dụng ngrok để public đường dẫn ra ngoài)

![alt text](https://s3-ap-southeast-1.amazonaws.com/kipalog.com/q9afmt4u74_image.png)
![alt text](https://s3-ap-southeast-1.amazonaws.com/kipalog.com/cqwwszcesb_image.png)

### Bước 1. Tạo Replication Endpoint

Để nhân bản từ Harbor chính sang phụ cần khai báo thông tin Harbor Backup ấy trên replication endpoint 

1) Vào mục Registries , chọn New Endpoint

![alt text](https://s3-ap-southeast-1.amazonaws.com/kipalog.com/6hspf2hrzt_image.png)

2) Trong phần Provider là 1 danh sách các Endpoint có thể thực hiện quá trình backup với các image của Harbor (Phần này sẽ chọn Harbor vì mô hình build 1 hệ thống Harbor backup ). Ngoài Harbor có rất nhiều tùy chọn khác để có làm replication endpoint.

![alt text](https://s3-ap-southeast-1.amazonaws.com/kipalog.com/7zads63st3_image.png)

3) Nhập URL dẫn đến replication endpoint. Với mục Access ID và Access Secret sẽ là user và password truy nhập vào Harbor Backup

![alt text](https://s3-ap-southeast-1.amazonaws.com/kipalog.com/ksvfo6uj0v_image.png)

Sau khi thiết lập chọn mục Test connection để thử kết nối đến Harbor Backup

![alt text](https://s3-ap-southeast-1.amazonaws.com/kipalog.com/lbyovtnk6z_image.png)

Test connection báo thành công. Chúng ta có thể thêm sửa xóa các endpoint vừa được đăng kí này ở mục Registries.

![alt text](https://s3-ap-southeast-1.amazonaws.com/kipalog.com/lwos1ex1az_image.png)

### Bước 2. Thiết lập Replication Rule

1) Vào mục Replications chọn New Replication Rule

![alt text](https://s3-ap-southeast-1.amazonaws.com/kipalog.com/jzd01rkcsu_image.png)

●	Replication mode: Có 2 chế độ Push-based hoặc Pull-based 

●	Source resource filter: Xác định Image để replicate

●	Destination registry: Chọn registry endpoint đã thiết lập trước đó 

●	Destination: Xác định vị trí lưu image ở hệ thống backup

●	Trigger Mode: Có 3 mode Manual-thủ công, Scheduled-Sao chép 1 cách định kỳ, Event Based-Khi có một sự kiện nào đó xảy ra (Image được push lên, được retag …). Bài viết sẽ chọn phương pháp Manual

![alt text](https://s3-ap-southeast-1.amazonaws.com/kipalog.com/botspztn2c_image.png)

### Bước 3. Kiểm tra 

1) Thực hiện push 1 image lên Harbor chính 

Bước 1. Trên local Docker login vào Harbor chính 

![alt text](https://s3-ap-southeast-1.amazonaws.com/kipalog.com/phdr2yfxo8_image.png)

Bước 2. Push image lên Harbor chính 

![alt text](https://s3-ap-southeast-1.amazonaws.com/kipalog.com/gwfqcwq0xr_image.png)

![alt text](https://s3-ap-southeast-1.amazonaws.com/kipalog.com/n0ciakh4tr_image.png)

2) Thực hiện replication mode Manual

Chọn mục Replications chọn Rule vừa thiết lập và chọn Replicate

![alt text](https://s3-ap-southeast-1.amazonaws.com/kipalog.com/r6rwcj288m_image.png)

Thông tin tiến trình hiển thị ở dưới 

3) Kiểm tra trên Harbor backup

![alt text](https://s3-ap-southeast-1.amazonaws.com/kipalog.com/dln4y0jn9n_image.png)

Như vậy đã thiết lập thành công.



