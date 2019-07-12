---
title: 'Dokumentacja interfejsu API w wersji 2 usługi Azure Monitor stanu: Rozpocznij śledzenie | Dokumentacja firmy Microsoft'
description: Dokumentacja interfejsu API w wersji 2 Monitor stanu. Rozpocznij śledzenie. Zbieranie dzienników zdarzeń systemu Windows z Monitora stanu i zestawu SDK usługi Application Insights.
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
ms.openlocfilehash: b6787134707273a76290adb723a9bc9012252ebd
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807054"
---
# <a name="status-monitor-v2-api-start-applicationinsightsmonitoringtrace-v040-alpha"></a>Interfejs API w wersji 2 Monitora stanu: Start-ApplicationInsightsMonitoringTrace (v0.4.0-alpha)

W tym artykule opisano polecenia cmdlet, które jest członkiem [modułu Az.ApplicationMonitor PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

> [!IMPORTANT]
> Monitor stanu w wersji 2 jest obecnie w publicznej wersji zapoznawczej.
> Tej wersji zapoznawczej jest oferowana bez umowy dotyczącej poziomu usług, i firma Microsoft nie jest to zalecane w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą nie być obsługiwane, a niektóre mogą mieć ograniczone możliwości.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="description"></a>Opis

Gromadzi informacje o [zdarzenia ETW](https://docs.microsoft.com/windows/desktop/etw/event-tracing-portal) z uaktualniać dołączanie środowiska uruchomieniowego. To polecenie cmdlet jest alternatywą do uruchamiania [narzędzia PerfView](https://github.com/microsoft/perfview).

Zebrane zdarzenia będą drukowane do konsoli w w czasie rzeczywistym i jest zapisywana w pliku ETL. Plik ETL danych wyjściowych może zostać otwarty przez [narzędzia PerfView](https://github.com/microsoft/perfview) celu bliższego zbadania problemu.

To polecenie cmdlet zostanie uruchomiony, dopóki osiągnie limitu czasu (domyślnie 5 minut) lub ręcznie zatrzymana (`Ctrl + C`).

> [!IMPORTANT] 
> To polecenie cmdlet wymaga sesji programu PowerShell z uprawnieniami administratora.

## <a name="examples"></a>Przykłady

### <a name="how-to-collect-events"></a>Jak służąca do gromadzenia zdarzeń

Będzie zazwyczaj poproś zbieranie zdarzeń do badania, dlaczego nie ma instrumentacji aplikacji.

Bez kodu dołączanie środowiska uruchomieniowego emitować zdarzenia ETW, podczas uruchamiania usług IIS i podczas uruchamiania aplikacji.

Aby zebrać następujące zdarzenia:
1. W konsoli polecenia z uprawnieniami administratora, wykonaj `iisreset /stop` wyłączenia usług IIS i wszystkie aplikacje sieci web.
2. Wykonanie tego polecenia cmdlet
3. W konsoli polecenia z uprawnieniami administratora, wykonaj `iisreset /start` do uruchamiania usług IIS.
4. Spróbuj przejść do swojej aplikacji.
5. Po zakończeniu ładowania aplikacji możesz ręcznie zatrzymać (`Ctrl + C`) lub poczekaj na wartość limitu czasu.

### <a name="what-events-to-collect"></a>Jakie zdarzenia mają być zbierane

Podczas zbierania zdarzeń są trzy opcje:
1. Użyj przełącznika `-CollectSdkEvents` służąca do gromadzenia zdarzeń emitowane przez zestaw SDK usługi Application Insights.
2. Użyj przełącznika `-CollectRedfieldEvents` służąca do gromadzenia zdarzeń wyemitowane przez Monitor stanu i środowiska uruchomieniowego Redfield. Dzienniki te są pomocne podczas diagnozowania usług IIS i uruchomienia aplikacji.
3. Aby zebrać oba typy zdarzeń, należy użyć oba przełączniki.
4. Domyślnie, jeśli przełącznik nie jest określony, czy oba typy zdarzeń zostaną zebrane.


## <a name="parameters"></a>Parametry

### <a name="-maxdurationinminutes"></a>-MaxDurationInMinutes
**Opcjonalnie.** Użyj tego parametru, aby ustawić, jak długo ten skrypt należy zbierać zdarzenia. Wartość domyślna to 5 minut.

### <a name="-logdirectory"></a>-LogDirectory
**Opcjonalnie.** Użyj tego przełącznika, aby ustawić katalogu wyjściowego pliku ETL. Domyślnie ten plik zostanie utworzony w katalogu modułów programu PowerShell. Pełna ścieżka zostanie wyświetlony podczas wykonywania skryptu.


### <a name="-collectsdkevents"></a>-CollectSdkEvents
**Opcjonalnie.** Ten przełącznik umożliwia zbieranie zdarzeń zestawu SDK usługi Application Insights.

### <a name="-collectredfieldevents"></a>-CollectRedfieldEvents
**Opcjonalnie.** Aby zbierać zdarzenia z Monitora stanu i środowiska uruchomieniowego Redfield, należy użyć tego przełącznika.

### <a name="-verbose"></a>-Verbose
**Typowy parametr.** W danych wyjściowych szczegółowych dzienników, należy użyć tego przełącznika.



## <a name="output"></a>Output


### <a name="example-of-application-startup-logs"></a>Przykład dzienników uruchamiania aplikacji
```
PS C:\Windows\system32> Start-ApplicationInsightsMonitoringTrace -ColectRedfieldEvents
Starting...
Log File: C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\logs\20190627_144217_ApplicationInsights_ETW_Trace.etl
Tracing enabled, waiting for events.
Tracing will timeout in 5 minutes. Press CTRL+C to cancel.

2:42:31 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Resolved variables to: MicrosoftAppInsights_ManagedHttpModulePath='C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll', MicrosoftAppInsights_ManagedHttpModuleType='Microsoft.ApplicationInsights.RedfieldIISModule.RedfieldIISModule'
2:42:31 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Resolved variables to: MicrosoftDiagnosticServices_ManagedHttpModulePath2='', MicrosoftDiagnosticServices_ManagedHttpModuleType2=''
2:42:31 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Environment variable 'MicrosoftDiagnosticServices_ManagedHttpModulePath2' or 'MicrosoftDiagnosticServices_ManagedHttpModuleType2' is null, skipping managed dll loading
2:42:31 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace MulticastHttpModule.constructor, success, 70 ms
2:42:31 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace Current assembly 'Microsoft.ApplicationInsights.RedfieldIISModule, Version=2.8.18.27202, Culture=neutral, PublicKeyToken=f23a46de0be5d6f3' location 'C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll'
2:42:31 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace Matched filter '.*'~'STATUSMONITORTE', '.*'~'DemoWithSql'
2:42:31 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace Lightup assembly calculated path: 'C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.Redfield.Lightup.dll'
2:42:31 PM EVENT: Microsoft-ApplicationInsights-FrameworkLightup Trace Loaded applicationInsights.config from assembly's resource Microsoft.ApplicationInsights.Redfield.Lightup, Version=2.8.18.27202, Culture=neutral, PublicKeyToken=f23a46de0be5d6f3/Microsoft.ApplicationInsights.Redfield.Lightup.ApplicationInsights-recommended.config
2:42:34 PM EVENT: Microsoft-ApplicationInsights-FrameworkLightup Trace Successfully attached ApplicationInsights SDK
2:42:34 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace RedfieldIISModule.LoadLightupAssemblyAndGetLightupHttpModuleClass, success, 2687 ms
2:42:34 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace RedfieldIISModule.CreateAndInitializeApplicationInsightsHttpModules(lightupHttpModuleClass), success
2:42:34 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace ManagedHttpModuleHelper, multicastHttpModule.Init() success, 3288 ms
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Resolved variables to: MicrosoftAppInsights_ManagedHttpModulePath='C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll', MicrosoftAppInsights_ManagedHttpModuleType='Microsoft.ApplicationInsights.RedfieldIISModule.RedfieldIISModule'
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Resolved variables to: MicrosoftDiagnosticServices_ManagedHttpModulePath2='', MicrosoftDiagnosticServices_ManagedHttpModuleType2=''
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Environment variable 'MicrosoftDiagnosticServices_ManagedHttpModulePath2' or 'MicrosoftDiagnosticServices_ManagedHttpModuleType2' is null, skipping managed dll loading
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace MulticastHttpModule.constructor, success, 0 ms
2:42:35 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace RedfieldIISModule.CreateAndInitializeApplicationInsightsHttpModules(lightupHttpModuleClass), success
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace ManagedHttpModuleHelper, multicastHttpModule.Init() success, 0 ms
Timeout Reached. Stopping...
```


## <a name="next-steps"></a>Kolejne kroki

Dodatkowe procedury rozwiązywania problemów:

- Przejrzyj dodatkowe kroki rozwiązywania problemów w tym miejscu: https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-troubleshoot
- Przegląd [dokumentacja interfejsu API](status-monitor-v2-overview.md#powershell-api-reference) Aby dowiedzieć się więcej na temat parametrów, które mogły zostać pominięte.
- Jeśli potrzebujesz dodatkowej pomocy, możesz skontaktować się nam na [GitHub](https://github.com/Microsoft/ApplicationInsights-Home/issues).



 Wykonuj więcej zadań dzięki v2 Monitora stanu:
 - Użyj zapoznaj się z przewodnikiem [Rozwiązywanie problemów z](status-monitor-v2-troubleshoot.md) Monitora stanu w wersji 2.
 - [Pobierz konfigurację](status-monitor-v2-api-get-config.md) aby upewnić się, że Twoje ustawienia zostały prawidłowo zarejestrowane.
 - [Pobierz stan](status-monitor-v2-api-get-status.md) do inspekcji, monitorowania.
