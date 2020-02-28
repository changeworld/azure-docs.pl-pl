---
title: Dokumentacja interfejsu API usługi Azure Application Insights Agent
description: Dokumentacja interfejsu API agenta Application Insights. Enable-ApplicationInsightsMonitoring. Monitorowanie wydajności witryny sieci Web bez ponownego wdrażania witryny sieci Web. Współpracuje z usługą ASP.NET Web Apps hostowaną lokalnie, na maszynach wirtualnych lub na platformie Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 8bbdc96a49fffc91f80d24a9eb0926766f86ee16
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77671311"
---
# <a name="application-insights-agent-api-enable-applicationinsightsmonitoring"></a>Interfejs API agenta Application Insights: Enable-ApplicationInsightsMonitoring

W tym artykule opisano polecenie cmdlet, które jest członkiem [modułu programu PowerShell AZ. ApplicationMonitor](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

## <a name="description"></a>Opis

Włącza bezkodowe monitorowanie aplikacji usług IIS na komputerze docelowym.

To polecenie cmdlet zmodyfikuje plik applicationHost. config usług IIS i ustawi niektóre klucze rejestru.
Utworzy również plik ApplicationInsights. iKey. config, który definiuje klucz Instrumentacji używany przez poszczególne aplikacje.
Usługi IIS będą ładować RedfieldModule przy uruchamianiu, co spowoduje wstrzyknięcie zestawu SDK Application Insights do aplikacji w miarę uruchamiania aplikacji.
Uruchom ponownie usługi IIS, aby zmiany zaczęły obowiązywać.

Po włączeniu monitorowania zalecamy korzystanie z [metryk na żywo](live-stream.md) , aby szybko sprawdzić, czy aplikacja wysyła do nas dane telemetryczne.


> [!NOTE] 
> - Aby rozpocząć, musisz dysponować kluczem Instrumentacji. Aby uzyskać więcej informacji, zobacz [Tworzenie zasobu](create-new-resource.md#copy-the-instrumentation-key).
> - To polecenie cmdlet wymaga przejrzenia i zaakceptowania naszych licencji i zasad zachowania poufności informacji.

> [!IMPORTANT] 
> To polecenie cmdlet wymaga sesji programu PowerShell z uprawnieniami administratora i zasadami wykonywania podwyższonego poziomu uprawnień. Aby uzyskać więcej informacji, zobacz [Uruchamianie programu PowerShell jako administrator z zasadami wykonywania podwyższonego poziomu uprawnień](status-monitor-v2-detailed-instructions.md#run-powershell-as-admin-with-an-elevated-execution-policy).

## <a name="examples"></a>Przykłady

### <a name="example-with-a-single-instrumentation-key"></a>Przykład z pojedynczym kluczem Instrumentacji
W tym przykładzie wszystkie aplikacje na bieżącym komputerze mają przypisany pojedynczy klucz Instrumentacji.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

### <a name="example-with-an-instrumentation-key-map"></a>Przykład z mapą kluczy Instrumentacji
W tym przykładzie:
- `MachineFilter` dopasowuje bieżący komputer przy użyciu symbolu wieloznacznego `'.*'`.
- `AppFilter='WebAppExclude'` zapewnia `null` klucz Instrumentacji. Określona aplikacja nie będzie Instrumentacją.
- `AppFilter='WebAppOne'` przypisuje określoną aplikację unikatowy klucz Instrumentacji.
- `AppFilter='WebAppTwo'` przypisuje określoną aplikację unikatowy klucz Instrumentacji.
- Na koniec `AppFilter` używa również symbolu wieloznacznego `'.*'`, aby dopasować wszystkie aplikacje sieci Web, które nie są zgodne ze starszymi regułami i przypisać domyślny klucz Instrumentacji.
- Spacje są dodawane do czytelności.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap 
    @(@{MachineFilter='.*';AppFilter='WebAppExclude'},
      @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'}},
      @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'}},
      @{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'}})

```


## <a name="parameters"></a>Parametry

### <a name="-instrumentationkey"></a>-InstrumentationKey
**Wymagane.** Użyj tego parametru, aby dostarczyć pojedynczy klucz instrumentacji do użycia przez wszystkie aplikacje na komputerze docelowym.

### <a name="-instrumentationkeymap"></a>-InstrumentationKeyMap
**Wymagane.** Użyj tego parametru, aby podać wiele kluczy Instrumentacji i mapowanie kluczy Instrumentacji używanych przez poszczególne aplikacje.
Można utworzyć pojedynczy skrypt instalacyjny dla kilku komputerów, ustawiając `MachineFilter`.

> [!IMPORTANT]
> Aplikacje będą zgodne z regułami w kolejności, w jakiej są udostępniane reguły. Dlatego należy najpierw określić najbardziej szczegółowe reguły i najbardziej ogólne reguły.

#### <a name="schema"></a>Schemat
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'}})`

- **MachineFilter** to wymagane C# wyrażenie regularne nazwy komputera lub maszyny wirtualnej.
    - ". *" będzie pasować do wszystkich
    - Nazwa "ComputerName" będzie pasować tylko do komputerów o określonej nazwie.
- **AppFilter** to wymagane C# wyrażenie regularne nazwy witryny usług IIS. Listę witryn na serwerze można uzyskać, uruchamiając polecenie [Get-iissite](https://docs.microsoft.com/powershell/module/iisadministration/get-iissite).
    - ". *" będzie pasować do wszystkich
    - Wartość "sitename" będzie pasować tylko do witryny usług IIS z określoną dokładną nazwą.
- **InstrumentationKey** jest wymagana, aby umożliwić monitorowanie aplikacji, które pasują do poprzednich dwóch filtrów.
    - Pozostaw tę wartość null, jeśli chcesz zdefiniować reguły do wykluczenia monitorowania.


### <a name="-enableinstrumentationengine"></a>-EnableInstrumentationEngine
**Obowiązkowe.** Użyj tego przełącznika, aby umożliwić aparatowi Instrumentacji zbieranie zdarzeń i komunikatów na temat tego, co się dzieje w trakcie wykonywania procesu zarządzanego. Te zdarzenia i komunikaty obejmują kody wyników zależności, zlecenia HTTP i tekst polecenia SQL.

Aparat Instrumentacji dodaje obciążenie i jest domyślnie wyłączony.

### <a name="-acceptlicense"></a>-AcceptLicense
**Obowiązkowe.** Ten przełącznik umożliwia zaakceptowanie licencji i zasad zachowania poufności informacji w instalacjach bezobsługowych.

### <a name="-ignoresharedconfig"></a>-IgnoreSharedConfig
W przypadku klastra serwerów sieci Web może być używana [Konfiguracja udostępniona](https://docs.microsoft.com/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211).
Nie można wstrzyknąć modułu HttpModule do tej konfiguracji udostępnionej.
Ten skrypt zakończy się niepowodzeniem z komunikatem, że wymagane jest wykonanie dodatkowych czynności instalacyjnych.
Użyj tego przełącznika, aby zignorować to sprawdzenie i kontynuować instalowanie wymagań wstępnych. Aby uzyskać więcej informacji, zobacz [znane konflikty z-with-IIS — konfiguracja udostępniona](status-monitor-v2-troubleshoot.md#conflict-with-iis-shared-configuration)

### <a name="-verbose"></a>-Verbose
**Wspólny parametr.** Użyj tego przełącznika, aby wyświetlić szczegółowe dzienniki.

### <a name="-whatif"></a>-WhatIf 
**Wspólny parametr.** Za pomocą tego przełącznika można testować i weryfikować parametry wejściowe bez faktycznego włączenia monitorowania.

## <a name="output"></a>Dane wyjściowe


#### <a name="example-output-from-a-successful-enablement"></a>Przykładowe dane wyjściowe z pomyślnego włączenia

```
Initiating Disable Process
Applying transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config'
'C:\Windows\System32\inetsrv\config\applicationHost.config' backed up to 'C:\Windows\System32\inetsrv\config\applicationHost.config.backup-2019-03-26_08-59-52z'
in :1,237
No element in the source document matches '/configuration/location[@path='']/system.webServer/modules/add[@name='ManagedHttpModuleHelper']'
Not executing RemoveAll (transform line 1, 546)
Transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config' was successfully applied. Operation: 'disable'
GAC Module will not be removed, since this operation might cause IIS instabilities
Configuring IIS Environment for codeless attach...
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]
Configuring IIS Environment for instrumentation engine...
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]
Configuring registry for instrumentation engine...
Successfully disabled Application Insights Status Monitor
Installing GAC module 'C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.0\content\Runtime\Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll'
Applying transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config'
Found GAC module Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.ManagedHttpModuleHelper, Microsoft.AppInsights.IIS.ManagedHttpModuleHelper, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35
'C:\Windows\System32\inetsrv\config\applicationHost.config' backed up to 'C:\Windows\System32\inetsrv\config\applicationHost.config.backup-2019-03-26_08-59-52z_1'
Transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config' was successfully applied. Operation: 'enable'
Configuring IIS Environment for codeless attach...
Configuring IIS Environment for instrumentation engine...
Configuring registry for instrumentation engine...
Updating app pool permissions...
Successfully enabled Application Insights Status Monitor
```

## <a name="next-steps"></a>Następne kroki

  Wyświetlanie telemetrii:
 - [Poznaj metryki](../../azure-monitor/app/metrics-explorer.md) , aby monitorować wydajność i użycie.
- [Wyszukaj zdarzenia i dzienniki](../../azure-monitor/app/diagnostic-search.md) , aby zdiagnozować problemy.
- [Użyj analizy](../../azure-monitor/app/analytics.md) , aby uzyskać bardziej zaawansowane zapytania.
- [Tworzenie pulpitów nawigacyjnych](../../azure-monitor/app/overview-dashboard.md).
 
 Dodawanie kolejnych funkcji telemetrii:
 - [Utwórz testy sieci Web](monitor-web-app-availability.md) , aby upewnić się, że witryna pozostaje aktywna.
- [Dodaj telemetrię klienta sieci Web](../../azure-monitor/app/javascript.md) , aby zobaczyć wyjątki z kodu strony sieci Web i włączyć wywołania śledzenia.
- [Dodaj do kodu zestaw SDK Application Insights](../../azure-monitor/app/asp-net.md) , aby móc wstawiać wywołania śledzenia i rejestrowania.
 
 Zrób więcej dzięki Application Insights agentowi:
 - Skorzystaj z naszego przewodnika, aby [rozwiązać problemy z](status-monitor-v2-troubleshoot.md) agentem Application Insights.
 - [Pobierz konfigurację](status-monitor-v2-api-get-config.md) , aby upewnić się, że Twoje ustawienia zostały poprawnie zarejestrowane.
 - [Pobierz stan,](status-monitor-v2-api-get-status.md) aby sprawdzić monitorowanie.
