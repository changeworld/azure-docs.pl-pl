---
title: Aktualizowanie istniejącej oferty w portalu Azure Marketplace | Dokumentacja firmy Microsoft
description: Wyjaśnia, jak aktualizowanie istniejącej oferty portalu Azure Marketplace przy użyciu portalu Cloud Partner.
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
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: pbutlerm
ms.openlocfilehash: bfe2b0c70c8b912f6489ed461f5bcf6f233ed60d
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2018
ms.locfileid: "48810365"
---
<a name="update-an-existing-offer-for-azure-marketplace"></a>Aktualizowanie istniejącej oferty w portalu Azure Marketplace
==============================================

Istnieją różne rodzaje aktualizacje, które można zastosować do oferty, gdy jest stała się na żywo.   [Portalu Cloud Partner](https://cloudpartner.azure.com/) posiada kilka funkcji, które ułatwiają edytowanie ofertę, aby upewnić się, zmiany są poprawne, w tym:

-  Dodawanie nowej wersji obrazu maszyny wirtualnej (VM) na istniejących jednostek SKU
-  Zmienianie regionów, którego jednostka SKU jest dostępna w
-  Dodawanie nowych jednostek SKU
-  Aktualizowanie metadanych witryny marketplace dla oferty lub jednostki SKU 
-  Trwa aktualizowanie cennik płatności oferuje
-  Porównanie funkcji
-  Historia funkcji

Po zmodyfikowaniu ofertę lub jednostki SKU go należy ponownie opublikować przed wprowadzanie zmian.
W tym artykule przedstawiono różne aspekty aktualizowanie oferty maszyny Wirtualnej.


<a name="unpermitted-changes-to-vm-offersku"></a>Niedozwolonej zmiany oferty/jednostki SKU maszyny Wirtualnej
-----------------------------------

Istnieją pewne atrybuty oferty maszyny Wirtualnej lub jednostki SKU, która nie może być modyfikowany po oferta jest dostępna na żywo w witrynie Azure Marketplace.

-  Identyfikator oferty i identyfikatorze wydawcy, oferty.
-  Identyfikator jednostki SKU istniejących jednostek SKU.
-  Liczba istniejących jednostek SKU na dysku danych.
-  Model rozliczeń/licencji zmian do istniejących jednostek SKU.


<a name="updating-the-vm-image-version-for-a-sku"></a>Aktualizowanie wersji obrazu maszyny Wirtualnej dla jednostki SKU
---------------------------------------

Obraz maszyny Wirtualnej dla jednostki SKU oferty mogły zostać zaktualizowane przy użyciu dodatkowe funkcje, poprawki zabezpieczeń, itp. W takich przypadkach można zaktualizować obrazu maszyny Wirtualnej, który odwołuje się do danej jednostki SKU. Wykonaj następujące kroki, aby zaktualizować obrazu maszyny Wirtualnej. 

1.  Zaloguj się do [portalu dla partnerów w chmurze](https://cloudpartner.azure.com/).
2.  W obszarze **wszystkich ofert**, odnaleźć oferty, czy chcesz zaktualizować.
3.  W obszarze **jednostki SKU** formularza, kliknij w ramach jednostki SKU, których maszyna wirtualna obrazu możesz chcesz zaktualizować.
4.  W obszarze **wersje dyskowe**, kliknij pozycję **+ nowa wersja dysku** można dodać nowego obrazu maszyny Wirtualnej.
5.  Podaj nowe obrazy maszyn wirtualnych **wersje dyskowe**. Wersje dyskowe musi wykonać [wersji semantycznej](http://semver.org/) formatu. Wersje powinny być w postaci X.Y.Z, gdzie X, Y i Z są liczbami całkowitymi. Upewnij się, że nowa wersja, którą podasz jest większa niż w starszych wersjach, inne nowy numer wersji nie będą wyświetlane w witrynie Azure portal lub portalu Azure marketplace, po ponownym opublikowaniu.
6.  Aby uzyskać **adres URL wirtualnego dysku twardego systemu operacyjnego**, wprowadź sygnatury dostępu współdzielonego (SAS) identyfikator URI utworzony dla wirtualnego dysku twardego systemu operacyjnego. Należy pamiętać, że liczba dysków danych nie można zmienić między różnymi wersjami jednostki SKU. Jeśli poprzednie wersje oferowały dyski danych skonfigurowane, nowa wersja musi mieć dyski danych skonfigurowane.
7.  Kliknij pozycję **Publikuj** Konferencję przepływu pracy publikowania można pobrać nową wersję maszyny Wirtualnej do emisji na żywo na rynku platformy Azure i witryny Azure portal.


<a name="changing-regions-a-sku-is-available-in"></a>Zmiana regionów jednostka SKU jest dostępna w
--------------------------------------

Wraz z upływem czasu można udostępnić ofertę/jednostkę SKU w większej liczbie regionów.
Alternatywnie możesz polegająca na wyłączeniu obsługi oferty/jednostki SKU w danym regionie.
Aby zaimplementować te zmiany, wykonaj poniższe kroki.

1.  Zaloguj się do [portalu dla partnerów w chmurze](https://cloudpartner.azure.com/).
2.  W obszarze **wszystkich ofert** odnaleźć oferty, czy chcesz zaktualizować.
3.  W obszarze **jednostki SKU** formularza, kliknij w jednostce SKU dostępność regionów, których chcesz zaktualizować.
4.  Kliknij pozycję **Wybierz kraje** przycisku w obszarze **dostępności Kraj/Region** pola.
5.  W podręcznym dostępność regionów Dodawanie/usuwanie regionów, które mają dla tej jednostki SKU.
6.  Kliknij pozycję **Publikuj** Konferencję przepływu pracy publikowania do aktualizacji poziomu dostępności region jednostek SKU.

Jeśli jednostka SKU jest wykonywane w nowym regionie, będzie miał możliwość określenia ceny dla tego konkretnego regionu za pośrednictwem **Eksport danych ceny** funkcji. Jeśli dodajesz ponownie region, który był po jego udostępnieniu przed nie można zaktualizować, cennik, ponieważ zmianie cen nie są dozwolone.


<a name="adding-a-new-sku"></a>Dodawanie nowej jednostki SKU
----------------

Aby udostępnić nowej jednostki SKU istniejącej oferty, wykonaj poniższe kroki.

1.  Zaloguj się do [portalu dla partnerów w chmurze](https://cloudpartner.azure.com/).
2.  W obszarze **wszystkich ofert** odnaleźć oferty, czy chcesz zaktualizować.
3.  W obszarze **jednostki SKU** formularz, clik na **Dodawanie nowej jednostki SKU** i podaj **identyfikator jednostki SKU** w oknie podręcznym.
4.  Wykonaj pozostałe kroki szczegółowo opisane w [publikować maszynę wirtualną w portalu Azure Marketplace](./cloud-partner-portal-publish-virtual-machine.md).
5.  Kliknij pozycję **Publikuj** Konferencję przepływu pracy publikowania ma nowe jednostki SKU emisji na żywo.


<a name="updating-offer-marketplace-metadata"></a>Aktualizowanie oferty marketplace metadanych.
------------------------------------

Masz scenariusze, w których należy zaktualizować metadane marketplace skojarzone z ofertą, takie jak aktualizowanie Twoje logo firmy, np. Wykonaj następujące kroki, aby zaktualizować swoją ofertę metadanych.

1.  Zaloguj się do [portalu dla partnerów w chmurze](https://cloudpartner.azure.com/).
2.  W obszarze **wszystkich ofert** odnaleźć oferty, czy chcesz zaktualizować.
3.  Przejdź do **Marketplace** formularza i postępuj zgodnie z instrukcjami poniżej, aby wprowadzać żadnych zmian.
4.  Kliknij pozycję **Publikuj** Konferencję przepływu pracy publikowania zostały wprowadzone zmiany, przejdź na żywo.


<a name="updating-pricing-on-published-offers"></a>Trwa aktualizowanie ceny na opublikowane oferty
------------------------------------

Po opublikowaniu oferty zgodnie z rzeczywistym użyciem, nie można zwiększyć cena istniejących jednostek SKU, ale można utworzyć nowej jednostki SKU w ramach tej samej oferty, usuwanie starych jednostek SKU, a następnie ponownie opublikować ofertę. Umożliwiamy również zmniejszyć ceny z już opublikowanych ofert. Aby zmniejszyć oferta:

1.  Kliknij jednostkę SKU, dla którego chcesz zmniejszyć ceny.
2.  Jeśli ustawiono cenie w 1 x 1 graficznego interfejsu użytkownika, można zmienić cena bezpośrednio w interfejsie użytkownika. Jeśli ustawisz ceny za pośrednictwem importu/eksportu arkusza kalkulacyjnego, można zmniejszyć tylko ceny za pomocą funkcji importowania/eksportowania.
3.  Kliknij pozycję **Zapisz**.
4.  Ponownie opublikować ofertę, a następnie przejdź na żywo.

Ceny będą widoczne dla nowych klientów po całkowicie na żywo w witrynie sieci Web, i wszyscy nowi klienci będą płacić nowa cena obniżona.

W przypadku istniejących klientów spadek cen zostaną odzwierciedlone wstecznie na początku okresu rozliczeniowego, w którym się skuteczne spadek cen.
Jeśli zostały one już zafakturowane cykl, w którym wystąpił spadek, otrzymają zwrot pieniędzy podczas następnego cyklu rozliczeniowego dla pokrycia obniżonej cenie.


<a name="simplified-currency-pricing"></a>Cennik uproszczone waluty
---------------------------

Od września 2018 r. nową sekcję o nazwie **uproszczony ceny waluty** będą widoczne dla Ciebie. Microsoft jest usprawnienie działalności w portalu Azure Marketplace, należy włączyć bardziej przewidywalne ceny i kolekcje z klientami na całym świecie. To usprawnienie można osiągnąć dzięki zmniejszeniu liczby walut, w których firma Microsoft faktury dla odbiorców.

Nowa sekcja podejmie, ceny w tych nowych walutach. Po przeprowadzeniu migracji wszystkich klientów do tych nowych walutach rozliczenia oryginalnego sekcji zostaną wycofane, a pozostanie tylko sekcję "Uproszczony ceny waluty".

Będziesz mieć do momentu 1 listopada 2018 można ustawić nowej ceny dla regionów, polegającego na tym, Waluta rozliczenia ulega zmianie. Nie można zwiększyć ceny dla regionów, w którym nie ulega zmianie waluty rozliczenia. Dostępne są następujące regiony, w którym zmianie waluty:

| Kraj                  | Kod ISO2 | Twoja waluta rozliczeniowa uproszczony |
|--------------------------|-----------|-----------------------------|
| **Algieria**              | DZ        | USD                         |
| **Argentyna**            | AR        | USD                         |
| **Bahrajn**              | BH        | USD                         |
| **Białoruś**              | PRZEZ        | USD                         |
| **Brazylia**               | BRAZYLIA        | BRL                         |
| **Bułgaria**             | BG        | EUR                         |
| **Chile**                | CL        | USD                         |
| **Kolumbia**             | CO        | USD                         |
| **Kostaryka**           | CR        | USD                         |
| **Chorwacja**              | Kadry        | EUR                         |
| **Czechy**       | CZECHY        | EUR                         |
| **Egipt**                | NA PRZYKŁAD        | USD                         |
| **Gwatemala**            | GT        | USD                         |
| **Hongkong**            | HK        | USD                         |
| **Węgry**              | HU        | EUR                         |
| **Islandia**              | JEST        | EUR                         |
| **Indonezja**            | ID        | USD                         |
| **Izrael**               | IL        | USD                         |
| **Jordania**               | JO        | USD                         |
| **Kazachstan**           | KZ        | USD                         |
| **Kenia**                | KE        | USD                         |
| **Kuwejt**               | KW        | USD                         |
| **Liechtenstein**        | LI        | EUR                         |
| **Macedonia (była Jugosłowiańska Republika)**     | MK        | USD                         |
| **Malezja**             | MOJE        | USD                         |
| **Meksyk**               | MX        | USD                         |
| **Czarnogóra**           | MNIE        | USD                         |
| **Maroko**              | MA        | USD                         |
| **Nigeria**              | NG        | USD                         |
| **Oman**                 | OM        | USD                         |
| **Pakistan**             | KLUCZ PODSTAWOWY        | USD                         |
| **Paragwaj**             | PY        | USD                         |
| **Peru**                 | PE        | USD                         |
| **Filipiny**          | PH        | USD                         |
| **Polska\***             | PL        | EUR                         |
| **Katar**                | ODPOWIEDZI NA PYTANIA        | USD                         |
| **Rumunia**              | RO        | EUR                         |
| **Arabia Saudyjska**         | AMERYKA POŁUDNIOWA        | USD                         |
| **Serbia**               | RS        | USD                         |
| **Singapur**            | GRUPY ZABEZPIECZEŃ        | USD                         |
| **Republika Południowej Afryki**         | ZA        | USD                         |
| **Tajlandia**             | TH        | USD                         |
| **Trynidad i Tobago**  | TT        | USD                         |
| **Tunezja**              | TN        | USD                         |
| **Turcja**               | TR        | USD                         |
| **Ukraina**              | AGENT UŻYTKOWNIKA        | USD                         |
| **Zjednoczone Emiraty Arabskie** | AE        | USD                         |
| **Urugwaj**              | UY        | USD                         |
|  |  |  |

> [!NOTE]
> Jeśli publikowanie oferty za pomocą interfejsów API można zauważyć nowa sekcja w ramach tej oferty, o nazwie **JSON**. To może być oznaczona jako `virtualMachinePricingV2` lub `monthlyPricingV2`, w zależności od typu oferty.

Jeśli masz jakieś pytania dotyczące tej zmiany, skontaktuj się z działem pomocy technicznej systemu Azure Marketplace.


<a name="compare-feature"></a>Porównanie funkcji
---------------

Po wprowadzeniu zmian w ramach oferty już opublikowane, możesz użyć *porównania* funkcji inspekcji zmian, które zostały wprowadzone. Korzystanie z porównania:

1.  W dowolnym momencie procesu edycji, możesz kliknąć pozycję **porównania** przycisk oferty.

2.  Wyświetl wersje side-by-side marketingu zasoby i metadanych.


<a name="history-of-publishing-actions"></a>Historia akcji publikowania
-----------------------------

Aby wyświetlić wszystkie działań historycznych w publikacji, kliknij **historii** kartę w lewym okienku nawigacji o portalu Cloud Partner. W tym miejscu można wyświetlić oznaczony sygnaturą czasową akcje, które zostały podjęte w okresie istnienia Twoich ofert w portalu Azure Marketplace.
