---
title: Seria Fsv2 — maszyny wirtualne platformy Azure
description: Dane techniczne maszyn wirtualnych serii Fsv2.
services: virtual-machines
author: brbell
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: f28c6b61aee3c8cbc078db1c2cfb48ed1fba4554
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164852"
---
# <a name="fsv2-series"></a>Seria Fsv2

Seria Fsv2 jest oparta na procesorze Intel® Xeon® Platinum 8168. Posiada trwałą prędkość zegara Turbo całego rdzenia 3,4 GHz i maksymalną jednordzeniową częstotliwość turbo 3,7 GHz. Instrukcje Intel® AVX-512 są nowe w przypadku procesorów Intel Scalable. Te instrukcje zapewniają maksymalnie 2-krotny wzrost wydajności do obciążeń przetwarzania wektorowego zarówno w operacjach zmiennoprzecinkowych pojedynczej, jak i podwójnej precyzji. Innymi słowy, są one bardzo szybkie dla każdego obciążenia obliczeniowego.

Maszyny wirtualne z serii Fsv2 są wyposażone w technologię Intel® Hyper-Threading.

ACU: 195 - 210

Pamięć masowa w wersji premium: obsługiwane

Buforowanie magazynu w wersji Premium: obsługiwane

Migracja na żywo: obsługiwane

Aktualizacje zachowywania pamięci: Obsługiwane

| Rozmiar | procesory wirtualne | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maks. liczba dysków danych | Maksymalna przepływność pamięci podręcznej i magazynu tymczasowego: IOPS/MBPS (rozmiar pamięci podręcznej w GiB) | Maksymalna przepustowość dysku bez buforów: IOPS/MB/s | Maksymalna przepustowość sieci ową/oczekiwana przepustowość sieci (Mb/s) |
|---|---|---|---|---|---|---|---|
| Standard_F2s_v2  | 2  | 4   | 16  | 4  | 4000/31 (32)       | 3200/47    | 2/875   |
| Standard_F4s_v2  | 4  | 8   | 32  | 8  | 8000/63 (64)       | 6400/95    | 2/1750  |
| Standard_F8s_v2  | 8  | 16  | 64  | 16 | 16000/127 (128)    | 12800/190  | 4/3500  |
| Standard_F16s_v2 | 16 | 32  | 128 | 32 | 32000/255 (256)    | 25600/380  | 4/7000  |
| Standard_F32s_v2 | 32 | 64  | 256 | 32 | 64000/512 (512)    | 51200/750  | 8/14000 |
| Standard_F48s_v2 | 48 | 96  | 384 | 32 | 96000/768 (768)    | 76800/1100 | 8/21000 |
| Standard_F64s_v2 | 64 | 128 | 512 | 32 | 128000/1024 (1024) | 80000/1100 | 8/28000 |
| Standard_F72s_v2<sup>1, 2</sup> | 72 | 144 | 576 | 32 | 144000/1152 (1520) | 80000/1100 | 8/30000 |

<sup>1</sup> Użycie więcej niż 64 procesora wirtualnego wymaga jednego z tych obsługiwanych systemów operacyjnych gościa:

- Windows Server 2016 lub nowsza
- Ubuntu 16.04 LTS lub nowsze, z jądrem dostrojonym na platformie Azure (jądro 4.15 lub nowsze)
- SLES 12 SP2 lub nowsza
- RHEL lub CentOS w wersji od 6.7 do 6.10, z zainstalowanym pakietem LIS dostarczonym przez firmę Microsoft 4.3.1 (lub nowszym)
- RHEL lub CentOS w wersji 7.3, z zainstalowanym pakietem LIS dostarczonym przez firmę Microsoft 4.2.1 (lub nowszym)
- RHEL lub CentOS w wersji 7.6 lub nowszej
- Oracle Linux z UEK4 lub nowszym
- Debian 9 z jądrem backports, Debian 10 lub nowszym
- CoreOS z jądrem 4.14 lub nowszym

<sup>2</sup> Wystąpienie jest izolowane do sprzętu dedykowanego jednemu klientowi.

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
