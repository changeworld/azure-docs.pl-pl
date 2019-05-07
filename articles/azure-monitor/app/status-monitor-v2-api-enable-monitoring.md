---
title: 'Dokumentacja interfejsu API w wersji 2 Monitora stanu platformy Azure: Aby włączyć monitorowanie | Dokumentacja firmy Microsoft'
description: Stan monitora v2 interfejsu API odwołania Enable-ApplicationInsightsMonitoring. Monitorowanie wydajności witryny sieci Web bez konieczności ponownego wdrażania witryny sieci Web. Działa z aplikacjami internetowymi platformy ASP.NET hostowanymi lokalnie na maszynach wirtualnych lub platformie Azure.
services: application-insights
documentationcenter: .net
author: MS-TimothyMothra
manager: alexklim
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: tilee
ms.openlocfilehash: 17798c78b167821f5f30a11996ac90cf67fb0179
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65144949"
---
# <a name="status-monitor-v2-api-enable-applicationinsightsmonitoring-v021-alpha"></a>Interfejs API w wersji 2 Monitora stanu: Enable-ApplicationInsightsMonitoring (v0.2.1 alfa)

W tym dokumencie opisano polecenia cmdlet, który jest dostarczany jako członek [modułu Az.ApplicationMonitor PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

> [!IMPORTANT]
> Monitor stanu w wersji 2 jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [dodatkowym warunkom użytkowania wersji zapoznawczych usług Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="description"></a>Opis

Włącz kod bez dołączania, monitorowanie aplikacji usług IIS na komputerze docelowym.
To polecenie cmdlet spowoduje modyfikowania pliku applicationHost.config usług IIS i ustawić niektóre klucze rejestru.
To polecenie cmdlet utworzy również applicationinsights.ikey.config, który definiuje klucz instrumentacji, który jest używany przez aplikację.
Usługi IIS zostaną załadowane RedfieldModule przy uruchamianiu, która doda zestaw SDK usługi Application Insights do aplikacji, jak uruchamiać tych aplikacji.
Ponowne uruchomienie usług IIS, aby zmiany zaczęły obowiązywać.

Po włączeniu monitorowania, zaleca się używanie [metryki na żywo](live-stream.md) szybko obserwować, jeśli aplikacja wysyła nam dane telemetryczne.


> [!NOTE] 
> Aby rozpocząć pracę, musi mieć klucz instrumentacji. Aby uzyskać więcej informacji, przeczytaj [Tworzenie nowego zasobu](create-new-resource.md#copy-the-instrumentation-key).


> [!IMPORTANT] 
> To polecenie cmdlet wymaga sesji programu PowerShell z uprawnieniami administratora i za pomocą zasad wykonania z podwyższonym poziomem uprawnień. Odczyt [tutaj](status-monitor-v2-detailed-instructions.md#run-powershell-as-administrator-with-an-elevated-execution-policy) Aby uzyskać więcej informacji.

> [!NOTE] 
> To polecenie cmdlet wymaga Przejrzyj i zaakceptuj nasze licencyjne i zasady zachowania poufności.


## <a name="examples"></a>Przykłady

### <a name="example-with-single-instrumentation-key"></a>Przykład: klucz Instrumentacji pojedynczego
W tym przykładzie wszystkie aplikacje na bieżącym komputerze zostanie przypisany klucz Instrumentacji jednego.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

### <a name="example-with-instrumentation-key-map"></a>Przykład: mapy kluczy Instrumentacji
W tym przykładzie 
- `MachineFilter` będą pasować do bieżącego komputera przy użyciu `'.*'` symboli wieloznacznych.
- `AppFilter='WebAppExclude'` udostępnia `null` elementu InstrumentationKey. Ta aplikacja nie będzie instrumentowany.
- `AppFilter='WebAppOne'` przypisze tej konkretnej aplikacji klucz Instrumentacji unikatowy.
- `AppFilter='WebAppTwo'` również spowoduje przypisanie klucza Instrumentacji unikatowy tej konkretnej aplikacji.
- Na koniec `AppFilter` używa również `'.*'` symboli wieloznacznych, aby dopasować wszystkie pozostałe aplikacje sieci web dopasowane przez wcześniejsze reguły i przypisuje domyślny klucz instrumentacji.
- Dodano dla czytelności tylko miejsca do magazynowania.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap 
    @(@{MachineFilter='.*';AppFilter='WebAppExclude'},
      @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'},
      @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'},
      @{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'})

```


## <a name="parameters"></a>Parametry 

### <a name="-instrumentationkey"></a>-InstrumentationKey
**Wymagane.** Użyj tego parametru, aby dostarczyć pojedynczego klucza iKey do użytku przez wszystkie aplikacje na komputerze docelowym.

### <a name="-instrumentationkeymap"></a>-InstrumentationKeyMap
**Wymagane.** Użyj tego parametru, aby podać wiele kluczy ikey i mapowanie aplikacje, które do użycia, które klucz instrumentacji. Ustawiając MachineFilter, możesz utworzyć skrypt jednej instalacji na kilku komputerach. 

> [!IMPORTANT] 
> Aplikacje zostanie dopasowany do reguły w kolejności, że są one udostępniane. Dlatego najpierw należy określić najbardziej specyficzne reguły, a ostatni najbardziej ogólne reguły.

#### <a name="schema"></a>Schemat
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'})`

- **MachineFilter** jest regex wymagane c# o nazwie komputera lub maszyny wirtualnej.
    - ". *" będzie zgodne ze wszystkimi
    - 'ComputerName' będzie zgodna tylko komputery z takiej samej nazwie.
- **AppFilter** jest regex wymagane c# o nazwie komputera lub maszyny wirtualnej.
    - ". *" będzie zgodne ze wszystkimi
    - "ApplicationName" będzie zgodna tylko aplikacje usług IIS przy użyciu takiej samej nazwie.
- **InstrumentationKey** jest wymagane do umożliwienia monitorowania aplikacji, które pasują do powyższych dwóch filtrów.
    - Pozostaw tę wartość na wartość null, jeśli chcesz zdefiniować zasady, które mają zostać wykluczone z monitorowania


### <a name="-enableinstrumentationengine"></a>-EnableInstrumentationEngine
**Opcjonalnie.** Użyj tego przełącznika, aby włączyć aparat Instrumentacji do gromadzenia zdarzeń i komunikatów, co się dzieje się podczas wykonywania procesu zarządzanego. W tym między innymi kody wyników programu zależności, zleceń HTTP i tekst polecenia SQL. Aparat Instrumentacji doda dodatkowe obciążenie i jest domyślnie wyłączona.

### <a name="-acceptlicense"></a>-AcceptLicense
**Opcjonalnie.** Aby zaakceptować umowę licencyjną i zachowania poufności w przypadku instalacji nienadzorowanej, należy użyć tego przełącznika.

### <a name="-verbose"></a>-Verbose
**Typowy parametr.** W danych wyjściowych szczegółowych dzienników, należy użyć tego przełącznika.

### <a name="-whatif"></a>-WhatIf 
**Typowy parametr.** Ten przełącznik umożliwia testowanie i Walidacja parametry wejściowe bez faktycznego włączania monitorowania.

## <a name="output"></a>Dane wyjściowe


#### <a name="example-output-from-a-successful-enablement"></a>Przykładowe dane wyjściowe z pomyślnej aktywacji

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

## <a name="next-steps"></a>Kolejne kroki

  Wyświetlanie telemetrii:
 - [Eksplorowanie metryk](../../azure-monitor/app/metrics-explorer.md) w celu monitorowania wydajności i użycia
- [Wyszukiwanie zdarzeń i dzienników](../../azure-monitor/app/diagnostic-search.md) do diagnozowania problemów
- [Analiza](../../azure-monitor/app/analytics.md) dla bardziej zaawansowanych zapytań
- [Tworzenie pulpitów nawigacyjnych](../../azure-monitor/app/app-insights-dashboards.md)
 
 Dodawanie kolejnych funkcji telemetrii:
 - [Tworzenie testów sieci web](monitor-web-app-availability.md) aby upewnić się, że witryna pozostaje aktywna.
- [Dodawanie telemetrii klienta sieci web](../../azure-monitor/app/javascript.md) aby zobaczyć wyjątki pochodzące z kodu strony sieci web i umożliwić wstawianie wywołań śladu.
- [Dodawanie zestawu SDK usługi Application Insights do kodu](../../azure-monitor/app/asp-net.md) tak, aby wstawić ślad i rejestrować wywołania
 
 Wykonuj więcej zadań dzięki v2 Monitora stanu:
 - Użyj zapoznaj się z przewodnikiem [rozwiązywanie](status-monitor-v2-troubleshoot.md) Monitora stanu w wersji 2.
 - [Pobierz konfigurację](status-monitor-v2-api-get-config.md) aby upewnić się, że Twoje ustawienia zostały prawidłowo zarejestrowane.
 - [Pobierz stan](status-monitor-v2-api-get-status.md) do inspekcji, monitorowania.
