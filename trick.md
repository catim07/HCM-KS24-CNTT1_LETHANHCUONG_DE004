TRƯỜNG HỢP 1: ĐỀ YÊU CẦU TẠO USER MỚI (ADDUSER)
(Dùng tài khoản admin devopsg5 để tạo user riêng cuongcatim, cấp quyền và deploy)

Bước 1.1: SSH vào VPS bằng tài khoản Admin
Mở PowerShell trên máy tính gõ:

powershell
ssh devopsg5@103.72.57.148
(Nhập mật khẩu: aetermius123@)

Bước 1.2: Dán toàn bộ khối lệnh này vào Terminal VPS (Copy 1 lần dán hết)
bash
# 1. Tạo user cuongcatim với mật khẩu hoatien20
sudo adduser --disabled-password --gecos "" cuongcatim
echo "cuongcatim:hoatien20" | sudo chpasswd
# 2. Cấp quyền sudo (Admin) và quyền chạy Docker cho user mới
sudo usermod -aG sudo,docker cuongcatim
# 3. Gắn SSH Key để máy tính và GitHub Actions tự động kết nối không hỏi pass
sudo mkdir -p /home/cuongcatim/.ssh
sudo chmod 700 /home/cuongcatim/.ssh
echo "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQDBpfFounUH0PmOC2y++/PWm7gDjevewHRhgZaWmtQ2N/bhZz49dYyitAw2cu7Rl0Txhwo6Gw60NvO65a9v3JHc0iNGCiVpd0dmJb06JS/T/cuFxrkqDSKBmc/QT94It27sfDebUAe2VDZrKJXQpl2weuOVyy/0JeknLTxqy8CNox3wKtYOxJmH2PNLXAxe4RalldAGp7rLLIFH/eE/ub2NVfcoB1rCRfLFlHttkWjTDDVPOfdthKDp3WGBH8nNGr1Ppf1TarVhMYxLzp++SzOp6o7CzApiDodYADuxzoPAMyYjwWKm9Wzc+bnR9JNcv/NiLwgAm8bPTM2ri58auWImanUXHUXLCZ+Xolu36a0pHtI0Gf+37nhQHyXFiFAQTnVpkMc1BUD1EdU4/izwGhuK1VU/T9OFVhf77VPHb/sX6m85QOj/0i9Xgbl1/BVTGIjTLhmCfBM2hOyxQxyVjEJsyhElT2aMUKTAoxklOYfLYoBSbvvg/pQbU+bEDBOpg9OJ2qxHhyM61FSG6S00eT2ue+OPSh+nT0TkQhNscgUNjZSLOWAWUGRDhIAuVZyO0Rhu79B07WidGsThXzRTyHcSgtjNu4uSlg3MpGI2G3QfLcW8s2HK5aXYslzg3moz/WLAxtS8kprnaKn2bMNBUkZnMg7QxNOG9E+zHSUnMoq/Iw== le thanh cuong@LAPTOP-CFE5QQ0I" | sudo tee /home/cuongcatim/.ssh/authorized_keys
sudo chmod 600 /home/cuongcatim/.ssh/authorized_keys
sudo chown -R cuongcatim:cuongcatim /home/cuongcatim/.ssh
# 4. Tạo thư mục app thuộc quyền của cuongcatim
sudo mkdir -p /home/cuongcatim/app
sudo chown -R cuongcatim:cuongcatim /home/cuongcatim/app
# 5. Cài Docker, tắt Nginx và mở tường lửa dải port 1822-1826
curl -fsSL https://get.docker.com | sudo sh
sudo systemctl stop nginx
sudo systemctl disable nginx
sudo fuser -k 1822/tcp 1823/tcp 1824/tcp 1825/tcp 1826/tcp 2>/dev/null
sudo ufw allow 1822:1826/tcp
sudo ufw reload
# 6. Kiểm tra user vừa tạo thành công (Chụp ảnh minh chứng)
id cuongcatim
📸 Chụp ảnh màn hình Terminal có dòng uid=...(cuongcatim)...groups=...,sudo,docker làm minh chứng tạo user. Sau đó gõ exit.

Bước 1.3: Cài đặt GitHub Secrets (Lưu ý biến SERVER_USER)
Vào Settings GitHub Repo ➔ Secrets and variables ➔ Actions:

