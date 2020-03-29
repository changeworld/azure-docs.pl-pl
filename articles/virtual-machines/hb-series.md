---
title: Seria HB — maszyny wirtualne platformy Azure
description: Dane techniczne maszyn wirtualnych serii HB.
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 600f10e81742e9bb66c800b747fd7b2dc062754d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164835"
---
# <a name="hb-series"></a>Seria HB

Maszyny wirtualne z serii HB są zoptymalizowane pod kątem aplikacji opartych na przepustowości pamięci, takich jak dynamika płynów, jawna analiza elementów skończonych i modelowanie pogody. Maszyny wirtualne HB są wyposażone w 60 rdzeni procesorów AMD EPYC 7551, 4 GB pamięci RAM na rdzeń procesora i nie ma jednoczesnej wielowątkowości. Maszyna wirtualna HB zapewnia przepustowość pamięci do 260 GB/s.

ACU: 199-216

Pamięć masowa w wersji premium: obsługiwane

Buforowanie magazynu w jakości Premium: obsługiwane

Migracja na żywo: nie jest obsługiwana

Aktualizacje zachowywania pamięci: nie są obsługiwane

| Rozmiar | Procesor wirtualny | Procesor | Pamięć (GB) | Przepustowość pamięci GB/s | Podstawowa częstotliwość procesora (GHz) | Częstotliwość wszystkich rdzeni (GHz, szczyt) | Częstotliwość jednożyłowa (GHz, szczytowa) | Wydajność RDMA (Gb/s) | Obsługa MPI | Pamięć tymczasowa (GB) | Maks. liczba dysków danych | Maksymalne karty sieciowe Ethernet |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HB60rs | 60 | AMD EPYC 7551 | 240 | 263 | 2.0 | 2.55 | 2.55 | 100 | Wszystkie | 700 | 4 | 1 |

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