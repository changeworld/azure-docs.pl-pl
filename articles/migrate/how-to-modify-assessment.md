---
title: Dostosowywanie oceny dla oceny Server migracji platformy Azure | Dokumentacja firmy Microsoft
description: Opisuje sposób dostosowywania oceny utworzone wraz z oceną serwera migracji platformy Azure
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/09/2019
ms.author: raynew
ms.openlocfilehash: 8b200ce3d6e73a575b1b89d82a9323d58f435a48
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807926"
---
# <a name="customize-an-assessment"></a>Dostosowywanie oceny

W tym artykule opisano sposób dostosowywania oceny utworzone przez oceny Server migracji platformy Azure.

[Usługa Azure Migrate](migrate-services-overview.md) udostępnia centralną Centrum do śledzenia wykrywania, oceny i migracji aplikacji lokalnych i obciążeń i chmury prywatne/publiczne maszyn wirtualnych na platformie Azure. Centrum zapewnia usługa Azure Migrate narzędzia do oceny i migracji, a także oferty Niezależnym dostawcą oprogramowania niezależnie od innych firm.

Narzędzie oceny Server migracji platformy Azure utworzyć oceny dla lokalnych maszyn wirtualnych z programu VMware i maszyn wirtualnych funkcji Hyper-V, w ramach przygotowania do migracji na platformę Azure. 

## <a name="about-assessments"></a>Temat ocen

Istnieją dwa typy ocen, które można uruchomić za pomocą oceny Server migracji platformy Azure.

**Ocena** | **Szczegóły** | **Dane**
--- | --- | ---
**Na podstawie wydajności** | Na podstawie danych wydajności zebranych ocen | **Zalecany rozmiar maszyny Wirtualnej**: Na podstawie danych użycia procesora CPU i pamięci.<br/><br/> **Zalecany typ dysku (standardowa lub premium dysku zarządzanego)** : Na podstawie operacji We/Wy i przepływność dysków w środowisku lokalnym.
**Jako lokalne** | Oceny na podstawie lokalnie zmiany rozmiaru. | **Zalecany rozmiar maszyny Wirtualnej**: Na podstawie rozmiaru maszyny Wirtualnej w środowisku lokalnym<br/><br> **Zalecany typ dysku**: Na podstawie magazynu typu ustawienia wybrane dla oceny.


## <a name="how-is-an-assessment-done"></a>Jak odbywa się oceny

Ocena usługi Azure Migrate ma trzy etapy. Ocena zaczyna się od analizy przydatności, po której następuje zmiana rozmiaru, i na koniec comiesięczny szacowania kosztów. Maszyna tylko przenosi na późniejszym etapie Jeśli przekazuje poprzedniego. Na przykład w przypadku niepowodzenia sprawdzania gotowości usługi Azure machine on jest oznaczony jako nieodpowiedni dla platformy Azure, a zmiany rozmiaru i wyceny nie będzie odbywać się.

## <a name="whats-in-an-assessment"></a>Co obejmuje ocena?

