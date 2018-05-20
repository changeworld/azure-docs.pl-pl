---
title: Dostosuj ustawienia migracji Azure oceny | Dokumentacja firmy Microsoft
description: Opisuje sposób ustawiania i uruchamiania ocenę do migrowania maszyn wirtualnych VMware do platformy Azure przy użyciu usługi Azure planowania migracji
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 05/15/2018
ms.author: raynew
ms.openlocfilehash: c826453dcbcaf2facfd58daa05b77decda7ae456
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="customize-an-assessment"></a>Dostosowywanie oceny

[Azure migracji](migrate-overview.md) tworzy oceny z właściwości domyślnych. Po utworzeniu ocenę, można zmodyfikować właściwości domyślnych, korzystając z instrukcji w tym artykule.


## <a name="edit-assessment-properties"></a>Edytowanie właściwości oceny

1. Na stronie **Oceny** projektu migracji wybierz ocenę i kliknij pozycję **Edytuj właściwości**.
2. Zmodyfikuj właściwości zgodnie z poniższą tabelą:

    **Ustawienie** | **Szczegóły** | **Domyślne**
    --- | --- | ---
    **Lokalizacja docelowa** | Lokalizacja platformy Azure, do której chcesz przeprowadzić migrację.<br/><br/> Migracji Azure obsługuje obecnie 30 regionów, w tym Australia Wschodnia, Południowo-Wschodnia Australia, Brazylia Południowa, Kanada centralnej, Kanada Wschodnia, Indie środkowe, środkowe stany USA, wschodnie Chin, Chin Północna, Azja Wschodnia, wschodnie stany USA, Niemcy centralnej, Niemcy północno-wschodnie, wschodnie stany USA 2, Japonia Wschodnia, Japonia Zachodnia Korei środkowe, Korea Południowa, Północna środkowe stany USA, Europa Północna, południowo środkowe stany USA, Azja południowo-wschodnia, Indie Południowe, Wielka Brytania Południowa, Wielka Brytania Zachodnia, Virginia wersji dla instytucji rządowych wersji dla instytucji rządowych Texas, USA wersji dla instytucji rządowych Arizona, USA USA, zachodnie środkowe stany USA, Europa Zachodnia, Indie Zachodnie, zachodnie stany USA i US2 zachodnie. |  Zachodnie stany USA 2 jest domyślną lokalizacją.
    **Nadmiarowość magazynu** | Typ nadmiarowości magazynu, który będzie używany przez maszyny wirtualne platformy Azure po zakończeniu migracji. | Wartość domyślna to [Magazyn lokalnie nadmiarowy (LRS)](../storage/common/storage-redundancy-lrs.md). Azure obsługuje tylko migracji zarządzane opartej na dyskach: ocen i dysków zarządzanych obsługują tylko LRS, dlatego właściwość obecnie zawiera tylko opcja LRS.
    **Kryterium rozmiaru** | Kryterium do użycia przez usługę Azure Migrate w celu określenia odpowiedniego rozmiaru dla maszyn wirtualnych platformy Azure. Rozmiar maszyn wirtualnych możesz ustalać *na podstawie wydajności* lub rozmiaru *maszyn lokalnych* bez uwzględniania historii wydajności. | Opcja domyślna to określanie rozmiaru na podstawie wydajności.
    **Historia wydajności** | Przedział czasu uwzględniany podczas oceny wydajności maszyn wirtualnych. Ta właściwość ma zastosowanie tylko w przypadku *ustalania rozmiaru na podstawie wydajności*. | Wartość domyślna to jeden dzień.
    **Użycie percentyla** | Wartość percentyla próbki wydajności uwzględniana w celu prawidłowego określenia rozmiaru. Ta właściwość ma zastosowanie tylko w przypadku *ustalania rozmiaru na podstawie wydajności*.  | Domyślnie jest to 95. percentyl.
    **Seria maszyn wirtualnych** | Można określić serii maszyny Wirtualnej, która ma zostać należy wziąć pod uwagę doboru wielkości. Na przykład jeśli masz środowiska produkcyjnego, który nie jest planowana migracja do maszyn wirtualnych A-series na platformie Azure, A-series można wykluczyć z listy lub serii i doboru wielkości zostaną wykonane tylko w wybranej serii. | Domyślnie wybrane są wszystkie szeregi maszyny Wirtualnej.
    **Warstwa cenowa** | Możesz określić [warstwę cenową (Podstawowa/Standardowa)](../virtual-machines/windows/sizes-general.md) dla docelowych maszyn wirtualnych platformy Azure. Na przykład jeśli planujesz migrację środowiska produkcyjnego, rozważ warstwę Standardowa, która udostępnia maszyny wirtualne o małych opóźnieniach, lecz które mogą kosztować więcej. Z drugiej strony w przypadku środowiska deweloperskiego i testowego rozważ warstwę Podstawowa, która udostępnia maszyny wirtualnych o większych opóźnieniach, lecz przy niższych kosztach. | Domyślnie jest używana warstwa [Standardowa](../virtual-machines/windows/sizes-general.md).
    **Współczynnik komfortu** | Podczas oceny usługa Azure Migrate uwzględnia bufor (współczynnik komfortu). Jest on stosowany do wszystkich danych użycia maszyn wirtualnych (procesora, pamięci, dysku i sieci). Współczynnik komfortu uwzględnia kwestie, takie jak okresowe użycie, krótka historia wydajności i prawdopodobne zwiększenie użycia w przyszłości.<br/><br/> Na przykład 10-rdzeniowa maszyna wirtualna o użyciu na poziomie 20% jest w normalnych warunkach równoważna 2-rdzeniowej maszynie wirtualnej. Jednak wynik zastosowania współczynnika komfortu o wartości 2 daje 4-rdzeniową maszynę wirtualną. | Ustawienie domyślne to 1,3x.
    **Oferta** | [Oferta platformy Azure](https://azure.microsoft.com/support/legal/offer-details/), w której przeprowadzono rejestrację. | Ustawienie domyślne to [Płatność zgodnie z rzeczywistym użyciem](https://azure.microsoft.com/offers/ms-azr-0003p/).
    **Waluta** | Twoja waluta rozliczeniowa. | Wartość domyślna to USD.
    **Rabat (%)** | Dowolny rabat skojarzony z daną subskrypcją, stosowany do całej oferty platformy Azure. | Ustawienie domyślne to 0%.
    **Korzyść użycia hybrydowego platformy Azure** | Określ, jeśli masz pakiet Software Assurance i kwalifikujesz się do otrzymania [korzyści użycia hybrydowego platformy Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/). W przypadku wybrania opcji Yes maszyny wirtualne z systemem Windows są wyceniane jak platforma Azure z systemami innymi niż Windows. | Wartość domyślna to Yes.

3. Kliknij przycisk **zapisać** zaktualizować oceny.


## <a name="next-steps"></a>Kolejne kroki

[Dowiedz się więcej](concepts-assessment-calculation.md) na temat sposobu obliczania ocen.
