---
title: Monitorowanie dostarczania komunikatów usługi Azure Event Grid
description: W tym artykule opisano sposób korzystania z witryny Azure Portal, aby wyświetlić stan dostarczania komunikatów usługi Azure Event Grid.
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 16587feaca65aa21836d9be1c44e00faa0f4f8d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76722139"
---
# <a name="monitor-event-grid-message-delivery"></a>Monitorowanie dostarczania komunikatów siatki zdarzeń 

W tym artykule opisano sposób korzystania z portalu, aby zobaczyć stan dostaw zdarzeń.

Usługa Event Grid zapewnia trwałe dostarczanie. Dostarcza każdej wiadomości co najmniej raz dla każdej subskrypcji. Zdarzenia są wysyłane do zarejestrowanego elementu webhook każdej subskrypcji natychmiast. Jeśli element webhook nie potwierdzi otrzymania zdarzenia w ciągu 60 sekund od pierwszej próby dostarczenia, usługa Event Grid ponawia próbę dostarczenia zdarzenia.

Aby uzyskać informacje o dostarczaniu zdarzeń i ponownych próbach, [w ucho.](delivery-and-retry.md)

## <a name="delivery-metrics"></a>Dane dotyczące dostawy

Portal wyświetla metryki dotyczące stanu dostarczania komunikatów o zdarzeniach.

W przypadku tematów metryki to:

* **Opublikowanie powiodło się**: Zdarzenie zostało pomyślnie wysłane do tematu i przetworzone z odpowiedzią 2xx.
* **Opublikowanie nie powiodło się**: Zdarzenie wysłane do tematu, ale odrzucone z kodem błędu.
* **Niedopasowane:** Zdarzenie zostało pomyślnie opublikowane w temacie, ale nie jest dopasowane do subskrypcji zdarzenia. Zdarzenie zostało przerwane.

W przypadku subskrypcji metryki są następujące:

* **Dostawa powiodła się:** Zdarzenie zostało pomyślnie dostarczone do punktu końcowego subskrypcji i otrzymało odpowiedź 2xx.
* **Dostarczanie nie powiodło się:** Zdarzenie wysłane do punktu końcowego subskrypcji, ale otrzymało odpowiedź 4xx lub 5xx.
* **Zdarzenia wygasłe:** zdarzenie nie zostało dostarczone i wszystkie próby ponawiania zostały wysłane. Zdarzenie zostało przerwane.
* **Dopasowane zdarzenia:** Zdarzenie w temacie zostało dopasowane przez subskrypcję wydarzenia.

## <a name="event-subscription-status"></a>Stan subskrypcji zdarzeń

Aby wyświetlić metryki subskrypcji zdarzeń, możesz wyszukiwać według typu subskrypcji lub subskrypcji dla określonego zasobu.

Aby wyszukać według typu subskrypcji zdarzeń, wybierz pozycję **Wszystkie usługi**.

![Wybierz wszystkie usługi](./media/monitor-event-delivery/all-services.png)

Wyszukaj **siatkę zdarzeń** i wybierz **subskrypcje siatki zdarzeń** z dostępnych opcji.

![Wyszukiwanie subskrypcji zdarzeń](./media/monitor-event-delivery/search-and-select.png)

Filtruj według typu zdarzenia, subskrypcji i lokalizacji. Wybierz **metryki** dla subskrypcji do wyświetlenia.

![Filtrowanie subskrypcji zdarzeń](./media/monitor-event-delivery/filter-events.png)

Wyświetl metryki tematu zdarzenia i subskrypcji.

![Wyświetlanie danych zdarzeń](./media/monitor-event-delivery/subscription-metrics.png)

Aby znaleźć metryki dla określonego zasobu, wybierz ten zasób. Następnie wybierz pozycję **Zdarzenia**.

![Wybieranie zdarzeń dla zasobu](./media/monitor-event-delivery/select-events.png)

Zobaczysz metryki dla subskrypcji dla tego zasobu.

## <a name="custom-event-status"></a>Stan zdarzenia niestandardowego

Jeśli opublikowano temat niestandardowy, możesz wyświetlić jego metryki. Wybierz grupę zasobów dla tematu i wybierz temat.

![Wybieranie tematu niestandardowego](./media/monitor-event-delivery/select-custom-topic.png)

Wyświetl metryki tematu zdarzenia niestandardowego.

![Wyświetlanie danych zdarzeń](./media/monitor-event-delivery/custom-topic-metrics.png)

## <a name="set-alerts"></a>Ustawianie alertów

Alerty dotyczące tematu i metryki poziomu domeny można ustawić dla tematów niestandardowych i domen zdarzeń. W bloku przeglądu dla, wybierz **alerty** z lewej miał menu zasobów w celu wyświetlania, zarządzania i tworzenia reguł alertów. [Dowiedz się więcej o alertach monitora platformy Azure](../azure-monitor/platform/alerts-overview.md)

![Wyświetlanie danych zdarzeń](./media/monitor-event-delivery/select-alerts.png)

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać informacje o dostarczaniu zdarzeń i ponownych próbach, [w ucho.](delivery-and-retry.md)
* Aby zapoznać się z wprowadzeniem do usługi Event Grid, zobacz [Wprowadzenie do usługi Azure Event Grid](overview.md).
* Aby szybko rozpocząć korzystanie z usługi Event Grid, zobacz [Tworzenie i rozsyłanie zdarzeń niestandardowych za pomocą usługi Azure Event Grid](custom-event-quickstart.md).
