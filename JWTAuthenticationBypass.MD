# Lab: JWT authentication bypass via weak signing key

# JWT Authentication Bypass Lab - Brute-force Weak Secret Key

## Mục lục
- [1. Mô tả](#1-🧾-mô-tả)
- [2. Mục tiêu](#2-🎯-mục-tiêu)
- [3. Thông tin đăng nhập](#3-🔐-thông-tin-đăng-nhập)
- [4. Gợi ý công cụ](#4-🧰-gợi-ý-công-cụ)
- [5. Các bước thực hiện (gợi ý)](#5-📌-các-bước-thực-hiện-gợi-ý)

---

## 1. 🧾 Mô tả

Phòng lab này sử dụng **JWT (JSON Web Token)** để xử lý phiên đăng nhập (session). Tuy nhiên, hệ thống lại sử dụng một **khóa bí mật (secret key) rất yếu** để thực hiện việc **ký và xác thực token**.

Do secret key quá đơn giản, kẻ tấn công có thể **brute-force** bằng cách sử dụng một **wordlist chứa các secret phổ biến**.

---

## 2. 🎯 Mục tiêu

1. **Brute-force** được secret key dùng để ký JWT.
2. **Chỉnh sửa nội dung token**, đổi quyền người dùng thành `administrator`.
3. **Tạo lại chữ ký JWT** bằng secret vừa brute được.
