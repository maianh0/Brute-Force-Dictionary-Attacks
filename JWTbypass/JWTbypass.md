# Lab: JWT authentication bypass via weak signing key
**Thực hiện**: Mai Anh  
**Cập nhật lần cuối**: 01/08/2025

## Mục lục
- [1. Mô tả](#1-mô-tả)
- [2. Mục tiêu](#2-mục-tiêu)
- [3. Các bước thực hiện (gợi ý)](#3-các-bước-thực-hiện-gợi-ý)

---

## 1. Mô tả

Phòng lab này sử dụng **JWT (JSON Web Token)** để xử lý phiên đăng nhập (session). Tuy nhiên, hệ thống lại sử dụng một **khóa bí mật (secret key) rất yếu** để thực hiện việc **ký và xác thực token**.

Do secret key quá đơn giản, kẻ tấn công có thể **brute-force** bằng cách sử dụng một **wordlist chứa các secret phổ biến**.

### JSON Web Token (JWT)
- JWT là chuẩn token dùng để truyền tải dữ liệu giữa client và server. Một JWT gồm 3 phần: <Header>.<Payload>.<Signature> 
- Trong đó:
  - Header: chứa thông tin về thuật toán ký (ví dụ: HS256).
  - Payload: chứa dữ liệu (claims) như sub, iss, exp.
  - Signature: chữ ký số xác thực tính toàn vẹn, được tạo từ Header + Payload và một secret key.

### Lỗ hổng: Weak Signing Key
Nếu server dùng một secret yếu (ví dụ: "secret", "admin", "123456"), attacker có thể brute-force được key → tạo JWT hợp lệ → giả mạo quyền truy cập.

## 2. Mục tiêu bài lab

- Phát hiện và khai thác lỗ hổng xác thực sử dụng JWT có khóa ký yếu.
- Thực hiện brute-force để tìm khóa bí mật (secret key).
- Sử dụng secret tìm được để ký lại JWT giả mạo với quyền `administrator`.
- Gửi token đã sửa để truy cập `/admin` và xóa người dùng `carlos`.

## 3. Các bước thực hiện
