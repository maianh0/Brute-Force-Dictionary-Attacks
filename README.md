# Brute-Force-Dictionary-Attacks

**Thực hiện**: Mai Anh  
**Cập nhật lần cuối**: 30/07/2025


## 1. Giới thiệu về Brute Force và Dictionary Attacks

### 1.1 Định nghĩa và phân biệt

#### Brute Force Attack

- **Định nghĩa**: Phương pháp tấn công thử tất cả các khả năng có thể của mật khẩu/key một cách tuần tự.
- **Đặc điểm**:
  - Thử từng ký tự, từng tổ hợp có thể (a, b, c... aa, ab, ac...)
  - Đảm bảo 100% tìm được mật khẩu nhưng tốn rất nhiều thời gian.
- **Ví dụ**: Thử mật khẩu từ `000000` → `999999` cho mã PIN 6 số.

#### Dictionary Attack

- **Định nghĩa**: Sử dụng danh sách các mật khẩu phổ biến, thường dùng (wordlist).
- **Đặc điểm**:
  - Nhanh hơn brute force nhưng không đảm bảo 100% thành công.
  - Dựa trên thói quen tạo mật khẩu yếu của người dùng.
- **Ví dụ**: Thử các mật khẩu như `"123456"`, `"password"`, `"admin"`.

### 1.2 Mục tiêu và nguyên lý hoạt động

#### Mục tiêu

- Chiếm quyền truy cập hệ thống, tài khoản.
- Crack hash password đã thu thập được.
- Bypass authentication mechanisms.
- Tìm hidden directories/files trên web server.

#### Nguyên lý hoạt động

1. Thu thập thông tin target (username, login form...)
2. Chuẩn bị wordlist/pattern tấn công  
3. Gửi request login với từng password candidate  
4. Phân tích response để xác định thành công/thất bại  
5. Lặp lại cho đến khi tìm được credentials đúng

## 2. Phương pháp thực hiện tấn công

### 2.1 Sử dụng các công cụ brute-force

#### A. Hydra - Network Login Cracker

**Các lệnh cơ bản**:

```bash
# SSH brute force
hydra -l maianh -P wordlist.txt ssh://172.16.1.29

# Kết quả thành công
[22][ssh] host: 172.16.1.29   login: maianh   password: ubuntu
1 of 1 target successfully completed, 1 valid password found
```
<img width="938" height="235" alt="image" src="https://github.com/user-attachments/assets/9ddffcee-b1e5-49a5-9c63-7f952a167c86" />

#### B. Dirsearch - Web Directory Scanner
**Thời gian quét**: Bắt đầu lúc [03:42 PM +07, July 30, 2025], hoàn thành lúc [03:43 PM +07, July 30, 2025].
**Kết quả**: Đã phát hiện nhiều endpoint và tệp, bao gồm:
- /google.com (HTTP 302)
- /test (HTTP 200)
- /etc/passwd (HTTP 200)
- /admin/%3Bindex/ (HTTP 200)
- /axis2-web/HappyAxis.jsp (HTTP 200)
- /cgi-bin/.%2e/.%2e/.%2e/.%2e/etc/passwd (HTTP 200)
- /Citrix/AccessPlatform/auth/clientscripts/cookies.js (HTTP 200)
- /engine/classes/swfupload/swfupload_f9.swf (HTTP 200)
- /jkstatus (HTTP 200)
- /login.wdm%2e (HTTP 200)
- Và nhiều thư mục/tệp khác
<img width="1448" height="697" alt="image" src="https://github.com/user-attachments/assets/5430f667-afe4-4fc2-906c-54766898874f" />

#### C. John the ripper

**Các lệnh cơ bản**:

