# Tấn Công Hệ Thống (System Hacking)

## Giành Quyền Truy Cập (Gaining Access)

### Windows - Lưu Trữ Mật Khẩu

**Cách Windows lưu mật khẩu trong SAM:**

Windows dùng Security Accounts Manager (SAM) hoặc cơ sở dữ liệu AD để quản lý tài khoản và mật khẩu ở dạng băm một chiều (one-way hash). SAM không lưu mật khẩu dạng plaintext. File SAM được dùng như một file registry cơ sở dữ liệu và không thể sao chép sang nơi khác trong khi Windows đang chạy. SAM dùng hàm SYSKEY để mã hóa một phần các hash mật khẩu.

**Vị trí:** `%SystemRoot%\system32\config\SAM` → trong registry: `HKEY_LOCAL_MACHINE\SAM`

SAM lưu mật khẩu dạng hash LM hoặc NTLM.

**NTLM Authentication (NTLMv2 khá an toàn nhưng vẫn yếu hơn Kerberos):**
- Là scheme xác thực mặc định
- Không dựa trên bất kỳ thông số giao thức chính thức nào
- Vista và các phiên bản Windows sau đã vô hiệu hóa LM hashing
- Giá trị LM hash để trống trong các phiên bản Windows mới hơn

**Công cụ trích xuất hash mật khẩu:**
- `pwdump7` - Chính
- Mimikatz
- DSinternals
- hashcat
- PyCrack

**Quy trình NTLM:** Client gửi yêu cầu → Server gửi challenge → Client tính response → Server xác minh (AD hoặc SAM)

---

### Kerberos Authentication

- Dùng mật mã khóa bí mật (secret key cryptography)
- **KDC** - Key Distribution Center
- **AS** - Authentication Server (Máy chủ xác thực)
- **TGS** - Ticket Granting Server (Máy chủ cấp vé)
- Nâng cấp so với NTLM

**Quy trình Kerberos:** Đăng nhập và yêu cầu vé → Nhận Ticket-Granting Ticket (TGT) → Yêu cầu truy cập dịch vụ → Nhận Service Ticket → Truy cập dịch vụ

---

### Các Tùy Chọn Bẻ Khóa Thông Tin Đăng Nhập

- Dump thông tin xác thực từ bộ nhớ
- Đánh cắp bản sao cục bộ của database SAM
- Đánh cắp file `ntds.dit` của AD
- Trích xuất khóa boot SYSKEY
- Chặn thông tin xác thực trên mạng:
  - Passive sniffing
  - MITM
  - Mật khẩu plaintext
  - LM, NTLM, NTLMv2, Kerberos

**Brute force dịch vụ mạng:**
- Logon/SMB/File and print server (TCP 139, 445)
- Web server (80, 443)
- MS Exchange (TCP 25, 110, 143)
- MSSQL (1433)

**Brute force điều khiển từ xa:**
- RDP (TCP 3389)
- Telnet (23)

---

## Bẻ Khóa Mật Khẩu (Password Cracking)

### Tấn Công Phi Điện Tử (Non-Electronic)
- Social engineering, dumpster diving,...

### Tấn Công Online Chủ Động (Active Online)
- Đoán mật khẩu, dictionary attack, brute force, password spraying, mask attack, hash injection, LLMNR/NBT-NS poisoning, trojan/spyware/keylogger, internal monologue attack, markov-chain,...

**Chi tiết:**

