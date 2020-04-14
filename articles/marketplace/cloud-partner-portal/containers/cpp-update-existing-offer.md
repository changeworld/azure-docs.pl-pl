---
title: Aktualizowanie istniejącej oferty kontenerów platformy Azure | Azure Marketplace
description: Jak zaktualizować istniejącą ofertę kontenerów w portalu Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: dsindona
ms.openlocfilehash: 650ef45e56184b67efba67810580d9d6e763a4d0
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81271271"
---
# <a name="update-an-existing-container-offer"></a>Aktualizowanie istniejącej oferty kontenerów

> [!IMPORTANT]
> Od 13 kwietnia 2020 r. rozpoczniemy przenoszenie zarządzania ofertami usługi Azure Container do Centrum partnerów. Po migracji utworzysz oferty i zarządzasz nimi w Centrum partnerów. Postępuj zgodnie z instrukcjami w [tworzenie oferty usługi Azure Container](https://aka.ms/CreateContainerOffer) do zarządzania zmigrowanych ofert.

W tym artykule ostępuje kilka kroków od różnych aspektów aktualizowania oferty kontenerów w [portalu cloud partner.](https://cloudpartner.azure.com/)

Istnieje kilka powodów, dla których warto zaktualizować ofertę, takich jak:

-  Dodawanie nowej wersji obrazu kontenera do istniejących jednostek SKU.
-  Dodawanie nowych jednostek SKU.
-  Aktualizowanie metadanych portalu marketplace dla oferty lub poszczególnych jednostek SKU.

Aby ułatwić te modyfikacje, portal udostępnia funkcje **Porównania** i **historii.**  


## <a name="unpermitted-changes-to-a-container-offer-or-sku"></a>Niedozwolone zmiany w ofercie kontenera lub jednostce SKU

Istnieją atrybuty oferty kontenera lub jednostki SKU, których nie można zmienić po uruchomieniu oferty w portalu Azure Marketplace. Nie można zmienić następujących ustawień:

-  **Identyfikator oferty** i **identyfikator wydawcy** oferty
-  **Identyfikator jednostki SKU** istniejących jednostek SKU
-  Tagi wersji, na przykład:`1.0.1`
-  Zmiany modelu rozliczeń/licencji na istniejących jednostkach SKU

## <a name="common-update-operations"></a>Typowe operacje aktualizacji

Następujące operacje aktualizacji są wspólne.

### <a name="update-container-image-version-for-a-sku"></a>Aktualizowanie wersji obrazu kontenera dla jednostki SKU

Często obraz kontenera jest okresowo aktualizowany za pomocą poprawek zabezpieczeń, dodatkowych funkcji i tak dalej. W tym scenariuszu chcesz zaktualizować obraz kontenera, do którego odwołuje się jednostka SKU, wykonując następujące kroki:

1. Zaloguj się do [portalu cloud partner](https://cloudpartner.azure.com/).
2. W obszarze **Wszystkie oferty**znajdź ofertę, którą chcesz zaktualizować.
3. Na karcie **Jednostki SKU** wybierz jednostkę SKU skojarzoną z obrazem kontenera do zaktualizowania.
4. W obszarze **Obraz kontenera**wybierz **pozycję + Nowa wersja obrazu,** aby dodać nowy obraz kontenera.
5. Podaj nowe **wersje obrazów kontenera**. Wersja obrazu musi być zgodna z tymi samymi wytycznymi dotyczącymi znaczników, co poprzednie wersje. Tagi wersji powinny mieć postać X.Y.Z, gdzie X, Y i Z są liczbami całkowitymi. Sprawdź, czy nowa wersja, którą podasz, jest większa niż wszystkie poprzednie wersje.
6. Wybierz **pozycję Publikuj,** aby uruchomić przepływ pracy, aby opublikować nową wersję obrazu kontenera w portalu Azure Marketplace.

### <a name="add-a-new-sku"></a>Dodawanie nowej jednostki SKU

Aby udostępnić nową jednostkę SKU dla swojej oferty, użyj następujących czynności:

1. Zaloguj się do [portalu cloud partner](https://cloudpartner.azure.com/).
2. W obszarze **Wszystkie oferty**znajdź ofertę, którą chcesz zaktualizować.
3. Na karcie **Jednostki SKU** wybierz pozycję **Dodaj nową jednostkę SKU** i podaj identyfikator **jednostki SKU** w wyskakującym oknie.
4. Ponownie opublikuj kontener, wykonując czynności opisane w [opisie oferty kontenera Publikowania](./cpp-publish-offer.md).
5. Wybierz **pozycję Publikuj,** aby uruchomić przepływ pracy, aby opublikować nową jednostkę SKU.

### <a name="update-offer-marketplace-metadata"></a>Aktualizowanie metadanych portalu marketplace ofert

Aby zaktualizować metadane portalu marketplace skojarzone z ofertą, należy wykonać następujące czynności. (Na przykład: nazwa firmy, logo itp.)

1. Zaloguj się do [portalu cloud partner](https://cloudpartner.azure.com/).
2. W obszarze **Wszystkie oferty**znajdziesz ofertę, którą chcesz zaktualizować.
3. Przejdź do karty **Marketplace.** Użyj instrukcji w artykule [Oferta oferty kontenera publikowania,](./cpp-publish-offer.md) aby wprowadzić zmiany metadanych.
4. Wybierz **pozycję Publikuj,** aby uruchomić przepływ pracy, aby opublikować zmiany.

## <a name="compare-feature"></a>Funkcja Porównywania

Po wprowadzeniu zmian w opublikowanej ofercie można użyć funkcji **Porównaj** do inspekcji wprowadzonych zmian.

### <a name="to-use-the-compare-feature"></a>Aby użyć funkcji Porównaj:

1. W dowolnym momencie procesu edycji wybierz pozycję Porównaj dla swojej oferty.
2. Przyjrzyj się wersjom zasobów marketingowych i metadanych obok siebie.


## <a name="history-of-publishing-actions"></a>Historia działań wydawniczych

Aby wyświetlić historyczne działania publikowania, wybierz kartę **Historia** na pasku menu nawigacji po lewej stronie portalu Cloud Partner Portal. Możesz zobaczyć akcje sygnatury czasowej podjęte w okresie istnienia oferty portalu Azure Marketplace.