SERVER_USER: cuongcatim
SERVER_HOST: 103.72.57.148
SERVER_PORT: 22
SERVER_SSH_KEY: (Chạy lệnh: Get-Content "$HOME\.ssh\id_k24cntt1" | Set-Clipboard rồi paste vào)
DOCKER_USER: eggplant07
DOCKER_PAT: (Token Docker Hub quyền Read & Write)
DB_NAME: taskdb (hoặc tên DB trong file SQL)
DB_USER: root
DB_PASS: hoatien20
DB_PORT: 1822
DB_URL: jdbc:mysql://db:3306/taskdb?useSSL=false&serverTimezone=UTC&allowPublicKeyRetrieval=true
🔵 TRƯỜNG HỢP 2: ĐỀ YÊU CẦU DÙNG TÀI KHOẢN CÓ SẴN (KHÔNG CẦN ADDUSER)
(Dùng trực tiếp tài khoản đề cấp là devopsg5 để cấu hình và deploy luôn)

Bước 2.1: SSH vào VPS bằng tài khoản có sẵn
Mở PowerShell trên máy tính gõ:

powershell
ssh devopsg5@103.72.57.148
(Nhập mật khẩu: aetermius123@)

Bước 2.2: Dán toàn bộ khối lệnh này vào Terminal VPS (Copy 1 lần dán hết)
bash
# 1. Cài đặt Docker & Cấp quyền Docker cho user devopsg5
curl -fsSL https://get.docker.com | sudo sh
sudo usermod -aG docker $USER
# 2. Gắn SSH Key vào user devopsg5 (để GitHub Actions kết nối không bị lỗi handshake)
mkdir -p ~/.ssh && chmod 700 ~/.ssh
echo "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQDBpfFounUH0PmOC2y++/PWm7gDjevewHRhgZaWmtQ2N/bhZz49dYyitAw2cu7Rl0Txhwo6Gw60NvO65a9v3JHc0iNGCiVpd0dmJb06JS/T/cuFxrkqDSKBmc/QT94It27sfDebUAe2VDZrKJXQpl2weuOVyy/0JeknLTxqy8CNox3wKtYOxJmH2PNLXAxe4RalldAGp7rLLIFH/eE/ub2NVfcoB1rCRfLFlHttkWjTDDVPOfdthKDp3WGBH8nNGr1Ppf1TarVhMYxLzp++SzOp6o7CzApiDodYADuxzoPAMyYjwWKm9Wzc+bnR9JNcv/NiLwgAm8bPTM2ri58auWImanUXHUXLCZ+Xolu36a0pHtI0Gf+37nhQHyXFiFAQTnVpkMc1BUD1EdU4/izwGhuK1VU/T9OFVhf77VPHb/sX6m85QOj/0i9Xgbl1/BVTGIjTLhmCfBM2hOyxQxyVjEJsyhElT2aMUKTAoxklOYfLYoBSbvvg/pQbU+bEDBOpg9OJ2qxHhyM61FSG6S00eT2ue+OPSh+nT0TkQhNscgUNjZSLOWAWUGRDhIAuVZyO0Rhu79B07WidGsThXzRTyHcSgtjNu4uSlg3MpGI2G3QfLcW8s2HK5aXYslzg3moz/WLAxtS8kprnaKn2bMNBUkZnMg7QxNOG9E+zHSUnMoq/Iw== le thanh cuong@LAPTOP-CFE5QQ0I" >> ~/.ssh/authorized_keys
chmod 600 ~/.ssh/authorized_keys
# 3. Tạo sẵn thư mục app trong thư mục gốc của devopsg5
mkdir -p ~/app
# 4. Tắt Nginx & Mở tường lửa dải port 1822-1826
sudo systemctl stop nginx
sudo systemctl disable nginx
sudo fuser -k 1822/tcp 1823/tcp 1824/tcp 1825/tcp 1826/tcp 2>/dev/null
sudo ufw allow 1822:1826/tcp
sudo ufw reload
exit
Bước 2.3: Cài đặt GitHub Secrets (Lưu ý biến SERVER_USER)
Vào Settings GitHub Repo ➔ Secrets and variables ➔ Actions:

SERVER_USER: devopsg5 (Tài khoản có sẵn)
SERVER_HOST: 103.72.57.148
SERVER_PORT: 22
SERVER_SSH_KEY: (Chạy lệnh: Get-Content "$HOME\.ssh\id_k24cntt1" | Set-Clipboard rồi paste vào)
DOCKER_USER: eggplant07
DOCKER_PAT: (Token Docker Hub quyền Read & Write)
DB_NAME: taskdb
DB_USER: root
DB_PASS: hoatien20 (hoặc pass mysql của đề)
DB_PORT: 1822
DB_URL: jdbc:mysql://db:3306/taskdb?useSSL=false&serverTimezone=UTC&allowPublicKeyRetrieval=true
🟢 PHẦN CHUNG: SỬA PORT, PUSH GIT & LẤY HÌNH NỘP BÀI (Áp dụng cho cả 2 trường hợp)
1. Sửa file docker-compose.yml trong mã nguồn đề thi:
   yaml
   db:
   ports:
    - "127.0.0.1:${DB_PORT:-1822}:3306"
      app:
      ports:
    - "1825:8083"
