# Nghe Lén Mạng (Network Sniffing)

Packet sniffing (nghe lén gói tin) là việc giám sát và bắt các gói dữ liệu đi qua mạng bằng ứng dụng phần mềm hoặc thiết bị phần cứng.

Cho phép quan sát và tấn công toàn bộ mạng từ bất kỳ điểm nào.

Kẻ tấn công chuyển NIC sang chế độ promiscuous để lắng nghe tất cả dữ liệu trong phân đoạn mạng.

---

## Các Môi Trường Ethernet

- **Shared Ethernet** - Bus duy nhất kết nối tất cả máy chủ chia sẻ băng thông; dùng Hub.
- **Switched Ethernet** - Switch duy trì bảng ARP (ánh xạ MAC ↔ cổng), chỉ gửi gói đến máy đích; do đó chế độ promiscuous không hoạt động. Tuy nhiên có các phương pháp khác.

---

## Phương Pháp Sniffing Trong Mạng Chuyển Mạch

### ARP Spoofing/Poisoning

ARP không có trạng thái (stateless). Máy có thể gửi ARP reply mà không cần được hỏi.
- **ARP spoofing** - Gửi bản tin ARP giả mạo liên kết MAC của kẻ tấn công với IP của máy khác, kích hoạt MITM
- **MAC flooding** - Tầng 2; kẻ tấn công gửi địa chỉ MAC giả cho đến khi bảng CAM đầy, lúc này switch hoạt động như hub phát tán gói đến mọi nơi
- **Công cụ:** arpspoof, Habu
- **Phòng thủ:** Dynamic ARP Inspection
- **Phát hiện:** Capsa Portable Network Analyzer, Wireshark, OPUtils, Netspionage

### MAC Spoofing/Duplicating

Giả mạo MAC để kết nối vào cổng switch.
- Thay đổi trong cài đặt Windows
- Công cụ: MAC Address Changer
- **Phòng thủ:** DHCP snooping binding table, Dynamic ARP Inspection, IP Source Guard

### ICMP Router Discovery Protocol (IDPR) Spoofing

Cho phép máy chủ khám phá địa chỉ IP của router đang hoạt động trong subnet.

---

## VLAN Hopping

### Switch Spoofing

Rogue switch tạo trunk giữa switch hợp lệ và switch giả. Chỉ khả thi khi interface được cấu hình "dynamic auto", "dynamic desirable" hoặc trunk mode.
- **Phòng thủ:** Cấu hình cổng là access port, không cho phép tự thương lượng trunk

### Double Tagging

Thêm và sửa đổi tag 802.1Q ngoài và trong frame Ethernet, cho phép lưu lượng đi qua bất kỳ VLAN nào.
- **Phòng thủ:** Chỉ định VLAN mặc định riêng; đổi tất cả VLAN trên trunk thành VLAN ID không sử dụng; gắn tag tường minh tất cả cổng VLAN

### STP Attack (Spanning Tree Protocol)

Đưa rogue switch vào mạng với priority thấp hơn mọi switch khác, khiến nó trở thành root bridge - toàn bộ lưu lượng đi qua nó.
- **Phòng thủ:** BPDU Guard, Root Guard, Loop Guard, UDLD (Unidirectional Link Detection)

---

## Các Loại Sniffing

- **Passive sniffing** - Không gửi gói tin nào
- **Active sniffing** - Chủ động chèn lưu lượng vào mạng:
  - MAC flooding
  - DNS poisoning
  - ARP poisoning
  - DHCP attack
  - Switch port stealing
  - Spoofing attack

---

## Giao Thức Dễ Bị Sniffing

- Telnet và Rlogin
- HTTP
- SNMP
- SMTP
- NNTP (Network News Transfer Protocol)
- POP
- FTP
- IMAP
- TFTP

---

## Phần Cứng Phân Tích Giao Thức

- **Xgig 1000 32/128G** - Inline, non-intrusive capture, auto negotiation, link training, forward error correction
- **SierraNet M1288** - Fiber channel fabrics

---

## SPAN Port (Switched Port Analyzer)

Tính năng của Cisco, còn gọi là port mirroring. Nếu kẻ tấn công kết nối vào SPAN port, có thể xâm phạm toàn bộ mạng.

---

## Nghe Lén Điện Thoại (Wiretapping)

