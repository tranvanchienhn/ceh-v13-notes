# Dò Quét Mạng (Scanning Networks)

## Giới Thiệu

**Công cụ:** Metasploit, Nmap, Hping3

## Cờ Giao Tiếp TCP

| Cờ | Ý nghĩa |
| --- | ------- |
| SYN | Khởi tạo kết nối TCP, cờ SYN ban đầu |
| ACK | Xác nhận cờ SYN, được đặt trên tất cả segment sau SYN đầu tiên |
| FIN | Đóng kết nối, kết thúc có trật tự |
| RST | Buộc chấm dứt kết nối |
| PSH | Buộc chuyển dữ liệu ngay lập tức |
| URG | Đánh dấu dữ liệu ưu tiên, gửi ngoài dải (out of band) |

**Colasoft Packet Builder** - Công cụ tạo gói tin tùy chỉnh

---

## Hping3

```bash
hping3 -1 0.0.0.0                     # Ping sweep ICMP
hping3 -A 10.10.10.10 -p 80           # Quét ACK trên cổng 80
hping3 -2 0.0.0.0 -p 80               # Quét UDP cổng 80
hping3 10.10.10.10 -Q -p 139          # Thu thập số thứ tự ban đầu
hping3 -S 10.10.10.10 -p 80 --tcp-timestamp
hping3 -8 50-60 -S 10.10.10.10 -V    # SYN scan cổng 50-60
hping3 -F -P -U 10.10.10.10 -p 80    # Quét FIN, PSH và URG
hping3 -1 10.0.1.x --rand-dest -I eth0  # Quét toàn bộ subnet tìm máy đang hoạt động
hping3 -9 HTTP -I eth0                # Bắt lưu lượng chứa chữ ký HTTP
hping3 -S 10.10.1.1 -a 192.168.1.254 -p 22 --flood  # SYN flood nạn nhân
```

### Các Cờ Hping3

| Cờ | Ý nghĩa |
| --- | ------- |
| `-S` | SYN |
| `-A` | ACK |
| `-F` | FIN |
| `-R` | RST |
| `-P` | PSH |
| `-U` | URG |
| `-p` | Cổng |
| `-c` | Số lượng gói tin |
| `-i` | Khoảng thời gian |
| `--flood` | Gửi với tốc độ tối đa |
| `--traceroute` | Traceroute TCP |

---

## Kỹ Thuật Dò Quét Phát Hiện Máy Chủ (Host Discovery)

```bash
nmap -sn                    # Chỉ phát hiện máy chủ, không quét cổng
nmap -PR 192.168.1.0/24     # Quét ping ARP (chỉ dùng nội bộ mạng)
nmap -sn -PU                # Quét ping UDP
nmap -sn -PE                # Quét ping ICMP ECHO
nmap -sn -PP                # Quét ping ICMP Timestamp - lấy thời gian hiện tại của máy
nmap -sn -PM                # Quét ICMP Address Masking - lấy subnet mask
nmap -sn -PS                # Quét TCP SYN - phát hiện máy đang online mà không tạo kết nối
nmap -PA                    # Quét ping TCP ACK - cổng mặc định 80, tăng khả năng vượt firewall
nmap -sn -PO                # Quét ping giao thức IP - bất kỳ phản hồi nào cho thấy máy đang online
```

### Công Cụ Ping Sweep

- Angry IP Scanner
- SolarWinds Engineer's Toolset
- NetScanTools Pro
- Colasoft Ping Tool
- Advanced IP Scanner
- OpUtils

---

## Phát Hiện Cổng và Dịch Vụ

> **PHẢI BIẾT**

### Phân Loại Cổng

| Loại | Dải |
|------|-----|
| Cổng nổi tiếng (Well-known) | 0 – 1.023 |
| Cổng đã đăng ký (Registered) | 1.024 – 49.141 |
| Cổng động (Dynamic) | 49.152 – 65.535 |

