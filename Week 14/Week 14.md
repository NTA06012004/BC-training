# Tạo folder và đổi tên

- Mã độc sẽ tạo ra thư mục `Explorer` cùng vị trí với file `explorer.exe`

  <img src = 'https://github.com/NTA06012004/BC-training/blob/main/Week%2014/File%20PNG/1.png'>

- Mã độc sẽ tạo ra file `Unikey.exe` trong thư mục `Explorer` và copy lại chính nó vào file đó

  <img src = 'https://github.com/NTA06012004/BC-training/blob/main/Week%2014/File%20PNG/2.png'>

- Sau 2 bước này mã độc sẽ có tên là `Unikey.exe` và được lưu trong đường dẫn `Explorer`.

# Persistent

- Mã độc sẽ tạo thêm giá trị trong `HKLM\\Software\\Microsoft\\Windows\\CurrentVersion\\Run` để chạy mỗi khi máy tính nạn nhân khởi động

  <img src = 'https://github.com/NTA06012004/BC-training/blob/main/Week%2014/File%20PNG/3.png'>

# Tạo ra file Tranfer.exe từ resource

- Mã độc tạo ra một file `Tranfer.exe` rỗng sau đó copy toàn bộ từ file resource có tên chữa chuỗi `exe` của mã độc vào file vừa tạo.

  <img src = 'https://github.com/NTA06012004/BC-training/blob/main/Week%2014/File%20PNG/4.png'>

- Phân tích file Tranfer.exe

  - File thực thi này viết bằng C# có chức năng nén các file lại và gửi từ gmail `anhthc95@gmail.com` đến gmail `hunganh1803@gmail.com`.
 
    <img src = 'https://github.com/NTA06012004/BC-training/blob/main/Week%2014/File%20PNG/5.png'>

# Lấy thông tin về vi xử lý 

- Mã độc sẽ truy vấn key `HKLM\\HARDWARE\\DESCRIPTION\\System\\CentralProcessor\\0` giá trị `ProcessorNameString` rồi ghi vào file `Systeminfo.txt`.

  <img src = 'https://github.com/NTA06012004/BC-training/blob/main/Week%2014/File%20PNG/6.png'>

# Tạo luồng để thực thi hàm

- Mã độc sẽ tạo một luồng để thực thi hàm:

  <img src = 'https://github.com/NTA06012004/BC-training/blob/main/Week%2014/File%20PNG/7.png'>

  
  <img src = 'https://github.com/NTA06012004/BC-training/blob/main/Week%2014/File%20PNG/11.png'>

- Phân tích hàm `sub_401320`:

  - Mã độc sẽ tạo ra file `Keylog.dll` bằng cách tạo file rỗng có tên như vậy và copy data từ resource có tên chứa chuỗi dll vào file rỗng đó.
 
    <img src = 'https://github.com/NTA06012004/BC-training/blob/main/Week%2014/File%20PNG/12.png'>

  - Tiếp theo, mã độc sẽ thực thi hàm `FillKeyboard` của dll bằng hàm `SetWindowsHookExW` với idHook là 2, mỗi khi có sự kiện bàn phím xảy ra hàm này sẽ được thực thi.
 
    <img src = 'https://github.com/NTA06012004/BC-training/blob/main/Week%2014/File%20PNG/8.png'>

  - Sau đó. mã độc sẽ thực thi hàm `SetGlobalHookHandle`
 
    <img src = 'https://github.com/NTA06012004/BC-training/blob/main/Week%2014/File%20PNG/9.png'>

- Phân tích hàm `FillKeyboard`

  - Hàm này tạo ra file `Log.txt` và ghi dữ liệu thời gian, các phím được ấn xuống vào đó.
 
  - Sau đó, hàm này sẽ gọi hàm `CallNextHookEx` để tiếp tục quá trình hook.
 
- Phân tích hàm `SetGlobalHookHandle`

  - Hàm này là hàm kiểm tra trạng thái hook của mã độc.
 
# Vòng lặp

- Mã độc sẽ chụp ảnh màn hình và lưu vào file `screen.jpeg`.

  <img src = 'https://github.com/NTA06012004/BC-training/blob/main/Week%2014/File%20PNG/10.png'>

- Sau đó, mã độc sẽ thực thi file `Tranfer.exe`.

- Cuối cùng, mã độc sẽ dừng 60s rồi tiếp tục vòng lặp.
