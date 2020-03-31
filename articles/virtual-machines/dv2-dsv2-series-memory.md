---
title: Maszyny wirtualne z serii Dv2 i DSv2 zoptymalizowane pod kątem pamięci — maszyny wirtualne platformy Azure
description: Dane techniczne maszyn wirtualnych serii Dv2 i DSv2.
services: virtual-machines
author: joelpelley
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 66e415070e60676df5602078aff50c7b68920f1e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77914045"
---
# <a name="memory-optimized-dv2-and-dsv2-series"></a>Zoptymalizowane pod kątem pamięci seria Dv2 i Dsv2

Dv2 i Dsv2-series, kontynuacja oryginalnej serii D, posiada mocniejszy procesor. Rozmiary serii DSv2 działają na procesorach Intel® Xeon® 8171M 2.1 GHz (Skylake) lub Intel® Xeon® E5-1 2673 v4 2.3 GHz (Broadwell) lub Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell). Konfiguracje pamięci i dysków serii Dv2 są takie same jak w przypadku serii D.

## <a name="dv2-series-11-15"></a>Seria Dv2 11-15

Rozmiary serii Dv2 działają na procesorze Intel® Xeon® 8171M 2.1 GHz (Skylake) lub Intel® Xeon® E5-2 673 v4 2.3 GHz (Broadwell) lub Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell).

ACU: 210–250

Pamięć masowa w wersji Premium: nie jest obsługiwana

Buforowanie magazynu w wersji Premium: nie jest obsługiwane

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maksymalna przepustowość magazynu temp: IOPS / Odczyt MB/Zapis MB/s | Maksymalna ilość dysków/przepływności danych: Usługi We/Wy | Maksymalna przepustowość sieci ową/oczekiwana przepustowość sieci (Mb/s) |
|---|---|---|---|---|---|---|
| Standardowa_D11_v2 | 2  | 14  | 100 | 6000/93/46    | 8/8x500   | 2/1500  |
| Standardowa_D12_v2 | 4  | 28  | 200 | 12000/187/93  | 16/16x500 | 4/3000  |
| Standardowa_D13_v2 | 8  | 56  | 400 | 24000/375/187 | 32/32x500 | 8/6000  |
| Standardowa_D14_v2 | 16 | 112 | 800 | 48000/750/375 | 64/64x500 | 8/12000 |
| Standard_D15_v2 <sup>1</sup> | 20 | 140 | 1000 | 60000/937/468 | 64/64x500 | 8/25000 <sup>2</sup> |

<sup>1</sup> Wystąpienie jest izolowane do sprzętu dedykowanego jednemu klientowi.
<sup>2</sup> 25000 Mb/s z przyspieszoną siecią.

## <a name="dsv2-series-11-15"></a>Seria DSv2 11-15

Rozmiary serii DSv2 działają na procesorach Intel® Xeon® 8171M 2.1 GHz (Skylake) lub Intel® Xeon® E5-1 2673 v4 2.3 GHz (Broadwell) lub Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell).

ACU: 210 - 250 <sup>1</sup>

Pamięć masowa w wersji premium: obsługiwane

Buforowanie magazynu w wersji Premium: obsługiwane

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maks. liczba dysków danych | Maksymalna przepływność pamięci podręcznej i magazynu tymczasowego: IOPS/MBPS (rozmiar pamięci podręcznej w GiB) | Maksymalna przepustowość dysku bez buforów: IOPS/MB/s | Maksymalna przepustowość sieci ową/oczekiwana przepustowość sieci (Mb/s) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS11_v2 <sup>3</sup> | 2  | 14  | 28  | 8  | 8000/64 (72)    | 6400/96   | 2/1500  |
| Standard_DS12_v2 <sup>3</sup> | 4  | 28  | 56  | 16 | 16000/128 (144) | 12800/192 | 4/3000  |
| Standard_DS13_v2 <sup>3</sup> | 8  | 56  | 112 | 32 | 32000/256 (288) | 25600/384 | 8/6000  |
| Standard_DS14_v2 <sup>3</sup> | 16 | 112 | 224 | 64 | 64000/512 (576) | 51200/768 | 8/12000 |
| Standard_DS15_v2 <sup>2</sup> | 20 | 140 | 280 | 64 | 80000/640 (720) | 64000/960 | 8/25000 <sup>4</sup> |

<sup>1</sup> Maksymalna przepustowość dysku (IOPS lub MB/s) możliwa w maszynie wirtualnej z serii DSv2 może być ograniczona liczbą, rozmiarem i rozkładem podłączonych dysków.  Aby uzyskać szczegółowe informacje, zobacz [Projektowanie w celu uzyskania wysokiej wydajności](./windows/premium-storage-performance.md).
<sup>2</sup> Wystąpienie jest odizolowane od sprzętu opartego na technologii Intel Haswell i dedykowane jednemu klientowi.  
<sup>3</sup> Dostępne ograniczone rozmiary rdzeni.  
<sup>4</sup> 25000 Mb/s z przyspieszoną siecią.

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
