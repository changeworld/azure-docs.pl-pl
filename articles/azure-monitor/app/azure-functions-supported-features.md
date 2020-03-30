---
title: Usługa Azure Application Insights — obsługiwane funkcje usług Azure Functions
description: Obsługiwane funkcje usługi Application Insights dla funkcji platformy Azure
ms.topic: reference
author: TimothyMothra
ms.author: tilee
ms.date: 4/23/2019
ms.reviewer: mbullwin
ms.openlocfilehash: cf0c97fd65f9966bf42fa22e2c8f92263952cb7a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77655654"
---
# <a name="application-insights-for-azure-functions-supported-features"></a>Obsługiwane funkcje usługi Application Insights for Azure Functions

Usługa Azure Functions oferuje [wbudowaną integrację](../../azure-functions/functions-monitoring.md) z usługą Application Insights, która jest dostępna za pośrednictwem interfejsu ILogger. Poniżej znajduje się lista aktualnie obsługiwanych funkcji. Zapoznaj się z przewodnikiem usługi Azure Functions dla [wprowadzenie.](../../azure-functions/functions-monitoring.md#enable-application-insights-integration)

Aby uzyskać więcej informacji na temat wersji środowiska wykonawczego funkcji, zobacz [tutaj](../../azure-functions/functions-versions.md).

Aby uzyskać więcej informacji na temat zgodnych wersji usługi Application Insights, zobacz [zależności](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Logging.ApplicationInsights/).

## <a name="supported-features"></a>Obsługiwane funkcje

| Azure Functions                       | Wersja 1                | V2 & V3   | 
|-----------------------------------    |---------------    |------------------ |
| | | | 
| **Automatyczna zbiórka**        |                 |                   |               
| &bull;Żądania                     | Tak             | Tak               | 
| &bull;Wyjątki                   | Tak             | Tak               | 
| &bull;Liczniki wydajności         | Tak             | Tak               |
| &bull;Zależności                   |                   |                   |               
| &nbsp;&nbsp;&nbsp;&mdash;Protokół HTTP      |                 | Tak               | 
| &nbsp;&nbsp;&nbsp;&mdash;ServiceBus (ServiceBus)|                 | Tak               | 
| &nbsp;&nbsp;&nbsp;&mdash;EventHub (Polski)  |                 | Tak               | 
| &nbsp;&nbsp;&nbsp;&mdash;Sql       |                 | Tak               | 
| | | | 
| **Obsługiwane funkcje**                |                   |                   |               
| &bull;Metryki QuickPulse/LiveMetrics       | Tak             | Tak               | 
| &nbsp;&nbsp;&nbsp;&mdash;Bezpieczny kanał sterowania|                 | Tak               | 
| &bull;Pobierania próbek                     | Tak             | Tak               | 
| &bull;Bicie serca                   |                 | Tak               | 
| | | | 
| **Korelacja**                       |                   |                   |               
| &bull;ServiceBus (ServiceBus)                     |                   | Tak               | 
| &bull;EventHub (Polski)                       |                   | Tak               | 
| | | | 
| **Możliwość konfiguracji**                      |                   |                   |           
| &bull;W pełni konfigurowalny.<br/>Aby uzyskać instrukcje, zobacz [usługi Azure Functions.](https://github.com/Microsoft/ApplicationInsights-aspnetcore/issues/759#issuecomment-426687852)<br/>Zobacz [Asp.NET Core](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Custom-Configuration) dla wszystkich opcji.               |                   | Tak                   | 


## <a name="performance-counters"></a>Liczniki wydajności

Automatyczna kolekcja liczników wydajności działa tylko na komputerach z systemem Windows.


## <a name="live-metrics--secure-control-channel"></a>Metryki na żywo & bezpiecznym kanale sterowania

Określone kryteria filtrów niestandardowych są wysyłane z powrotem do składnika Aktywne metryki w zestawie SDK usługi Application Insights. Filtry mogą potencjalnie zawierać poufne informacje, takie jak identyfikatory identyfikatorów customerI. Kanał można zabezpieczyć za pomocą tajnego klucza interfejsu API. Aby uzyskać instrukcje, zobacz [Zabezpieczanie kanału sterowania.](https://docs.microsoft.com/azure/azure-monitor/app/live-stream#secure-the-control-channel)

## <a name="sampling"></a>Próbkowanie

Usługa Azure Functions domyślnie włącza próbkowanie w ich konfiguracji. Aby uzyskać więcej informacji, zobacz [Konfigurowanie próbkowania](https://docs.microsoft.com/azure/azure-functions/functions-monitoring#configure-sampling).

Jeśli projekt ma zależność od SDK usługi Application Insights do ręcznego śledzenia telemetrii, może wystąpić dziwne zachowanie, jeśli konfiguracja próbkowania jest inna niż konfiguracja próbkowania funkcji. 

Zalecamy użycie tej samej konfiguracji co funkcje. Za pomocą **funkcji w wersji 2**można uzyskać tę samą konfigurację za pomocą iniekcji zależności w konstruktorze:

```csharp
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.Extensibility;

public class Function1 
{

    private readonly TelemetryClient telemetryClient;

    public Function1(TelemetryConfiguration configuration)
    {
        this.telemetryClient = new TelemetryClient(configuration);
    }

    [FunctionName("Function1")]
    public async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req, ILogger logger)
    {
        this.telemetryClient.TrackTrace("C# HTTP trigger function processed a request.");
    }
}
```
