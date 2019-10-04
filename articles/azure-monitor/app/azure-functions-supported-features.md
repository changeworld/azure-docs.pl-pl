---
title: Obsługiwane funkcje platformy Azure Azure Functions Application Insights | Microsoft Docs
description: Application Insights obsługiwane funkcje Azure Functions
services: application-insights
documentationcenter: .net
author: TimothyMothra
manager: ''
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: reference
ms.date: 4/23/2019
ms.reviewer: mbullwin
ms.author: tilee
ms.openlocfilehash: cf39c8b5e204493380c095519e0ff25c3ce19f68
ms.sourcegitcommit: 4d177e6d273bba8af03a00e8bb9fe51a447196d0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/04/2019
ms.locfileid: "71959900"
---
# <a name="application-insights-for-azure-functions-supported-features"></a>Application Insights Azure Functions obsługiwanych funkcji

Azure Functions oferuje [wbudowaną integrację](https://docs.microsoft.com/azure/azure-functions/functions-monitoring) z usługą Application Insights, która jest dostępna za pomocą interfejsu ILogger. Poniżej znajduje się lista obecnie obsługiwanych funkcji. Zapoznaj się z tematem "Przewodnik po [rozpoczęciu](https://github.com/Azure/Azure-Functions/wiki/App-Insights)Azure Functions".

## <a name="supported-features"></a>Obsługiwane funkcje

| Azure Functions                       | Wersjach                | V2 (zapłon 2018)  | 
|-----------------------------------    |---------------    |------------------ |
| **Zestaw SDK Application Insights .NET**   | **2.5.0**       | **2.9.1**         |
| | | | 
| **Automatyczne zbieranie**        |                 |                   |               
| Żądania &bull;                     | Tak             | Tak               | 
| Wyjątki &bull;                   | Tak             | Tak               | 
| Liczniki wydajności &bull;         | Tak             | Tak               |
| Zależności &bull;                   |                   |                   |               
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 HTTP      |                 | Tak               | 
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 ServiceBus|                 | Tak               | 
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 EventHub  |                 | Tak               | 
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 SQL       |                 | Tak               | 
| | | | 
| **Obsługiwane funkcje**                |                   |                   |               
| &bull; QuickPulse/LiveMetrics       | Tak             | Tak               | 
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 Secure Control Channel|                 | Tak               | 
| Próbkowanie &bull;                     | Tak             | Tak               | 
| pulsy &bull;                   |                 | Tak               | 
| | | | 
| **Korelacja**                       |                   |                   |               
| &bull; ServiceBus                     |                   | Tak               | 
| &bull; EventHub                       |                   | Tak               | 
| | | | 
| **Skonfigurować**                      |                   |                   |           
| &bull;Fully — konfigurowalne.<br/>Aby uzyskać instrukcje, zobacz [Azure Functions](https://github.com/Microsoft/ApplicationInsights-aspnetcore/issues/759#issuecomment-426687852) .<br/>Zobacz [ASP.NET Core](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Custom-Configuration) , aby poznać wszystkie opcje.               |                   | Tak                   | 


## <a name="performance-counters"></a>Liczniki wydajności

Automatyczne zbieranie liczników wydajności działa tylko na maszynach z systemem Windows.


## <a name="live-metrics--secure-control-channel"></a>Metryki na żywo & kanale bezpiecznego sterowania

Określone kryteria filtrów niestandardowych są wysyłane z powrotem do składnika metryki na żywo w zestawie Application Insights SDK. Filtry mogą potencjalnie zawierać informacje poufne, takie jak customerID. Kanał można zabezpieczyć przy użyciu klucza tajnego interfejsu API. Instrukcje można znaleźć w temacie [Zabezpieczenia kanału kontroli](https://docs.microsoft.com/azure/azure-monitor/app/live-stream#secure-the-control-channel) .

## <a name="sampling"></a>Sond

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
