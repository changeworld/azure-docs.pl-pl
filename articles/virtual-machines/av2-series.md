---
title: Seria Av2 — maszyny wirtualne platformy Azure
description: Dane techniczne maszyn wirtualnych serii Av2.
services: virtual-machines
author: migerdes
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: fe27a1cc78d1f37d535f364c03803a5196090a3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78163951"
---
# <a name="av2-series"></a>Seria Av2

Maszyny wirtualne z serii Av2 można wdrożyć na różnych typach sprzętu i procesorach. Maszyny wirtualne z serii Av2 mają wydajność procesora i konfiguracje pamięci najlepiej dostosowane do obciążeń na poziomie podstawowym, takich jak program i testowanie. Rozmiar jest ograniczany, aby zapewnić stałą wydajność procesora dla uruchomionego wystąpienia, niezależnie od sprzętu, na który jest wdrażany. Aby określić sprzęt fizyczny, na którym jest wdrażany dany rozmiar, utwórz zapytanie o sprzęt wirtualny z poziomu maszyny wirtualnej. Niektóre przykładowe przypadki użycia obejmują serwery deweloperów i testów, serwery sieci web o małym i małym ruchu, małe i średnie bazy danych, weryfikacji koncepcji i repozytoria kodu.

ACU: 100

Pamięć masowa w wersji Premium: nie jest obsługiwana

Buforowanie magazynu w wersji Premium: nie jest obsługiwane

Migracja na żywo: obsługiwane

Aktualizacje zachowywania pamięci: Obsługiwane

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maksymalna przepustowość magazynu temp: IOPS / Odczyt MB/Zapis MB/s | Maksymalna ilość dysków/przepływności danych: Usługi We/Wy | Maksymalna przepustowość sieci ową/oczekiwana przepustowość sieci (Mb/s) |
|---|---|---|---|---|---|---|
| Standardowa_A1_v2  | 1 | 2  | 10 | 1000/20/10  | 2/2x500   | 2/250  |
| Standardowa_A2_v2  | 2 | 4  | 20 | 2000/40/20  | 4/4x500   | 2/500  |
| Standardowa_A4_v2  | 4 | 8  | 40 | 4000/80/40  | 8/8x500   | 4/1000 |
| Standardowa_A8_v2  | 8 | 16 | 80 | 8000/160/80 | 16/16x500 | 8/2000 |
| Standardowa_A2m_v2 | 2 | 16 | 20 | 2000/40/20  | 4/4x500   | 2/500  |
| Standardowa_A4m_v2 | 4 | 32 | 40 | 4000/80/40  | 8/8x500   | 4/1000 |
| Standardowa_A8m_v2 | 8 | 64 | 80 | 8000/160/80 | 16/16x500 | 8/2000 |

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
