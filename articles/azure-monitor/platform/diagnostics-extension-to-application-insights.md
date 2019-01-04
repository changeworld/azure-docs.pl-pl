---
title: Konfigurowanie diagnostyki Azure do przesyłania danych do usługi Application Insights
description: Zaktualizuj konfigurację publicznej diagnostyki Azure do przesyłania danych do usługi Application Insights.
services: azure-monitor
author: rboucher
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/19/2016
ms.author: robb
ms.component: diagnostic-extension
ms.openlocfilehash: c22c97efed7c70b554acd449ed15c6641e377b6a
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/27/2018
ms.locfileid: "53788501"
---
# <a name="send-cloud-service-virtual-machine-or-service-fabric-diagnostic-data-to-application-insights"></a>Wysyłanie danych diagnostycznych usługi w chmurze, maszyny wirtualnej lub usługi Service Fabric do usługi Application Insights
Usług cloud services, Virtual Machines, Virtual Machine Scale Sets i usługi Service Fabric wszystkich umożliwia zbieranie danych przez rozszerzenie Diagnostyka Azure.  Diagnostyka Azure wysyła dane do tabel usługi Azure Storage.  Można jednak również potoku wszystkie lub podzbiór danych do innych lokalizacji za pomocą rozszerzenia diagnostyki platformy Azure w wersji 1.5 lub nowszej.

W tym artykule opisano sposób wysyłania danych z rozszerzenia diagnostyki Azure do usługi Application Insights.

## <a name="diagnostics-configuration-explained"></a>Konfiguracja diagnostyki wyjaśniono
Rozszerzenie diagnostyki platformy Azure w wersji 1.5, wprowadzono wychwytywanie, które są dodatkowych lokalizacjach, w którym można wysyłać dane diagnostyczne.

Przykład konfiguracji obiekt sink dla usługi Application Insights:

```XML
<SinksConfig>
    <Sink name="ApplicationInsights">
      <ApplicationInsights>{Insert InstrumentationKey}</ApplicationInsights>
      <Channels>
        <Channel logLevel="Error" name="MyTopDiagData"  />
        <Channel logLevel="Verbose" name="MyLogData"  />
      </Channels>
    </Sink>
</SinksConfig>
```
```JSON
"SinksConfig": {
    "Sink": [
        {
            "name": "ApplicationInsights",
            "ApplicationInsights": "{Insert InstrumentationKey}",
            "Channels": {
                "Channel": [
                    {
                        "logLevel": "Error",
                        "name": "MyTopDiagData"
                    },
                    {
                        "logLevel": "Error",
                        "name": "MyLogData"
                    }
                ]
            }
        }
    ]
}
```
- **Ujścia** *nazwa* atrybut jest wartość ciągu, który unikatowo identyfikuje obiekt sink.

- **ApplicationInsights** element określa klucz Instrumentacji zasobu usługi Application insights wysyłania danych diagnostycznych platformy Azure.
    - Jeśli nie masz istniejącego zasobu usługi Application Insights, zobacz [Utwórz nowy zasób usługi Application Insights](../../application-insights/app-insights-create-new-resource.md) Aby uzyskać więcej informacji na temat tworzenia zasobu i uzyskiwanie klucza instrumentacji.
    - Jeśli opracowujesz usługi w chmurze przy użyciu Azure SDK 2.8 lub nowszy, ten klucz Instrumentacji jest wypełniane automatycznie. Wartość zależy od **APPINSIGHTS_INSTRUMENTATIONKEY** ustawienie konfiguracyjne usługi podczas pakowania projektu usługi w chmurze. Zobacz [usługi Application Insights za pomocą usług w chmurze](../../azure-monitor/app/cloudservices.md).

- **Kanały** elementu zawiera jeden lub więcej **kanału** elementów.
    - *Nazwa* jednoznacznie odnosi się do tego kanału.
    - *Loglevel* atrybut pozwala określić poziom rejestrowania, który umożliwia kanału. Dostępne są następujące poziomy dziennika dostępne w kolejności od najbardziej do najmniej informacji:
        - Pełne
        - Informacje
        - Ostrzeżenie
        - Błąd
        - Krytyczny

Kanał działa jak filtr i umożliwia wybranie określonego dziennika poziomów do wysłania do ujścia docelowego. Na przykład może zbierać pełne dzienniki i wysyłać je do magazynu, ale tylko błędy wysyłania do ujścia.

Opisywaną relację przedstawiono na poniższym rysunku.

![Publiczna Konfiguracja diagnostyki](media/diagnostics-extension-to-application-insights/AzDiag_Channels_App_Insights.png)

Poniższa ilustracja zawiera podsumowanie wartości konfiguracji i sposobie ich działania. Może zawierać wiele ujścia w konfiguracji na różnych poziomach hierarchii. Obiekt sink na najwyższym poziomie działa jako ustawienie globalne i określona na pojedynczego elementu zachowuje się jak zastąpienie tego ustawienia globalnego.

![Diagnostyka wychwytywanie konfiguracji za pomocą usługi Application Insights](media/diagnostics-extension-to-application-insights/Azure_Diagnostics_Sinks.png)

## <a name="complete-sink-configuration-example"></a>Przykład konfiguracji pełną ujścia
Oto pełny przykład publiczna Konfiguracja pliku
1. wysyła wszystkie błędy do usługi Application Insights (określony w **DiagnosticMonitorConfiguration** węzeł)
2. wysyła również pełne dzienniki poziom Dzienniki aplikacji (określony w **dzienniki** węzła).

