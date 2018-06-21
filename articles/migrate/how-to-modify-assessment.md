---
title: Dostosuj ustawienia migracji Azure oceny | Dokumentacja firmy Microsoft
description: Opisuje sposób ustawiania i uruchamiania ocenę do migrowania maszyn wirtualnych VMware do platformy Azure przy użyciu usługi Azure planowania migracji
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 06/20/2018
ms.author: raynew
ms.openlocfilehash: 9ddd6c32388b2e05fd97138414958b67c009f9ee
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2018
ms.locfileid: "36284917"
---
# <a name="customize-an-assessment"></a>Dostosowywanie oceny

[Azure migracji](migrate-overview.md) tworzy oceny z właściwości domyślnych. Po utworzeniu ocenę, można zmodyfikować właściwości domyślnych, korzystając z instrukcji w tym artykule.


## <a name="edit-assessment-properties"></a>Edytowanie właściwości oceny

1. Na stronie **Oceny** projektu migracji wybierz ocenę i kliknij pozycję **Edytuj właściwości**.
2. Zmodyfikuj właściwości zgodnie z poniższą tabelą:

    **Ustawienie** | **Szczegóły** | **Domyślne**
    --- | --- | ---
    **Lokalizacja docelowa** | Lokalizacja platformy Azure, do której chcesz przeprowadzić migrację.<br/><br/> Usługa Azure Migrate obsługuje obecnie 30 regionów, w tym: Australia Południowo-Wschodnia, Australia Wschodnia, Azja Południowo-Wschodnia, Azja Wschodnia, Brazylia Południowa, Chiny Północne, Chiny Wschodnie, Europa Północna, Europa Zachodnia, Indie Południowe, Indie Środkowe, Indie Zachodnie, Japonia Wschodnia, Japonia Zachodnia, Kanada Środkowa, Kanada Wschodnia, Korea Południowa, Korea Środkowa, Niemcy Północno-Wschodnie, Niemcy Środkowe, Południowe Zjednoczone Królestwo, Południowo-środkowe stany USA, Północno-środkowe stany USA, Środkowe stany USA, US Gov Arizona, US Gov Teksas, US Gov Wirginia, Wschodnie stany USA, Wschodnie stany USA 2, Zachodnie stany USA, Zachodnie stany USA 2, Zachodnie Zjednoczone Królestwo i Zachodnio-środkowe stany USA. |  Zachodnie stany USA 2 jest domyślną lokalizacją.
    **Warstwa cenowa** | Możesz określić [warstwę cenową (Podstawowa/Standardowa)](../virtual-machines/windows/sizes-general.md) dla docelowych maszyn wirtualnych platformy Azure. Na przykład jeśli planujesz migrację środowiska produkcyjnego, rozważ warstwę Standardowa, która udostępnia maszyny wirtualne o małych opóźnieniach, lecz które mogą kosztować więcej. Z drugiej strony w przypadku środowiska deweloperskiego i testowego rozważ warstwę Podstawowa, która udostępnia maszyny wirtualnych o większych opóźnieniach, lecz przy niższych kosztach. | Domyślnie jest używana warstwa [Standardowa](../virtual-machines/windows/sizes-general.md).
    **Typ magazynu** | Można określić typ dysków, które mają zostać przydzielone na platformie Azure. Ta właściwość ma zastosowanie, gdy kryterium zmiany rozmiaru jest takie jak lokalna zmiana rozmiaru. Typem dysku docelowego mogą być dyski zarządzane w warstwie Premium lub dyski zarządzane w warstwie Standardowa. Jeśli rozmiar zostanie określony na podstawie wydajności, rekomendacje dotyczące dysku odbywają się automatycznie na podstawie danych wydajności maszyn wirtualnych. Usługa Azure Migrate obsługuje dyski zarządzane tylko na potrzeby oceny migracji. | Wartość domyślna to dysków zarządzanych w warstwie Premium (z kryterium zmiany rozmiaru jako *jako lokalne zmiany rozmiaru*).
    **Zarezerwowane wystąpień** |  Można również określić, czy masz [zastrzeżone wystąpień](https://azure.microsoft.com/pricing/reserved-vm-instances/) in Azure i migracji Azure oszacuje koszt odpowiednio. Zarezerwowane wystąpień nie mają zastosowania do regionów suwerennych (Azure dla instytucji rządowych, Niemczech i Chiny) i są one stosowane tylko do oferty płatność za rzeczywiste użycie w Azure migracji. | Wartość domyślna dla tej właściwości to 3 lat zastrzeżone wystąpień.
    **Kryterium rozmiaru** | Kryterium do użycia przez usługę Azure Migrate w celu określenia odpowiedniego rozmiaru dla maszyn wirtualnych platformy Azure. Rozmiar maszyn wirtualnych możesz ustalać *na podstawie wydajności* lub rozmiaru *maszyn lokalnych* bez uwzględniania historii wydajności. | Opcja domyślna to określanie rozmiaru na podstawie wydajności.
    **Historia wydajności** | Przedział czasu uwzględniany podczas oceny wydajności maszyn wirtualnych. Ta właściwość ma zastosowanie tylko w przypadku *ustalania rozmiaru na podstawie wydajności*. | Wartość domyślna to jeden dzień.
    **Użycie percentyla** | Wartość percentyla próbki wydajności uwzględniana w celu prawidłowego określenia rozmiaru. Ta właściwość ma zastosowanie tylko w przypadku *ustalania rozmiaru na podstawie wydajności*.  | Domyślnie jest to 95. percentyl.
    **Serie maszyn wirtualnych** | Określić możesz serie maszyn wirtualnych, które mają być brane pod uwagę podczas ustalania właściwego rozmiaru. Jeśli na przykład masz środowisko produkcyjne, którego nie zamierzasz migrować do maszyn wirtualnych serii A na platformie Azure, możesz wykluczyć serię A z listy serii, a ustalanie właściwego rozmiaru zostanie wykonane tylko przy użyciu wybranych serii. | Domyślnie wybrane są wszystkie szeregi maszyny Wirtualnej.
    **Współczynnik komfortu** | Podczas oceny usługa Azure Migrate uwzględnia bufor (współczynnik komfortu). Jest on stosowany do wszystkich danych użycia maszyn wirtualnych (procesora, pamięci, dysku i sieci). Współczynnik komfortu uwzględnia kwestie, takie jak okresowe użycie, krótka historia wydajności i prawdopodobne zwiększenie użycia w przyszłości.<br/><br/> Na przykład 10-rdzeniowa maszyna wirtualna o użyciu na poziomie 20% jest w normalnych warunkach równoważna 2-rdzeniowej maszynie wirtualnej. Jednak wynik zastosowania współczynnika komfortu o wartości 2 daje 4-rdzeniową maszynę wirtualną. | Ustawienie domyślne to 1,3x.
    **Oferta** | [Oferta platformy Azure](https://azure.microsoft.com/support/legal/offer-details/), w której przeprowadzono rejestrację. | Ustawienie domyślne to [Płatność zgodnie z rzeczywistym użyciem](https://azure.microsoft.com/offers/ms-azr-0003p/).
    **Waluta** | Twoja waluta rozliczeniowa. | Wartość domyślna to USD.
    **Rabat (%)** | Dowolny rabat skojarzony z daną subskrypcją, stosowany do całej oferty platformy Azure. | Ustawienie domyślne to 0%.
    **Czas działania maszyny Wirtualnej** | Jeśli maszyny wirtualne nie będą działać 24 x 7 na platformie Azure, można określić czas trwania (liczba dni w miesiącu) a liczbą godzin na dzień, dla którego one będzie działać i kosztów uzyskać szacunkowe wartości zostaną wykonane, w związku z tym. | Wartość domyślna to 31 dni w miesiącu i 24 godziny na dobę.
    **Korzyść użycia hybrydowego platformy Azure** | Określ, jeśli masz pakiet Software Assurance i kwalifikujesz się do otrzymania [korzyści użycia hybrydowego platformy Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/). W przypadku wybrania opcji Yes maszyny wirtualne z systemem Windows są wyceniane jak platforma Azure z systemami innymi niż Windows. | Wartość domyślna to Yes.

3. Kliknij przycisk **zapisać** zaktualizować oceny.


## <a name="next-steps"></a>Kolejne kroki

[Dowiedz się więcej](concepts-assessment-calculation.md) na temat sposobu obliczania ocen.
