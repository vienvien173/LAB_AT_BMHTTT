
# LAB – CẤU HÌNH VÀ KIỂM THỬ FIREWALL PFSENSE

## 1. Thông tin sinh viên

- **Họ và tên:** Nguyễn Thị Bạch Viên
- **MSSV:** 1150080122
- **Lớp:** CNPM2
- **Môn học:** An toàn và bảo mật hệ thống thông tin
- **Tên bài lab:** Cấu hình và kiểm thử Firewall pfSense

## 2. Mục tiêu bài lab

Bài lab nhằm tìm hiểu cách triển khai và quản lý Firewall pfSense trong môi trường mạng ảo VMware Workstation.

Các nội dung thực hiện:

- Cài đặt và cấu hình pfSense.
- Thiết lập hệ thống mạng WAN, LAN và DMZ.
- Cấu hình Windows Server làm Domain Controller và DNS Server.
- Cài đặt IIS Web Server trong vùng DMZ.
- Cấu hình Outbound NAT để các máy trong LAN truy cập Internet.
- Thiết lập và kiểm thử các Firewall Rule.
- Kiểm tra khả năng cho phép hoặc chặn lưu lượng mạng.
- Thực hiện các tình huống kiểm thử chính sách bảo mật.

## 3. Môi trường thực hiện

### 3.1. Phần mềm sử dụng

| Phần mềm | Chức năng |
|---|---|
| VMware Workstation | Tạo và quản lý máy ảo |
| pfSense CE 2.7.2 | Firewall và định tuyến mạng |
| Windows Server | Domain Controller và DNS Server |
| Windows Server | IIS Web Server trong DMZ |
| PowerShell / CMD | Kiểm tra kết nối mạng |
| Trình duyệt Web | Quản trị pfSense WebGUI |

### 3.2. Mô hình mạng

Hệ thống gồm ba vùng mạng chính:

- **WAN:** Kết nối Internet thông qua VMware NAT.
- **LAN:** Mạng nội bộ chứa Domain Controller.
- **DMZ:** Vùng mạng riêng chứa IIS Web Server.

### 3.3. Bảng địa chỉ IP

| Thiết bị | Interface | Địa chỉ IP | Vai trò |
|---|---|---|---|
| pfSense | WAN (em0) | 192.168.203.132 | Kết nối Internet |
| pfSense | LAN (em1) | 10.0.0.1/8 | Gateway LAN |
| pfSense | DMZ (em2) | 172.16.0.1/16 | Gateway DMZ |
| Domain Controller | LAN | 10.0.0.2/8 | AD DS và DNS |
| DMZ-Web | DMZ | 172.16.0.2/16 | IIS Web Server |
| Máy thật | VMnet1 | 10.0.0.100/8 | Quản trị pfSense |


### 3.4. Cấu hình VMware Network

| VMnet | Chế độ | Subnet |
|---|---|---|
| VMnet8 | NAT | 192.168.203.0/24 |
| VMnet1 | Host-only | 10.0.0.0/8 |
| VMnet2 | Host-only | 172.16.0.0/16 |

## 4. Các nội dung đã thực hiện

### 4.1. Cài đặt và cấu hình pfSense

- Cài đặt pfSense CE 2.7.2 trên VMware.
- Cấu hình ba card mạng WAN, LAN và DMZ.
- Thiết lập địa chỉ IP cho từng interface.
- Truy cập thành công WebGUI tại `https://10.0.0.1`.
- Hoàn thành cấu hình ban đầu của pfSense.

**Kết quả:** PASS.

### 4.2. Cấu hình Domain Controller

- Cấu hình địa chỉ IP `10.0.0.2/8`.
- Thiết lập Default Gateway `10.0.0.1`.
- Cài đặt Active Directory Domain Services.
- Cài đặt DNS Server.
- Tạo domain `vietnam.local`.
- Cấu hình DNS Forwarder `8.8.8.8`.
- Kiểm tra phân giải tên miền thành công.

**Kết quả:** PASS.

### 4.3. Cấu hình máy chủ DMZ-Web

- Tạo Windows Server trong mạng VMnet2.
- Cấu hình IP `172.16.0.2/16`.
- Thiết lập Default Gateway `172.16.0.1`.
- Cài đặt IIS Web Server.
- Kiểm tra IIS bằng lệnh:

```powershell
curl.exe http://localhost
```

Kết quả trả về nội dung trang mặc định IIS.

- Tạo Firewall Rule cho phép ICMP Echo Request từ DMZ-Web đến địa chỉ DMZ của pfSense.
- Kiểm tra ping đến `172.16.0.1` thành công.

**Kết quả:** PASS đối với cài đặt IIS và kết nối đến gateway DMZ.

### 4.4. Cấu hình Outbound NAT

Truy cập:

`Firewall > NAT > Outbound`

Các bước thực hiện:

1. Chuyển NAT Mode sang Hybrid Outbound NAT.
2. Tạo rule NAT cho mạng LAN.
3. Cấu hình Interface là WAN.
4. Chọn Address Family là IPv4.
5. Thiết lập Source Network `10.0.0.0/8`.
6. Destination là Any.
7. Translation Address là WAN address.
8. Lưu và áp dụng cấu hình.

**Kết quả:** PASS.

