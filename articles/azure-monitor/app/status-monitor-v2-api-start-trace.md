---
title: 'Dokumentacja interfejsu API platformy Azure monitor stanu v2: Rozpocznij śledzenie | Microsoft Docs'
description: Dokumentacja interfejsu API monitor stanu v2. Rozpocznij śledzenie. Zbierz dzienniki ETW z monitor stanu i Application Insights SDK.
services: application-insights
documentationcenter: .net
author: TimothyMothra
manager: alexklim
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: tilee
ms.openlocfilehash: f4c43e6bdb70687606041c2f0859ab072db2b587
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/23/2019
ms.locfileid: "71200376"
---
# <a name="status-monitor-v2-api-start-applicationinsightsmonitoringtrace"></a>Interfejs API monitor stanu v2: Start-ApplicationInsightsMonitoringTrace

W tym artykule opisano polecenie cmdlet, które jest członkiem [modułu programu PowerShell AZ. ApplicationMonitor](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

## <a name="description"></a>Opis

Zbiera [zdarzenia ETW](https://docs.microsoft.com/windows/desktop/etw/event-tracing-portal) z środowiska wykonawczego dołączania bezkodowego. To polecenie cmdlet jest alternatywą dla uruchamiania [Narzędzia PerfView](https://github.com/microsoft/perfview).

Zebrane zdarzenia będą drukowane w konsoli programu w czasie rzeczywistym i zapisane w pliku ETL. Wyjściowy plik ETL może być otwarty przez [Narzędzia PerfView](https://github.com/microsoft/perfview) do dalszej analizy.

To polecenie cmdlet zostanie uruchomione do momentu osiągnięcia limitu czasu trwania (domyślnie 5 minut) lub zostanie zatrzymane ręcznie (`Ctrl + C`).

> [!IMPORTANT] 
> To polecenie cmdlet wymaga sesji programu PowerShell z uprawnieniami administratora.

## <a name="examples"></a>Przykłady

### <a name="how-to-collect-events"></a>Jak zbierać zdarzenia

Zwykle będziemy zbierać zdarzenia w celu zbadania, Dlaczego aplikacja nie jest Instrumentacją.

Środowisko uruchomieniowe dołączania bezkodowego będzie emitować zdarzenia ETW podczas uruchamiania usług IIS i uruchamiania aplikacji.

Aby zebrać te zdarzenia:
1. W konsoli cmd z uprawnieniami administratora wykonaj `iisreset /stop` polecenie, aby wyłączyć usługi IIS i wszystkie aplikacje sieci Web.
2. Wykonaj to polecenie cmdlet
3. W konsoli cmd z uprawnieniami administratora uruchom polecenie, `iisreset /start` aby uruchomić usługi IIS.
4. Spróbuj przejść do swojej aplikacji.
5. Po zakończeniu ładowania aplikacji można ją zatrzymać ręcznie (`Ctrl + C`) lub poczekać na przekroczenie limitu czasu.

### <a name="what-events-to-collect"></a>Jakie zdarzenia należy zebrać

Podczas zbierania zdarzeń są dostępne trzy opcje:
1. Użyj przełącznika `-CollectSdkEvents` , aby zbierać zdarzenia emitowane z zestawu SDK Application Insights.
2. Użyj przełącznika `-CollectRedfieldEvents` , aby zbierać zdarzenia emitowane przez Monitor stanu i środowisko uruchomieniowe Redfield. Te dzienniki są przydatne podczas diagnozowania usług IIS i uruchamiania aplikacji.
3. Użyj obu przełączników do zbierania obu typów zdarzeń.
4. Domyślnie, jeśli nie określono żadnego przełącznika, zostaną zebrane oba typy zdarzeń.


## <a name="parameters"></a>Parametry

### <a name="-maxdurationinminutes"></a>-MaxDurationInMinutes
**Opcjonalnie.** Użyj tego parametru, aby określić, jak długo ten skrypt ma zbierać zdarzenia. Wartość domyślna to 5 minut.

### <a name="-logdirectory"></a>-LogDirectory
**Opcjonalnie.** Użyj tego przełącznika, aby ustawić katalog wyjściowy pliku ETL. Domyślnie ten plik zostanie utworzony w katalogu modułów programu PowerShell. Pełna ścieżka zostanie wyświetlona podczas wykonywania skryptu.


### <a name="-collectsdkevents"></a>-CollectSdkEvents
**Opcjonalnie.** Ten przełącznik umożliwia zbieranie zdarzeń Application Insights SDK.

### <a name="-collectredfieldevents"></a>-CollectRedfieldEvents
**Opcjonalnie.** Ten przełącznik służy do zbierania zdarzeń z monitor stanu i środowiska uruchomieniowego Redfield.

### <a name="-verbose"></a>-Verbose
**Wspólny parametr.** Ten przełącznik umożliwia wyprowadzanie szczegółowych dzienników.



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


## <a name="next-steps"></a>Następne kroki

Dodatkowe Rozwiązywanie problemów:

- Zapoznaj się z dodatkowymi krokami rozwiązywania problemów: https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-troubleshoot
- Przejrzyj [odwołanie do interfejsu API](status-monitor-v2-overview.md#powershell-api-reference) , aby dowiedzieć się więcej na temat parametrów, które mogły zostać pominięte.
- Jeśli potrzebujesz dodatkowej pomocy, możesz skontaktować się z nami w serwisie [GitHub](https://github.com/Microsoft/ApplicationInsights-Home/issues).



 Więcej informacji o monitor stanu v2:
 - Skorzystaj z naszego przewodnika, aby [rozwiązywać problemy z](status-monitor-v2-troubleshoot.md) Monitor stanu v2.
 - [Pobierz konfigurację](status-monitor-v2-api-get-config.md) , aby upewnić się, że Twoje ustawienia zostały poprawnie zarejestrowane.
 - [Pobierz stan,](status-monitor-v2-api-get-status.md) aby sprawdzić monitorowanie.
