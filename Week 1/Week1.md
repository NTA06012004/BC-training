# Lý thuyết

- Process: Một process là một chương trình đang hoạt động trên máy tính. Nó bao gồm mã máy (executable code), dữ liệu, và tài nguyên hệ thống EPROCESS. Cấu trúc dữ liệu liên quan:

  - Process Control Block (PCB) là một cấu trúc dữ liệu trong hệ điều hành chứa thông tin về một process như địa chỉ của process, trạng thái, thanh ghi CPU,...

  - Process Environment Block (PEB) liên kết giữa hai cấu trúc này thường được thể hiện qua trường Peb trong EPROCESS.

- Thread: Một thread là một luồng thực thi trong một process. Một process có thể chứa nhiều thread chia sẻ các tài nguyên của process cha ETHREADS. Cấu trúc dữ liệu liên quan:

  - Thread Control Block (TCB) chứa thông tin về một thread như địa chỉ của thread, trạng thái, thanh ghi CPU,...

  - Thread Environment Block (TEB) liên kết giữa hai cấu trúc này thường được thể hiện qua trường Teb trong ETHREAD.

- Event: Là một cơ chế đồng bộ hóa được sử dụng để đồng bộ hóa các hoạt động giữa các process hoặc các thread.
Cấu trúc dữ liệu liên quan: EVENT_OBJECT trong Windows API.

- Handle: Là một cơ chế cho phép quản lý và truy cập tài nguyên hệ thống như tệp tin, ổ đĩa, socket, v.v.

- Session: Một session đại diện cho một phiên làm việc người dùng trên hệ thống hoặc một phiên tương tác giữa các event, handle với nhau. Trên Windows, mỗi phiên đăng nhập (logon session) sẽ có một ID duy nhất.

# Bài tập

## Bài 1:

```
#include <iostream>
#include <windows.h>
#include <tlhelp32.h>
#include <iomanip>
#include <psapi.h>
using namespace std;
int main()
{
    PROCESSENTRY32 pe32;
    pe32.dwSize = sizeof(pe32);
    HANDLE hProcess = CreateToolhelp32Snapshot(TH32CS_SNAPPROCESS, 0);
    bool check = Process32First(hProcess, &pe32);
    cout << left << setw(40) << "Image Name" << setw(10) << "PID" << setw(10) << "Mem usage" << endl;
    while (check != FALSE)
    {
        PROCESS_MEMORY_COUNTERS pmc;
        HANDLE hRes = OpenProcess(PROCESS_ALL_ACCESS, FALSE, pe32.th32ProcessID); 
        GetProcessMemoryInfo(hRes, &pmc, sizeof(pmc));
        wcout << left << setw(40) << pe32.szExeFile << setw(10) << pe32.th32ProcessID << setw(10) << pmc.WorkingSetSize / 1024 << " K" << endl;
        check = Process32Next(hProcess, &pe32);
    }
    CloseHandle(hProcess);
}
```

## Bài 2:

```
#include <iostream>
#include <windows.h>
#include <tlhelp32.h>
using namespace std;
void KillPID(int n)
{
    HANDLE hDel = OpenProcess(PROCESS_TERMINATE, FALSE, n);
    if (hDel != NULL)
    {
        TerminateProcess(hDel, 0);
        CloseHandle(hDel);
    }
    else
    {
        cout << "No process!";
        CloseHandle(hDel);
    }
}
void KillIM(char *s)
{
    PROCESSENTRY32 pe32;
    pe32.dwSize = sizeof(pe32);
    HANDLE hProcess = CreateToolhelp32Snapshot(TH32CS_SNAPPROCESS, 0);
    bool check = Process32First(hProcess, &pe32);
    bool ok = 0;
    while (check != FALSE)
    {
        if (strcmp(pe32.szExeFile, s) == 0)
        {
            ok = 1;
            HANDLE hDel = OpenProcess(PROCESS_TERMINATE, FALSE, pe32.th32ProcessID);
            TerminateProcess(hDel, 0);
            CloseHandle(hDel);
        }
        check = Process32Next(hProcess, &pe32);
    }
    if (ok == 0)
    {
        cout << "No process!";
    }
    CloseHandle(hProcess);
}
int main(int argc, char **argv)
{
    if (argc != 3)
    {
        cout << "Error!";
    }
    else
    {
        if (strcmp(argv[1], "IM") == 0)
        {
            KillIM(argv[2]);
        }
        if (strcmp(argv[1], "PID") == 0)
        {
            KillPID(stoll(argv[2]));
        }
    }
}
```

## Bài 3:

