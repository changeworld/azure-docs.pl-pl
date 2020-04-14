---
title: Aktualizowanie istniejącej oferty maszyn wirtualnych w portalu Azure Marketplace
description: W tym artykule wyjaśniono, jak zaktualizować istniejącą ofertę maszyn wirtualnych w portalu Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/27/2018
ms.author: dsindona
ms.openlocfilehash: a15ccb1de2a9ce0072d032e624ead3b4d730763b
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273056"
---
# <a name="update-an-existing-vm-offer-on-azure-marketplace"></a>Aktualizowanie istniejącej oferty maszyn wirtualnych w portalu Azure Marketplace

> [!IMPORTANT]
> Od 13 kwietnia 2020 r. rozpoczniemy przenoszenie zarządzania ofertami maszyny wirtualnej platformy Azure do centrum partnerskiego. Po migracji utworzysz oferty i zarządzasz nimi w Centrum partnerów. Postępuj zgodnie z instrukcjami w [tworzenie oferty maszyny wirtualnej platformy Azure,](https://aka.ms/CreateAzureVMoffer) aby zarządzać zmigrowanymi ofertami.

W tym artykule oproszą Cię o różne aspekty aktualizowania oferty maszyny wirtualnej w portalu partnerów w [chmurze,](https://cloudpartner.azure.com/) a następnie ponowne publikowanie oferty. 

Istnieje wiele powszechnych powodów, dla których warto zaktualizować ofertę, w tym:

-  Dodawanie nowej wersji obrazu maszyny Wirtualnej do istniejących jednostek SKU
-  Zmienianie regionów, w które dostępna jest jednostka SKU
-  Dodawanie nowych jednostek SKU
-  Aktualizowanie metadanych portalu marketplace dla oferty lub poszczególnych jednostek SKU
-  Aktualizowanie cen ofert płatności zgodnie z rzeczywistym użyciem

Aby pomóc w tych modyfikacjach, portal oferuje funkcje **Porównaj** i **Historia.**  

>[!Note]
>Usługa wyboru kanału partnerskiego dostawców rozwiązań w chmurze (CSP) jest już dostępna.  Zobacz [dostawców rozwiązań w chmurze, aby](../../cloud-solution-providers.md) uzyskać więcej informacji na temat marketingu oferty za pośrednictwem kanałów partnerów CSP firmy Microsoft.

## <a name="unpermitted-changes-to-vm-offer-or-sku"></a>Niedozwolone zmiany w ofercie maszyny Wirtualnej lub jednostce SKU

Istnieją pewne atrybuty oferty maszyny Wirtualnej lub jednostki SKU, które nie mogą być modyfikowane, gdy oferta jest dostępna w portalu Azure Marketplace, głównie:

-  **Identyfikator oferty** i **identyfikator wydawcy** oferty
-  **Identyfikator jednostki SKU** istniejących jednostek SKU
-  Liczba dysków danych istniejących jednostek SKU
-  Zmiany modelu rozliczeń/licencji na istniejących jednostkach SKU
-  Wzrost cen do opublikowanej jednostki SKU


## <a name="common-update-operations"></a>Typowe operacje aktualizacji

Chociaż istnieje szeroki zakres cech, które można zmienić w ofercie maszyny Wirtualnej, następujące operacje są wspólne.

### <a name="update-the-vm-image-version-for-a-sku"></a>Aktualizowanie wersji obrazu maszyny Wirtualnej dla jednostki SKU

Często jest dla obrazu maszyny Wirtualnej, które mają być okresowo aktualizowane za pomocą poprawek zabezpieczeń, dodatkowe funkcje i tak dalej.  W takich scenariuszach chcesz zaktualizować obraz maszyny Wirtualnej, do którego odwołuje się jednostka SKU, wykonując następujące kroki:

1.  Zaloguj się do [portalu cloud partner](https://cloudpartner.azure.com/).

2.  W obszarze **Wszystkie oferty**znajdziesz ofertę do aktualizacji.

3.  Na karcie **Jednostki SKU** kliknij jednostkę SKU skojarzoną z obrazem maszyny Wirtualnej, aby ją zaktualizować.

4.  W **obszarze Wersja dysku**kliknij **+Nowa wersja dysku,** aby dodać nowy obraz maszyny Wirtualnej.

5.  Podaj nową wersję dysku obrazów **maszyn wirtualnych**. Wersja dysku musi być zgodna z [formatem wersji semantycznej.](https://semver.org/) Wersje powinny mieć postać X.Y.Z, gdzie X, Y i Z są liczbami całkowitymi. Sprawdź, czy nowa wersja, którą podasz, jest większa niż wszystkie poprzednie wersje; w przeciwnym razie po ponownym opublikowaniu nowej wersji nie będzie wyświetlany w portalu lub portalu Azure Marketplace.

6.  W przypadku **adresu URL VHD systemu operacyjnego**wprowadź identyfikator [URI sygnatury dostępu współdzielonego (SAS)](./cpp-get-sas-uri.md) utworzony dla vhd systemu operacyjnego. 

    > [!WARNING] 
    > Liczba dysków danych nie może się zmieniać między różnymi wersjami jednostki SKU. Jeśli w poprzednich wersjach skonfigurowano dyski z danymi, ta nowa wersja musi mieć taką samą liczbę dysków z danymi.

7.  Kliknij **przycisk Publikuj,** aby uruchomić przepływ pracy, aby opublikować nową wersję maszyny Wirtualnej w portalu Azure Marketplace.


### <a name="change-region-availability-of-a-sku"></a>Zmienianie dostępności regionu jednostki SKU

Z biegiem czasu możesz udostępnić swoją ofertę/jednostkę SKU w większej liczbie regionów.  Alternatywnie można zatrzymać obsługę oferty/jednostki SKU w danym regionie.
Aby zmodyfikować dostępność, należy wykonać następujące czynności:

1.  Zaloguj się do [portalu cloud partner](https://cloudpartner.azure.com/).

2.  W obszarze **Wszystkie oferty** znajdź ofertę, którą chcesz zaktualizować.

3.  Na karcie **Jednostki SKU** kliknij jednostkę SKU, która ma zmodyfikować jej dostępność.

4.  Kliknij przycisk **Wybierz kraje** w polu Dostępność **kraju/regionu.**

5.  W wyskakującym okienku dostępności regionu dodaj lub usuń regiony tej jednostki SKU.

6.  Kliknij **przycisk Publikuj,** aby uruchomić przepływ pracy publikowania, aby zaktualizować dostępność regionu jednostek SKU.

Jeśli jednostka SKU jest udostępniana w nowym regionie, można określić ceny dla danego regionu za pomocą funkcji **Eksportuj dane cen.** Jeśli dodajesz region z powrotem, który był kiedyś dostępny, nie będzie można zaktualizować jego cen, ponieważ zmiany cen nie są dozwolone.


### <a name="add-a-new-sku"></a>Dodawanie nowej jednostki SKU

Aby udostępnić nową jednostkę SKU dla istniejącej oferty, należy wykonać następujące czynności: 

1.  Zaloguj się do [portalu cloud partner](https://cloudpartner.azure.com/).

2.  W obszarze **Wszystkie oferty** znajdź ofertę, którą chcesz zaktualizować.

3.  Na karcie **Jednostki SKU** kliknij pozycję **Dodaj nową jednostkę SKU** i podaj **identyfikator jednostki SKU** w wyskakującym okienku.

4.  Ponownie opublikuj maszynę wirtualną, jak opisano w artykule [Publikowanie maszyny wirtualnej w portalu Azure Marketplace](./cpp-publish-offer.md).

5.  Kliknij **przycisk Publikuj,** aby uruchomić przepływ pracy, aby opublikować nową jednostkę SKU.


### <a name="update-offer-marketplace-metadata"></a>Aktualizowanie metadanych portalu marketplace ofert

Aby zaktualizować metadane portalu marketplace — nazwę firmy, logo itp.— skojarzone z ofertą: 

1.  Zaloguj się do [portalu cloud partner](https://cloudpartner.azure.com/).

2.  W obszarze **Wszystkie oferty** znajdź ofertę, którą chcesz zaktualizować.

3.  Goto **marketplace** kartę następnie postępuj zgodnie z instrukcjami w artykule [Publikowania maszyny wirtualnej w portalu Azure Marketplace,](./cpp-publish-offer.md) aby wprowadzić zmiany metadanych.

4.  Kliknij **przycisk Publikuj,** aby uruchomić przepływ pracy, aby opublikować zmiany.


### <a name="update-pricing-on-published-offers"></a>Aktualizuj ceny w opublikowanych ofertach

Po opublikowaniu oferty płatności zgodnie z rzeczywistym użyciem nie można bezpośrednio zwiększyć cen jednostki SKU.  (Można jednak utworzyć nową jednostkę SKU w ramach tej samej oferty, usunąć starą jednostkę SKU, a następnie ponownie opublikować ofertę dla nowych klientów).  W przeciwieństwie do tego można obniżyć cenę opublikowanej oferty, wykonując następujące czynności:

1.  Zaloguj się do [portalu cloud partner](https://cloudpartner.azure.com/).

2.  W obszarze **Wszystkie oferty**znajdziesz ofertę do aktualizacji.

3.  Kliknij jednostkę SKU, dla której chcesz obniżyć ceny.

4.  Jeśli ustawisz ceny w graficznym interfejsie użytkownika 1x1, możesz zmienić cenę bezpośrednio w interfejsie użytkownika. Jeśli ustawisz ceny za pomocą arkusza kalkulacyjnego import/eksport, możesz obniżyć ceny tylko za pośrednictwem funkcji importu/eksportu.

3.  Kliknij przycisk **Zapisz**.

4.  Kliknij **przycisk Publikuj,** aby uruchomić przepływ pracy, aby opublikować zmiany.

Nowe obniżone ceny będą widoczne dla nowych klientów po jego uruchomieniu na stronie internetowej.  Ta nowa cena wpłynie na klientów w następujący sposób:

- Nowi klienci zostaną obciążeni tą nową stawką. 
- W przypadku istniejących klientów spadek cen zostanie odzwierciedlony z mocą wsteczną do początku cyklu rozliczeniowego, w trakcie którego spadek cen stał się skuteczny.
Jeśli zostały już naliczone za cykl, w którym nastąpił spadek cen, otrzymają zwrot pieniędzy podczas następnego cyklu rozliczeniowego na pokrycie obniżonej ceny.


<!-- TD: This has been implemented, need to change the SKU Tab topic to reflect and move this section there. -->
### <a name="simplified-currency-pricing"></a>Uproszczone ceny walut

Od 1 września 2018 r. do portalu zostanie dodana nowa sekcja o nazwie **Uproszczone ceny walut.** Firma Microsoft usprawnia działalność portalu Azure Marketplace, umożliwiając bardziej przewidywalne ceny i kolekcje od klientów z całego świata. Usprawnienie to będzie obejmować zmniejszenie liczby walut, w których wystawiamy faktury klientom.

Nowa sekcja zajmie ceny w tych nowych walutach.Po migracji wszystkich klientów do tych nowych walut rozliczeniowych oryginalna sekcja cen zostanie wycofana i pozostanie tylko sekcja Uproszczona cena waluty.

Będziesz mieć czas do 1 listopada 2018 r., aby ustalić nową cenę dla regionów, w których waluta rozliczeniowa się zmienia. Nie będzie można podnieść ceny dla regionów, w których waluta rozliczeniowa nie zmienia się.

> [!NOTE] 
> Jeśli używasz interfejsów API do publikowania oferty, możesz zobaczyć nową sekcję w ofercie JSON. Byłoby to adnotacją `virtualMachinePricingV2` `monthlyPricingV2`jako lub , w zależności od rodzaju oferty. 

Jeśli masz jakieś pytania dotyczące tej zmiany, skontaktuj się z [pomocą techniczną portalu Azure Marketplace](../../support-azure-marketplace.md).


## <a name="compare-feature"></a>Porównaj funkcję

Po wprowadzeniu zmian w już opublikowanej ofercie można wykorzystać funkcję **Porównaj** do inspekcji wprowadzonych zmian. Aby użyć tej funkcji:

1.  W dowolnym momencie procesu edycji kliknij przycisk **Porównaj** dla swojej oferty.

    ![Przycisk Porównaj funkcję](./media/publishvm_037.png)


2.  Wyświetlanie wersji obok siebie zasobów marketingowych i metadanych.


## <a name="history-of-publishing-actions"></a>Historia akcji wydawniczych

Aby wyświetlić wszelkie historyczne działania publikowania, kliknij element **Historia** w lewym pasku menu nawigacji w portalu Cloud Partner. W tym miejscu będzie można wyświetlić akcje sygnatury czasowej, które zostały podjęte w okresie istnienia oferty portalu Azure Marketplace.  
<!-- TD: Add after section authored: For more information, see [History page](../portal-tour/cpp-history-page.md). -->