```bash
# Tạo file md5.hash chứa hash MD5
echo "81d9d0b5d2d4dc2003d6db8313ed055" > md5.hash

# Hiển thị nội dung file md5.hash
cat md5.hash

# Sử dụng John the Ripper để crack hash MD5
sudo john --format=raw-md5 md5.hash

```
Ở đây ta sẽ bẻ khoá hàm băm md5. Hash dữ liệu “1234” với MD5
 <img width="940" height="228" alt="image" src="https://github.com/user-attachments/assets/8608a607-7b99-4c1e-bfd4-bc5688b18371" />

Thực hiện tạo 1 file là md5.hash để lưu dữ liệu vừa hash. 
Để thực hiển bẻ khóa mã hash MD5 trên với John the Ripper
<img width="940" height="326" alt="image" src="https://github.com/user-attachments/assets/70672851-abbf-4bc1-ac25-d8a28618e11a" />

#### D. FFUF
**Các lệnh cơ bản**:

```bash
# Hiển thị nội dung file myList.txt
cat /home/kali/Desktop/myList.txt

# Sử dụng FFUF để quét các thư mục/tệp trên web
ffuf -u https://united.com/Fuzz -w /home/kali/Desktop/myList.txt -e php,bak,zip,env,log,sql -mc 200,403 -t 50
```
- Ở đây ta sẽ quét các thư mục hoặc tệp ẩn trên trang web https://united.com/Fuzz bằng FFUF. Danh sách từ điển trong myList.txt sẽ được sử dụng để thử các đường dẫn.
  <img width="1252" height="561" alt="image" src="https://github.com/user-attachments/assets/07c7bbf0-817e-45b1-872c-92964efa650a" />

-Thực hiện tạo file myList.txt để lưu danh sách từ điển và sử dụng FFUF để quét các endpoint tiềm năng.


### 2.2 Ứng dụng Burp Suite Intruder

#### A. Cấu hình Intruder Attack

**Bước 1**: Capture Request

- Intercept login request với Burp Proxy  
- Send to Intruder (Ctrl+I)

**Bước 2**: Position Setup

```http
POST /login HTTP/1.1
Host: target.com
Content-Type: application/x-www-form-urlencoded

username=admin&password=§password§
```

- Đánh dấu parameter cần brute force với `§ §`
- Chọn Attack Type: Sniper, Battering Ram, Pitchfork, Cluster Bomb

**Bước 3**: Payload Configuration

- Simple List: Load wordlist từ file  
- Runtime File: Dynamic wordlist loading  
- Character Substitution: Thay thế ký tự (@ → a, 3 → e)  
- Case Modification: Uppercase/lowercase variants

#### B. Attack Types chi tiết

- **Sniper Attack**:
  - 1 payload set, 1 position  
  - Dùng cho brute-force một parameter duy nhất

- **Battering Ram**:
  - 1 payload set, nhiều vị trí  
  - Cùng một giá trị sẽ điền vào tất cả các vị trí

- **Pitchfork**:
  - Nhiều payload set, áp dụng tương ứng từng vị trí

- **Cluster Bomb**:
  - Nhiều payload set, thử tất cả tổ hợp (combinations)
#### Để nắm chi tiết, thực hiện một vài bài lab
**Lab: Username enumeration via different responses**
**Bước 1: Tìm đúng username**
- Mở Burp và truy cập trang login, nhập sai username + password.

- Trong Burp, vào Proxy > HTTP history, tìm request POST /login.

- Click phải vào phần username → chọn Send to Intruder.

- Trong tab Intruder, để chế độ Sniper, giữ nguyên password, chỉ đánh dấu username (§username§).

- Vào tab Payloads, dán danh sách các username cần thử và bấm Start attack.

- Khi kết thúc:

  - Xem cột Length → giá trị nào dài hơn có thể là username đúng.

  - So sánh response → nếu thấy "Incorrect password" thay vì "Invalid username" → bạn đã tìm ra username hợp lệ.
<img width="1861" height="960" alt="image" src="https://github.com/user-attachments/assets/91c07dfb-6b2a-46f6-9a98-df89b1f3d2d6" />
<img width="1808" height="890" alt="image" src="https://github.com/user-attachments/assets/4cdba024-d703-4033-b3db-13b571d6f7b4" />
- Từ hình trên ta thấy được username tìm được là "apache"
**Bước 2: Tìm đúng password**
- Quay lại Intruder, bấm Clear §.

