---
title: Dostosowywanie ocen do oceny serwera migracji platformy Azure | Dokumenty firmy Microsoft
description: W tym artykule opisano sposób dostosowywania ocen utworzonych za pomocą oceny serwera migracji usługi Azure
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/15/2019
ms.author: raynew
ms.openlocfilehash: 2cfac978b0a5af20e9e2fa1e32a7361488f20fbe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68234267"
---
# <a name="customize-an-assessment"></a>Dostosowywanie oceny

W tym artykule opisano sposób dostosowywania ocen utworzonych przez usługę Azure Migrate Server Assessment.

[Usługa Azure Migrate](migrate-services-overview.md) udostępnia centralne centrum do śledzenia odnajdowania, oceny i migracji lokalnych aplikacji i obciążeń oraz maszyn wirtualnych chmury prywatnej/publicznej na platformę Azure. Centrum udostępnia narzędzia migracji platformy Azure do oceny i migracji, a także oferty niezależnych dostawców oprogramowania innych firm (ISV).

Za pomocą narzędzia Oceny serwera migracji usługi Azure można tworzyć oceny lokalnych maszyn wirtualnych vmware i maszyn wirtualnych funkcji Hyper-V w ramach przygotowań do migracji na platformę Azure.

## <a name="about-assessments"></a>Informacje o ocenach

Istnieją dwa typy ocen, które można uruchomić przy użyciu oceny serwera migracji usługi Azure.

**Ocena** | **Szczegóły** | **Dane**
--- | --- | ---
**Oparte na wydajności** | Oceny na podstawie zebranych danych dotyczących wydajności | **Zalecany rozmiar maszyny Wirtualnej:** Na podstawie danych wykorzystania procesora i pamięci.<br/><br/> **Zalecany typ dysku (dysk zarządzany standardowy lub premium):** na podstawie usług We/Wy i przepływności dysków lokalnych.
**Jako lokalnie** | Oceny oparte na rozmiarze lokalnym. | **Zalecany rozmiar maszyny Wirtualnej:** Na podstawie lokalnego rozmiaru maszyny Wirtualnej<br/><br> **Zalecany typ dysku:** Na podstawie ustawienia typu magazynu wybranego dla oceny.


## <a name="how-is-an-assessment-done"></a>W jaki sposób przeprowadza się ocenę?

Ocena przeprowadzona w usłudze Azure Migrate Server Assessment ma trzy etapy. Ocena rozpoczyna się od analizy przydatności, a następnie dosyć rozmiaru i wreszcie miesięcznego oszacowania kosztów. Maszyna przechodzi do późniejszego etapu tylko wtedy, gdy przechodzi poprzedni. Na przykład jeśli komputer nie powiedzie się sprawdzanie przydatności platformy Azure, jest oznaczony jako nieodpowiedni dla platformy Azure i zmiany rozmiaru i wyceny nie zostaną wykonane. [Dowiedz się więcej.](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation)

## <a name="whats-in-an-assessment"></a>Co obejmuje ocena?

