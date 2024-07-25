# Các lệnh Linux

- Xem thông tin OS:

  - Tên, phiên bản, kiến trúc: uname -a, hostnamectl, cat /etc/os-release
 
    ```
    uname -a
    cat /etc/os-release
    ```
 
  - Danh sách các gói phần mềm được cài đặt:
 
    ```
    apt list --installed
    ```
 
- Xem cấu hình máy:

  - CPU:
    
    ``` 
    lscpu
    cat /proc/cpuinfo
    ``` 

  - RAM:
    
    ```
    free -h
    cat /prco/meminfo
    ```
    
  - Ổ cứng:
    
    ```
    lsblk
    df -h
    ```
    
- Xem thông tin về mạng:

  - Xem thông tin địa chỉ IP, Gateway, DNS:
 
    ```
    ip addr show
    ip route show
    cat /etc/resolv.conf
    ```
  - Xem các kết nối và tiến trình tương ứng:
 
    ```
    ss -tuln
    ss -tp
    ```
- Quản lý tiến trình:

  - Xem danh sách các tiến trình đang chạy: Tên, PID, User, Lệnh để chạy tiến trình:
 
    ```
    ps aux
    ```
  
  - Tắt tiến trình theo PID:
    
     ```
     kill -9 PID
     ```
   
  - Tắt tiến trình theo tên:
 
    ```
    pkill -f name
    ```
 
- Tìm kiếm file:

  - Tìm kiếm theo tên
 
    ```
    find path -name filename: phân biệt chữ hoa, chữ thường
    find path -iname filename: không phân biệt chữ hoa, chữ thường.
    ```

  - Tìm kiếm theo owner / group
 
    ```
    find path -user username: theo owner
    find path -group groupname: theo group
    ```

  - Tìm theo thời gian chỉnh sửa / truy cập cuối:
 
    ```
    find path -mtime +n: tìm kiếm tệp được chỉnh sửa cách đây hơn n ngày
    find path -mtime -n: tìm kiếm tệp được chỉnh sửa trong n ngày qua
    ```

  - Tìm theo dung lượng:
 
    ```
    find path -size +nM: Tìm tệp có dung lượng lớn hơn n MB.
    find path -size -nM: Tìm tệp có dung lượng nhỏ hơn n MB.
    ```
 
  - Tìm kiếm theo nội dung file
 
    ```
    grep -r "search_string" /path/to/search: phân biệt chữ hoa, chữ thường.
    grep -ri "search_string" /path/to/search: không phân biệt chữ hoa, chữ thường.
    ```
- Đặt lệnh chạy định kỳ:

  - Chạy lệnh X định kỳ vào 0h00 mỗi ngày

    ```
    0 0 * * * X
    ```
    
  - Chạy lệnh Y định kỳ vào 8h00 từ thứ hai đến thứ sáu
 
    ```
    0 8 * * 1-5 Y
    ```
  - Chạy lệnh Z định kỳ mỗi 3h một lần vào ngày 15 của tháng
 
    ```
    0 */3 15 * * Z
    ```
  - Cú pháp
 
    ```
    * * * * * command
    | | | | |
    | | | | ----- Ngày trong tuần (0 - 7) (Chủ nhật là 0 hoặc 7)
    | | | ------- Tháng (1 - 12)
    | | --------- Ngày trong tháng (1 - 31)
    | ----------- Giờ (0 - 23)
    ------------- Phút (0 - 59)
    ```

