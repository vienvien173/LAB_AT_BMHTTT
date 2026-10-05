# LAB 4 – KHẢO SÁT VÀ ĐÁNH GIÁ BỀ MẶT MẠNG BẰNG NMAP

## 1. Thông tin sinh viên

- Họ và tên: Nguyễn Thị Bạch Viên
- MSSV: 1150080122
- Môn học: An toàn và bảo mật hệ thống thông tin
- Tên bài Lab: Lab 4 – Khảo sát và đánh giá bề mặt mạng bằng Nmap

---

## 2. Mục tiêu bài Lab

Bài Lab nhằm làm quen với công cụ Nmap và sử dụng Nmap để khảo sát, đánh giá bề mặt mạng trong môi trường thực hành cô lập.

Các nội dung chính đã thực hiện:

- Xây dựng môi trường mạng thực hành bằng máy ảo.
- Xác định địa chỉ IP của các máy trong mạng.
- Phát hiện các host đang hoạt động.
- Khảo sát các cổng TCP và UDP.
- So sánh một số kỹ thuật quét TCP.
- Nhận diện dịch vụ và phiên bản dịch vụ.
- Nhận diện hệ điều hành của máy đích.
- Sử dụng NSE để thu thập thông tin SMB và kiểm tra dấu hiệu lỗ hổng MS17-010.
- Xuất kết quả quét ra các định dạng khác nhau.
- Đánh giá sự thay đổi trước và sau khi áp dụng biện pháp hardening.

---

## 3. Môi trường thực hành

| Máy | Vai trò |
|---|---|
| Windows 11 VM | Máy Windows dùng để cài Nmap/Zenmap và làm máy đích đối chiếu |
| Kali Linux VM | Máy quét chính sử dụng Nmap |
| Metasploitable 2 | Máy đích cố ý có các dịch vụ và lỗ hổng phục vụ thực hành |

### Địa chỉ IP sử dụng

| Máy | Địa chỉ IP |
|---|---|
| Kali Linux | 192.168.157.128 |
| Metasploitable 2 | 192.168.157.129 |
| Windows 11 VM | 192.168.157.130 |

Các máy được cấu hình trong cùng mạng host-only
---

## 4. Công cụ sử dụng

- Nmap
- Npcap
- Zenmap
- Kali Linux
- Windows 11
- Metasploitable 2
- VMware/VirtualBox
- Terminal/CMD

---

## 5. Các tình huống đã thực hiện

### 5.1. Kiểm tra và cài đặt Nmap

Nmap được cài đặt và kiểm tra trên Windows 11 VM và Kali Linux.

Sử dụng lệnh kiểm tra phiên bản:

    nmap --version

Kết quả: Nmap hoạt động bình thường và sẵn sàng thực hiện các bài quét.

**Trạng thái: PASS**

---

### 5.2. Xác định địa chỉ IP các máy

Xác định địa chỉ IP thực tế của Kali Linux, Metasploitable 2 và Windows 11 VM.

Các máy được kiểm tra để bảo đảm nằm trong cùng mạng thực hành và có khả năng giao tiếp với nhau.

**Trạng thái: PASS**

---

### 5.3. Phát hiện các host đang hoạt động

Sử dụng Nmap để thực hiện host discovery trên dải mạng thực hành.

Qua kết quả quét, xác định được các host đang hoạt động và đối chiếu với các máy ảo đã khởi động.

**Trạng thái: PASS**

---

### 5.4. Khảo sát cổng TCP

Thực hiện các kỹ thuật quét TCP bao gồm:

- TCP Connect Scan (-sT)
- SYN Scan (-sS)
- FIN Scan (-sF)
- Xmas Scan (-sX)
- NULL Scan (-sN)
- ACK Scan (-sA)

Kết quả cho phép quan sát các trạng thái cổng như:

- open
- closed
- filtered
- unfiltered
- open|filtered

Qua đó có thể so sánh sự khác nhau giữa các kỹ thuật quét và phản ứng của máy đích.

**Trạng thái: PASS**

---

### 5.5. Quét UDP

Thực hiện quét một số cổng UDP phổ biến để khảo sát các dịch vụ UDP trên máy đích.

Do UDP không thực hiện cơ chế bắt tay giống TCP nên quá trình quét chậm hơn và có thể xuất hiện trạng thái open|filtered.

**Trạng thái: PASS**

---

### 5.6. Nhận diện dịch vụ và phiên bản

Sử dụng chức năng Version Detection của Nmap để xác định dịch vụ và phiên bản đang chạy trên các cổng mở.

Trên Metasploitable 2 phát hiện nhiều dịch vụ đang hoạt động như:

- FTP
- SSH
- Telnet
- SMTP
- HTTP
- SMB
- MySQL
- PostgreSQL
- VNC
- IRC

Một số phiên bản dịch vụ cũng được Nmap xác định thành công.

**Trạng thái: PASS**

---

### 5.7. Nhận diện hệ điều hành

Thực hiện OS Detection trên máy Metasploitable 2.

Kết quả Nmap nhận diện máy đích thuộc hệ điều hành Linux và cung cấp thông tin ước lượng về phiên bản kernel.

**Trạng thái: PASS**

---

### 5.8. Kiểm tra SMB bằng NSE

Thực hiện NSE trên Metasploitable 2 và Windows 11 VM.

#### Metasploitable 2

Cổng:

    445/tcp open microsoft-ds

Script smb-os-discovery thu thập được các thông tin:

- OS: Unix (Samba 3.0.20-Debian)
- Computer name: metasploitable
- Domain name: localdomain
- FQDN: metasploitable.localdomain

Script smb-vuln-ms17-010 không trả về trạng thái VULNERABLE, vì vậy không có đủ bằng chứng từ kết quả này để kết luận máy bị ảnh hưởng bởi MS17-010.

#### Windows 11 VM

Kết quả:

    445/tcp filtered microsoft-ds

Cổng 445 trên Windows 11 VM ở trạng thái filtered. Vì vậy các script smb-os-discovery và smb-vuln-ms17-010 không thu thập được thông tin SMB.

Không thể kết luận tình trạng MS17-010 của Windows chỉ dựa trên kết quả này.

**Trạng thái: PASS**

---

### 5.9. Xuất kết quả quét

Kết quả Nmap được lưu để phục vụ việc phân tích và làm bằng chứng cho báo cáo.

Đã thực hiện xuất kết quả dạng:

- Normal text (.txt)
- XML (.xml)
- Grepable
- HTML (nếu thực hiện chuyển đổi XML)

Ví dụ tệp kết quả:

    ket_qua.txt
    ket_qua.xml

**Trạng thái: PASS**

---

## 6. Kết quả tổng hợp

| Nội dung | Kết quả |
|---|---|
| Cài đặt và kiểm tra Nmap | PASS |
| Cấu hình môi trường mạng | PASS |
| Xác định IP | PASS |
| Host Discovery | PASS |
| TCP Connect Scan | PASS |
| SYN Scan | PASS |
| FIN/Xmas/NULL Scan | PASS |
| ACK Scan | PASS |
| UDP Scan | PASS |
| Version Detection | PASS |
| OS Detection | PASS |
| NSE SMB Discovery | PASS |
| NSE MS17-010 | PASS |
| Xuất kết quả | PASS |
