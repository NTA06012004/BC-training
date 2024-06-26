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

- Kỹ thuật hijack DLL theo DLL Search Order là một phương pháp thường được sử dụng để thay thế một DLL hợp lệ bằng một DLL tấn công thông qua thứ tự tìm kiếm của hệ thống. Trong Windows, khi một ứng dụng cần tải một DLL, hệ thống sẽ theo dõi một thứ tự cụ thể của các vị trí để tìm kiếm DLL đó. Thứ tự này thường được gọi là DLL Search Order.
- Thứ tự tìm kiếm DLL theo mặc định trong Windows bao gồm các vị trí sau:
  - Thư mục của chương trình hiện tại.
  - Thư mục System32.
  - Thư mục Windows.
  - Các thư mục được đặt trong biến môi trường PATH.
  - Các thư mục được liệt kê trong Registry (được cài đặt thông qua các khóa Registry như HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Session Manager\KnownDLLs).
- Chương trình sẽ thay thế dll của thư mục đầu tiên được tìm kiếm từ đó dll này sẽ được load vào khi process chạy.

# Bài tập

## Bài 1:

```
#include <iostream>
#include <windows.h>
using namespace std;
int main(int argc, char **argv)
{
    if (argc == 3)
    {
        HMODULE hModule = LoadLibraryA(argv[1]);
        FARPROC function = GetProcAddress(hModule, argv[2]);
        function();
        FreeLibrary(hModule);
        return 0;
    }
    else
    {
        exit(0);
    }
}
```

## Bài 2:

```
#include <windows.h>
BOOL APIENTRY DllMain(HMODULE hModule,
    DWORD  ul_reason_for_call,
    LPVOID lpReserved
)
{
    switch (ul_reason_for_call)
    {
    case DLL_PROCESS_ATTACH:
        MessageBox(NULL, TEXT("Hi, I am Dll!"), TEXT("Text"), NULL);
        break;
    case DLL_THREAD_ATTACH:
    case DLL_THREAD_DETACH:
    case DLL_PROCESS_DETACH:
        break;
    }
    return TRUE;
}

```

## Bài 3:

- Dll

```
#include <windows.h>
#include <iostream>
#include <fstream>
using namespace std;
extern "C" __declspec(dllexport) void PrintMessage(const char* message) {
    cout << message << std::endl;
}

extern "C" __declspec(dllexport) void WriteToFile(const char* filename, const char *s) {
    ofstream outfile;
    outfile.open(filename, ios_base::app);
    outfile << s;
    outfile.close();
}

BOOL APIENTRY DllMain(HMODULE hModule,
    DWORD  ul_reason_for_call,
    LPVOID lpReserved
)
{
    switch (ul_reason_for_call)
    {
    case DLL_PROCESS_ATTACH:
    case DLL_THREAD_ATTACH:
    case DLL_THREAD_DETACH:
    case DLL_PROCESS_DETACH:
        break;
    }
    return TRUE;
}
```

