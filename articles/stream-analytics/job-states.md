---
title: Stany zadań usługi Azure Stream Analytics
description: W tym artykule opisano cztery różne stany zadania usługi Stream Analytics; działa, zatrzymywany, degradowany i nie powiodło się.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: 454474333cac94dc25deae8196e9ba45bcb3a574
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75359770"
---
# <a name="azure-stream-analytics-job-states"></a>Stany zadań usługi Azure Stream Analytics

Zadanie usługi Stream Analytics może znajdować się w jednym z czterech stanów w danym momencie: uruchomionym, zatrzymanym, zdegradowanym lub nie powiodło się. Stan zadania można znaleźć na stronie Przegląd zadania usługi Stream Analytics w witrynie Azure portal. 

| Stan | Opis | Zalecane działania |
| --- | --- | --- |
| **Uruchomiono** | Zadanie jest uruchomione na platformie Azure odczytu zdarzeń pochodzących ze zdefiniowanych źródeł wejściowych, przetwarzania ich i zapisywania wyników do skonfigurowanych pochłaniaczy danych wyjściowych. | Jest to najlepsza praktyka, aby śledzić wydajność zadania poprzez monitorowanie [kluczowych wskaźników](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor). |
| **Zatrzymano** | Zadanie jest zatrzymane i nie przetwarza zdarzeń. | Nie dotyczy | 
| **Obniżona wydajność** | Mogą wystąpić sporadyczne problemy z połączeniami wejściowymi i wyjściowymi. Błędy te są nazywane błędami przejściowymi, które mogą sprawić, że zadanie stanie się stanem zdegradowanym. Usługa Stream Analytics natychmiast spróbuje odzyskać z takich błędów i powrócić do stanu uruchamiania (w ciągu kilku minut). Te błędy mogą się zdarzyć z powodu problemów z siecią, dostępności innych zasobów platformy Azure, błędów deserializacji itp. Wydajność zadania może mieć wpływ, gdy zadanie jest w stanie obniżonym.| Można spojrzeć na [dzienniki diagnostyczne lub aktywności,](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs#debugging-using-activity-logs) aby dowiedzieć się więcej o przyczynach tych błędów przejściowych. W przypadkach takich jak błędy deserializacji zaleca się podjęcie działań naprawczych, aby upewnić się, że zdarzenia nie są zniekształcone. Jeśli zadanie nadal osiąga limit wykorzystania zasobów, spróbuj zwiększyć numer SU lub [zrównoleglić zadanie](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization). W innych przypadkach, gdy nie można podjąć żadnych działań, Usługa Stream Analytics spróbuje odzyskać do stanu *uruchomionego.* <br> Można użyć [metryki opóźnienia znaku wodnego,](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor) aby zrozumieć, czy te błędy przejściowe mają wpływ na wydajność zadania.|
| **Niepowodzenie** | Zadanie napotkało błąd krytyczny powodujący niepowodzenie. Zdarzenia nie są odczytywane i przetwarzane. Błędy środowiska uruchomieniowego są częstą przyczyną dla zadań kończących się w stanie awarii. | Alerty można [skonfigurować](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#set-up-alerts-in-the-azure-portal) w taki sposób, aby otrzymywać powiadomienia, gdy zadanie przechodzi do stanu Niepowodzenie. <br> <br>Można debugować przy użyciu [dzienników aktywności i diagnostyki,](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs#debugging-using-activity-logs) aby zidentyfikować główną przyczynę i rozwiązać problem.|

## <a name="next-steps"></a>Następne kroki
* [Alerty dotyczące konfigurowania zadań usługi Azure Stream Analytics](stream-analytics-set-up-alerts.md)
* [Dane dostępne w usłudze Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-monitoring#metrics-available-for-stream-analytics)
* [Rozwiązywanie problemów z używaniem dzienników aktywności i diagnostyki](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs)
