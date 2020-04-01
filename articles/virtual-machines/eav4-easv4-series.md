---
title: Seria Eav4 i seria Easv4 — maszyny wirtualne platformy Azure
description: Specyfikacje maszyn wirtualnych z serii Eav4 i Easv4.
services: virtual-machines
author: migerdes
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: ab45298dbcb8988da7d0211fcbde3087d0099038
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437080"
---
# <a name="eav4-and-easv4-series"></a>Serie Eav4 i Easv4

Seria Eav4 i Easv4 wykorzystują procesor AMD 2.35Ghz EPYC<sup>TM</sup> 7452 w konfiguracji wielowątkowej z pamięcią podręczną L3 o rozmiarze do 256 MB, zwiększając możliwości uruchamiania większości obciążeń zoptymalizowanych pod kątem pamięci. Seria Eav4 i Easv4 mają taką samą konfigurację pamięci i dysków jak ev3 & serii Esv3.

## <a name="eav4-series"></a>Seria Eav4

ACU: 230 - 260

Pamięć masowa w wersji Premium: nie jest obsługiwana

Buforowanie magazynu w wersji Premium: nie jest obsługiwane

Migracja na żywo: obsługiwane

Aktualizacje zachowywania pamięci: Obsługiwane

Rozmiary serii Eav4 są oparte na procesorze AMD EPYC<sup>TM</sup> 7452 2,35 GHz, który może osiągnąć maksymalną częstotliwość 3,35 GHz i korzystać z najwyższej jakości SSD. Rozmiary serii Eav4 są idealne do zastosowań korporacyjnych wymagających dużej ilości pamięci. Opłaty za magazyn dysków danych są naliczane oddzielnie od opłat za maszyny wirtualne. Aby korzystać z najwyższej jakości ssd, użyj rozmiarów serii Easv4. Liczniki cen i rozliczeń dla rozmiarów Easv4 są takie same jak w przypadku serii Eav3.

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maks. liczba dysków danych | Maksymalna przepływność magazynu tymczasowego: operacje we/wy na sek. / odczyt MB/s / zapis MB/s | Maksymalna karta sieciowa / oczekiwana przepustowość sieci (MB/s) |
| -----|-----|-----|-----|-----|-----|-----|
| Standard\_E2a\_v4|2|16|50|4|3000 / 46 / 23|2 / 1000 |
| Standard\_E4a\_v4|4|32|100|8|6000 / 93 / 46|2 / 2000 |
| Standard\_E8a\_v4|8|64|200|16|12000 / 187 / 93|4 / 4000 |
| Standard\_E16a\_v4|16|128|400|32|24000 / 375 / 187|8 / 8000 |
| Standard\_E20a\_v4|20|160|500|32|30000 / 468 / 234|8 / 10000 |
| Standard\_E32a\_v4|32|256|800|32|48000 / 750 / 375|8 / 16 000 |
| Standard\_E48a\_v4|48|384|1200|32|96000 / 1000 (500)|8 / 24000 |
| Standard\_E64a\_v4|64|512|1600|32|96000 / 1000 (500)|8 / 30000 |
| Standard\_E96a\_v4|96|672|2400|32|96000 / 1000 (500)|8 / 30000 |

## <a name="easv4-series"></a>Seria Easv4

ACU: 230 - 260

Pamięć masowa w wersji premium: obsługiwane

Buforowanie magazynu w wersji Premium: obsługiwane

Migracja na żywo: obsługiwane

Aktualizacje zachowywania pamięci: Obsługiwane

Rozmiary serii Easv4 są oparte na procesorze AMD EPYC<sup>TM</sup> 7452 2,35 GHz, który może osiągnąć maksymalną częstotliwość 3,35 GHz i korzystać z najwyższej jakości SSD. Rozmiary serii Easv4 idealnie nadają się do zastosowań korporacyjnych wymagających dużej ilości pamięci.

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maks. liczba dysków danych | Maksymalna przepływność magazynu buforowanego i tymczasowego: liczba operacji we/wy na sekundę / MB/s (rozmiar pamięci podręcznej w GiB) | Maksymalna przepływność niebuforowanych dysków: liczba operacji we/wy na sekundę / MB/s | Maksymalna karta sieciowa / oczekiwana przepustowość sieci (MB/s) |
|-----|-----|-----|-----|-----|-----|-----|-----|
| Standard_E2as_v4|2|16|32|4|4000 / 32 (50)|3200 / 48|2 / 1000 |
| Standard_E4as_v4|4|32|64|8|8000 / 64 (100)|6400 / 96|2 / 2000 |
| Standard_E8as_v4|8|64|128|16|16000 / 128 (200)|12800 / 192|4 / 4000 |
| Standard_E16as_v4|16|128|256|32|32000 / 255 (400)|25600 / 384|8 / 8000 |
| Standard_E20as_v4|20|160|320|32|40000 / 320 (500)|32000 / 480|8 / 10000 |
| Standard_E32as_v4|32|256|512|32|64000 / 510 (800)|51200 / 768|8 / 16 000 |
| Standard_E48as_v4|48|384|768|32|96000 / 1020 (1200)|76800 / 1148|8 / 24000 |
| Standard_E64as_v4|64|512|1024|32|128000 / 1020 (1600)|80000 / 1200|8 / 30000 |
| Standard_E96as_v4|96|672|1344|32|192000 / 1020 (2400)|80000 / 1200|8 / 30000 |

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
