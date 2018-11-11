---
title: Subskrypcje usługi Event Grid platformy Azure za pośrednictwem portalu
description: W tym artykule opisano sposób tworzenia subskrypcji usługi Event Grid za pośrednictwem portalu.
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: conceptual
ms.date: 11/08/2018
ms.author: tomfitz
ms.openlocfilehash: eb48e40007a25992a9a399176b6a4f93be89efc8
ms.sourcegitcommit: 96527c150e33a1d630836e72561a5f7d529521b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/09/2018
ms.locfileid: "51344092"
---
# <a name="subscribe-to-events-through-portal"></a>Subskrybowanie do zdarzeń za pośrednictwem portalu

W tym artykule opisano sposób tworzenia subskrypcji usługi Event Grid za pośrednictwem portalu.

## <a name="create-event-subscriptions"></a>Tworzenie subskrypcji zdarzeń

Aby utworzyć subskrypcję usługi Event Grid dla każdej z obsługiwanych [źródła zdarzeń](event-sources.md), wykonaj następujące kroki. W tym artykule przedstawiono sposób tworzenia subskrypcji usługi Event Grid dla subskrypcji platformy Azure.

1. Wybierz pozycję **Wszystkie usługi**.

   ![Wybierz wszystkie usługi](./media/subscribe-through-portal/select-all-services.png)

1. Wyszukaj **subskrypcje usługi Event Grid** i wybierz ją z listy dostępnych opcji.

   ![Wyszukiwanie](./media/subscribe-through-portal/search.png)

1. Wybierz pozycję **+ Subskrypcja zdarzeń**.

   ![Dodawanie subskrypcji](./media/subscribe-through-portal/add-subscription.png)

1. Wybierz typ subskrypcji, którą chcesz utworzyć. Na przykład aby subskrybować zdarzenia dla Twojej subskrypcji platformy Azure, wybierz pozycję **subskrypcje platformy Azure** i subskrypcji docelowej.

   ![Wybierz subskrypcję platformy Azure](./media/subscribe-through-portal/azure-subscription.png)

1. Subskrybuj wszystkie typy zdarzeń dla tego źródła zdarzeń, pamiętaj o **Subskrybuj wszystkie typy zdarzeń** zaznaczoną opcją. W przeciwnym razie wybierz typy zdarzeń dla tej subskrypcji.

   ![Wybierz typy zdarzeń](./media/subscribe-through-portal/select-event-types.png)

1. Podaj dodatkowe szczegóły dotyczące subskrypcji zdarzeń, takich jak punkt końcowy do obsługi zdarzeń i nazwy subskrypcji.

   ![Podaj szczegóły subskrypcji](./media/subscribe-through-portal/provide-subscription-details.png)

1. Włącz obsługę utraconych komunikatów i Dostosuj zasady ponawiania, wybierz **dodatkowe funkcje**.

   ![Wybierz dodatkowe funkcje](./media/subscribe-through-portal/select-additional-features.png)

1. Wybierz kontener do przechowywania zdarzeń, które nie są dostarczane, i określenie, jak są wysyłane ponownych prób.

   ![Włącz utraconych, a następnie spróbuj ponownie](./media/subscribe-through-portal/set-deadletter-retry.png)

1. Po zakończeniu wybierz pozycję **Utwórz**.

## <a name="create-subscription-on-resource"></a>Utwórz subskrypcję dla zasobu

Niektóre źródła zdarzeń pomocy technicznej, Utwórz subskrypcję zdarzeń za pośrednictwem portalu interfejsu dla tego zasobu. Wybierz źródła zdarzeń i poszukaj **zdarzenia** w okienku po lewej stronie.

![Podaj szczegóły subskrypcji](./media/subscribe-through-portal/resource-events.png)

W portalu jest wyświetlany za pomocą opcji tworzenia subskrypcji zdarzeń, która jest odpowiednia dla tego źródła.

## <a name="next-steps"></a>Kolejne kroki

* Informacje o dostarczanie zdarzeń i ponownych prób [dostarczanie komunikatów usługi Event Grid i ponów próbę](delivery-and-retry.md).
* Aby zapoznać się z wprowadzeniem do usługi Event Grid, zobacz [Wprowadzenie do usługi Azure Event Grid](overview.md).
* Aby szybko rozpocząć pracę, przy użyciu usługi Event Grid, zobacz [Utwórz i wyznaczać trasy zdarzeń niestandardowych za pomocą usługi Azure Event Grid](custom-event-quickstart.md).
