---
title: Aktualizowanie istniejącej oferty modułu usługi Azure IoT Edge | Dokumentacja firmy Microsoft
description: Jak zaktualizować istniejące oferty modułu usługi IoT Edge w witrynie Azure Marketplace.
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
ms.date: 10/18/2018
ms.author: pbutlerm
ms.openlocfilehash: ca7bed26d91c28304638e85d6da93708bfcfcada
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60914126"
---
# <a name="update-an-existing-iot-edge-module-offer"></a>Aktualizowanie istniejącej oferty modułu usługi IoT Edge

Ten artykuł przeprowadza przez różne aspekty aktualizowanie oferty modułu usługi IoT Edge [portalu Cloud Partner](https://cloudpartner.azure.com/) i ponowne opublikowanie oferty.

Istnieje kilka powodów dlaczego warto zaktualizować oferty, takich jak:

-  Dodawanie nowej wersji obrazu modułu usługi IoT Edge do istniejącej jednostki SKU.
-  Dodawanie nowych jednostek SKU.
-  Aktualizowanie metadanych portalu marketplace oferty lub poszczególne jednostki SKU.

Aby pomóc w tych zmian, oferuje portalu **porównania** i **historii** funkcji.  


## <a name="unpermitted-changes-to-iot-edge-module-offer-or-sku"></a>Niedozwolonej zmiany oferty modułu usługi IoT Edge lub jednostki SKU

Istnieją atrybuty oferty modułu usługi IoT Edge lub jednostki SKU, nie można jej zmienić po oferta jest dostępna na żywo w witrynie Azure Marketplace. Nie można zmienić następujące ustawienia:

-  **Identyfikator oferty** i **Identyfikatora wydawcy** oferty
-  **Identyfikator jednostki SKU** istniejących jednostek SKU
-  Wersja — tagi, na przykład: `1.0.1`
-  Model rozliczeń/licencji zmian do istniejących jednostek SKU

## <a name="common-update-operations"></a>Typowe operacje aktualizacji

Następujące operacje aktualizacji są powszechne.

### <a name="update-the-iot-edge-module-image-version-for-a-sku"></a>Zaktualizuj wersję obrazu modułu usługi IoT Edge dla jednostki SKU

Bardzo często obraz modułu usługi IoT Edge, aby być okresowo aktualizowane za pomocą poprawek zabezpieczeń, dodatkowe funkcje i tak dalej. W tym scenariuszu chcesz zaktualizować obrazu modułu usługi IoT Edge, który odwołuje się do danej jednostki SKU wykonując następujące kroki:

1.  Zaloguj się do [portalu dla partnerów w chmurze](https://cloudpartner.azure.com/).

2.  W obszarze **wszystkich ofert**, odnaleźć oferty, o których chcesz zaktualizować.

3.  W **jednostki SKU** , a następnie wybierz jednostkę SKU skojarzone z obrazem moduł usługi IoT Edge do zaktualizowania.

4.  W obszarze **obraz modułu krawędzi**, wybierz opcję **+ nowa wersja obrazu** można dodać nowego obrazu modułu usługi IoT Edge.

5.  Podaj nowy moduł usługi IoT Edge **obraz wersji**. Wersja obrazu musi postępuj zgodnie z tym samym wytycznym tagów w poprzednich wersjach. Wersja tagi powinny mieć postać X.Y.Z, gdzie X, Y i Z są liczbami całkowitymi. Sprawdź, czy nowa wersja, którą podasz jest większa niż wszystkie poprzednie wersje.

6.  Wybierz **Publikuj** można uruchomić przepływu pracy, aby opublikować nową wersję modułu usługi IoT Edge w portalu Azure Marketplace.

### <a name="add-a-new-sku"></a>Dodawanie nowej jednostki SKU

Wykonaj następujące kroki, aby udostępnić nowej jednostki SKU oferty: 

1.  Zaloguj się do [portalu dla partnerów w chmurze](https://cloudpartner.azure.com/).

2.  W obszarze **wszystkich ofert**, odnaleźć oferty, o których chcesz zaktualizować.

3.  W obszarze **jednostki SKU** zaznacz **Dodawanie nowej jednostki SKU** i podaj **identyfikator jednostki SKU** w oknie podręcznym.

4.  Publikuj ponownie moduł usługi IoT Edge, korzystając z procedury opisanej w [publikować moduł usługi IoT Edge w portalu Azure Marketplace](./cpp-publish-offer.md).

5.  Wybierz **Publikuj** można uruchomić przepływu pracy, aby opublikować nowe jednostki SKU.


### <a name="update-offer-marketplace-metadata"></a>Zaktualizowanie metadanych portalu marketplace oferty

Wykonaj następujące kroki, aby zaktualizować metadane marketplace skojarzone z ofertą. (Na przykład: firmy nazwy, logo, itp.)

1.  Zaloguj się do [portalu dla partnerów w chmurze](https://cloudpartner.azure.com/).

2.  W obszarze **wszystkich ofert**, odnaleźć oferty, czy chcesz zaktualizować.

3.  Przejdź do **Marketplace** kartę. Postępuj zgodnie z instrukcjami w [publikować moduł usługi IoT Edge w portalu Azure Marketplace](./cpp-publish-offer.md) artykuł, aby wprowadzić zmiany metadanych.

4.  Wybierz **Publikuj** można uruchomić przepływu pracy, aby opublikować zmiany.

## <a name="compare-feature"></a>Porównanie funkcji

Po wprowadzeniu zmian w ramach oferty opublikowane, możesz użyć **porównania** funkcję inspekcji, które zostały wprowadzone zmiany. 

**Aby użyć funkcji porównania:**

1.  W dowolnym momencie procesu edycji, wybierz **porównania** oferty.

    ![Porównanie funkcji przycisku](./media/iot-edge-module-compare.png)


2.  Obejrzyj wersje side-by-side marketingu zasoby i metadanych.


## <a name="history-of-publishing-actions"></a>Historia akcji publikowania

Aby wyświetlić działań historycznych w publikacji, wybierz **historii** kartę na lewym pasku nawigacyjnym menu paska z portalu Cloud Partner. Możesz zobaczyć akcje oznaczony sygnaturą czasową wykonywane w okresie istnienia Twoich ofert w portalu Azure Marketplace.  <!-- Need to find correct link here:  legal time windowsFor more information, see [History page](cpp-history-page.md) -->
