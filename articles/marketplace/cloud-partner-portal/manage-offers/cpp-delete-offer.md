---
title: Usuwanie ofert marketplace | Azure Marketplace
description: Usuwanie ofert na platformie Azure i w witrynach AppSource Marketplaces przy użyciu portalu cloud partnerów
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: dsindona
ms.openlocfilehash: fa8ee834bd6d9261c47c540e9517c200c894b6c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80286459"
---
# <a name="delete-azure-marketplace-and-appsource-offers-or-skus"></a>Usuwanie ofert portalu Azure Marketplace i AppSource lub jednostek SKU

Z różnych powodów możesz wycofać swoją ofertę z witryny Microsoft Marketplace, która może przybierać dwie formy:

- *Usunięcie oferty* gwarantuje, że nowi klienci nie mogą już kupować ani wdrażać oferty, ale nie ma wpływu na istniejących klientów, których musisz obsługiwać zgodnie z umową licencyjną i obowiązującymi przepisami. 
- *Rozwiązanie oferty* to proces rozwiązania umowy o świadczenie usług i/lub licencji między Użytkownikiem a jego obecnymi klientami. 

Wskazówki i zasady związane z usuwaniem i rozwiązaniem oferty podlegają [Umowie wydawcy microsoft marketplace](https://go.microsoft.com/fwlink/?LinkID=699560) i [Zasadom uczestnictwa](https://azure.microsoft.com/support/legal/marketplace/participation-policies/) (sekcja [Oferowanie zawieszenia i usuwania).](https://docs.microsoft.com/legal/marketplace/participation-policy#offering-suspension-and-removal) 

W tym artykule o innych obsługiwanych scenariuszach usuwania i krokach wymaganych do wykonania każdego z nich.  

> [!NOTE]
> Możesz usunąć ofertę, która nie została opublikowana, po prostu wybierając przycisk **Usuń** na pasku narzędzi na karcie **Edytor.**


## <a name="delete-a-published-sku-from-the-azure-marketplace"></a>Usuwanie opublikowanej jednostki SKU z portalu Azure Marketplace

Opublikowaną jednostkę SKU można usunąć z witryny Azure Marketplace, wykonując następujące czynności:

1.  Zaloguj się do [portalu cloud partner](https://cloudpartner.azure.com/).
2.  Na stronie **Wszystkie oferty** wybierz swoją ofertę.  Twoja oferta powinna być wyświetlana na karcie **Edytor.**
3.  Na lewym pasku narzędzi wybierz kartę **Jednostki SKU.** 
4.  Wybierz jednostkę SKU, którą chcesz usunąć, i kliknij przycisk **Usuń.**
5.  [Ponownie opublikuj](./cpp-publish-offer.md) ofertę w portalu Azure Marketplace.

Po opublikowaniu zmodyfikowanej oferty w portalu Azure Marketplace wybrana jednostka SKU nie będzie już wyświetlana w portalu Azure Marketplace i Azure.


## <a name="roll-back-to-a-previous-sku-version"></a>Powrót do poprzedniej wersji jednostki SKU

Możesz usunąć bieżącą wersję opublikowanej jednostki SKU z usługi Azure Marketplace, wykonując kroki opisane w tym miejscu. Po zakończeniu procesu jednostka SKU jest przywracana do poprzedniej wersji.

1. Zaloguj się do [portalu cloud partner](https://cloudpartner.azure.com/).
2. Na stronie **Wszystkie oferty** wybierz swoją ofertę.  Twoja oferta powinna być wyświetlana na karcie **Edytor.**
3. Na lewym pasku narzędzi wybierz kartę **Jednostki SKU.** 
4. Usuń najnowszą wersję skojarzonego zasobu rozwiązania z listy wersji dysków.  W zależności od typu oferty to pole może być **wersja dysku,** **wersje pakietów**lub podobny zasób. 
5. [Ponownie opublikuj](./cpp-publish-offer.md) ofertę w portalu Azure Marketplace.

Po opublikowaniu zmodyfikowanej oferty w portaluAzure Marketplace bieżąca wersja wymienionej jednostki SKU nie będzie już wyświetlana. w portalu Azure Marketplace i witrynie Azure.  Jednostka SKU jest przywracana do poprzedniej wersji.


## <a name="delete-a-live-offer"></a>Usuwanie oferty na żywo

Istnieją różne aspekty proceduralne, biznesowe i prawne, aby usunąć ofertę na żywo. Wykonaj następujące kroki, aby uzyskać wskazówki od zespołu pomocy technicznej, aby usunąć ofertę na żywo z portalu Azure Marketplace:

1.  Podnieś bilet pomocy technicznej, korzystając ze strony [Utwórz zdarzenie](https://go.microsoft.com/fwlink/?linkid=844975) lub klikając **pozycję Pomoc techniczna** w prawym górnym rogu [portalu cloud partner.](https://cloudpartner.azure.com/)

2.  Wybierz określony typ oferty na liście **Typ problemu** i wybierz pozycję **Usuń opublikowaną ofertę** na liście **Kategoria.**

3.  Prześlij wniosek.

Zespół pomocy technicznej poprowadzi Cię przez proces usuwania oferty.

> [!NOTE]
> Usunięcie oferty (lub jednostki SKU) nie wpłynie na bieżące zakupy tej oferty (lub jednostki SKU). Te zakupy będą nadal działać tak jak poprzednio. Jednak usunięte oferty lub jednostki SKU nie będą dostępne dla przyszłych zakupów.


## <a name="next-steps"></a>Następne kroki

Po zapoznaniu się z podstawowymi operacjami używanymi do zarządzania ofertami można przystąpić do utworzenia wystąpienia [oferty firmy](../cpp-marketplace-offers.md)Microsoft marketplace .
