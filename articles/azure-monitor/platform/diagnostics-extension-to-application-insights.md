---
title: Wysyłanie danych diagnostyki platformy Azure do usługi Application Insights
description: Zaktualizuj publiczną konfigurację diagnostyki platformy Azure, aby wysyłać dane do usługi Application Insights.
ms.subservice: diagnostic-extension
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/19/2016
ms.openlocfilehash: 80d971abd248ca8253a374b488c693ea9aa2ea3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77672331"
---
# <a name="send-cloud-service-virtual-machine-or-service-fabric-diagnostic-data-to-application-insights"></a>Wysyłanie danych diagnostycznych usługi w chmurze, maszyny wirtualnej lub sieci szkieletowej usług do usługi Application Insights
Usługi w chmurze, maszyny wirtualne, zestawy skalowania maszyn wirtualnych i sieć szkieletowa usług używają rozszerzenia Diagnostyka Azure do zbierania danych.  Diagnostyka platformy Azure wysyła dane do tabel usługi Azure Storage.  Można jednak również potoku wszystkie lub podzbiór danych do innych lokalizacji przy użyciu rozszerzenia diagnostyki platformy Azure 1.5 lub nowszych.

W tym artykule opisano sposób wysyłania danych z rozszerzenia diagnostyki platformy Azure do usługi Application Insights.

## <a name="diagnostics-configuration-explained"></a>Konfiguracja diagnostyki wyjaśniona
Rozszerzenie diagnostyki platformy Azure 1.5 wprowadzono pochłaniacze, które są dodatkowe lokalizacje, w których można wysyłać dane diagnostyczne.

Przykładowa konfiguracja ujścia dla usługi Application Insights:

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
- Atrybut **Sink** *Sink name* jest wartością ciągu, która jednoznacznie identyfikuje obiekt sink.

- **Element ApplicationInsights** określa klucz instrumentacji zasobu wglądu w aplikacje, w którym są wysyłane dane diagnostyczne platformy Azure.
    - Jeśli nie masz istniejącego zasobu usługi Application Insights, zobacz [Tworzenie nowego zasobu usługi Application Insights, aby](../../azure-monitor/app/create-new-resource.md ) uzyskać więcej informacji na temat tworzenia zasobu i uzyskiwania klucza instrumentacji.
    - Jeśli tworzysz usługę w chmurze przy za pomocą narzędzia Azure SDK 2.8 lub nowszych, ten klucz instrumentacji jest automatycznie wypełniany. Wartość jest oparta na ustawieniu konfiguracji usługi **APPINSIGHTS_INSTRUMENTATIONKEY** podczas pakowania projektu usługi w chmurze. Zobacz [Korzystanie z usługi Application Insights z usługami w chmurze](../../azure-monitor/app/cloudservices.md).

- **Channels** Element zawiera jeden lub więcej **Kanał** elementów.
    - Atrybut *nazwa* jednoznacznie odnosi się do tego kanału.
    - Atrybut *loglevel* umożliwia określenie poziomu dziennika, na który zezwala kanał. Dostępne poziomy dziennika w kolejności od większości do najmniejszych informacji to:
        - Pełny
        - Informacje
        - Ostrzeżenie
        - Błąd
        - Krytyczny

Kanał działa jak filtr i pozwala wybrać określone poziomy dziennika do wysłania do ujścia docelowego. Na przykład można zbierać pełne dzienniki i wysyłać je do magazynu, ale wysyłać tylko błędy do ujścia.

Na poniższej ilustracji przedstawiono tę relację.

![Diagnostyka konfiguracja publiczna](media/diagnostics-extension-to-application-insights/AzDiag_Channels_App_Insights.png)

Na poniższej ilustracji podsumowano wartości konfiguracji i ich pracę. W konfiguracji można dołączyć wiele czynników pochłaniania na różnych poziomach w hierarchii. Zlewnia na najwyższym poziomie działa jako ustawienie globalne i jeden określony w poszczególnych element działa jak zastąpienie tego ustawienia globalnego.

![Konfiguracja pochłaniania diagnostyki za pomocą usługi Application Insights](media/diagnostics-extension-to-application-insights/Azure_Diagnostics_Sinks.png)

## <a name="complete-sink-configuration-example"></a>Przykład konfiguracji zlewu kompletnego
Oto kompletny przykład publicznego pliku konfiguracyjnego, który
1. wysyła wszystkie błędy do usługi Application Insights (określonej w węźle **DiagnosticMonitorConfiguration)**
2. również wysyła pełne dzienniki poziomu dla dzienników aplikacji (określonych w węźle **Dzienniki).**

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

### <a name="send-all-the-data-that-is-being-collected-by-azure-diagnostics"></a>Wyślij wszystkie dane zbierane przez diagnostykę platformy Azure

```XML
<DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights">
```
```JSON
"DiagnosticMonitorConfiguration": {
    "overallQuotaInMB": 4096,
    "sinks": "ApplicationInsights",
}
```

### <a name="send-only-error-logs-to-the-application-insights-sink"></a>Wysyłanie tylko dzienników błędów do ujścia usługi Application Insights

```XML
<DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights.MyTopDiagdata">
```
```JSON
"DiagnosticMonitorConfiguration": {
    "overallQuotaInMB": 4096,
    "sinks": "ApplicationInsights.MyTopDiagData",
}
```

### <a name="send-verbose-application-logs-to-application-insights"></a>Wysyłanie pełnych dzienników aplikacji do usługi Application Insights

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

- **Kanały tylko typ dziennika, a nie liczniki wydajności.** Jeśli określisz kanał z elementem licznika wydajności, zostanie on zignorowany.
- **Poziom dziennika dla kanału nie może przekroczyć poziomu dziennika dla tego, co jest zbierane przez diagnostykę platformy Azure.** Na przykład nie można zebrać błędy dziennika aplikacji w logs elementu i spróbuj wysłać pełne dzienniki do ujścia wglądu w uzysk aplikacji. *ScheduledTransferLogLevelFilter* atrybut musi zawsze zbierać równe lub więcej dzienników niż dzienniki, które próbujesz wysłać do ujścia.
- **Nie można wysyłać danych obiektów blob zebranych przez rozszerzenie diagnostyki platformy Azure do usługi Application Insights.** Na przykład wszystko określone w węźle *Katalogi.* W przypadku zrzutów awaryjnych rzeczywisty zrzut awaryjny jest wysyłany do magazynu obiektów blob i tylko powiadomienie, że zrzut awaryjny został wygenerowany, jest wysyłane do usługi Application Insights.

## <a name="next-steps"></a>Następne kroki
* Dowiedz się, jak [wyświetlić informacje diagnostyczne platformy Azure](https://docs.microsoft.com/azure/application-insights/app-insights-cloudservices) w usłudze Application Insights.
* Użyj [programu PowerShell,](../../cloud-services/cloud-services-diagnostics-powershell.md) aby włączyć rozszerzenie diagnostyki platformy Azure dla aplikacji.
* Włącz rozszerzenie diagnostyki platformy Azure dla aplikacji za pomocą [programu Visual Studio](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines)

