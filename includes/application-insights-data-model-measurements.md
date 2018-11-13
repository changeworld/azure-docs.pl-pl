---
author: mrbullwinkle
ms.service: application-insights
ms.topic: include
ms.date: 11/09/2018
ms.author: mbullwin
ms.openlocfilehash: 3986b77cfad167134bf8ada1e3cbb73ad64dd3ca
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2018
ms.locfileid: "51572501"
---
Kolekcja niestandardowych miar. Użyj tej kolekcji do raportu o nazwie miary skojarzone z elementem telemetrii. Typowe przypadki użycia to:
- Rozmiar ładunku Telemetrii zależności
- Liczba elementów w kolejce przetwarzane przez żądanie Telemetrii
- czas klienta trwało ukończeniu kroku kończenia pracy Kreatora kroku danych Telemetrycznych zdarzeń.

Można tworzyć zapytania [pomiary niestandardowe](https://analytics.applicationinsights.io/demo?q=H4sIAAAAAAAAA2WLOw6DMAyGd07hZoLeoRPqyMaGGAL8aiPhGCV2kKoeHsHK%2Bj1myyr8LoiaqfrT%2FkUCzRft4LMl8OUeL3LuLLIx%2BxR%2BIF8%2BtcoiNq2o78vgWuFthQaJ1AeGGxt6UlBwKxa1qQ6EpLhAfQAAAA%3D%3D&timespan=PT24H) w usłudze Application Analytics:

```
customEvents
| where customMeasurements != ""
| summarize avg(todouble(customMeasurements["Completion Time"]) * itemCount)
```

 > [!NOTE]
 > Pomiary niestandardowe są skojarzone z elementem telemetrii, które należą do. Podlegają próbkowania przy użyciu elementu telemetrii zawierający pomiarów. Aby śledzić miary, który ma wartość niezależnie od innych typów danych telemetrycznych, należy użyć [telemetrii metryk](../articles/application-insights/app-insights-api-custom-events-metrics.md).

Maksymalna długość: 150
