---
title: Odzyskiwanie po awarii geograficznej w usłudze Azure Event Grid | Dokumenty firmy Microsoft
description: W tym artykule opisano, jak usługa Azure Event Grid automatycznie obsługuje odzyskiwanie po awarii geograficznej (GeoDR).
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/24/2019
ms.author: spelluru
ms.openlocfilehash: 5b5c973a8daa8776efb0909092c569ea46902265
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "66307320"
---
# <a name="server-side-geo-disaster-recovery-in-azure-event-grid"></a>Odzyskiwanie po awarii geograficznej po stronie serwera w usłudze Azure Event Grid
Usługa Event Grid ma teraz automatyczne odzyskiwanie po awarii geograficznej (GeoDR) metadanych nie tylko dla nowych, ale wszystkich istniejących domen, tematów i subskrypcji zdarzeń. Jeśli cały region platformy Azure ujmuje się, usługa Event Grid będzie już synchronizowana wszystkie metadane infrastruktury związane ze zdarzeniami z sparowanym regionem. Twoje nowe wydarzenia zaczną płynąć ponownie bez interwencji ze strony Ciebie. 

Odzyskiwanie po awarii jest mierzone za pomocą dwóch metryk:

- [Cel punktu odzyskiwania (RPO)](https://en.wikipedia.org/wiki/Disaster_recovery#Recovery_Point_Objective): minuty lub godziny danych, które mogą zostać utracone.
- [Cel czasu odzyskiwania (RTO)](https://en.wikipedia.org/wiki/Disaster_recovery#Recovery_time_objective): minuty godzin, w których usługa może być wyłączna.

Automatyczna funkcja trybu failover usługi Event Grid ma różne obiekty RPO i RTO dla metadanych (subskrypcje zdarzeń itp.) i danych (zdarzenia). Jeśli potrzebujesz innej specyfikacji niż następujące, nadal możesz zaimplementować własną [stronę klienta w trybie fail over przy użyciu apis kondycji tematu](custom-disaster-recovery.md).

## <a name="recovery-point-objective-rpo"></a>Cel punktu odzyskiwania (recovery point objective, RPO)
- **RPO metadanych:** zero minut. Za każdym razem, gdy zasób jest tworzony w uszkcie zdarzeń, jest natychmiast replikowany w różnych regionach. W przypadku pracy awaryjnej nie są tracone metadane.
- **RPO danych**: Jeśli system jest zdrowy i nadrabia problem z istniejącym ruchem w czasie regionalnego trybu failover, obiekt RPO dla zdarzeń wynosi około 5 minut.

## <a name="recovery-time-objective-rto"></a>Cel czasu odzyskiwania (recovery time objective, RTO)
- **RTO metadanych:** Chociaż ogólnie dzieje się to znacznie szybciej, w ciągu 60 minut usługa Event Grid zacznie akceptować wywołania create/update/delete dla tematów i subskrypcji.
- **RTO danych:** Podobnie jak metadane, zwykle dzieje się znacznie szybciej, jednak w ciągu 60 minut usługa Event Grid rozpocznie akceptowanie nowego ruchu po regionalnej pracy awaryjnej.

> [!NOTE]
> Koszt metadanych GeoDR w siatce zdarzeń wynosi: $0.


## <a name="next-steps"></a>Następne kroki
Jeśli chcesz zaimplementować własną logikę trybu failover po stronie klienta, zobacz [# Tworzenie własnego odzyskiwania po awarii dla tematów niestandardowych w siatce zdarzeń](custom-disaster-recovery.md)
