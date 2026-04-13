# Mạng TCP/IP

- Sử dụng mạng chuyển mạch (switched network) giúp giảm số lượng frame nhận được không được đánh địa chỉ đến hệ thống của bạn
- UDP - không hướng kết nối (connectionless)
- Các giao thức sử dụng UDP phổ biến nhất: TFTP, DNS, DHCP
- **Cờ TCP (TCP Flags):**
  - **SYN (Synchronize)** - Khởi tạo kết nối, thương lượng tham số và số thứ tự (sequence number)
  - **ACK (Acknowledgement)** - Xác nhận cờ SYN, được đặt trên tất cả các segment sau cờ SYN ban đầu
  - **RST (Reset)** - Buộc chấm dứt kết nối theo cả hai hướng
  - **FIN (Finish)** - Đóng kết nối
  - **URG (Urgent)** - Cho biết dữ liệu bên trong được gửi ngoài dải (out of band), ví dụ hủy bỏ thông điệp giữa chừng

Số thứ tự SYN là ngẫu nhiên và tăng dần với mỗi gói tin gửi đi, giúp duy trì tính hợp lệ và duy nhất của phiên. Có nhiều kiểu tấn công có thể đoán được số thứ tự này.

> **Ghi nhớ bắt tay ba bước (three-way handshake) và các cờ TCP để thi: SYN → SYN/ACK → ACK → FIN**

**Công cụ tạo gói tin (Packet crafting tools):**
- NetScanTools
- Ostinato
- packETH
- LANforge FIRE
- Colasoft Packet Builder

---

# Trinh Sát / Thu Thập Thông Tin (Footprinting/Reconnaissance)

## Các Loại Trinh Sát

- **Thụ động (Passive)** - Không tương tác trực tiếp; dùng OSINT, cơ sở dữ liệu, chia sẻ thông tin tình báo
- **Chủ động (Active)** - Truy vấn DNS, kỹ thuật tấn công phi kỹ thuật (social engineering), dò quét cổng,...

## Toán Tử Tìm Kiếm Nâng Cao Google

| Toán tử tìm kiếm | Mục đích |
| ---------------- | -------- |
| `cache:` | Hiển thị trang web trong bộ nhớ đệm của Google |
| `link:` | Liệt kê các trang web có liên kết đến trang được chỉ định |
| `related:` | Liệt kê các trang web tương tự trang được chỉ định |
| `info:` | Hiển thị thông tin Google có về một trang cụ thể |
| `site:` | Giới hạn kết quả trong tên miền được chỉ định |
| `allintitle:` | Giới hạn kết quả chứa tất cả từ khóa trong tiêu đề |
| `intitle:` | Giới hạn kết quả chứa từ khóa trong tiêu đề tài liệu |
| `allinurl:` | Giới hạn kết quả chứa từ khóa trong URL |
| `inurl:` | Tài liệu có chứa từ khóa trong URL |
| `location:` | Tìm kiếm thông tin theo vị trí địa lý cụ thể |

- **Meta search engine** - Startpage, Metagear, etools.ch | Ẩn địa chỉ IP của người dùng
- **FTP search engine** - NAPALM FTP indexer, FreewareWeb, Mamont, globalfilesearch.com
- **Tìm kiếm SCADA và IoT** - Shodan, Censys, ZoomEye
- **Tên miền cấp cao và subdomain** - Netcraft, DNSdumpster, pentest-tools, Sublist3r
- **Photon** - Khôi phục URL đã lưu trữ (archived URLs)
- **Dịch vụ tra cứu người** - Spokeo
- **Phát hiện hệ điều hành** - Netcraft, Censys
- **Thu thập thông tin tình báo cạnh tranh** - EDGAR database, D&B Hoovers, LexisNexis, BusinessWire, Factiva, MarketWatch, The Wall Street Transcript, Euromonitor, Experian, The Search Monitor, USPTO, ABI Inform Global, SimilarWeb, SEranking
- **Kho mã nguồn công khai** - ReconNG
- **Mạng xã hội** - TheHarvester

