---
title: Aktualizowanie istniejącej oferty aplikacji platformy Azure | Azure Marketplace
description: Jak zaktualizować istniejącą ofertę aplikacji platformy Azure w portalu Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: dsindona
ms.openlocfilehash: 152fd24fbc5d2762d381ffce2a937bc448858b0a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275955"
---
# <a name="update-an-existing-azure-application-offer"></a>Aktualizowanie istniejącej oferty aplikacji platformy Azure

Istnieją różne rodzaje aktualizacji, które warto zrobić z ofertą po jej opublikowaniu i jest na żywo. Wszelkie zmiany w nowej wersji oferty powinny zostać zapisane i ponownie opublikowane, aby odzwierciedlała ją w Marketplace. W tym artykule ostępuje kilka kroków od różnych aspektów aktualizowania oferty zarządzanych aplikacji w [portalu cloud partner.](https://cloudpartner.azure.com/)

Istnieje kilka powodów, dla których warto zaktualizować ofertę, takich jak:

- Dodawanie nowej wersji obrazu do istniejących jednostek SKU.
- Dodawanie nowych jednostek SKU.
- Aktualizowanie metadanych portalu marketplace dla oferty lub poszczególnych jednostek SKU.

Aby ułatwić te modyfikacje, portal udostępnia funkcje **Porównania** i **historii.**

## <a name="unpermitted-changes-to-an-azure-application-offer-or-sku"></a>Niedozwolone zmiany w ofercie aplikacji platformy Azure lub jednostce SKU

Istnieją atrybuty oferty kontenera lub jednostki SKU, których nie można zmienić po uruchomieniu oferty w portalu Azure Marketplace. Nie można zmienić następujących ustawień:

- Identyfikator oferty i identyfikator wydawcy oferty
- Identyfikator jednostki SKU istniejących jednostek SKU
- Tagi wersji, na przykład:`1.0.1`
- Zmiany modelu rozliczeń/licencji na istniejących jednostkach SKU

## <a name="common-update-operations"></a>Typowe operacje aktualizacji

Następujące operacje aktualizacji są wspólne.

### <a name="update-image-version-for-a-sku"></a>Aktualizowanie wersji obrazu dla jednostki SKU

Często obraz jest okresowo aktualizowany za pomocą poprawek zabezpieczeń, dodatkowych funkcji i tak dalej. W tym scenariuszu chcesz zaktualizować obraz, do którego odwołuje się jednostka SKU, wykonując następujące kroki:

1. Zaloguj się do [portalu cloud partner](https://cloudpartner.azure.com/).
2. W obszarze **Wszystkie oferty**znajdź ofertę, którą chcesz zaktualizować.
3. Na karcie **Jednostki SKU** wybierz jednostkę SKU skojarzoną z obrazem do zaktualizowania.
4. Wybierz **+ Nowa wersja obrazu,** aby dodać nowy obraz.
5. Podaj nowe wersje obrazów. Wersja obrazu musi być zgodna z tymi samymi wytycznymi dotyczącymi znaczników, co poprzednie wersje. Tagi wersji powinny mieć postać X.Y.Z, gdzie X, Y i Z są liczbami całkowitymi. Sprawdź, czy nowa wersja, którą podasz, jest większa niż wszystkie poprzednie wersje.
6. Wybierz **pozycję Publikuj,** aby uruchomić przepływ pracy, aby opublikować nową wersję obrazu kontenera w portalu Azure Marketplace.

### <a name="add-a-new-sku"></a>Dodawanie nowej jednostki SKU

Aby udostępnić nową jednostkę SKU dla swojej oferty, użyj następujących czynności:

1. Zaloguj się do [portalu cloud partner](https://cloudpartner.azure.com/).
2. W obszarze **Wszystkie oferty**znajdź ofertę, którą chcesz zaktualizować.
3. Na karcie **Jednostki SKU** wybierz pozycję **Dodaj nową jednostkę SKU** i podaj identyfikator **jednostki SKU** w wyskakującym oknie.
4. Ponownie opublikuj ofertę, wykonując kroki opisane w [aplikacji Publikowania na platformie Azure.](./cpp-publish-offer.md)
5. Wybierz **pozycję Publikuj,** aby uruchomić przepływ pracy, aby opublikować nową jednostkę SKU.

### <a name="update-offer-marketplace-metadata"></a>Aktualizowanie metadanych portalu marketplace ofert

Aby zaktualizować metadane portalu marketplace skojarzone z ofertą, należy wykonać następujące czynności. (Na przykład: nazwa firmy, logo itp.)

1. Zaloguj się do [portalu cloud partner](https://cloudpartner.azure.com/).
2. W obszarze **Wszystkie oferty**znajdziesz ofertę, którą chcesz zaktualizować.
3. Przejdź do karty **Marketplace.** Użyj instrukcji w [aplikacji Publikowania na platformie Azure,](./cpp-publish-offer.md) aby wprowadzić zmiany metadanych.
4. Wybierz **pozycję Publikuj,** aby uruchomić przepływ pracy, aby opublikować zmiany.
 
>[!Note]
>Usługa wyboru kanału partnerskiego dostawców rozwiązań w chmurze (CSP) jest już dostępna.  Zobacz [dostawców rozwiązań w chmurze, aby](../../cloud-solution-providers.md) uzyskać więcej informacji na temat marketingu oferty za pośrednictwem kanałów partnerów CSP firmy Microsoft.

## <a name="deleting-an-existing-offer"></a>Usuwanie istniejącej oferty

Możesz usunąć swoją ofertę z Marketplace. Usunięcie oferty nie ma wpływu na bieżące zakupy tej oferty. Te zakupy klientów będą nadal działać tak jak poprzednio. Jednakże, oferta nie będzie dostępna dla nowych zakupów po zakończeniu usuwania.

Rozwiązanie oferty to proces rozwiązania umowy o świadczenie usług i/lub licencji między Użytkownikiem a państwa obecnymi klientami.
Wskazówki i zasady związane z usuwaniem i rozwiązaniem oferty podlegają umowie wydawcy witryny Microsoft Marketplace (patrz sekcja 7) i zasadom uczestnictwa (patrz punkt 6.2).

Aby uzyskać więcej informacji, zobacz [Usuwanie i oferuj/SKU z portalu Azure Marketplace](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-managed-app-offer-delete).

## <a name="compare-feature"></a>Funkcja Porównywania

Po wprowadzeniu zmian w opublikowanej ofercie można użyć funkcji Porównaj do inspekcji wprowadzonych zmian.

Aby użyć funkcji Porównaj:

1. W dowolnym momencie procesu edycji wybierz pozycję Porównaj dla swojej oferty.
2. Przyjrzyj się wersjom zasobów marketingowych i metadanych obok siebie.

## <a name="history-of-publishing-actions"></a>Historia działań wydawniczych

Aby wyświetlić historyczne działania publikowania, wybierz kartę **Historia** na pasku menu nawigacji po lewej stronie portalu Cloud Partner Portal. Możesz zobaczyć akcje sygnatury czasowej podjęte w okresie istnienia oferty portalu Azure Marketplace.

## <a name="next-steps"></a>Następne kroki

[Oferta aplikacji platformy Azure](./cpp-azure-app-offer.md)
