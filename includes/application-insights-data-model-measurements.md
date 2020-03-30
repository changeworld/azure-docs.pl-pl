---
author: mrbullwinkle
ms.service: application-insights
ms.topic: include
ms.date: 11/09/2018
ms.author: mbullwin
ms.openlocfilehash: 91141e314bf276d9138157c8a9d85d5262ac5907
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67182989"
---
Zbieranie niestandardowych pomiarów. Ta kolekcja służy do raportowania o nazwie pomiaru skojarzonego z elementem telemetrii. Typowe przypadki użycia to:
- rozmiar ładunku telemetrii zależności
- liczba elementów kolejki przetworzonych przez telemetrię żądania
- czas, który klient wziął, aby zakończyć krok w zakończeniu kroku kreatora Telemetria zdarzeń.

W usłudze Application Analytics można wyszukiwać [niestandardowe pomiary:](https://analytics.applicationinsights.io/demo?q=H4sIAAAAAAAAA2WLOw6DMAyGd07hZoLeoRPqyMaGGAL8aiPhGCV2kKoeHsHK%2Bj1myyr8LoiaqfrT%2FkUCzRft4LMl8OUeL3LuLLIx%2BxR%2BIF8%2BtcoiNq2o78vgWuFthQaJ1AeGGxt6UlBwKxa1qQ6EpLhAfQAAAA%3D%3D&timespan=PT24H)

```
customEvents
| where customMeasurements != ""
| summarize avg(todouble(customMeasurements["Completion Time"]) * itemCount)
```

 > [!NOTE]
 > Pomiary niestandardowe są skojarzone z elementem telemetrii, do którego należą. Są one przedmiotem próbkowania z elementem telemetrii zawierającym te pomiary. Aby śledzić pomiar, który ma wartość niezależną od innych typów danych telemetrycznych, należy użyć [danych telemetrycznych metryk](../articles/azure-monitor/app/api-custom-events-metrics.md).

Maksymalna długość klucza: 150
