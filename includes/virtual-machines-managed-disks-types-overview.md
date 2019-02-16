---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/22/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 7f5583bfd6089362aef51285643f5fc920005242
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/16/2019
ms.locfileid: "56331233"
---
# <a name="what-disk-types-are-available-in-azure"></a>Jakie typy dysków są dostępne na platformie Azure?

Usługi Azure managed disks jest obecnie oferuje cztery typy dysków, z których trzy są ogólnie dostępne (GA) oraz jedną, która jest obecnie dostępna w wersji zapoznawczej. Te cztery typy dysków każdego mieć własnych scenariuszy dla odpowiedniego obiektu docelowego.

## <a name="disk-comparison"></a>Porównanie dysków

W poniższej tabeli przedstawiono porównanie ultra dysków (wersja zapoznawcza) — wersja premium solid-Województwo dyski (SSD), SSD w warstwie standardowa i standardowych dysków twardych (HDD) dla dysków managed disks, aby ułatwić wybór rozwiązania do zastosowania.

|   | Największa dysków (wersja zapoznawcza)   | Premium, SSD   | Standardowa, SSD   | Standardowa, dysk twardy   |
|---------|---------|---------|---------|---------|
|Typ dysku   |SSD   |SSD   |SSD   |HDD   |
|Scenariusz   |Obciążeń intensywnie korzystających z operacji We/Wy, takich jak SAP HANA, baz danych w warstwie najwyższego (na przykład SQL, Oracle) i innych obciążeń dużej transakcji.   |Obciążenia produkcyjne i wrażliwe na wydajność   |Serwery sieci Web, aplikacje dla przedsiębiorstw używana w niewielkim stopniu i tworzenie i testowanie   |Tworzenie kopii zapasowej, zastosowania niekrytyczne, rzadkie dostępu   |
|Rozmiar dysku   |gibibajt 65 536 (GiB) (wersja zapoznawcza)   |4095 giB (GA), 32 767 GiB (wersja zapoznawcza)    |4095 GiB (GA), 32 767 GiB (wersja zapoznawcza)   |4095 giB (GA), 32 767 GiB (wersja zapoznawcza)   |
|Maksymalna przepływność   |2000 MiB/s (wersja zapoznawcza)   |250 MiB (GA) / s, 750 MiB/s (wersja zapoznawcza)   |60 MiB/s (GA), 500 MiB/s (wersja zapoznawcza)   |60 Mib/s (GA), 500 MiB/s (wersja zapoznawcza)   |
|Maks. IOPS   |160,000 (wersja zapoznawcza)   |7500 (GA), 20 000 (wersja zapoznawcza)   |500 (GA), 2000 (wersja zapoznawcza)   |500 (GA), 2000 (wersja zapoznawcza)   |

## <a name="ultra-disks-preview"></a>Największa dysków (wersja zapoznawcza)

Największa dyskami platformy Azure (wersja zapoznawcza) dostarczać wysokiej przepływności, wysoka operacje We/Wy i magazynu dyskowego niskie opóźnienia dla maszyn wirtualnych IaaS platformy Azure. Niektóre dodatkowe korzyści ultra dyski obejmują możliwość dynamicznie zmieniać wydajność dysku, wraz z obciążeń, bez konieczności ponownego uruchamiania maszyn wirtualnych. Największa dyski są odpowiednie dla obciążeń intensywnie korzystających z danych, takich jak SAP HANA, najwyższej warstwy bazy danych i transakcji duże obciążenia. Największa dyski mogą być używane tylko jako dyski z danymi. Zalecamy używanie dysków premium SSD jako dyski systemu operacyjnego.

### <a name="performance"></a>Wydajność

Podczas aprowizowania dysku ultra niezależnie skonfigurować pojemność i wydajność dysku. Największa dyski są dostępne w kilku stałych rozmiarach od 4 GiB maksymalnie 64 TiB i są wyposażone w modelu konfiguracji elastyczna wydajność umożliwiająca można niezależnie konfigurować operacje We/Wy i przepływność.

Niektóre kluczowe funkcje Ultra dyski są:

- Pojemność dysku: Zakresy pojemności dysków Ultra z 4 GiB rozmiarze do 64 TiB.
- Disk IOPS: Dyski Ultra obsługują operacje We/Wy limitów 300 operacji We/Wy/GiB, maksymalnie 160 KB operacje We/Wy na dysk. Uzyskanie operacje We/Wy, które należy aprowizować, upewnij się, że wybrany dysk operacje We/Wy mniejsza niż na SEKUNDĘ maszyny Wirtualnej. Minimalna dysku operacje We/Wy są 100 operacji We/Wy.
- Przepływność dysku: Z dyskami najwyższej przepływności pojedynczego dysku wynoszący 256 KiB/s dla każdego aprowizowane operacje We/Wy, maksymalnie 2000 MB/s na dysk (gdzie MB/s = 10 ^ 6 bajtów na sekundę). Przepływność dysku minimalną jest 1 MiB.

