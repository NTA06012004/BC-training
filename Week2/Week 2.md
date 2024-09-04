# Lý thuyết

## Virtual memory

- Virtual memory là một kỹ thuật quản lý bộ nhớ cho phép mỗi ứng dụng được chia sẻ không gian bộ nhớ mà không cần lo lắng về việc quản lý vùng nhớ thực tế. Nó tạo ra không gian bộ nhớ mà từ quan điểm của ứng dụng, nó sở hữu toàn bộ không gian bộ nhớ

## Segmented memory, Paged memory

- Segmented memory (bộ nhớ phân đoạn): là một kỹ thuật quản lý bộ nhớ mà trong đó không gian của tiến trình được chia thành các đoạn có kích thước khác nhau.

  - Nguyên lý:
 
    - Phân đoạn: bộ nhớ của tiến trình được phân chia thành các đoạn có kích thước khác nhau.
   
    - Địa chỉ đoạn: mỗi địa chỉ bao gồm 2 phần: số hiệu đoạn và offset trong đoạn đó.
   
- Paged memory: là một kỹ thuật quản lý bộ nhớ trong đó, không gian địa chỉ của một tiến trình được chia thành các khối nhỏ có kích thước cố định. Các trang này sau đó được ánh xạ tới khung trang trong bộ nhớ vật lý

  - Nguyên lý:
 
    - Phân trang: Bộ nhớ ảo của tiến trình chia thành các trang cố định có kích thước 4KB.
   
    - Khung trang: Bộ nhớ vật lý được chia thành khung trang giống kích thước trang.
   
    - Bảng trang: Mỗi tiến trình có một bảng trang riêng chứa các mục ánh xạ từ số trang ảo sang số khung trang vật lý.
   
  - So sánh:
 
    - Paging nhanh hơn segmentation
## Andress Translation

- Là quá trình chuyển đổi địa chỉ ảo thành địa chỉ vật lý trong hệ thống máy tính.
 
## Alignment

- CPU luôn đọc ở kích thước word (4 bytes cho 32 bit)
 
- Việc này giúp đọc dữ liệu nhanh hơn.
 
## Protection Attributes

- Protection Attributes: là các thuộc tính được sử dụng để kiểm soát quyền truy cập vào các vùng bộ nhớ hoặc tài nguyên khác trong hệ thống máy tính. Các thuộc tính này giúp bảo vệ dữ liệu và đảm bảo rằng chỉ các tiến trình hoặc người dùng có quyền hợp lệ mới có thể truy cập hoặc thay đổi dữ liệu.

## Stack, Heap

- Stack

  - Khi một thread được tạo, bộ nhớ sẽ luôn dự trữ 1Mb cho user stack và 12kb cho kernel stack (32bit).
   
  - Lưu biến cục bộ, giá trị trả về của hàm, dùng để chuyền tham số cho hàm.
   
  - Các biến được tạo trên ngăn xếp nằm ngoài phạm vi và sẽ tự động được giải phóng.
   
- Heap là một vùng bộ nhớ động trong hệ thống, được sử dụng để cấp phát bộ nhớ cho các biến và đối tượng mà thời gian sống của chúng không được xác định trước.
 
  - Được lưu trong RAM giống stack.
   
  - Được dùng để phân bổ dữ liệu cho chương trình sử dụng.
   
  - Có thể xảy ra lỗi nếu bộ đệm quá lớn.
   


# Bài tập

## Bài 1

