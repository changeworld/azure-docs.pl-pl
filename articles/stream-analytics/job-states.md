---
title: Stany zadania w usłudze Azure Stream Analytics
description: W tym artykule opisano różne stany zadania usługi Stream Analytics
services: stream-analytics
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/06/2019
ms.openlocfilehash: 28e0e69d3a6a4d3a38146cbf2c49426b3b16c784
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60789456"
---
# <a name="azure-stream-analytics-job-states"></a>Stany zadania w usłudze Azure Stream Analytics

Zadanie usługi Stream Analytics może być w jednej z czterech stanów w danym momencie. Stan zadania na stronie przeglądu zadania usługi Stream Analytics można znaleźć w witrynie Azure portal. 

| Stan | Opis | Zalecane akcje |
| --- | --- | --- |
| **Uruchomiono** | Zadanie działa na platformie Azure zdarzenia pochodzące z określonych źródeł danych wejściowych, przetwarzanie ich i zapisywanie wyniki do ujścia danych wyjściowych skonfigurowany do czytania. | Jest najlepszym rozwiązaniem, aby śledzić wydajność zadania, monitorując [kluczowe metryki](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor). |
| **Zatrzymana** | Zadanie zostaje zatrzymana, a nie przetwarza zdarzeń. | Nie dotyczy | 
| **Negatywny wpływ na dostępność** | Błędy przejściowe, prawdopodobnie wpływają na zadania. Stream Analytics natychmiast podejmie próbę sprawności takie błędy i powrócić do stanu działania (w ciągu kilku minut). Te błędy można możliwe z powodu problemów z siecią, dostępności innych zasobów platformy Azure deserializacji błędów itp. Może mieć wpływ na wydajność zadania, gdy zadanie jest w stanie obniżonej wydajności.| Można przyjrzeć się [dzienniki diagnostyczne lub działanie](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs#debugging-using-activity-logs) Aby dowiedzieć się więcej na temat przyczyny tych błędów przejściowych. W przypadkach, takich jak błędy deserializacji zalecane jest podjęcie działań korygujących, aby upewnić się, że zdarzenia nie są źle sformułowane. Jeśli zadanie utrzymuje osiągnięcie limitu wykorzystania zasobów, spróbuj zwiększyć liczbę SU lub [zrównoleglić zadania](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization). W innych przypadkach, gdy użytkownik nie może podejmować działań, Stream Analytics podejmie próbę odzyskania *systemem* stanu.  |
| **Niepowodzenie** | Zadanie napotkał błąd krytyczny, co w stanie niepowodzenia. Zdarzenia nie są Odczyt i przetworzone. Błędy w czasie wykonywania są Częstą przyczyną zadań kończy w stanie niepowodzenia. | Możesz [Konfigurowanie alertów](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#set-up-alerts-in-the-azure-portal) tak, aby uzyskać powiadomienie, gdy zadanie przechodzi do stanu nie powiodło się. <br> <br>Można debugować z użyciem [aktywności i dziennikach diagnostycznych](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs#debugging-using-activity-logs) Aby zidentyfikować przyczynę i rozwiązać problem.|

## <a name="next-steps"></a>Kolejne kroki
* [Konfigurowanie alertów dotyczących zadań usługi Azure Stream Analytics](stream-analytics-set-up-alerts.md)
* [Metryk dostępnych w usłudze Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-monitoring#metrics-available-for-stream-analytics)
* [Rozwiązywanie problemów przy użyciu aktywności i dzienników diagnostycznych](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs)