- Phân quyền:

  - Tạo mới 3 user: UserA và UserB thuộc GroupX, UserC thuộc GroupY:
 
  ```
  # Tạo GroupX
  sudo groupadd GroupX
  
  # Tạo GroupY
  sudo groupadd GroupY

  # Tạo người dùng A thuộc GroupX
  sudo useradd -m -g GroupX UserA

  # Tạo người dùng B thuộc GroupX
  sudo useradd -m -g GroupX UserB

  # Tạo người dùng C thuộc GroupY
  sudo useradd -m -g GroupY UserC
  ```

  - Phân quyền file F1 chỉ cho phép thực thi bởi UserA / GroupX:
 
  ```
  # Tạo file F1
  sudo touch path\to\F1

  # Thay đổi quyền sở hữu file F1 sang UserA / GroupX
  sudo chown UserA:GroupX path\to\F1

  # Cấp quyền thực thi cho UserA / GroupX
  sudo chmod 750 path\to\F1
  ```

  - Phân quyền thư mục D1 cho phép mọi user có quyền đọc file bên trong thư mục nhưng chỉ UserA / GroupX được tạo file mới
 
  ```
  # Tạo thư mục D1
  sudo mkdir path\to\D1

  # Thay đổi quyền sở hữu sang UserA / GroupX
  sudo chown UserA:GroupX

  # Cấp quyền đọc cho mọi người và quyền ghi cho UserA và GroupX:
  sudo chmod 775 /path/to/D1
  sudo chmod +t /path/to/D1
  ```

  - Phân quyền thư mục D2 chỉ cho phép UserA / Group A được xem danh sách file trong thư mục đó
 
  ```
  # Tạo thư mục D2
  sudo mkdir D2

  # Phân quyền thư mục D2
  sudo chmod 710 D2  # Owner (UserA) có quyền đọc, ghi và thực thi; Group (GroupA) có quyền thực thi; Others không có quyền nào.
  sudo chown UserA:GroupA D2  # Gán thư mục D2 cho UserA và GroupA
  ``` 
# Luyện tập

## 3.1

```
#!/bin/bash

echo "Tên máy: $(hostname)"

echo "Tên bản phân phối: $(lsb_release -d)"

echo "Phiên bản hệ điều hành: $(uname -r)"

echo "Thông tin CPU:"
echo "Tên: $(lscpu | grep "Model name" | awk -F: '{print $2}')"
echo "Kiến trúc: $(lscpu | grep "Architecture" | awk -F: '{print $2}')"
echo "Tốc độ: $(lscpu | grep "CPU MHz" | awk -F: '{print $2}')"

echo "Tổng bộ nhớ vật lý: $(free -m | grep Mem | awk '{print $2}')"

echo "Ổ đĩa còn trống: $(df -m --total | grep 'total' | awk '{print $4}') MB"

echo "Danh sách địa chỉ IP: "
hostname -i 

echo "Danh sách user trên hệ thống: "
cut -d: -f1 /etc/passwd

echo "TThông tin các tiến trình đang chạy với quyền root: "
ps -U root -u root u | awk '{print $11}'

echo "Thông tin các port đang mở: "
sudo netstat -tuln | grep LISTEN | awk '{print $4}' | cut -d: -f2

echo "Danh sách các thư mục trên hệ thống cho phép other có quyền ghi:"
find / -type d -perm -o=w 2>/dev/null

echo "Danh sách các gói phần mềm được cài trên hệ thống:"
apt list --installed
```

## 3.2

```
#!/bin/bash

LOG_FILE="/var/log/checketc.log"

CURRENT_TIME=$(date +"%H:%M:%S %d/%m/%Y")

{
  echo "[Log checketc - $CURRENT_TIME]"

  # 1. Kiểm tra thư mục /etc có file nào được tạo mới không?
  echo "=== Danh sach file tao moi ==="
  find /etc -type f -cmin -30 | while read -r file; do
    echo "$file"
    if [ "$(file -b --mime-type "$file")" == "text/plain" ]; then
      head "$file"
    fi
  done

  # 2. Kiểm tra thư mục /etc có file nào thay đổi không?
  echo "=== Danh sach file sua doi ==="
  find /etc -type f -mmin -30 | while read -r file; do
    echo "$file"
  done

  # 3. Thư mục /etc có file nào bị xóa không?
  echo "=== Danh sach file bi xoa ==="
  comm -23 <(ls -1 /etc | sort) <(find /etc -type f | xargs -n 1 basename | sort) | while read -r file; do
    echo "$file"
  done

} >> "$LOG_FILE"

echo -e "Subject: Log checketc - $CURRENT_TIME\n\n$(cat "$LOG_FILE")" | sendmail root@localhost


# */30 * * * * /path/to/checketc.sh

```

# 3.3