### 4.5. Cấu hình Firewall Rule LAN

Truy cập:

`Firewall > Rules > LAN`

Các bước thực hiện:

1. Giữ nguyên Anti-Lockout Rule.
2. Disable các rule mặc định cho phép LAN truy cập mọi đích.
3. Reset State Table.
4. Tạo rule Pass IPv4 với:
   - Protocol: Any
   - Source: LAN subnets
   - Destination: Any
5. Save và Apply Changes.

**Kết quả:** PASS.

## 5. Kiểm thử Firewall

### 5.1. Kiểm thử khi bật rule LAN

Trên Domain Controller chạy:

```cmd
ping 10.0.0.1
ping 8.8.8.8
nslookup google.com
```

Kết quả:

- Ping gateway LAN thành công.
- Ping Internet thành công.
- Phân giải tên miền thành công.

**Đánh giá:** PASS.

### 5.2. Kiểm thử khi tắt rule LAN

Thực hiện:

1. Disable rule Pass LAN.
2. Apply Changes.
3. Reset State Table.
4. Kiểm tra kết nối Internet.

Lệnh:

```cmd
ping 8.8.8.8
```

Kết quả:

```text
Request timed out.
```

Nhận xét: Khi rule LAN bị vô hiệu hóa, lưu lượng ICMP ra Internet không còn được cho phép.

**Đánh giá:** PASS.

### 5.3. Kiểm thử sau khi bật lại rule LAN

- Enable lại rule Pass LAN.
- Apply Changes.
- Kiểm tra kết nối.

Kết quả ping Internet thành công.

**Đánh giá:** PASS.

## 6. Các tình huống Firewall

### 6.1. Tình huống 1 – Chặn ICMP nhưng vẫn cho phép Web/DNS

**Mục tiêu:**

Chặn ICMP Echo Request từ LAN nhưng vẫn cho phép phân giải DNS và kết nối HTTPS.

**Cấu hình:**

Tạo Firewall Rule trên interface LAN:

| Thuộc tính | Giá trị |
|---|---|
| Action | Block |
| Interface | LAN |
| Address Family | IPv4 |
| Protocol | ICMP |
| ICMP Subtypes | Echo Request |
| Source | LAN subnets |
| Destination | Any |

Đặt rule Block ICMP phía trên rule Pass LAN.

**Kiểm thử ICMP:**

```cmd
ping 8.8.8.8
```

Kết quả:

```text
Request timed out.
```

**Kiểm thử DNS:**

```cmd
nslookup google.com
```

Kết quả: Phân giải tên miền thành công.

**Kiểm thử HTTPS:**

```powershell
Test-NetConnection google.com -Port 443
```

Kết quả:

```text
TcpTestSucceeded : True
```

**Tổng hợp kết quả:**

| Nội dung | Kết quả | Đánh giá |
|---|---|---|
| Chặn ICMP | Request timed out | PASS |
| Phân giải DNS | Thành công | PASS |
| Kết nối HTTPS | TcpTestSucceeded: True | PASS |

**Kết luận:**

Firewall pfSense đã chặn thành công ICMP Echo Request từ LAN, đồng thời vẫn cho phép các dịch vụ DNS và HTTPS hoạt động bình thường.

**Trạng thái:** HOÀN THÀNH.

## 7. Lỗi gặp phải và cách khắc phục

| Lỗi / Hiện tượng | Nguyên nhân | Cách xử lý |
|---|---|---|
| Không ping được gateway DMZ | Chưa có rule cho phép ICMP trên DMZ | Tạo rule Pass ICMP Echo Request |
| WebGUI mất kết nối sau Reset States | Các trạng thái kết nối cũ bị xóa | Truy cập lại WebGUI |
| Rule LAN chưa đúng giao thức | Chọn TCP thay vì Any | Chỉnh Protocol thành Any |
| Source NAT sai subnet | Nhập sai prefix mạng LAN | Sửa thành 10.0.0.0/8 |
| Ping Internet bị timeout khi Disable rule | Firewall chặn lưu lượng theo chính sách | Bật lại rule khi cần khôi phục kết nối |
| Ping Internet bị timeout khi Block ICMP | Rule Block ICMP hoạt động | Đây là kết quả kiểm thử mong đợi |

## 8. Tổng hợp kết quả

| STT | Nội dung | Trạng thái |
|---|---|---|
| 1 | Cài đặt pfSense | PASS |
| 2 | Cấu hình WAN, LAN, DMZ | PASS |
| 3 | Cấu hình Domain Controller và DNS | PASS |
| 4 | Cài đặt IIS trên DMZ-Web | PASS |
| 5 | Cấu hình Outbound NAT LAN | PASS |
| 6 | Cấu hình Firewall Rule LAN | PASS |
| 7 | Kiểm thử bật/tắt rule LAN | PASS |
| 8 | Tình huống 1 – Block ICMP, Allow DNS/HTTPS | PASS |
| 9 | Tình huống 2 – Chỉ cho phép một host | Chưa thực hiện |
| 10 | Cô lập DMZ khỏi LAN | Chưa thực hiện |
| 11 | Port Forward WAN đến DMZ | Chưa thực hiện |
| 12 | Bật Logging và kiểm tra Firewall Log | Chưa thực hiện |

