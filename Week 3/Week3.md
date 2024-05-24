# Lý thuyết

## Định nghĩa

- DLL là viết tắt của Dynamic Link Library - Thư viện liên kết động trong lập trình máy tính. Đó là một module chứa các hàm và dữ liệu mà có thể được sử dụng bởi nhiều chương trình khác nhau cùng một lúc.
- Ưu điểm của DLL:
  - Chia sẻ Code và Dữ liệu: DLL giúp tất cả các chương trình chia sẻ cùng một DLL và chia sẻ cùng code và dữ liệu.
  - Tiết kiệm Bộ Nhớ: DLL giúp tiết kiệm bộ nhớ trong quá trình thực hiện các ứng dụng. Bởi vì DLL không cần nạp vào bộ nhớ cho tới khi chương trình gọi nó.

## Cách thức code, build và load dll

- Code dll bằng visual studio, Create a new project -> Dynamic-Link Library.

- Load dll bằng hàm LoadLibrary và dùng hàm GetProcAddress để lấy địa chỉ của hàm cần dùng.

## Các kỹ thuật inject dll

### LoadLibrary injection

- Chương trình sẽ tạo một vùng nhớ trống trong memory của một process, ghi dữ liệu của dll vào đó rồi dùng hàm CreateRemoteThread để load dll vừa ghi.

### Inject dll sử dụng SetWindowHookEx

- Hàm SetWindowsHookEx là hàm để cài đặt một hook vào chương trình để theo dõi một sự kiện nhất định.

- Chương trình sẽ dùng hàm này cài một hook để theo dõi một sự kiện của một thread, mỗi khi sự kiện xảy ra, chương trình sẽ chạy một hàm hook.

### Dll sideloading

- 
