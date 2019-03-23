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
ms.openlocfilehash: 6eae536bd19a2c0e5707d8e0b379774b6eb2707a
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2019
ms.locfileid: "58395616"
---
# <a name="what-disk-types-are-available-in-azure"></a>Jakie typy dysków są dostępne na platformie Azure?

Usługi Azure managed disks jest obecnie oferuje cztery typy dysków, z których trzy są ogólnie dostępne (GA) oraz jedną, która jest obecnie dostępna w wersji zapoznawczej. Te cztery typy dysków każdego mieć własnych scenariuszy dla odpowiedniego obiektu docelowego.

## <a name="disk-comparison"></a>Porównanie dysków

Poniższa tabela zawiera porównanie ultra solid-Województwo dyski (SSD) (wersja zapoznawcza), dysk SSD w warstwie premium, SSD w warstwie standardowa i standardowych dysków twardych (HDD) dla dysków zarządzanych, aby ułatwić wybór rozwiązania do zastosowania.

|   | Największa SSD (wersja zapoznawcza)   | Premium, SSD   | Standardowa, SSD   | Standardowa, dysk twardy   |
|---------|---------|---------|---------|---------|
|Typ dysku   |SSD   |SSD   |SSD   |HDD   |
|Scenariusz   |Obciążeń intensywnie korzystających z operacji We/Wy, takich jak SAP HANA, baz danych w warstwie najwyższego (na przykład SQL, Oracle) i innych obciążeń dużej transakcji.   |Obciążenia produkcyjne i wrażliwe na wydajność   |Serwery sieci Web, aplikacje dla przedsiębiorstw używana w niewielkim stopniu i tworzenie i testowanie   |Tworzenie kopii zapasowej, zastosowania niekrytyczne, rzadkie dostępu   |
|Rozmiar dysku   |gibibajt 65 536 (GiB) (wersja zapoznawcza)   |32,767 GiB    |32,767 GiB   |32,767 GiB   |
|Maksymalna przepływność   |2000 MiB/s (wersja zapoznawcza)   |900 MiB/s   |750 MiB/s   |500 MiB/s   |
|Maks. IOPS   |160,000 (wersja zapoznawcza)   |20,000   |6,000   |2000   |

## <a name="ultra-ssd-preview"></a>Największa SSD (wersja zapoznawcza)

Dyski SSD ultra platformy Azure (wersja zapoznawcza) dostarczać wysokiej przepływności, wysoka operacje We/Wy i magazynu dyskowego niskie opóźnienia dla maszyn wirtualnych IaaS platformy Azure. Niektóre dodatkowe korzyści ultra SSD obejmują możliwość dynamicznie zmieniać wydajność dysku, wraz z obciążeń, bez konieczności ponownego uruchamiania maszyn wirtualnych. Największa dyski SSD są odpowiednie dla obciążeń intensywnie korzystających z danych, takich jak SAP HANA, najwyższej warstwy bazy danych i transakcji duże obciążenia. Największa dysków SSD należy używać tylko jako dyski z danymi. Zalecamy używanie dysków premium SSD jako dyski systemu operacyjnego.

### <a name="performance"></a>Wydajność

Podczas aprowizowania dysku ultra niezależnie skonfigurować pojemność i wydajność dysku. Największa dyski SSD są dostępne w kilku stałych rozmiarach od 4 GiB maksymalnie 64 TiB i są wyposażone w modelu konfiguracji elastyczna wydajność umożliwiająca można niezależnie konfigurować operacje We/Wy i przepływność.

Niektóre kluczowe funkcje Ultra dyski SSD są:

- Pojemność dysku: Największa zakresy pojemność dysków SSD z 4 GiB rozmiarze do 64 TiB.
- Disk IOPS: Największa SSD obsługuje operacje We/Wy limitów 300 operacji We/Wy/GiB, maksymalnie 160 KB operacje We/Wy na dysk. Uzyskanie operacje We/Wy, które należy aprowizować, upewnij się, że wybrany dysk operacje We/Wy mniejsza niż na SEKUNDĘ maszyny Wirtualnej. Minimalna dysku operacje We/Wy są 100 operacji We/Wy.
- Przepływność dysku: Przy użyciu najwyższej SSD przepływność pojedynczego dysku wynoszący 256 KiB/s dla każdego aprowizowane operacje We/Wy, maksymalnie 2000 MB/s na dysk (gdzie MB/s = 10 ^ 6 bajtów na sekundę). Przepływność dysku minimalną jest 1 MiB.

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

W trakcie okresu zapoznawczego ultra SSD:

- Są obsługiwane w regionie wschodnie stany USA 2 w pojedynczej strefie dostępności  
- Należy używać tylko z użyciem strefy dostępności (zestawy dostępności i jednego wdrożenia maszyn wirtualnych poza strefy będą ma możliwość dołączenia dysku ultra)
- Są obsługiwane tylko na maszynach wirtualnych v3 ES/DS
- Są dostępne tylko jako dyski z danymi i tylko rozmiar sektora fizycznego 4k pomocy technicznej  
- Może być utworzony tylko jako pustych dysków  
- Obecnie można wdrożyć tylko przy użyciu szablonów usługi Azure Resource Manager, interfejsu wiersza polecenia i zestawu SDK języka python.
- Nie obsługuje jeszcze migawki dysków maszyny Wirtualnej obrazy, zestawy dostępności, zestawy skalowania maszyn wirtualnych i usługa Azure disk encryption.
- Nie obsługuje jeszcze integracji z usługą Azure Backup lub usługi Azure Site Recovery.
- Podobnie jak w przypadku [większość wersji zapoznawczych](https://azure.microsoft.com/support/legal/preview-supplemental-terms/), nie należy używać tej funkcji dla obciążeń produkcyjnych do ogólnodostępnej (GA).
