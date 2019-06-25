---
author: mrbullwinkle
ms.service: application-insights
ms.topic: include
ms.date: 11/09/2018
ms.author: mbullwin
ms.openlocfilehash: 91141e314bf276d9138157c8a9d85d5262ac5907
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67182989"
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
 > Pomiary niestandardowe są skojarzone z elementem telemetrii, które należą do. Podlegają próbkowania przy użyciu elementu telemetrii zawierający pomiarów. Aby śledzić miary, który ma wartość niezależnie od innych typów danych telemetrycznych, należy użyć [telemetrii metryk](../articles/azure-monitor/app/api-custom-events-metrics.md).

Maksymalna długość klucza: 150