- **Dictionary attack** - File từ điển được nạp vào ứng dụng bẻ khóa chạy dựa trên tài khoản người dùng
- **Brute force attack** - Phần mềm thử mọi tổ hợp cho đến khi bẻ được mật khẩu
- **Rule-based attack** - Khi có một số thông tin về mật khẩu. Hybrid: dựa trên dictionary dùng mật khẩu cũ. Syllable attack: kết hợp từ điển và các phương pháp khác. Password spraying nhắm nhiều tài khoản cùng lúc.
- **Hash injection / Pass the Hash (PtH)** - Chèn hash bị xâm phạm vào phiên cục bộ để xác thực tài nguyên mạng, dùng hash của người dùng đang đăng nhập để đăng nhập vào domain controller
- **LLMNR/NBT-NS poisoning** - Hai thành phần chính của Windows dùng để phân giải tên; công cụ: **Responder** (Link-local Multicast Name Resolution + NetBIOS Name Service)
- **Bẻ khóa Kerberos** - AS-REP Roasting nhắm vào người dùng không bật Kerberos pre-authentication; TGT được trích xuất để lấy mật khẩu
- **Kerberoasting** - Thu và bẻ hash tài khoản service, nhằm truy cập tài khoản đặc quyền cao và di chuyển ngang; công cụ: hashcat
- **Pass the Ticket** - Xác thực mà không cần mật khẩu bằng cách dùng công cụ credential dumping để đánh cắp ST/TGT; công cụ: Mimikatz
- **NTLM Relay attack** - Chặn và chuyển tiếp yêu cầu xác thực NTLM; công cụ: Responder và ntlmrelayx

### Tấn Công Online Thụ Động (Passive Online)
Không thay đổi hệ thống theo bất kỳ cách nào, thu thập mật khẩu bằng cách thụ động giám sát luồng dữ liệu.

### Tấn Công Offline
Khôi phục mật khẩu từ dump hash.

---

### Công Cụ Password Spraying

```bash
hydra -l [login] -p [password] -L [logins] -P [passwords]
```

Công cụ khác: Metasploit, Rubeus, adfsbrute, CrackMapExec

**Mask attack** - Brute-force khôi phục mật khẩu từ hash dựa trên mẫu mật khẩu.
```bash
hashcat -m [hash_mode]    # Ví dụ: MD5
```

**Tra cứu mật khẩu mặc định:** fortypoundhead.com, cirt.net

**Internal monologue attack** - Dùng Security Support Provider Interface (SSPI) từ ứng dụng user mode để tính NetNTLM response.

---

### Chi Tiết Bẻ Khóa Kerberos

| Kỹ thuật | Mô tả |
|----------|-------|
| **AS-REP Roasting** | Bẻ TGT; cần kết nối DC và tài khoản domain; chỉ áp dụng với tài khoản không yêu cầu Kerberos pre-authentication |
| **Kerberoasting** | Bẻ TGS; công cụ: RUBEUS |
| **Pass the Ticket** | Dùng vé Kerberos không cần mật khẩu; công cụ: Mimikatz |
| **NTLM Relay** | Chặn yêu cầu NTLM; `responder -I eth0` |
| **Fingerprint attack** | Bẻ mật khẩu từng ký tự 'p', 'a', 's'... |
| **PRINCE attack** | Dùng chuỗi các từ kết hợp |
| **Markov chain attack** | Chia mật khẩu thành các âm tiết 2-3 ký tự để tạo bảng chữ cái mới |

**Tấn công thụ động:** Wire sniffing tại tầng data link.

**Tấn công online:**
- **Rainbow table attack** - Dùng thông tin đã tính sẵn trong bộ nhớ để bẻ mã hóa; công cụ: rtgen
- **Distributed Network Attack** - Dùng nhiều máy trong mạng; công cụ: Exterro Password Recovery Toolkit

---

### Công Cụ Bẻ Khóa

**Khôi phục mật khẩu:** Elcomsoft Distributed Password Recovery, Passware Kit, hashcat, PCUnlocker, Lazersoft, Passper WinSenior

**Bẻ khóa mật khẩu:** L0phtCrack, THC-Hydra, RainbowCrack

**Password salting** - Thêm chuỗi ký tự ngẫu nhiên trước khi tính hash

**Phát hiện LLMNR/NBT-NS poisoning:** Vindicate, Respounder, got-responded

---

