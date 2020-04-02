---
title: Seria M — maszyny wirtualne platformy Azure
description: Dane techniczne maszyn wirtualnych serii M.
services: virtual-machines
author: ayshakeen
ms.service: virtual-machines
ms.topic: article
ms.date: 03/31/2020
ms.author: jushiman
ms.openlocfilehash: 3e0552570d5bdb7f812852cd058710b833b7fdd4
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521344"
---
# <a name="m-series"></a>Seria M

Seria M oferuje wysoką liczbę procesorów wirtualnych (do 128 procesorów wirtualnych) i dużą ilość pamięci (do 3,8 TiB). Jest również idealny dla bardzo dużych baz danych lub innych aplikacji, które korzystają z wysokiej liczby procesorów wirtualnych i dużych ilości pamięci. Rozmiary serii M są obsługiwane zarówno&reg; w&reg; procesorach Intel Xeon E7-8890 v3 @ 2.50GHz, jak i w Intel&reg; Xeon&reg; Platinum 8280M 2.7GHz (Cascade Lake).

VM serii M jest&reg; wyposażony w technologię Intel Hyper-Threading.

ACU: 160–180

Pamięć masowa w wersji premium: obsługiwane

Buforowanie magazynu w wersji Premium: obsługiwane

Migracja na żywo: nie jest obsługiwana

Aktualizacje zachowywania pamięci: nie są obsługiwane

Akcelerator zapisu: [Obsługiwane](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maks. liczba dysków danych | Maksymalna przepływność pamięci podręcznej i magazynu tymczasowego: IOPS/MBPS (rozmiar pamięci podręcznej w GiB) | Maksymalna przepustowość dysku bez buforów: IOPS/MB/s | Maksymalna przepustowość sieci ową/oczekiwana przepustowość sieci (Mb/s) |
|---|---|---|---|---|---|---|---|
| Standard_M8ms <sup>2</sup>       | 8   | 218.75 | 256   | 8  | 10000/100 (793)     | 5000/125   | 4/2000  |
| Standard_M16ms <sup>2</sup>      | 16  | 437.5  | 512   | 16 | 20000/200 (1587)    | 10000/250  | 8/4000  |
| Standard_M32ts                   | 32  | 192    | 1024  | 32 | 40000/400 (3174)    | 20000/500  | 8/8000  |
| Standard_M32ls                   | 32  | 256    | 1024  | 32 | 40000/400 (3174)    | 20000/500  | 8/8000  |
| Standard_M32ms <sup>2</sup>      | 32  | 875    | 1024  | 32 | 40000/400 (3174)    | 20000/500  | 8/8000  |
| Standard_M64s                    | 64  | 1024   | 2048  | 64 | 80000/800 (6348)    | 40000/1000 | 8/16000 |
| Standard_M64ls                   | 64  | 512    | 2048  | 64 | 80000/800 (6348)    | 40000/1000 | 8/16000 |
| Standard_M64ms <sup>3</sup>      | 64  | 1792   | 2048  | 64 | 80000/800 (6348)    | 40000/1000 | 8/16000 |
| Standard_M128s <sup>1</sup>      | 128 | 2048   | 4096  | 64 | 160000/1600 (12696) | 80000/2000 | 8/30000 |
| Standard_M128ms <sup>1,2,3</sup> | 128 | 3892   | 4096  | 64 | 160000/1600 (12696) | 80000/2000 | 8/30000 |
| Standard_M64                     | 64  | 1024   | 7168  | 64 | 80000/800 (1228)    | 40000/1000 | 8/16000 |
| Standard_M64m                    | 64  | 1792   | 7168  | 64 | 80000/800 (1228)    | 40000/1000 | 8/16000 |
| Standard_M128 <sup>1</sup>       | 128 | 2048   | 14336 | 64 | 250000/1600 (2456)  | 80000/2000 | 8/32000 |
| Standard_M128m <sup>1</sup>      | 128 | 3892   | 14336 | 64 | 250000/1600 (2456)  | 80000/2000 | 8/32000 |

<sup>1</sup> Ponad 64 procesory wirtualne wymagają jednego z tych obsługiwanych systemów operacyjnych gościa: Windows Server 2016, Ubuntu 16.04 LTS, SLES 12 SP2 i Red Hat Enterprise Linux, CentOS 7.3 lub Oracle Linux 7.3 z LIS 4.2.1.

<sup>2</sup> Dostępne ograniczone rozmiary rdzeni.

<sup>3</sup> Wystąpienie jest izolowane do sprzętu dedykowanego jednemu klientowi.

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
