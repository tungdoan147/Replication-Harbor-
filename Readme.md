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
- IP 192.168.18.103 (Harbor Chính)
- IP 192.168.18.129 (Harbor Backup)

Hình 1: Giao diện Harbor Backup 
Hình 2: Giao diện Harbor chính (Em có sử dụngh ngrok để public đường dẫn ra ngoài)

### Bước 1. Tạo Replication Endpoint

Để nhân bản từ Harbor chính sang phụ cần khai báo thông tin Harbor Backup ấy trên replication endpoint 

1) Vào mục Registries , chọn New Endpoint
Hình 3. Thao tác 

2) 