- Nghe lén chính thức/không chính thức đường dây điện thoại
- Ghi âm cuộc trò chuyện
- Direct line wiretap
- Radio wiretap

**Active tapping** - MITM, chèn hoặc thay đổi dữ liệu

**Passive tapping** - Nghe trộm (snooping/eavesdropping)

---

## Tấn Công DHCP

### DHCP Starvation

Gửi số lượng lớn request đến DHCP server, cạn kiệt pool địa chỉ.

**Công cụ:** Yersinia, dhcpStarvation.py, Metasploit, Hyenae

**Phòng thủ:** Bật port security, DHCP filtering

### Rogue DHCP Server Attack

MITM; đưa DHCP server giả vào mạng, gói tin đến server giả trước; có thể cấp phát IP của kẻ tấn công làm default gateway.

**Công cụ:** mitm6, Ettercap, Gobbler

**Phòng thủ:** Cấu hình kết nối giữa interface và rogue server là untrusted

---

## Kỹ Thuật DNS Poisoning

| Kỹ thuật | Mô tả |
|---------|-------|
| **Intranet DNS Spoofing** | Dùng ARP poisoning |
| **Internet DNS Spoofing** | Rogue DNS với IP tĩnh, gửi trojan thay đổi DNS entry trên máy nạn nhân |
| **Proxy Server DNS Poisoning** | Trojan thay đổi cài đặt proxy trong trình duyệt |
| **DNS Cache Poisoning** | Thay đổi hoặc thêm bản ghi DNS giả vào DNS resolver; SAD DNS attack - chèn DNS có hại vào cache; khai thác side channel, lỗ hổng trong dnsmasq, unbound, BIND |

**Công cụ:** DerpNSpoof, Deserter, PolarDNS, Ettercap, Evilgrade, DNS Goisoner

**Phòng thủ:** DNSSEC, SSL

---

## MAC Flooding

```bash
macof -i eth0
```

## Switch Port Stealing

Gửi gói ARP giả mạo dùng địa chỉ MAC của nạn nhân.

---

## Wireshark - Bộ Lọc

| Bộ lọc | Mô tả |
|--------|-------|
| `tcp.port == 23` | Giám sát cổng cụ thể |
| `ip.addr == 192.168.1.100` | Lọc theo địa chỉ IP |
| `ip.addr == 192.168.1.100 && tcp.port == 23` | Kết hợp IP và cổng |
| `ip.addr == 10.0.0.4 or ip.addr == 10.0.0.5` | Lọc theo nhiều địa chỉ |
| `ip.dst == 10.0.1.50 && frame_len > 400` | IP đích kết hợp kích thước frame |
| `tcp.flags.reset == 1` | Hiển thị tất cả TCP reset |
| `udp.contains` | Lọc giá trị hex |
| `http.request` | Hiển thị tất cả HTTP GET request |
| `tcp.analysis.retransmission` | Hiển thị tất cả retransmission |
| `tcp contains traffic` | Tất cả gói TCP chứa từ "traffic" |
| `!(arp or icmp or dns)` | Ẩn ARP, ICMP, DNS |
| `tcp.port == 4000` | Cổng nguồn hoặc đích 4000 |
| `tcp.port eq 25 or icmp` | Chỉ SMTP và ICMP |
| `ip.src == 192.168.0.0/16 and ip.dst == 192.168.0.0/16` | Lưu lượng LAN nội bộ |

---

## Công Cụ Sniffing

- Capsa Portable Network Analyzer
- OmniPeek
- **Wireshark** - Dùng WinPcap

---

## Biện Pháp Đối Phó Sniffing

- Hạn chế truy cập vật lý vào mạng
- Mã hóa đầu cuối (end-to-end encryption)
- Thêm MAC vào ARP cache tĩnh

---

## Cách Phát Hiện Sniffing

- IDS
- Phát hiện thiết bị chạy ở chế độ promiscuous
- Chạy các công cụ mạng

**Ping method** - Ping với MAC sai

**DNS method** - Lưu lượng mạng tăng; giám sát reverse DNS lookup; gửi ICMP request đến IP không tồn tại

**ARP method** - Gửi ARP non-broadcast đến tất cả node

**Phát hiện chế độ promiscuous:**
```bash
nmap --script=sniffer-detect
# hoặc dùng NetScanToolsPro
```