2. Mở PowerShell tại thư mục code và Push kích hoạt CI/CD:
   powershell
   git init
   git branch -M main
   git remote remove origin 2>$null
   git remote add origin https://github.com/catim07/<ten-repo-vua-tao>.git
   git add .
   git commit -m "ci: deploy via github actions"
   git push -u origin main --force
3. Lấy 2 ảnh minh chứng nộp bài:
   📸 Ảnh 1 (CI/CD): Vào tab Actions trên GitHub ➔ Bấm job deploy ➔ Mở rộng bước Deploy via Docker Compose SSH (hiển thị 4 job xanh và các dòng Container task-app Started, Deployment completed successfully) ➔ Chụp toàn màn hình trình duyệt.
   📸 Ảnh 2 (Web + Container):
   Nửa trái: Chrome vào http://103.72.57.148:1825/api/... (ra dữ liệu JSON).
   Nửa phải: PowerShell gõ:
   Nếu Trường hợp 1: ssh cuongcatim@103.72.57.148 "docker ps"
   Nếu Trường hợp 2: ssh devopsg5@103.72.57.148 "docker ps" (Hiện 2 container task-app port 1825 và task-db healthy).
   Chụp màn hình chia đôi này!
   7:27 AM
   trường hợp 2 là đăng nhập vô acc shh của tôi là cuongcatim
   7:28 AM
   Đã rõ! Dưới đây là TRƯỜNG HỢP 2 ĐƯỢC VIẾT LẠI CHUẨN XÁC KHI ĐĂNG NHẬP THẲNG BẰNG TÀI KHOẢN cuongcatim:

🔵 TRƯỜNG HỢP 2: DÙNG TÀI KHOẢN CÓ SẴN LÀ cuongcatim
(Không cần tạo user mới, đăng nhập thẳng bằng cuongcatim để chuẩn bị server và deploy)

Bước 2.1: SSH vào VPS bằng tài khoản cuongcatim
Mở PowerShell trên máy tính gõ:

powershell
ssh cuongcatim@103.72.57.148
(Nếu hỏi mật khẩu, nhập: hoatien20)

Bước 2.2: Dán toàn bộ khối lệnh này vào Terminal VPS (Copy 1 lần dán hết)
bash
# 1. Cài đặt Docker & Cấp quyền Docker cho user cuongcatim
curl -fsSL https://get.docker.com | sudo sh
sudo usermod -aG docker $USER
# 2. Gắn SSH Key vào cuongcatim (để GitHub Actions kết nối tự động không bị lỗi handshake)
mkdir -p ~/.ssh && chmod 700 ~/.ssh
echo "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQDBpfFounUH0PmOC2y++/PWm7gDjevewHRhgZaWmtQ2N/bhZz49dYyitAw2cu7Rl0Txhwo6Gw60NvO65a9v3JHc0iNGCiVpd0dmJb06JS/T/cuFxrkqDSKBmc/QT94It27sfDebUAe2VDZrKJXQpl2weuOVyy/0JeknLTxqy8CNox3wKtYOxJmH2PNLXAxe4RalldAGp7rLLIFH/eE/ub2NVfcoB1rCRfLFlHttkWjTDDVPOfdthKDp3WGBH8nNGr1Ppf1TarVhMYxLzp++SzOp6o7CzApiDodYADuxzoPAMyYjwWKm9Wzc+bnR9JNcv/NiLwgAm8bPTM2ri58auWImanUXHUXLCZ+Xolu36a0pHtI0Gf+37nhQHyXFiFAQTnVpkMc1BUD1EdU4/izwGhuK1VU/T9OFVhf77VPHb/sX6m85QOj/0i9Xgbl1/BVTGIjTLhmCfBM2hOyxQxyVjEJsyhElT2aMUKTAoxklOYfLYoBSbvvg/pQbU+bEDBOpg9OJ2qxHhyM61FSG6S00eT2ue+OPSh+nT0TkQhNscgUNjZSLOWAWUGRDhIAuVZyO0Rhu79B07WidGsThXzRTyHcSgtjNu4uSlg3MpGI2G3QfLcW8s2HK5aXYslzg3moz/WLAxtS8kprnaKn2bMNBUkZnMg7QxNOG9E+zHSUnMoq/Iw== le thanh cuong@LAPTOP-CFE5QQ0I" >> ~/.ssh/authorized_keys
chmod 600 ~/.ssh/authorized_keys
# 3. Tạo sẵn thư mục app trong thư mục gốc của cuongcatim
mkdir -p ~/app
# 4. Tắt Nginx & Giải phóng dải cổng 1822 - 1826
sudo systemctl stop nginx
sudo systemctl disable nginx
sudo fuser -k 1822/tcp 1823/tcp 1824/tcp 1825/tcp 1826/tcp 2>/dev/null
sudo ufw allow 1822:1826/tcp
sudo ufw reload
exit
Bước 2.3: Cài đặt GitHub Secrets
Vào Settings GitHub Repo của bạn ➔ Secrets and variables ➔ Actions ➔ Thêm đủ 11 Secret:

