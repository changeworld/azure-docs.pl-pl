---
title: Aktualizowanie istniejącej oferty aplikacji platformy Azure | Portal Azure Marketplace
description: Jak aktualizowanie istniejącej oferty aplikacji platformy Azure w witrynie Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: pabutler
ms.openlocfilehash: a36df757e3a2682af641101ed82583a0cd293e0a
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2019
ms.locfileid: "64942819"
---
# <a name="update-an-existing-azure-application-offer"></a>Aktualizowanie istniejącej oferty aplikacji platformy Azure

Istnieją różne rodzaje aktualizacje, które warto wykonać oferty po opublikowaniu i działa. Wszelkie zmiany wprowadzone do nowej wersji oferty należy zapisać i ponownie opublikować, aby odzwierciedlić w portalu Marketplace. Ten artykuł przeprowadza przez różne aspekty aktualizowanie oferty aplikacji zarządzanej [portalu Cloud Partner](https://cloudpartner.azure.com/).

Istnieje kilka powodów dlaczego warto zaktualizować oferty, takich jak:

- Dodawanie nowej wersji obrazu do istniejącej jednostki SKU.
- Dodawanie nowych jednostek SKU.
- Aktualizowanie metadanych portalu marketplace oferty lub poszczególne jednostki SKU.

Aby pomóc w tych zmian, portal zapewnia **porównania** i **historii** funkcji.

## <a name="unpermitted-changes-to-an-azure-application-offer-or-sku"></a>Niedozwolonej zmiany do skorzystania z oferty aplikacji platformy Azure lub jednostki SKU

Istnieją atrybuty oferty kontenera lub jednostki SKU, nie można jej zmienić po oferta jest dostępna na żywo w witrynie Azure Marketplace. Nie można zmienić następujące ustawienia:

- Identyfikator oferty i identyfikatorze wydawcy, oferty
- Identyfikator jednostki SKU istniejących jednostek SKU
- Wersja — tagi, na przykład: `1.0.1`
- Model rozliczeń/licencji zmian do istniejących jednostek SKU

## <a name="common-update-operations"></a>Typowe operacje aktualizacji

Następujące operacje aktualizacji są powszechne.

### <a name="update-image-version-for-a-sku"></a>Zaktualizuj wersję obrazu dla jednostki SKU

Bardzo często obraz, aby być okresowo aktualizowane za pomocą poprawek zabezpieczeń, dodatkowe funkcje i tak dalej. W tym scenariuszu chcesz zaktualizować obraz, który odwołuje się do danej jednostki SKU wykonując następujące kroki:

1. Zaloguj się do [portalu dla partnerów w chmurze](https://cloudpartner.azure.com/).
2. W obszarze **wszystkich ofert**, odnaleźć oferty, o których chcesz zaktualizować.
3. W **jednostki SKU** , a następnie wybierz jednostkę SKU skojarzonych z obrazem do zaktualizowania.
4. Wybierz **+ nowa wersja obrazu** można dodać nowego obrazu.
5. Podaj nowe wersje obrazu. Wersja obrazu musi postępuj zgodnie z tym samym wytycznym tagów w poprzednich wersjach. Wersja tagi powinny mieć postać X.Y.Z, gdzie X, Y i Z są liczbami całkowitymi. Sprawdź, czy nowa wersja, którą podasz jest większa niż wszystkie poprzednie wersje.
6. Wybierz **Publikuj** można uruchomić przepływu pracy, aby opublikować nową wersję obrazu kontenera w portalu Azure Marketplace.

### <a name="add-a-new-sku"></a>Dodawanie nowej jednostki SKU

Wykonaj następujące kroki, aby udostępnić nowej jednostki SKU oferty:

1. Zaloguj się do [portalu dla partnerów w chmurze](https://cloudpartner.azure.com/).
2. W obszarze **wszystkich ofert**, odnaleźć oferty, o których chcesz zaktualizować.
3. W obszarze **jednostki SKU** zaznacz **Dodawanie nowej jednostki SKU** i podaj **identyfikator jednostki SKU** w oknie podręcznym.
4. Ponownie opublikować ofertę, korzystając z procedury opisanej w [opublikować ofertę platformy Azure aplikacja](./cpp-publish-offer.md).
5. Wybierz **Publikuj** można uruchomić przepływu pracy, aby opublikować nowe jednostki SKU.

### <a name="update-offer-marketplace-metadata"></a>Zaktualizowanie metadanych portalu marketplace oferty

Wykonaj następujące kroki, aby zaktualizować metadane marketplace skojarzone z ofertą. (Na przykład: firmy nazwy, logo, itp.)

1. Zaloguj się do [portalu dla partnerów w chmurze](https://cloudpartner.azure.com/).
2. W obszarze **wszystkich ofert**, odnaleźć oferty, czy chcesz zaktualizować.
3. Przejdź do **Marketplace** kartę. Postępuj zgodnie z instrukcjami w [opublikować ofertę platformy Azure aplikacja](./cpp-publish-offer.md) zmian metadanych.
4. Wybierz **Publikuj** można uruchomić przepływu pracy, aby opublikować zmiany.
 
>[!Note]
>Dostawcy rozwiązań (CSP) partnera kanału zoptymalizowany pod kątem w chmurze jest teraz dostępna.  Zobacz [dostawców rozwiązań w chmurze](../../cloud-solution-providers.md) więcej informacji na temat marketingowych oferty za pośrednictwem programu Microsoft CSP partner kanałów.

## <a name="deleting-an-existing-offer"></a>Usuwanie istniejącej oferty

Użytkownik może zdecydować o usunięciu oferty z portalu Marketplace. Usuwanie oferty nie wpływa na bieżących zakupów tę ofertę. Tych zakupów klientów będą w dalszym ciągu działać tak jak poprzednio. Jednak oferty nie będą dostępne dla żadnych nowych zakupów po zakończeniu usuwania.

Zakończenie oferty to proces zakończenia świadczenia usługi i/lub rozwiązania umowy licencjonowania między Tobą i Twoimi obecnymi klientami.
Wskazówki i zasady dotyczące usuwania i kończenia oferty podlegają umowie wydawcy portalu Microsoft Marketplace (zobacz sekcję 7) i zasad uczestnictwa (patrz sekcja 6.2).

Aby uzyskać więcej informacji, zobacz [Delete i/jednostki SKU oferty z portalu Azure Marketplace](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-managed-app-offer-delete).

## <a name="compare-feature"></a>Porównanie funkcji

Po wprowadzeniu zmian do opublikowane oferty można użyć funkcji porównania inspekcji, które zostały wprowadzone zmiany.

Aby użyć funkcji porównania:

1. W dowolnym momencie procesu edycji wybierz Porównaj oferty.
2. Obejrzyj wersje side-by-side marketingu zasoby i metadanych.

## <a name="history-of-publishing-actions"></a>Historia akcji publikowania

Aby wyświetlić działań historycznych w publikacji, wybierz **historii** kartę na lewym pasku nawigacyjnym menu paska z portalu Cloud Partner. Możesz zobaczyć akcje oznaczony sygnaturą czasową wykonywane w okresie istnienia Twoich ofert w portalu Azure Marketplace.

## <a name="next-steps"></a>Kolejne kroki

[Oferta aplikacji platformy Azure](./cpp-azure-app-offer.md)