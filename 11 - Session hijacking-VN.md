# Chiếm Quyền Phiên (Session Hijacking)

Session hijacking là việc kiểm soát một phiên giao tiếp TCP hợp lệ đang diễn ra. Hầu hết xác thực chỉ xảy ra ở đầu phiên TCP. Session ID hợp lệ bị đánh cắp để xác thực với server.

**Lý do session hijacking thành công:**
- Không có khóa tài khoản hoặc Session ID không hợp lệ
- Thuật toán tạo Session ID yếu hoặc Session ID quá nhỏ
- Xử lý Session ID không an toàn
- Session timeout vô hạn
- Hầu hết máy tính dùng TCP/IP đều dễ bị tấn công
- Hầu hết các biện pháp đối phó không hoạt động nếu không có mã hóa

---

## Các Giai Đoạn Chiếm Quyền Phiên

1. **Theo dõi kết nối** - Dùng sniffer theo dõi nạn nhân; Nmap để tìm TCP sequence dễ đoán
2. **Mất đồng bộ hóa kết nối** - Phải thay đổi số SEQ/ACK của server; gửi dữ liệu null; có thể gửi cờ reset
3. **Chèn gói tin của kẻ tấn công** - Chèn dữ liệu vào mạng hoặc tham gia như MITM

**Packet analysis khi hijack phiên cục bộ** - Cần biết Next Sequence Number (NSN).

**Passive session hijacking** - Chỉ quan sát và ghi lại lưu lượng, bắt ID và mật khẩu.

**Active session hijacking** - Tiếp quản phiên đang hoạt động bằng cách cắt đứt kết nối hoặc tham gia trực tiếp (ví dụ MITM - đoán sequence number trước khi mục tiêu phản hồi server).

**Spoofing** - Giả làm người dùng hoặc máy khác; khởi tạo phiên mới dùng thông tin xác thực bị đánh cắp.

**Hijacking** - Chiếm quyền kiểm soát phiên đang hoạt động; nạn nhân phải tự tạo kết nối trước.

---

## Chiếm Quyền Phiên Ở Tầng Ứng Dụng

- **Stealing (Đánh cắp)** - Nhiều kỹ thuật đánh cắp Session ID
- **Guessing (Đoán)** - Cố đoán Session ID bằng cách quan sát biến phiên
- **Brute forcing** - Thử mọi hoán vị có thể của Session ID

**Session sniffing:** Header của HTTP request (cookie) hoặc body của HTTP request

**Dự đoán session token:**
- Token tuần tự
- Token dựa trên timestamp
- Không gian token nhỏ (01-55,...)
- Trình tạo số ngẫu nhiên yếu (PRNG), thiếu rate limiting

---

## Man-in-the-Browser (Kẻ Thao Túng Trong Trình Duyệt)

1. Trojan lây nhiễm phần mềm trình duyệt
2. Trojan cài mã độc
3. Sau khi khởi động lại trình duyệt, mã được nạp
4. Handler được đăng ký cho mỗi lần truy cập trang web
5. Khi trang tải, extension khớp URL với danh sách trang web mục tiêu
6. Người dùng đăng nhập vào trang
7. Extension đăng ký event handler
8. Extension trích xuất giá trị trường DOM và sửa đổi chúng
9. Trình duyệt gửi giá trị đã sửa đến server
10. Server nhận giá trị đã sửa
11. Server xác nhận giao dịch
12. Trình duyệt nhận biên lai cho giao dịch đã bị sửa đổi
13. Trình duyệt hiển thị biên lai với thông tin gốc
14. Người dùng không biết gì

---

## Các Kỹ Thuật Xâm Phạm Session ID

