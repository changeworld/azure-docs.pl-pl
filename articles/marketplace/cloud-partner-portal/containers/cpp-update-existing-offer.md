---
title: Aktualizowanie istniejącej oferty Azure Containers | Dokumentacja firmy Microsoft
description: Jak zaktualizować istniejące oferty kontenera w witrynie Azure Marketplace.
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
ms.topic: article
ms.date: 11/01/2018
ms.author: pbutlerm
ms.openlocfilehash: 2b568717b6656fb9ae15e9a6dbd27441689c4372
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61472710"
---
# <a name="update-an-existing-container-offer"></a>Aktualizowanie istniejącej oferty kontenera

Ten artykuł przeprowadzi różne aspekty aktualizowanie oferty kontenera [portalu Cloud Partner](https://cloudpartner.azure.com/).

Istnieje kilka powodów dlaczego warto zaktualizować oferty, takich jak:

-  Dodawanie nowej wersji obrazu kontenera do istniejącej jednostki SKU.
-  Dodawanie nowych jednostek SKU.
-  Aktualizowanie metadanych portalu marketplace oferty lub poszczególne jednostki SKU.

Aby pomóc w tych zmian, portal zapewnia **porównania** i **historii** funkcji.  


## <a name="unpermitted-changes-to-a-container-offer-or-sku"></a>Niedozwolonej zmiany do skorzystania z oferty kontenera lub jednostki SKU

Istnieją atrybuty oferty kontenera lub jednostki SKU, nie można jej zmienić po oferta jest dostępna na żywo w witrynie Azure Marketplace. Nie można zmienić następujące ustawienia:

-  **Identyfikator oferty** i **Identyfikatora wydawcy** oferty
-  **Identyfikator jednostki SKU** istniejących jednostek SKU
-  Wersja — tagi, na przykład: `1.0.1`
-  Model rozliczeń/licencji zmian do istniejących jednostek SKU

## <a name="common-update-operations"></a>Typowe operacje aktualizacji

Następujące operacje aktualizacji są powszechne.

### <a name="update-container-image-version-for-a-sku"></a>Zaktualizuj wersję obrazu kontenera dla jednostki SKU

Jest to częsty problem w postaci obrazu kontenera do być okresowo aktualizowane za pomocą poprawek zabezpieczeń, dodatkowe funkcje i tak dalej. W tym scenariuszu chcesz zaktualizować obraz kontenera, który odwołuje się do danej jednostki SKU wykonując następujące kroki:

1. Zaloguj się do [portalu dla partnerów w chmurze](https://cloudpartner.azure.com/).
2. W obszarze **wszystkich ofert**, odnaleźć oferty, o których chcesz zaktualizować.
3. W **jednostki SKU** , a następnie wybierz jednostki SKU powiązanej z obrazu kontenera do zaktualizowania.
4. W obszarze **obraz kontenera**, wybierz opcję **+ nowa wersja obrazu** Aby dodać nowy obraz kontenera.
5. Podaj nowy kontener **obraz wersji**. Wersja obrazu musi postępuj zgodnie z tym samym wytycznym tagów w poprzednich wersjach. Wersja tagi powinny mieć postać X.Y.Z, gdzie X, Y i Z są liczbami całkowitymi. Sprawdź, czy nowa wersja, którą podasz jest większa niż wszystkie poprzednie wersje.
6. Wybierz **Publikuj** można uruchomić przepływu pracy, aby opublikować nową wersję obrazu kontenera w portalu Azure Marketplace.

### <a name="add-a-new-sku"></a>Dodawanie nowej jednostki SKU

Wykonaj następujące kroki, aby udostępnić nowej jednostki SKU oferty:

1. Zaloguj się do [portalu dla partnerów w chmurze](https://cloudpartner.azure.com/).
2. W obszarze **wszystkich ofert**, odnaleźć oferty, o których chcesz zaktualizować.
3. W obszarze **jednostki SKU** zaznacz **Dodawanie nowej jednostki SKU** i podaj **identyfikator jednostki SKU** w oknie podręcznym.
4. Ponownie opublikować kontener, korzystając z procedury opisanej w [oferty kontenera Publikuj](./cpp-publish-offer.md).
5. Wybierz **Publikuj** można uruchomić przepływu pracy, aby opublikować nowe jednostki SKU.

### <a name="update-offer-marketplace-metadata"></a>Zaktualizowanie metadanych portalu marketplace oferty

Wykonaj następujące kroki, aby zaktualizować metadane marketplace skojarzone z ofertą. (Na przykład: firmy nazwy, logo i itp.)

1. Zaloguj się do [portalu dla partnerów w chmurze](https://cloudpartner.azure.com/).
2. W obszarze **wszystkich ofert**, odnaleźć oferty, czy chcesz zaktualizować.
3. Przejdź do **Marketplace** kartę. Postępuj zgodnie z instrukcjami w [oferty kontenera Publikuj](./cpp-publish-offer.md) oferują artykuł, aby wprowadzić zmiany metadanych.
4. Wybierz **Publikuj** można uruchomić przepływu pracy, aby opublikować zmiany.

## <a name="compare-feature"></a>Porównanie funkcji

Po wprowadzeniu zmian do opublikowane oferty, możesz użyć **porównania** funkcję inspekcji, które zostały wprowadzone zmiany.

### <a name="to-use-the-compare-feature"></a>Aby użyć funkcji porównania:

1. W dowolnym momencie procesu edycji wybierz Porównaj oferty.
2. Obejrzyj wersje side-by-side marketingu zasoby i metadanych.


## <a name="history-of-publishing-actions"></a>Historia akcji publikowania

Aby wyświetlić działań historycznych w publikacji, wybierz **historii** kartę na lewym pasku nawigacyjnym menu paska z portalu Cloud Partner. Możesz zobaczyć akcje oznaczony sygnaturą czasową wykonywane w okresie istnienia Twoich ofert w portalu Azure Marketplace.