---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 08/15/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 87e130d3a4569971bffb9b1ac2e189babb900225
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/14/2019
ms.locfileid: "70997692"
---
# <a name="what-disk-types-are-available-in-azure"></a>Jakie typy dysków są dostępne na platformie Azure?

Usługa Azure Managed disks obecnie oferuje cztery typy dysków, a każdy typ jest skierowany do określonych scenariuszy klientów.

## <a name="disk-comparison"></a>Porównanie dysków

Poniższa tabela zawiera porównanie dysków twardych, dysków półprzewodnikowych (SSD), standardowego dysku SSD i standardowych dysków twardych na potrzeby dysków zarządzanych, które ułatwią podjęcie decyzji o tym, czego używać.

|   | Ultra Disk   | SSD w warstwie Premium   | SSD w warstwie Standardowa   | HDD w warstwie Standardowa   |
|---------|---------|---------|---------|---------|
|Typ dysku   |SSD   |SSD   |SSD   |HDD   |
|Scenariusz   |Obciążenia intensywnie korzystające z operacji we/wy, takie jak SAP HANA, baz danych najwyższej warstwy (na przykład SQL, Oracle) i innych obciążeń intensywnie korzystających z transakcji.   |Obciążenia produkcyjne i wrażliwe na wydajność   |Serwery sieci Web, lekko używane aplikacje dla przedsiębiorstw i tworzenie/testowanie   |Kopia zapasowa, niekrytyczny, rzadko występujący dostęp   |
|Rozmiar dysku   |65 536 nazywana gigabajtem i (GiB)    |32 767 GiB    |32 767 GiB   |32 767 GiB   |
|Maksymalna przepływność   |2 000 MiB/s    |900 MiB/s   |750 MiB/s   |500 MiB/s   |
|Maks. IOPS   |160 000    |20,000   |6,000   |2000   |

## <a name="ultra-disk"></a>Ultra Disk

Usługa Azure Ultra disks zapewnia wysoką przepływność, dużą liczbę operacji we/wy na sekundę oraz spójną małą ilość miejsca na dysku dla maszyn wirtualnych usługi Azure IaaS Niektóre dodatkowe zalety funkcji Ultra disks to możliwość dynamicznego zmieniania wydajności dysku wraz z obciążeniami bez konieczności ponownego uruchamiania maszyn wirtualnych. Ultra dyski są odpowiednie dla obciążeń intensywnie korzystających z danych, takich jak SAP HANA, baz danych najwyższej warstwy i obciążeń intensywnie korzystających z transakcji. Ultra disks można używać tylko jako dysków danych. Zalecamy używanie dysków SSD Premium jako dysków systemu operacyjnego.

### <a name="performance"></a>Wydajność

Podczas aprowizacji Ultra Disk można niezależnie skonfigurować pojemność i wydajność dysku. Niezwykle dyski są dostępne w kilku stałych rozmiarach, z przedziału od 4 GiB do 64 TiB i oferują elastyczny model konfiguracji wydajności, który pozwala niezależnie konfigurować operacje we/wy i przepływność.

Niektóre kluczowe możliwości Ultra disks to:

- Pojemność dysku: Pojemność Ultra disks mieści się w zakresie od 4 GiB do 64 TiB.
- Liczba operacji we/wy dysku: Ultra disks obsługuje limity liczby operacji we/wy 300 IOPS/GiB, maksymalnie 160 K operacji we/wy na dysk. Aby osiągnąć liczbę operacji we/wy, która została zainicjowana, upewnij się, że wybrane operacje we/wy na dysku są mniejsze niż limit liczby IOPS maszyny wirtualnej. Minimalna liczba operacji we/wy na dysk wynosi 2 IOPS/GiB, a ogólna podstawowa linia bazowa wynosi 100 operacji we/wy. Na przykład jeśli masz 4 GiB Ultra Disk, będziesz mieć co najmniej 100 operacji we/wy, a nie osiem operacji we/wy na sekundę.
- Przepływność dysku: W przypadku bardzo dysków limit przepływności pojedynczego dysku to 256 KiB/s dla każdej z zainicjowanych operacji we/wy, maksymalnie 2000 s MB na dysk (gdzie MB/s = 10 ^ 6 bajtów na sekundę). Minimalna przepływność na dysk to 4KiB/s dla każdej z zainicjowanych operacji we/wy z co najmniej 1 MB/s.
- Ultra disks obsługuje dostosowanie atrybutów wydajności dysku (IOPS i przepływności) w czasie wykonywania bez odłączania dysku od maszyny wirtualnej. Po wystawieniu operacji zmiany rozmiaru dysku na dysku może upłynąć do czasu, aż zmiana zacznie obowiązywać. Przekroczono limit czterech operacji zmiany rozmiaru wydajności w oknie 24-godzinnym. Istnieje możliwość, że operacja zmiany rozmiaru wydajności nie powiedzie się z powodu braku wydajności przepustowości.

### <a name="disk-size"></a>Rozmiar dysku

|Rozmiar dysku (GiB)  |Limit operacji we/wy  |Limit przepływności (MB/s)  |
|---------|---------|---------|
|4     |1,200         |300         |
|8     |2,400         |600         |
|16     |4,800         |1,200         |
|32     |9 600         |2000         |
|64     |19 200         |2000         |
|128     |38 400         |2000         |
|256     |76 800         |2000         |
|512     |80,000         |2000         |
|1024-65536 (rozmiary w tym zakresie zwiększają się w przyrostach 1 TiB)     |160 000         |2000         |

### <a name="ga-scope-and-limitations"></a>Zakres i ograniczenia dotyczące GA

Na razie Ultra dyski mają dodatkowe ograniczenia, są następujące:

- Są obsługiwane w regionach Wschodnie stany USA 2, Azja Południowo-Wschodnia i Europa Północna, w dwóch strefach dostępności na region  
- Może być używany tylko z strefami dostępności (zbiory dostępności i pojedyncze wdrożenia maszyn wirtualnych poza strefami nie będą miały możliwości dołączenia Ultra Disk)
- Są obsługiwane tylko na maszynach wirtualnych ES/DS. v3
- Są dostępne tylko jako dyski danych i obsługują tylko rozmiar sektora fizycznego 4 KB  
- Można utworzyć tylko jako puste dyski  
- Nie obsługują jeszcze migawek dysków, obrazów maszyn wirtualnych, zestawów dostępności, zestawów skalowania maszyn wirtualnych i usługi Azure Disk Encryption
- Nie należy jeszcze obsługiwać integracji z programem Azure Backup lub Azure Site Recovery
- Bieżący maksymalny limit liczby operacji we/wy na maszynach wirtualnych "GA" to 80 000.