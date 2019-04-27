---
title: Aktualizowanie istniejącej oferty aplikacji SaaS usługi Azure | Dokumentacja firmy Microsoft
description: Jak zaktualizować istniejące oferty aplikacji SaaS w portalu Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 12/04/2018
ms.author: pbutlerm
ms.openlocfilehash: 9d490a0ec09e351e4cee00e7c30c9161bdfac3e6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60594270"
---
# <a name="update-an-existing-saas-application-offer"></a>Aktualizowanie istniejącej oferty aplikacji SaaS

Istnieją różne rodzaje aktualizacje, które możesz chcieć wykonać oferty po opublikowaniu zostały i działa. Wszelkie zmiany wprowadzone do nowej wersji oferty należy zapisać i ponownie opublikować, aby odzwierciedlić w portalu Marketplace. Ten artykuł przeprowadza przez różne aspekty aktualizowanie oferty SaaS [portalu Cloud Partner](https://cloudpartner.azure.com/).

Istnieje kilka powodów dlaczego warto zaktualizować oferty, takich jak:

- Dodawanie nowej wersji do istniejącej aplikacji.
- Aktualizowanie aplikacji.
- Dodawanie nowych funkcji do aplikacji.
- Aktualizowanie metadanych elementu portalu marketplace oferty.

Aby pomóc w tych zmian, portal zapewnia **porównania** i **historii** funkcji.

## <a name="unpermitted-changes-to-a-saas-offer"></a>Niedozwolonej zmiany to oferta SaaS

Istnieją atrybuty to oferta SaaS, nie można jej zmienić po oferta jest dostępna na żywo w witrynie Azure Marketplace. Nie można zmienić następujące ustawienia:

- Identyfikator oferty i identyfikatorze wydawcy, oferty
- Wersja — tagi, na przykład: `1.0.1`
- Model rozliczeń/licencji zmieni się na istniejące oferty.

## <a name="common-update-operations"></a>Typowe operacje aktualizacji
 
Następujące operacje aktualizacji są powszechne.

### <a name="update-offer-contacts"></a>Aktualizowanie kontaktów oferty

Wykonaj następujące kroki, aby zaktualizować kontaktów pomocy technicznej oferty.

1. Zaloguj się do [portalu dla partnerów w chmurze](https://cloudpartner.azure.com/).
2. W obszarze **wszystkich ofert**, odnaleźć oferty, czy chcesz zaktualizować.
3. Przejdź do **kontakty** kartę. Aktualizowanie kontaktów.
4. Wybierz **Publikuj** można uruchomić przepływu pracy, aby opublikować zmiany.


### <a name="update-offer-marketplace-metadata"></a>Zaktualizowanie metadanych portalu marketplace oferty

Wykonaj następujące kroki, aby zaktualizować metadane marketplace skojarzone z ofertą. (Na przykład: firmy nazwy, logo, itp.)

1. Zaloguj się do [portalu dla partnerów w chmurze](https://cloudpartner.azure.com/).
2. W obszarze **wszystkich ofert**, odnaleźć oferty, czy chcesz zaktualizować.
3. Przejdź do **szczegóły Storefront** kartę. Postępuj zgodnie z instrukcjami w [publikowania SaaS oferują](./cpp-publish-offer.md) artykuł, aby wprowadzić zmiany metadanych.
4. Wybierz **Publikuj** można uruchomić przepływu pracy, aby opublikować zmiany.

## <a name="compare-feature"></a>Porównanie funkcji

Po wprowadzeniu zmian do opublikowane oferty można użyć funkcji porównania inspekcji, które zostały wprowadzone zmiany. Następnym zrzucie ekranu przedstawiono porównanie opcji opublikowane oferty.

![Użyj porównania, aby zobaczyć zmiany w portalu Cloud Partner oferty](./media/saas-offer-compare.png)

### <a name="to-use-the-compare-feature"></a>Aby użyć funkcji porównania:

1. W dowolnym momencie procesu edycji wybierz Porównaj oferty.
2. Obejrzyj wersje side-by-side marketingu zasoby i metadanych.

## <a name="publishing-history"></a>Historia publikowania

Aby wyświetlić działań historycznych w publikacji, wybierz **historii** kartę na lewym pasku nawigacyjnym menu paska z portalu Cloud Partner. Możesz zobaczyć akcje oznaczony sygnaturą czasową wykonywane w okresie istnienia Twoich ofert w portalu Azure Marketplace.

![Zobacz oferty historii w portalu Cloud Partner](./media/saas-offer-history.png)

Strona historii inspekcji można użyć do wyszukiwania określonych oferty i stosować filtry, takie jak wydawcy, oferty i typu zdarzenia (na przykład OfferGoLiveRequested.) Historia inspekcji można również pobrać jako plik csv.


## <a name="next-steps"></a>Kolejne kroki

[Oferta aplikacji SaaS](./cpp-saas-offer.md)