| Tấn công | Ý tưởng cốt lõi | Cách hoạt động | Yêu cầu | Từ khóa CEH | Phòng chống |
|---------|----------------|----------------|---------|------------|------------|
| **XSS (Cross-Site Scripting)** | Chèn JS để đánh cắp cookie phiên | Script độc chạy trong trình duyệt nạn nhân đọc `document.cookie` | Xử lý đầu vào kém | Client-side code execution | Validate đầu vào, HttpOnly cookies |
| **Malicious JavaScript** | Payload JS đánh cắp token | JS bắt Session ID và gửi đến kẻ tấn công | Vector chèn script | Session token exfiltration | CSP, HttpOnly |
| **Trojan** | Malware đánh cắp dữ liệu phiên | Trojan đọc bộ nhớ trình duyệt hoặc cookie | Máy nạn nhân bị lây nhiễm | Client compromise | AV, endpoint security |
| **CSRF** | Lạm dụng phiên đã xác thực | Nạn nhân click link → trình duyệt tự gửi cookie | Người dùng đã đăng nhập | One-click attack / Session riding | Anti-CSRF token, SameSite cookie |
| **Session Replay Attack** | Bắt và tái sử dụng auth token | Kẻ tấn công sniff lưu lượng và replay request | Cho phép tái sử dụng token | Replay authentication | TLS, nonce, token expiration |
| **Session Fixation** | Kẻ tấn công đặt Session ID trước | Nạn nhân đăng nhập dùng Session ID đã biết | Session ID không được tái tạo | Pre-authentication session ID | Tái tạo Session ID sau đăng nhập |
| **Proxy-based Hijacking** | Đánh cắp phiên qua proxy giả | Nạn nhân kết nối qua proxy do kẻ tấn công kiểm soát | Người dùng tin proxy | Man-in-the-Browser | HTTPS, xác thực certificate |
| **CRIME Attack** | Rò rỉ bí mật qua nén | Khai thác tỷ lệ nén TLS/HTTP để suy ra cookie | TLS compression bật | Compression side-channel | Tắt TLS compression |
| **FREAK / Forbidden Attack** | Phá mật mã TLS | MITM ép dùng mã hóa yếu hoặc tái sử dụng nonce (AES-GCM) | Cấu hình TLS yếu | TLS downgrade / nonce reuse | Cipher mạnh, TLS hardening |
| **Session Donation Attack** | Nạn nhân xác thực phiên của kẻ tấn công | Kẻ tấn công đăng nhập → nạn nhân click link → kẻ tấn công truy cập | Phiên dùng chung hoặc tái sử dụng | Session misbinding | Bind phiên theo user/IP/thiết bị |

**Ghi nhớ theo loại:**
```
CODE    → XSS, Malicious JS
TRUST   → CSRF, Session Donation
REUSE   → Replay, Fixation
NETWORK → Proxy, CRIME
CRYPTO  → FREAK / Forbidden
```

---

## Chiếm Quyền Phiên Ở Tầng Mạng

### TCP/IP Hijacking

1. Sniff kết nối nạn nhân và gửi gói giả mạo với packet number đã đoán
2. Máy nhận xử lý gói và tăng sequence number
3. Máy nạn nhân bỏ qua gói ACK và lệch đếm sequence
4. Máy nhận nhận gói với sequence number sai
5. Kẻ tấn công đẩy kết nối nạn nhân vào trạng thái mất đồng bộ
6. Theo dõi sequence và liên tục giả mạo gói từ IP nạn nhân
7. Kẻ tấn công giao tiếp trong khi kết nối nạn nhân bị treo

### IP Spoofing - Source Routed Packets

1. Giành quyền truy cập bằng IP của máy chủ tin cậy
2. Server chấp nhận gói từ kẻ tấn công (IP đã bị giả mạo)
3. Chèn gói giả trước khi máy chủ thật phản hồi
4. Gói gốc bị mất; server nhận gói với sequence number đã dùng bởi kẻ tấn công
5. Gói được source-route với địa chỉ đích do kẻ tấn công chỉ định

### RST Hijacking

- Chèn gói RST có vẻ xác thực với địa chỉ nguồn giả mạo
- Kẻ tấn công có thể reset kết nối nạn nhân nếu có số ACK chính xác
- Nạn nhân tin rằng nguồn đã gửi gói RST và reset kết nối
- Công cụ: Colasoft Packet Builder, tcpdump