```
#include <iostream>
#include <Windows.h>
#include <winreg.h>
#include <iomanip>
#include <chrono>
#include <ctime>
#include <comdef.h>
#include <Wbemidl.h>
#pragma comment(lib, "wbemuuid.lib")
using namespace std;
void GetTime1()
{
    auto currentTime = chrono::system_clock::now();
    auto uptime = chrono::milliseconds(GetTickCount64());
    auto bootTime = currentTime - uptime;
    time_t bootTimeT = chrono::system_clock::to_time_t(bootTime);
    cout << ctime(&bootTimeT);
}
void GetTime2()
{
    HKEY hKey;
    DWORD installDate;
    DWORD dataSize = sizeof(installDate);
    RegOpenKeyEx(HKEY_LOCAL_MACHINE, "SOFTWARE\\Microsoft\\Windows NT\\CurrentVersion", 0, KEY_READ, &hKey);
    RegQueryValueEx(hKey, "InstallDate", NULL, NULL, reinterpret_cast<LPBYTE>(&installDate), &dataSize);
    time_t bootTimeT = installDate;
    cout << std::asctime(std::localtime(&bootTimeT));
}
void HotfixCountKey()
{
    HRESULT hres = CoInitializeEx(0, COINIT_MULTITHREADED);
    hres = CoInitializeSecurity(NULL, -1, NULL, NULL, RPC_C_AUTHN_LEVEL_DEFAULT, RPC_C_IMP_LEVEL_IMPERSONATE, NULL, EOAC_NONE, NULL);
    IWbemLocator* pLoc = NULL;
    hres = CoCreateInstance(CLSID_WbemLocator, 0, CLSCTX_INPROC_SERVER, IID_IWbemLocator, (LPVOID*)&pLoc);
    IWbemServices* pSvc = NULL;
    hres = pLoc->ConnectServer(_bstr_t(L"ROOT\\CIMV2"), NULL, NULL, 0, NULL, 0, 0, &pSvc);
    hres = CoSetProxyBlanket(pSvc, RPC_C_AUTHN_WINNT, RPC_C_AUTHZ_NONE, NULL, RPC_C_AUTHN_LEVEL_CALL, RPC_C_IMP_LEVEL_IMPERSONATE, NULL, EOAC_NONE);
    IEnumWbemClassObject* pEnumerator = NULL;
    hres = pSvc->ExecQuery(bstr_t("WQL"), bstr_t("SELECT * FROM Win32_QuickFixEngineering"), WBEM_FLAG_FORWARD_ONLY | WBEM_FLAG_RETURN_IMMEDIATELY, NULL, &pEnumerator);
    IWbemClassObject* pclsObj = NULL;
    ULONG uReturn = 0;
    while (pEnumerator) {
        HRESULT hr = pEnumerator->Next(WBEM_INFINITE, 1, &pclsObj, &uReturn);
        if (0 == uReturn)
            break;
        VARIANT vtProp;
        hr = pclsObj->Get(L"HotFixID", 0, &vtProp, 0, 0);
        wcout << left << setw(40) << "" << vtProp.bstrVal << endl;
        VariantClear(&vtProp);
        pclsObj->Release();
    }
    pSvc->Release();
    pLoc->Release();
    pEnumerator->Release();
    CoUninitialize();
}
void NetworkCardCountKey()
{
    HRESULT hres = CoInitializeEx(0, COINIT_MULTITHREADED);
    hres = CoInitializeSecurity(NULL, -1, NULL, NULL, RPC_C_AUTHN_LEVEL_DEFAULT, RPC_C_IMP_LEVEL_IMPERSONATE, NULL, EOAC_NONE, NULL);
    IWbemLocator* pLoc = NULL;
    hres = CoCreateInstance(CLSID_WbemLocator, 0, CLSCTX_INPROC_SERVER, IID_IWbemLocator, (LPVOID*)&pLoc);
    IWbemServices* pSvc = NULL;
    hres = pLoc->ConnectServer(_bstr_t(L"ROOT\\CIMV2"), NULL, NULL, 0, NULL, 0, 0, &pSvc);
    hres = CoSetProxyBlanket(
        pSvc,
        RPC_C_AUTHN_WINNT,           
        RPC_C_AUTHZ_NONE,            
        NULL,                        
        RPC_C_AUTHN_LEVEL_CALL,      
        RPC_C_IMP_LEVEL_IMPERSONATE, 
        NULL,                        
        EOAC_NONE                    
    );
    IEnumWbemClassObject* pEnumerator = NULL;
    hres = pSvc->ExecQuery(
        bstr_t("WQL"),
        bstr_t("SELECT * FROM Win32_NetworkAdapter WHERE NetConnectionStatus != NULL"),
        WBEM_FLAG_FORWARD_ONLY | WBEM_FLAG_RETURN_IMMEDIATELY,
        NULL,
        &pEnumerator);
    IWbemClassObject* pclsObj = NULL;
    ULONG uReturn = 0;
    while (pEnumerator) {
        HRESULT hr = pEnumerator->Next(WBEM_INFINITE, 1,
            &pclsObj, &uReturn);
        if (0 == uReturn)
            break;
        VARIANT vtProp;
        hr = pclsObj->Get(L"Name", 0, &vtProp, 0, 0);
        wcout << left << setw(40) << "" << vtProp.bstrVal << endl;
        VariantClear(&vtProp);
        pclsObj->Release();
    }
    pSvc->Release();
    pEnumerator->Release();
    pLoc->Release();
    CoUninitialize();
}
void ProcessorCountKey()
{
    long n, result, count = 0;
    wchar_t path[] = L"HARDWARE\\DESCRIPTION\\System\\CentralProcessor\\";
    wchar_t Processors[30][50]{};
    DWORD MHz[30]{};
    for (int i = 0; i < 30; i++)
    {
        HKEY key;
        DWORD type = 0, dataSize;
        wstring valueData;
        wstring keyPath = (path + to_wstring(i)).c_str();
        n = RegOpenKeyExW(HKEY_LOCAL_MACHINE, keyPath.c_str(), 0, KEY_QUERY_VALUE, &key);
        dataSize = 256 * sizeof(wchar_t);
        valueData.resize(dataSize / sizeof(wchar_t));
        wchar_t valueName[] = L"Identifier";
        result = RegQueryValueExW(key, valueName, 0, &type, reinterpret_cast<BYTE*>(&valueData[0]), &dataSize);
        wcsncpy(Processors[i], valueData.c_str(), 50);
        count++;
        dataSize = sizeof(DWORD);
        result = RegQueryValueExW(key, L"~MHz", 0, &type, (LPBYTE)&MHz[i], &dataSize);
    }
    wcout << count << L" Processor(s) Installed." << endl;
    for (int i = 0; i < count; i++)
        wcout << left << setw(40) << wstring(27, L' ') << L"[" + to_wstring(i + 1) + L"] " << Processors[i] << L" ~" << MHz[i] << L" MHz" << endl;
}
wchar_t* QueryValue(HKEY Hkey, const wchar_t* value, const wchar_t* path)
{
    long n, result;
    HKEY key;
    DWORD type = 0, size = 256;
    wchar_t productBuffer[256] = {};
    n = RegOpenKeyExW(Hkey, path, 0, KEY_QUERY_VALUE, &key);
    result = RegQueryValueExW(key, value, 0, 0, (LPBYTE)productBuffer, &size);
    RegCloseKey(key);
    wchar_t* res = (wchar_t*)calloc(256, sizeof(wchar_t));
    wcsncpy(res, productBuffer, 256);
    return res;
}
int main()
{
    wchar_t* Computername = QueryValue(HKEY_LOCAL_MACHINE, L"ComputerName", L"SYSTEM\\CurrentControlSet\\Control\\ComputerName\\ComputerName");
    wcout << left << setw(40) << "Host Name:" << setw(10) << Computername << endl;
    wchar_t* ProductName = QueryValue(HKEY_LOCAL_MACHINE, L"ProductName", L"SOFTWARE\\Microsoft\\Windows NT\\CurrentVersion");
    wcout << left << setw(40) << "OS Name:" << setw(10) << "Microsoft " << ProductName << endl;
    wcout << left << setw(40) << "OS Manufacturer: " << setw(10) << "Microsoft Corporation" << endl;
    wcout << left << setw(40) << "OS Configuration: " << setw(10) << "Standalone Workstation" << endl;
    wchar_t* OSBuild = QueryValue(HKEY_LOCAL_MACHINE, L"CurrentType", L"SOFTWARE\\Microsoft\\Windows NT\\CurrentVersion");
    wcout << left << setw(40) << "OS Build Type:" << setw(10) << OSBuild << endl;
    wchar_t* RegisteredOwner = QueryValue(HKEY_LOCAL_MACHINE, L"RegisteredOwner", L"SOFTWARE\\Microsoft\\Windows NT\\CurrentVersion");
    wcout << left << setw(40) << "RegisteredOwner:" << setw(10) << RegisteredOwner << endl;
    wchar_t* ProductId = QueryValue(HKEY_LOCAL_MACHINE, L"ProductId", L"SOFTWARE\\Microsoft\\Windows NT\\CurrentVersion");
    wcout << left << setw(40) << "Original Install Date: " << setw(10);
    GetTime2();
    wcout << left << setw(40) << "System Boot Time: ";
    GetTime1();
    wcout << left << setw(40) << "Product ID: " << setw(10) << ProductId << endl;
    wchar_t* BaseBoardManufacturer = QueryValue(HKEY_LOCAL_MACHINE, L"BaseBoardManufacturer", L"SYSTEM\\HardwareConfig\\Current");
    wcout << left << setw(40) << "System Manufacturer: " << setw(10) << BaseBoardManufacturer << endl;
    wchar_t* SystemProductName = QueryValue(HKEY_LOCAL_MACHINE, L"SystemProductName", L"SYSTEM\\HardwareConfig\\Current");
    wcout << left << setw(40) << "System Model: " << setw(10) << SystemProductName << endl;
    wchar_t* BuildArch = QueryValue(HKEY_LOCAL_MACHINE, L"DeviceDesc", L"SYSTEM\\ControlSet001\\Enum\\ROOT\\ACPI_HAL\\0000");
    wchar_t* lastSemicolon = wcsrchr(BuildArch, L';');
    wcout << left << setw(40) << "System Type: " << (lastSemicolon + 1) << endl;
    wcout << left << setw(40) << "Processor(s): ";
    ProcessorCountKey();
    wchar_t* BIOSVendor = QueryValue(HKEY_LOCAL_MACHINE, L"BIOSVendor", L"SYSTEM\\HardwareConfig\\Current");
    wchar_t* BIOSVersion = QueryValue(HKEY_LOCAL_MACHINE, L"BIOSVersion", L"SYSTEM\\HardwareConfig\\Current");
    wchar_t* BIOSReleaseDate = QueryValue(HKEY_LOCAL_MACHINE, L"BIOSReleaseDate", L"SYSTEM\\HardwareConfig\\Current");
    wcout << left << setw(40) << "BIOS Version: " << setw(10) << BIOSVendor << " " << BIOSVersion << ", " << BIOSReleaseDate << endl;
    TCHAR WinDir[MAX_PATH] = { 0 };
    GetWindowsDirectoryA(WinDir, MAX_PATH);
    wcout << left << setw(40) << "Windows Directory: " << setw(10) << WinDir << endl;
    TCHAR SysDir[MAX_PATH] = { 0 };
    GetSystemDirectoryA(SysDir, MAX_PATH);
    wcout << left << setw(40) << "System Directory: " << setw(10) << SysDir << endl;
    wchar_t* SystemPartition = QueryValue(HKEY_LOCAL_MACHINE, L"SystemPartition", L"SYSTEM\\Setup");
    wcout << left << setw(40) << "Boot Device: " << setw(10) << SystemPartition << endl;
    wcout << left << setw(40) << "System Locale: " << setw(10) << "Len-us;English (United States)" << endl;
    wcout << left << setw(40) << "Input Locale: " << setw(10) << "Len-us;English (United States)" << endl;
    wchar_t* GetKey = QueryValue(HKEY_LOCAL_MACHINE, L"TimeZoneKeyName", L"SYSTEM\\CurrentControlSet\\Control\\TimeZoneInformation");
    wstring TZP = wstring(L"SOFTWARE\\Microsoft\\Windows NT\\CurrentVersion\\Time Zones\\") + wstring(GetKey);
    wchar_t* TimeZone = QueryValue(HKEY_LOCAL_MACHINE, L"Display", TZP.c_str());
    wcout << left << setw(40) << "Time Zone: " << setw(10) << TimeZone << endl;
    MEMORYSTATUSEX AvailMem;
    AvailMem.dwLength = sizeof(AvailMem);
    GlobalMemoryStatusEx(&AvailMem);
    wcout << left << setw(40) << "Total Physical Memory: " << AvailMem.ullTotalPhys / (1024 * 1024) << " MB" << endl;
    wcout << left << setw(40) << "Available Physical Memory: " << AvailMem.ullAvailPhys / (1024 * 1024) << " MB" << endl;
    wcout << left << setw(40) << "Virtual Memory: Max Size: " << AvailMem.ullTotalPageFile / (1024 * 1024) << " MB" << endl;
    wcout << left << setw(40) << "Virtual Memory: Available: " << AvailMem.ullAvailPageFile / (1024 * 1024) << " MB" << endl;
    wcout << left << setw(40) << "Virtual Memory: In Use:    " << (AvailMem.ullTotalPageFile - AvailMem.ullAvailPageFile) / (1024 * 1024) << " MB" << endl;
    wchar_t* PageSys = QueryValue(HKEY_LOCAL_MACHINE, L"PagingFiles", L"SYSTEM\\CurrentControlSet\\Control\\Session Manager\\Memory Management");
    wcout << left << setw(40) << "Page File Location(s): " << "C" << PageSys + 1 << endl;
    wchar_t* Domain = QueryValue(HKEY_LOCAL_MACHINE, L"Domain", L"SYSTEM\\CurrentControlSet\\Services\\Tcpip\\Parameters");
    wcout << left << setw(40) << "Domain: " << setw(10) << Domain << endl;
    wchar_t* LogonServer = QueryValue(HKEY_USERS, L"LOGONSERVER", L"S-1-5-21-1210761906-4139111624-3390506840-1001\\Volatile Environment");
    wcout << left << setw(40) << "Logon Server: " << LogonServer << endl;
    wcout << left << setw(40) << "Hotfix(s): " << endl;
    HotfixCountKey();
    wcout << left << setw(40) << "Network Card(s): " << endl;
    NetworkCardCountKey();
    free(Computername);
    free(ProductName);
    free(RegisteredOwner);
    free(ProductId);
    free(SystemProductName);
    free(BuildArch);
    free(BIOSVendor);
    free(BIOSVersion);
    free(BIOSReleaseDate);
    free(BaseBoardManufacturer);
    free(SystemPartition);
    free(PageSys);
    free(Domain);
    free(GetKey);
    free(TimeZone);
    return 0;
}
```