## Khai Thác Lỗ Hổng (Vulnerability Exploitation)

**Các bước:**
1. Xác định lỗ hổng
2. Xác định rủi ro liên quan
3. Xác định khả năng khai thác
4. Phát triển exploit
5. Chọn phương thức phân phối - cục bộ hoặc từ xa
6. Tạo và phân phối payload
7. Giành quyền truy cập từ xa

**Nguồn exploit:**
- ExploitDB, VulnDB, OSV.dev (dự án mã nguồn mở), MITRE CVE, Windows Exploit Suggester (WES-NG)

---

### Metasploit

**Exploit module:**
1. Cấu hình exploit chủ động
2. Xác minh các tùy chọn exploit
3. Chọn mục tiêu
4. Chọn payload
5. Khởi chạy exploit

**Payload module:**
1. Thiết lập kênh liên lạc giữa Metasploit và nạn nhân
2. Kết hợp mã tùy ý được thực thi sau khi exploit thành công
3. Lệnh: `msf payload`

Các loại:
- **Singles** - Độc lập và hoàn toàn khép kín
- **Stagers** - Thiết lập kết nối mạng giữa kẻ tấn công và nạn nhân
- **Stages** - Được tải xuống bởi các module Stager

**Auxiliary module:**
- Dùng cho các tác vụ một lần: dò quét cổng, DoS, fuzzing
- `show auxiliary` - Liệt kê tất cả module
- `exploit` / `run` - Khởi chạy

**NOPS module:**
- Tạo lệnh no-operation để chặn buffer
- `msf generate -t c 50` - Tạo NOP sled 50 byte

**Encoder module:**
- Ẩn/mã hóa payload để tránh bị phát hiện bởi AV, IDS
- Obfuscation, vượt qua phát hiện signature, polymorphism

**Evasion module:**
- Thay đổi đặc tính payload/exploit để né tránh phát hiện
- `evasion/windows/windows_defender.exe`
- `evasion/windows/antivirus_disable`

**Post-exploitation module:**
- Dùng sau khi xâm phạm hệ thống thành công
- `post/windows/gather/enum_logged_on_users`
- `post/linux/gather/enum_configs`
- `post/windows/manage/portproxy`

**Công cụ AI:** Nebula, DeepExploit (tích hợp Metasploit)

---

## Buffer Overflow (Tràn Bộ Đệm)

Buffer là vùng bộ nhớ liền kề được cấp phát cho chương trình để xử lý dữ liệu runtime. Buffer overflow xảy ra khi chương trình chấp nhận nhiều dữ liệu hơn bộ đệm được cấp phát, cho phép ghi đè vùng bộ nhớ lân cận.

**Khai thác để:** làm hỏng file, sửa đổi dữ liệu, truy cập thông tin nhạy cảm, leo thang đặc quyền, giành shell access.

**Chương trình dễ bị buffer overflow khi:**
- Không thực hiện kiểm tra ranh giới (boundary check)
- Dùng phiên bản ngôn ngữ lập trình cũ
- Dùng hàm không an toàn để kiểm tra kích thước buffer
- Thiếu quy tắc lập trình tốt
- Thiếu lọc và kiểm tra đầu vào
- Thực thi mã trong stack segment
- Cấp phát và vệ sinh bộ nhớ không đúng cách

### Các Loại Buffer Overflow

**Stack-based overflow** - Stack dùng cho cấp phát bộ nhớ tĩnh (LIFO):
- PUSH lưu dữ liệu, POP lấy dữ liệu
- Kẻ tấn công kiểm soát thanh ghi EIP để thay thế địa chỉ return và giành shell access

Các thanh ghi Stack:
- **EBP** - Extended Base Pointer (StackBase)
- **ESP** - Extended Stack Pointer
- **EIP** - Extended Instruction Pointer - lưu địa chỉ lệnh tiếp theo
- **ESI** - Extended Source Index
- **EDI** - Extended Destination Index

