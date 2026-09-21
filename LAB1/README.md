# LAB 1 – BẮT GÓI TIN TELNET - SSH

## 1. Thông tin sinh viên

- Họ tên: Nguyễn Thị Bạch Viên
- MSSV: 1150080122
- Lớp: CNPM2

---

## 2. Môi trường thực hành

Bài thực hành được thực hiện trên môi trường máy ảo VMware.

### Mô hình

- Windows 11: Máy Client, sử dụng PuTTY để kết nối Telnet/SSH.
- Ubuntu Server 26.04 LTS: Máy Server, chạy dịch vụ Telnet và SSH.
- Windows Server: Máy Attacker, sử dụng Wireshark để bắt và phân tích gói tin.

Các máy ảo được kết nối trong mạng lab nội bộ.

> Lưu ý: Telnet chỉ được sử dụng trong mạng lab nội bộ, không công khai cổng TCP/23 ra Internet.

---

## 3. Công cụ sử dụng

- VMware Workstation
- Ubuntu Server 26.04 LTS
- Windows 11
- Windows Server
- PuTTY 0.85
- Wireshark 4.6.8
- Npcap

---

# 4. NỘI DUNG ĐÃ THỰC HIỆN

## 4.1. Thiết lập mạng

Đã cấu hình 3 máy ảo cùng tham gia mạng lab nội bộ.

### Các máy

| Máy | Vai trò | Phần mềm chính |
|---|---|---|
| Windows 11 | Client | PuTTY |
| Ubuntu Server | Server | Telnet, SSH |
| Windows Server | Attacker | Wireshark |

Đã kiểm tra kết nối giữa các máy bằng lệnh `ping`.

---

# 5. CẤU HÌNH TELNET SERVER

## 5.1. Cài đặt Telnet Server trên Ubuntu

Trên Ubuntu Server thực hiện:

```bash
sudo apt update
sudo apt install inetutils-telnetd
