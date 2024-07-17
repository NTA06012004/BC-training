# Các lệnh Linux

- Xem thông tin OS:

  - Tên, phiên bản, kiến trúc: uname -a, hostnamectl, cat /etc/os-release
 
    ```
    uname -a
    cat /etc/osrelease
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
 
    - 
