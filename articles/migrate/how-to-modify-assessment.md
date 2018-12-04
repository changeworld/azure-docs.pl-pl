---
title: Dostosowywanie ustawień oceny usługi Azure Migrate | Dokumentacja firmy Microsoft
description: W tym artykule opisano jak skonfigurować i uruchomić ocenę dla migracji maszyn wirtualnych VMware na platformę Azure za pomocą usługi Azure migrate
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 10/30/2018
ms.author: raynew
ms.openlocfilehash: 2423c4fde177ab50552af580a60c7a15550e5586
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2018
ms.locfileid: "52840439"
---
# <a name="customize-an-assessment"></a>Dostosowywanie oceny

[Usługa Azure Migrate](migrate-overview.md) tworzy oceny za pomocą właściwości domyślnych. Po utworzeniu oceny można modyfikować domyślnych właściwości, korzystając z instrukcji w tym artykule.


## <a name="edit-assessment-properties"></a>Edytuj właściwości oceny

1. Na stronie **Oceny** projektu migracji wybierz ocenę i kliknij pozycję **Edytuj właściwości**.
2. Dostosuj właściwości oceny, w oparciu o szczegóły poniżej:

    **Ustawienie** | **Szczegóły** | **Domyślne**
    --- | --- | ---
    **Lokalizacja docelowa** | Lokalizacja platformy Azure, do której chcesz przeprowadzić migrację.<br/><br/> Usługa Azure Migrate obsługuje obecnie 30 regionów, w tym: Australia Południowo-Wschodnia, Australia Wschodnia, Azja Południowo-Wschodnia, Azja Wschodnia, Brazylia Południowa, Chiny Północne, Chiny Wschodnie, Europa Północna, Europa Zachodnia, Indie Południowe, Indie Środkowe, Indie Zachodnie, Japonia Wschodnia, Japonia Zachodnia, Kanada Środkowa, Kanada Wschodnia, Korea Południowa, Korea Środkowa, Niemcy Północno-Wschodnie, Niemcy Środkowe, Południowe Zjednoczone Królestwo, Południowo-środkowe stany USA, Północno-środkowe stany USA, Środkowe stany USA, US Gov Arizona, US Gov Teksas, US Gov Wirginia, Wschodnie stany USA, Wschodnie stany USA 2, Zachodnie stany USA, Zachodnie stany USA 2, Zachodnie Zjednoczone Królestwo i Zachodnio-środkowe stany USA. |  Zachodnie stany USA 2 jest domyślną lokalizacją.
    **Typ magazynu** | Ta właściwość służy do określania typu dysków, które mają zostać przydzielone na platformie Azure. Dla jako — lokalne zmiany rozmiaru można określić typ dysku docelowego jako dyski zarządzane w warstwie Premium lub standardowa — managed disks. W przypadku ustalania rozmiaru na podstawie wydajności, można określić typ dysku docelowego jako automatyczne dysków zarządzane w warstwie Premium lub standardowa — managed disks. Po określeniu typu magazynu jako automatyczne zalecenie dysku jest określane na podstawie danych wydajności dysków (operacje We/Wy i przepływność). Na przykład, jeśli chcesz osiągnąć [pojedynczego wystąpienia maszyny Wirtualnej umowa SLA na poziomie 99,9%](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/), możesz chcieć określić typ magazynu jako dyski zarządzane w warstwie Premium, co daje pewność, że wszystkie dyski w ocenie zaleca się jako dyski zarządzane w warstwie Premium. Usługa Azure Migrate obsługuje dyski zarządzane tylko na potrzeby oceny migracji. | Wartość domyślna to dysków zarządzanych w warstwie Premium (przy użyciu kryterium ustalania rozmiaru jako *jako lokalnego rozmiaru*).
    **Wystąpienia zarezerwowane** |  Możesz również określić, czy masz [wystąpienia zarezerwowane](https://azure.microsoft.com/pricing/reserved-vm-instances/) na platformie Azure, a usługa Azure Migrate odpowiednio oszacuje koszt. Wystąpienia zarezerwowane nie mają zastosowania do regionów suwerennych (Azure Government, Niemcy i Chiny) i są one zastosowanie tylko do oferty płatności w usłudze Azure Migrate. | Wartość domyślna tej właściwości to 3 lat zarezerwowane wystąpienia.
    **Kryterium rozmiaru** | Kryterium do użycia przez usługę Azure Migrate w celu określenia odpowiedniego rozmiaru dla maszyn wirtualnych platformy Azure. Rozmiar maszyn wirtualnych możesz ustalać *na podstawie wydajności* lub rozmiaru *maszyn lokalnych* bez uwzględniania historii wydajności. | Opcja domyślna to określanie rozmiaru na podstawie wydajności.
    **Historia wydajności** | Przedział czasu uwzględniany podczas oceny wydajności maszyn wirtualnych. Ta właściwość ma zastosowanie tylko w przypadku *ustalania rozmiaru na podstawie wydajności*. | Wartość domyślna to jeden dzień.
    **Użycie percentyla** | Wartość percentyla próbki wydajności uwzględniana w celu prawidłowego określenia rozmiaru. Ta właściwość ma zastosowanie tylko w przypadku *ustalania rozmiaru na podstawie wydajności*.  | Domyślnie jest to 95. percentyl.
    **Serie maszyn wirtualnych** | Określić możesz serie maszyn wirtualnych, które mają być brane pod uwagę podczas ustalania właściwego rozmiaru. Jeśli na przykład masz środowisko produkcyjne, którego nie zamierzasz migrować do maszyn wirtualnych serii A na platformie Azure, możesz wykluczyć serię A z listy serii, a ustalanie właściwego rozmiaru zostanie wykonane tylko przy użyciu wybranych serii. | Domyślnie są zaznaczone wszystkie serię maszyn wirtualnych.
    **Współczynnik komfortu** | Podczas oceny usługa Azure Migrate uwzględnia bufor (współczynnik komfortu). Jest on stosowany do wszystkich danych użycia maszyn wirtualnych (procesora, pamięci, dysku i sieci). Współczynnik komfortu uwzględnia kwestie, takie jak okresowe użycie, krótka historia wydajności i prawdopodobne zwiększenie użycia w przyszłości.<br/><br/> Na przykład 10-rdzeniowa maszyna wirtualna o użyciu na poziomie 20% jest w normalnych warunkach równoważna 2-rdzeniowej maszynie wirtualnej. Jednak wynik zastosowania współczynnika komfortu o wartości 2 daje 4-rdzeniową maszynę wirtualną. | Ustawienie domyślne to 1,3x.
    **Oferta** | [Oferta platformy Azure](https://azure.microsoft.com/support/legal/offer-details/), w której przeprowadzono rejestrację. | Ustawienie domyślne to [Płatność zgodnie z rzeczywistym użyciem](https://azure.microsoft.com/offers/ms-azr-0003p/).
    **Waluta** | Twoja waluta rozliczeniowa. | Wartość domyślna to USD.
    **Rabat (%)** | Dowolny rabat skojarzony z daną subskrypcją, stosowany do całej oferty platformy Azure. | Ustawienie domyślne to 0%.
    **Czas pracy maszyny wirtualnej** | Jeśli Twoje maszyny wirtualne nie będą działać 24 x 7 w systemie Azure, można określić czas trwania (liczba dni w miesiącu) i liczby godzin dziennie, dla którego one będzie działać i oszacowanie kosztów związanych będzie odbywać się odpowiednio. | Wartość domyślna to 31 dni miesięcznie i 24 godziny na dobę.
    **Korzyść użycia hybrydowego platformy Azure** | Określ, jeśli masz pakiet Software Assurance i kwalifikujesz się do otrzymania [korzyści użycia hybrydowego platformy Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/). W przypadku wybrania opcji Yes maszyny wirtualne z systemem Windows są wyceniane jak platforma Azure z systemami innymi niż Windows. | Wartość domyślna to Yes.

3. Kliknij przycisk **Zapisz** można zaktualizować oceny.


## <a name="next-steps"></a>Kolejne kroki

[Dowiedz się więcej](concepts-assessment-calculation.md) na temat sposobu obliczania ocen.
