---
title: Seria HBv2 — maszyny wirtualne platformy Azure
description: Dane techniczne maszyn wirtualnych serii HBv2.
services: virtual-machines
author: vermagit
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: amverma
ms.openlocfilehash: eea649610ca53ccbb98b5ca361555280dcd3dafe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164801"
---
# <a name="hbv2-series"></a>Seria HBv2

Maszyny wirtualne serii HBv2 są zoptymalizowane pod kątem aplikacji opartych na przepustowości pamięci, takich jak dynamika płynów, analiza elementów skończonych i symulacja zbiornika. Maszyny wirtualne HBv2 są wyposażone w 120 rdzeni procesora AMD EPYC 7742, 4 GB pamięci RAM na rdzeń procesora i brak jednoczesnej wielowątkowej wersji. Każda maszyna wirtualna HBv2 zapewnia przepustowość pamięci do 340 GB/s i do 4 teraFLOPS danych obliczeniowych FP64.

Pamięć masowa w wersji premium: obsługiwane

Migracja na żywo: nie jest obsługiwana

Aktualizacje zachowywania pamięci: nie są obsługiwane

| Rozmiar | Procesor wirtualny | Procesor | Pamięć (GB) | Przepustowość pamięci GB/s | Podstawowa częstotliwość procesora (GHz) | Częstotliwość wszystkich rdzeni (GHz, szczyt) | Częstotliwość jednożyłowa (GHz, szczytowa) | Wydajność RDMA (Gb/s) | Obsługa MPI | Pamięć tymczasowa (GB) | Maks. liczba dysków danych | Maksymalne karty sieciowe Ethernet |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HB120rs_v2 | 120 | AMD EPYC 7V12 | 480 | 350 | 2.45 | 3.1 | 3.3 | 200 | Wszystkie | 480 + 960 | 8 | 1 |


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