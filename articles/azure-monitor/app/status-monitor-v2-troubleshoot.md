---
title: Azure w wersji 2 Monitora stanu rozwiązywania problemów i znane problemy | Dokumentacja firmy Microsoft
description: Znane problemy v2 Monitor stanu i rozwiązywanie problemów z przykładów. Monitorowanie wydajności witryny sieci Web bez konieczności ponownego wdrażania witryny sieci Web. Działa z aplikacjami internetowymi platformy ASP.NET hostowanymi lokalnie na maszynach wirtualnych lub platformie Azure.
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
ms.openlocfilehash: d640206fd72b4eb89afe5ed1750627823bca9637
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2019
ms.locfileid: "65415875"
---
# <a name="troubleshooting-status-monitor-v2"></a>Rozwiązywanie problemów dotyczących stan monitorowania v2

Po włączeniu monitorowania, mogą wystąpić problemy, które rezygnację ze zbierania danych. Ten dokument zawiera listę wszystkich znanych problemów i przykłady dotyczące rozwiązywania problemów.
Jeśli przejście między problemu niewymienione w tym przypadku może skontaktuj się z nami [tutaj](https://github.com/Microsoft/ApplicationInsights-Home/issues).


> [!IMPORTANT]
> Monitor stanu w wersji 2 jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [dodatkowym warunkom użytkowania wersji zapoznawczych usług Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="known-issues"></a>Znane problemy

### <a name="conflicting-dlls-in-an-applications-bin-directory"></a>Konflikt biblioteki dll w katalogu bin aplikacji

Jeśli w dowolnej z tych bibliotek dll znajdują się w katalogu bin, monitorowania może zakończyć się niepowodzeniem.

- Microsoft.ApplicationInsights.dll
- Microsoft.AspNet.TelemetryCorrelation.dll
- System.Diagnostics.DiagnosticSource.dll

Niektóre z tych bibliotek DLL są uwzględnione w aplikacji szablonów programu Visual Studio, nawet wtedy, gdy aplikacja nie używa ich.
Zachowanie objawem są widoczne przy użyciu narzędzia do rozwiązywania problemów:

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

- polecenie iisreset + aplikacja obciążenia (bez danych TELEMETRYCZNYCH). Badanie przy użyciu Sysinternals (Handle.exe i ListDLLs.exe)
    ```
    .\handle64.exe -p w3wp | findstr /I "InstrumentationEngine AI. ApplicationInsights"
    E54: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll

    .\Listdlls64.exe w3wp | findstr /I "InstrumentationEngine AI ApplicationInsights"
    0x0000000009be0000  0x127000  C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\MicrosoftInstrumentationEngine_x64.dll
    0x0000000009b90000  0x4f000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.ExtensionsHost_x64.dll
    0x0000000004d20000  0xb2000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.Extensions.Base_x64.dll
    ```

### <a name="conflict-with-iis-shared-configuration"></a>Konflikt z udostępnionej konfiguracji IIS

Jeśli masz klaster serwerów sieci web, być może używasz [konfiguracji udostępnionej](https://docs.microsoft.com/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211). Firma Microsoft nie może automatycznie wstrzyknąć naszych HttpModule do tej konfiguracji udostępnionej. Każdy serwer sieci web należy najpierw uruchomić polecenie Włącz, aby zainstalować naszej bibliotece DLL w jego pamięci podręcznej GAC.

Po uruchomieniu polecenia Enable 
1. Przejdź do katalogu konfiguracji udostępnionej, a następnie znajdź swoje `applicationHost.config` pliku.
2. Dodaj następujący wiersz do konfiguracji w sekcji modułów:
    ```
    <modules>
        <!-- Registered global managed http module handler. The 'Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll' must be installed in the GAC before this config is applied. -->
        <add name="ManagedHttpModuleHelper" type="Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.ManagedHttpModuleHelper, Microsoft.AppInsights.IIS.ManagedHttpModuleHelper, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" preCondition="managedHandler,runtimeVersionv4.0" />
    </modules>
    ```
    


## <a name="troubleshooting"></a>Rozwiązywanie problemów
    
### <a name="troubleshooting-powershell"></a>Rozwiązywanie problemów z programu PowerShell

#### <a name="how-to-inspect-what-modules-are-available"></a>Jak sprawdzić, jakie moduły są dostępne?
Można przeprowadzać ich inspekcje zainstalowanych modułów za pomocą polecenia: `Get-Module -ListAvailable`

#### <a name="how-to-import-a-module-into-the-current-session"></a>Jak zaimportować moduł do bieżącej sesji?
Jeśli moduł nie został załadowany do sesji programu PowerShell, można ręcznie załadować przy użyciu polecenia: `Import-Module <path to psd1>`


### <a name="troubleshooting-the-status-monitor-v2-module"></a>Rozwiązywanie problemów z modułu Monitora stanu w wersji 2

#### <a name="how-to-review-what-commands-are-available-in-the-status-monitor-v2-module"></a>Jak przeglądać, jakie polecenia są dostępne w module v2 Monitor stanu?
- Uruchom polecenie: `Get-Command -Module Az.ApplicationMonitor` można pobrać dostępnych poleceń:

    ```
    CommandType     Name                                               Version    Source
    -----------     ----                                               -------    ------
    Cmdlet          Disable-ApplicationInsightsMonitoring              0.2.1      Az.ApplicationMonitor
    Cmdlet          Disable-InstrumentationEngine                      0.2.1      Az.ApplicationMonitor
    Cmdlet          Enable-ApplicationInsightsMonitoring               0.2.1      Az.ApplicationMonitor
    Cmdlet          Enable-InstrumentationEngine                       0.2.1      Az.ApplicationMonitor
    Cmdlet          Get-ApplicationInsightsMonitoringConfig            0.2.1      Az.ApplicationMonitor
    Cmdlet          Get-ApplicationInsightsMonitoringStatus            0.2.1      Az.ApplicationMonitor
    Cmdlet          Set-ApplicationInsightsMonitoringConfig            0.2.1      Az.ApplicationMonitor
    ```

#### <a name="what-is-the-current-version-of-the-status-monitor-v2-module"></a>Co to jest bieżącą wersję modułu v2 Monitor stanu?
- Uruchom polecenie: `Get-ApplicationInsightsMonitoringStatus` można pobrać informacji na temat tego modułu danych wyjściowych:
    - Wersja modułu programu PowerShell
    - Wersja zestawu SDK usługi Insights aplikacji
    - Ścieżki plików modułu programu PowerShell
    
Przejrzyj nasze [dokumentacja interfejsu API](status-monitor-v2-api-get-status.md) szczegółowy opis sposobu użycia tego polecenia cmdlet.



### <a name="troubleshooting-running-processes"></a>Rozwiązywanie problemów z uruchomionych procesów

Można sprawdzić procesu na komputerze instrumentowane, aby sprawdzić, czy wszystkie biblioteki DLL są ładowane.
Jeśli działa monitorowania, co najmniej 12 biblioteki DLL powinny być załadowane.

- Cmd: `Get-ApplicationInsightsMonitoringStatus -InspectProcess`

Przejrzyj nasze [dokumentacja interfejsu API](status-monitor-v2-api-get-status.md) szczegółowy opis sposobu użycia tego polecenia cmdlet.


### <a name="collect-etw-logs-with-perfview"></a>Zbieranie dzienników zdarzeń systemu Windows za pomocą narzędzia PerfView

#### <a name="setup"></a>Konfiguracja

1. Pobierz PerfView.exe i PerfView64.exe z https://github.com/Microsoft/perfview/releases
2. Uruchom PerfView64.exe
3. Rozwiń pozycję "Opcje zaawansowane"
4. Usuń zaznaczenie pola wyboru:
    - ZIP
    - Scal
    - Kolekcja symboli .NET
5. Zestaw dodatkowych dostawców: `61f6ca3b-4b5f-5602-fa60-759a2a2d1fbd,323adc25-e39b-5c87-8658-2c1af1a92dc5,925fa42b-9ef6-5fa7-10b8-56449d7a2040,f7d60e07-e910-5aca-bdd2-9de45b46c560,7c739bb9-7861-412e-ba50-bf30d95eae36,61f6ca3b-4b5f-5602-fa60-759a2a2d1fbd,323adc25-e39b-5c87-8658-2c1af1a92dc5,252e28f4-43f9-5771-197a-e8c7e750a984`


#### <a name="collecting-logs"></a>Zbieranie dzienników

1. W konsoli polecenia z uprawnieniami administratora, wykonaj `iisreset /stop` wyłączenia usług IIS i wszystkie aplikacje sieci web.
2. W PerfView kliknij przycisk "Rozpocznij zbieranie"
3. W konsoli polecenia z uprawnieniami administratora, wykonaj `iisreset /start` do uruchamiania usług IIS.
4. Spróbuj przejść do swojej aplikacji.
5. Po zakończeniu ładowania aplikacji wrócić do narzędzia PerfView i kliknij pozycję "Stop Collection"



## <a name="next-steps"></a>Kolejne kroki

- Przejrzyj nasze [dokumentacja interfejsu API](status-monitor-v2-overview.md#powershell-api-reference) można znaleźć parametru mogły zostać pominięte.
- Jeśli przejście między problemu niewymienione w tym przypadku może skontaktuj się z nami [tutaj](https://github.com/Microsoft/ApplicationInsights-Home/issues).