### Cổng Quan Trọng

```
20/21  FTP
22     SSH
23     Telnet
25     SMTP
53     DNS
67/68  DHCP
69     TFTP
80     HTTP
110    POP3
123    NTP
135    RPC
137-139 NetBIOS
143    IMAP
161    SNMP
389    LDAP
443    HTTPS
445    SMB
500    ISAKMP
514    Syslog
1433   MSSQL
3306   MySQL
3389   RDP
5060   SIP
```

---

## Các Kiểu Dò Quét Cổng

**TCP Connect / Full open scan** - Nếu cổng mở, bắt tay thành công; nếu đóng nhận RST.
```bash
nmap -sT -v
```

**Stealth scan (Half-open scan)** - Cắt đứt kết nối TCP đột ngột, không hoàn thành bắt tay.
```bash
nmap -sS   # Ẩn hơn so với -sT
```

**Inverse TCP scan** - Gửi tổ hợp cờ TCP không chuẩn để tránh IDS (không hiệu quả với Windows, dùng cho Unix).

- **XMAS scan:** Bật cờ FIN, URG và PSH.
```bash
nmap -sX   # (-sF cho FIN scan, -sN cho NULL scan)
```

- **TCP Maimon:** Gửi cờ FIN và ACK; nếu cổng đóng sẽ phản hồi RST.
```bash
nmap -sM
```

- **ACK flag probe scan:** Gửi cờ ACK và kiểm tra TTL; nếu RST nhỏ hơn 64 byte thì cổng mở.
```bash
nmap --ttl [time] [target]
```

- **IDLE/IPID scan ⚠️ QUAN TRỌNG** - Gửi địa chỉ nguồn giả mạo đến máy tính. Sử dụng máy zombie của bên thứ ba.
```bash
nmap -sI [zombieIP] [targetIP]
```

- **UDP scan** - Gửi datagram đến cổng; nếu đóng có phản hồi, không có phản hồi thì có thể mở.

- **SSDP scan** - Quét UPnP.

```bash
nmap -sS -A -f 172.17.15.12   # Phân mảnh SYN scan kết hợp nhận diện OS
```

---

## Bảng Phản Hồi Khi Dò Quét

| Cờ | Mở | Đóng | Bị lọc (Filtered) | Không lọc (Unfiltered) |
| --- | --- | ---- | ----------------- | ---------------------- |
| -sT | SYN-ACK | RST | Không phản hồi/ICMP unreachable | |
| -sS | SYN-ACK | RST | Không phản hồi/ICMP unreachable | |
| -sA | | | Không phản hồi | RST |
| -sI | IPID tăng (RST gửi bởi zombie) | IPID không đổi | Thay đổi bất thường | |
| -sU | Không phản hồi | ICMP unreachable type 3 code 3 | ICMP unreachable khác/không phản hồi | |
| -sN | Không phản hồi | RST | ICMP | |
| -sF | Không phản hồi | RST | ICMP | |
| -sX | Không phản hồi | RST | ICMP | |
| -sM | Không phản hồi | RST | ICMP | |

---

## Bảng Tổng Hợp Các Kiểu Quét Nmap

| Cờ | Mô tả |
| --- | ----- |
| `-sA` | Quét ACK |
| `-sF` | Quét FIN |
| `-sI` | Quét IDLE (Zombie) |
| `-sL` | Quét danh sách (chỉ phân giải DNS) |
| `-sN` | Quét NULL |
| `-sO` | Quét giao thức |
| `-sP` | Quét ping *(cũ, nay dùng -sn)* |
| `-sR` | Quét RPC |
| `-sS` | Quét SYN (Stealth/Half-open) |
| `-sT` | Quét TCP Connect |
| `-sW` | Quét Window |
| `-sX` | Quét XMAS |

## Phát Hiện Máy Chủ (Kiểu Ping)

