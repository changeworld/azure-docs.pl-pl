---
title: Obsługiwane funkcje platformy Azure Application Insights Azure Functions
description: Application Insights obsługiwane funkcje Azure Functions
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: reference
author: TimothyMothra
ms.author: tilee
ms.date: 4/23/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 5688bd92e63016e195726488bd9ff9ed039c5795
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74928419"
---
# <a name="application-insights-for-azure-functions-supported-features"></a>Application Insights Azure Functions obsługiwanych funkcji

Azure Functions oferuje [wbudowaną integrację](../../azure-functions/functions-monitoring.md) z usługą Application Insights, która jest dostępna za pomocą interfejsu ILogger. Poniżej znajduje się lista obecnie obsługiwanych funkcji. Zapoznaj się z tematem "Przewodnik po [rozpoczęciu](../../azure-functions/functions-monitoring.md#enable-application-insights-integration)Azure Functions".

## <a name="supported-features"></a>Obsługiwane funkcje

| Stan usługi Funkcje Azure                       | Wersja 1                | V2 (zapłon 2018)  | 
|-----------------------------------    |---------------    |------------------ |
| **Zestaw SDK Application Insights .NET**   | **2.5.0**       | **2.9.1**         |
| | | | 
| **Automatyczne zbieranie**        |                 |                   |               
| Żądania &bull;                     | Tak             | Tak               | 
| Wyjątki &bull;                   | Tak             | Tak               | 
| &bull; liczniki wydajności         | Tak             | Tak               |
| &bull; zależności                   |                   |                   |               
| &nbsp;&nbsp;&nbsp;&mdash; HTTP      |                 | Tak               | 
| &nbsp;&nbsp;&nbsp;&mdash; ServiceBus|                 | Tak               | 
| &nbsp;&nbsp;&nbsp;&mdash; EventHub  |                 | Tak               | 
| &nbsp;&nbsp;&nbsp;&mdash; SQL       |                 | Tak               | 
| | | | 
| **Obsługiwane funkcje**                |                   |                   |               
| &bull; QuickPulse/LiveMetrics       | Tak             | Tak               | 
| &nbsp;&nbsp;&nbsp;&mdash; kanał Secure Control|                 | Tak               | 
| Próbkowanie &bull;                     | Tak             | Tak               | 
| &bull; pulsy                   |                 | Tak               | 
| | | | 
| **Korelacja**                       |                   |                   |               
| &bull; ServiceBus                     |                   | Tak               | 
| &bull; centrum zdarzeń                       |                   | Tak               | 
| | | | 
| **Skonfigurować**                      |                   |                   |           
| &bull;w pełni konfigurowalne.<br/>Aby uzyskać instrukcje, zobacz [Azure Functions](https://github.com/Microsoft/ApplicationInsights-aspnetcore/issues/759#issuecomment-426687852) .<br/>Zobacz [ASP.NET Core](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Custom-Configuration) , aby poznać wszystkie opcje.               |                   | Tak                   | 


## <a name="performance-counters"></a>Liczniki wydajności

Automatyczne zbieranie liczników wydajności działa tylko na maszynach z systemem Windows.


## <a name="live-metrics--secure-control-channel"></a>Metryki na żywo & kanale bezpiecznego sterowania

Określone kryteria filtrów niestandardowych są wysyłane z powrotem do składnika metryki na żywo w zestawie Application Insights SDK. Filtry mogą potencjalnie zawierać informacje poufne, takie jak customerID. Kanał można zabezpieczyć przy użyciu klucza tajnego interfejsu API. Instrukcje można znaleźć w temacie [Zabezpieczenia kanału kontroli](https://docs.microsoft.com/azure/azure-monitor/app/live-stream#secure-the-control-channel) .

## <a name="sampling"></a>Próbkowanie

Azure Functions domyślnie włącza próbkowanie w ich konfiguracji. Aby uzyskać więcej informacji, zobacz [Konfigurowanie próbkowania](https://docs.microsoft.com/azure/azure-functions/functions-monitoring#configure-sampling).

Jeśli projekt przyjmuje zależność od zestawu SDK Application Insights do ręcznego śledzenia danych telemetrycznych, może wystąpić dziwne zachowanie, jeśli konfiguracja próbkowania różni się od konfiguracji próbkowania funkcji. 

Zalecamy używanie tej samej konfiguracji co funkcje. Z **funkcjami w wersji 2**można uzyskać tę samą konfigurację przy użyciu iniekcji zależności w konstruktorze:

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
