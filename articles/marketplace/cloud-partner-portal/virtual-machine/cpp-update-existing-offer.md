---
title: Aktualizacja istniejącej maszyny Wirtualnej oferty w portalu Azure Marketplace | Dokumentacja firmy Microsoft
description: Wyjaśnia, jak aktualizowanie istniejącej oferty maszyny Wirtualnej w portalu Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 08/27/2018
ms.author: Ankit.Sud
ms.openlocfilehash: 254215389d129874f7ac3fc855b26819d41a095c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60343197"
---
# <a name="update-an-existing-vm-offer-on-azure-marketplace"></a>Aktualizowanie istniejącej oferty maszyny Wirtualnej w portalu Azure Marketplace

Ten artykuł przeprowadzi Cię przez różne aspekty aktualizowanie oferty maszyny wirtualnej (VM) [portalu Cloud Partner](https://cloudpartner.azure.com/) i ponowne opublikowanie oferty. 

Istnieje wiele możliwych przyczyn powszechnie używane, należy zaktualizować swoje oferty w tym:

-  Dodawanie nowej wersji obrazu maszyny Wirtualnej do istniejących jednostek SKU
-  Regiony zmiany jednostki SKU jest dostępna
-  Dodaj nowe jednostki SKU
-  Aktualizacji metadanych portalu marketplace oferty lub poszczególne jednostki SKU
-  Aktualizuj cennik oferty zgodnie z rzeczywistym użyciem

Aby pomóc w tych zmian, oferuje portalu **porównania** i **historii** funkcji.  

>[!Note]
>Dostawcy rozwiązań (CSP) partnera kanału zoptymalizowany pod kątem w chmurze jest teraz dostępna.  Zobacz [dostawców rozwiązań w chmurze](../../cloud-solution-providers.md) więcej informacji na temat marketingowych oferty za pośrednictwem programu Microsoft CSP partner kanałów.

## <a name="unpermitted-changes-to-vm-offer-or-sku"></a>Niedozwolonej zmiany oferty maszyny Wirtualnej lub jednostki SKU

Istnieją pewne atrybuty oferty maszyny Wirtualnej lub jednostki SKU, którego nie można zmodyfikować po oferta jest dostępna na żywo w witrynie Azure Marketplace głównie:

-  **Identyfikator oferty** i **Identyfikatora wydawcy** oferty
-  **Identyfikator jednostki SKU** istniejących jednostek SKU
-  Liczba istniejących jednostek SKU na dysku danych
-  Model rozliczeń/licencji zmian do istniejących jednostek SKU
-  Cena wzrasta do opublikowane jednostki SKU


## <a name="common-update-operations"></a>Typowe operacje aktualizacji

Chociaż istnieje szereg właściwości, które można zmienić w ramach oferty maszyny Wirtualnej, następujące operacje są wspólne.

### <a name="update-the-vm-image-version-for-a-sku"></a>Zaktualizuj wersję obrazu maszyny Wirtualnej dla jednostki SKU

Jest to typowe dla obrazu maszyny Wirtualnej być okresowo aktualizowane za pomocą poprawek zabezpieczeń, dodatkowe funkcje i tak dalej.  W takich przypadkach chcesz zaktualizować obrazu maszyny Wirtualnej, który odwołuje się do danej jednostki SKU wykonując następujące kroki:

1.  Zaloguj się do [portalu dla partnerów w chmurze](https://cloudpartner.azure.com/).

2.  W obszarze **wszystkich ofert**, Znajdź ofertę zaktualizowania.

3.  W **jednostki SKU** kliknij w ramach jednostki SKU skojarzone z obrazem maszyny Wirtualnej do zaktualizowania.

4.  W obszarze **wersje dyskowe**, kliknij pozycję **+ nowa wersja dysku** można dodać nowego obrazu maszyny Wirtualnej.

5.  Podaj nowe obrazy maszyn wirtualnych **wersje dyskowe**. Wersje dyskowe musi wykonać [wersji semantycznej](https://semver.org/) formatu. Wersje powinny być w postaci X.Y.Z, gdzie X, Y i Z są liczbami całkowitymi. Sprawdź, czy nowa wersja, którą podasz jest większa niż wszystkie poprzednie wersje; w przeciwnym razie po ponowne publikowanie nowej wersji nie będą wyświetlane na portalu lub w portalu Azure Marketplace.

6.  Aby uzyskać **adres URL wirtualnego dysku twardego systemu operacyjnego**, wprowadź [sygnatury dostępu współdzielonego (SAS) identyfikator URI](./cpp-get-sas-uri.md) utworzony dla wirtualnego dysku twardego systemu operacyjnego. 

    > [!WARNING] 
    > Liczba dysków danych nie można zmienić między różnymi wersjami jednostki SKU. Jeśli poprzednie wersje oferowały dyski danych skonfigurowane, nowa wersja również musi mieć taką samą liczbę dysków z danymi.

7.  Kliknij pozycję **Publikuj** można uruchomić przepływu pracy, aby opublikować nową wersję maszyny Wirtualnej w portalu Azure Marketplace.


### <a name="change-region-availability-of-a-sku"></a>Zmień dostępność regionów dla jednostki SKU

Wraz z upływem czasu można udostępnić ofertę/jednostkę SKU w większej liczbie regionów.  Alternatywnie możesz polegająca na wyłączeniu obsługi oferty/jednostki SKU w danym regionie.
Aby zmodyfikować dostępności, użyj następujących kroków:

1.  Zaloguj się do [portalu dla partnerów w chmurze](https://cloudpartner.azure.com/).

2.  W obszarze **wszystkich ofert** odnaleźć oferty, czy chcesz zaktualizować.

3.  W **jednostki SKU** kliknij jednostki SKU, którą chcesz zmodyfikować jego dostępność.

4.  Kliknij pozycję **Wybierz kraje** przycisku w obszarze **dostępności Kraj/Region** pola.

5.  W podręcznym dostępność regionów Dodawanie lub usuwanie regionów dla tej jednostki SKU.

6.  Kliknij pozycję **Publikuj** można uruchomić przepływu pracy publikowania, aby zaktualizować swoje dostępność w poszczególnych regionach jednostki SKU.

Jeśli jednostka SKU jest wykonywane w nowym regionie, będzie miał możliwość określenia ceny dla tego konkretnego regionu za pośrednictwem **Eksport danych ceny** funkcji. Jeśli dodajesz ponownie region, który był po jego udostępnieniu przed nie można zaktualizować, cennik, ponieważ zmianie cen nie są dozwolone.


### <a name="add-a-new-sku"></a>Dodawanie nowej jednostki SKU

Wykonaj następujące kroki, aby udostępnić istniejące oferty nowej jednostki SKU: 

1.  Zaloguj się do [portalu dla partnerów w chmurze](https://cloudpartner.azure.com/).

2.  W obszarze **wszystkich ofert** odnaleźć oferty, czy chcesz zaktualizować.

3.  W obszarze **jednostki SKU** kartę, kliknij pozycję **Dodawanie nowej jednostki SKU** i podaj **identyfikator jednostki SKU** w oknie podręcznym.

4.  Publikuj ponownie maszyny Wirtualnej zgodnie z opisem w artykule [publikować maszynę wirtualną w portalu Azure Marketplace](./cpp-publish-offer.md).

5.  Kliknij pozycję **Publikuj** można uruchomić przepływu pracy, aby opublikować nowe jednostki SKU.


### <a name="update-offer-marketplace-metadata"></a>Zaktualizowanie metadanych portalu marketplace oferty

Wykonaj następujące kroki w celu zaktualizowania metadanych marketplace — firmy, nazwy, logo, itd. — skojarzone z ofertą: 

1.  Zaloguj się do [portalu dla partnerów w chmurze](https://cloudpartner.azure.com/).

2.  W obszarze **wszystkich ofert** odnaleźć oferty, czy chcesz zaktualizować.

3.  Przejdź do **Marketplace** kartę, a następnie postępuj zgodnie z instrukcjami w artykule [publikować maszynę wirtualną w portalu Azure Marketplace](./cpp-publish-offer.md) zmian metadanych.

4.  Kliknij pozycję **Publikuj** można uruchomić przepływu pracy, aby opublikować zmiany.


### <a name="update-pricing-on-published-offers"></a>Aktualizacja cen na opublikowane oferty

Po opublikowaniu oferty zgodnie z rzeczywistym użyciem nie może bezpośrednio zwiększyć cena jednostki SKU.  (Jednak może Tworzenie nowej jednostki SKU w ramach tej samej oferty, usuwanie starych jednostek SKU i ponownie opublikować ofertę dla nowych klientów.)  Z kolei może obniżyć cenę opublikowane oferty, wykonując następujące czynności:

1.  Zaloguj się do [portalu dla partnerów w chmurze](https://cloudpartner.azure.com/).

2.  W obszarze **wszystkich ofert**, Znajdź ofertę zaktualizowania.

3.  Kliknij jednostkę SKU, dla którego chcesz zmniejszyć ceny.

4.  Jeśli ustawiono cenie w 1 x 1 graficznego interfejsu użytkownika, można zmienić cena bezpośrednio w interfejsie użytkownika. Jeśli ustawisz ceny za pośrednictwem importu/eksportu arkusza kalkulacyjnego, można zmniejszyć tylko ceny za pomocą funkcji importowania/eksportowania.

3.  Kliknij pozycję **Zapisz**.

4.  Kliknij pozycję **Publikuj** można uruchomić przepływu pracy, aby opublikować zmiany.

Nowe ceny pogorszyła będą widoczne dla nowych klientów po na żywo w witrynie sieci Web.  To nowa cena będzie mieć wpływ na klientów w następujący sposób:

- Nowi klienci naliczana to nowa stawka. 
- W przypadku istniejących klientów spadek cen zostaną odzwierciedlone wstecznie na początku okresu rozliczeniowego, w którym się skuteczne spadek cen.
Jeśli zostały one już zafakturowane cykl, w którym wystąpił spadek, otrzymają zwrot pieniędzy podczas następnego cyklu rozliczeniowego dla pokrycia obniżonej cenie.


<!-- TD: This has been implemented, need to change the SKU Tab topic to reflect and move this section there. -->
### <a name="simplified-currency-pricing"></a>Cennik uproszczone waluty

Począwszy od września 2018 r. 1, nową sekcję o nazwie **uproszczony ceny waluty** zostanie dodany do portalu. Microsoft jest usprawnienie działalności w portalu Azure Marketplace, należy włączyć bardziej przewidywalne ceny i kolekcje z klientami na całym świecie. To usprawnienie zostaną uwzględnione w zmniejszeniu liczby walut, w których firma Microsoft faktury dla odbiorców.

Nowa sekcja podejmie, ceny w tych nowych walutach. Po przeprowadzeniu migracji wszystkich klientów do tych nowych walutach rozliczenia oryginalnego sekcji zostaną wycofane, a pozostanie tylko sekcję uproszczony ceny waluty.

Będziesz mieć do momentu 1 listopada 2018 można ustawić nowej ceny dla regionów, polegającego na tym, Waluta rozliczenia ulega zmianie. Nie można zwiększyć ceny dla regionów, w którym nie ulega zmianie waluty rozliczenia.

> [!NOTE] 
> Jeśli publikowanie oferty przy użyciu interfejsów API, mogą pojawić się nowa sekcja w ramach oferty w formacie JSON. To może być oznaczona jako `virtualMachinePricingV2` lub `monthlyPricingV2`, w zależności od typu oferty. 

Jeśli masz jakieś pytania dotyczące tej zmiany, skontaktuj się z pomocą [pomocy technicznej systemu Azure Marketplace](../../support-azure-marketplace.md).


## <a name="compare-feature"></a>Porównanie funkcji

Po wprowadzeniu zmian w już opublikowane oferty można wykorzystać **porównania** funkcji inspekcji zmian, które zostały wprowadzone. Aby użyć tej funkcji:

1.  W dowolnym momencie procesu edycji, kliknij przycisk **porównania** przycisk oferty.

    ![Porównanie funkcji przycisku](./media/publishvm_037.png)


2.  Wyświetl wersje side-by-side marketingu zasoby i metadanych.


## <a name="history-of-publishing-actions"></a>Historia akcji publikowania

Aby wyświetlić wszystkie działań historycznych w publikacji, kliknij **historii** elementu na pasku menu nawigacji po lewej stronie portalu Cloud Partner. W tym miejscu można wyświetlić oznaczony sygnaturą czasową akcje, które zostały podjęte w okresie istnienia Twoich ofert w portalu Azure Marketplace.  
<!-- TD: Add after section authored: For more information, see [History page](../portal-tour/cpp-history-page.md). -->