| Cờ | Mô tả |
| --- | ----- |
| `-PI` | Ping ICMP Echo |
| `-PS` | Ping TCP SYN |
| `-PT` | Ping TCP |
| `-PO` | Ping giao thức IP (không dùng TCP/UDP/ICMP) |

## Tùy Chọn Xuất Kết Quả

| Cờ | Mô tả |
| --- | ----- |
| `-oN` | Xuất ra văn bản thông thường |
| `-oX` | Xuất ra XML |

## Mẫu Thời Gian (Timing Templates)

| Cờ | Mô tả |
| --- | ----- |
| `-T0` | Tuần tự - chậm nhất, "hoang tưởng" |
| `-T1` | Tuần tự - rất chậm |
| `-T2` | Tuần tự - lịch sự |
| `-T3` | Song song - tốc độ bình thường |
| `-T4` | Song song - nhanh |
| `-T5` | Song song - tốc độ cực cao |

---

## Các Cờ Chế Độ và Quét Của Hping3

| Cờ | Mô tả |
| --- | ----- |
| `-1` | Chế độ ICMP. Ví dụ: `hping3 -1 172.17.15.12` |
| `-2` | Chế độ UDP. Ví dụ: `hping3 -2 192.168.12.55 -p 80` |
| `-8` | Chế độ quét cổng. Nhận đơn lẻ, dải hoặc `all`. Ví dụ: `hping3 -8 20-100` |
| `-9` | Chế độ lắng nghe theo chữ ký. Ví dụ: `hping3 -9 HTTP -I eth0` |
| `--flood` | Gửi gói tin nhanh nhất có thể (không hiển thị phản hồi). Dùng cho mô phỏng DoS. |
| `-Q` | Thu thập số thứ tự TCP để phân tích khả năng dự đoán. |

## Cờ TCP của Hping3

| Cờ | Mô tả |
| --- | ----- |
| `-F` | Đặt cờ FIN |
| `-S` | Đặt cờ SYN |
| `-R` | Đặt cờ RST |
| `-P` | Đặt cờ PSH |
| `-A` | Đặt cờ ACK |
| `-U` | Đặt cờ URG |
| `-X` | Đặt cờ XMAS (FIN + PSH + URG) |

**Công cụ giả mạo gói tin:** Hping, Scapy, Komodia, Ettercap, Cain.

---

## Phát Hiện Phiên Bản Dịch Vụ

```bash
nmap -sV
```

---

## Nhận Diện OS / Banner Grabbing

**Banner grabbing chủ động**

**Nhận diện OS theo TTL:**

| Hệ điều hành | TTL mặc định |
|-------------|-------------|
| Linux | 64 |
| Windows | 128 |
| FreeBSD | 64 |
| OpenBSD | 255 |
| Cisco | 255 |
| Solaris | 255 |
| AIX | 255 |

```bash
nmap -O [target]                    # Nhận diện OS
nmap --script [script] [target]     # Dùng Nmap Script Engine
nmap -sC [target]                   # Dùng script mặc định
nmap -6 -O 69.69.69.69              # Quét IPv6
```

---

## Dò Quét Vượt Qua Firewall

**Kỹ thuật:**
- Phân mảnh gói tin (Packet Fragmentation)
- Source Routing
- Thay đổi cổng nguồn (Source Port Manipulation)
- Giả mạo địa chỉ IP (IP Address Decoy)
- IP Address Spoofing
- MAC Address Spoofing
- Tạo gói tin tùy chỉnh (Custom Packets)
- Ngẫu nhiên hóa thứ tự máy chủ (Randomizing Host Order)
- Gửi checksum sai (Bad Checksums)
- Proxy Server
- Anonymizer

```bash
nmap -sS -t4 -A -f -v              # Phân mảnh SYN scan

# Giả mạo địa chỉ IP với Hping3:
hping3 www.certifiedhacker.com -a 7.7.7.7
```
