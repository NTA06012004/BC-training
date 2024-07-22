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
PREV_STATE="/var/log/checketc.prev"
CURR_STATE="/var/log/checketc.curr"

# Ghi lại trạng thái hiện tại của thư mục /etc
find /etc -type f -exec stat --format '%n %Y' {} \; > "$CURR_STATE"

# Kiểm tra xem lần chạy trước đã tồn tại chưa
if [ ! -f "$PREV_STATE" ]; then
    cp "$CURR_STATE" "$PREV_STATE"
    exit 0
fi

# So sánh trạng thái trước và hiện tại để tìm sự thay đổi
NEW_FILES=$(comm -13 <(sort "$PREV_STATE") <(sort "$CURR_STATE"))
MODIFIED_FILES=$(comm -12 <(sort "$PREV_STATE") <(sort "$CURR_STATE") | cut -d ' ' -f1)
DELETED_FILES=$(comm -23 <(sort "$PREV_STATE") <(sort "$CURR_STATE") | cut -d ' ' -f1)

# Ghi log thông tin về các file mới, file thay đổi và file bị xóa
{
    echo "========================================"
    echo "Thời gian: $(date)"
    
    # Kiểm tra file mới
    if [ -n "$NEW_FILES" ]; then
        echo "Các file mới được tạo:"
        echo "$NEW_FILES"
        
        # Hiển thị 10 dòng đầu của các file text mới
        for FILE in $NEW_FILES; do
            if file "$FILE" | grep -q 'text'; then
                echo "10 dòng đầu của $FILE:"
                head -n 10 "$FILE"
            fi
        done
    fi
    
    # Kiểm tra file bị thay đổi
    if [ -n "$MODIFIED_FILES" ]; then
        echo "Các file bị thay đổi:"
        echo "$MODIFIED_FILES"
    fi
    
    # Kiểm tra file bị xóa
    if [ -n "$DELETED_FILES" ]; then
        echo "Các file bị xóa:"
        echo "$DELETED_FILES"
    fi
} >> "$LOG_FILE"

# Gửi email log cho quản trị viên
mail -s "Thông tin kiểm tra thư mục /etc" root@localhost < "$LOG_FILE"

# Cập nhật trạng thái hiện tại thành trạng thái trước
mv "$CURR_STATE" "$PREV_STATE"
```

# 3.3

```
#!/bin/bash

LOG_FILE="/var/log/sshmonitor.log"
PREV_LOGINS="/var/log/sshmonitor.prev"
CURR_LOGINS="/var/log/sshmonitor.curr"

# Ghi lại các phiên đăng nhập SSH hiện tại
who | grep 'pts/' > "$CURR_LOGINS"

# Kiểm tra xem lần chạy trước đã tồn tại chưa
if [ ! -f "$PREV_LOGINS" ]; then
    cp "$CURR_LOGINS" "$PREV_LOGINS"
    exit 0
fi

# Tìm các phiên đăng nhập mới
NEW_LOGINS=$(comm -13 <(sort "$PREV_LOGINS") <(sort "$CURR_LOGINS"))

# Nếu có phiên đăng nhập mới, ghi log và gửi email cho quản trị viên
if [ -n "$NEW_LOGINS" ]; then
    {
        echo "========================================"
        echo "Thời gian: $(date)"
        echo "Phiên đăng nhập SSH mới:"
        echo "$NEW_LOGINS"
    } >> "$LOG_FILE"
    
    # Gửi email log cho quản trị viên
    mail -s "Thông tin phiên đăng nhập SSH mới" root@localhost < "$LOG_FILE"
fi

# Cập nhật trạng thái hiện tại thành trạng thái trước
mv "$CURR_LOGINS" "$PREV_LOGINS"
```
