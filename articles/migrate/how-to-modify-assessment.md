---
title: Dostosowywanie ustawień oceny usługi Azure Migrate | Dokumentacja firmy Microsoft
description: W tym artykule opisano jak skonfigurować i uruchomić ocenę dla migracji maszyn wirtualnych VMware na platformę Azure za pomocą usługi Azure migrate
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 01/10/2019
ms.author: raynew
ms.openlocfilehash: 8419d7e7a91e4cbfd0eebfe00d35bf498cf5998c
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62129827"
---
# <a name="customize-an-assessment"></a>Dostosowywanie oceny

[Usługa Azure Migrate](migrate-overview.md) tworzy oceny za pomocą właściwości domyślnych. Po utworzeniu oceny można modyfikować domyślnych właściwości, korzystając z instrukcji w tym artykule.


## <a name="edit-assessment-properties"></a>Edytuj właściwości oceny

1. Na stronie **Oceny** projektu migracji wybierz ocenę i kliknij pozycję **Edytuj właściwości**.
2. Dostosuj właściwości oceny, w oparciu o następujące informacje:

    **Ustawienie** | **Szczegóły** | **Domyślne**
    --- | --- | ---
    **Lokalizacja docelowa** | Lokalizacja platformy Azure, do której chcesz przeprowadzić migrację.<br/><br/> Usługa Azure Migrate obsługuje obecnie 30 regionów, w tym: Australia Południowo-Wschodnia, Australia Wschodnia, Azja Południowo-Wschodnia, Azja Wschodnia, Brazylia Południowa, Chiny Północne, Chiny Wschodnie, Europa Północna, Europa Zachodnia, Indie Południowe, Indie Środkowe, Indie Zachodnie, Japonia Wschodnia, Japonia Zachodnia, Kanada Środkowa, Kanada Wschodnia, Korea Południowa, Korea Środkowa, Niemcy Północno-Wschodnie, Niemcy Środkowe, Południowe Zjednoczone Królestwo, Południowo-środkowe stany USA, Północno-środkowe stany USA, Środkowe stany USA, US Gov Arizona, US Gov Teksas, US Gov Wirginia, Wschodnie stany USA, Wschodnie stany USA 2, Zachodnie stany USA, Zachodnie stany USA 2, Zachodnie Zjednoczone Królestwo i Zachodnio-środkowe stany USA. |  Zachodnie stany USA 2 jest domyślną lokalizacją.
    **Typ magazynu** | Ta właściwość służy do określania typu dysków, które chcesz przenieść do platformy Azure. Dla jako — lokalne zmiany rozmiaru można określić typ dysku docelowego jako dyski zarządzane w warstwie Premium lub standardowa — managed disks. W przypadku ustalania rozmiaru na podstawie wydajności, można określić typ dysku docelowego jako automatyczne dysków zarządzane w warstwie Premium lub standardowa — managed disks. Po określeniu typu magazynu jako automatyczne zalecenie dysku jest określane na podstawie danych wydajności dysków (operacje We/Wy i przepływność). Na przykład, jeśli chcesz osiągnąć [pojedynczego wystąpienia maszyny Wirtualnej umowa SLA na poziomie 99,9%](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/), możesz chcieć określić typ magazynu jako dyski zarządzane w warstwie Premium. Daje to gwarancję, że wszystkie dyski w ocenie są zalecane jako dyski zarządzane w warstwie Premium. Usługa Azure Migrate obsługuje dyski zarządzane tylko na potrzeby oceny migracji. | Wartość domyślna to dyski zarządzane w warstwie Premium (przy użyciu kryterium ustalania rozmiaru jako *jako lokalnego rozmiaru*).
    **Wystąpienia zarezerwowane** |  Możesz również określić, czy masz [wystąpienia zarezerwowane](https://azure.microsoft.com/pricing/reserved-vm-instances/) na platformie Azure, a usługa Azure Migrate odpowiednio oszacuje koszt. Wystąpienia zarezerwowane są obecnie obsługiwane tylko w przypadku ofertę płatność za rzeczywiste użycie w usłudze Azure Migrate. | Wartość domyślna tej właściwości to 3 lat zarezerwowane wystąpienia.
    **Kryterium rozmiaru** | Kryterium do użycia przez usługę Azure Migrate w celu określenia odpowiedniego rozmiaru dla maszyn wirtualnych platformy Azure. Rozmiar maszyn wirtualnych możesz ustalać *na podstawie wydajności* lub rozmiaru *maszyn lokalnych* bez uwzględniania historii wydajności. | Opcja domyślna to określanie rozmiaru na podstawie wydajności.
    **Historia wydajności** | Przedział czasu uwzględniany podczas oceny wydajności maszyn wirtualnych. Ta właściwość ma zastosowanie tylko w przypadku *ustalania rozmiaru na podstawie wydajności*. | Wartość domyślna to jeden dzień.
    **Użycie percentyla** | Wartość percentyla próbki wydajności uwzględniana w celu prawidłowego określenia rozmiaru. Ta właściwość ma zastosowanie tylko w przypadku *ustalania rozmiaru na podstawie wydajności*.  | Domyślnie jest to 95. percentyl.
    **Serie maszyn wirtualnych** | Określić możesz serie maszyn wirtualnych, które mają być brane pod uwagę podczas ustalania właściwego rozmiaru. Na przykład w przypadku środowiska produkcyjnego, który nie jest planowana migracja do maszyn wirtualnych z serii A na platformie Azure — seria A można wykluczyć z listy lub serii i doboru wielkości odbywa się tylko w wybranej serii. | Domyślnie są zaznaczone wszystkie serię maszyn wirtualnych.
    **Współczynnik komfortu** | Podczas oceny usługa Azure Migrate uwzględnia bufor (współczynnik komfortu). Jest on stosowany do wszystkich danych użycia maszyn wirtualnych (procesora, pamięci, dysku i sieci). Współczynnik komfortu uwzględnia kwestie, takie jak okresowe użycie, krótka historia wydajności i prawdopodobne zwiększenie użycia w przyszłości.<br/><br/> Na przykład 10-rdzeniowa maszyna wirtualna o użyciu na poziomie 20% jest w normalnych warunkach równoważna 2-rdzeniowej maszynie wirtualnej. Jednak wynik zastosowania współczynnika komfortu o wartości 2 daje 4-rdzeniową maszynę wirtualną. | Ustawienie domyślne to 1,3x.
    **Oferta** | [Oferta platformy Azure](https://azure.microsoft.com/support/legal/offer-details/), w której przeprowadzono rejestrację. | Ustawienie domyślne to [Płatność zgodnie z rzeczywistym użyciem](https://azure.microsoft.com/offers/ms-azr-0003p/).
    **Waluta** | Twoja waluta rozliczeniowa. | Wartość domyślna to USD.
    **Rabat (%)** | Dowolny rabat skojarzony z daną subskrypcją, stosowany do całej oferty platformy Azure. | Ustawienie domyślne to 0%.
    **Czas pracy maszyny wirtualnej** | Jeśli Twoje maszyny wirtualne nie będą działać 24 x 7 w systemie Azure, można określić czas trwania (liczba dni w miesiącu) i liczby godzin dziennie, dla którego one będzie działać i oszacowanie kosztów związanych będzie odbywać się odpowiednio. | Wartość domyślna to 31 dni w miesiącu i 24 godziny dziennie.
    **Korzyść użycia hybrydowego platformy Azure** | Określ, jeśli masz pakiet Software Assurance i kwalifikujesz się do otrzymania [korzyści użycia hybrydowego platformy Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/). W przypadku wybrania opcji Yes maszyny wirtualne z systemem Windows są wyceniane jak platforma Azure z systemami innymi niż Windows. | Wartość domyślna to Yes.

3. Kliknij przycisk **Zapisz** można zaktualizować oceny.

## <a name="faqs-on-assessment-properties"></a>Często zadawane pytania na temat właściwości oceny

### <a name="what-is-the-difference-between-as-on-premises-sizing-and-performance-based-sizing"></a>Jaka jest różnica między ustalania rozmiaru jako — w środowisku lokalnym i ustalania rozmiaru na podstawie wydajności?

Po określeniu kryterium ustalania rozmiaru jako jako on-premises zmiany rozmiaru, usługa Azure Migrate nie należy wziąć pod uwagę dane wydajności maszyn wirtualnych i rozmiarach maszyn wirtualnych na podstawie konfiguracji w środowisku lokalnym. Jeśli kryterium ustalania rozmiaru jest oparte na wydajności, zmiany rozmiaru odbywa się na podstawie danych użycia. Załóżmy na przykład, że istnieje lokalna maszyna wirtualna z 4 rdzeniami i 8 GB pamięci RAM, mająca 50% użycia procesora CPU i 50% użycia pamięci. Jeśli kryterium jest ustalanie rozmiaru jako lokalnego, zalecana jest jednostka SKU maszyny wirtualnej platformy Azure z 4 rdzeniami i 8 GB pamięci, ale jeśli kryterium ustalania rozmiaru jest oparte na wydajności, zalecana jest jednostka SKU maszyny wirtualnej z 2 rdzeniami i 4 GB, ponieważ procent użycia jest uwzględniany podczas rekomendowania rozmiaru.

Podobnie dysków, rozmiaru dysku zależy od dwóch właściwości oceny — zmiany rozmiaru typu kryterium i magazynu. Jeśli kryterium ustalania rozmiaru jest oparte na wydajności, a typ magazynu to Automatyczny, uwzględniane są wartości przepływności i liczby operacji we/wy na sekundę dysku w celu zidentyfikowania docelowego typu dysku (warstwa Standardowa lub Premium). Jeśli kryterium ustalania rozmiaru jest oparte na wydajności, a typ magazynu to Premium, zalecany jest dysk w warstwie Premium. Jednostka SKU dysku w warstwie Premium na platformie Azure jest wybierana na podstawie rozmiaru dysku lokalnego. Ta sama logika jest używana do ustalania rozmiaru dysków, gdy kryterium jest ustalanie rozmiaru jako lokalnego, a typ magazynu to warstwa Standardowa lub Premium.

### <a name="what-impact-does-performance-history-and-percentile-utilization-have-on-the-size-recommendations"></a>Jaki wpływ wydajności historii i percentyl użycia ma na zaleceń dotyczących rozmiarów?

Te właściwości dotyczą tylko ustalania rozmiaru na podstawie wydajności. Usługa Azure Migrate zbiera informacje o historii wydajności maszyn lokalnych i używa ich do określania zalecanych rozmiarów maszyn wirtualnych i typów dysków na platformie Azure.

- Urządzenie modułu zbierającego stale profiluje środowisko lokalne w celu zbierania danych użycia w czasie rzeczywistym co 20 sekund.
- Urządzenie podsumowuje 20-sekundowe próbki i tworzy jeden punkt danych co 15 minut. Aby utworzyć jeden punkt danych, urządzenie wybiera szczytową wartość z wszystkich 20-sekundowych próbek i wysyła ją do platformy Azure.
- Podczas tworzenia oceny na platformie Azure (na podstawie czasu trwania wydajności i wartości percentylu historii wydajności) usługa Azure Migrate oblicza wartość efektywnego użycia i na tej podstawie ustala rozmiar.

Jeśli na przykład ustawiono czas trwania wydajności na 1 dzień oraz wartość percentylu na 95. percentyl, usługa Azure Migrate używa punktów próbek 15-minutowych wysłanych przez moduł zbierający dla ostatniego dnia, sortuje je w kolejności rosnącej i wybiera wartość 95. percentylu jako efektywne użycie. Wartość 95. percentylu zapewnia, że zostaną zignorowane wszelkie elementy odstające, które mogą występować w przypadku wybrania 99. percentylu. Jeśli chcesz wybrać szczytowe użycie w danym okresie i nie pomijać żadnych elementów odstających, wybierz 99. percentyl.

## <a name="next-steps"></a>Kolejne kroki

[Dowiedz się więcej](concepts-assessment-calculation.md) na temat sposobu obliczania ocen.
