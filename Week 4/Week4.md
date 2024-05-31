# Lý thuyết

# Bài tập

## WMI event subscription

```
#include <windows.h>
#include <wbemidl.h>
#include <comdef.h>

#pragma comment(lib, "wbemuuid.lib")

int main()
{
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
    _variant_t varQuery = "SELECT * FROM __InstanceCreationEvent WHERE TargetInstance ISA 'Win32_Process'";
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
    _variant_t varCommandLineTemplate = "notepad.exe";
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
