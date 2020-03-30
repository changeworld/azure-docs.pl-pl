---
title: Model danych dla danych telemetrycznych — usługa Azure Application Insights
description: Model danych usługi Application Insights dla danych metrycznych metryk
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.openlocfilehash: 3e4a1fc3de58b8e65ab9c7a288bdf3eb37e7bae0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671974"
---
# <a name="metric-telemetry-application-insights-data-model"></a>Dane telemetryczne metryki: model danych usługi Application Insights

Istnieją dwa typy danych telemetrycznych metryk obsługiwanych przez [aplikację Application Insights:](../../azure-monitor/app/app-insights-overview.md)pojedynczy pomiar i wstępnie zagregowane metryki. Pojedynczy pomiar to tylko nazwa i wartość. Wstępnie zagregowana metryka określa minimalną i maksymalną wartość metryki w interwale agregacji i jej odchyleniu standardowym.

Wstępnie zagregowane dane telemetryczne metryki zakładają, że okres agregacji był jedną minutę.

Istnieje kilka znanych nazw metryk obsługiwanych przez usługa Application Insights. Te metryki umieszczone w tabeli performanceCounters.

Metryka reprezentująca liczniki systemowe i procesowe:

| **Nazwa .NET**             | **Nazwa niezależna od platformy** | **Nazwa interfejsu API REST** | **Opis**
| ------------------------- | -------------------------- | ----------------- | ---------------- 
| `\Processor(_Total)\% Processor Time` | Prace w toku... | [procesorCpuPercentage](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessorCpuPercentage) | całkowita wartość procesora maszyny
| `\Memory\Available Bytes`                 | Prace w toku... | [pamięćDostępne Bajty](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FmemoryAvailableBytes) | Pokazuje ilość pamięci fizycznej w bajtach, dostępnej dla procesów uruchomionych na komputerze. Jest obliczana przez zsumowanie ilości miejsca na listach pamięci wyzerowanej, wolnej i rezerwowej. Wolna pamięć jest gotowa do użycia; wyzerowana pamięć składa się ze stron pamięci wypełnionych zerami, aby zapobiec wyświetlaniu danych używanych przez poprzedni proces przez późniejsze procesy; Pamięć rezerwowa to pamięć, która została usunięta z zestawu roboczego procesu (jego pamięci fizycznej) w drodze na dysk, ale jest nadal dostępna do odwołania. Zobacz [obiekt pamięci](https://msdn.microsoft.com/library/ms804008.aspx)
| `\Process(??APP_WIN32_PROC??)\% Processor Time` | Prace w toku... | [processCpuPercentage](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessCpuPercentage) | Procesor procesu hostowania aplikacji
| `\Process(??APP_WIN32_PROC??)\Private Bytes`      | Prace w toku... | [procesPrivateBytes](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessPrivateBytes) | pamięci używanej przez proces hosting aplikacji
| `\Process(??APP_WIN32_PROC??)\IO Data Bytes/sec` | Prace w toku... | [processIOBytesPerSekund](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessIOBytesPerSecond) | szybkość operacji we/wy uruchamianych przez proces hostowania aplikacji
| `\ASP.NET Applications(??APP_W3SVC_PROC??)\Requests/Sec`             | Prace w toku... | [requestsPerSecond](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FrequestsPerSecond) | liczba wniosków rozpatrywanych przez wniosek 
| `\.NET CLR Exceptions(??APP_CLR_PROC??)\# of Exceps Thrown / sec`    | Prace w toku... | [exceptionsPerSecond](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FexceptionsPerSecond) | wskaźnik wyjątków zgłaszanych przez aplikację
| `\ASP.NET Applications(??APP_W3SVC_PROC??)\Request Execution Time`   | Prace w toku... | [wniosekExecutionTime](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FrequestExecutionTime) | średni czas realizacji żądań
| `\ASP.NET Applications(??APP_W3SVC_PROC??)\Requests In Application Queue` | Prace w toku... | [requestsInQueue (Niekieylanie)](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FrequestsInQueue) | liczba żądań oczekujących na przetworzenie w kolejce

## <a name="name"></a>Nazwa

Nazwa metryki, którą chcesz wyświetlić w portalu usługi Application Insights i interfejsie użytkownika. 

## <a name="value"></a>Wartość

Pojedyncza wartość pomiaru. Suma indywidualnych pomiarów dla agregacji.

## <a name="count"></a>Liczba

Waga metryki zagregowanych metryk. Nie należy ustawiać dla pomiaru.

## <a name="min"></a>Min.

Minimalna wartość zagregowanych metryk. Nie należy ustawiać dla pomiaru.

## <a name="max"></a>Maks.

Maksymalna wartość zagregowanych metryk. Nie należy ustawiać dla pomiaru.

## <a name="standard-deviation"></a>Odchylenie standardowe

Odchylenie standardowe zagregowanego metryki. Nie należy ustawiać dla pomiaru.

## <a name="custom-properties"></a>Właściwości niestandardowe

Metryka z `CustomPerfCounter` właściwością `true` niestandardową ustawioną w celu wskazania, że metryka reprezentuje licznik wydajności systemu Windows. Te metryki umieszczone w tabeli performanceCounters. Nie w customMetrics. Również nazwa tej metryki jest analizowany wyodrębnić nazwy kategorii, licznika i wystąpień.

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak używać [interfejsu API usługi Application Insights dla zdarzeń niestandardowych i metryk](../../azure-monitor/app/api-custom-events-metrics.md#trackmetric).
- Zobacz [model danych](data-model.md) dla typów usługi Application Insights i modelu danych.
- Zapoznaj się z [platformami](../../azure-monitor/app/platforms.md) obsługiwanymi przez usługa Application Insights.
