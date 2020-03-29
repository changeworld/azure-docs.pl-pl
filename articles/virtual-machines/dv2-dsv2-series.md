---
title: Seria Dv2 i Dsv2 — maszyny wirtualne platformy Azure
description: Dane techniczne maszyn wirtualnych serii Dv2 i Dsv2.
services: virtual-machines
author: joelpelley
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 808b14f118e842cb9e52d110075f92ba25a343c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164427"
---
# <a name="dv2-and-dsv2-series"></a>Serie Dv2 i DSv2

Seria Dv2 i Dsv2, kontynuacja oryginalnej serii D, jest wyposażona w mocniejszy procesor i optymalną konfigurację procesora cpu-to-memory, dzięki czemu nadają się do większości obciążeń produkcyjnych. Seria Dv2 jest o około 35% szybsza niż seria D. Seria Dv2 działa na procesorze Intel® Xeon® 8171M 2.1GHz (Skylake), Intel® Xeon® E5-2673 v4 2.2.. 3 GHz (Broadwell) lub procesory Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) z technologią Intel Turbo Boost 2.0. Konfiguracje pamięci i dysków serii Dv2 są takie same jak w przypadku serii D.

## <a name="dv2-series"></a>Seria Dv2

Rozmiary serii Dv2 działają na procesorze Intel® Xeon® 8171M 2,1 GHz (Skylake) lub w procesorze Intel® Xeon® E5-2673 v 4 procesory 2,3 GHz (Broadwell) lub Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) z technologią Intel Turbo Boost 2.0.

ACU: 210–250

Pamięć masowa w wersji Premium: nie jest obsługiwana

Buforowanie magazynu w wersji Premium: nie jest obsługiwane

Migracja na żywo: obsługiwane

Aktualizacje zachowywania pamięci: Obsługiwane

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maksymalna przepustowość magazynu temp: IOPS / Odczyt MB/Zapis MB/s | Maks. liczba dysków danych | Przepływność: IOPS | Maksymalna przepustowość sieci ową/oczekiwana przepustowość sieci (Mb/s) |
|---|---|---|---|---|---|---|---|
| Standardowa_D1_v2 | 1  | 3,5 | 50  | 3000/46/23    | 4  | 4 x 500  | 2/750   |
| Standardowa_D2_v2 | 2  | 7   | 100 | 6000/93/46    | 8  | 8 x 500  | 2/1500  |
| Standardowa_D3_v2 | 4  | 14  | 200 | 12000/187/93  | 16 | 16 x 500 | 4/3000  |
| Standardowa_D4_v2 | 8  | 28  | 400 | 24000/375/187 | 32 | 32 x 500 | 8/6000  |
| Standardowa_D5_v2 | 16 | 56  | 800 | 48000/750/375 | 64 | 64x500 | 8/12000 |

## <a name="dsv2-series"></a>Seria DSv2

Rozmiary serii DSv2 działają na procesorach Intel® Xeon® 8171M 2,1 GHz (Skylake) lub intel® Xeon® E5-2673 v4 4 2 2,3 GHz (Broadwell) lub procesory Intel® Xeon® E5-2673 v3 2,4 GHz (Haswell) z technologią Intel Turbo Boost 2.0 i korzystają z pamięci masowej premium.

ACU: 210–250

Pamięć masowa w wersji premium: obsługiwane

Buforowanie magazynu w wersji Premium: obsługiwane

Migracja na żywo: obsługiwane

Aktualizacje zachowywania pamięci: Obsługiwane

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maks. liczba dysków danych | Maksymalna przepływność pamięci podręcznej i magazynu tymczasowego: IOPS/MBPS (rozmiar pamięci podręcznej w GiB) | Maksymalna przepustowość dysku bez buforów: IOPS/MB/s | Maksymalna przepustowość sieci ową/oczekiwana przepustowość sieci (Mb/s) |
|---|---|---|---|---|---|---|---|
| Standardowa_DS1_v2 | 1  | 3,5 | 7   | 4  | 4000/32 (43)    | 3200/48   | 2/750   |
| Standardowa_DS2_v2 | 2  | 7   | 14  | 8  | 8000/64 (86)    | 6400/96   | 2/1500  |
| Standardowa_DS3_v2 | 4  | 14  | 28  | 16 | 16000/128 (172) | 12800/192 | 4/3000  |
| Standardowa_DS4_v2 | 8  | 28  | 56  | 32 | 32000/256 (344) | 25600/384 | 8/6000  |
| Standardowa_DS5_v2 | 16 | 56  | 112 | 64 | 64000/512 (688) | 51200/768 | 8/12000 |

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