**Właściwość** | **Szczegóły**
--- | ---
**Lokalizacja docelowa** | Lokalizacja platformy Azure, do której chcesz przeprowadzić migrację.<br/> Ocena serwera obsługuje obecnie te regiony docelowe: Australia Wschodnia, Australia Południowo-Wschodnia, Brazylia Południowa, Kanada Środkowa, Kanada Wschodnia, Indie Środkowe, Środkowe STANY USA, Chiny Wschodnie, Chiny Północne, Azja Wschodnia, Wschodnie stany USA, Wschodnie US2, Niemcy Środkowe, Niemcy północno-wschodnie, Japonia Wschodnia, Japonia Zachodnia, Korea Środkowa, Korea Południowa, Północno-Środkowe STANY USA, Europa Północna, Południowo-Środkowe STANY USA, Azja Południowo-Wschodnia, Indie Południowe, Wielka Brytania Południowa, Wielka Brytania Zachodnia, US Gov Arizona, US Gov Texas, US Gov Virginia, Zachodnio-Środkowe STANY USA, Europa Zachodnia, Zachodnie Stany Zjednoczone i Zachodnie STANY USA2.
**Typ magazynu** | Ta właściwość służy do określenia typu dysków, które mają zostać przeniesione do platformy Azure.<br/><br/> W przypadku rozmiaru lokalnego jako lokalnego można określić typ magazynu docelowego jako dyski zarządzane w wersji Premium, dyski zarządzane przez standardowe dyski SSD lub dyski zarządzane przez standardowe dyski twarde. W przypadku rozmiaru opartego na wydajności można określić typ dysku docelowego jako dyski automatyczne, zarządzane w wersji Premium, dyski zarządzane przez standardowe dyski twarde lub dyski zarządzane przez standardowe dyski SSD.<br/><br/> Po określeniu typu magazynu jako automatycznego, zalecenie dysku odbywa się na podstawie danych wydajności dysków (IOPS i przepływności). Jeśli określisz typ magazynu jako premium/standard, ocena zaleci jednostkę SKU dysku w wybranym typie magazynu. Jeśli chcesz osiągnąć jedno wystąpienie umowy SLA maszyny Wirtualnej 99,9%, można określić typ magazynu jako dyski zarządzane w wersji Premium. Gwarantuje to, że wszystkie dyski w ocenie są zalecane jako dyski zarządzane w wersji Premium. Azure
**Wystąpienia zarezerwowane (RI)** | Ta właściwość pomaga określić, jeśli masz [wystąpienia zarezerwowane](https://azure.microsoft.com/pricing/reserved-vm-instances/) na platformie Azure, szacowania kosztów w ocenie są następnie wykonywane przy uwzględnieniu rabatów RI. Wystąpienia zarezerwowane są obecnie obsługiwane tylko dla oferty płatności zgodnie z rzeczywistym użyciem w usłudze Azure Migrate.
**Kryterium zmiany rozmiaru** | Kryterium, które ma być używane do maszyn wirtualnych o odpowiednim rozmiarze dla platformy Azure. Można wykonać *rozmiary oparte na wydajności* lub rozmiar maszyn wirtualnych *jako lokalnie,* bez uwzględnienia historii wydajności.
**Historia wydajności** | Czas trwania do rozważenia do oceny danych wydajności maszyn. Ta właściwość ma zastosowanie tylko wtedy, gdy kryterium zmiany rozmiaru jest *oparte na wydajności.*
**Użycie percentyla** | Wartość percentyla próbki wydajności uwzględniana w celu prawidłowego określenia rozmiaru. Ta właściwość ma zastosowanie tylko wtedy, gdy rozmiar jest *oparty na wydajności.*
**Serie maszyn wirtualnych** |     Określić możesz serie maszyn wirtualnych, które mają być brane pod uwagę podczas ustalania właściwego rozmiaru. Na przykład jeśli masz środowisko produkcyjne, które nie planujesz migracji do maszyn wirtualnych serii A na platformie Azure, możesz wykluczyć serię A z listy lub serii, a zmiana rozmiaru po prawej stronie jest wykonywana tylko w wybranej serii.
**Współczynnik komfortu** | Ocena programu Azure Migrate Server uwzględnia bufor (współczynnik komfortu) podczas oceny. Jest on stosowany do wszystkich danych użycia maszyn wirtualnych (procesora, pamięci, dysku i sieci). Współczynnik komfortu uwzględnia kwestie, takie jak okresowe użycie, krótka historia wydajności i prawdopodobne zwiększenie użycia w przyszłości.<br/><br/> Na przykład 10-rdzeniowa maszyna wirtualna o użyciu na poziomie 20% jest w normalnych warunkach równoważna 2-rdzeniowej maszynie wirtualnej. Jednak wynik zastosowania współczynnika komfortu o wartości 2 daje 4-rdzeniową maszynę wirtualną.
**Oferta** | [Oferta platformy Azure](https://azure.microsoft.com/support/legal/offer-details/), w której dokonano rejestracji. Zgodnie z tym usługa Azure Migrate odpowiednio szacuje koszty.
**Waluta** | Twoja waluta rozliczeniowa.
**Rabat (%)** | Dowolny rabat skojarzony z daną subskrypcją, stosowany do całej oferty platformy Azure.<br/> Ustawienie domyślne to 0%.
**Czas pracy maszyny wirtualnej** | Jeśli maszyny wirtualne nie będą działać 24x7 na platformie Azure, można określić czas trwania (liczba dni w miesiącu i liczbę godzin dziennie), dla których będą uruchomione i szacunki kosztów zostaną wykonane odpowiednio.<br/> Wartość domyślna to 31 dni w miesiącu i 24 godziny dziennie.
**Korzyść użycia hybrydowego platformy Azure** | Określ, czy masz gwarancję oprogramowania i kwalifikujesz się do korzystania z [usługi Azure Hybrid Benefit.](https://azure.microsoft.com/pricing/hybrid-use-benefit/) W przypadku wybrania opcji Yes maszyny wirtualne z systemem Windows są wyceniane jak platforma Azure z systemami innymi niż Windows. Wartość domyślna to Yes.


## <a name="edit-assessment-properties"></a>Edytowanie właściwości oceny

Aby edytować właściwości oceny po utworzeniu oceny, wykonaj następujące czynności:

1. W projekcie migracji platformy Azure kliknij pozycję **Serwery**.
2. W **usłudze Azure Migrate: Server Assessment**kliknij liczbę ocen.
3. W **oknie Ocena**kliknij odpowiednią ocenę > Edytuj **właściwości**.
5. Dostosuj właściwości oceny zgodnie z powyższą tabelą.
6. Kliknij **przycisk Zapisz,** aby zaktualizować ocenę.


Podczas tworzenia oceny można również edytować właściwości oceny.


## <a name="next-steps"></a>Następne kroki

[Dowiedz się więcej](concepts-assessment-calculation.md) na temat sposobu obliczania ocen.
