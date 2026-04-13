# Liệt Kê (Enumeration)

## Các Kỹ Thuật Liệt Kê

- Trích xuất tên người dùng qua địa chỉ email
- Mật khẩu mặc định (default password)
- Brute force Active Directory
- DNS zone transfer - `dig`
- Trích xuất nhóm người dùng từ Windows
- Trích xuất tên người dùng qua SNMP
- Trích xuất tài nguyên mạng và cấu trúc liên kết qua SNMP

---

## Dịch Vụ và Cổng Cần Liệt Kê

| Cổng | Dịch vụ |
|------|---------|
| TCP/UDP 53 | DNS Zone Transfer |
| TCP/UDP 134 | MS RPC Endpoint Mapper |
| UDP 137 | NetBIOS Name Service (NBNS) |
| TCP 139 | NetBIOS Session Service (SMB over NetBIOS) |
| TCP/UDP 445 | SMB over TCP (Direct Host) - máy in, chia sẻ file |
| UDP 161 | SNMP |
| TCP/UDP 389 | LDAP |
| TCP 2049 | NFS (Network File System) |
| TCP 25 | SMTP |
| TCP/UDP 162 | SNMP Trap |
| UDP 500 | ISAKMP / IKE |
| TCP 22 | SSH / SFTP |
| TCP/UDP 3268 | Global Catalog Service |
| TCP/UDP 5060, 5061 | SIP (Session Initiation Protocol) |
| TCP 20/21 | FTP |
| TCP 23 | Telnet |
| UDP 69 | TFTP |
| TCP 179 | BGP |

---

## Liệt Kê NetBIOS

**Cổng:**
- 137 UDP - Name service
- 138 UDP - Datagram service
- 139 TCP - Session service

> ⚠️ Không hoạt động trên IPv6

### Bảng Mã NetBIOS

| Tên | Mã NetBIOS | Loại | Thông tin thu được |
| --- | ---------- | ----- | ------------------ |
| hostname | `<00>` | UNIQUE | Tên máy chủ |
| domain | `<00>` | Group | Tên miền |
| hostname | `<03>` | UNIQUE | Dịch vụ Messenger |
| username | `<03>` | UNIQUE | Dịch vụ Messenger của người dùng đang đăng nhập |
| hostname | `<20>` | UNIQUE | Server service đang chạy |
| domain | `1B` | UNIQUE | Tên Domain Master Browser |
| domain | `1E` | Group | Bầu chọn Browser service |

**Lệnh:**
```bash
nbtstat -m              # Bảng NetBIOS cục bộ
nbtstat -A 10.10.10.10  # Bảng NetBIOS hệ thống từ xa
nbtstat -c              # Cache từ xa
```

**Công cụ:**
- `PsExec` - Liệt kê tài khoản người dùng
- `PsFile`

**Liệt kê tài nguyên chia sẻ:**
```bash
net view \\computername
net view \\domain
```

---

## Liệt Kê SNMP

**Cổng:**
- UDP 161 - SNMP Agent
- UDP 162 - SNMP Trap

**Phiên bản:**
- v1 - Không bảo mật, chuỗi bảo mật dạng plaintext
- v2c - Không bảo mật nhưng nhanh hơn, vẫn dùng plaintext
- v3 - Xác thực + mã hóa, an toàn và được khuyến nghị

**Công cụ:**
- SNMPCheck
- Engineers Toolset
- SNMP Scanner
- OpUtils 5
- SNScan
- SoftPerfect Network Scanner

```bash
snmpwalk -v1 -c public    # Xem tất cả OID
snmp-check
```

---

## MIB (Management Information Base)

| MIB | Chức năng |
|-----|-----------|
| **DHCP.MIB** | Giám sát lưu lượng mạng giữa các máy chủ DHCP |
| **HOSTMIB.MIB** | Giám sát và quản lý tài nguyên máy chủ |
| **LNMIB2.MIB** | Chứa các kiểu đối tượng cho dịch vụ workstation và server |
| **MIB_II.MIB** | Quản lý internet dựa trên TCP/IP |
| **WINS.MIB** | Dành cho Windows Internet Name Service (WINS) |

- 🟢 **MIB-II** = Mạng TCP/IP
- 🟡 **HOSTMIB** = Thống kê phần cứng và hệ thống
- 🔵 **LMMIB2** = Dịch vụ Windows LAN Manager
- 🟣 **WINS.MIB** = Cơ sở dữ liệu tên NetBIOS
- 🟠 **DHCP.MIB** = Chỉ dành cho dịch vụ DHCP

---

## Liệt Kê LDAP (Lightweight Directory Access Protocol)

**Cổng:**
- TCP 389 - LDAP
- TCP 636 - Secure LDAP

**Công cụ:**
- `ldapsearch`
- AD Explorer
- Softerra LDAP Administrator
- Nmap LDAP brute NSE script

---

## Liệt Kê NTP và NFS

**NTP - Cổng:** UDP 123

**Công cụ NTP:** ntptrace, ntpdc, ntpq

**NFS - Cổng:** 2049

**Công cụ NFS:**
```bash
rpcinfo -p      # Kiểm tra cổng mở
showmount
rpc-scan
SuperEnum
```

---

## Liệt Kê SMTP và DNS

**Cổng SMTP:** TCP 25

**Lệnh SMTP qua Telnet:**
```bash
SMTP VRFY      # Kiểm tra địa chỉ người dùng có tồn tại
SMTP EXPN      # Mở rộng danh sách gửi thư thành các địa chỉ riêng lẻ
SMTP RCPT TO   # Chỉ định người nhận thư
telnet <email_server>
```

**Công cụ SMTP:** Nmap, Metasploit, NetScanTools Pro, smtp-user-enum

**Liệt kê DNS qua Zone Transfer - UDP 53:**
```bash
dig ns                           # Lấy tất cả name server
nslookup                         # Windows: name server, mail,...
DNSRecon -t axfr -d [domain]
```

**DNS Cache Snooping:**
```bash
dig +norecursive    # Phương pháp non-recursive: phản hồi với root.hints
# Phương pháp recursive: kiểm tra giá trị TTL
```

**DNSSEC Zone Walking** - Liệt kê vùng DNSSEC

**Công cụ:** LDNS, DNSRecon, Knock, Raccoon, Turbolist3r, OWASP AMASS

```bash
amass enum -d [domain]
```

---

## Liệt Kê IPSec

```bash
nmap -sU -p 500
ike-scan -M
```

## Liệt Kê VoIP

- `svmap`

## Liệt Kê RPC

```bash
nmap -sR
nmap -T4 -A
```

## Liệt Kê Người Dùng Unix/Linux

```bash
rusers -a -l -u -i
rwho -a
finger -s
```

## Liệt Kê SMB

```bash
nmap -p 445 -A
nmap -p 445 --script smb-protocols
nmap -p 139 --script smb-protocols
```