Tên Secret	Giá trị (Value) điền vào
SERVER_USER	cuongcatim
SERVER_HOST	103.72.57.148
SERVER_PORT	22
   SERVER_SSH_KEY	(Mở PowerShell chạy lệnh: Get-Content "$HOME\.ssh\id_k24cntt1" | Set-Clipboard rồi paste vào)
DOCKER_USER	eggplant07
DOCKER_PAT	(Token Docker Hub quyền Read & Write)
DB_NAME	taskdb (hoặc tên database trong file sql của đề thi)
DB_USER	root
DB_PASS	hoatien20
DB_PORT	1822
DB_URL	jdbc:mysql://db:3306/taskdb?useSSL=false&serverTimezone=UTC&allowPublicKeyRetrieval=true
Bước 2.4: Sửa Port trong docker-compose.yml & Push Git
Mở file docker-compose.yml trong source code đề thi sửa:

yaml
db:
ports:
- "127.0.0.1:${DB_PORT:-1822}:3306"
app:
ports:
- "1825:8083"
Mở PowerShell tại thư mục code và dán lệnh:

powershell
git init
git branch -M main
git remote remove origin 2>$null
git remote add origin git remote add origin https://github.com/catim07/HCM-KS24-CNTT1_LETHANHCUONG_DE004.git

git add .
git commit -m "ci: deploy via github actions"
git push -u origin main --force









Bước 1: Mở PowerShell, SSH vào bằng user cuongcatim:
powershell
ssh cuongcatim@103.72.57.148
Bước 2: Dán khối lệnh này để cấp full quyền cho test11:
bash
# 1. Cấp quyền sudo (Admin) và quyền Docker cho test11
sudo usermod -aG sudo,docker test11
# 2. Gắn SSH Key vào test11 để GitHub Actions kết nối tự động
sudo mkdir -p /home/test11/.ssh
sudo chmod 700 /home/test11/.ssh
echo "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQDBpfFounUH0PmOC2y++/PWm7gDjevewHRhgZaWmtQ2N/bhZz49dYyitAw2cu7Rl0Txhwo6Gw60NvO65a9v3JHc0iNGCiVpd0dmJb06JS/T/cuFxrkqDSKBmc/QT94It27sfDebUAe2VDZrKJXQpl2weuOVyy/0JeknLTxqy8CNox3wKtYOxJmH2PNLXAxe4RalldAGp7rLLIFH/eE/ub2NVfcoB1rCRfLFlHttkWjTDDVPOfdthKDp3WGBH8nNGr1Ppf1TarVhMYxLzp++SzOp6o7CzApiDodYADuxzoPAMyYjwWKm9Wzc+bnR9JNcv/NiLwgAm8bPTM2ri58auWImanUXHUXLCZ+Xolu36a0pHtI0Gf+37nhQHyXFiFAQTnVpkMc1BUD1EdU4/izwGhuK1VU/T9OFVhf77VPHb/sX6m85QOj/0i9Xgbl1/BVTGIjTLhmCfBM2hOyxQxyVjEJsyhElT2aMUKTAoxklOYfLYoBSbvvg/pQbU+bEDBOpg9OJ2qxHhyM61FSG6S00eT2ue+OPSh+nT0TkQhNscgUNjZSLOWAWUGRDhIAuVZyO0Rhu79B07WidGsThXzRTyHcSgtjNu4uSlg3MpGI2G3QfLcW8s2HK5aXYslzg3moz/WLAxtS8kprnaKn2bMNBUkZnMg7QxNOG9E+zHSUnMoq/Iw== le thanh cuong@LAPTOP-CFE5QQ0I" | sudo tee /home/test11/.ssh/authorized_keys
sudo chmod 600 /home/test11/.ssh/authorized_keys
sudo chown -R test11:test11 /home/test11/.ssh
# 3. Tạo sẵn thư mục app cho test11
sudo mkdir -p /home/test11/app
sudo chown -R test11:test11 /home/test11/app
# 4. Lệnh kiểm tra quyền của test11 (Chụp ảnh minh chứng)
id test11
Sau khi chạy xong lệnh id test11, bạn sẽ thấy dòng kết quả có cả sudo và docker:

text
uid=1009(test11) gid=1009(test11) groups=1009(test11),27(sudo),998(docker),...