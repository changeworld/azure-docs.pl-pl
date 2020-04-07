---
title: Aktualizowanie istniejącej oferty modułów usługi Azure IoT Edge | Azure Marketplace
description: Jak zaktualizować istniejącą ofertę modułu usługi IoT Edge w portalu Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: 821007e40eaa91c98f157ee0b28fe01f37e3cbc9
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80743964"
---
# <a name="update-an-existing-iot-edge-module-offer"></a>Aktualizowanie istniejącej oferty modułów usługi IoT Edge

>[!Important]
>Od 30 marca 2020 r. rozpoczniemy przenoszenie zarządzania ofertami modułu IoT Edge do Centrum partnerskiego. Po migracji utworzysz oferty i zarządzasz nimi w Centrum partnerów. Postępuj zgodnie z instrukcjami w [tworzenie oferty modułu IoT Edge,](https://aka.ms/AzureCreateIoT) aby zarządzać zmigrowanymi ofertami.

W tym artykule krok postępuje przez różne aspekty aktualizowania oferty modułu Usługi IoT Edge w [portalu cloud partner,](https://cloudpartner.azure.com/) a następnie ponownego publikowania oferty.

Istnieje kilka powodów, dla których warto zaktualizować ofertę, takich jak:

-  Dodawanie nowej wersji obrazu modułu usługi IoT Edge do istniejących jednostek SKU.
-  Dodawanie nowych jednostek SKU.
-  Aktualizowanie metadanych portalu marketplace dla oferty lub poszczególnych jednostek SKU.

Aby pomóc w tych modyfikacjach, portal oferuje funkcje **Porównaj** i **Historia.**  


## <a name="unpermitted-changes-to-iot-edge-module-offer-or-sku"></a>Niedozwolone zmiany w ofercie modułów IoT Edge lub jednostce SKU

Istnieją atrybuty oferty modułu usługi IoT Edge lub jednostki SKU, których nie można zmienić po uruchomieniu oferty w portalu Azure Marketplace. Nie można zmienić następujących ustawień:

-  **Identyfikator oferty** i **identyfikator wydawcy** oferty
-  **Identyfikator jednostki SKU** istniejących jednostek SKU
-  Tagi wersji, na przykład:`1.0.1`
-  Zmiany modelu rozliczeń/licencji na istniejących jednostkach SKU

## <a name="common-update-operations"></a>Typowe operacje aktualizacji

Następujące operacje aktualizacji są wspólne.

### <a name="update-the-iot-edge-module-image-version-for-a-sku"></a>Aktualizowanie wersji obrazu modułu usługi IoT Edge dla jednostki SKU

Często jest to, że obraz modułu usługi IoT Edge jest okresowo aktualizowany za pomocą poprawek zabezpieczeń, dodatkowych funkcji i tak dalej. W tym scenariuszu chcesz zaktualizować obraz modułu usługi IoT Edge, do którego odwołuje się jednostka SKU, wykonując następujące kroki:

1.  Zaloguj się do [portalu cloud partner](https://cloudpartner.azure.com/).

2.  W obszarze **Wszystkie oferty**znajdź ofertę, którą chcesz zaktualizować.

3.  Na karcie **Jednostki SKU** wybierz jednostkę SKU skojarzoną z obrazem modułu usługi IoT Edge do zaktualizowania.

4.  W obszarze **Obraz modułu Brzeg**wybierz **+ Nowa wersja obrazu,** aby dodać nowy obraz modułu IoT Edge.

5.  Podaj nowe wersje **obrazów**modułu IoT Edge . Wersja obrazu musi być zgodna z tymi samymi wytycznymi dotyczącymi znaczników, co poprzednie wersje. Tagi wersji powinny mieć postać X.Y.Z, gdzie X, Y i Z są liczbami całkowitymi. Sprawdź, czy nowa wersja, którą podasz, jest większa niż wszystkie poprzednie wersje.

6.  Wybierz **pozycję Publikuj,** aby uruchomić przepływ pracy, aby opublikować nową wersję modułu usługi IoT Edge w portalu Azure Marketplace.

### <a name="add-a-new-sku"></a>Dodawanie nowej jednostki SKU

Aby udostępnić nową jednostkę SKU dla swojej oferty, użyj następujących czynności: 

1.  Zaloguj się do [portalu cloud partner](https://cloudpartner.azure.com/).

2.  W obszarze **Wszystkie oferty**znajdź ofertę, którą chcesz zaktualizować.

3.  Na karcie **Jednostki SKU** wybierz pozycję **Dodaj nową jednostkę SKU** i podaj identyfikator **jednostki SKU** w wyskakującym oknie.

4.  Ponownie opublikuj moduł usługi IoT Edge, wykonując czynności opisane w [obszarze Publikowanie modułu usługi IoT Edge w portalu Azure Marketplace.](./cpp-publish-offer.md)

5.  Wybierz **pozycję Publikuj,** aby uruchomić przepływ pracy, aby opublikować nową jednostkę SKU.


### <a name="update-offer-marketplace-metadata"></a>Aktualizowanie metadanych portalu marketplace ofert

Aby zaktualizować metadane portalu marketplace skojarzone z ofertą, należy wykonać następujące czynności. (Na przykład: nazwa firmy, logo itp.)

1.  Zaloguj się do [portalu cloud partner](https://cloudpartner.azure.com/).

2.  W obszarze **Wszystkie oferty**znajdziesz ofertę, którą chcesz zaktualizować.

3.  Przejdź do karty **Marketplace.** Użyj instrukcji w [artykule Publikowania modułu usługi IoT Edge](./cpp-publish-offer.md) w portalu Azure Marketplace, aby wprowadzić zmiany metadanych.

4.  Wybierz **pozycję Publikuj,** aby uruchomić przepływ pracy, aby opublikować zmiany.

## <a name="compare-feature"></a>Porównaj funkcję

Po wprowadzeniu zmian w opublikowanej ofercie można użyć funkcji **Porównaj** do inspekcji wprowadzonych zmian. 

**Aby użyć funkcji Porównaj:**

1.  W dowolnym momencie procesu edycji wybierz **pozycję Porównaj** dla swojej oferty.

    ![Przycisk Porównaj funkcję](./media/iot-edge-module-compare.png)


2.  Przyjrzyj się wersjom zasobów marketingowych i metadanych obok siebie.


## <a name="history-of-publishing-actions"></a>Historia akcji wydawniczych

Aby wyświetlić historyczne działania publikowania, wybierz kartę **Historia** na pasku menu nawigacji po lewej stronie portalu Cloud Partner Portal. Możesz zobaczyć akcje sygnatury czasowej podjęte w okresie istnienia oferty portalu Azure Marketplace.  <!-- Need to find correct link here:  legal time windowsFor more information, see [History page](cpp-history-page.md) -->