```
#include <iostream>
#include <windows.h>
#include <tlhelp32.h>
#include <fstream>
#include <tchar.h>
#include <algorithm>
#include <vector>
using namespace std;
void cntword(double time)
{
    FILETIME createTime, exitTime, kernelTime, userTime;
    ULONGLONG startTime, endTime;
    GetThreadTimes(GetCurrentThread(), &createTime, &exitTime, &kernelTime, &userTime);
    startTime = (ULONGLONG)createTime.dwHighDateTime << 32 + createTime.dwLowDateTime;
    fstream file;
    file.open("1.txt");
    string data;
    int cnt = 0;
    while (file >> data)
    {
        cnt++;
    }
    file.close();
    GetThreadTimes(GetCurrentThread(), &createTime, &exitTime, &kernelTime, &userTime);
    endTime = (ULONGLONG)createTime.dwHighDateTime << 32 + createTime.dwLowDateTime;
    time = (double)(endTime - startTime) / 1e9;
}
void cntfile(double time)
{
    FILETIME createTime, exitTime, kernelTime, userTime;
    ULONGLONG startTime, endTime;
    GetThreadTimes(GetCurrentThread(), &createTime, &exitTime, &kernelTime, &userTime);
    startTime = (ULONGLONG)createTime.dwHighDateTime << 32 + createTime.dwLowDateTime;
    int cnt = 0;
    WIN32_FIND_DATA findfile;
    HANDLE hfind = FindFirstFile(_T("*.*"), &findfile);
    if (hfind != INVALID_HANDLE_VALUE)
    {
        do
        {
            cnt++;
        } while (FindNextFile(hfind, &findfile));
    }
    GetThreadTimes(GetCurrentThread(), &createTime, &exitTime, &kernelTime, &userTime);
    endTime = (ULONGLONG)createTime.dwHighDateTime << 32 + createTime.dwLowDateTime;
    time = (double)(endTime - startTime) / 1e9;
}
void cntthread(double time)
{
    FILETIME createTime, exitTime, kernelTime, userTime;
    ULONGLONG startTime, endTime;
    GetThreadTimes(GetCurrentThread(), &createTime, &exitTime, &kernelTime, &userTime);
    startTime = (ULONGLONG)createTime.dwHighDateTime << 32 + createTime.dwLowDateTime;
    int cnt = 0;
    HANDLE hsnap = CreateToolhelp32Snapshot(TH32CS_SNAPPROCESS, 0);
    PROCESSENTRY32 pe32;
    pe32.dwSize = sizeof(pe32);
    if (Process32First(hsnap, &pe32))
    {
        do
        {
            if (!strcmp((char *)pe32.szExeFile, "explorer.exe"))
            {
                do
                {
                    cnt += pe32.cntThreads;
                } while (Process32Next(hsnap, &pe32));
            }
        } while (Process32Next(hsnap, &pe32));
    }
    GetThreadTimes(GetCurrentThread(), &createTime, &exitTime, &kernelTime, &userTime);
    endTime = (ULONGLONG)createTime.dwHighDateTime << 32 + createTime.dwLowDateTime;
    time = (double)(endTime - startTime) / 1e9;
}
int main()
{
    HANDLE h1, h2, h3;
    DWORD thread1, thread2, thread3;
    double t1, t2, t3;
    h1 = CreateThread(NULL, 0, (LPTHREAD_START_ROUTINE)cntword, &t1, 0, &thread1);
    h2 = CreateThread(NULL, 0, (LPTHREAD_START_ROUTINE)cntfile, &t2, 0, &thread2);
    h3 = CreateThread(NULL, 0, (LPTHREAD_START_ROUTINE)cntthread, &t3, 0, &thread3);
    HANDLE handle[3] = {h1, h2, h3};
    WaitForMultipleObjects(3, handle, TRUE, INFINITE);
    CloseHandle(h1);
    CloseHandle(h2);
    CloseHandle(h3);
    // cout << t1 << endl << t2 << endl << t3 << endl;
    vector<pair<double, string>> results;
    results.push_back(make_pair(t1, "Thread 1"));
    results.push_back(make_pair(t2, "Thread 2"));
    results.push_back(make_pair(t3, "Thread 3"));
    sort(results.begin(), results.end());
    for (int i = 0; i < 3; i++)
    {
        cout << results[i].second << ": Giai " << (i + 1) << endl;
    }
    return 0;
}
```

# Tài liệu tham khảo

- `PROCESSENTRY32 structure`: https://learn.microsoft.com/en-us/windows/win32/api/tlhelp32/ns-tlhelp32-processentry32

- `CreateToolhelp32Snapshot function`: https://learn.microsoft.com/en-us/windows/win32/api/tlhelp32/nf-tlhelp32-createtoolhelp32snapshot

- `Process32First function`: https://learn.microsoft.com/en-us/windows/win32/api/tlhelp32/nf-tlhelp32-process32first

- `Process32Next function`: https://learn.microsoft.com/en-us/windows/win32/api/tlhelp32/nf-tlhelp32-process32next

- `OpenProcess function`: https://learn.microsoft.com/en-us/windows/win32/api/processthreadsapi/nf-processthreadsapi-openprocess

- `TerminateProcess function`: https://learn.microsoft.com/en-us/windows/win32/api/processthreadsapi/nf-processthreadsapi-terminateprocess

- `CloseHandle function`: https://learn.microsoft.com/en-us/windows/win32/api/handleapi/nf-handleapi-closehandle

- `FILETIME structure`: https://learn.microsoft.com/en-us/windows/win32/api/minwinbase/ns-minwinbase-filetime

- `GetThreadTimes function`: https://learn.microsoft.com/en-us/windows/win32/api/processthreadsapi/nf-processthreadsapi-getthreadtimes

- `WIN32_FIND_DATAA structure`: https://learn.microsoft.com/en-us/windows/win32/api/minwinbase/ns-minwinbase-win32_find_dataa

- `FindFirstFileA function`: https://learn.microsoft.com/en-us/windows/win32/api/fileapi/nf-fileapi-findfirstfilea

- `FindNextFileA function`: https://learn.microsoft.com/en-us/windows/win32/api/fileapi/nf-fileapi-findnextfilea

- `CreateThread function`: https://learn.microsoft.com/en-us/windows/win32/api/processthreadsapi/nf-processthreadsapi-createthread

- `WaitForMultipleObjects function`: https://learn.microsoft.com/en-us/windows/win32/api/synchapi/nf-synchapi-waitformultipleobjects
