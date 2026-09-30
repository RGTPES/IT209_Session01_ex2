# Báo Cáo Thực Hành: Cấu Hình Người Dùng devops (Least Privilege)
**Bài tập:** Session 02 - Exercise 2  
**Học viên:** PTIT DevOps  
**Máy chủ Droplet IP:** `221.121.3.56`  

---

## 1. Mục tiêu
- Vận hành hệ thống theo nguyên tắc đặc quyền tối thiểu (**Least Privilege**).
- Tạo tài khoản làm việc riêng có tên là `devops` để thực hiện cấu hình máy chủ hàng ngày thay vì sử dụng trực tiếp tài khoản `root`.
- Cấu hình xác thực SSH Key và quyền quản trị `sudo` cho tài khoản `devops`.

---

## 2. Các bước và lệnh Linux đã thực hiện

### Bước 1: Tạo tài khoản người dùng mới `devops`
Đăng nhập vào máy chủ bằng tài khoản `root` và tạo người dùng `devops`:
```bash
adduser devops
```
*(Hệ thống yêu cầu nhập mật khẩu cho user mới và các thông tin cơ bản).*

---

### Bước 2: Thêm user `devops` vào nhóm quản trị `sudo`
Cấp quyền thực thi các lệnh quản trị hệ thống cho `devops`:
```bash
usermod -aG sudo devops
```

Kiểm tra lại nhóm của user `devops`:
```bash
groups devops
# Kết quả hiển thị: devops : devops sudo
```

---

### Bước 3: Sao chép SSH Key từ tài khoản `root` sang `devops`
Để cho phép tài khoản `devops` đăng nhập được thông qua SSH Key mà không cần mật khẩu:
```bash
# Tạo thư mục .ssh trong thư mục home của devops
mkdir -p /home/devops/.ssh

# Sao chép file authorized_keys từ root sang devops
cp /root/.ssh/authorized_keys /home/devops/.ssh/authorized_keys
```

---

### Bước 4: Phân quyền bảo mật cho thư mục `.ssh` và file `authorized_keys`
Cơ chế SSH yêu cầu phân quyền nghiêm ngặt để đảm bảo an toàn:
```bash
# Phân quyền 700 cho thư mục .ssh (chỉ user sở hữu mới có quyền đọc, ghi, thực thi)
chmod 700 /home/devops/.ssh

# Phân quyền 600 cho file authorized_keys (chỉ user sở hữu có quyền đọc và ghi)
chmod 600 /home/devops/.ssh/authorized_keys

# Chuyển quyền sở hữu thư mục .ssh và toàn bộ file bên trong sang cho user devops
chown -R devops:devops /home/devops/.ssh
```

Kiểm tra quyền đã gán:
```bash
ls -ld /home/devops/.ssh
ls -l /home/devops/.ssh/authorized_keys
```

---

## 3. Log Terminal kiểm tra kết quả

### 3.1. Đăng nhập SSH từ máy tính cá nhân bằng tài khoản `devops`
Từ máy cá nhân (Terminal / PowerShell), thực hiện lệnh kết nối:
```text
$ ssh devops@221.121.3.56
Welcome to Ubuntu 22.04.4 LTS (GNU/Linux 5.15.0-105-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/pro

Last login: Wed Sep 30 22:00:00 2026 from 113.190.x.x
devops@taskapi-prod:~$
```
*(Đăng nhập thành công bằng SSH Key vào tài khoản `devops`)*

---

### 3.2. Kiểm tra danh tính hiện tại
```text
devops@taskapi-prod:~$ whoami
devops
```

---

### 3.3. Kiểm tra quyền sudo với lệnh `sudo whoami`
```text
devops@taskapi-prod:~$ sudo whoami
[sudo] password for devops: 
root
```
> **Kết quả:** Lệnh trả về `root`, xác nhận tài khoản `devops` đã có quyền quản trị `sudo` thành công.
