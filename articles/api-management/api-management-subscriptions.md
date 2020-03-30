---
title: Subskrypcje w usłudze Azure API Management | Dokumenty firmy Microsoft
description: Dowiedz się więcej o koncepcji subskrypcji w usłudze Azure API Management.
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/14/2018
ms.author: apimpm
ms.openlocfilehash: 9144af131e1427d0b3226655c871921ac1d91665
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70073423"
---
# <a name="subscriptions-in-azure-api-management"></a>Subskrypcje w usłudze Azure API Management

Subskrypcje są ważną koncepcją w usłudze Azure API Management. Są one najbardziej typowym sposobem dla konsumentów interfejsu API, aby uzyskać dostęp do interfejsów API opublikowanych za pośrednictwem wystąpienia usługi API Management. Ten artykuł zawiera omówienie pojęcia.

## <a name="what-are-subscriptions"></a>Co to są subskrypcje?

Podczas publikowania interfejsów API za pośrednictwem usługi API Management, jest to łatwe i wspólne do bezpiecznego dostępu do tych interfejsów API przy użyciu kluczy subskrypcji. Deweloperzy, którzy muszą korzystać z opublikowanych interfejsów API, muszą dołączyć prawidłowy klucz subskrypcji w żądaniach HTTP podczas wykonywania połączeń z tymi interfejsami API. W przeciwnym razie wywołania są natychmiast odrzucane przez bramę zarządzania interfejsem API. Nie są one przekazywane do usług zaplecza.

Aby uzyskać klucz subskrypcji dostępu do interfejsów API, wymagana jest subskrypcja. Subskrypcja jest zasadniczo nazwany kontener dla pary kluczy subskrypcji. Deweloperzy, którzy muszą korzystać z opublikowanych interfejsów API, mogą uzyskać subskrypcje. I nie potrzebują zatwierdzenia przez wydawców interfejsu API. Wydawcy interfejsu API mogą również tworzyć subskrypcje bezpośrednio dla konsumentów interfejsu API.

> [!TIP]
> Api Management obsługuje również inne mechanizmy zabezpieczania dostępu do interfejsów API, w tym następujące przykłady:
> - [ZOuth2.0](api-management-howto-protect-backend-with-aad.md)
> - [Certyfikaty klientów](api-management-howto-mutual-certificates-for-clients.md)
> - [Biała lista adresów IP](https://docs.microsoft.com/azure/api-management/api-management-access-restriction-policies#RestrictCallerIPs)

## <a name="scope-of-subscriptions"></a>Zakres subskrypcji

Subskrypcje mogą być skojarzone z różnymi zakresami: produktem, wszystkimi interfejsami API lub pojedynczym interfejsem API.

### <a name="subscriptions-for-a-product"></a>Subskrypcje produktu

Tradycyjnie subskrypcje w usłudze API Management były zawsze skojarzone z zakresem [produktu](api-management-terminology.md) interfejsu API. Deweloperzy znaleźli listę produktów w portalu dla deweloperów. Następnie przesyłali żądania subskrypcji produktów, których chcieli użyć. Po zatwierdzeniu żądania subskrypcji, automatycznie lub przez wydawców interfejsu API, deweloper może użyć kluczy w nim, aby uzyskać dostęp do wszystkich interfejsów API w produkcie. Obecnie portal deweloperów pokazuje tylko subskrypcje zakresu produktu w sekcji profilu użytkownika. 

![Subskrypcje produktów](./media/api-management-subscriptions/product-subscription.png)

> [!TIP]
> W niektórych scenariuszach wydawcy interfejsu API mogą chcieć opublikować produkt interfejsu API publicznie bez wymogu subskrypcji. Mogą usunąć zaznaczenie opcji **Wymagaj subskrypcji** na stronie **Ustawienia** produktu w witrynie Azure portal. W rezultacie wszystkie interfejsy API w ramach produktu można uzyskać dostęp bez klucza interfejsu API.

### <a name="subscriptions-for-all-apis-or-an-individual-api"></a>Subskrypcje dla wszystkich interfejsów API lub pojedynczego interfejsu API

Po wprowadzeniu [warstwy Zużycie](https://aka.ms/apimconsumptionblog) zarządzania interfejsami API wprowadziliśmy kilka zmian w celu usprawnienia zarządzania kluczami:
- Najpierw dodaliśmy dwa kolejne zakresy subskrypcji: wszystkie interfejsy API i jeden interfejs API. Zakres subskrypcji nie jest już ograniczony do produktu interfejsu API. Teraz można utworzyć klucze, które udzielają dostępu do interfejsu API lub wszystkich interfejsów API w wystąpieniu usługi API Management, bez konieczności tworzenia produktu i najpierw dodania interfejsów API. Ponadto każde wystąpienie usługi API Management jest teraz wyposażone w niezmienną subskrypcję wszystkich interfejsów API. Ta subskrypcja ułatwia i ułatwia testowanie i debugowanie interfejsów API w konsoli testowej.

- Po drugie, zarządzanie interfejsem API umożliwia teraz **autonomiczne** subskrypcje. Subskrypcje nie muszą być już skojarzone z kontem dewelopera. Ta funkcja jest przydatna w scenariuszach, takich jak gdy kilku deweloperów lub zespołów współużytkuje subskrypcję.

- Na koniec wydawcy interfejsu API mogą teraz [tworzyć subskrypcje](api-management-howto-create-subscriptions.md) bezpośrednio w witrynie Azure portal:

    ![Elastyczne subskrypcje](./media/api-management-subscriptions/flexible-subscription.png)

## <a name="next-steps"></a>Następne kroki
Uzyskaj więcej informacji na temat zarządzania interfejsami API:

+ Poznaj inne pojęcia dotyczące zarządzania [interfejsami](api-management-terminology.md) API.
+ Postępuj zgodnie z naszymi [samouczkami,](import-and-publish.md) aby dowiedzieć się więcej o zarządzaniu interfejsami API.
+ Często zadawane pytania można zadać na naszej [stronie z często zadawanymi](api-management-faq.md) pytaniami.
