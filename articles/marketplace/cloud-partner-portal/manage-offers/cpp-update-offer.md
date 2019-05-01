---
title: Aktualizowanie oferty w witrynie marketplace | Portal Azure Marketplace
description: Aktualizacja oferty na platformie Azure i usługi AppSource rynków przy użyciu portalu Cloud Partner
services: Azure, AppSource, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: pabutler
ms.openlocfilehash: 73e2812c678dca7e21089ee9cc091db756d7e25a
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2019
ms.locfileid: "64942337"
---
# <a name="update-azure-marketplace-and-appsource-offers"></a>Aktualizowanie oferty w portalu Azure Marketplace i AppSource

Istnieją różne rodzaje aktualizacje, które można zastosować do swojej oferty po jej opublikowaniu.  [Portalu Cloud Partner](https://cloudpartner.azure.com/) pomaga w prawidłowo modyfikowanie atrybutów oferty, w tym:

-  Dodawanie nowej wersji obrazu lub pakietu maszynę wirtualną (VM) na istniejących jednostek SKU
-  Zmienianie regionów, którego jednostka SKU jest dostępna w
-  Dodawanie nowych jednostek SKU
-  Aktualizowanie metadanych witryny marketplace dla oferty lub jednostki SKU 
-  Trwa aktualizowanie cennik płatności oferuje

Portal udostępnia również funkcje, takie jak możliwość porównanie funkcji i wyświetlić historię funkcji dla oferty, które są pomocne w zarządzaniu zmianami.  Po zmodyfikowaniu ofertę lub jednostki SKU go należy ponownie opublikować przed zmian "na żywo".  W tym artykule przedstawiono różne aspekty aktualizowanie oferty w portalu marketplace.

## <a name="unpermitted-changes-to-an-offersku"></a>Niedozwolonej zmiany oferty/jednostki SKU

Istnieją pewne atrybuty oferty lub jednostki SKU, która nie może być modyfikowany po opublikowaniu w portalu marketplace.  Odpowiednie pola są wyłączone w **edytora** karty portalu, na przykład:  

- Oferują one ID i identyfikator wydawcy
- IDENTYFIKATOR JEDNOSTKI SKU 
- Liczba istniejących jednostek SKU na dysku danych
- Rozliczenia i licencji zmiany modelu istniejących jednostek SKU
- Wersja — tagi, na przykład: `1.0.1`


## <a name="common-update-operations"></a>Typowe operacje aktualizacji

Poniższe sekcje opisują sposób wykonywania niektórych większość operacji aktualizacji.  Te operacje nie są dostępne dla wszystkich typów ofert.  Użytkownik musi zalogować się do portalu Cloud Partner można uruchomić dowolne z tych operacji.


### <a name="update-offer-contacts"></a>Aktualizowanie kontaktów oferty

Wykonaj następujące kroki, aby zaktualizować kontaktów pomocy technicznej oferty.
1. W **oferuje wszystkie** wybierz ofertę.
2. Wybierz **kontakty** kartę. Aktualizowanie kontaktów.
3. Wybierz ikonę **Zapisz**.
4. Wybierz **Publikuj** aby rozpocząć proces publikowania.


### <a name="change-regions-an-offer-or-sku-is-available-in"></a>Zmień regiony, które oferty lub jednostki SKU jest dostępna w

Wraz z upływem czasu można udostępnić ofertę/jednostkę SKU w większej liczbie regionów.
Alternatywnie możesz polegająca na wyłączeniu obsługi oferty/jednostki SKU w danym regionie.
Aby zaimplementować te zmiany, wykonaj poniższe kroki.

1. W **wszystkich ofert** strony, Znajdź tej oferty, które chcesz zaktualizować.

W przypadku ofert portalu Azure Marketplace:

1. Wybierz **jednostki SKU** kartę.  Wybierz jednostkę SKU, aby zmodyfikować.
1. Kliknij przycisk **Wybierz kraje** przycisku w obszarze **dostępności Kraj/Region** pola.
1. W oknie dialogowym dostępności regionu należy dodać lub usunąć regiony tej jednostki SKU.

W przypadku ofert usługi AppSource:

1. Wybierz **szczegóły Storefront** kartę.
1. Obok pozycji **obsługiwane kraje/regiony** etykietę, kliknij przycisk **obsługiwane kraje/regiony**. 
1. W oknie dialogowym obsługiwane kraje/regiony Dodawanie lub usuwanie regionów dla tej oferty.

Dla każdej witryny marketplace:

1. Kliknij przycisk **Publikuj** aby rozpocząć proces publikowania. 

Jeśli jednostka SKU jest wykonywane w nowym regionie, masz możliwość określenia ceny dla tego konkretnego regionu za pośrednictwem **Eksport danych ceny** funkcji. W przypadku dodawania ponownie region, który wcześniej był dostępny, nie można zaktualizować, cennik, ponieważ zmianie cen nie są dozwolone.


### <a name="add-a-new-sku"></a>Dodawanie nowej jednostki SKU 

Aby udostępnić nowej jednostki SKU dla istniejącej oferty, należy użyć następujące czynności:

1. W **wszystkich ofert** strony, Znajdź oferty.
3. W obszarze **jednostki SKU** formularza, kliknij przycisk **Dodawanie nowej jednostki SKU** i podaj **identyfikator jednostki SKU** w oknie podręcznym.
4. Wykonaj pozostałe kroki szczegółowo opisane w [publikowanie oferty maszyny wirtualnej](../virtual-machine/cpp-publish-offer.md).
5. Kliknij przycisk **Publikuj** aby rozpocząć proces publikowania.


### <a name="update-offer-marketplace-assets"></a>Aktualizowanie zasobów portalu marketplace oferty

Może być scenariuszach, gdzie należy zaktualizować w portalu marketplace, na podstawie tekstu i obrazów zasoby, takie Twoje logo firmy oferują opis itd. Wykonaj następujące kroki, aby zaktualizować te zasoby.

1. W **wszystkich ofert** strony, Znajdź swoją ofertę. 
2. Wybierz **Marketplace** karcie, a następnie postępuj zgodnie z instrukcjami w swojej ofercie *kartę Marketplace* tematu.
3. Kliknij przycisk **Publikuj** aby rozpocząć proces publikowania.


### <a name="update-pricing-on-published-offers"></a>Aktualizacja cen na opublikowane oferty

Po opublikowaniu oferty zgodnie z rzeczywistym użyciem nie może zwiększyć cena istniejących jednostek SKU.  Zamiast tego należy utworzyć jednostki SKU w ramach tej samej oferty usuwanie starych jednostek SKU i ponownie opublikować ofertę. Może obniżyć cenę na poprzednio opublikowanych ofert. Aby zmniejszyć oferta:

1. Wybierz jednostkę SKU, dla którego chcesz zmniejszyć ceny.
2. Należy ustawić niższej cenie przez ten sam mechanizm, który pierwotnie używana: bezpośrednio w portalu, interfejsu użytkownika lub z arkusza kalkulacyjnego importu/eksportu.
3. Kliknij pozycję **Zapisz**.
4. Kliknij przycisk **Publikuj** aby rozpocząć proces publikowania.

Ceny są widoczne dla nowych klientów, po jest aktywna, w portalu marketplace i wszyscy nowi klienci będą następnie wina nowe pogorszyła.  W przypadku istniejących klientów spadek cen maszynie wirtualnej jest odzwierciedlana wstecznie rozpoczęcia okresu rozliczeniowego, w którym się skuteczne spadek cen.  Jeśli zostały one już zafakturowane cykl, w którym wystąpił spadek, otrzymają zwrot pieniędzy podczas następnego cyklu rozliczeniowego dla pokrycia obniżonej cenie.


## <a name="compare-feature"></a>Porównanie funkcji

Po wprowadzeniu zmian w ramach oferty opublikowane, możesz użyć *porównania* funkcji inspekcji zmian. Korzystanie z tej funkcji:

1. W dowolnym momencie procesu edycji, możesz kliknąć pozycję **porównania** znajdujący się w **edytora** karcie oferty.
2. W oknie porównania Wyświetla side-by-side wersje zapisane zmiany tej oferty w porównaniu z oferty w portalu marketplace. 

![Porównanie oferty przycisku w karcie edytora](./media/offer-compare-button.png)


## <a name="history-of-publishing-actions"></a>Historia akcji publikowania

Zaznacz, aby wyświetlić historyczne działania publikowania **historii** karcie po lewej stronie pionowy pasek menu z portalu Cloud Partner.  Strona historii zapewnia elastyczne filtrowanie według pewne cechy i obsługuje kolejność kolumn.  Każde zdarzenie publikowania jest oznaczony sygnaturą czasową.  Aby uzyskać więcej informacji, zobacz [strony historii inspekcji](../portal-tour/cpp-history-page.md).


## <a name="next-steps"></a>Kolejne kroki

Można również użyć portalu Cloud Partner, aby [usuwanie opublikowane jednostki SKU lub zamieszczanie ofert](./cpp-delete-offer.md).