**Property** | **Szczegóły**
--- | ---
**Lokalizacja docelowa** | Lokalizacja platformy Azure, do której chcesz przeprowadzić migrację.<br/> Usługa Azure Migrate obsługuje obecnie następujące regiony docelowe: Australia Wschodnia, Australia Południowo-Wschodnia, Brazylia Południowa, Kanada Środkowa, Kanada Wschodnia, Indie środkowe, środkowe stany USA, Chiny wschodnie, Chiny Północne, Azja Wschodnia, wschodnie stany USA, wschodnie stany USA 2, Niemcy środkowe, Niemcy północno-wschodnie, Japonia Wschodnia, Japonia Zachodnia, Korea środkowa, Korea Południowa, Północna Środkowe stany USA, Europa Północna, południowo-środkowe stany USA, Azja południowo-wschodnia, Indie Południowe, południowe Zjednoczone Królestwo, zachodnie Zjednoczone Królestwo, administracja Gov Teksas stany USA, administracja USA — Arizona USA — Wirginia, środkowe stany USA Zachodnia, Europa Zachodnia, Indie Zachodnie, Indie, zachodnie stany USA i zachodnie stany USA 2.<br/> Domyślny region docelowy to Zachodnie stany USA 2.
**Typ magazynu** | Standardowa HHD dysków/Standard dysków SSD dyski/Premium.<br/> Po określeniu typu magazynu jako automatyczne w ocenie zalecenie dysku opiera się na dane wydajności dysków (operacje We/Wy i przepływność).<br/> Jeśli określisz typ magazynu jako standardowa/Premium, wybrana zaleca oceny dysk jednostki SKU w ramach typu magazynu.<br/> Jeśli chcesz uzyskać jedno wystąpienie maszyny Wirtualnej w umowie SLA na poziomie 99,9%, można ustawić typ magazynu jako dyski zarządzane w warstwie Premium. Wszystkie dyski w ocenie będą następnie zalecane jako dyski zarządzane w warstwie Premium. <br/> Usługa Azure Migrate obsługuje tylko dyski zarządzane na potrzeby oceny migracji.<br/> 
**Wystąpienia zarezerwowane (RI)** | Należy określić tę właściwość, jeśli zostały zarezerwowane wystąpienia w systemie Azure. Szacunkowych kosztów w ocenie będzie uwzględniać rabaty wystąpień Zarezerwowanych. Wystąpienia zarezerwowane są obecnie obsługiwane tylko w przypadku płatności oferty w usłudze Azure Migrate.
**Kryterium rozmiaru** | Używane do odpowiedniego rozmiaru maszyn wirtualnych. Zmiana rozmiaru może być oparte na wydajności lub **jako lokalne**, bez uwzględniania historii wydajności.
**Historia wydajności** | Czas trwania uwzględniany podczas oceny wydajności maszyn wirtualnych. Ta właściwość ma zastosowanie tylko w przypadku, gdy ustalania rozmiaru jest oparte na wydajności.
**Użycie percentyla** | Wartość percentyla próbki wydajności używany do właściwych rozmiarów maszyn wirtualnych. Ta właściwość ma zastosowanie tylko w przypadku, gdy ustalania rozmiaru jest oparte na wydajności.
**Serie maszyn wirtualnych** | Serie maszyn wirtualnych używane do szacowania rozmiaru. Jeśli na przykład masz środowisko produkcyjne, którego nie zamierzasz migrować do maszyn wirtualnych serii A na platformie Azure, możesz wykluczyć serię A z listy serii. Ustalanie właściwego rozmiaru zostanie wykonane tylko przy użyciu wybranych serii.
**Współczynnik komfortu** | Ocena serwera migracji platformy Azure uwzględnia bufor (współczynnik komfortu) podczas oceny. Jest on stosowany do wszystkich danych użycia maszyn wirtualnych (procesora, pamięci, dysku i sieci). Współczynnik komfortu uwzględnia kwestie, takie jak okresowe użycie, krótka historia wydajności i prawdopodobne zwiększenie użycia w przyszłości.<br/><br/> Na przykład 10-rdzeniowa maszyna wirtualna o użyciu na poziomie 20% jest w normalnych warunkach równoważna 2-rdzeniowej maszynie wirtualnej. Jednak wynik zastosowania współczynnika komfortu o wartości 2 daje 4-rdzeniową maszynę wirtualną.
**Oferta** | [Oferta platformy Azure](https://azure.microsoft.com/support/legal/offer-details/), w której dokonano rejestracji. Zgodnie z tym usługa Azure Migrate odpowiednio szacuje koszty.
**Waluta** | Twoja waluta rozliczeniowa. 
**Rabat (%)** | Dowolny rabat skojarzony z daną subskrypcją, stosowany do całej oferty platformy Azure.<br/> Ustawienie domyślne to 0%.
**Czas pracy maszyny wirtualnej** | Jeśli Twoje maszyny wirtualne nie będą działać 24 x 7 w systemie Azure, można określić czas trwania (liczba dni w miesiącu) i liczby godzin dziennie, dla którego one będzie działać i oszacowanie kosztów związanych będzie odbywać się odpowiednio.<br/> Wartość domyślna to 31 dni w miesiącu i 24 godziny dziennie.
**Korzyść użycia hybrydowego platformy Azure** | Określa, czy masz pakiet software assurance i kwalifikują się do [korzyść użycia hybrydowego platformy Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/). W przypadku wybrania opcji Yes maszyny wirtualne z systemem Windows są wyceniane jak platforma Azure z systemami innymi niż Windows. | Wartość domyślna to Yes.


## <a name="edit-assessment-properties"></a>Edytuj właściwości oceny

Aby edytować właściwości oceny po utworzeniu oceny, wykonaj następujące czynności:

1. W projekcie usługi Azure Migrate kliknij **serwerów**.
2. W **usługi Azure Migrate: Ocena serwera**, kliknij przycisk liczba ocen.
3. W **oceny**, kliknij odpowiednie oceny > **Edytuj właściwości**.
5. Dostosuj właściwości oceny zgodnie z powyższej tabeli.
6. Kliknij przycisk **Zapisz** można zaktualizować oceny.


Jeśli tworzysz ocenę, można również edytować właściwości oceny.


## <a name="next-steps"></a>Następne kroki

[Dowiedz się więcej](concepts-assessment-calculation.md) na temat sposobu obliczania ocen.
