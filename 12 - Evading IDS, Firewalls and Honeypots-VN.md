# Né Tránh IDS, Firewall và Honeypot

## IDS (Intrusion Detection System - Hệ Thống Phát Hiện Xâm Nhập)

**Chức năng chính:**
- Thu thập và phân tích thông tin trong máy tính hoặc mạng để xác định truy cập trái phép và lạm dụng
- IDS = Packet sniffer chặn các gói tin, thường TCP/IP
- Phân tích và bắt các gói tin
- Đánh giá lưu lượng và phát cảnh báo

**Vị trí trong mạng:**
- Gần firewall (bên ngoài/bên trong)
- Vị trí lý tưởng bên trong: gần DMZ
- Thực tiễn tốt nhất: một IDS ngoài firewall và một bên trong gần DMZ

**Cơ chế phát hiện xâm nhập:**
- **Signature recognition (Nhận diện chữ ký)** - Phát hiện lạm dụng (misuse detection)
- **Anomaly detection (Phát hiện dị thường)** - Dựa trên đặc điểm hành vi
- **Protocol anomaly detection (Phát hiện dị thường giao thức)** - Sai lệch so với chuẩn giao thức

---

### NIDS (Network-based IDS)

- Hộp đen đặt trong mạng ở chế độ promiscuous, lắng nghe các mẫu
- Phát hiện DDoS và tấn công mạng diện rộng

### HIDS (Host-based IDS)

- Cài đặt trên máy chủ cụ thể
- Không phổ biến, tiêu tốn nhiều tài nguyên
- Có thể phát hiện nhiều hơn NIDS (như sửa đổi file)

---

### Các Loại Cảnh Báo IDS

| Loại | Hành động | Giải thích |
|------|-----------|-----------|
| **True positive** | Tấn công → Cảnh báo | IDS phát cảnh báo khi có tấn công thật |
| **False positive** | Không tấn công → Cảnh báo | IDS phát cảnh báo khi không có tấn công |
| **False negative** | Tấn công → Không cảnh báo | IDS không phát cảnh báo khi có tấn công |
| **True negative** | Không tấn công → Không cảnh báo | IDS không phát cảnh báo khi không có tấn công |

---

## IPS (Intrusion Prevention System - Hệ Thống Ngăn Chặn Xâm Nhập)

IPS được coi là IDS chủ động; không chỉ phát hiện mà còn ngăn chặn mối đe dọa.

- Tạo cảnh báo khi phát hiện lưu lượng bất thường
- Ghi log thời gian thực
- Chặn và lọc lưu lượng độc hại
- Phát hiện và loại bỏ mối đe dọa nhanh chóng
- Xác định mối đe dọa chính xác mà không tạo false positive

**Phân loại:** Host-based, Network-based

---

## Firewall (Tường Lửa)

- Ngăn chặn truy cập trái phép
- Đặt tại ranh giới giữa hai mạng
- Kiểm tra tất cả thông điệp vào/ra

### Kiến Trúc Firewall

- **Bastion host** - Bảo vệ mạng chống tấn công; làm trung gian giữa trong và ngoài; hai interface: public (Internet) và private
- **Screened subnet (DMZ)** - Tạo với firewall hai/ba chân
  - **Multi-homed firewall** - Node có nhiều NIC kết nối các phân đoạn mạng riêng biệt
- **DMZ (Demilitarized Zone)** - Vùng trung lập giữa mạng nội bộ an toàn và Internet không tin cậy

### Các Loại Firewall

**Dựa trên cấu hình:**
- **Network-based firewall** - Đặt ở vành đai mạng; kiểm tra header gói tin; ví dụ: Cisco Secure Firewall ASA, PA7500, Fortigate 7121F
- **Host-based firewall** - Trên từng PC/server; ví dụ: MS Defender, Comodo Firewall, Norton Firewall

**Dựa trên cơ chế hoạt động:**

