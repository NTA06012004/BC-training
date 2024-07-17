# Các lệnh Linux

- Xem thông tin OS:

  - Tên, phiên bản, kiến trúc: uname -a, hostnamectl, cat /etc/os-release
 
  - Danh sách các gói phần mềm được cài đặt: apt list --installed, dpkg --list
 
- Xem cấu hình máy:

  - CPU: lscpu, cat /proc/cpuinfo
 
  - RAM: free -h, cat /prco/meminfo
 
  - Ổ cứng: lsblk, df -h
 
- Quản lý tiến trình:

  - Xem danh sách các tiến trình đang chạy: Tên, PID, User, Lệnh để chạy tiến trình:
  
    `ps aux`
 
  - Tắt tiến trình theo PID:
  
   `kill PID`
  
   `kill -9 PID`
 
  - Tắt tiến trình theo tên:
  
    `killall name`

    `pkill -f name`
 
- Tìm kiếm file:

  - Tìm kiếm  
