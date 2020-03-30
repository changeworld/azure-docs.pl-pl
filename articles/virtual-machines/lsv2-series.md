---
title: Seria Lsv2 — maszyny wirtualne platformy Azure
description: Dane techniczne maszyn wirtualnych serii Lsv2.
services: virtual-machines
author: sasha-melamed
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 103e19d6e299956b5ee1ad45b577e25f9f2de1c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164036"
---
# <a name="lsv2-series"></a>Seria Lsv2

Seria Lsv2 charakteryzuje się wysoką przepustowością, małym opóźnieniem, bezpośrednio mapowaną lokalną pamięcią NVMe działającą na [procesorze AMD EPYC<sup>TM</sup> 7551](https://www.amd.com/en/products/epyc-7000-series) z doładowaniem całego rdzenia 2,55 GHz i maksymalnym wzmocnieniem 3,0 GHz. Maszyny wirtualne serii Lsv2 mają rozmiary od 8 do 80 vCPU w jednoczesnej konfiguracji wielowątkowej.  Na procesory wirtualne przypada 8 GiB pamięci, a na urządzenie L80s v2 jest dostępne 1,92 TB NVMe SSD M.2 na 8 procesorów wirtualnych.

> [!NOTE]
> Maszyny wirtualne z serii Lsv2 są zoptymalizowane pod kątem używania dysku lokalnego w węźle dołączonym bezpośrednio do maszyny wirtualnej, a nie przy użyciu dysków z danymi trwałymi. Pozwala to na większe IOP / przepływność dla obciążeń. Lsv2 i Ls serii nie obsługują tworzenia lokalnej pamięci podręcznej, aby zwiększyć IO można osiągalne przez dyski danych trwałe.
>
> Wysoka przepustowość i IOP dysku lokalnego sprawia, że maszyny wirtualne z serii Lsv2 są idealne dla magazynów NoSQL, takich jak Apache Cassandra i MongoDB, które replikują dane między wieloma maszynami wirtualnymi w celu osiągnięcia trwałości w przypadku awarii pojedynczej maszyny wirtualnej.
>
> Aby dowiedzieć się więcej, zobacz Optymalizowanie wydajności na maszynach wirtualnych z serii Lsv2 dla [systemu Windows](../virtual-machines/windows/storage-performance.md) lub [Linux](../virtual-machines/linux/storage-performance.md).  

ACU: 150-175

Pamięć masowa w wersji premium: obsługiwane

Buforowanie magazynu w wersji Premium: nie jest obsługiwane

Migracja na żywo: nie jest obsługiwana

Aktualizacje zachowywania pamięci: nie są obsługiwane

| Rozmiar | Procesor wirtualny | Pamięć (GiB) | Dysk tymczasowy<sup>1</sup> (GiB) | Dyski NVMe<sup>2</sup> | Przepustowość dysku NVMe<sup>3</sup> (odczyt IOPS/MB/s) | Maksymalna przepustowość dysku danych nieokaszowy (IOP/MBps)<sup>4</sup> | Maksymalna liczba dysków z danymi | Maksymalna karta sieciowa / oczekiwana przepustowość sieci (Mb/s) |
|---|---|---|---|---|---|---|---|---|
| Standard_L8s_v2   |  8 |  64 |  80 |  1x1,92 TB  | 400000/2000  | 8000/160   | 16 | 2 / 3200   |
| Standard_L16s_v2  | 16 | 128 | 160 |  2x1,92 TB  | 800000/4000  | 16000/320  | 32 | 4 / 6400   |
| Standard_L32s_v2  | 32 | 256 | 320 |  4x1,92 TB  | 1,5 mln/8000    | 32000/640  | 32 | 8 / 12800  |
| Standard_L48s_v2  | 48 | 384 | 480 |  6x1,92 TB  | 2,2 mln/14000   | 48000/960  | 32 | 8 / 16000+ |
| Standard_L64s_v2  | 64 | 512 | 640 |  8x1,92 TB  | 2,9 mln/16000   | 64000/1280 | 32 | 8 / 16000+ |
| Standard_L80s_v2<sup>5</sup> | 80 | 640 | 800 | 10x1,92 TB | 3,8 mln/20000 | 80000/1400 | 32 | 8 / 16000+ |

<sup>1</sup> Maszyny wirtualne z serii Lsv2 mają standardowy dysk zasobów tymczasowych opartych na interfejsie SCSI do używania plików stronicowania/wymiany systemu operacyjnego (D: w systemie Windows, /dev/sdb w systemie Linux). Ten dysk zapewnia 80 GiB pamięci masowej, 4000 IOPS i 80 MB/s szybkości transferu na każde 8 procesorów wirtualnych (np. Standard_L80s_v2 zapewnia 800 GiB przy 40 000 IOPS i 800 MBPS). Dzięki temu dyski NVMe mogą być w pełni dedykowane do użytku aplikacji. Ten dysk jest efemeryczny, a wszystkie dane zostaną utracone na stop/deallocate.

<sup>2</sup> Lokalne dyski NVMe są efemeryczne, dane zostaną utracone na tych dyskach, jeśli zatrzymasz/zdążysz przydzielić maszynę wirtualną.

<sup>3</sup> Technologia Hyper-V NVMe Direct zapewnia nieograniczony dostęp do lokalnych dysków NVMe mapowanych bezpiecznie w przestrzeń maszyny wirtualnej gościa.  Osiągnięcie maksymalnej wydajności wymaga użycia najnowszej kompilacji WS2019 lub Ubuntu 18.04 lub 16.04 z portalu Azure Marketplace.  Wydajność zapisu zależy od rozmiaru we/wy, obciążenia dysku i wykorzystania pojemności.

<sup>4</sup> Maszyny wirtualne z serii Lsv2 nie zapewniają pamięci podręcznej hosta dla dysku danych, ponieważ nie przynosi korzyści obciążeń Lsv2.  Jednak maszyny wirtualne Lsv2 może pomieścić ephemeral opcji systemu operacyjnego maszyny Wirtualnej platformy Azure opcji (do 30 GiB).

<sup>5</sup> maszyn wirtualnych z więcej niż 64 procesorami wirtualnymi wymaga jednego z tych obsługiwanych systemów operacyjnych gościa:

- Windows Server 2016 lub nowsza
- Ubuntu 16.04 LTS lub nowsze, z jądrem dostrojonym na platformie Azure (jądro 4.15 lub nowsze)
- SLES 12 SP2 lub nowsza
- RHEL lub CentOS w wersji od 6.7 do 6.10, z zainstalowanym pakietem LIS dostarczonym przez firmę Microsoft 4.3.1 (lub nowszym)
- RHEL lub CentOS w wersji 7.3, z zainstalowanym pakietem LIS dostarczonym przez firmę Microsoft 4.2.1 (lub nowszym)
- RHEL lub CentOS w wersji 7.6 lub nowszej
- Oracle Linux z UEK4 lub nowszym
- Debian 9 z jądrem backports, Debian 10 lub nowszym
- CoreOS z jądrem 4.14 lub nowszym

## <a name="size-table-definitions"></a>Definicje tabel rozmiaru

- Pojemność magazynu jest podawana w jednostkach GiB (1024^3 bajtów). Podczas porównywania dysków mierzonych w GB (1000^3 bajtów) z dyskami mierzonymi w GiB (1024^3 bajtów) należy pamiętać, że pojemność podawana w GiB może wydawać się mniejsza. Na przykład 1023 GiB = 1098,4 GB.
- Przepływność dysku mierzona jest jako liczba operacji wejścia/wyjścia na sekundę i MB/s, gdzie 1 MB/s = 10^6 bajtów/s.
- Jeśli chcesz uzyskać najlepszą wydajność dla maszyn wirtualnych, należy ograniczyć liczbę dysków z danymi do 2 dysków na procesor wirtualny.
- **Oczekiwana przepustowość sieci** to maksymalna zagregowana [przepustowość przydzielona na typ maszyny Wirtualnej](../virtual-network/virtual-machine-network-throughput.md) dla wszystkich kart sieciowych dla wszystkich miejsc docelowych. Górne limity nie są gwarantowane, ale mają stanowić wskazówkę do wybierania właściwego typu maszyny wirtualnej dla planowanej aplikacji. Rzeczywista wydajność sieci będzie zależeć od wielu czynników, takich jak przeciążenie sieci, obciążenie aplikacji oraz ustawienia sieci. Aby uzyskać informacje na temat optymalizowania przepływności sieci, zobacz [Optimizing network throughput for Windows and Linux (Optymalizowanie przepływności sieci dla systemów Windows i Linux)](../virtual-network/virtual-network-optimize-network-bandwidth.md). Aby uzyskać oczekiwaną wydajność sieci w systemie Linux lub Windows, konieczne może być wybranie konkretnej wersji maszyny wirtualnej lub jej zoptymalizowanie. Aby uzyskać więcej informacji, zobacz [How to reliably test for virtual machine throughput (Jak wiarygodnie przetestować przepływność maszyny wirtualnej)](../virtual-network/virtual-network-bandwidth-testing.md).

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o tym, jak [jednostki obliczeniowe platformy Azure (ACU)](acu.md) mogą pomóc w porównywaniu wydajności obliczeniowej w jednostkach SKU platformy Azure.