| Loại Firewall | Tầng OSI | Cách hoạt động | Từ khóa CEH |
|--------------|---------|--------------|------------|
| Packet-Filtering | L3/L4 | Lọc theo IP, cổng, giao thức | Stateless filtering |
| Stateful Firewall | L3/L4 | Theo dõi trạng thái kết nối | Session awareness |
| Circuit-Level Gateway | L5 | Xác thực bắt tay TCP | Session validation |
| Application-Level Firewall (Proxy) | L7 | Kiểm tra dữ liệu ứng dụng | Deep packet inspection |
| NGFW (Next-Generation Firewall) | L3–L7 | DPI + IDS/IPS + application awareness | Application awareness |

---

## YARA Rules - Phát Hiện Xâm Nhập

YARA là công cụ nghiên cứu malware theo quy tắc:
- **Condition** - Khi nào kết quả là true với một file
- **Strings** - Định nghĩa chuỗi cần tìm trong file
- **Metadata** - Thông tin chung

**Công cụ:** yarGen

---

## Công Cụ IDS

- **Snort** - Phân tích lưu lượng và log gói tin; phân tích giao thức và tìm kiếm/khớp nội dung; ngôn ngữ rule linh hoạt
- **Suricata** - IDS thời gian thực, IPS inline, network security monitoring, xử lý PCAP offline

## Công Cụ IPS

- Trellix IPS - Phát hiện botnet, worm và tấn công trinh sát
- Check Point Quantum IPS
- McAfee

---

## Kỹ Thuật Né Tránh IDS và Firewall

### Nhận Diện

- **Port scanning** - Dò quét cổng
- **Firewalking** - Dùng TTL để xác định ACL filter của gateway và lập bản đồ mạng; packet có TTL lớn hơn một hop so với firewall
- **Banner grabbing** - Bản thông báo dịch vụ (service announcements)

### Giả Mạo IP, Source Routing và Phân Mảnh

- **IP address spoofing** - Thay đổi IP nguồn; tạo gói với địa chỉ nguồn giả; Hping để tạo gói
- **Source routing** - Gói được định tuyến qua phân đoạn ít được giám sát hơn
- **Tiny fragments** - Tạo các mảnh nhỏ, buộc thông tin header TCP vào mảnh tiếp theo

### Vượt Qua Firewall/IDS Dùng Proxy

Thêm cài đặt proxy vào PC.

### DNS Tunneling

- Dữ liệu độc có thể nhúng vào gói DNS; DNSSEC không phát hiện được
- Dùng để malware né IDS và duy trì kết nối C2
- **Công cụ:** iodine, dnscat2

### Hệ Thống Ngoài (External System)

- Người dùng làm việc từ xa, kẻ tấn công đánh cắp lưu lượng, Session ID hoặc cookie
- Kẻ tấn công truy cập mạng doanh nghiệp; phát lệnh `openURL()`
- Trình duyệt người dùng chuyển hướng đến server của kẻ tấn công; tải xuống và thực thi mã độc

### MITM Attack

- Kẻ tấn công đầu độc DNS server
- Người dùng gửi request facebook.com → truy cập server độc hại → kẻ tấn công tunnel HTTP traffic

### Vượt Qua Qua Nội Dung

- Gửi nội dung chứa mã độc: macro bypass exploit, .exe, .com, .bat,...

### XSS Attack

- Xảy ra khi xử lý tham số đầu vào; chèn mã HTML độc
- Dùng giá trị ASCII, mã hóa HEX, obfuscation

### Vượt Qua WAF

- **HTTP header spoofing** - Header và cú pháp giả mạo
- **Blacklist detection** - Xác định từ khóa trong blacklist (SQL)
- **Fuzzing/Brute forcing** - Wordlist của Assetnote
- **Lạm dụng SSL/TLS cipher** - sslscan2

### HTML Smuggling

- File đính kèm HTML5
- Qua JavaScript
- URL lure

### Windows BITS

