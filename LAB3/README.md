# LAB 3 – CÁC MỐI ĐE DỌA AN TOÀN THÔNG TIN

## 1. THÔNG TIN SINH VIÊN

- Họ và tên: Nguyễn Thị Bạch Viên
- MSSV: 1150080122
- Lớp: 11_DH_CNPM2
- Môn học: An toàn và bảo mật hệ thống thông tin
- Tên Lab: LAB 3 – Các mối đe dọa an toàn thông tin

---

## 2. MÔI TRƯỜNG THỰC HÀNH

- VMware Workstation Pro: 26H1
- Hệ điều hành: Windows 11 25H2 x64
- OS Build: 26200.9445
- CPU: 4 vCPU
- RAM: 4 GB
- Disk: 64 GB
- Network Adapter 1: Custom VMnet1 – Host-only
- Network Adapter 2: NAT khi cần truy cập Internet

### Công cụ sử dụng

- Windows Defender / Windows Security
- PowerShell 5.1
- Sysmon 15.22
- Autoruns 14.3
- Process Explorer 17.14
- Wireshark 4.6.9
- Python 3.14.7

---

# 3. CHUẨN BỊ MÔI TRƯỜNG

Đã tạo các thư mục:

C:\LAB3
C:\LAB3\Evidence
C:\LAB3\Tools
C:\LAB3\Downloads
C:\LAB3\Assets

Đã chuẩn bị bộ tài nguyên LAB3 tại:

C:\LAB3\LAB3_Threats_Assets

Các công cụ Sysinternals:

C:\LAB3\Tools\Sysmon
C:\LAB3\Tools\Autoruns
C:\LAB3\Tools\ProcessExplorer

Đã cài đặt Python 3.14.7 và Wireshark 4.6.8.

Đã tạo file:

C:\LAB3\Evidence\start_time.txt

---

# 4. TÌNH HUỐNG 1 – XÁC ĐỊNH TÀI SẢN, LỖ HỔNG, MỐI ĐE DỌA VÀ RỦI RO

## 4.1. Risk Register

| Asset | Vulnerability | Threat | Risk | Control |
|---|---|---|---|---|
| Thông tin xác thực tài khoản lab | Mật khẩu ngắn hoặc bị nhập vào phần mềm không tin cậy | Dò mật khẩu/keylogging/phishing | Mất quyền truy cập hoặc chiếm tài khoản | Mật khẩu dài, MFA, endpoint protection, audit log |
| Dữ liệu trong C:\LAB3 | Thiếu backup/kiểm soát thay đổi | Xóa nhầm, mã độc, lỗi đĩa | Mất hoặc thay đổi dữ liệu | Backup, least privilege, hash, logging |
| Dịch vụ nghiệp vụ giả lập | Cổng lắng nghe ngoài dự kiến, thiếu giám sát | Backdoor/DoS | Gián đoạn hoặc truy cập trái phép | Firewall, process/network monitoring, allow-list |
| Email/người dùng | Thiếu xác minh domain/người gửi | Phishing/Spear Phishing | Lộ thông tin xác thực | Awareness, email filtering, MFA, xác minh người gửi |

## 4.2. Phân loại nguồn đe dọa

1. Nhân viên xóa nhầm tệp cấu hình đang sử dụng:
   - Hành động vô ý

2. Người có ác ý cài phần mềm thu thập dữ liệu:
   - Hành động cố ý

3. Mất điện kéo dài làm dịch vụ dừng và file chưa ghi bị mất:
   - Thảm họa tự nhiên

4. Ổ đĩa hỏng hoặc dịch vụ treo do lỗi phần mềm:
   - Lỗi kỹ thuật

5. Không có backup hoặc không vá hệ thống đúng hạn:
   - Lỗi quản lý

### Kết quả TH1

- PASS – Đã lập Risk Register.
- PASS – Đã phân loại các nguồn đe dọa.

---

# 5. TÌNH HUỐNG 2 – EICAR + WINDOWS DEFENDER

## 5.1. Kiểm tra Windows Defender

Đã kiểm tra trạng thái:

- AntivirusEnabled: True
- RealTimeProtectionEnabled: True
- Tamper Protection: Enabled

## 5.2. Kiểm tra EICAR

