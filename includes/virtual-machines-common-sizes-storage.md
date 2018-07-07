---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 07/06/2018
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 512f251a91a035d3d48566c414076b1a5b6d8805
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/07/2018
ms.locfileid: "37907104"
---
Magazyn zoptymalizowanych rozmiarów maszyn wirtualnych zapewniają Wysoka przepływność dysku i we/wy oraz idealnie nadają się do obsługi dużych ilości danych, SQL i NoSQL baz danych. Ten artykuł zawiera informacje o liczbie procesorów wirtualnych, dysków z danymi i kart sieciowych, a także przepustowość przepływności i sieć magazynu dla każdego rozmiaru w tej metodzie grupowania. 

Maszyny wirtualne serii Ls oferują do 32 procesorów wirtualnych vCPU — procesor [Intel® Xeon® z rodziny E5 v3](http://www.intel.com/content/www/us/en/processors/xeon/xeon-e5-solutions.html). Wydajność procesora CPU dla serii Ls jest taka sama jak w przypadku maszyn wirtualnych serii G/GS. Na procesor wirtualny vCPU przypada pamięć o pojemności 8 GiB.  

## <a name="ls-series"></a>Seria Ls

ACU: 180–240

Usługa Premium Storage: obsługiwane

Buforowanie Premium Storage: Nieobsługiwane
 
| Rozmiar          | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maks. liczba dysków danych | Maksymalna przepływność magazynu tymczasowego: operacje We/Wy / MB/s | Maksymalna przepływność niebuforowanych dysków: liczba operacji we/wy na sekundę / MB/s | Maksymalna liczba kart sieciowych / oczekiwana przepustowość sieci (MB/s) | 
|---------------|-----------|-------------|--------------------------|----------------|-------------------------------------------------------------|-------------------------------------------|------------------------------| 
| Standardowa_L4s   | 4    | 32   | 678   | 16    | 20,000 / 200   | 5000 / 125        | 2 / 4,000  | 
| Standardowa_L8s   | 8    | 64   | 1,388 | 32   | 40,000 / 400   | 10 000 / 250       | 4 / 8,000  | 
| Standardowa_L16s  | 16   | 128  | 2,807 | 64   | 80,000 / 800   | 20 000 / 500       | 8 / 16,000 | 
| Standard_L32s <sup>1</sup> | 32   | 256  | 5,630 | 64   | 160,000 / 1,600   | 40 000 / 1000     | 8 / 20,000 | 
 

Maksymalna przepływność dysków możliwa przy użyciu maszyn wirtualnych serii Ls może być ograniczona przez liczbę, rozmiar i rozłożenie wszelkich dołączonych dysków. Aby uzyskać szczegółowe informacje, zobacz [Premium Storage: High-performance storage for Azure virtual machine workloads](../articles/virtual-machines/windows/premium-storage.md) (Premium Storage: magazyn o wysokiej wydajności dla obciążeń maszyn wirtualnych platformy Azure).

<sup>1</sup> wystąpienie jest izolowane do sprzętu dedykowanego pojedynczemu klientowi.

## <a name="size-table-definitions"></a>Definicje tabel rozmiaru

- Pojemność magazynu jest podawana w jednostkach GiB (1024^3 bajtów). Podczas porównywania dysków mierzonych w GB (1000^3 bajtów) z dyskami mierzonymi w GiB (1024^3 bajtów) należy pamiętać, że pojemność podawana w GiB może wydawać się mniejsza. Na przykład 1023 GiB = 1098,4 GB.
- Przepływność dysku mierzona jest jako liczba operacji wejścia/wyjścia na sekundę i MB/s, gdzie 1 MB/s = 10^6 bajtów/s.
- Jeśli chcesz uzyskać najlepszą wydajność dla maszyn wirtualnych, należy ograniczyć liczbę dysków z danymi na 2 dyski na procesor wirtualny vCPU.
- **Oczekiwano przepustowość sieci** maksymalną mają charakter [przydzielonej dla typu maszyny Wirtualnej przepustowości](../articles/virtual-network/virtual-machine-network-throughput.md) we wszystkich kartach sieciowych, dla wszystkich miejsc docelowych. Górne limity nie są gwarantowane, ale mają stanowić wskazówkę do wybierania właściwego typu maszyny wirtualnej dla planowanej aplikacji. Rzeczywista wydajność sieci będzie zależeć od wielu czynników, takich jak przeciążenie sieci, obciążenie aplikacji oraz ustawienia sieci. Aby uzyskać informacje na temat optymalizowania przepływności sieci, zobacz [Optimizing network throughput for Windows and Linux (Optymalizowanie przepływności sieci dla systemów Windows i Linux)](../articles/virtual-network/virtual-network-optimize-network-bandwidth.md). Aby uzyskać oczekiwaną wydajność sieci w systemie Linux lub Windows, konieczne może być wybranie konkretnej wersji maszyny wirtualnej lub jej zoptymalizowanie. Aby uzyskać więcej informacji, zobacz [How to reliably test for virtual machine throughput (Jak wiarygodnie przetestować przepływność maszyny wirtualnej)](../articles/virtual-network/virtual-network-bandwidth-testing.md).
