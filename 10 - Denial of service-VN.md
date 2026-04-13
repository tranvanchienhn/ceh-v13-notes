# Từ Chối Dịch Vụ (Denial of Service - DoS/DDoS)

## Botnet

**Tìm kiếm máy dễ bị tấn công:**
- **Random scanning** - Quét ngẫu nhiên tìm lỗ hổng
- **Hit-list scanning** - Thu thập danh sách máy tiềm năng rồi tạo đội quân zombie
- **Topological scanning** - Dùng thông tin từ máy đã lây nhiễm để tìm máy mới
- **Local subnet scanning** - Quét trong subnet cục bộ
- **Permutation scanning** - Dùng danh sách hoán vị giả ngẫu nhiên (pseudo-random permutation) của tất cả địa chỉ IP; block cipher 32-bit và khóa được chọn trước

**Phát tán mã độc:**
- **Central source propagation** - Kẻ tấn công đặt toolkit tại nguồn trung tâm; nguồn → khai thác → sao chép code → lặp lại
- **Back-chaining propagation** - Dùng TFTP, từ máy của kẻ tấn công
- **Autonomous propagation** - Máy tấn công tự chuyển toolkit đồng thời khi xâm nhập hệ thống

---

## DDoS - Nghiên Cứu Tình Huống

**HTTP/2 "Rapid Reset" (2023)** - Tấn công vào Google Cloud, khai thác stream multiplexing: 100 luồng trực tiếp qua một kết nối TCP.

---

## Các Loại Tấn Công DDoS

### Tấn Công Băng Thông (Volumetric Attacks)

Làm cạn kiệt băng thông; thường nhắm NTP và SSDP (stateless):
- **Flood attack** - Lưu lượng khổng lồ
- **Amplification attack** - Gửi thông điệp đến địa chỉ broadcast

### Tấn Công Giao Thức (Protocol Attacks)

Nhắm vào bảng trạng thái kết nối thay vì băng thông.

### Tấn Công Tầng Ứng Dụng (Application Layer Attacks)

Làm ngập lưu lượng web hợp lệ, chặn truy cập bằng đăng nhập sai liên tục, SQL query; Slowloris (kết nối HTTP half-open - rất nhiều kết nối).

---

## Kỹ Thuật Tấn Công DoS/DDoS

| Kỹ thuật | Mô tả |
|---------|-------|
| **UDP flood** | Gói UDP giả mạo gửi với tốc độ cao đến cổng ngẫu nhiên |
| **ICMP flood** | Lượng lớn ICMP Echo Request |
| **Ping of Death** | Gói tin dị dạng hoặc quá khổ |
| **Smurf attack** | Địa chỉ IP nguồn giả gửi ICMP ECHO |
| **Pulse wave DDoS** | Luồng gói tin lặp lại mỗi 10 phút; gần như không thể phục hồi |
| **Zero-day DDoS** | Dùng lỗ hổng chưa có patch; không có bảo vệ đã biết |
| **NTP amplification** | Botnet gửi UDP đến IP giả mạo; NTP có monlist enabled; `nmap -sU -Pu:123 -Pn -n --script=ntp-monlist` |
| **SYN flood** | TCP SYN với IP nguồn giả; máy chủ không nhận được phản hồi |
| **Fragmentation attack** | Ngăn nạn nhân tái lắp ráp gói tin; số lượng lớn gói 1500 byte |
| **Spoofed session flood** | Phiên TCP giả mạo với SYN, ACK và RST/FIN |
| **HTTP GET/POST** | Dùng HTTP header trễ để duy trì kết nối HTTP |
| **Slowloris** | HTTP request một phần đến web server mục tiêu |
| **Multi-vector DDoS** | Kết hợp volumetric + protocol + application layer |
| **Peer-to-peer** | Dùng mạng DC++ |
| **Permanent DoS (phlashing)** | Gây thiệt hại phần cứng không thể khôi phục; gửi bản cập nhật firmware giả |
| **TCP SACK panic** | Chỉ Linux; gửi gói SACK với MSS dị dạng gây tràn số nguyên trong socket buffer Linux; gói 48 byte → kernel panic |
| **Distributed Reflection DRDoS** | Tấn công giả mạo; dùng nhiều máy trung gian và thứ cấp |
| **Ransom DDoS** | Đòi tiền chuộc đi kèm DDoS |

---

## Toolkit Tấn Công

- ISB (I'm So Bored) - HTTP, UDP, TCP, ICMP flood
- UltraDDOS-v2
- HULK
- Slowloris
- UFONet

---

## Biện Pháp Đối Phó

- **Activity profiling** - Thiết lập đường cơ sở lưu lượng trung bình
- **Sequential change-point detection** - Lọc theo địa chỉ IP, flow theo thời gian
- **Wavelet-based signal analysis** - Phân tích lưu lượng mạng theo thành phần phổ

**Evasion:** Blumira honeypot software
