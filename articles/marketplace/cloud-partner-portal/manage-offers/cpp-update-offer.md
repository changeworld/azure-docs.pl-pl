---
title: Aktualizowanie ofert marketplace | Azure Marketplace
description: Aktualizowanie ofert na platformie Azure i w witrynach AppSource Marketplaces przy użyciu portalu cloud partnerów
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: dsindona
ms.openlocfilehash: 101369a050770be3acd9534cef6229037fe1c366
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288516"
---
# <a name="update-azure-marketplace-and-appsource-offers"></a>Aktualizowanie ofert portalu Azure Marketplace i appsource

Istnieją różne rodzaje aktualizacji, które można zastosować do oferty po jej opublikowaniu.  [Portal cloud partner](https://cloudpartner.azure.com/) pomaga w prawidłowym modyfikowaniu atrybutów oferty, w tym:

-  Dodawanie nowego obrazu maszyny wirtualnej (VM) obrazu lub wersji pakietu do istniejącej jednostki SKU
-  Zmienianie regionów, w
-  Dodawanie nowych jednostek SKU
-  Aktualizowanie metadanych portalu marketplace dla ofert lub jednostek SKU 
-  Aktualizowanie cen w ofertach płatności zgodnie z rzeczywistym użyciem

Portal posiada również funkcje, takie jak możliwość porównywania funkcji i przeglądania historii funkcji oferty, które pomagają w zarządzaniu zmianami.  Po zmodyfikowaniu oferty lub jednostki SKU musi ona zostać ponownie opublikowana, zanim zmiany zostaną wprowadzone na "żywo".  W tym artykule otrzymasz od innych aspektów aktualizowania oferty w portalu marketplace.

## <a name="unpermitted-changes-to-an-offersku"></a>Niedozwolone zmiany w ofercie/jednostce SKU

Istnieją pewne atrybuty oferty lub jednostki SKU, których nie można zmodyfikować po opublikowaniu w portalu Marketplace.  Odpowiednie pola są wyłączone na karcie **Edytor** portalu, na przykład:  

- Identyfikator oferty i identyfikator wydawcy
- Identyfikator jednostki SKU 
- Liczba dysków danych istniejących jednostek SKU
- Zmiany modelu rozliczeń/licencji istniejących jednostek SKU
- Tagi wersji, na przykład:`1.0.1`


## <a name="common-update-operations"></a>Typowe operacje aktualizacji

W poniższych sekcjach wyjaśniono, jak wykonać niektóre z najbardziej operacji aktualizacji.  Te operacje nie są dostępne dla wszystkich typów ofert.  Aby rozpocząć dowolną z tych operacji, należy zalogować się do portalu cloud partner.


### <a name="update-offer-contacts"></a>Aktualizowanie kontaktów oferty

Aby zaktualizować kontakty pomocy technicznej dla oferty, należy wykonać następujące czynności.
1. Na stronie **Wszystkie oferty** wybierz ofertę.
2. Wybierz kartę **Kontakty.** Zaktualizuj kontakty.
3. Wybierz ikonę **Zapisz**.
4. Wybierz **pozycję Publikuj,** aby rozpocząć proces publikowania.


### <a name="change-regions-an-offer-or-sku-is-available-in"></a>Zmienianie regionów, w które jest dostępna oferta lub jednostka SKU

Z biegiem czasu możesz udostępnić swoją ofertę/jednostkę SKU w większej liczbie regionów.
Alternatywnie można zatrzymać obsługę oferty/jednostki SKU w danym regionie.
Aby zaimplementować te zmiany, wykonaj następujące kroki.

1. Na stronie **Wszystkie oferty** znajdź ofertę, którą chcesz zaktualizować.

W przypadku usług Azure Marketplace oferuje:

1. Wybierz kartę **Jednostki SKU.**  Wybierz jednostkę SKU do zmodyfikowania.
1. Kliknij przycisk **Wybierz kraje** w polu **Dostępność kraju/regionu.**
1. W oknie dialogowym dostępności regionu dodaj lub usuń regiony tej jednostki SKU.

W przypadku ofert AppSource:

1. Wybierz kartę **Szczegóły sklepu.**
1. Obok etykiety **Obsługiwane kraje/regiony** kliknij pozycję **Obsługiwane kraje/regiony**. 
1. W oknie dialogowym Obsługiwane kraje/regiony dodaj lub usuń regiony dla tej oferty.

Dla obu rynków:

1. Kliknij **przycisk Publikuj,** aby rozpocząć proces publikowania. 

Jeśli jednostka SKU jest udostępniana w nowym regionie, można określić ceny dla danego regionu za pomocą funkcji **Eksportuj dane cen.** Jeśli dodajesz region z powrotem, który był wcześniej dostępny, nie można zaktualizować jego cen, ponieważ zmiany cen nie są dozwolone.


### <a name="add-a-new-sku"></a>Dodawanie nowej jednostki SKU 

Aby udostępnić nową jednostkę SKU dla istniejącej oferty, należy wykonać następujące czynności:

1. Na stronie **Wszystkie oferty** znajdź ofertę.
3. W formularzu **Jednostek SKU** kliknij pozycję **Dodaj nową jednostkę SKU** i podaj **identyfikator jednostki SKU** w wyskakującym okienku.
4. Wykonaj pozostałe czynności opisane w [obszarze Publikuj ofertę maszyny wirtualnej](../virtual-machine/cpp-publish-offer.md).
5. Kliknij **przycisk Publikuj,** aby rozpocząć proces publikowania.


### <a name="update-offer-marketplace-assets"></a>Aktualizowanie zasobów portalu marketplace ofert

Możesz mieć scenariusze, w których musisz zaktualizować zasoby tekstowe i graficzne portalu marketplace, takie jak logo firmy, opis oferty itp. Aby zaktualizować te zasoby, wykonaj następujące czynności.

1. Na stronie **Wszystkie oferty** znajdź swoją ofertę. 
2. Wybierz kartę **Marketplace** i postępuj zgodnie z instrukcjami w temacie *karty Marketplace* oferty.
3. Kliknij **przycisk Publikuj,** aby rozpocząć proces publikowania.


### <a name="update-pricing-on-published-offers"></a>Aktualizowanie cen w opublikowanych ofertach

Po opublikowaniu oferty płatności zgodnie z rzeczywistym i wyjazdem nie można podnieść ceny istniejącej jednostki SKU.  Zamiast tego utwórz jednostkę SKU w ramach tej samej oferty, usuń starą jednostkę SKU, a następnie ponownie opublikuj ofertę. Możesz obniżyć cenę w stosunku do wcześniej opublikowanych ofert. Aby obniżyć cenę ofertową:

1. Wybierz jednostkę SKU, dla której chcesz obniżyć ceny.
2. Niższą cenę należy ustawić za pomocą tego samego mechanizmu, który został pierwotnie użyty: bezpośrednio w interfejsie użytkownika portalu lub w arkuszu kalkulacyjnym importu/eksportu.
3. Kliknij przycisk **Zapisz**.
4. Kliknij **przycisk Publikuj,** aby rozpocząć proces publikowania.

Ceny są widoczne dla nowych klientów, gdy jest on na rynku, a wszyscy nowi klienci będą płacić nową obniżoną cenę.  W przypadku istniejących klientów spadek cen jest odzwierciedlony z mocą wsteczną do początku cyklu rozliczeniowego, w trakcie którego spadek cen stał się skuteczny.  Jeśli zostały już naliczone za cykl, w którym nastąpił spadek cen, otrzymają zwrot pieniędzy podczas następnego cyklu rozliczeniowego na pokrycie obniżonej ceny.


## <a name="compare-feature"></a>Funkcja Porównywania

Po wprowadzeniu zmian w opublikowanej ofercie można użyć funkcji *Porównaj* do inspekcji zmian. Aby skorzystać z tej funkcji:

1. W dowolnym momencie procesu edycji możesz kliknąć przycisk **Porównaj** na karcie **Edytor** oferty.
2. Okno porównania wyświetla wersje obok siebie zapisanych zmian w tej ofercie w porównaniu z ofertą marketplace. 

![Przycisk Porównaj ofertę w zakładce edytora](./media/offer-compare-button.png)


## <a name="history-of-publishing-actions"></a>Historia działań wydawniczych

Aby wyświetlić historyczne działania publikowania, wybierz kartę **Historia** na lewym pasku menu pionowym portalu Cloud Partner Portal.  Historia strona zapewnia elastyczne filtrowanie według kilku cech i obsługuje zamawiania kolumn.  Każde zdarzenie publikowania jest sygnaturą czasowa.  Aby uzyskać więcej informacji, zobacz [Strona historii inspekcji](../portal-tour/cpp-history-page.md).


## <a name="next-steps"></a>Następne kroki

Za pomocą portalu cloud partnerów można również [usunąć opublikowaną jednostkę SKU lub ofertę](./cpp-delete-offer.md).
