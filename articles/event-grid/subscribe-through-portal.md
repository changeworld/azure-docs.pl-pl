---
title: Subskrypcje Azure Event Grid za poorednictwem portalu
description: W tym artykule opisano sposób tworzenia Event Grid subskrypcji dla obsługiwanych źródeł, takich jak Azure Blob Storage, przy użyciu Azure Portal.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 3172c92ecae094ab5d978803d2ccac7e6404a5e1
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721510"
---
# <a name="subscribe-to-events-through-portal"></a>Subskrybowanie zdarzeń za poorednictwem portalu

W tym artykule opisano sposób tworzenia subskrypcji Event Grid za pomocą portalu.

## <a name="create-event-subscriptions"></a>Tworzenie subskrypcji zdarzeń

Aby utworzyć subskrypcję Event Grid dla dowolnego z obsługiwanych [źródeł zdarzeń](event-sources.md), wykonaj następujące czynności. W tym artykule przedstawiono sposób tworzenia subskrypcji Event Grid dla subskrypcji platformy Azure.

1. Wybierz pozycję **Wszystkie usługi**.

   ![Wybierz wszystkie usługi](./media/subscribe-through-portal/select-all-services.png)

1. Wyszukaj **Event Grid subskrypcje** i wybierz ją z dostępnych opcji.

   ![Search](./media/subscribe-through-portal/search.png)

1. Wybierz pozycję **+ Subskrypcja zdarzeń**.

   ![Dodawanie subskrypcji](./media/subscribe-through-portal/add-subscription.png)

1. Wybierz typ subskrypcji, którą chcesz utworzyć. Aby na przykład subskrybować zdarzenia dla subskrypcji platformy Azure, wybierz pozycję **subskrypcje platformy Azure** i subskrypcja docelowa.

   ![Wybierz subskrypcję platformy Azure](./media/subscribe-through-portal/azure-subscription.png)

1. Aby subskrybować wszystkie typy zdarzeń dla tego źródła zdarzeń, należy zaznaczyć opcję **Zasubskrybuj wszystkie typy zdarzeń** . W przeciwnym razie wybierz typy zdarzeń dla tej subskrypcji.

   ![Wybierz typy zdarzeń](./media/subscribe-through-portal/select-event-types.png)

1. Podaj dodatkowe szczegóły dotyczące subskrypcji zdarzeń, takie jak punkt końcowy dla zdarzeń obsługi i nazwa subskrypcji.

   ![Podaj szczegóły subskrypcji](./media/subscribe-through-portal/provide-subscription-details.png)

1. Aby włączyć obsługę wiadomości utraconych i dostosować zasady ponawiania, wybierz pozycję **dodatkowe funkcje**.

   ![Wybierz dodatkowe funkcje](./media/subscribe-through-portal/select-additional-features.png)

1. Wybierz kontener, który ma być używany do przechowywania zdarzeń, które nie są dostarczane, i Ustaw sposób wysyłania ponownych prób.

   ![Włącz obsługę utraconych wiadomości i ponów próbę](./media/subscribe-through-portal/set-deadletter-retry.png)

1. Po zakończeniu wybierz pozycję **Utwórz**.

## <a name="create-subscription-on-resource"></a>Utwórz subskrypcję dla zasobu

Niektóre źródła zdarzeń obsługują tworzenie subskrypcji zdarzeń za pomocą interfejsu portalu dla tego zasobu. Wybierz źródło zdarzenia i Wyszukaj **zdarzenia** w okienku po lewej stronie.

![Podaj szczegóły subskrypcji](./media/subscribe-through-portal/resource-events.png)

W portalu przedstawiono opcje tworzenia subskrypcji zdarzeń, która jest odpowiednia dla tego źródła.

## <a name="next-steps"></a>Następne kroki

* Informacje o dostarczanie zdarzeń i ponownych prób [dostarczanie komunikatów usługi Event Grid i ponów próbę](delivery-and-retry.md).
* Aby zapoznać się z wprowadzeniem do usługi Event Grid, zobacz [Wprowadzenie do usługi Azure Event Grid](overview.md).
* Aby szybko rozpocząć pracę, przy użyciu usługi Event Grid, zobacz [Utwórz i wyznaczać trasy zdarzeń niestandardowych za pomocą usługi Azure Event Grid](custom-event-quickstart.md).