```bash
theHarvester -d microsoft -l 200 -b linkedin
# -d: chỉ định tên miền
# -b: chỉ định nguồn dữ liệu LinkedIn
# -l 200: giới hạn 200 kết quả
```

- **Phân tích mạng xã hội** - BuzzSumo
- **Trinh sát mạng xã hội** - Sherlock, Social Searcher

---

## WHOIS, Trinh Sát DNS

### WHOIS

**Các loại WHOIS:**
- **Thick WHOIS** - Lưu trữ thông tin WHOIS đầy đủ
- **Thin WHOIS** - Chỉ lưu tên máy chủ WHOIS
- **Decentralized WHOIS** - Thông tin đầy đủ do các tổ chức độc lập quản lý

**Các tổ chức đăng ký Internet khu vực (Regional Internet Registries):**
- **ARIN** - Châu Mỹ
- **AFRINIC** - Châu Phi
- **APNIC** - Châu Á - Thái Bình Dương
- **RIPE** - Châu Âu
- **LACNIC** - Mỹ Latinh và Caribbean

**Tra cứu vị trí địa lý:** IP2Location

---

### DNS

#### Các Loại Bản Ghi DNS

| Loại bản ghi | Nhãn | Mô tả |
| ------------ | ----- | ----- |
| A | Address record | Ánh xạ hostname sang IPv4 |
| AAAA | IPv6 address record | Ánh xạ hostname sang IPv6 |
| MX | Mail exchange | Xác định máy chủ thư cho tên miền |
| NS | Name server | Xác định máy chủ tên thẩm quyền (authoritative name server) |
| CNAME | Canonical name | Ánh xạ bí danh (alias) sang hostname thực |
| SOA | Start of Authority | Xác định thẩm quyền cho DNS zone |
| SRV | Service record | Chỉ định vị trí dịch vụ (LDAP, SIP) |
| PTR | Pointer record | Tra cứu ngược - ánh xạ địa chỉ IP sang hostname |
| RP | Responsible person | Liệt kê quản trị viên/chủ sở hữu tên miền |
| HINFO | Host information | Lưu loại phần cứng và hệ điều hành |
| TXT | Text Record | Lưu dữ liệu văn bản cho DKIM và SPF |

**Công cụ:**
- **Fierce** - Tìm subdomain, cấu hình sai DNS, dải IP, hostname, mẫu đặt tên nội bộ
- **DNSRecon** - Liệt kê DNS, khám phá máy chủ và subdomain
- **mxtoolbox**

---

## Traceroute

**Công cụ:** NetscanToolsPro, PingPlotter

**Công cụ theo dõi email:** eMailTrackerPro, IP2Location

---

## Kỹ Thuật Tấn Công Phi Kỹ Thuật (Social Engineering)

- **Eavesdropping (Nghe lén)** - Nghe trộm các cuộc trò chuyện
- **Shoulder surfing (Nhìn trộm qua vai)** - Bí mật quan sát mục tiêu
- **Dumpster diving (Lục thùng rác)** - Tìm kiếm thông tin trong rác thải
- **Impersonation (Giả mạo danh tính)** - Giả làm người hợp lệ hoặc được ủy quyền

---

## Tự Động Hóa Các Tác Vụ Trinh Sát

- **Maltego** - Xác định mối quan hệ và liên kết trong thế giới thực
- **Recon-ng** - Framework trinh sát web, mã nguồn mở
- **FOCA** - Tìm metadata và thông tin ẩn trong các tài liệu đã quét
- **Subfinder** - Khám phá subdomain
- **OSINT Framework**
- **Recon-dog** - Sử dụng API để thu thập thông tin về mục tiêu
- **BillCipher** - Nhiều chức năng: tra cứu DNS, WHOIS, dò quét cổng, zone transfer,...

---

## Cổng (Ports)

| Phân loại | Dải số cổng |
|-----------|-------------|
| **Cổng nổi tiếng (Well-known ports)** | 0 – 1.023 |
| **Cổng đã đăng ký (Registered ports)** | 1.024 – 49.151 |
| **Cổng động (Dynamic ports)** | 49.152 – 65.535 |

