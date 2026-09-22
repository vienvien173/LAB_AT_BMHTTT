# LAB 3 – CÁC MỐI ĐE DỌA AN TOÀN THÔNG TIN

## 1. Thông tin sinh viên

- **Họ tên:** Nguyễn Thị Bạch Viên
- **MSSV:** 1150080122
- **Tên Lab:** LAB 3 – Các mối đe dọa An toàn thông tin
- **Môi trường thực hiện:** VMware Workstation Pro trên Windows 11

---

## 2. Phiên bản môi trường

### 2.1. Máy ảo

- **Phần mềm ảo hóa:** VMware Workstation Pro 26H1
- **Hệ điều hành:** Windows 11 25H2 x64
- **OS Build:** 26200.9445 (KB5124008)
- **CPU:** 4 vCPU
- **RAM:** 4 GB
- **Ổ đĩa:** 64 GB
- **Network Adapter 1:** Custom – VMnet1 (Host-only)
- **Network Adapter 2:** NAT
- **TPM:** Có

### 2.2. Công cụ sử dụng

| Công cụ | Phiên bản yêu cầu |
| :--- | :---: |
| **Python** | 3.14.7 |
| **Wireshark** | 4.6.8 Stable |
| **Npcap** | Đi kèm Wireshark |
| **Sysmon** | 15.22 |
| **Autoruns** | 14.3 |
| **Process Explorer** | 17.14 |
| **PowerShell** | 5.1 |
| **Microsoft Defender Antivirus** | Có |

---

## 3. Cách dựng môi trường

### 3.1. Tạo máy ảo Windows 11

Tạo máy ảo Windows 11 trên VMware Workstation Pro với cấu hình:

- Windows 11 25H2 x64
- 4 vCPU
- 4 GB RAM
- 64 GB Disk
- Network Adapter sử dụng VMnet1 – Host-only
- Có thể sử dụng NAT cho các bước cần truy cập Internet
- TPM được bật

Kiểm tra phiên bản Windows bằng:

```powershell
winver
