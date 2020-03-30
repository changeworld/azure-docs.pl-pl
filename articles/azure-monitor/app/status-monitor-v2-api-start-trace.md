---
title: Odwołanie do interfejsu API agenta usługi Azure Application Insights
description: Odwołanie do interfejsu API agenta usługi Application Insights. Śledzenie rozruchu. Zbieranie dzienników ETW z monitora stanu i sdk usługi Application Insights.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: b9680101f1a22dd6d9c1617c8afc13a10ad1c594
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671226"
---
# <a name="application-insights-agent-api-start-applicationinsightsmonitoringtrace"></a>Interfejs API agenta usługi Application Insights: Uruchamianie obrazów systemu monitorowania aplikacji

W tym artykule opisano polecenie cmdlet, który jest członkiem [modułu Az.ApplicationMonitor PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

## <a name="description"></a>Opis

Zbiera [zdarzenia ETW](https://docs.microsoft.com/windows/desktop/etw/event-tracing-portal) z środowiska wykonawczego dołączania bez kodu. To polecenie cmdlet jest alternatywą dla uruchamiania [Programu PerfView](https://github.com/microsoft/perfview).

Zebrane zdarzenia zostaną wydrukowane na konsoli w czasie rzeczywistym i zapisane w pliku ETL. Wyjściowy plik ETL może zostać otwarty przez [PerfView](https://github.com/microsoft/perfview) w celu dalszego zbadania.

To polecenie cmdlet będzie działać do momentu osiągnięcia limitu czasu (domyślnie`Ctrl + C`5 minut) lub ręcznego zatrzymania ( ).

> [!IMPORTANT] 
> To polecenie cmdlet wymaga sesji programu PowerShell z uprawnieniami administratora.

## <a name="examples"></a>Przykłady

### <a name="how-to-collect-events"></a>Jak zbierać wydarzenia

Zwykle prosimy o zbieranie zdarzeń w celu zbadania, dlaczego aplikacja nie jest instrumentowana.

Środowisko wykonawcze dołączania bez kodu będzie emitować zdarzenia ETW podczas uruchamiania usług IIS i uruchamiania aplikacji.

Aby zebrać te zdarzenia:
1. W konsoli cmd z uprawnieniami `iisreset /stop` administratora wykonaj, aby wyłączyć usługi IIS i wszystkie aplikacje internetowe.
2. Wykonaj tę polecenie cmdlet
3. W konsoli cmd z uprawnieniami `iisreset /start` administratora uruchom aby uruchomić usługi IIS.
4. Spróbuj przejść do aplikacji.
5. Po zakończeniu ładowania aplikacji można ją ręcznie`Ctrl + C`zatrzymać ( ) lub poczekać na limit czasu.

### <a name="what-events-to-collect"></a>Jakie wydarzenia zebrać

Podczas zbierania zdarzeń dostępne są trzy opcje:
1. Użyj przełącznika `-CollectSdkEvents` do zbierania zdarzeń emitowanych z SDK usługi Application Insights.
2. Użyj przełącznika `-CollectRedfieldEvents` do zbierania zdarzeń emitowanych przez Monitor stanu i Środowisko uruchomieniowe Redfield. Te dzienniki są przydatne podczas diagnozowania usług IIS i uruchamiania aplikacji.
3. Użyj obu przełączników, aby zebrać oba typy zdarzeń.
4. Domyślnie, jeśli nie przełącznik jest określony oba typy zdarzeń będą zbierane.


## <a name="parameters"></a>Parametry

### <a name="-maxdurationinminutes"></a>-MaxDurationInMinutes
**Opcjonalne.** Ten parametr służy do ustawiania czasu zbierania zdarzeń przez ten skrypt. Wartość domyślna to 5 minut.

### <a name="-logdirectory"></a>-LogDirectory
**Opcjonalne.** Ten przełącznik służy do ustawiania katalogu wyjściowego pliku ETL. Domyślnie ten plik zostanie utworzony w katalogu modułów programu PowerShell. Pełna ścieżka zostanie wyświetlona podczas wykonywania skryptu.


### <a name="-collectsdkevents"></a>-CollectSdkEvents
**Opcjonalne.** Ten przełącznik służy do zbierania zdarzeń SDK usługi Application Insights.

### <a name="-collectredfieldevents"></a>-CollectRedfieldNiejściacje
**Opcjonalne.** Ten przełącznik służy do zbierania zdarzeń z Monitora stanu i środowiska wykonawczego Redfield.

### <a name="-verbose"></a>-Pełne
**Wspólny parametr.** Użyj tego przełącznika do wyprowadzania szczegółowych dzienników.



## <a name="output"></a>Dane wyjściowe


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


## <a name="next-steps"></a>Następne kroki

Dodatkowe rozwiązywanie problemów:

- Zapoznaj się z dodatkowymi krokami rozwiązywania problemów tutaj:https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-troubleshoot
- Przejrzyj [odwołanie do interfejsu API,](status-monitor-v2-overview.md#powershell-api-reference) aby dowiedzieć się więcej o parametrach, które mogły zostać pominięte.
- Jeśli potrzebujesz dodatkowej pomocy, możesz skontaktować się z nami na [GitHub](https://github.com/Microsoft/ApplicationInsights-Home/issues).



 Więcej informacji za pomocą agenta usługi Application Insights:
 - Skorzystaj z naszego [przewodnika,](status-monitor-v2-troubleshoot.md) aby rozwiązać problem z agentem aplikacji Insights.
 - [Pobierz config,](status-monitor-v2-api-get-config.md) aby potwierdzić, że ustawienia zostały nagrane poprawnie.
 - [Uzyskaj stan,](status-monitor-v2-api-get-status.md) aby sprawdzić monitorowanie.