### Số Cổng Quan Trọng

| Số cổng | Giao thức | Giao thức truyền tải |
| ------- | --------- | --------------------- |
| 20/21 | FTP | TCP |
| 22 | SSH | TCP |
| 23 | Telnet | TCP |
| 25 | SMTP | TCP |
| 53 | DNS | TCP và UDP |
| 67 | DHCP | UDP |
| 69 | TFTP | UDP |
| 80 | HTTP | TCP |
| 88 | Kerberos | TCP |
| 110 | POP3 | TCP |
| 123 | NTP | UDP |
| 135 | MS RPC | TCP |
| 137–139 | NetBIOS | TCP/UDP |
| 143 | IMAP | TCP |
| 161/162 | SNMP | UDP |
| 178 | RTSP | TCP/UDP |
| 389 | LDAP | TCP/UDP |
| 443 | HTTPS | TCP |
| 445 | SMB | TCP |
| 514 | Syslog | UDP/TCP |

**CurrPorts** - Hiển thị tất cả cổng đang mở trên máy tính của bạn

**Trạng thái cổng:**
- `CLOSE_WAIT` - Phía từ xa đã đóng kết nối
- `TIME_WAIT` - Phía của bạn đã đóng kết nối

```bash
netstat -an     # Hiển thị tất cả kết nối và cổng đang lắng nghe
netstat -b      # Hiển thị tiến trình liên kết với cổng đang mở
```

---

## ICMP (Giao Thức Kiểm Soát Internet - Internet Control Message Protocol)

**Tầng mạng (Network Layer)**

**Các mã thông điệp ICMP:**
- `0` - Echo Reply
- `3` - Destination Unreachable:
  - `0` - Mạng đích không thể truy cập
  - `1` - Máy chủ đích không thể truy cập
  - `6` - Mạng không xác định
  - `9` - Mạng bị cấm bởi quản trị
  - `13` - Giao tiếp bị cấm bởi quản trị
- `4` - Source Quench
- `5` - Redirect
- `8` - Echo Request
- `11` - Time Exceeded

**Ping sweep** - Cách tốt nhất để tìm máy chủ đang hoạt động trên mạng, rất dễ bị phát hiện

**Công cụ ping sweep:** Angry IP Scanner, SolarWinds Engineers Toolset, Network Ping, OpUtils, Superscan, Advanced IP Scanner, Pinkie

**Nmap qua TOR:** `nmap --proxy socks4://127.0.0.1:9050 [target]`

---

## ARP

Liên kết địa chỉ IP với địa chỉ MAC trong mạng

```bash
nmap -sn -PR 192.168.1.69   # Quét ARP bằng Nmap
```

---

## Các Kiểu Dò Quét Cổng (Port Scan Types)

- **Full connect (TCP Connect)** - Thực hiện đầy đủ bắt tay ba bước, kết thúc bằng RST. Dễ phát hiện nhất nhưng đáng tin cậy nhất. Cổng mở trả về SYN/ACK, cổng đóng trả về RST.

- **Stealth (Half-open / SYN scan)** - Chỉ gửi gói SYN. Ít bị phát hiện hơn vì không hoàn thành kết nối.

- **Inverse TCP flag** - Dùng cờ FIN, URG hoặc PSH để kiểm tra. Cổng mở không có phản hồi; cổng đóng trả về RST/ACK. Không hoạt động với máy Windows.

- **XMAS scan** - Bật tất cả các cờ. Phản hồi giống Inverse TCP. Không hoạt động với Windows.

- **ACK flag probe** - Gửi cờ ACK và kiểm tra header trả về (TTL hoặc Window). Nếu TTL của RST nhỏ hơn 64 thì cổng mở. Cũng có thể dùng để kiểm tra firewall.

- **IDLE scan** - Giả mạo IP, cần một máy zombie đang nhàn rỗi.

---

## Nmap

Nmap không có tùy chọn mặc định sẽ chạy SYN scan.

**Các công cụ khác:** NetScanTools, Hping3