### Blind Hijacking

- Kẻ tấn công có thể chèn dữ liệu hoặc lệnh độc vào phiên TCP ngay cả khi source routing bị tắt
- Kẻ tấn công gửi dữ liệu nhưng không xem được phản hồi

### UDP Hijacking

- Kẻ tấn công gửi UDP reply giả mạo đến UDP request của nạn nhân
- Dùng MITM để chặn phản hồi thật của server

### MITM Dùng ICMP Giả Mạo

- Gói ICMP giả mạo để chuyển hướng lưu lượng giữa client và host qua máy của kẻ tấn công
- Gói gửi thông điệp lỗi, đánh lừa server định tuyến qua kẻ tấn công

### ARP Spoofing

- Phát broadcast ARP request và thay đổi bảng ARP bằng cách gửi ARP reply giả mạo

### PetitPotam Hijacking

- Ép Domain Controller khởi tạo xác thực đến server của kẻ tấn công
- Dùng MS Encrypting File System Remote Protocol (MS-EFSRPC) API để chiếm phiên xác thực
- Relay xác thực NTLM từ DC đến AD Certificate Services để lấy quyền admin

**Phân loại theo cơ chế:**
```
TCP Sequence Abuse → TCP/IP Hijacking, Blind Hijacking, RST Hijacking
Trust Abuse        → IP Spoofing, PetitPotam
Stateless Abuse    → UDP Hijacking
Routing Abuse      → ICMP Forgery
LAN Poisoning      → ARP Spoofing
```

---

## Bảng Tổng Hợp Tấn Công Mạng

| Tấn công | Ý tưởng cốt lõi | Yêu cầu | Từ khóa CEH |
|---------|----------------|---------|------------|
| **TCP/IP Hijacking** | Chiếm phiên TCP bằng mất đồng bộ sequence | Sniff hoặc đoán TCP sequence | Sequence number prediction, desynchronization |
| **IP Spoofing (Source Routing)** | Giả mạo host tin cậy | Source routing bật + IP tin cậy | Trusted host abuse, source routing |
| **RST Hijacking** | Buộc chấm dứt phiên TCP | Sequence/ACK number chính xác | TCP RST flag, forced reset |
| **Blind Hijacking** | Chèn dữ liệu mà không thấy phản hồi | Sequence number dự đoán được | Blind injection, no sniffing |
| **UDP Hijacking** | Chèn hoặc thay thế giao tiếp UDP | UDP stateless | Packet spoofing, connectionless |
| **MITM qua ICMP giả** | Chuyển hướng lưu lượng qua lỗi ICMP giả | Tin vào thông điệp định tuyến ICMP | ICMP redirect, traffic rerouting |
| **ARP Spoofing** | Đầu độc ARP cache để chặn lưu lượng | Truy cập mạng cục bộ | ARP poisoning, MAC spoofing |
| **PetitPotam** | Ép DC xác thực và relay | NTLM bật + AD CS | Authentication coercion, NTLM relay |

---

## Công Cụ Chiếm Quyền Phiên

- **Hetty** - MITM proxy, HTTP client
- **Caido** - Bộ công cụ kiểm toán bảo mật
- **bettercap** - Viết bằng Go

## Công Cụ Phát Hiện Session Hijacking

- USM Anywhere
- Wireshark

---

## Phòng Chống Session Hijacking

- HTTP Strict Transport Security (HSTS)
- Token Binding
- Công cụ: Checkmarx One SAST, Fiddler

## Phòng Chống MITM

- DNS over HTTPS (DoH)
- WPA3
- VPN
- Xác thực hai yếu tố (2FA)
- Password manager
- Zero-trust
- PKI
- Network segmentation

---

## IPsec

**Các chế độ:**
- **Transport mode** - Chỉ mã hóa payload của gói IP
- **Tunnel mode** - IPsec đóng gói toàn bộ gói IP
