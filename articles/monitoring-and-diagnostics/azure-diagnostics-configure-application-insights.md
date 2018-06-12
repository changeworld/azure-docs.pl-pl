---
title: Skonfiguruj diagnostyki Azure w celu wysyłania danych do usługi Application Insights
description: Zaktualizuj konfigurację publicznego diagnostyki Azure do przesyłania danych do usługi Application Insights.
services: azure-monitor
author: rboucher
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/19/2016
ms.author: robb
ms.component: diagnostic-extension
ms.openlocfilehash: 3e1f4076c7a90cbb348f31b7b92e745fff79a04f
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35262141"
---
# <a name="send-cloud-service-virtual-machine-or-service-fabric-diagnostic-data-to-application-insights"></a>Wyślij dane diagnostyczne usługi w chmurze maszyny wirtualnej i sieci szkieletowej usług do usługi Application Insights
Usługi w chmurze, maszyn wirtualnych, zestawy skalowania maszyny wirtualnej i sieci szkieletowej usług wszystkie używane rozszerzenie diagnostyki Azure do zbierania danych.  Diagnostyka Azure wysyła dane do tabel usługi Azure Storage.  Można jednak również potoku wszystkie lub podzbiór danych do innych lokalizacji przy użyciu rozszerzenia diagnostyki Azure w wersji 1.5 lub nowszego.

W tym artykule opisano sposób wysyłania danych z rozszerzenia diagnostyki Azure do usługi Application Insights.

## <a name="diagnostics-configuration-explained"></a>Wyjaśniono konfiguracji diagnostyki
Wychwytywanie rozszerzenia diagnostyki Azure w wersji 1.5 wprowadzono, które są dodatkowe lokalizacje, w którym może wysyłać dane diagnostyczne.

Przykład konfiguracji zbiorniku dla usługi Application Insights:

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
- **Zbiornika** *nazwa* atrybut jest wartość ciągu, który unikatowo identyfikuje obiekt sink.

- **ApplicationInsights** element określa klucz Instrumentacji zasobu usługi Application insights wysyłania danych diagnostycznych platformy Azure.
    - Jeśli nie masz istniejący zasób usługi Application Insights, zobacz [utworzyć nowy zasób usługi Application Insights](../application-insights/app-insights-create-new-resource.md) Aby uzyskać więcej informacji na temat tworzenia zasobu i pobierania klucza instrumentacji.
    - Jeśli tworzysz usługi w chmurze z Azure SDK 2.8 i nowsze, ten klucz Instrumentacji jest wypełniane automatycznie. Wartość na podstawie **APPINSIGHTS_INSTRUMENTATIONKEY** ustawienie konfiguracyjne usługi podczas pakowania projektu usługi w chmurze. Zobacz [usługi Application Insights za pomocą usługi w chmurze](../application-insights/app-insights-cloudservices.md).

- **Kanałów** elementu zawiera jeden lub więcej **kanału** elementów.
    - *Nazwa* jednoznacznie odnosi się do tego kanału.
    - *Loglevel* atrybutu pozwala określić poziom dziennika, który umożliwia kanału. Dostępne są następujące poziomy rejestrowania dostępne w kolejności od najbardziej do najmniej informacji:
        - Pełne
        - Informacje
        - Ostrzeżenie
        - Błąd
        - Krytyczne

Kanał działa jak filtr i służy do wybierania określonego dziennika poziomów do wysłania do docelowej obiekt sink. Na przykład można zebrać dzienniki pełne i wysyłać je do magazynu, ale tylko błędy wysyłania do ujścia.

Opisywaną relację przedstawiono na poniższym rysunku.

![Publiczna Konfiguracja diagnostyki](./media/azure-diagnostics-configure-applicationinsights/AzDiag_Channels_App_Insights.png)

Na rysunku poniżej przedstawiono wartości konfiguracji i jak działają. Wychwytywanie wielu można uwzględnić w konfiguracji na różnych poziomach hierarchii. Obiekt sink na najwyższym poziomie działa jako ustawienie globalne, określony w elemencie poszczególnych działa jak zastąpienie tego ustawienia globalnego.

![Diagnostyka wychwytywanie konfiguracji z usługą Application Insights](./media/azure-diagnostics-configure-applicationinsights/Azure_Diagnostics_Sinks.png)

## <a name="complete-sink-configuration-example"></a>Przykład konfiguracji zbiornika ukończone
W tym miejscu jest kompletnym przykładem publicznej konfiguracji pliku
1. wysyła wszystkie błędy do usługi Application Insights (określony w **DiagnosticMonitorConfiguration** węzła)
2. wysyła również dzienniki poziomu Verbose dzienników aplikacji (określony w **dzienniki** węzła).

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

### <a name="send-all-the-data-that-is-being-collected-by-azure-diagnostics"></a>Wyślij wszystkie dane, które jest zbieranych przez Diagnostyka Azure

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

### <a name="send-verbose-application-logs-to-application-insights"></a>Wysyłanie dzienników pełne aplikacji do usługi Application Insights

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

- **Kanały zalogować się wyłącznie liczniki wydajności nie i typu.** Jeśli określisz kanał z elementem licznika wydajności jest ignorowana.
- **Poziom dziennika dla kanału nie może przekraczać poziom dziennika co zbieranych przez diagnostycznych platformy Azure.** Na przykład nie można gromadzić błędy w dzienniku aplikacji w elemencie dzienniki i spróbuj wysłać pełne dzienniki do ujścia szczegółowe informacje o aplikacji. *ScheduledTransferLogLevelFilter* atrybutów zawsze należy zebrać takie same lub więcej dzienników niż dzienniki chcesz wysyłać do ujścia.
- **Nie można wysłać danych obiektów blob zbierane przez rozszerzenie diagnostyki Azure do usługi Application Insights.** Na przykład niczego określone w obszarze *katalogów* węzła. Dla zrzuty awaryjne zrzutu awaryjnego rzeczywiste są wysyłane do magazynu obiektów blob i tylko wygenerowano zrzutu awaryjnego powiadomienie jest wysyłane do usługi Application Insights.

## <a name="next-steps"></a>Następne kroki
* Dowiedz się, jak [wyświetlania informacji diagnostycznych platformy Azure](https://docs.microsoft.com/azure/application-insights/app-insights-cloudservices#view-azure-diagnostic-events) w usłudze Application Insights.
* Użyj [PowerShell](../cloud-services/cloud-services-diagnostics-powershell.md) można włączyć rozszerzenia diagnostyki Azure dla aplikacji.
* Użyj [programu Visual Studio](../vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md) można włączyć rozszerzenia diagnostyki Azure dla aplikacji
