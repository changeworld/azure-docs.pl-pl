---
title: Dostosuj ustawienia migracji Azure oceny | Dokumentacja firmy Microsoft
description: Opisuje sposób ustawiania i uruchamiania ocenę do migrowania maszyn wirtualnych VMware do platformy Azure przy użyciu usługi Azure planowania migracji
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 06/19/2018
ms.author: raynew
ms.openlocfilehash: 4e7decc34105b02be51f002e1951145759a9f46e
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36231529"
---
# <a name="customize-an-assessment"></a>Dostosowywanie oceny

[Azure migracji](migrate-overview.md) tworzy oceny z właściwości domyślnych. Po utworzeniu ocenę, można zmodyfikować właściwości domyślnych, korzystając z instrukcji w tym artykule.


## <a name="edit-assessment-properties"></a>Edytowanie właściwości oceny

1. Na stronie **Oceny** projektu migracji wybierz ocenę i kliknij pozycję **Edytuj właściwości**.
2. Zmodyfikuj właściwości zgodnie z poniższą tabelą:

    **Ustawienie** | **Szczegóły** | **Domyślne**
    --- | --- | ---
    **Lokalizacja docelowa** | Lokalizacja platformy Azure, do której chcesz przeprowadzić migrację.<br/><br/> Usługa Azure Migrate obsługuje obecnie 30 regionów, w tym: Australia Południowo-Wschodnia, Australia Wschodnia, Azja Południowo-Wschodnia, Azja Wschodnia, Brazylia Południowa, Chiny Północne, Chiny Wschodnie, Europa Północna, Europa Zachodnia, Indie Południowe, Indie Środkowe, Indie Zachodnie, Japonia Wschodnia, Japonia Zachodnia, Kanada Środkowa, Kanada Wschodnia, Korea Południowa, Korea Środkowa, Niemcy Północno-Wschodnie, Niemcy Środkowe, Południowe Zjednoczone Królestwo, Południowo-środkowe stany USA, Północno-środkowe stany USA, Środkowe stany USA, US Gov Arizona, US Gov Teksas, US Gov Wirginia, Wschodnie stany USA, Wschodnie stany USA 2, Zachodnie stany USA, Zachodnie stany USA 2, Zachodnie Zjednoczone Królestwo i Zachodnio-środkowe stany USA. |  Zachodnie stany USA 2 jest domyślną lokalizacją.
    **Typ magazynu** | Można określić typ dyski, które mają zostać przydzielone na platformie Azure. Ta właściwość jest stosowane, gdy jest kryterium zmiany rozmiaru, ponieważ lokalne zmiany rozmiaru. Typ dysku docelowym można określić jako zarządzanych w warstwie Premium dyskach zarządzanych przez dyski lub Standard. Na podstawie rozmiaru zalecenie dotyczące dysku automatycznie jest wykonywane na podstawie danych wydajności maszyn wirtualnych. Należy pamiętać, że migracja Azure obsługuje tylko dysków zarządzanych w celu oceny migracji. | Wartość domyślna to dysków zarządzanych w warstwie Premium (z kryterium zmiany rozmiaru jako *jako lokalne zmiany rozmiaru*).
    **Kryterium rozmiaru** | Kryterium do użycia przez usługę Azure Migrate w celu określenia odpowiedniego rozmiaru dla maszyn wirtualnych platformy Azure. Rozmiar maszyn wirtualnych możesz ustalać *na podstawie wydajności* lub rozmiaru *maszyn lokalnych* bez uwzględniania historii wydajności. | Opcja domyślna to określanie rozmiaru na podstawie wydajności.
    **Historia wydajności** | Przedział czasu uwzględniany podczas oceny wydajności maszyn wirtualnych. Ta właściwość ma zastosowanie tylko w przypadku *ustalania rozmiaru na podstawie wydajności*. | Wartość domyślna to jeden dzień.
    **Użycie percentyla** | Wartość percentyla próbki wydajności uwzględniana w celu prawidłowego określenia rozmiaru. Ta właściwość ma zastosowanie tylko w przypadku *ustalania rozmiaru na podstawie wydajności*.  | Domyślnie jest to 95. percentyl.
    **Serie maszyn wirtualnych** | Określić możesz serie maszyn wirtualnych, które mają być brane pod uwagę podczas ustalania właściwego rozmiaru. Jeśli na przykład masz środowisko produkcyjne, którego nie zamierzasz migrować do maszyn wirtualnych serii A na platformie Azure, możesz wykluczyć serię A z listy serii, a ustalanie właściwego rozmiaru zostanie wykonane tylko przy użyciu wybranych serii. | Domyślnie wybrane są wszystkie szeregi maszyny Wirtualnej.
    **Warstwa cenowa** | Możesz określić [warstwę cenową (Podstawowa/Standardowa)](../virtual-machines/windows/sizes-general.md) dla docelowych maszyn wirtualnych platformy Azure. Na przykład jeśli planujesz migrację środowiska produkcyjnego, rozważ warstwę Standardowa, która udostępnia maszyny wirtualne o małych opóźnieniach, lecz które mogą kosztować więcej. Z drugiej strony w przypadku środowiska deweloperskiego i testowego rozważ warstwę Podstawowa, która udostępnia maszyny wirtualnych o większych opóźnieniach, lecz przy niższych kosztach. | Domyślnie jest używana warstwa [Standardowa](../virtual-machines/windows/sizes-general.md).
    **Współczynnik komfortu** | Podczas oceny usługa Azure Migrate uwzględnia bufor (współczynnik komfortu). Jest on stosowany do wszystkich danych użycia maszyn wirtualnych (procesora, pamięci, dysku i sieci). Współczynnik komfortu uwzględnia kwestie, takie jak okresowe użycie, krótka historia wydajności i prawdopodobne zwiększenie użycia w przyszłości.<br/><br/> Na przykład 10-rdzeniowa maszyna wirtualna o użyciu na poziomie 20% jest w normalnych warunkach równoważna 2-rdzeniowej maszynie wirtualnej. Jednak wynik zastosowania współczynnika komfortu o wartości 2 daje 4-rdzeniową maszynę wirtualną. | Ustawienie domyślne to 1,3x.
    **Oferta** | [Oferta platformy Azure](https://azure.microsoft.com/support/legal/offer-details/), w której przeprowadzono rejestrację. | Ustawienie domyślne to [Płatność zgodnie z rzeczywistym użyciem](https://azure.microsoft.com/offers/ms-azr-0003p/).
    **Waluta** | Twoja waluta rozliczeniowa. | Wartość domyślna to USD.
    **Rabat (%)** | Dowolny rabat skojarzony z daną subskrypcją, stosowany do całej oferty platformy Azure. | Ustawienie domyślne to 0%.
    **Korzyść użycia hybrydowego platformy Azure** | Określ, jeśli masz pakiet Software Assurance i kwalifikujesz się do otrzymania [korzyści użycia hybrydowego platformy Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/). W przypadku wybrania opcji Yes maszyny wirtualne z systemem Windows są wyceniane jak platforma Azure z systemami innymi niż Windows. | Wartość domyślna to Yes.

3. Kliknij przycisk **zapisać** zaktualizować oceny.


## <a name="next-steps"></a>Kolejne kroki

[Dowiedz się więcej](concepts-assessment-calculation.md) na temat sposobu obliczania ocen.