Đã tạo mẫu EICAR phục vụ kiểm tra khả năng phát hiện của Windows Defender.

File kiểm tra:

C:\LAB3\Evidence\eicar.com.txt

Windows Security đã phát hiện mối đe dọa.

Protection History ghi nhận:

- Threat blocked
- Threat quarantined

Mức độ:

- Severe

## 5.3. Kiểm tra bằng PowerShell

Đã sử dụng:

Get-MpThreatDetection

Kết quả ghi nhận ThreatID và file:

C:\LAB3\Evidence\eicar.com.txt

ActionSuccess: True

### Kết quả TH2

- PASS – Windows Defender phát hiện EICAR.
- PASS – EICAR được block/quarantine.
- PASS – Không tắt Real-time Protection.
- PASS – Không tạo exclusion.
- PASS – Không khôi phục mẫu EICAR.

### Evidence

H4_ProtectionHistory_EICAR.png

---

# 6. TÌNH HUỐNG 3 – PASSWORD / AUTHENTICATION LOGGING

## 6.1. Bật Audit Logon

Đã bật audit cho đăng nhập thành công và thất bại.

Kiểm tra bằng:

auditpol /get /subcategory:"Logon"

Kết quả:

Success and Failure

## 6.2. Tạo tài khoản kiểm tra

Đã tạo tài khoản:

lab3user

Tài khoản được cấu hình yêu cầu mật khẩu.

Kiểm tra:

Get-LocalUser -Name 'lab3user'

Kết quả:

- Enabled: True
- PasswordRequired: True

## 6.3. Đăng nhập thành công

Đã sử dụng:

runas /user:$env:COMPUTERNAME\lab3user cmd.exe

Sau khi đăng nhập thành công, sử dụng:

whoami

Kết quả xác nhận tài khoản lab3user.

## 6.4. Đăng nhập thất bại

Đã thực hiện 2 lần đăng nhập sai mật khẩu để tạo Security Event.

Đã thu thập:

- Event ID 4624 – Successful Logon
- Event ID 4625 – Failed Logon
- Event ID 4648 – Explicit Credentials

File:

C:\LAB3\Evidence\auth_events_before_rotation.txt

## 6.5. Kiểm tra Event ID 4625

Đã kiểm tra:

Windows Logs
→ Security

Event ID:

4625

Event xác nhận:

Account For Which Logon Failed:
Account Name: lab3user

Failure Reason:

Unknown user name or bad password.

Evidence:

H5_Event4625.png

## 6.6. Đổi mật khẩu

Đã đổi mật khẩu của tài khoản:

lab3user

Sau khi đổi mật khẩu:

- Mật khẩu cũ không đăng nhập được.
- Mật khẩu mới đăng nhập thành công.
- Đã kiểm tra lại Security Event Log.

File:

C:\LAB3\Evidence\auth_events_after_rotation.txt

### Kết quả TH3

- PASS – Audit Logon được bật.
- PASS – Tạo tài khoản lab3user.
- PASS – Đăng nhập thành công.
- PASS – Tạo đăng nhập thất bại.
- PASS – Thu thập Event ID 4624/4625/4648.
- PASS – Xác nhận Event ID 4625 liên quan lab3user.
- PASS – Thực hiện password rotation.
- PASS – Mật khẩu cũ không còn sử dụng được.
- PASS – Mật khẩu mới sử dụng được.

---

# 7. TÌNH HUỐNG 4 – SYSMON / PERSISTENCE

## 7.1. Cài đặt Sysmon

Đã sử dụng Sysmon 15.22.

File cấu hình:

C:\LAB3\LAB3_Threats_Assets\lab3_assets\sysmon-lab.xml

Lệnh cài đặt:

C:\LAB3\Tools\Sysmon\Sysmon64.exe -accepteula -i C:\LAB3\LAB3_Threats_Assets\lab3_assets\sysmon-lab.xml

Đã kiểm tra cấu hình Sysmon bằng:

C:\LAB3\Tools\Sysmon\Sysmon64.exe -c

## 7.2. Thu baseline Autoruns

Đã tạo file:

C:\LAB3\Evidence\autoruns_before.csv

Lệnh:

C:\LAB3\Tools\Autoruns\autorunsc64.exe -a * -c -h -s > C:\LAB3\Evidence\autoruns_before.csv

