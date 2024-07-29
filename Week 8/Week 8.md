# Lý thuyết

- UID là mã định danh duy nhất của 1 người dùng trên hệ thống linux. Mỗi người dùng có một mã định danh khác nhau để xác định quyền truy cập của người đó đối với các tài nguyên hệ thống.

- EUID là mã định danh người dùng hiệu quả. Nó xác định quyền truy cập của người dùng hiện tại đối với tài nguyên hệ thống. EUID thường được sử dụng khi một chương trình hoặc quy trình cần có quyền truy cập cao hơn so với quyền của người dùng đang chạy nó.

- Owner là chủ sở hữu của một tệp hoặc một quy trình trên hệ thống. Chủ sở hữu thường có quyền kiểm soát cao nhất đối với tệp hoặc quy trình đó, bao gồm quyền thay đổi quyền truy cập, xóa, hoặc sửa đổi.
-
- MOD là quyền sửa đổi. Quyền này cho phép người dùng thực hiện các thay đổi đối với một tệp hoặc thư mục, bao gồm thêm, xóa, và thay đổi nội dung của tệp.

# Bài tập

## Bài 1:

```
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>

int main(int argc, char *argv[])
{
    if (argc != 2)
    {
        return 0;
    }

    char *username = argv[1];
    char command[200];
    sprintf(command, "passwd %s", username);
    system(command);
    return 0;
}

```

## Bài 2:

```
#include <stdio.h>
#include <stdlib.h>

int main()
{
	setreuid(geteuid(), geteuid());
    system("id");
    return 0;
}

```

Compile chương trình:

```
gcc -o run_id run_id.c
```

Sau đó, thiết lập quyền sở hữu và quyền setuid cho user2:

```
sudo chown user2:user2 run_id
sudo chmod u+s run_id
```
