---
title: Monitorowanie dostarczania komunikatów usługi Azure Event Grid
description: W tym artykule opisano, jak monitorować dostarczenia komunikatów usługi Azure Event Grid.
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/22/2019
ms.author: spelluru
ms.openlocfilehash: fdd18b833794c25cb90188ba8bc418d4785492ba
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60824162"
---
# <a name="monitor-event-grid-message-delivery"></a>Monitorowanie dostarczania komunikatów FIFO usługi Event Grid 

W tym artykule opisano, jak korzystać z portalu, aby wyświetlić stan wysyłki zdarzeń.

Usługi Event Grid oferuje niezawodne dostarczanie. System ten zapewnia każdy komunikat co najmniej raz dla każdej subskrypcji. Zdarzenia są wysyłane bezpośrednio do zarejestrowanych elementu webhook w każdej subskrypcji. Jeśli element webhook nie otrzymanie zdarzenia w ciągu 60 sekund od pierwszej próby dostarczenia, usługi Event Grid ponawia próbę dostarczaniu zdarzenia.

Informacje o dostarczanie zdarzeń i ponownych prób [dostarczanie komunikatów usługi Event Grid i ponów próbę](delivery-and-retry.md).

## <a name="delivery-metrics"></a>Dostarczanie metryk

Portal zawiera metryki dotyczące stanu dostarczania komunikaty o zdarzeniach.

Tematy dostępne są następujące metryki:

* **Publikowanie zostało zakończone pomyślnie**: Pomyślnie zdarzeń wysłanych do tematu i przetwarzane przy użyciu odpowiedzi 2xx.
* **Nie można opublikować**: Zdarzenia wysyłane do tematu, ale odrzucone z kodem błędu.
* **Niedopasowane**: Zdarzenie została pomyślnie opublikowana do tematu, ale nie pasuje do subskrypcji zdarzeń. Zdarzenie zostało porzucone.

W przypadku subskrypcji dostępne są następujące metryki:

* **Dostarczenie powiodło się**: Zdarzenie pomyślnie dostarczone do endpoint dla subskrypcji i Odebrano odpowiedź 2xx.
* **Dostarczanie nie powiodło się**: Zdarzenia wysyłane do punktu końcowego subskrypcji, ale Odebrano odpowiedź 4xx lub 5xx.
* **Ważność zdarzenia**: Zdarzenie nie zostało dostarczone, i zostały wysłane przeprowadzenia wszystkich ponownych prób. Zdarzenie zostało porzucone.
* **Dopasowane zdarzenia**: Zdarzenia w tym temacie zostały dopasowane do subskrypcji zdarzeń.

## <a name="event-subscription-status"></a>Stan subskrypcji zdarzeń

Aby wyświetlić metryki subskrypcji zdarzeń, albo można wyszukiwać według typu subskrypcji lub subskrypcji dla określonego zasobu.

Aby przeprowadzić wyszukiwanie według typu subskrypcji zdarzeń, wybierz **wszystkich usług**.

![Wybierz wszystkie usługi](./media/monitor-event-delivery/all-services.png)

Wyszukaj **usługi event grid** i wybierz **subskrypcje usługi Event Grid** z dostępnych opcji.

![Wyszukaj subskrypcje zdarzeń](./media/monitor-event-delivery/search-and-select.png)

Filtruj według typu zdarzenia, subskrypcji i lokalizacji. Wybierz **metryki** dla subskrypcji wyświetlić.

![Filtruj subskrypcje zdarzeń](./media/monitor-event-delivery/filter-events.png)

Wyświetlaj metryki dla zdarzenia tematu i subskrypcji.

![Wyświetl metryki zdarzenia](./media/monitor-event-delivery/subscription-metrics.png)

Aby uzyskać metryki dla określonego zasobu, zaznacz ten zasób. Następnie wybierz **zdarzenia**.

![Wybierz zdarzenia dla zasobu](./media/monitor-event-delivery/select-events.png)

Możesz wyświetlić metryki dla subskrypcji dla tego zasobu.

## <a name="custom-event-status"></a>Stan zdarzenia niestandardowego

Po opublikowaniu tematu niestandardowego, możesz wyświetlić metryki dla niego. Wybierz grupę zasobów dla tematu, a następnie wybierz tematu.

![Wybieranie tematu niestandardowego](./media/monitor-event-delivery/select-custom-topic.png)

Wyświetlaj metryki dla tematu niestandardowego zdarzenia.

![Wyświetl metryki zdarzenia](./media/monitor-event-delivery/custom-topic-metrics.png)

## <a name="next-steps"></a>Kolejne kroki

* Informacje o dostarczanie zdarzeń i ponownych prób [dostarczanie komunikatów usługi Event Grid i ponów próbę](delivery-and-retry.md).
* Aby zapoznać się z wprowadzeniem do usługi Event Grid, zobacz [Wprowadzenie do usługi Azure Event Grid](overview.md).
* Aby szybko rozpocząć pracę, przy użyciu usługi Event Grid, zobacz [Utwórz i wyznaczać trasy zdarzeń niestandardowych za pomocą usługi Azure Event Grid](custom-event-quickstart.md).