- Sửa request thành: username=correct-user&password=§pass§.

- Trong tab Payloads, dán danh sách password cần thử và bấm Start attack.

- Khi kết thúc:

  - Xem cột Status: Nếu tất cả là 200, nhưng 1 dòng là 302 → đó là login thành công.
<img width="1806" height="844" alt="image" src="https://github.com/user-attachments/assets/04c44233-8517-42f8-9749-264a5ab052a9" />
> Ta thấy được username : apache và password : ginger
<img width="1853" height="821" alt="image" src="https://github.com/user-attachments/assets/313995a9-1a76-4e69-bfad-86a86dfed1b9" />

**Lab: Username enumeration via subtly different responses**
- **Bước 1: Xác định Username hợp lệ**
1. Mở Burp Suite và gửi một yêu cầu login với **username và password sai**.
2. Truy cập tab **Proxy > HTTP history**, tìm request `POST /login`.
3. **Bôi đen tham số `username`** và chọn **Send to Intruder**.
4. Trong tab **Intruder**:
   - Username sẽ được đánh dấu sẵn: `§username§`.
   - Đảm bảo chế độ attack là **Sniper**.
5. Chuyển sang tab **Payloads**:
   - Loại payload: **Simple list**.
   - Dán danh sách các username cần thử.
6. Vào tab **Settings** > mục **Grep - Extract**:
   - Nhấn **Add** → trong cửa sổ phản hồi, tìm dòng báo lỗi `"Invalid username or password."`.
   - **Bôi đen dòng này** để trích xuất phản hồi tự động.
7. Nhấn **Start Attack**.

### 📋 Sau khi tấn công:
- Một **cột mới** hiển thị nội dung lỗi từ phản hồi.
- Sắp xếp theo cột đó → phát hiện một dòng có lỗi **khác biệt nhẹ** (ví dụ có dấu cách ở cuối).
- Đó có thể là **username hợp lệ** → **ghi lại username này**.
<img width="1919" height="1013" alt="image" src="https://github.com/user-attachments/assets/db8f256c-bf60-4605-ae02-ae3c8ad584cc" />
<img width="1611" height="740" alt="image" src="https://github.com/user-attachments/assets/213bbf3f-b373-49ae-ab23-a3c268fea375" />

**Bước 2: Tìm đúng password**
- Quay lại Intruder, bấm Clear §.

- Sửa request thành: username=correct-user&password=§pass§.

- Trong tab Payloads, dán danh sách password cần thử và bấm Start attack.

- Khi kết thúc:

  - Xem cột Status: Nếu tất cả là 200, nhưng 1 dòng là 302 → đó là login thành công.
  - <img width="1605" height="761" alt="image" src="https://github.com/user-attachments/assets/e255d9c9-4986-4839-8fcc-74fccd2f3a1c" />
  > Ta thấy được username : alterwind và password : buster
<img width="1666" height="766" alt="image" src="https://github.com/user-attachments/assets/9da74006-6d66-46b1-a40b-ef9888ce950a" />

### 2.3 Kỹ thuật bypass bảo vệ

#### 1. Xoay IP (IP Rotation)
- Hệ thống bảo vệ thường theo dõi số lượng yêu cầu từ một địa chỉ IP trong khoảng thời gian nhất định. Nếu vượt quá ngưỡng (ví dụ: 10 lần đăng nhập thất bại trong 5 phút), IP sẽ bị chặn tạm thời (rate limiting) hoặc vĩnh viễn (IP ban).
- **Cách bypass**: 
  - Sử dụng nhiều địa chỉ IP khác nhau cho từng yêu cầu bằng cách áp dụng proxy (như proxy residential), VPN, hoặc dịch vụ ẩn danh như Tor. 
  - Ví dụ: Cấu hình công cụ như Burp Suite với danh sách proxy để tự động chuyển đổi IP sau mỗi yêu cầu.
  - Điều này giúp phân tán các yêu cầu, tránh bị phát hiện bởi các quy tắc chặn dựa trên IP.
