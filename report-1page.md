# Report 1 page - Lab 6 AES-CBC Socket

## Thông tin nhóm

- Thành viên 1: Lê Hoàng Nam - MSSV: 1871020417
- Thành viên 2: Không có

## Mục tiêu

Bài lab yêu cầu xây dựng hệ thống truyền dữ liệu mã hóa qua hai kênh TCP riêng biệt. Thực hiện AES-CBC với PKCS#7 padding để bảo vệ nội dung, tách riêng kênh khóa (key/IV) và kênh dữ liệu (ciphertext), và thiết kế header độ dài để đảm bảo nhận dữ liệu đầy đủ qua socket. Đồng thời, cần kiểm thử các tình huống đúng và sai để chứng minh tính đúng đắn và phát hiện điểm yếu bảo mật.

## Phân công thực hiện

Do làm một mình, tôi tự đảm nhận toàn bộ phần thiết kế, mã hóa, gửi/nhận, test và ghi log. Tôi cũng hoàn thiện bộ kiểm thử để xác nhận các kịch bản padding, kênh khóa, kênh dữ liệu, sai khóa và dữ liệu bị thay đổi.

## Cách làm

- `sender.py` đọc plaintext từ biến môi trường `MESSAGE` hoặc file `INPUT_FILE`, mã hóa bằng AES-CBC và gửi key/IV qua `KEY_PORT`, ciphertext qua `DATA_PORT`.
- `receiver.py` lắng nghe hai cổng riêng biệt, nhận packet kênh khóa và packet kênh dữ liệu, giải mã ciphertext và ghi ra stdout hoặc file `OUTPUT_FILE`.
- `aes_socket_utils.py` định nghĩa padding PKCS#7, hàm tạo/kiểm tra key và IV, xây dựng và phân tích packet với header 4 byte network-order.
- Key packet có cấu trúc `[key_length: 4 bytes][key][iv: 16 bytes]`. Data packet có cấu trúc `[ciphertext_length: 4 bytes][ciphertext]`.
- Hàm `recv_exact` đảm bảo nhận đúng số byte yêu cầu, giúp tránh lỗi khi dữ liệu bị phân mảnh trên TCP.

## Kết quả

Đã hoàn thành hệ thống với các file bắt buộc và chạy thành công tất cả test:
- `16 passed` trong `pytest -q`
- Đã kiểm tra `test_key_channel_contract`, `test_data_channel_contract`, `test_tamper_negative`, `test_wrong_key_negative`, `test_aes_padding_and_header`, và `test_sender_receiver_local`
- Chạy demo local thành công với các biến môi trường `SERVER_IP`, `DATA_PORT`, `KEY_PORT`, `MESSAGE` và `OUTPUT_FILE`.

## Kết luận

Bài lab giúp hiểu rõ cách phối hợp AES-CBC với socket TCP và tầm quan trọng của thiết kế packet header. Đồng thời, học được rằng nếu key/IV được truyền plaintext thì thiết kế hoàn toàn không an toàn, dù dữ liệu chính thức đã được mã hóa. Đây là bài học quan trọng khi phân biệt kênh khóa và kênh dữ liệu trong hệ thống truyền thông bảo mật.