## 7.3. Kiểm tra Sysmon Event ID 1

Đã mở:

Event Viewer
→ Applications and Services Logs
→ Microsoft
→ Windows
→ Sysmon
→ Operational

Đã xác nhận:

- Log Name: Microsoft-Windows-Sysmon/Operational
- Source: Sysmon
- Event ID: 1
- Task Category: Process Create

Evidence:

H6_Sysmon_Event1.png

### Trạng thái TH4 hiện tại

- PASS – Sysmon 15.22 đã cài đặt.
- PASS – Sysmon configuration đã được nạp.
- PASS – Autoruns baseline đã được tạo.
- PASS – Đã xác nhận Sysmon Event ID 1.
- CHƯA HOÀN THÀNH – Chưa tạo persistence LAB3_Run_Demo.
- CHƯA HOÀN THÀNH – Chưa tạo Scheduled Task LAB3_Persistence_Demo.
- CHƯA HOÀN THÀNH – Chưa kiểm tra Autoruns LAB3_Run_Demo.
- CHƯA HOÀN THÀNH – Chưa tạo HTTP listener 127.0.0.1:8080.
- CHƯA HOÀN THÀNH – Chưa kiểm tra Process Explorer.

---

# 8. DANH SÁCH EVIDENCE ĐÃ CÓ

C:\LAB3\Evidence\

- start_time.txt
- eicar.com.txt
- auth_events_before_rotation.txt
- auth_events_after_rotation.txt
- autoruns_before.csv
- H4_ProtectionHistory_EICAR.png
- H5_Event4625.png
- H6_Sysmon_Event1.png

---

# 9. TỔNG KẾT TIẾN ĐỘ

| Tình huống | Nội dung | Trạng thái |
|---|---|---|
| TH1 | Asset – Vulnerability – Threat – Risk – Control | PASS |
| TH2 | EICAR + Windows Defender | PASS |
| TH3 | Authentication + Security Event Log | PASS |
| TH4 | Sysmon + Autoruns + Persistence | ĐANG THỰC HIỆN |
| TH5 | HTTP/TLS + Wireshark | CHƯA THỰC HIỆN |
| TH6 | Local Load + DDoS/Mail Log Offline | CHƯA THỰC HIỆN |
| TH7 | Phishing/Social Engineering Offline | CHƯA THỰC HIỆN |

---

# 10. LỖI ĐÃ GẶP VÀ CÁCH XỬ LÝ

## 10.1. Lỗi cài Python

Ban đầu không cài được Python bằng winget.

Cách xử lý:
- Cài Python bằng bộ cài chính thức.
- Kiểm tra Python 3.14.7.

## 10.2. Lỗi Sysmon configuration

Ban đầu sử dụng sai đường dẫn:

C:\LAB3\lab3_assets\sysmon-lab.xml

Đường dẫn chính xác:

C:\LAB3\LAB3_Threats_Assets\lab3_assets\sysmon-lab.xml

Sau khi sử dụng đúng đường dẫn, Sysmon được cài đặt thành công.

## 10.3. Lỗi runas

Ban đầu runas báo:

RUNAS ERROR: Unable to acquire user password

Đã xử lý bằng cách:

- Kiểm tra PasswordRequired.
- Đặt lại mật khẩu tài khoản lab3user.
- Kiểm tra dịch vụ Secondary Logon.
- Khởi động dịch vụ seclogon.
- Sử dụng dạng tài khoản COMPUTERNAME\lab3user.

Sau đó đăng nhập thành công.

---

# 11. KẾT LUẬN

Đã hoàn thành:

- Tình huống 1 – Risk Register và phân loại nguồn đe dọa.
- Tình huống 2 – EICAR và Windows Defender.
- Tình huống 3 – Authentication logging, Event 4624/4625/4648 và password rotation.
- Tình huống 4 – Cài Sysmon, tạo Autoruns baseline và xác nhận Event ID 1.

Tình huống 4 đang tiếp tục thực hiện phần:

- LAB3_Run_Demo
- LAB3_Persistence_Demo
- Autoruns
- Sysmon persistence logs
- HTTP listener 127.0.0.1:8080
- Process Explorer

Các tình huống TH5, TH6 và TH7 chưa thực hiện.
