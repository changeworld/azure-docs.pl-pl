---
title: Seria Mv2 — maszyny wirtualne platformy Azure
description: Dane techniczne maszyn wirtualnych serii Mv2.
services: virtual-machines
author: ayshakeen
ms.service: virtual-machines
ms.topic: article
ms.date: 04/07/2020
ms.author: lahugh
ms.openlocfilehash: 764dc93608ae3b8882b7048a722c6d3415cbc644
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885790"
---
# <a name="mv2-series"></a>Seria Mv2

Seria Mv2 jest wyposażona w platformę o wysokiej przepustowości i niskim opóźnieniu działającą na hiperwątkowym procesorze Intel® Xeon® Platinum 8180M 2,5 GHz (Skylake) o podstawowej częstotliwości 2,5 GHz i maksymalnej częstotliwości turbo 3,8 GHz. Wszystkie rozmiary maszyn wirtualnych serii Mv2 mogą używać dysków trwałych standardowych i premium. Wystąpienia serii Mv2 to zoptymalizowane pod kątem pamięci rozmiary maszyn wirtualnych, zapewniające niezrównaną wydajność obliczeniową do obsługi dużych baz danych i obciążeń w pamięci, z wysokim współczynnikiem pamięci do procesora, który jest idealny dla relacyjnych serwerów baz danych, dużych pamięci podręcznych i analizy w pamięci.

Funkcja maszyn wirtualnych serii Mv2 Intel® Technologia Hyper-Threading

Pamięć masowa w wersji premium: obsługiwane

Buforowanie magazynu w wersji Premium: obsługiwane

Migracja na żywo: nie jest obsługiwana

Aktualizacje zachowywania pamięci: nie są obsługiwane

Akcelerator zapisu: [Obsługiwane](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)

|Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maks. liczba dysków danych | Maksymalna przepływność magazynu buforowanego i tymczasowego: liczba operacji we/wy na sekundę / MB/s (rozmiar pamięci podręcznej w GiB) | Maksymalna przepływność niebuforowanych dysków: liczba operacji we/wy na sekundę / MB/s | Maksymalna karta sieciowa / oczekiwana przepustowość sieci (Mb/s) |
|---|---|---|---|---|---|---|---|
| Standard_M208ms_v2<sup>1</sup> | 208 | 5700 | 4096 | 64 | 80000 / 800 (7040) | 40000 / 1000 | 8 / 16 000 |
| Standard_M208s_v2<sup>1</sup> | 208 | 2850 | 4096 | 64 | 80000 / 800 (7040) | 40000 / 1000 | 8 / 16 000 |
| Standard_M416ms_v2<sup>1</sup> | 416 | 11400 | 8192 | 64 | 250000 / 1600 (14080) | 80000 / 2000 | 8 / 32000 |
| Standard_M416s_v2<sup>1</sup> | 416 | 5700 | 8192 | 64 | 250000 / 1600 (14080) | 80000 / 2000 | 8 / 32000 |

Maszyny wirtualne serii Mv2 <sup>1</sup> są tylko generacją 2. Jeśli używasz systemu Linux, zobacz [obsługa maszyn wirtualnych generacji 2 na platformie Azure,](./linux/generation-2.md) aby uzyskać instrukcje dotyczące znajdowania i wybierania obrazu. Jeśli używasz systemu Windows, zobacz [obsługa maszyn wirtualnych generacji 2 na platformie Azure,](./windows/generation-2.md) aby uzyskać instrukcje dotyczące znajdowania i wybierania obrazu. Minimalne wersje systemu operacyjnego wymagane dla wszystkich czterech różnych typów maszyn wirtualnych serii Mv2, takich jak:

- Windows Server 2019 lub nowsza
- SUSE Linux Enterprise Server 12 SP4 i nowsze lub SUSE Linux Enterprise Server 15 SP1 i nowsze
- Red Hat Enterprise Linux 7.6, 7.7, 8.1 lub nowsze 
- Oracle Enterprise Linux 7.7 lub nowsza



[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes"></a>Inne rozmiary

- [Zastosowania ogólne](sizes-general.md)
- [Optymalizacja pod kątem pamięci](sizes-memory.md)
- [Optymalizacja pod kątem magazynu](sizes-storage.md)
- [Optymalizacja pod kątem procesora GPU](sizes-gpu.md)
- [Obliczenia o wysokiej wydajności](sizes-hpc.md)
- [Poprzednie generacje](sizes-previous-gen.md)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o tym, jak [jednostki obliczeniowe platformy Azure (ACU)](acu.md) mogą pomóc w porównywaniu wydajności obliczeniowej w jednostkach SKU platformy Azure.
