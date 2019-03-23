---
title: Usuwanie oferty w witrynie Marketplace — witryna Azure Marketplace | Dokumentacja firmy Microsoft
description: Usuwanie oferty na platformie Azure i usługi AppSource rynków przy użyciu portalu Cloud Partner
services: Azure, AppSource, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: pbutlerm
ms.openlocfilehash: f26597022b89bcd113ee250db75a842c634273eb
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2019
ms.locfileid: "58371387"
---
# <a name="delete-azure-marketplace-and-appsource-offers-or-skus"></a>Usuwanie ofert portalu Azure Marketplace i AppSource lub jednostek SKU

Z różnych powodów może podjąć decyzję wycofania oferty z jej witryny Microsoft marketplace, co może zająć do dwóch formach:

- *Usunięcie oferty* gwarantuje, że nowi klienci mogą już zakup lub wdrożenie oferty, ale nie ma wpływu na istniejących klientów, których musi obsługiwać zgodnie ze swoją umowę licencyjną i stosownych przepisów. 
- *Zakończenie oferty* to proces zakończenia świadczenia usługi i/lub umowę między Tobą i Twoimi obecnymi klientami licencjonowania. 

Wskazówki i zasady dotyczące usuwania i kończenia oferty podlegają [umowie wydawcy portalu Microsoft Marketplace](https://go.microsoft.com/fwlink/?LinkID=699560) (sekcja 7) i [— zasady udziału](https://azure.microsoft.com/support/legal/marketplace/participation-policies/) (sekcja [ Zawieszenie i usunięcie oferty](https://docs.microsoft.com/en-us/legal/marketplace/participation-policy#offering-suspension-and-removal)). 

Rozmowy tego artykułu, o różnych obsługiwane usunięcia scenariusze i kroki wymagane do wykonania każdego.  

> [!NOTE]
> Możesz usunąć ofertę, która nie została opublikowana, po prostu wybierając **Usuń** przycisk na pasku narzędzi **edytora** kartę.


## <a name="delete-a-published-sku-from-the-azure-marketplace"></a>Usuń opublikowane jednostki SKU w portalu Azure Marketplace

Opublikowane jednostki SKU można usunąć z portalu Azure Marketplace wykonując następujące czynności:

1.  Zaloguj się do [portalu dla partnerów w chmurze](https://cloudpartner.azure.com/).
2.  W **wszystkich ofert** wybierz ofertę.  Ofercie powinien być wyświetlany w **edytora** kartę.
3.  Na pasku po lewej stronie wybierz **jednostki SKU** kartę. 
4.  Wybierz jednostkę SKU, który chcesz usunąć, a następnie kliknij przycisk **Usuń** przycisku.
5.  [Ponownie opublikować](./cpp-publish-offer.md) oferty w portalu Azure Marketplace.

Po opublikowaniu oferty zmodyfikowane w portalu Azure Marketplace to wybrana jednostka SKU nie jest już będzie wyświetlane w portalu Azure Marketplace i witryny Azure portal.


## <a name="roll-back-to-a-previous-sku-version"></a>Przywracanie poprzedniej wersji jednostki SKU

Możesz usunąć bieżącą wersję opublikowane jednostki SKU z witryny Azure Marketplace wykonując kroki opisane w tym miejscu. Po zakończeniu procesu jednostki SKU jest przywrócenie poprzedniej wersji.

1. Zaloguj się do [portalu dla partnerów w chmurze](https://cloudpartner.azure.com/).
2. W **wszystkich ofert** wybierz ofertę.  Ofercie powinien być wyświetlany w **edytora** kartę.
3. Na pasku po lewej stronie wybierz **jednostki SKU** kartę. 
4. Najnowszą wersję rozwiązania skojarzonego elementu zawartości należy usunąć z listy wersji dysków.  W zależności od typu oferty to pole może być **wersje dyskowe**, **wersje pakietów**, lub podobne zasobu. 
5. [Ponownie opublikować](./cpp-publish-offer.md) oferty w portalu Azure Marketplace.

Po opublikowaniu oferty zmodyfikowane na theAzure Marketplace bieżącej wersji SKU wymienionych już nie będzie wyświetlane. w portalu Azure Marketplace i witryny Azure portal.  Jednostka SKU jest przywrócenie poprzedniej wersji.


## <a name="delete-a-live-offer"></a>Usuń ofertę opartą na żywo

Istnieją różne proceduralne, biznesową i kwestie prawne usuwanie oferty na żywo. Wykonaj poniższe kroki, aby uzyskać wskazówki z zespołem pomocy technicznej, aby usunąć ofertę opartą na żywo w witrynie Azure Marketplace:

1.  Zgłosić bilet pomocy technicznej przy użyciu [utworzenia zdarzenia](https://go.microsoft.com/fwlink/?linkid=844975) strony lub klikając **obsługuje** w prawym górnym rogu [portalu Cloud Partner](https://cloudpartner.azure.com/).

2.  Wybierz swój typ oferty określone w **typ problemu** listy i wybierz **Usuń opublikowane oferty** w **kategorii** listy.

3.  Prześlij żądanie.

Zespół pomocy technicznej przeprowadzi Cię przez proces usuwania oferty.

> [!NOTE]
> Usuwanie oferty (lub jednostki SKU) nie wpłynie na bieżącym zakupy w tej ofercie (lub jednostki SKU). Te zakupy będą w dalszym ciągu działać tak jak poprzednio. Jednak usunięte oferty lub jednostki SKU nie będą dostępne do wszystkie zakupy dokonane w przyszłości.


## <a name="next-steps"></a>Kolejne kroki

Po użytkownik zna podstawowe operacje, które umożliwiają zarządzanie ofertami, można przystąpić do utworzenia wystąpienia programu Microsoft [oferty w portalu marketplace](../cpp-marketplace-offers.md).