```XML
<WadCfg>
  <DiagnosticMonitorConfiguration overallQuotaInMB="4096"
       sinks="ApplicationInsights.MyTopDiagData"> <!-- All info below sent to this channel -->
    <DiagnosticInfrastructureLogs />
    <PerformanceCounters>
      <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" />
      <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
    </PerformanceCounters>
    <WindowsEventLog scheduledTransferPeriod="PT1M">
      <DataSource name="Application!*" />
    </WindowsEventLog>
    <Logs scheduledTransferPeriod="PT1M" scheduledTransferLogLevelFilter="Verbose"
            sinks="ApplicationInsights.MyLogData"/> <!-- This specific info sent to this channel -->
  </DiagnosticMonitorConfiguration>

<SinksConfig>
    <Sink name="ApplicationInsights">
      <ApplicationInsights>{Insert InstrumentationKey}</ApplicationInsights>
      <Channels>
        <Channel logLevel="Error" name="MyTopDiagData"  />
        <Channel logLevel="Verbose" name="MyLogData"  />
      </Channels>
    </Sink>
  </SinksConfig>
</WadCfg>
```
```JSON
"WadCfg": {
    "DiagnosticMonitorConfiguration": {
        "overallQuotaInMB": 4096,
        "sinks": "ApplicationInsights.MyTopDiagData", "_comment": "All info below sent to this channel",
        "DiagnosticInfrastructureLogs": {
        },
        "PerformanceCounters": {
            "PerformanceCounterConfiguration": [
                {
                    "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                    "sampleRate": "PT3M"
                },
                {
                    "counterSpecifier": "\\Memory\\Available MBytes",
                    "sampleRate": "PT3M"
                }
            ]
        },
        "WindowsEventLog": {
            "scheduledTransferPeriod": "PT1M",
            "DataSource": [
                {
                    "name": "Application!*"
                }
            ]
        },
        "Logs": {
            "scheduledTransferPeriod": "PT1M",
            "scheduledTransferLogLevelFilter": "Verbose",
            "sinks": "ApplicationInsights.MyLogData", "_comment": "This specific info sent to this channel"
        }
    },
    "SinksConfig": {
        "Sink": [
            {
                "name": "ApplicationInsights",
                "ApplicationInsights": "{Insert InstrumentationKey}",
                "Channels": {
                    "Channel": [
                        {
                            "logLevel": "Error",
                            "name": "MyTopDiagData"
                        },
                        {
                            "logLevel": "Verbose",
                            "name": "MyLogData"
                        }
                    ]
                }
            }
        ]
    }
}
```
W poprzedniej konfiguracji następujące wiersze mają następujące znaczenie:

### <a name="send-all-the-data-that-is-being-collected-by-azure-diagnostics"></a>Wyślij wszystkie dane, które są zbierane przez narzędzie diagnostyczne systemu Azure

```XML
<DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights">
```
```JSON
"DiagnosticMonitorConfiguration": {
    "overallQuotaInMB": 4096,
    "sinks": "ApplicationInsights",
}
```

### <a name="send-only-error-logs-to-the-application-insights-sink"></a>Wyślij tylko dzienniki błędów do ujścia usługi Application Insights

```XML
<DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights.MyTopDiagdata">
```
```JSON
"DiagnosticMonitorConfiguration": {
    "overallQuotaInMB": 4096,
    "sinks": "ApplicationInsights.MyTopDiagData",
}
```

### <a name="send-verbose-application-logs-to-application-insights"></a>Wyślij dzienniki pełne aplikacji do usługi Application Insights

```XML
<Logs scheduledTransferPeriod="PT1M" scheduledTransferLogLevelFilter="Verbose" sinks="ApplicationInsights.MyLogData"/>
```
```JSON
"DiagnosticMonitorConfiguration": {
    "overallQuotaInMB": 4096,
    "sinks": "ApplicationInsights.MyLogData",
}
```

## <a name="limitations"></a>Ograniczenia

- **Kanały zalogować się wyłącznie typu i nie liczników wydajności.** Jeśli określisz kanału z elementem licznika wydajności jest ignorowany.
- **Poziom rejestrowania dla kanału nie może przekraczać poziom dziennika, na jakie są zbierane przez narzędzie diagnostyczne systemu Azure.** Na przykład nie zbieraj błędy w dzienniku aplikacji w elemencie dzienniki i spróbuj wysłać pełne dzienniki do ujścia Application Insights. *ScheduledTransferLogLevelFilter* atrybutów zawsze należy zebrać równe lub dzienniki więcej niż dzienniki chcesz wysłać do ujścia.
- **Nie można wysłać danych obiektów blob, zebrane przez rozszerzenie diagnostyki platformy Azure do usługi Application Insights.** Na przykład niczego określone w obszarze *katalogi* węzła. Dla zrzuty awaryjne zrzutu awaryjnego rzeczywiste są wysyłane do magazynu obiektów blob i tylko powiadomienie wygenerowania zrzutu awaryjnego jest wysyłane do usługi Application Insights.

## <a name="next-steps"></a>Następne kroki
* Dowiedz się, jak [wyświetlanie informacji o Twoim Diagnostyka Azure](https://docs.microsoft.com/azure/application-insights/app-insights-cloudservices#view-azure-diagnostic-events) w usłudze Application Insights.
* Użyj [PowerShell](../../cloud-services/cloud-services-diagnostics-powershell.md) włączyć rozszerzenie diagnostyki platformy Azure dla aplikacji.
* Użyj [programu Visual Studio](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines) włączyć rozszerzenie diagnostyki platformy Azure dla aplikacji