- Background Intelligent Transfer Service - phân phối Windows updates tự động
- `bitsadmin` có thể tạo job để chuyển file độc hại; tạo persistence

---

## Các Kỹ Thuật Né Tránh Khác

| Kỹ thuật | Mô tả |
|---------|-------|
| **Insertion attack** | Làm nhầm lẫn IDS bằng cách buộc đọc gói tin không hợp lệ |
| **Evasion** | IDS bỏ gói nhưng host đích chấp nhận; xảy ra ở IP layer |
| **DoS** | Tiêu tốn tất cả tài nguyên IDS khiến thiết bị khóa |
| **Obfuscating** | Chỉ đích có thể giải mã, không phải IDS |
| **False positive generation** | Tạo lượng lớn báo cáo false để ẩn tấn công thật |
| **Session splicing** | Chia lưu lượng thành quá nhiều gói nhỏ; không gói nào kích hoạt IDS; công cụ: Nessus |
| **Unicode evasion** | Nhiều biểu diễn của một ký tự; UTF-16: "/" = "%u2215"; UTF-8: "©" = "%c2%a9" |
| **Fragmentation attack** | Nếu vượt MTU, gói bị chia nhỏ |
| **TTL attack** | Gói bị drop khi TTL = 0; cần biết topology mạng nạn nhân |
| **Urgency flag** | TCP bỏ qua dữ liệu trước URG pointer; IDS không xem xét tính năng này |
| **Invalid RST packets** | Gửi gói RST với checksum không hợp lệ |
| **Polymorphic shellcode** | Bao gồm nhiều chữ ký để né NIDS |
| **ASCII shellcode** | Chỉ dùng ký tự ASCII chuẩn |
| **Application layer attacks** | Qua file media - hình ảnh, audio, video |
| **Desynchronization** | Pre-connection SYN với checksum không hợp lệ; Post-connection SYN với sequence number khác nhau |
| **DGA (Domain Generation Algorithm)** | Phần mềm tạo tên miền mới để thực thi mã malware; thay đổi domain liên tục |

---

## Né Tránh NAC và Endpoint Security

### Network Access Control (NAC)

- **VLAN hopping** - Truy cập Dynamic Trunking Protocol (DTP); chuyển chế độ switch; công cụ: VLANPWN
- **Dùng thiết bị đã xác thực** - Truy cập thiết bị đã xác thực và lén gửi gói tin qua (thường dùng Raspberry Pi); công cụ: nac_bypass_setup.sh, FENRIR

### Vượt Qua Endpoint Security

| Kỹ thuật | Mô tả |
|---------|-------|
| **Ghostwriting** | Sửa đổi cấu trúc malware mà không ảnh hưởng chức năng; dùng binary deconstruction; công cụ: ghostwriting.sh |
| **Application whitelisting** | DLL hijacking để đặt DLL độc với tên hợp lệ mà ứng dụng tìm kiếm |
| **Dechaining macros** | Spawn qua ShellCOM; spawn dùng XMLDOM để tải và chạy code trong Office process |
| **Clearing memory hooks** | Tìm DLL và syscall, dùng x64dbg để xác định syscall trong bộ nhớ, tạo payload khôi phục byte gốc |
| **Process injection** | Malware trong không gian bộ nhớ của tiến trình đang chạy; API: VirtualAllocEx(), WriteProcessMemory(), CreateRemoteThread() |
| **LoLBins (Living off the Land Binaries)** | Dùng công cụ cài sẵn của hệ thống; cấu hình Deimos C2 qua HTTPS; tải file từ xa; thực thi shell |
| **Control Panel sideloading** | Giả mạo chức năng CPL applet gốc; CPLResourceRunner |
| **Metasploit templates** | msfvenom rồi kiểm tra với VirusTotal |
| **AMSI bypass** | Downgrade PowerShell xuống 2.0; dùng obfuscation; ép lỗi; hijack bộ nhớ |
| **Fast flux DNS** | Thay đổi nhanh cả địa chỉ IP lẫn tên DNS; vượt blacklist và ẩn C&C |
| **Timing-based evasion** | Sleep patching, delay API và time bomb |
| **Single binary proxy execution** | rundll32 |
| **Shellcode encryption** | Mã hóa shellcode |
| **Reducing entropy** | Thao túng đặc điểm nhị phân |
| **Escaping local AV sandbox** | Thoát khỏi sandbox AV cục bộ |
| **Disabling ETW** | Vô hiệu hóa Event Tracing for Windows |
| **Spoofing thread call stack** | Giả mạo call stack của thread |
| **In-memory beacon encryption** | Mã hóa beacon trong bộ nhớ |

