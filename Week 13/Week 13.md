# Lab 01-03:

1. Chương trình đạt 66/72 score trên virustotal.

2. Chương trình đã bị pack.

# Lab 01-04:

1. Chương trình đạt 64/72 score trên virustotal.

2. Chương trình không bị pack.

3. Chương trình được tạo ra vào 2019-08-31.

4. Chương trình có gọi các hàm FindResourceA, SizeOfResourceA, LoadResourceA, WinExec để thực thi resource đi kèm với chương trình. Các hàm trong thư viện ADVAPI32 dùng để thực hiện hành vi leo quyền.

5. Trong chương trình có các chuỗi `\winup.exe`, `\system32\wupdmgrd.exe` có thể là các file thực thi chương trình tạo ra hoặc sửa đổi, chuỗi `http://www.practicalmalwareanalysis.com/updater.exe` có thể là nguồn của 1 file thực thi được tải về.

6. Phân tích resource cho thấy chương trình sẽ tải thêm mã độc từ đường dẫn `http://www.practicalmalwareanalysis.com/updater.exe`.

# Lab 03-01:

1. Chương trình import thư viện kernel32.dll và hàm ExitProcess.

2. Chương trình tạo ra một mutex tên là WinVMX32 và sao chép nó vào C:\Windows\System32\vmx32to64.exe xong tạo resistry key SOFTWARE\Microsoft\Windows\CurrentVersion\Run\VideoDriver để mỗi khi hệ thống khởi động thì chương trình chạy.

3. Chương trình sẽ kết nối tới URL ` www.practicalmalwareanalysis.com` và đọc 256 byte của URL đó.

# Lab 03-02:

1. Mã độc cài bằng cách rundll32.exe Lab03-02.dll, InstallA.

2. Chạy mã độc bằng cách khởi động service IPRIP: net start IPRIP.

3. Process đó là svchost.exe.

4. Có thể filter theo PID hoặc ProcessName.

5. Mã độc sẽ tự cài thông qua hàm InstallA và persistent bằng resgistry `SYSTEM\CurrentControlSet\Services\IPRIP`.

6. Mã độc có kết nối với URL `practicalmalwareanalysis.com`.

# Lab 03-03:

1. Mã độc sẽ hiện lên dòng thông báo của tiến trình svchost.exe.

2. Tiến trình svchost.exe bình thường sẽ không có chuỗi practicalmalwareanalysis.log trong memory còn tiến trình giả thì có.

3. Chương trình sẽ tạo ra file practicalmalwareanalysis.log.

4. Chương trình sẽ làm giả tiến trình svchost.exe để chạy keylogger và lưu toàn bộ dữ liệu vào file practicalmalwareanalysis.log.

# Lab 03-04:

1. Sau khi chạy chương trình, nó sẽ bị xóa.

2. Mã độc này phải chạy bằng các lệnh trong cmd.

3. Cách để chạy mã độc là truyền vào 1 trong 4 tham số `-in`, `-re`, `-c`, `-cc` và mật khẩu là `abcd`.
