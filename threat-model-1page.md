# Threat Model - Lab 6 AES-CBC Socket

## Thông tin nhóm

- Thành viên 1: Lê Hoàng Nam - MSSV: 1871020417
- Thành viên 2: Không có

## Assets

- Plaintext gốc được gửi từ sender.
- AES key và IV dùng cho AES-CBC.
- Ciphertext truyền qua kênh dữ liệu.
- File đầu vào `sample_input.txt` và file đầu ra `sample_output.txt`.
- Log gửi/nhận chứa thông tin kết nối và nội dung mã hóa.

## Attacker model

- Kẻ tấn công có thể nằm trên cùng mạng LAN hoặc can thiệp đường truyền TCP giữa sender và receiver.
- Attacker có khả năng nghe lén (eavesdrop) hai kênh TCP, bắt gói tin key/IV và ciphertext.
- Attacker có thể sửa đổi nội dung ciphertext, replay packet cũ hoặc xâm nhập vào log.
- Attacker không có key AES nội bộ ban đầu nhưng có thể dùng dữ liệu thu thập để tấn công thêm.

## Threats

- Key disclosure: Key/IV được gửi plaintext qua kênh khóa, nên attacker nghe lén có thể thu được thông tin này và giải mã ciphertext.
- Tampering: Ciphertext có thể bị sửa trên kênh dữ liệu, dẫn đến dữ liệu giải mã không đúng hoặc lỗi padding.
- Replay attack: Packet cũ có thể bị gửi lại, khiến receiver giải mã lại cùng một ciphertext mà không có kiểm tra tính mới.
- Log leakage: Nếu key/IV hoặc plaintext bị ghi vào log, attacker có thể khai thác log để thu được thông tin nhạy cảm.
- No authentication: Receiver không xác thực sender, nên attacker giả mạo sender được phép gửi dữ liệu tới receiver.

## Mitigations

- Không dùng thiết kế này trong hệ thống thật; kênh khóa chỉ là mô phỏng học tập.
- Thay vì gửi key/IV plaintext, dùng TLS hoặc cơ chế trao đổi khóa an toàn (ví dụ Diffie-Hellman) để bảo vệ kênh khóa.
- Dùng chế độ mã hóa có xác thực như AES-GCM để phát hiện các thay đổi ciphertext.
- Tránh ghi key/IV và plaintext thực tế vào log trong môi trường sản xuất.
- Thêm cơ chế chống replay như nonce, sequence number hoặc timestamp và xác thực sender.

## Residual risks

- Hệ thống vẫn không an toàn cho môi trường thật vì key/IV truyền plaintext và không có xác thực kênh.
- Chưa có bảo vệ chống replay đầy đủ và chưa dùng chế độ mã hóa có xác thực.
- Trust boundary vẫn yếu nếu attacker có thể truy cập mạng nội bộ hoặc log.
