---
title: Seria NVv4 — maszyny wirtualne platformy Azure
description: Dane techniczne maszyn wirtualnych serii NVv4.
services: virtual-machines
author: vikancha
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 0295ed7d44d64fcc1aeb68e1beaa37987b177edb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273858"
---
# <a name="nvv4-series"></a>Seria NVv4 

Maszyny wirtualne serii NVv4 są obsługiwane przez procesory graficzne [AMD Radeon Instinct MI25](https://www.amd.com/en/products/professional-graphics/instinct-mi25) i procesory AMD EPYC 7V12 (Rzym). Platforma Azure z serii NVv4 wprowadza maszyny wirtualne z częściowymi procesorami GPU. Wybierz odpowiednią maszynę wirtualną dla aplikacji graficznych akcelerowanych GPU i pulpitów wirtualnych, począwszy od 1/8 procesora graficznego z buforem 2 ramek GiB do pełnego procesora graficznego z buforem ramki 16 GiB. Maszyny wirtualne NVv4 obsługują obecnie tylko system operacyjny windowsa dla gości.

<br>

Pamięć masowa w wersji premium: obsługiwane

Buforowanie magazynu w wersji Premium: obsługiwane

Migracja na żywo: nie jest obsługiwana

Aktualizacje zachowywania pamięci: nie są obsługiwane

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Procesory GPU | Pamięć GPU: GiB | Maks. liczba dysków danych | Maksymalna liczba kart sieciowych |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV4as_v4 |4 |14 |88 | 1/8 | 2 | 4 | 2 |
| Standard_NV8as_v4 |8 |28 |176 | 1/4 | 4 | 8 | 4 |
| Standard_NV16as_v4 |16 |56 |352 | 1/2 | 8 | 16 | 8 |
| Standard_NV32as_v4 |32 |112 |704 | 1 | 16 | 32 | 8 |

<sup>1</sup> maszyny wirtualne serii NVv4 są wyposażone w technologię multiwątników AMD Simultaneous

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Obsługiwane systemy operacyjne i sterowniki

Aby korzystać z możliwości procesorów GPU maszyn wirtualnych z serii Azure NVv4 z systemem Windows, należy zainstalować sterowniki procesora graficznego AMD.

Aby ręcznie zainstalować sterowniki procesorów graficznych AMD, zobacz [Konfiguracja sterowników GPU AMD serii N dla systemu Windows,](./windows/n-series-amd-driver-setup.md) aby uzyskać informacje o obsługiwanych systemach operacyjnych, sterownikach, instalacji i weryfikacji.

## <a name="other-sizes"></a>Inne rozmiary

- [Zastosowania ogólne](sizes-general.md)
- [Optymalizacja pod kątem pamięci](sizes-memory.md)
- [Optymalizacja pod kątem magazynu](sizes-storage.md)
- [Optymalizacja pod kątem procesora GPU](sizes-gpu.md)
- [Obliczenia o wysokiej wydajności](sizes-hpc.md)
- [Poprzednie generacje](sizes-previous-gen.md)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o tym, jak [jednostki obliczeniowe platformy Azure (ACU)](acu.md) mogą pomóc w porównywaniu wydajności obliczeniowej w jednostkach SKU platformy Azure.