**Heap-based overflow** - Heap được cấp phát động tại runtime; dẫn đến ghi đè object pointer; không nhất quán và có kỹ thuật khai thác khác nhau.

### Buffer Overflow trên Windows

**Spiking** - Tạo gói TCP/UDP thủ công:
```bash
nc -nv [ip] [port]
generic_send_tcp [ip] [port] [spike_script] SKIPVAR SKIPSTR
```

**Fuzzing** - Gửi lượng lớn dữ liệu để ghi đè thanh ghi EIP:
```bash
# Script while loop Python gửi dữ liệu lớn
# Dùng pattern_create của Ruby để tạo byte ngẫu nhiên
# Dùng pattern_offset của Metasploit để tìm vị trí EIP
nc -nvlp 4444
```

**Xác định offset** - Dùng `pattern_create` và `pattern_offset` của Metasploit để tìm vị trí EIP bị ghi đè.

**Return Oriented Programming (ROP)** - Tái sử dụng các đoạn code đã tồn tại trong chương trình (thường trong libc hoặc kernel32.dll).

**Heap spraying** - Làm ngập vùng nhớ trống của tiến trình bằng nhiều bản sao mã độc.

**JIT spraying** - Thực thi mã tùy ý thông qua tính năng JIT compilation trong trình duyệt hiện đại; dùng JavaScript chứa payload độc hại.

**Exploit chaining** - Kết hợp nhiều exploit và lỗ hổng.

---

## AD Domain Mapping - BloodHound

Ứng dụng web JS để lập bản đồ Active Directory.

**Post AD Enumeration dùng PowerView:**

| Lệnh | Mô tả |
|------|-------|
| `Get-ADomain` / `Get-NetDomain` | Lấy thông tin domain hiện tại bao gồm các DC |
| `Get-DomainSID` | Lấy Security ID |
| `Get-DomainPolicy` | Lấy thông tin cấu hình chính sách domain |
| `(Get-DomainPolicy)."SystemAccess"` | Cấu hình chính sách system access |
| `(Get-DomainPolicy)."kerberospolicy"` | Chính sách Kerberos của domain |
| `Get-NetDomainController` | Thông tin về domain controller hiện tại |
| `Get-NetUser` | Thông tin về người dùng hiện tại |
| `Get-NetLoggedon -ComputerName` | Người dùng domain đang active |
| `Get-UserProperty -Properties pwdlastset` | Ngày giờ đổi mật khẩu lần cuối |
| `Find-LocalAdminAccess` / `Invoke-EnumerateLocalAdmin` | Người dùng có quyền admin cục bộ |
| `Computer-NetSession -ComputerName` | Người dùng đang đăng nhập trên máy |

**Liệt kê domain trust forests:**
- **One-way trust** - Một chiều; người dùng trong trusted domain truy cập tài nguyên của trusting domain
- **Two-way trust** - Hai chiều; người dùng có thể truy cập tài nguyên qua lại

**Xác định điểm yếu dùng GhostPack Seatbelt** - Thu thập thông tin PowerShell, vé Kerberos, các mục trong RecycleBin.

---

### Công Cụ Phát Hiện Buffer Overflow

OllyDbg, Veracode, Flawfinder, Kiuwan, Splint, Valgrind

---

## Leo Thang Đặc Quyền (Privilege Escalation)

**Leo thang ngang (Horizontal)** - Truy cập tài nguyên thuộc về người dùng có cùng cấp quyền nhưng ở vị trí lẽ ra bị bảo vệ.

**Leo thang dọc (Vertical)** - Người dùng không được phép cố truy cập tài khoản có quyền cao hơn; thực thi mã ở mức đặc quyền cao hơn.

### Kỹ Thuật Leo Thang Đặc Quyền

