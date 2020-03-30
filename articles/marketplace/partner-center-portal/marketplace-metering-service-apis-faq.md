---
title: Interfejsy API usługi pomiarowej marketplace - często zadawane pytania | Azure Marketplace
description: Emituj użycie oferty SaaS w portalu Azure Marketplace.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/11/2019
ms.openlocfilehash: 6e5b691a41ef283449f9eeeb90e9d01a91616146
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275785"
---
# <a name="marketplace-metering-service-apis---faq"></a>Interfejsy API usługi pomiaru w witrynie Marketplace — często zadawane pytania

Gdy użytkownik platformy Azure subskrybuje usługę SaaS, która zawiera rozliczanie taryfowe, będzie śledzić zużycie dla każdego wymiaru rozliczeń używanego przez klienta. Jeśli zużycie przekracza uwzględnione ilości ustawione dla terminu wybranego przez klienta, usługa będzie emitować zdarzenia użycia do firmy Microsoft.

## <a name="emit-usage-events"></a>Emitowanie zdarzeń użycia

>[!Note]
>Ta sekcja ma zastosowanie tylko do ofert SaaS, gdzie co najmniej jeden z planów ma wymiary usługi pomiarowej zdefiniowane w momencie publikacji oferty.

![Emitowanie zdarzeń użycia](media/isv-emits-usage-event.png)

Zobacz [interfejs API zdarzenia użycia partii SaaS,](./marketplace-metering-service-apis.md#batch-usage-event) aby uzyskać informacje na temat umowy interfejsu API do emitowania zdarzeń użycia.

### <a name="how-often-is-it-expected-to-emit-usage"></a>Jak często oczekuje się, że będzie emitować użycie?

W idealnym przypadku oczekuje się, że użycie co godzinę dla ostatniej godziny, tylko wtedy, gdy istnieje użycie w poprzedniej godzinie.

### <a name="what-is-the-maximum-delay-between-the-time-an-event-occurs-and-the-time-a-usage-event-is-emitted-to-microsoft"></a>Jakie jest maksymalne opóźnienie między czasem wystąpienia zdarzenia a czasem emisji zdarzenia użycia do firmy Microsoft?

W idealnym przypadku zdarzenie użycia jest emitowane co godzinę dla zdarzeń, które wystąpiły w ciągu ostatniej godziny. Spodziewane są jednak opóźnienia. Maksymalne dopuszczalne opóźnienie wynosi 24 godziny, po czym zdarzenia użytkowania nie będą akceptowane.

Na przykład jeśli zdarzenie użycia występuje o godzinie 13:00 w ciągu dnia, masz do 1 PM następnego dnia, aby emitować zdarzenie użycia skojarzone z tym zdarzeniem. Oznacza to, że w przypadku użycia emitującego system ma czas przestoju, można odzyskać, a następnie wysłać zdarzenie użycia dla przedziału godzinowego, w którym użycie się stało, bez utraty wierności.

### <a name="what-happens-when-you-send-more-than-one-usage-event-on-the-same-hour"></a>Co się stanie, gdy wyślesz więcej niż jedno zdarzenie użycia w tej samej godzinie?

Tylko jedno zdarzenie użycia jest akceptowane dla interwału godzinowego. Interwał godzinowy rozpoczyna się od minuty 0 i kończy się w minucie 59.  Jeśli więcej niż jedno zdarzenie użycia jest emitowane dla tego samego interwału godzin, wszystkie kolejne zdarzenia użycia są usuwane jako duplikaty.

### <a name="what-happens-when-you-emit-usage-for-a-saas-subscription-that-has-been-unsubscribed-already"></a>Co się stanie, gdy emitujesz użycie subskrypcji SaaS, która została już anulowana?

Żadne zdarzenie użycia emitowane na platformie marketplace nie zostanie zaakceptowane po usunięciu subskrypcji SaaS.

### <a name="can-you-get-a-list-of-all-saas-subscriptions-including-active-and-unsubscribed-subscriptions"></a>Czy możesz uzyskać listę wszystkich subskrypcji SaaS, w tym aktywnych i anulowanych subskrypcji?

Tak, po wywołaniu `GET /saas/subscriptions` interfejsu API zawiera listę wszystkich subskrypcji SaaS. Pole stanu w odpowiedzi dla każdej subskrypcji SaaS przechwytuje, czy subskrypcja jest aktywna, czy anulowana. Wywołanie listy subskrypcji zwraca maksymalnie 100 subskrypcji w czasie.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji, zobacz [Interfejsy API usługi pomiaru w portalu Marketplace.](./marketplace-metering-service-apis.md)
