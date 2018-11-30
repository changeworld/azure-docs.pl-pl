---
title: Subskrypcje w usłudze Azure API Management | Dokumentacja firmy Microsoft
description: Dowiedz się więcej na temat koncepcji subskrypcji w usłudze Azure API Management.
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2018
ms.author: apimpm
ms.openlocfilehash: 45a65c7a94f3e6917b2882f27c9ad7d764ef97d7
ms.sourcegitcommit: eba6841a8b8c3cb78c94afe703d4f83bf0dcab13
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/29/2018
ms.locfileid: "52621833"
---
# <a name="subscriptions-in-azure-api-management"></a>Subskrypcje w usłudze Azure API Management

Subskrypcje jest bardzo ważnym pojęciem w usłudze Azure API Management (APIM). To najbardziej popularny sposób interfejsu API w konsumentach napisanych w celu uzyskania dostępu do interfejsów API opublikowane za pośrednictwem wystąpienia usługi APIM. Ten artykuł zawiera omówienie koncepcji.

## <a name="what-is-subscriptions"></a>Co to jest subskrypcji

Podczas publikowania interfejsów API za pomocą usługi APIM, najłatwiejszym i najbardziej powszechnym sposobem bezpiecznego dostępu do tych interfejsów API jest za pomocą kluczy w subskrypcji. Innymi słowy deweloperzy, którzy muszą korzystać z opublikowanych interfejsów API musi zawierać prawidłowy klucz subskrypcji w żądaniach HTTP podczas wykonywania wywołań do tych interfejsów API. W przeciwnym razie wywołania są natychmiast odrzucane przez bramy APIM i nie są przekazywane do usług zaplecza.

Aby uzyskać klucz subskrypcji do uzyskiwania dostępu do interfejsów API, wymagana jest subskrypcja. Subskrypcja jest o nazwie kontener dla pary kluczy subskrypcji. Subskrypcje można uzyskać przez deweloperów, którzy muszą korzystać z opublikowanych interfejsów API, z lub bez zgody wydawcy interfejsów API. Wydawcy interfejsów API można również tworzyć subskrypcje bezpośrednio w imieniu odbiorcy interfejsu API.

> [!TIP]
> APIM obsługuje również inne mechanizmy do zabezpieczania dostępu do interfejsów API, w tym [OAuth2.0](api-management-howto-protect-backend-with-aad.md), [certyfikaty klienta](api-management-howto-mutual-certificates-for-clients.md), i [listy dozwolonych adresów IP](https://docs.microsoft.com/azure/api-management/api-management-access-restriction-policies#RestrictCallerIPs)

## <a name="scope-of-subscriptions"></a>Zakres subskrypcji

Subskrypcje można skojarzyć z różnych zakresów: produkt, wszystkie interfejsy API lub poszczególnych interfejsu API.

### <a name="subscriptions-for-a-product"></a>Subskrypcji dla produktu

Tradycyjnie subskrypcji w APIM zawsze zostały skojarzone z pojedynczym [oferowanego interfejsu API](api-management-terminology.md) zakresu. Deweloperów Znajdź listę produktów w portalu dla deweloperów i przesyłać żądania subskrypcji dla produktów, które ma być używana. Gdy subskrypcja żądanie zostanie zatwierdzone, (automatycznie lub przez wydawcy interfejsów API), deweloper służy kluczy w nim dostęp do wszystkich interfejsów API w produkcie.

![Subskrypcje produktów](./media/api-management-subscriptions/product-subscription.png)

> [!TIP]
> W niektórych scenariuszach może być publikowanie produktu interfejsu API publicznie bez wymogu subskrypcji wydawcy interfejsów API. Mogą oni zaznaczenie **wymagają subskrypcji** opcji **ustawienia** stronę produktu w witrynie Azure portal. W rezultacie wszystkie interfejsy API w ramach produktu są dostępne bez klucza API.

### <a name="subscriptions-for-all-apis-or-an-individual-api"></a>Subskrypcje dla wszystkich interfejsów API lub poszczególnych interfejsu API

> [!NOTE]
> Obecnie ta funkcja jest dostępna w ramach warstwy interfejsu API zarządzania zużycia.

Kiedy wdrożyliśmy [zużycie](https://aka.ms/apimconsumptionblog) warstwy z usługi APIM, wprowadziliśmy kilka zmian, aby usprawnić zarządzanie kluczami. Po pierwsze, dodaliśmy dwa zakresy więcej subskrypcji — wszystkie interfejsy API i jednego interfejsu API. Zakres subskrypcji nie jest już ograniczona do produktu interfejsu API. Teraz jest możliwa do tworzenia kluczy udzielanie dostępu do interfejsu API (lub wszystkie interfejsy API w ramach wystąpienia usługi APIM), bez konieczności tworzenia produktu i najpierw Dodaj interfejsy API do niego. Ponadto każde wystąpienie usługi APIM zawiera teraz niezmienne, interfejsy API wszystkich subskrypcji, co pozwala na łatwiejsze i bardziej bezpośredni, testowanie i debugowanie interfejsów API w konsoli testów.

Po drugie usługi APIM umożliwia teraz subskrypcjami "autonomicznej". Subskrypcje nie muszą być skojarzone z kontem dewelopera. Jest to przydatne w scenariuszach takich jak gdy subskrypcja jest współużytkowany przez wielu deweloperów i zespołów.

Na koniec wydawcy interfejsów API można teraz [tworzyć subskrypcje](api-management-howto-create-subscriptions.md) bezpośrednio w witrynie Azure Portal:

![Elastyczne subskrypcji](./media/api-management-subscriptions/flexible-subscription.png)

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji na temat usługi API Management:

+ Dowiedz się, inne [pojęcia](api-management-terminology.md) w usłudze API Management
+ Postępuj zgodnie z naszym [samouczki](import-and-publish.md) Aby dowiedzieć się więcej o usłudze API Management
+ Sprawdź nasze [z często Zadawanymi pytaniami](api-management-faq.md) dla często zadawane pytania