# BÁO CÁO PHÂN TÍCH MÃ ĐỘC WANNACRY

## Bước 1: Chuẩn bị môi trường an toàn (Environment Setup)
- Môi trường thử nghiệm: GitHub Codespaces (Ubuntu Linux Container cô lập).
- Công cụ cài đặt & sử dụng: `binutils`, `file`, `sha256sum`, `strings`, `radare2`.

## Bước 2: Phân tích Tĩnh Cơ Bản (Basic Static Analysis)
- **Tệp tin phân tích:** `wannacry_sample.exe`
- **Định dạng Tệp:** Binary Executable (PE32 Structure simulation)
- **Mã Hash SHA-256:**
  - Kết quả chạy `sha256sum`: `2cf47d500eb7f52af74c311fe536575027bf1be901e8532498d929ed2d15660b` (Hash mẫu mô phỏng)
  - Hash thực tế của WannaCry 2.0: `24d004a104d4d54034dbc29245d06192ec1e6b514810904f932be1981e663a3e`
- **Kết quả Trích xuất Chuỗi (Strings Analysis):**
  - Domain Kill-Switch: `http://www.iuqerfsodp9ifjaposdfjhgosurijfaewrwergwea.com`
  - Các tệp tin thành phần: `tasksche.exe`, `mssecsvc.exe`, `c.wnry`, `t.wnry`
  - Lệnh can thiệp hệ thống: `cmd.exe /c vssadmin.exe Delete Shadows /All /Quiet`

## Bước 3: Phân tích Động Cơ Bản (Basic Dynamic Analysis)
- Khi thực thi, mã độc thực hiện truy vấn HTTP GET tới URL Kill-Switch.
- Nếu không thể kết nối tới URL, mã độc kích hoạt cơ chế giải mã và cài đặt tiến trình `tasksche.exe`.
- Đăng ký Registry Key Persistence: `HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Run`.

## Bước 4: Phân tích Tĩnh Nâng Cao (Advanced Static Analysis)
- Kiểm tra bằng Radare2 (`r2 -qc "iI; iz"`): Cấu trúc Binary xác nhận `havecode: true`.
- Sử dụng thuật toán AES-128 để mã hóa dữ liệu người dùng và RSA-2048 để bảo vệ khóa AES.
- Tích hợp module khai thác lỗ hổng **MS17-010 (EternalBlue)** trên cổng SMBv1 (TCP 445).

## Bước 5: Phân tích Động Nâng Cao (Advanced Dynamic Analysis)
- Mã độc tự động quét địa chỉ IP ngẫu nhiên và dải mạng LAN qua cổng 445 để lan truyền.
- Mã hóa tệp tin cá nhân, bổ sung phần mở rộng `.WNCRY`, xóa các bản sao lưu Shadow Copies và bật giao diện tống tiền `@WanaDecryptor@.exe`.

## Bước 6: Chỉ số Cảnh báo Xâm nhập (IoCs)
- **Hash (SHA-256):** `24d004a104d4d54034dbc29245d06192ec1e6b514810904f932be1981e663a3e`
- **File & Path:** `.WNCRY`, `tasksche.exe`, `mssecsvc.exe`, `@Please_Read_Me@.txt`
- **Network:** Cổng TCP 445 / 139, Domain Kill-Switch.

## Bước 7: Khuyến nghị & Phòng thủ
- Vá lỗ hổng MS17-010 cho các hệ điều hành Windows.
- Tắt giao thức SMBv1 và chặn cổng TCP 445 trên Firewall.
- Thực hiện chiến lược sao lưu dữ liệu offline (Offline Backup) định kỳ.
