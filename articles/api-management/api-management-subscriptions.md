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
ms.openlocfilehash: afd43bbf6f52f498ad8f56d5a48b960d45d84137
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66243264"
---
# <a name="subscriptions-in-azure-api-management"></a>Subskrypcje w usłudze Azure API Management

Subskrypcje są bardzo ważnym pojęciem w usłudze Azure API Management. Są one najbardziej popularny sposób interfejsu API w konsumentach napisanych w celu uzyskania dostępu do interfejsów API opublikowane za pośrednictwem wystąpienia usługi API Management. Ten artykuł zawiera omówienie koncepcji.

## <a name="what-are-subscriptions"></a>Czym są subskrypcje?

Podczas publikowania interfejsów API za pomocą usługi API Management jest prosta i wspólne dla bezpiecznego dostępu do tych interfejsów API przy użyciu kluczy subskrypcji. Deweloperzy, którzy muszą korzystać z opublikowanych interfejsów API musi zawierać klucz ważnej subskrypcji w żądaniach HTTP, w momencie wywołania tych interfejsów API. W przeciwnym razie wywołania są odrzucane od razu przez bramę usługi API Management. Nie są one przekazywane do usług zaplecza.

Aby uzyskać klucz subskrypcji do uzyskiwania dostępu do interfejsów API, wymagana jest subskrypcja. Subskrypcja stanowi nazwane kontener dla pary kluczy subskrypcji. Deweloperzy, którzy muszą korzystać z opublikowanych interfejsów API można uzyskać subskrypcji. I nie wymagają zatwierdzenia od wydawcy interfejsów API. Wydawcy interfejsów API można również tworzyć subskrypcje bezpośrednio dla klientów interfejsu API.

> [!TIP]
> Usługa API Management obsługuje również inne mechanizmy do zabezpieczania dostępu do interfejsów API, w tym w następujących przykładach:
> - [OAuth2.0](api-management-howto-protect-backend-with-aad.md)
> - [Certyfikaty klienta](api-management-howto-mutual-certificates-for-clients.md)
> - [Listy dozwolonych adresów IP](https://docs.microsoft.com/azure/api-management/api-management-access-restriction-policies#RestrictCallerIPs)

## <a name="scope-of-subscriptions"></a>Zakres subskrypcji

Subskrypcje można skojarzyć z różnych zakresów: produkt, wszystkie interfejsy API lub poszczególnych interfejsu API.

### <a name="subscriptions-for-a-product"></a>Subskrypcji dla produktu

Tradycyjnie subskrypcji w usłudze API Management zawsze były skojarzone z pojedynczym [oferowanego interfejsu API](api-management-terminology.md) zakresu. Deweloperzy znaleźć listę produktów w portalu dla deweloperów. Następnie będzie przesyłają oni żądania subskrypcji dla produktów, że użytkownik chce używać. Po zatwierdzeniu żądania subskrypcji automatycznie lub przez wydawców interfejsów API, deweloper służy kluczy w nim dostęp do wszystkich interfejsów API w produkcie. Obecnie portal dla deweloperów wyświetlane są tylko subskrypcje w zakresie produktu w ramach sekcji profilu użytkownika. 

![Subskrypcje produktów](./media/api-management-subscriptions/product-subscription.png)

> [!TIP]
> W niektórych scenariuszach może być publikowanie produktu interfejsu API publicznie bez wymogu subskrypcji wydawcy interfejsów API. Można usunąć zaznaczenie **wymagają subskrypcji** opcja **ustawienia** stronę produktu w witrynie Azure portal. W rezultacie wszystkie interfejsy API w ramach produktu są dostępne bez klucza API.

### <a name="subscriptions-for-all-apis-or-an-individual-api"></a>Subskrypcje dla wszystkich interfejsów API lub poszczególnych interfejsu API

Kiedy wdrożyliśmy [zużycie](https://aka.ms/apimconsumptionblog) warstwy interfejsu API zarządzania, wprowadziliśmy kilka zmian, aby usprawnić zarządzanie kluczami:
- Po pierwsze, dodaliśmy dwa więcej zakresów subskrypcji: wszystkie interfejsy API i jednego interfejsu API. Zakres subskrypcji nie jest już ograniczona do produktu interfejsu API. Teraz jest możliwa do tworzenia kluczy określającymi udzielenie dostępu do interfejsu API lub wszystkie interfejsy API w ramach wystąpienia usługi API Management, bez konieczności tworzenia produktu i najpierw Dodaj interfejsy API do niego. Ponadto każde wystąpienie usługi API Management zawiera teraz niezmienne, interfejsy API wszystkich subskrypcji. Ta subskrypcja umożliwia łatwiejsze i bardziej bezpośredni, testowanie i debugowanie interfejsów API w konsoli testów.

- Po drugie, usługa API Management umożliwia teraz **autonomiczny** subskrypcji. Subskrypcje nie muszą być skojarzone z kontem dewelopera. Ta funkcja jest przydatne w scenariuszach, takich jak po kilku deweloperów i zespołów udostępnianie subskrypcji.

- Na koniec wydawcy interfejsów API można teraz [tworzyć subskrypcje](api-management-howto-create-subscriptions.md) bezpośrednio w witrynie Azure portal:

    ![Elastyczne subskrypcji](./media/api-management-subscriptions/flexible-subscription.png)

## <a name="next-steps"></a>Kolejne kroki
Uzyskaj więcej informacji na temat usługi API Management:

+ Dowiedz się, inne [pojęcia](api-management-terminology.md) w usłudze API Management.
+ Postępuj zgodnie z naszym [samouczki](import-and-publish.md) Aby dowiedzieć się więcej o usłudze API Management.
+ Sprawdź nasze [z często Zadawanymi pytaniami](api-management-faq.md) dla często zadawane pytania.