- **Ví dụ** lab Broken brute-force protection, IP block
    - Thử đăng nhập bằng username Carlos thì đăng nhập sai 3 lần sẽ bị giới hạn số lần nhập sai
    - <img width="1919" height="1015" alt="image" src="https://github.com/user-attachments/assets/34d954bd-09fa-46a7-b1fb-c2d8202c0a03" />
    - Do đó cần phải tạo 1 danh sách tên và mật khẩu sao cho thử 2 lần và tới lần thứ 3 sẽ là mật khẩu đúng(wiener:peter)
    - <img width="1116" height="599" alt="image" src="https://github.com/user-attachments/assets/4898eaf1-803a-4786-8792-081916636c7b" />
    <img width="1919" height="948" alt="image" src="https://github.com/user-attachments/assets/44be8b5d-f99e-405f-83c1-9730f2c45cae" />
    <img width="1919" height="722" alt="image" src="https://github.com/user-attachments/assets/ecfe8783-3d1f-476a-93a9-afa350279cd9" />
- Đã dò được password là pepper
<img width="1616" height="856" alt="image" src="https://github.com/user-attachments/assets/a072e978-e08a-4f98-9955-444828e54786" />
<img width="1856" height="818" alt="image" src="https://github.com/user-attachments/assets/c4c2f92f-4be4-4d1c-aaea-c0fdc8fd53f2" />



#### 2. Giả mạo Header (Header Spoofing)
- Một số hệ thống sử dụng header như `X-Forwarded-For` hoặc `User-Agent` để theo dõi nguồn gốc yêu cầu. Nếu phát hiện nhiều yêu cầu từ cùng một header hoặc mẫu, hệ thống có thể chặn dựa trên đó.
- **Cách bypass**: 
  - Thay đổi các header như `X-Forwarded-For` hoặc `X-Real-IP` bằng các giá trị ngẫu nhiên (ví dụ: IP giả mạo) để đánh lừa hệ thống rằng yêu cầu đến từ nhiều nguồn khác nhau.
  - Sử dụng công cụ như Postman hoặc script Python với thư viện `requests` để tùy chỉnh header.
  - Tuy nhiên, hiệu quả phụ thuộc vào việc hệ thống có kiểm tra tính hợp lệ của header (ví dụ: so sánh với IP thực tế) hay không.

#### 3. Thử nghiệm hợp lệ xen kẽ (Valid Login Interleaving)
- Hệ thống thường đếm số lần đăng nhập thất bại liên tiếp và kích hoạt chặn (ví dụ: khóa tài khoản sau 5 lần thử sai) để ngăn brute-force.
- **Cách bypass**: 
  - Thực hiện đăng nhập hợp lệ (ví dụ: tài khoản "wiener" với mật khẩu "peter") xen kẽ giữa các lần thử brute-force để làm "làm mới" bộ đếm thất bại.
  - Sử dụng script tự động (như Python với `time.sleep()` để thêm độ trễ) để luân phiên giữa đăng nhập hợp lệ và thử sai, tránh vượt ngưỡng chặn.
  - Phương pháp này hiệu quả với các hệ thống không theo dõi toàn bộ lịch sử mà chỉ dựa vào chuỗi thất bại liên tiếp.

#### 4. Vượt qua CAPTCHA (Bypass CAPTCHA)
- CAPTCHA được triển khai để xác minh rằng người dùng là con người, thường được kích hoạt sau một số lần thử thất bại để ngăn bot thực hiện brute-force.
- **Cách bypass**: 
  - Sử dụng dịch vụ nhận diện CAPTCHA tự động (như 2Captcha hoặc Anti-Captcha) để giải mã và gửi lại kết quả.
  - Thực hiện tấn công trước khi CAPTCHA được kích hoạt (ví dụ: thử với tốc độ chậm hoặc trong khoảng thời gian không bị giám sát).
  - Kết hợp với các kỹ thuật khác (như xoay IP) để duy trì quá trình brute-force liên tục.
  - Lưu ý: Phương pháp này có thể bị phát hiện nếu hệ thống sử dụng CAPTCHA phức tạp hoặc giám sát chặt chẽ.