- **DLL hijacking** - Đặt DLL độc hại vào thư mục thư viện của ứng dụng; công cụ: Spartacus
- **Khai thác lỗ hổng** - ExploitDB
- **Dylib hijacking** - Tấn công thư viện động; macOS; công cụ: Dylib Hijack Scanner
- **Spectre** - Tìm thấy trong bộ xử lý AMD, Apple, ARM, Intel; khai thác speculative execution để đọc dữ liệu bị hạn chế
- **Meltdown** - Tất cả ARM, Intel triển khai bởi Apple; đánh lừa processor truy cập bộ nhớ ngoài phạm vi
- **Named Pipe Impersonation** - Trong Windows; Metasploit thực hiện kỹ thuật này
- **Unquoted service paths** - Đường dẫn service không được đặt trong dấu ngoặc kép; kẻ tấn công khai thác để nâng quyền
- **Service object permissions** - Quyền dịch vụ cấu hình sai; thường là zero-day
- **Pivoting** - Vượt firewall thông qua hệ thống đã bị xâm phạm để truy cập hệ thống khác
- **NFS cấu hình sai** - Cổng 2049 dùng RPC:

```bash
nmap -sV [ip]
sudo apt-get install nfs-common
showmount -e
mkdir /tmp/nfs
sudo mount -t nfs [source] /tmp/nfs
```

- **Vượt qua UAC (User Account Control)** - Metasploit, memory injection, FodHelper registry key, eventvwr registry key, COM handler hijacking
- **Lạm dụng bước khởi động/đăng nhập:**
  - Logon script (Windows): `HKEY_CURRENT_USER\environment\userinitMPRLogonScript`
  - Logon script (macOS): login hooks
  - Network logon script: thông qua AD GPO
  - RC scripts (Unix): rc.common, rc.local
  - StartupItems: `/Library/Startupitems` (macOS)

- **Sửa đổi Domain Policy:**
  - Group Policy modification: `ScheduledTasks.xml` dùng script `New-GPOImmediateTask`
  - Domain trust modification: tiện ích `domain_trusts`

- **DCSync attack** - Kẻ tấn công có tài khoản với quyền replication domain, tạo DC ảo để lấy NTLM hash, thực hiện Golden Ticket attack:
```bash
mimikatz "lsadump::dcsync /domain:[domain] /user:Administrator"
```

- **Lạm dụng ADCS (Active Directory Certificate Services)** - Công cụ: Certipy

**Các kỹ thuật khác:**
- Access Token Manipulation
- Parent PID Spoofing (svchost.exe hoặc consent.exe)
- Application shimming - vượt UAC và chèn DLL độc hại
- Filesystem permission weakness
- Path interception
- Lạm dụng accessibility features
- SID-History Injection
- COM hijacking
- Scheduled tasks (Windows: Task Scheduler; Linux: cron/crond)
- Launch Daemon (macOS: launchd)
- Plist modification (macOS)
- Setuid và Setgid (Linux, macOS)
- Web shell
- Lạm dụng sudo rights
- Lạm dụng SUID/SGID permissions (Unix)
- Lạm dụng đường dẫn '.'
- Lạm dụng cơ chế nâng quyền macOS
- Process injection qua Ptrace system call
- Lạm dụng Microsoft Installer (MSI)
- Lạm dụng Windows Filtering Platform (WFP) - NoFilter attack

**Công cụ leo thang đặc quyền:** BeRoot, pwncat, PowerSploit, Traitor, PEASS-ng, FullPowers

**Phòng thủ DLL/Dylib Hijacking:** Dependency Walker, Dylib Hijack Scanner

---

## Duy Trì Truy Cập (Maintaining Access)

- **Backdoor** - Từ chối hoặc gián đoạn hoạt động, truy cập trái phép vào tài nguyên hệ thống
- **Cracker** - Bẻ code hoặc mật khẩu
- **Keylogger**
- **Spyware** - Chụp màn hình và nhiều thứ khác

### Kỹ Thuật Thực Thi Mã Từ Xa