### <a name="disk-size"></a>Rozmiar dysku

|Rozmiar dysku (GiB)  |Limity operacji We/Wy  |Limit przepływności (MB/s)  |
|---------|---------|---------|
|4     |1,200         |300         |
|8     |2,400         |600         |
|16     |4,800         |1,200         |
|32     |9,600         |2000         |
|64     |19,200         |2000         |
|128     |38,400         |2000         |
|256     |76,800         |2000         |
|512     |80,000         |2000         |
|1024 – 65 536 (rozmiary, w tym zakresie, zwiększając w przyrostach co 1 TiB)     |160,000         |2000         |

### <a name="preview-scope-and-limitations"></a>Zakres (wersja zapoznawcza) i ograniczenia

W trakcie okresu zapoznawczego ultra dyski:

- Są obsługiwane w regionie wschodnie stany USA 2 w pojedynczej strefie dostępności  
- Należy używać tylko z użyciem strefy dostępności (zestawy dostępności i jednego wdrożenia maszyn wirtualnych poza strefy będą ma możliwość dołączenia dysku ultra)
- Są obsługiwane tylko na maszynach wirtualnych v3 ES/DS
- Są dostępne tylko jako dyski z danymi i tylko rozmiar sektora fizycznego 4k pomocy technicznej  
- Może być utworzony tylko jako pustych dysków  
- Obecnie można wdrożyć tylko przy użyciu szablonów usługi Azure Resource Manager, interfejsu wiersza polecenia i zestawu SDK języka python.
- Nie obsługuje jeszcze migawki dysków maszyny Wirtualnej obrazy, zestawy dostępności, zestawy skalowania maszyn wirtualnych i usługa Azure disk encryption.
- Nie obsługuje jeszcze integracji z usługą Azure Backup lub usługi Azure Site Recovery.
- Podobnie jak w przypadku [większość wersji zapoznawczych](https://azure.microsoft.com/support/legal/preview-supplemental-terms/), nie należy używać tej funkcji dla obciążeń produkcyjnych do ogólnodostępnej (GA).

## <a name="premium-ssd"></a>Premium, SSD

Usługa Azure premium SSD dostarczać obsługę przez dyski o wysokiej wydajności i niskich opóźnieniach dla maszyn wirtualnych (VM) z operacji wejścia/wyjścia (IO)-intensywnych obciążeń. Aby móc korzystać z szybkości i wydajności dysków usługi premium storage, należy przeprowadzić migrację istniejących dysków maszyny Wirtualnej na dyski SSD w warstwie Premium. Dyski SSD w warstwie Premium są odpowiednie dla aplikacji produkcyjnych o kluczowym znaczeniu.

### <a name="disk-size"></a>Rozmiar dysku

Rozmiary oznaczone gwiazdką są obecnie dostępne w wersji zapoznawczej.

| Rozmiary dysków SSD — wersja Premium  | P4               | P6               | P10             | P15 | P20              | S30              | P40              | P50              | P60*              | P70*              | P80*              |
|---------------------|---------------------|---------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|
| Rozmiar dysku w GiB           | 32             | 64             | 128            | 256  | 512            | 1,024    | 2,048     | 4,095    | 8192     | 16,384     | 32,767     |
| Liczba operacji wejścia/wyjścia na sekundę na dysk       | Do 120 | Maksymalnie 240              | Maks. 500              | Maksymalnie 1100 | Maksymalnie 2300              | Maksymalnie 5000              | Maksymalnie 7500             | Maksymalnie 7500              | Maksymalnie 12 500              | Maksymalnie 15 000              | Maksymalnie 20 000              |
| Przepływność na dysk | Maksymalnie 25 MiB/s | Maksymalnie 50 MiB/s | Maksymalnie 100 MiB/s | Maksymalnie 125 MiB/s | Maksymalnie 150 MiB/s | Maksymalnie 200 MiB/s | Do 250 MiB/s | Do 250 MiB/s| Maksymalnie 480 MiB/s | Do 750 MiB/s | Do 750 MiB/s |

## <a name="standard-ssd"></a>Standardowa, SSD

Azure standardowe dyski SSD są opcji ekonomicznego magazynowania, zoptymalizowanych pod kątem obciążeń wymagających spójną wydajność na niższych poziomach operacji We/Wy. SSD w warstwie standardowa oferuje środowisko dobre klasy podstawowej dla osób, które chcesz przenieść do chmury, zwłaszcza, jeśli występują problemy z wariancję obciążeń działających na dysk twardy rozwiązań w środowisku lokalnym. Standardowa SSD zapewniają lepsze dostępności, spójności, niezawodności i opóźnienia w porównaniu do dysków HDD. Standardowe dyski SSD są odpowiednie dla serwerów sieci Web, niski serwerów aplikacji operacje We/Wy, aplikacje dla przedsiębiorstw używana w niewielkim stopniu i obciążeń deweloperskich/testowych.

### <a name="disk-size"></a>Rozmiar dysku

Rozmiary oznaczone gwiazdką są obecnie dostępne w wersji zapoznawczej.

| Standardowe rozmiary dysków SSD  | E10               | E15               | E20             | E30 | E40              | E50              | E60*              | E70*              | E80*              |
|---------------------|---------------------|---------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|
| Rozmiar dysku w GiB           | 128             | 256             | 512            | 1,024  | 2,048            | 4,095     | 8192     | 16,384     | 32,767    |
| Liczba operacji wejścia/wyjścia na sekundę na dysk       | Maks. 500              | Maks. 500              | Maks. 500              | Maks. 500 | Maks. 500              | Maks. 500              | Maks. 500             | Maks. 500              | Maksymalnie 1300              | Do 2000              | Do 2000              |
| Przepływność na dysk | Maksymalnie 60 MiB/s | Maksymalnie 60 MiB/s | Maksymalnie 60 MiB/s | Maksymalnie 60 MiB/s | Maksymalnie 60 MiB/s | Maksymalnie 60 MiB/s | Maksymalnie 60 MiB/s | Maksymalnie 60 MiB/s| Maksymalnie 300 MiB/s |  Maks. 500 MiB/s | Maks. 500 MiB/s |

## <a name="standard-hdd"></a>Standardowa, dysk twardy

Azure standardowych dysków twardych dostarczanie obsługi niezawodne, niedrogie dysków dla maszyn wirtualnych obciążeń niewrażliwego na opóźnienia. Obsługuje również obiekty BLOB, tabel, kolejek i plików. Dzięki usłudze standard storage dane są przechowywane dyski twarde (HDD). Podczas pracy z maszynami wirtualnymi, używając dysków SSD i HDD w warstwie standardowa dla scenariuszy deweloperskich lub testowych oraz mniej krytycznych obciążeń. Magazynu w warstwie standardowa jest dostępna we wszystkich regionach platformy Azure.

### <a name="disk-size"></a>Rozmiar dysku

Rozmiary oznaczone gwiazdką są obecnie dostępne w wersji zapoznawczej.

| Typ dysku standardowego  | S4               | S6               | S10             | S15 | S20              | S30              | S40              | S50              | S60*              | S70*              | S80*              |
|---------------------|---------------------|---------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|
| Rozmiar dysku w GiB          | 32             | 64             | 128            | 256  | 512            | 1,024    | 2,048     | 4,095    | 8192     | 16,384     | 32,767     |
| Liczba operacji wejścia/wyjścia na sekundę na dysk       | Maks. 500              | Maks. 500              | Maks. 500              | Maks. 500 | Maks. 500              | Maks. 500              | Maks. 500             | Maks. 500              | Maksymalnie 1300              | Do 2000              | Do 2000              |
| Przepływność na dysk | Maksymalnie 60 MiB/s | Maksymalnie 60 MiB/s | Maksymalnie 60 MiB/s | Maksymalnie 60 MiB/s | Maksymalnie 60 MiB/s | Maksymalnie 60 MiB/s | Maksymalnie 60 MiB/s | Maksymalnie 60 MiB/s| Maksymalnie 300 MiB/s | Maks. 500 MiB/s | Maks. 500 MiB/s |

## <a name="billing"></a>Rozliczenia

Korzystając z dysków zarządzanych, zastosuj następujące zagadnienia dotyczące rozliczeń:

- Typ dysku
- dysk zarządzany rozmiar
- Migawki
- Wychodzące transfery danych
- Liczba transakcji

**Rozmiar dysku zarządzanego**: opłaty za dyski zarządzane są naliczane zarezerwowany rozmiar. Usługi Azure maps zaprowizowany rozmiar (z zaokrągleniem) do najbliższej rozmiaru dysku oferowane przez użytkownika. Szczegółowe informacje o oferowanych rozmiary dysków można zobaczyć w poprzednich tabelach. Każdy dysk mapuje do oferty rozmiar dysku obsługiwane i jest rozliczana w związku z tym. Na przykład, jeśli zainicjowano obsługę administracyjną 200 GiB SSD w warstwie standardowa, jest on mapowany ofertę rozmiar dysku E15 (256 GiB). Rozliczenia dla dowolnego dysku jest proporcjonalnie do liczby godzin przy użyciu miesięczna oferta usługi Premium Storage. Na przykład jeśli zainicjowano obsługę administracyjną E10 dysku, a następnie usunięte 20 godzin, są naliczane dla oferty E10 jest naliczany proporcjonalnie do 20 godzin. Jest to niezależnie od ilości rzeczywiste dane zapisane na dysku.

**Migawki**: Migawki są rozliczane na podstawie rozmiaru używane. Na przykład jeśli utworzysz migawkę dysku zarządzanego z zaprowizowaną pojemnością 64 GiB i rzeczywistym użyciem danych rozmiaru 10 GiB migawki jest zapłacisz tylko za użyte dane o rozmiarze od 10 GiB.