```
#!/bin/bash

# Đường dẫn tới file lưu danh sách các phiên đăng nhập mới
LOG_FILE="/var/log/sshmonitor.log"

# Thời gian hiện tại
CURRENT_TIME=$(date +"%H:%M:%S %d/%m/%Y")

# Kiểm tra danh sách phiên đăng nhập mới và ghi vào file log
{
  echo "[SSH Monitor - $CURRENT_TIME]"

  # Lấy danh sách các phiên đăng nhập mới
  NEW_SESSIONS=$(journalctl _COMM=sshd --since "5 minutes ago" | grep "Accepted password\|Accepted publickey" | awk '{print $1, $2, $3, $10, $11}')

  # Ghi vào file log
  if [ -n "$NEW_SESSIONS" ]; then
    echo "=== Danh sach phiên dang nhap moi ==="
    echo "$NEW_SESSIONS"
  fi

} >> "$LOG_FILE"

# Kiểm tra xem có phiên đăng nhập mới hay không
if [ -s "$LOG_FILE" ]; then
  # Gửi email cho quản trị viên root@localhost
  mail -s "SSH Monitor - $CURRENT_TIME" root@localhost < "$LOG_FILE"
fi

# crontab -e
# */5 * * * * /path/to/sshmonitor.sh
```

# 3.4

1. Cài đặt apache2:

```
sudo apt install apache2
```

2. Tạo file html

```
echo "<h1>Hello world, apache2</h1>" | sudo tee /var/www/html/index1.html 
```

# 3.5

1. Tạo thư mục web1, web2

```
sudo mkdir /var/www/web1
sudo mkdir /var/www/web2
```

2. Tạo file html cho web1, web2

```
echo "<h1>Hello web1</h1>" | sudo tee /var/www/web1.com/index.html 
echo "<h1>Hello web2</h1>" | sudo tee /var/www/web2.com/index.html 
```

3. Tạo file cấu hình cho web1, web2

- Web1
```
- Tạo file
sudo nano /etc/apache2/sites-available/web1.conf

- Ghi vào file
<VirtualHost *:80>
    ServerAdmin webmaster@web1.com
    ServerName web1.com
    DocumentRoot /var/www/web1
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```

- Web2
```
- Tạo file
sudo nano /etc/apache2/sites-available/web2.conf

- Ghi vào file
<VirtualHost *:80>
    ServerAdmin webmaster@web1.com
    ServerName web1.com
    DocumentRoot /var/www/web1
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```

4. Cấu hình Virtual host

```
sudo a2ensite web1.conf
sudo a2ensite web2.conf
```

5. Reset lại apache2

```
sudo systemctl restart apache2
```

# 3.5

1. Cài MySql

```
sudo apt update
sudo apt install mysql-server
sudo mysql_secure_installation
```

2. Cài PHP

```
sudo apt install php libapache2-mod-php php-mysql php-curl php-gd php-mbstring php-xml php-xmlrpc php-soap php-intl php-zip
```

3. Cài Wordpress

3.1. Cài Wordpress

```
cd /tmp
wget https://wordpress.org/latest.tar.gz
tar xf latest.tar.gz
```

3.2. Di chuyển WordPress vào thư mục DocumentRoot của Virtual Host web1:

```
sudo cp -R /tmp/wordpress/* /var/www/web1/
```

3.3. Tạo cơ sở dữ liệu MySQL cho WordPress:

```
sudo mysql -u root -p

- Ghi vào mysql

CREATE DATABASE wordpress;
CREATE USER 'wordpressuser'@'localhost' IDENTIFIED BY 'password';
GRANT ALL PRIVILEGES ON wordpress.* TO 'wordpressuser'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```

3.4. Cấu hình Wordpress

```
sudo cp /var/www/web1/wp-config-sample.php /var/www/web1/wp-config.php
sudo nano /var/www/web1/wp-config.php

Sửa thông tin file

define('DB_NAME', 'wordpress');
define('DB_USER', 'wordpressuser');
define('DB_PASSWORD', 'password');
define('DB_HOST', 'localhost');
```

3.5. Phân quyền cho WordPress

```
sudo chown -R www-data:www-data /var/www/web1 & sudo chmod -R 755 /var/www/web1
```

3.6. Khởi động lại Apache và MySQL:

```
sudo service apache2 restart
sudo service mysql restart
```