- **Khai thác phía client:** Trình duyệt web (spear phishing), MS Office, ứng dụng bên thứ ba
- **Service execution**
- **WMI (Windows Management Instrumentation)**
- **WinRM (Windows Remote Management)**
- Dameware Remote Support, Ninja, Pupy, PDQ Deploy, ManageEngine Endpoint Central, PsExec

### Keylogger

**Công cụ Windows:** REFOG, All In One Keylogger, Revealer Keylogger, NetBull, Spytector

**Công cụ macOS:** Hoverwatch

Metasploit có thể tạo keylogger từ xa.

### Spyware

**Phân loại:** Desktop spyware, email spyware, internet spyware, child monitoring spyware, screen capturing spyware, USB spyware, audio spyware (theOneSpy, Snooper), video spyware (iSpy, Perfect IP Camera Viewer), print spyware, cellphone spyware (mSpy, XNSPY, iKeyMonitor, ONESPY, Highster Mobile)

---

### Rootkit

- **Hypervisor level rootkit**
- **Hardware/Firmware rootkit**
- **Kernel level rootkit**
- **Boot-loader rootkit**
- **Application level rootkit**
- **Library-level rootkit**
- **Memory rootkit**

**Rootkit nổi tiếng:** FudModule Rootkit, Fire Chili Rootkit (log4shell)

**Phát hiện rootkit:** Phát hiện dựa trên tính toàn vẹn (Tripwire, AIDE), phân tích memory dump

**Công cụ chống rootkit:** GMER, Stinger, Avast One, TDSSKiller

---

### NTFS ADS (Alternate Data Streams)

ADS (Luồng Dữ Liệu Thay Thế) - luồng ẩn của Windows, cho phép fork dữ liệu vào file hiện có và chèn mã độc.

**Công cụ phát hiện NTFS Stream:** Stream Armor, GMER, ADS Scanner, Stream, AlternateStreamView

---

### Steganography (Ẩn Thông Tin)

Ẩn thông điệp bí mật trong thông điệp thông thường, sử dụng ảnh làm vỏ bọc.

**Công cụ:**
- Text: snow
- Ảnh: OpenStego, StegOnline, Coagula, SSuite Picsel, CryptaPix
- Video: OmniHide Pro
- Audio: DeepSound
- Tài liệu: StegoStick, StegJ, Office XML, SNOW, Data Stash
- Email: Spam Mimic
- Folder: GillSoft File Lock Pro

**Công cụ phát hiện steganography:** zsteg, StegoVeritas, Stegextract, StegoHunt, Stenography Studio, Virtual Stenographic Laboratory

---

### Duy Trì Truy Cập Qua Kerberos

- **Skeleton Key attack** - Chèn thông tin xác thực giả, virus resident trong bộ nhớ; công cụ: Mimikatz
- **Golden Ticket attack** - Post-exploitation, giả mạo TGT bằng cách xâm phạm tài khoản Key Distribution Service
- **Silver Ticket attack** - Đánh cắp thông tin xác thực người dùng và tạo vé TGS giả; công cụ: Mimikatz
- **AdminSDHolder** - Bảo vệ tài khoản/nhóm có đặc quyền cao; kẻ tấn công có thể lạm dụng quá trình SDProp
- **WMI Event Subscription** - Duy trì persistence; công cụ: PowerLurk
- **Overpass the Hash (OPtH)** - Mở rộng của Pass-the-Ticket và Pass-the-Hash; công cụ: Mimikatz

---

## Che Giấu Dấu Vết (Hiding Evidence of Compromise)

1. Vô hiệu hóa kiểm toán (auditing)
2. Xóa log - Metasploit Meterpreter
3. Thao túng log
4. Che giấu dấu vết trên mạng/OS
5. Xóa file / ẩn artifact
6. Vô hiệu hóa chức năng Windows

```bash
cipher.exe    # Xóa file an toàn (overwrite không thể khôi phục)
```
