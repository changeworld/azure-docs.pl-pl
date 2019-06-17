---
title: Geograficzne odzyskiwanie po awarii w usłudze Azure Event Grid | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak usługi Azure Event Grid obsługuje odzyskiwania po awarii replikacji geograficznej (GeoDR) automatycznie.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/24/2019
ms.author: spelluru
ms.openlocfilehash: 5b5c973a8daa8776efb0909092c569ea46902265
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66307320"
---
# <a name="server-side-geo-disaster-recovery-in-azure-event-grid"></a>Po stronie serwera geograficznego odzyskiwania po awarii w usłudze Azure Event Grid
Usługa Event Grid ma teraz automatyczne geograficznego odzyskiwania po awarii (GeoDR) metadane nie tylko dla nowych, ale wszystkie istniejące domeny, tematy i subskrypcje zdarzeń. W przypadku awarii całego regionu platformy Azure, usługi Event Grid już mieć wszystkie metadane związane ze zdarzeniami infrastruktury synchronizowane z usługą sparowanym regionie. Twoje nowe zdarzenia rozpocznie się przepływ ponownie bez potrzeby interwencji użytkownika. 

Odzyskiwanie po awarii jest mierzona z dwie metryki:

- [Cel punktu odzyskiwania (RPO)](https://en.wikipedia.org/wiki/Disaster_recovery#Recovery_Point_Objective): minuty lub godziny, danych, które mogą zostać utracone.
- [Cel czasu odzyskiwania (RTO)](https://en.wikipedia.org/wiki/Disaster_recovery#Recovery_time_objective): minut, godzin, usługa może być w dół.

Automatycznej pracy awaryjnej usługi Event Grid ma inną wartościom celu punktu odzyskiwania i docelowego czasu odzyskiwania dla metadanych programu (subskrypcje zdarzeń itd.) oraz dane (zdarzenia). Jeśli potrzebujesz innej specyfikacji z poniższych, można nadal implementować własne [po stronie klienta w trybie Failover przy użyciu tematu kondycji interfejsów API](custom-disaster-recovery.md).

## <a name="recovery-point-objective-rpo"></a>Cel punktu odzyskiwania (recovery point objective, RPO)
- **Cel punktu odzyskiwania w metadanych**: zero minut. W dowolnym momencie zasób jest tworzony w usłudze Event Grid, natychmiast są replikowane między regionami. W przypadku przejścia w tryb failover metadanych nie zostaną utracone.
- **Cel punktu odzyskiwania danych**: Jeśli system jest w dobrej kondycji i w czasie wystąpienia regionalnego trybu failover będzie podążać za istniejącym ruchem, cel RPO dla zdarzeń wynosi około 5 minut.

## <a name="recovery-time-objective-rto"></a>Cel czasu odzyskiwania (recovery time objective, RTO)
- **Cel czasu odzyskiwania metadanych**: Chociaż zwykle dużo szybciej i nastąpi to w ciągu 60 minut, usługa Event Grid rozpocznie się do akceptowania połączeń Tworzenie/aktualizowanie/usuwanie tematów i subskrypcji.
- **Data RTO**: Metadane, np. Zazwyczaj zdarza się znacznie szybciej, jednak w ciągu 60 minut, usługa Event Grid rozpocznie akceptowanie natężenia ruchu po regionalnym przejściu w tryb failover.

> [!NOTE]
> Koszt metadanych GeoDR na usługi Event Grid to: $0.


## <a name="next-steps"></a>Kolejne kroki
Jeśli chcesz zaimplementować możesz logiki własnych trybu failover po stronie klienta, zobacz [# tworzyć własne odzyskiwanie po awarii dla tematy niestandardowe w usłudze Event Grid](custom-disaster-recovery.md)
