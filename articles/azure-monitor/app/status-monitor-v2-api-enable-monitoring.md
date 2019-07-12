---
title: 'Dokumentacja interfejsu API w wersji 2 usługi Azure Monitor stanu: Aby włączyć monitorowanie | Dokumentacja firmy Microsoft'
description: Dokumentacja interfejsu API w wersji 2 Monitor stanu. Enable-ApplicationInsightsMonitoring. Monitorowanie wydajności witryny sieci Web bez konieczności ponownego wdrażania witryny sieci Web. Działa z aplikacjami sieci web platformy ASP.NET hostowanej lokalnie, na maszynach wirtualnych lub na platformie Azure.
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
ms.openlocfilehash: 1e5a4ac9f159ce238149d44127fd63dcf3279623
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807099"
---
# <a name="status-monitor-v2-api-enable-applicationinsightsmonitoring-v040-alpha"></a>Interfejs API w wersji 2 Monitora stanu: Enable-ApplicationInsightsMonitoring (v0.4.0 alfa)

W tym artykule opisano polecenia cmdlet, które jest członkiem [modułu Az.ApplicationMonitor PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

> [!IMPORTANT]
> Monitor stanu w wersji 2 jest obecnie w publicznej wersji zapoznawczej.
> Tej wersji zapoznawczej jest oferowana bez umowy dotyczącej poziomu usług, i firma Microsoft nie jest to zalecane w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą nie być obsługiwane, a niektóre mogą mieć ograniczone możliwości.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="description"></a>Opis

Bez kodu umożliwia dołączenie, monitorowanie aplikacji usług IIS na komputerze docelowym.

To polecenie cmdlet spowoduje modyfikowania pliku applicationHost.config usług IIS i ustawić niektóre klucze rejestru.
Ponadto spowoduje to utworzenie pliku applicationinsights.ikey.config, który definiuje klucz instrumentacji, używany przez każdą aplikację.
Usługi IIS zostaną załadowane RedfieldModule uruchamianie doda zestaw SDK usługi Application Insights do aplikacji jako początkowy aplikacji.
Ponowne uruchomienie usług IIS, aby zmiany zaczęły obowiązywać.

Po włączeniu monitorowania, firma Microsoft zaleca użycie [metryki na żywo](live-stream.md) można szybko sprawdzić, jeśli Twoja aplikacja wysyła nam dane telemetryczne.


> [!NOTE] 
> - Aby rozpocząć pracę, musisz mieć klucz instrumentacji. Aby uzyskać więcej informacji, zobacz [Utwórz zasób](create-new-resource.md#copy-the-instrumentation-key).
> - To polecenie cmdlet wymaga Przejrzyj i zaakceptuj nasze licencyjne i zasady zachowania poufności.

> [!IMPORTANT] 
> To polecenie cmdlet wymaga sesji programu PowerShell z uprawnieniami administratora i zasad wykonania z podwyższonym poziomem uprawnień. Aby uzyskać więcej informacji, zobacz [Uruchom program PowerShell jako administrator przy użyciu zasad usługi z podwyższonym poziomem uprawnień wykonywanie](status-monitor-v2-detailed-instructions.md#run-powershell-as-admin-with-an-elevated-execution-policy).

## <a name="examples"></a>Przykłady

### <a name="example-with-a-single-instrumentation-key"></a>Przykład: klucz Instrumentacji pojedynczego
W tym przykładzie wszystkie aplikacje na bieżącym komputerze są przypisywane klucz Instrumentacji jednego.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

### <a name="example-with-an-instrumentation-key-map"></a>Przykład z mapą klucza Instrumentacji
W tym przykładzie:
- `MachineFilter` Dopasowuje bieżący komputer przy użyciu `'.*'` symboli wieloznacznych.
- `AppFilter='WebAppExclude'` udostępnia `null` klucz instrumentacji. Określona aplikacja nie będzie instrumentowany.
- `AppFilter='WebAppOne'` przypisuje określona aplikacja klucz Instrumentacji unikatowy.
- `AppFilter='WebAppTwo'` przypisuje określona aplikacja klucz Instrumentacji unikatowy.
- Na koniec `AppFilter` używa również `'.*'` symboli wieloznacznych, aby dopasować wszystkie aplikacje sieci web, które nie zostały dopasowane przez wcześniejsze reguły i przypisz domyślny klucz instrumentacji.
- Miejsca do magazynowania są dodawane dla czytelności.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap 
    @(@{MachineFilter='.*';AppFilter='WebAppExclude'},
      @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'}},
      @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'}},
      @{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'}})

```


## <a name="parameters"></a>Parametry

### <a name="-instrumentationkey"></a>-InstrumentationKey
**Wymagane.** Użyj tego parametru, aby podać klucz Instrumentacji pojedynczej do użytku przez wszystkie aplikacje na komputerze docelowym.

### <a name="-instrumentationkeymap"></a>-InstrumentationKeyMap
**Wymagane.** Użyj tego parametru jest podanie wielu kluczy Instrumentacji i mapowanie kluczy instrumentacji, używanych przez każdą aplikację.
Możesz utworzyć skrypt jednej instalacji dla kilku komputerów, ustawiając `MachineFilter`.

> [!IMPORTANT]
> Aplikacje zostanie dopasowany do reguły w kolejności, które zasady zostały udostępnione. Dlatego najpierw należy określić najbardziej specyficzne reguły, a ostatni najbardziej ogólne reguły.

#### <a name="schema"></a>Schemat
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'}})`

- **MachineFilter** jest wymaganym C# wyrażeń regularnych, komputera lub nazwa maszyny Wirtualnej.
    - ". *" będzie zgodne ze wszystkimi
    - 'ComputerName' będzie zgodna tylko komputery z dokładną nazwą określony.
- **AppFilter** jest wymaganym C# wyrażeń regularnych, nazwy witryny usług IIS. Listę witryn na serwerze można uzyskać, uruchamiając polecenie [get iissite](https://docs.microsoft.com/powershell/module/iisadministration/get-iissite).
    - ". *" będzie zgodne ze wszystkimi
    - "Nazwa witryny" będzie zgodna tylko witryny usług IIS o podanej nazwie dokładnie.
- **InstrumentationKey** jest wymagane do umożliwienia monitorowania aplikacji, które odpowiadają poprzednim dwa filtry.
    - Pozostaw tę wartość na wartość null, jeśli chcesz zdefiniować zasady, które mają zostać wykluczone z monitorowania.


### <a name="-enableinstrumentationengine"></a>-EnableInstrumentationEngine
**Opcjonalnie.** Użyj tego przełącznika, aby włączyć aparat Instrumentacji i Zbieraj zdarzenia i komunikaty o tym, co się dzieje podczas wykonywania procesu zarządzanego. Te zdarzenia i komunikaty zawierają kody wyników programu zależności, zleceń HTTP i tekst polecenia SQL.

Aparat Instrumentacji dodaje obciążenie i jest domyślnie wyłączona.

### <a name="-acceptlicense"></a>-AcceptLicense
**Opcjonalnie.** Aby zaakceptować umowę licencyjną i zachowania poufności w przypadku instalacji nienadzorowanej, należy użyć tego przełącznika.

### <a name="-ignoresharedconfig"></a>-IgnoreSharedConfig
W przypadku klastra serwerów sieci web, być może używasz [konfigurację udostępnioną](https://docs.microsoft.com/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211).
HttpModule nie wprowadzony w tej konfiguracji udostępnionej.
Ten skrypt zakończy się niepowodzeniem z komunikatem o wymaganych kroków instalacji dodatkowych.
Aby zignorować to sprawdzenie i kontynuuj instalowanie wymagań wstępnych, należy użyć tego przełącznika. Aby uzyskać więcej informacji, zobacz [znanych konfliktu z--konfiguracji udostępnionej usług iis —](status-monitor-v2-troubleshoot.md#conflict-with-iis-shared-configuration)

### <a name="-verbose"></a>-Verbose
**Typowy parametr.** Aby wyświetlić szczegółowe dzienniki, należy użyć tego przełącznika.

### <a name="-whatif"></a>-WhatIf 
**Typowy parametr.** Ten przełącznik umożliwia testowanie i Walidacja parametry wejściowe bez faktycznego włączania monitorowania.

## <a name="output"></a>Output


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

## <a name="next-steps"></a>Następne kroki

  Wyświetlanie telemetrii:
 - [Eksplorowanie metryk](../../azure-monitor/app/metrics-explorer.md) do monitorowania wydajności i użycia.
- [Wyszukiwanie zdarzeń i dzienników](../../azure-monitor/app/diagnostic-search.md) do diagnozowania problemów.
- [Korzystanie z analizy](../../azure-monitor/app/analytics.md) dla bardziej zaawansowanych zapytań.
- [Tworzenie pulpitów nawigacyjnych](../../azure-monitor/app/overview-dashboard.md).
 
 Dodawanie kolejnych funkcji telemetrii:
 - [Tworzenie testów sieci web](monitor-web-app-availability.md) aby upewnić się, że witryna pozostaje aktywna.
- [Dodawanie telemetrii klienta sieci web](../../azure-monitor/app/javascript.md) aby zobaczyć wyjątki pochodzące z kodu strony sieci web i umożliwić wywołania śledzenia.
- [Dodaj zestaw Application Insights SDK do kodu](../../azure-monitor/app/asp-net.md) dzięki czemu można wstawić ślad i rejestrować wywołania.
 
 Wykonuj więcej zadań dzięki v2 Monitora stanu:
 - Użyj zapoznaj się z przewodnikiem [Rozwiązywanie problemów z](status-monitor-v2-troubleshoot.md) Monitora stanu w wersji 2.
 - [Pobierz konfigurację](status-monitor-v2-api-get-config.md) aby upewnić się, że Twoje ustawienia zostały prawidłowo zarejestrowane.
 - [Pobierz stan](status-monitor-v2-api-get-status.md) do inspekcji, monitorowania.
