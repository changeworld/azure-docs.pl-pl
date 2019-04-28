---
title: Model danych Telemetrii aplikacji platformy Azure szczegółowe informacje — Telemetrii metryk | Dokumentacja firmy Microsoft
description: Model danych szczegółowych informacji aplikacji dla telemetrii metryk
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.author: mbullwin
ms.openlocfilehash: 0973c86d055ff4ebbe7e5a3c4a2ca4e3dcabc6a0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60900464"
---
# <a name="metric-telemetry-application-insights-data-model"></a>Telemetria metryk: Model danych usługi Application Insights

Istnieją dwa typy telemetrii metryk obsługiwane przez [usługi Application Insights](../../azure-monitor/app/app-insights-overview.md): pojedyncze pomiaru i wstępnie zagregowane metryki. Jednej miary jest po prostu nazwą i wartością. Wstępnie zagregowane metryki określa minimalną i maksymalną wartość metryki w interwał agregacji i odchylenie standardowe go.

Wstępnie zagregowane metryki danych telemetrycznych zakłada ten okres agregacji został jedną minutę.

Istnieje kilka znanych nazw metryki obsługiwane przez usługę Application Insights. Te metryki są umieszczane w tabeli liczniki wydajności.

Metryka reprezentujące system i proces liczników:

| **Nazwa platformy .NET**             | **Nazwa niezależny od platformy** | **Nazwa interfejsu API REST** | **Opis**
| ------------------------- | -------------------------- | ----------------- | ---------------- 
| `\Processor(_Total)\% Processor Time` | W toku... | [processorCpuPercentage](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessorCpuPercentage) | Łączna liczba procesorów maszyny
| `\Memory\Available Bytes`                 | W toku... | [memoryAvailableBytes](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FmemoryAvailableBytes) | Przedstawia ilość pamięci fizycznej w bajtach, dostępną dla procesów uruchomionych na komputerze. Jest ona obliczana przez zsumowanie ilość miejsca na listach wyzerowana, bezpłatnemu i rezerwy pamięci. Wolna pamięć jest gotowa do użycia; pamięć wyzerowana składa się ze stron pamięci wypełniane zerami w celu uniemożliwienia procesy nowsze dane używane przez poprzedniego procesu; rezerwy pamięci jest pamięcią, która została usunięta z zestawu roboczego procesu (jego pamięci fizycznej) trasie na dysku, ale jest nadal dostępna, należy przypomnieć. Zobacz [obiektów pamięci](https://msdn.microsoft.com/library/ms804008.aspx)
| `\Process(??APP_WIN32_PROC??)\% Processor Time` | W toku... | [processCpuPercentage](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessCpuPercentage) | Procesor CPU procesu hostingu aplikacji
| `\Process(??APP_WIN32_PROC??)\Private Bytes`      | W toku... | [processPrivateBytes](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessPrivateBytes) | Użycie pamięci przez proces, który hostuje aplikację
| `\Process(??APP_WIN32_PROC??)\IO Data Bytes/sec` | W toku... | [processIOBytesPerSecond](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessIOBytesPerSecond) | Liczba operacji We/Wy jest uruchamiane przez proces hostingu aplikacji
| `\ASP.NET Applications(??APP_W3SVC_PROC??)\Requests/Sec`             | W toku... | [requestsPerSecond](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FrequestsPerSecond) | Liczba żądań przetworzonych przez aplikację 
| `\.NET CLR Exceptions(??APP_CLR_PROC??)\# of Exceps Thrown / sec`    | W toku... | [exceptionsPerSecond](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FexceptionsPerSecond) | Liczba wyjątków zgłaszanych przez aplikację
| `\ASP.NET Applications(??APP_W3SVC_PROC??)\Request Execution Time`   | W toku... | [requestExecutionTime](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FrequestExecutionTime) | Średnia liczba żądań, czas wykonywania
| `\ASP.NET Applications(??APP_W3SVC_PROC??)\Requests In Application Queue` | W toku... | [requestsInQueue](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FrequestsInQueue) | Liczba żądań oczekujących do przetworzenia w kolejce

## <a name="name"></a>Name (Nazwa)

Nazwa metryki, które mają być wyświetlane w portalu Application Insights i interfejs użytkownika. 

## <a name="value"></a>Wartość

Pojedynczą wartość dla miary. Suma poszczególnymi pomiarami podczas agregacji.

## <a name="count"></a>Licznik

Waga metryki zagregowane metryki. Nie powinna być ustawiona dla miary.

## <a name="min"></a>Min.

Minimalna wartość metryki zagregowane. Nie powinna być ustawiona dla miary.

## <a name="max"></a>Maks.

Maksymalna wartość metryki zagregowane. Nie powinna być ustawiona dla miary.

## <a name="standard-deviation"></a>Odchylenie standardowe

Odchylenie standardowe zagregowane metryki. Nie powinna być ustawiona dla miary.

## <a name="custom-properties"></a>Właściwości niestandardowe

Metryka z niestandardowymi właściwościami `CustomPerfCounter` równa `true` wskazują, że metryka reprezentuje licznik wydajności systemu windows. Te metryki są umieszczane w tabeli liczniki wydajności. Nie znajduje się w customMetrics. Również nazwa ta metryka jest analizowany wyodrębnić kategorii, licznika i nazwy wystąpień.

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się, jak używać [interfejsu API usługi Application Insights dla niestandardowych zdarzeń i metryk](../../azure-monitor/app/api-custom-events-metrics.md#trackmetric).
- Zobacz [modelu danych](data-model.md) dla usługi Application Insights typów i danych modelu.
- Zapoznaj się z [platform](../../azure-monitor/app/platforms.md) obsługiwane przez usługę Application Insights.
