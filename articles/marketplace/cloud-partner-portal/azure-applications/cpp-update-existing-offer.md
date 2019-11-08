---
title: Aktualizowanie istniejącej oferty aplikacji platformy Azure | Portal Azure Marketplace
description: Jak zaktualizować istniejącą ofertę aplikacji platformy Azure w portalu Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: pabutler
ms.openlocfilehash: 6854e445ea63639866e9e39a6afc725237bbc8fe
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826116"
---
# <a name="update-an-existing-azure-application-offer"></a>Aktualizowanie istniejącej oferty aplikacji platformy Azure

Istnieją różne rodzaje aktualizacji, które warto wykonać w ramach oferty po jej opublikowaniu i zakończeniu. Wszelkie zmiany wprowadzone w nowej wersji oferty powinny zostać zapisane i opublikowane ponownie w celu odzwierciedlenia w portalu Marketplace. W tym artykule opisano różne aspekty aktualizowania oferty zarządzanej aplikacji w [Portal Cloud partner](https://cloudpartner.azure.com/).

Istnieje kilka powodów, dla których warto zaktualizować ofertę, na przykład:

- Dodawanie nowej wersji obrazu do istniejących jednostek SKU.
- Dodawanie nowych jednostek SKU.
- Aktualizowanie metadanych witryny Marketplace dla oferty lub poszczególnych jednostek SKU.

Aby ułatwić Ci modyfikację, Portal udostępnia funkcje **porównania** i **historii** .

## <a name="unpermitted-changes-to-an-azure-application-offer-or-sku"></a>Niedozwolone zmiany w ofercie lub jednostce SKU aplikacji platformy Azure

Istnieją atrybuty oferty kontenera lub jednostki SKU, których nie można zmienić po zakończeniu oferty w witrynie Azure Marketplace. Nie można zmienić następujących ustawień:

- Identyfikator oferty i identyfikator wydawcy oferty
- Identyfikator jednostki SKU istniejących jednostek SKU
- Tagi wersji, na przykład: `1.0.1`
- Zmiany modelu rozliczeń/licencji na istniejące jednostki SKU

## <a name="common-update-operations"></a>Typowe operacje aktualizacji

Następujące operacje aktualizacji są wspólne.

### <a name="update-image-version-for-a-sku"></a>Aktualizacja wersji obrazu dla jednostki SKU

Jest to typowy obraz, który ma być okresowo aktualizowany za pomocą poprawek zabezpieczeń, dodatkowych funkcji i tak dalej. W tym scenariuszu należy zaktualizować obraz, do którego odwołuje się jednostka SKU, wykonując następujące czynności:

1. Zaloguj się do [Portal Cloud partner](https://cloudpartner.azure.com/).
2. W obszarze **wszystkie oferty**Znajdź ofertę, którą chcesz zaktualizować.
3. Na karcie **jednostki SKU** wybierz jednostkę SKU skojarzoną z obrazem do zaktualizowania.
4. Wybierz pozycję **+ Nowa wersja obrazu** , aby dodać nowy obraz.
5. Podaj nowe wersje obrazu. Wersja obrazu musi być zgodna z tymi samymi wskazówkami dotyczącymi tagów, co w poprzednich wersjach. Tagi wersji powinny mieć postać X. Y. Z, gdzie X, Y i Z są liczbami całkowitymi. Sprawdź, czy wprowadzona nowa wersja jest nowsza niż wszystkie poprzednie wersje.
6. Wybierz pozycję **Publikuj** , aby uruchomić przepływ pracy w celu opublikowania nowej wersji obrazu kontenera w witrynie Azure Marketplace.

### <a name="add-a-new-sku"></a>Dodaj nową jednostkę SKU

Wykonaj następujące kroki, aby udostępnić nową jednostkę SKU dla oferty:

1. Zaloguj się do [Portal Cloud partner](https://cloudpartner.azure.com/).
2. W obszarze **wszystkie oferty**Znajdź ofertę, którą chcesz zaktualizować.
3. Na karcie **jednostki SKU** wybierz pozycję **Dodaj nową jednostkę SKU** i podaj **Identyfikator jednostki SKU** w oknie podręcznym.
4. Opublikuj ponownie ofertę, korzystając z procedury opisanej w temacie [Publikowanie oferty aplikacji platformy Azure](./cpp-publish-offer.md).
5. Wybierz pozycję **Publikuj** , aby uruchomić przepływ pracy w celu opublikowania nowej jednostki SKU.

### <a name="update-offer-marketplace-metadata"></a>Aktualizowanie metadanych witryny Marketplace z ofertą

Wykonaj następujące kroki, aby zaktualizować metadane portalu Marketplace skojarzone z ofertą. (Na przykład: Nazwa firmy, logo itp.)

1. Zaloguj się do [Portal Cloud partner](https://cloudpartner.azure.com/).
2. W obszarze **wszystkie oferty**Znajdź ofertę, którą chcesz zaktualizować.
3. Przejdź do karty **Marketplace** . Aby wprowadzić zmiany metadanych, Skorzystaj z instrukcji w temacie [Publikowanie oferty aplikacji platformy Azure](./cpp-publish-offer.md) .
4. Wybierz pozycję **Publikuj** , aby uruchomić przepływ pracy w celu opublikowania zmian.
 
>[!Note]
>Niedostępność kanału partnera dostawcy rozwiązań w chmurze (CSP) jest teraz dostępna.  Zobacz [dostawcy rozwiązań w chmurze](../../cloud-solution-providers.md) , aby uzyskać więcej informacji na temat marketingu oferty przez kanały partnerskie programu Microsoft CSP.

## <a name="deleting-an-existing-offer"></a>Usuwanie istniejącej oferty

Możesz zdecydować się na usunięcie oferty z portalu Marketplace. Usunięcie oferty nie ma wpływu na bieżące zakupy tej oferty. Te zakupy klienta będą nadal działały tak jak wcześniej. Jednak oferta nie będzie dostępna dla nowych zakupów po zakończeniu usuwania.

Zakończenie oferty to proces zakończenia świadczenia usługi i/lub rozwiązania umowy licencjonowania między Tobą i Twoimi obecnymi klientami.
Wskazówki i zasady dotyczące usuwania i kończenia oferty podlegają postanowieniom umowy wydawcy Microsoft Marketplace (patrz sekcja 7) i zasadami uczestnictwa (patrz sekcja 6,2).

Aby uzyskać więcej informacji, zobacz sekcję [usuwanie i oferowanie/jednostka SKU z witryny Azure Marketplace](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-managed-app-offer-delete).

## <a name="compare-feature"></a>Porównaj funkcję

Po wprowadzeniu zmian w opublikowanej ofercie można użyć funkcji porównania, aby przeprowadzić inspekcję wprowadzonych zmian.

Aby użyć funkcji porównania:

1. W dowolnym momencie procesu edycji wybierz pozycję Porównaj z ofertą.
2. Zobacz obok siebie wersje zasobów i metadanych marketingowych.

## <a name="history-of-publishing-actions"></a>Historia akcji publikowania

Aby wyświetlić historyczne działanie publikowania, wybierz kartę **historia** na pasku menu nawigacji po lewej stronie Portal Cloud partner. Możesz zobaczyć działania z sygnaturami czasowymi podejmowane w okresie istnienia ofert portalu Azure Marketplace.

## <a name="next-steps"></a>Następne kroki

[Oferta aplikacji platformy Azure](./cpp-azure-app-offer.md)