#### Response timing
- Hệ thống theo dõi thời gian phản hồi để phát hiện brute-force. Yêu cầu quá nhanh (dưới 1 giây) hoặc phản hồi chậm khi username không tồn tại (time-based comparison) sẽ kích hoạt chặn.
#### Cách vượt qua
- **Thêm độ trễ ngẫu nhiên**: Dùng script (như `time.sleep(1-5)` trong Python) để mô phỏng hành vi con người.
- **Phân tích thời gian**: Điều chỉnh tần suất dựa trên phản hồi bình thường (dùng `curl -w "%{time_total}"`).
- **Tấn công chậm**: Thử username mỗi 10 phút, kết hợp wordlist nhỏ.
- **Nhiều session**: Sử dụng proxy và đa luồng (như Hydra `-t`) để phân tán yêu cầu.
- **Ví dụ bài lab** Username enumeration via response timing
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/38e73c89-9adc-4c50-929d-4e38d2e5e1d3" />
<img width="1919" height="991" alt="image" src="https://github.com/user-attachments/assets/ce284af5-10d6-486e-8934-53b49916eb67" />
<img width="1609" height="861" alt="image" src="https://github.com/user-attachments/assets/e6a14ef0-c2a5-4e8c-bf40-1d75848896ee" />
- tương tự với password
- <img width="1877" height="980" alt="image" src="https://github.com/user-attachments/assets/3bfae7c5-f197-4f3f-86cb-133d309b02c3" />
<img width="1609" height="840" alt="image" src="https://github.com/user-attachments/assets/6d32f035-b89c-44a3-bf35-029e3a469431" />
> Tìm ra được username và password là access|mom.
<img width="1756" height="856" alt="image" src="https://github.com/user-attachments/assets/1b3253d6-9466-44fc-bc27-6f6f9d6d0840" />

### 2.4 Wordlist và Dictionary Management

- **RockYou**: Wordlist siêu phổ biến với hơn 14 triệu mật khẩu rò rỉ, tải từ Offensive Security hoặc GitHub. Dùng tốt cho SSH, FTP, web login.
- **John the Ripper Default**: Mật khẩu cơ bản kèm biến thể (như `password123`), có sẵn trong John (thường ở `/usr/share/john/password.lst`). Thử nhanh hoặc kết hợp quy tắc.
- **SecLists**: Bộ sưu tập đa dạng từ GitHub[](https://github.com/danielmiessler/SecLists), gồm mật khẩu, username. Hợp cho web và lỗ hổng đặc thù.
- **Crunch**: Tạo wordlist tùy chỉnh (như `?l?l?d?d`) ngay trên Kali với lệnh `crunch`. Dùng khi cần danh sách riêng.
- **Hashcat Default**: Mật khẩu phổ biến như `top1000.txt`, đi kèm Hashcat. Tốt cho crack hash hoặc brute-force phức tạp.
## Cách Tạo Wordlist dùng cho Brute-Force

### 1. Tạo Wordlist bằng Crunch

**Crunch** 

- Tạo wordlist với độ dài từ 8 đến 12 ký tự:

  ```bash
  crunch 8 12 -o wordlist.txt

  crunch 8 8 -t @@@%123 //Tạo mật khẩu theo mẫu: 3 chữ cái, 1 ký tự đặc biệt, sau đó là số 123
  ```
### 2. Tạo wordlist thủ công bằng notepad/mousepad
### 3. Tạo biến thể bằng John the Ripper
```bash
john --wordlist=mylist.txt --rules --stdout > mutated.txt
```
...
