---
title: Azure w wersji 2 Monitora stanu rozwiązywania problemów i znane problemy | Dokumentacja firmy Microsoft
description: Znane problemy v2 Monitor stanu i rozwiązywanie problemów z przykładów. Monitorowanie wydajności witryny sieci Web bez konieczności ponownego wdrażania witryny sieci Web. Działa z aplikacjami sieci web platformy ASP.NET hostowanej lokalnie, na maszynach wirtualnych lub na platformie Azure.
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
ms.openlocfilehash: df59766ce38ac81568570cd6544ee28808ff8249
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807025"
---
# <a name="troubleshooting-status-monitor-v2"></a>Rozwiązywanie problemów dotyczących stan monitorowania v2

Po włączeniu monitorowania, mogą pojawić się problemy, które uniemożliwiają zbierania danych.
W tym artykule wymieniono wszystkie znane problemy i przykłady dotyczące rozwiązywania problemów.
Jeśli trafisz na problem, który nie został tutaj wymieniony, możesz skontaktować się nam na [GitHub](https://github.com/Microsoft/ApplicationInsights-Home/issues).


> [!IMPORTANT]
> Monitor stanu w wersji 2 jest obecnie w publicznej wersji zapoznawczej.
> Tej wersji zapoznawczej jest oferowana bez umowy dotyczącej poziomu usług, i firma Microsoft nie jest to zalecane w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą nie być obsługiwane, a niektóre mogą mieć ograniczone możliwości.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="known-issues"></a>Znane problemy

### <a name="conflicting-dlls-in-an-apps-bin-directory"></a>Konflikt biblioteki dll w katalogu bin aplikacji

Jeśli w dowolnej z tych bibliotek dll znajdują się w katalogu bin, monitorowania może zakończyć się niepowodzeniem:

- Microsoft.ApplicationInsights.dll
- Microsoft.AspNet.TelemetryCorrelation.dll
- System.Diagnostics.DiagnosticSource.dll

Niektóre z tych bibliotek DLL są uwzględnione w aplikacji szablonów programu Visual Studio, nawet wtedy, gdy nie korzysta z nich.
Aby wyświetlić zachowanie objawem, można użyć narzędzia do rozwiązywania problemów:

- Narzędzia PerfView:
    ```
    ThreadID="7,500" 
    ProcessorNumber="0" 
    msg="Found 'System.Diagnostics.DiagnosticSource, Version=4.0.2.1, Culture=neutral, PublicKeyToken=cc7b13ffcd2ddd51' assembly, skipping attaching redfield binaries" 
    ExtVer="2.8.13.5972" 
    SubscriptionId="" 
    AppName="" 
    FormattedMessage="Found 'System.Diagnostics.DiagnosticSource, Version=4.0.2.1, Culture=neutral, PublicKeyToken=cc7b13ffcd2ddd51' assembly, skipping attaching redfield binaries" 
    ```

- Polecenie IISReset i aplikacji obciążenia (bez danych telemetrycznych). Badanie przy użyciu Sysinternals (Handle.exe i ListDLLs.exe):
    ```
    .\handle64.exe -p w3wp | findstr /I "InstrumentationEngine AI. ApplicationInsights"
    E54: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll

    .\Listdlls64.exe w3wp | findstr /I "InstrumentationEngine AI ApplicationInsights"
    0x0000000009be0000  0x127000  C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\MicrosoftInstrumentationEngine_x64.dll
    0x0000000009b90000  0x4f000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.ExtensionsHost_x64.dll
    0x0000000004d20000  0xb2000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.Extensions.Base_x64.dll
    ```

### <a name="conflict-with-iis-shared-configuration"></a>Konflikt z udostępnionej konfiguracji IIS

Jeśli masz klaster serwerów sieci web, być może używasz [konfigurację udostępnioną](https://docs.microsoft.com/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211).
HttpModule nie wprowadzony w tej konfiguracji udostępnionej.
Uruchom polecenie Włącz na wszystkich serwerach sieci web, aby zainstalować biblioteki DLL w pamięci podręcznej GAC każdego serwera.

Po uruchomieniu polecenia włączenia wykonaj następujące kroki:
1. Przejdź do katalogu konfiguracji udostępnionej, a następnie znajdź plik applicationHost.config.
2. Dodaj następujący wiersz do sekcji modules konfiguracji:
    ```
    <modules>
        <!-- Registered global managed http module handler. The 'Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll' must be installed in the GAC before this config is applied. -->
        <add name="ManagedHttpModuleHelper" type="Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.ManagedHttpModuleHelper, Microsoft.AppInsights.IIS.ManagedHttpModuleHelper, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" preCondition="managedHandler,runtimeVersionv4.0" />
    </modules>
    ```
## <a name="troubleshooting"></a>Rozwiązywanie problemów
    
### <a name="troubleshooting-powershell"></a>Rozwiązywanie problemów z programu PowerShell

#### <a name="determine-which-modules-are-available"></a>Określić, które moduły są dostępne
Możesz użyć `Get-Module -ListAvailable` polecenie, aby ustalić, które moduły są zainstalowane.

#### <a name="import-a-module-into-the-current-session"></a>Zaimportuj moduł do bieżącej sesji
Jeśli moduł nie został załadowany do sesji programu PowerShell, można ręcznie załadować go za pomocą `Import-Module <path to psd1>` polecenia.


### <a name="troubleshooting-the-status-monitor-v2-module"></a>Rozwiązywanie problemów z modułu Monitora stanu w wersji 2

#### <a name="list-the-commands-available-in-the-status-monitor-v2-module"></a>Lista poleceń dostępnych w module v2 Monitor stanu usługi
Uruchom polecenie `Get-Command -Module Az.ApplicationMonitor` można pobrać dostępnych poleceń:

```
CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Disable-ApplicationInsightsMonitoring              0.4.0      Az.ApplicationMonitor
Cmdlet          Disable-InstrumentationEngine                      0.4.0      Az.ApplicationMonitor
Cmdlet          Enable-ApplicationInsightsMonitoring               0.4.0      Az.ApplicationMonitor
Cmdlet          Enable-InstrumentationEngine                       0.4.0      Az.ApplicationMonitor
Cmdlet          Get-ApplicationInsightsMonitoringConfig            0.4.0      Az.ApplicationMonitor
Cmdlet          Get-ApplicationInsightsMonitoringStatus            0.4.0      Az.ApplicationMonitor
Cmdlet          Set-ApplicationInsightsMonitoringConfig            0.4.0      Az.ApplicationMonitor
Cmdlet          Start-ApplicationInsightsMonitoringTrace           0.4.0      Az.ApplicationMonitor
```

#### <a name="determine-the-current-version-of-the-status-monitor-v2-module"></a>Określić bieżącą wersję modułu Monitora stanu w wersji 2
Uruchom `Get-ApplicationInsightsMonitoringStatus` polecenie, aby wyświetlić następujące informacje o module:
   - Wersja modułu programu PowerShell
   - Wersja zestawu SDK usługi Insights aplikacji
   - Ścieżki plików modułu programu PowerShell
    
Przegląd [dokumentacja interfejsu API](status-monitor-v2-api-get-status.md) szczegółowy opis sposobu użycia tego polecenia cmdlet.


### <a name="troubleshooting-running-processes"></a>Rozwiązywanie problemów z uruchomionych procesów

Można sprawdzić procesów na komputerze instrumentowane, aby określić, czy wszystkie biblioteki DLL są ładowane.
Jeśli działa monitorowania, co najmniej 12 biblioteki DLL powinny być załadowane.

Użyj `Get-ApplicationInsightsMonitoringStatus -InspectProcess` polecenie, aby sprawdzić biblioteki dll.

Przegląd [dokumentacja interfejsu API](status-monitor-v2-api-get-status.md) szczegółowy opis sposobu użycia tego polecenia cmdlet.


### <a name="collect-etw-logs-by-using-perfview"></a>Zbieranie dzienników zdarzeń systemu Windows za pomocą narzędzia PerfView

#### <a name="setup"></a>Konfiguracja

1. Pobierz PerfView.exe i PerfView64.exe z [GitHub](https://github.com/Microsoft/perfview/releases).
2. Rozpocznij PerfView64.exe.
3. Rozwiń **zaawansowane opcje**.
4. Usuń zaznaczenie pola wyboru:
    - **Zip**
    - **Scal**
    - **Kolekcja symboli .NET**
5. Ustaw je **dodatkowych dostawców**: `61f6ca3b-4b5f-5602-fa60-759a2a2d1fbd,323adc25-e39b-5c87-8658-2c1af1a92dc5,925fa42b-9ef6-5fa7-10b8-56449d7a2040,f7d60e07-e910-5aca-bdd2-9de45b46c560,7c739bb9-7861-412e-ba50-bf30d95eae36,61f6ca3b-4b5f-5602-fa60-759a2a2d1fbd,323adc25-e39b-5c87-8658-2c1af1a92dc5,252e28f4-43f9-5771-197a-e8c7e750a984`


#### <a name="collecting-logs"></a>Zbieranie dzienników

1. W konsoli poleceń z uprawnieniami administratora, uruchom `iisreset /stop` polecenia, aby wyłączyć funkcję usług IIS i wszystkich aplikacji sieci web.
2. Z programu PerfView, wybierz **Rozpocznij zbieranie**.
3. W konsoli poleceń z uprawnieniami administratora, uruchom `iisreset /start` polecenia do uruchamiania usług IIS.
4. Spróbuj przejść do swojej aplikacji.
5. Po załadowaniu aplikacji, wróć do narzędzia PerfView, a następnie wybierz pozycję **Zatrzymaj Kolekcjonowanie**.



## <a name="next-steps"></a>Następne kroki

- Przegląd [dokumentacja interfejsu API](status-monitor-v2-overview.md#powershell-api-reference) Aby dowiedzieć się więcej na temat parametrów, które mogły zostać pominięte.
- Jeśli trafisz na problem, który nie został tutaj wymieniony, możesz skontaktować się nam na [GitHub](https://github.com/Microsoft/ApplicationInsights-Home/issues).