---

## Công Cụ Né Tránh IDS/Firewall

- Traffic IQ Professional
- Colasoft Packet Builder

---

## Honeypot (Bẫy Mật)

- Ghi log truy cập cổng
- Giám sát tổ hợp phím
- Cảnh báo sớm

### Các Loại Honeypot

| Loại | Mô tả |
|------|-------|
| **Low-interaction** | Mô phỏng số lượng giới hạn dịch vụ và ứng dụng; ví dụ: Tiny-SSH-Honeypot, KFSensor, Honeytrap |
| **Medium interaction** | Mô phỏng hệ điều hành, ứng dụng và dịch vụ thật |
| **High interaction** | Mô phỏng tất cả dịch vụ và ứng dụng của mạng mục tiêu |
| **Pure honeypot** | Mô phỏng mạng sản xuất thật |
| **Production honeypots** | Triển khai trong môi trường sản xuất; thu thập thông tin giới hạn; low interaction |
| **Research honeypots** | Triển khai bởi viện nghiên cứu |
| **Malware honeypots** | Bẫy chiến dịch malware; mô phỏng API cũ, giao thức SMBv1 dễ bị tấn công |
| **Database honeypots** | Dành cho cơ sở dữ liệu |
| **Spam honeypots** | Open mail relay và open proxy |
| **Email honeypots** | Địa chỉ email giả |
| **Spider honeypots** | Bẫy web crawler và spider |
| **Honeynets** | Mạng các honeypot |

**Công cụ:** HoneyBOT (medium interaction), Blumira, NeroSwarm

---

### Phát Hiện Honeypot

```bash
nmap -sV -p 80 [ip]                           # Fingerprint dịch vụ đang chạy
nmap -p [scan-delay] 1s --max-retries 5 [ip]  # Phân tích thời gian phản hồi
arp-scan --interface=eth0 --localnet          # Kiểm tra MAC address - OUI bất thường
nmap -p [ip]                                   # Liệt kê cổng mở không mong đợi
```

### Phát Hiện và Đánh Bại Honeypot

| Phương pháp | Mô tả |
|-------------|-------|
| **Layer 7 tar pit** | Giống honeypot, làm chậm các lần thử trái phép; phát hiện bằng độ trễ phản hồi |
| **Layer 4 tar pit** | Thao túng TCP/IP stack, làm chậm phát tán worm; IP tables chuyển sang zero-window size |
| **Layer 2 tar pit** | Bảo vệ khỏi tấn công cùng mạng |
| **Honeypot trên VMware** | Phân tích địa chỉ MAC |
| **Honeyd honeypot** | Tạo phản hồi SMTP giả; phát hiện bằng TCP fingerprinting theo thời gian; SYN proxy behavior |
| **User-mode Linux (UML)** | Phân tích `/proc/mounts`, `/proc/interrupts`, `/proc/cmdline` |
| **snort_inline** | Có thể thao túng gói tin; viết lại rule iptables; dùng trong Gen 2 honeynets |
| **Bait and switch** | Chuyển hướng toàn bộ lưu lượng sang honeypot |

**Công cụ phát hiện honeypot:** Send-Safe Honeypot Hunter
