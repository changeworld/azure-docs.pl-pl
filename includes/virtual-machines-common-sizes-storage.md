---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 04/17/2019
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 765ee3c737adbe1da89b9e908d0e22e44d0f29ba
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76748800"
---
Rozmiary maszyn wirtualnych zoptymalizowane pod kątem magazynu oferują wysoką przepływność dysku i operacje we/wy, a doskonale nadają się do obsługi danych Big Data, SQL, NoSQL, magazynowania danych i dużych transakcyjnych baz danych.  Przykłady obejmują Cassandra, MongoDB, Cloudera i Redis. Ten artykuł zawiera informacje o liczbie procesorów wirtualnych vCPU, dyskach danych i kartach sieciowych oraz o przepływności lokalnego magazynu i przepustowości sieci dla każdego zoptymalizowanego rozmiaru.

Funkcja serii Lsv2 ma wysoką przepływność, małe opóźnienia, bezpośrednio mapowane lokalne magazyny interfejsu NVMe działające na [procesorze AMD EPYC &trade; 7551](https://www.amd.com/en/products/epyc-7000-series) i wszystkie podstawowe zwiększenie wydajności 2.55 GHz oraz maksymalne zwiększenie wydajności 3,0 GHz. Maszyny wirtualne z serii Lsv2 są dostępne w rozmiarach od 8 do 80 procesorów wirtualnych w konfiguracji jednoczesnej wielowątkowości.  Dostępnych jest 8 GiB pamięci na każdy procesor wirtualny i jedno urządzenie NVMe SSD M.2 o pojemności 1,92 TB na każde 8 procesorów wirtualnych, a w przypadku serii L80s v2 19,2 TB (10x1,92 TB).

> [!NOTE]
> Maszyny wirtualne z serii Lsv2 są zoptymalizowane pod kątem używania dysku lokalnego w węźle dołączonym bezpośrednio do maszyny wirtualnej zamiast korzystania z trwałych dysków danych. Pozwala to na większą liczbę operacji we/wy na sekundę dla obciążeń. Seria Lsv2 i ls nie obsługuje tworzenia lokalnej pamięci podręcznej w celu zwiększenia liczby operacji we/wy osiągalnej przez trwałe dyski danych.
>
> Wysoka przepływność i operacje we/wy na dysku lokalnym sprawia, że maszyny wirtualne z serii Lsv2 i LS są idealne dla magazynów NoSQL, takich jak Apache Cassandra i MongoDB, które replikują dane między wieloma maszynami wirtualnymi w celu osiągnięcia trwałości w przypadku awarii pojedynczej maszyny wirtualnej.
>
> Aby dowiedzieć się więcej, zobacz [Optymalizacja wydajności na maszynach wirtualnych z serii Lsv2](../articles/virtual-machines/linux/storage-performance.md).  


## <a name="lsv2-series"></a>Seria Lsv2

ACU: 150-175

Premium Storage: obsługiwane

Buforowanie Premium Storage: nieobsługiwane

| Rozmiar          | vCPU | Pamięć (GiB) | Dysk tymczasowy<sup>1</sup> (GIB) | Dyski interfejsu NVMe<sup>2</sup> | Przepływność dysków interfejsu NVMe<sup>3</sup> (odczyt operacji we/wy/s) | Maksymalna przepływność dysku danych w pamięci podręcznej (IOPs/MB/s)<sup>4</sup> | Maksymalna liczba dysków danych | Maksymalna liczba kart sieciowych/oczekiwana przepustowość sieci (MB/s) |
|---------------|-----------|-------------|--------------------------|----------------|---------------------------------------------------|-------------------------------------------|------------------------------|------------------------------| 
| Standard_L8s_v2   |  8 |  64 |  80 |  1x 1.92 TB  | 400000/2000  | 8000/160   | 16 | 2 / 3200  |
| Standard_L16s_v2  | 16 | 128 | 160 |  2 1.92 TB  | 800000/4000  | 16000/320  | 32 | 4 / 6400  |
| Standard_L32s_v2  | 32 | 256 | 320 |  4x 1.92 TB  | 1,5 m/8000    | 32000/640  | 32 | 8 / 12800 |
| Standard_L48s_v2  | 48 | 384 | 480 |  6X 1.92 TB  | 2.2 m/14000   | 48000/960  | 32 | 8/16000 + |
| Standard_L64s_v2  | 64 | 512 | 640 |  8x 1.92 TB  | 2.9 m/16000   | 64000/1280 | 32 | 8/16000 + |
| Standard_L80s_v2<sup>5</sup> | 80 | 640 | 800 | 10X 1.92 TB   | 3.8 m/20000   | 80000/1400 | 32 | 8/16000 + |

<sup>1</sup> maszyny wirtualne z serii Lsv2 mają standardowy dysk zasobów oparty na interfejsie SCSI na potrzeby STRONICOWANIA systemu operacyjnego/użycia pliku wymiany (D: w systemie Windows,/dev/sdb on Linux). Ten dysk udostępnia 80 GiB magazynu, 4 000 IOPS i 80 liczbę MB/s dla każdego 8 procesorów wirtualnych vCPU (np. Standard_L80s_v2 zapewnia 800 GiB w przypadku operacji wejścia/wyjścia na sekundę i 40 000 MB/s). Dzięki temu dyski interfejsu NVMe mogą być w pełni przeznaczone do użytku aplikacji. Ten dysk jest nieulotny i wszystkie dane zostaną utracone przy zatrzymaniu/cofnięciu przydziału.

<sup>2</sup> lokalne dyski interfejsu NVMe są tymczasowe, dane zostaną utracone na tych dyskach, jeśli zatrzymasz/ZWOLNIsz maszynę wirtualną.

<sup>3</sup> Technologia bezpośredniego interfejsu NVMe funkcji Hyper-V zapewnia nieograniczoną dostęp do lokalnych dysków interfejsu NVMe, które są bezpiecznie mapowane do przestrzeni maszyny wirtualnej gościa.  Osiągnięcie maksymalnej wydajności wymaga użycia najnowszej WS2019 kompilacja lub Ubuntu 18,04 lub 16,04 z portalu Azure Marketplace.  Wydajność zapisu zależy od rozmiaru operacji we/wy, obciążenia dysku i wykorzystania pojemności.

<sup>4</sup> maszyny wirtualne z serii Lsv2 nie zapewniają pamięci podręcznej hosta dla dysku danych, ponieważ nie korzystają z obciążeń Lsv2.  Jednak maszyny wirtualne Lsv2 mogą obsługiwać opcję dysku tymczasowych maszyn wirtualnych systemu Azure (do 30 GiB).

<sup>5</sup> maszyny wirtualne z ponad 64 procesorów wirtualnych vCPU wymagają jednego z obsługiwanych systemów operacyjnych gościa:
- System Windows Server 2016 lub nowszy
- Ubuntu 16,04 LTS lub nowszy z dostrojonym jądrem platformy Azure (jądrem 4,15 lub nowszym)
- SLES 12 z dodatkiem SP2 lub nowszym
- RHEL lub CentOS w wersji 6,7 do 6,10 przy użyciu dostarczonej przez firmę Microsoft pakietu LIS (lub nowszego)
- RHEL lub CentOS w wersji 7,3 z pakietem LIS dostarczonym przez firmę Microsoft (lub nowszym)
- RHEL lub CentOS w wersji 7,6 lub nowszej
- Oracle Linux z UEK4 lub nowszym
- Debian 9 z jądrem, Debian 10 lub nowszym
- CoreOS z jądrem 4,14 lub nowszym


## <a name="size-table-definitions"></a>Definicje tabel rozmiaru

- Pojemność magazynu jest podawana w jednostkach GiB (1024^3 bajtów). Podczas porównywania dysków mierzonych w GB (1000^3 bajtów) z dyskami mierzonymi w GiB (1024^3 bajtów) należy pamiętać, że pojemność podawana w GiB może wydawać się mniejsza. Na przykład 1023 GiB = 1098,4 GB.
- Przepływność dysku mierzona jest jako liczba operacji wejścia/wyjścia na sekundę i MB/s, gdzie 1 MB/s = 10^6 bajtów/s.
- Jeśli chcesz uzyskać najlepszą wydajność dla maszyn wirtualnych, należy ograniczyć liczbę dysków danych do 2 dysków na vCPU.
- **Oczekiwana przepustowość sieci** to maksymalna zagregowana [przepustowość przyalokowana na typ maszyny wirtualnej](../articles/virtual-network/virtual-machine-network-throughput.md) dla wszystkich kart sieciowych dla wszystkich miejsc docelowych. Górne limity nie są gwarantowane, ale mają stanowić wskazówkę do wybierania właściwego typu maszyny wirtualnej dla planowanej aplikacji. Rzeczywista wydajność sieci będzie zależeć od wielu czynników, takich jak przeciążenie sieci, obciążenie aplikacji oraz ustawienia sieci. Aby uzyskać informacje na temat optymalizowania przepływności sieci, zobacz [Optimizing network throughput for Windows and Linux (Optymalizowanie przepływności sieci dla systemów Windows i Linux)](../articles/virtual-network/virtual-network-optimize-network-bandwidth.md). Aby uzyskać oczekiwaną wydajność sieci w systemie Linux lub Windows, konieczne może być wybranie konkretnej wersji maszyny wirtualnej lub jej zoptymalizowanie. Aby uzyskać więcej informacji, zobacz [How to reliably test for virtual machine throughput (Jak wiarygodnie przetestować przepływność maszyny wirtualnej)](../articles/virtual-network/virtual-network-bandwidth-testing.md).