- Bài 4
  - LoadLibrary Injection:

    - File Dll
   
    ```
    #include "pch.h"
    BOOL APIENTRY DllMain( HMODULE hModule,
                           DWORD  ul_reason_for_call,
                           LPVOID lpReserved
                         )
    {
        switch (ul_reason_for_call)
        {
        case DLL_PROCESS_ATTACH:
            MessageBoxExA(NULL, "Warnning!", "Dll Inject!", MB_OK, NULL);
            break;
        case DLL_THREAD_ATTACH:
        case DLL_THREAD_DETACH:
        case DLL_PROCESS_DETACH:
            break;
        }
        return TRUE;
    }
    ``` 

    - File exe
    
    ```
    #include<iostream>
    #include<windows.h>
    #include<fstream>
    #include<TlHelp32.h>
    #include<wchar.h>
    #include<string>
    #include"Resource1.h"
    using namespace std;
    int main()
    {
        HRSRC hRes = FindResourceA(NULL, MAKEINTRESOURCE(IDR_DLL1), "dll");
        HGLOBAL hResData = LoadResource(NULL, hRes);
        void* pDllBuffer = LockResource(hResData);
        DWORD dwSize = SizeofResource(NULL, hRes);
        ofstream dllFile("temp.dll", ios::binary);
        dllFile.write((char*)pDllBuffer, dwSize);
        dllFile.close();
        char path[MAX_PATH];
        GetModuleFileNameA(NULL, path, MAX_PATH);
        string strPath = string(path);
        size_t pos = strPath.find_last_of("\\/");
        if (std::string::npos != pos)
        {
            strPath = strPath.substr(0, pos);
        }
        strPath.append("\\temp.dll");
        const char* pathDll = strPath.c_str();
        while (TRUE)
        {
            PROCESSENTRY32 pe32;
            pe32.dwSize = sizeof(pe32);
            HANDLE hProcess = CreateToolhelp32Snapshot(TH32CS_SNAPPROCESS, 0);
            bool check = Process32First(hProcess, &pe32);
            while (check != FALSE)
            {
                if (strcmp(pe32.szExeFile, "Notepad.exe") == 0)
                {
                    HANDLE processHandle;
                    PVOID remoteBuffer;
                    processHandle = OpenProcess(PROCESS_VM_WRITE | PROCESS_VM_OPERATION | PROCESS_CREATE_THREAD, FALSE, pe32.th32ProcessID);
                    remoteBuffer = VirtualAllocEx(processHandle, NULL, strlen(pathDll) + 1, MEM_COMMIT | MEM_RESERVE, PAGE_READWRITE);
                    auto h = WriteProcessMemory(processHandle, remoteBuffer, (LPVOID)pathDll, strlen(pathDll) + 1, NULL);
                    PTHREAD_START_ROUTINE threatStartRoutineAddress = (PTHREAD_START_ROUTINE)GetProcAddress(GetModuleHandle(TEXT("Kernel32")), "LoadLibraryA");
                    CreateRemoteThread(processHandle, NULL, 0, threatStartRoutineAddress, remoteBuffer, 0, NULL);
                    CloseHandle(processHandle);
                    return 0;
                }
                check = Process32Next(hProcess, &pe32);
            }
            CloseHandle(hProcess);
        }
    }
  
    ```
  
  - SetWindowsHookEx:
 
    - File dll
   
    ```
    #include "pch.h"
    extern "C" __declspec(dllexport) int Hook()
    {
        MessageBoxA(NULL, "Inject!", "Warnning!", MB_OK);
        return 0;
    }
    BOOL APIENTRY DllMain( HMODULE hModule,
                           DWORD  ul_reason_for_call,
                           LPVOID lpReserved
                         )
    {
        switch (ul_reason_for_call)
        {
        case DLL_PROCESS_ATTACH:
        case DLL_THREAD_ATTACH:
        case DLL_THREAD_DETACH:
        case DLL_PROCESS_DETACH:
            break;
        }
        return TRUE;
    }
    ``` 
 
    - File exe

    ```
    #include<iostream>
    #include<windows.h>
    #include<fstream>
    #include<Tlhelp32.h>
    #include"resource.h"
    using namespace std;
    int main()
    {
        HRSRC hRes = FindResource(NULL, MAKEINTRESOURCE(IDR_DLL1), L"dll");
        HGLOBAL hResData = LoadResource(NULL, hRes);
        void* pDllBuffer = LockResource(hResData);
        DWORD dwSize = SizeofResource(NULL, hRes);
        ofstream dllFile("temp.dll", ios::binary);
        dllFile.write((char*)pDllBuffer, dwSize);
        dllFile.close();
        char path[MAX_PATH];
        GetModuleFileNameA(NULL, path, MAX_PATH);
        string strPath = string(path);
        size_t pos = strPath.find_last_of("\\/");
        if (std::string::npos != pos)
        {
            strPath = strPath.substr(0, pos);
        }
        strPath.append("\\temp.dll");
        const char* pathDll = strPath.c_str();
        while (TRUE)
        {
            DWORD pid = 0;
            PROCESSENTRY32 pe32;
            pe32.dwSize = sizeof(pe32);
            HANDLE hProcess = CreateToolhelp32Snapshot(TH32CS_SNAPPROCESS, NULL);
            bool check1 = Process32First(hProcess, &pe32);
            while (check1 != FALSE)
            {
                if (wcscmp(pe32.szExeFile, L"Notepad.exe") == 0)
                {
                    pid = pe32.th32ProcessID;
                    break;
                }
                check1 = Process32Next(hProcess, &pe32);
            }
            DWORD tid = 0;
            THREADENTRY32 te32;
            HANDLE hThread = CreateToolhelp32Snapshot(TH32CS_SNAPTHREAD, NULL);
            te32.dwSize = sizeof(te32);
            bool check2 = Thread32First(hThread, &te32);
            while (check2 != FALSE)
            {
                if (te32.th32OwnerProcessID == pid)
                {
                    tid = te32.th32ThreadID;
                    break;
                }
                check2 = Thread32Next(hThread, &te32);
            }
            HINSTANCE hDll;
            hDll = LoadLibraryA(pathDll);
            HHOOK hook = SetWindowsHookEx(WH_KEYBOARD, (HOOKPROC)GetProcAddress(hDll, "Hook"), hDll, tid);
        }
    }
    ```
