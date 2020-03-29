---
title: Seria HC — maszyny wirtualne platformy Azure
description: Dane techniczne maszyn wirtualnych serii HC.
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: cc25fb9b21d535ef07bcfae673be48216427b370
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164784"
---
# <a name="hc-series"></a>Seria HC

Maszyny wirtualne serii HC są zoptymalizowane pod kątem aplikacji opartych na gęstych obliczeniach, takich jak niejawna analiza elementów skończonych, dynamika molekularna i chemia obliczeniowa. Maszyny wirtualne HC są wyposażone w 44 rdzenie procesora Intel Xeon Platinum 8168, 8 GB pamięci RAM na rdzeń procesora i brak hiperwątkowości. Platforma Intel Xeon Platinum obsługuje bogaty ekosystem narzędzi programowych firmy Intel, takich jak biblioteka jądra matematycznego Intel.

ACU: 297-315

Pamięć masowa w wersji premium: obsługiwane

Buforowanie magazynu w jakości Premium: obsługiwane

Migracja na żywo: nie jest obsługiwana

Aktualizacje zachowywania pamięci: nie są obsługiwane

| Rozmiar | Procesor wirtualny | Procesor | Pamięć (GB) | Przepustowość pamięci GB/s | Podstawowa częstotliwość procesora (GHz) | Częstotliwość wszystkich rdzeni (GHz, szczyt) | Częstotliwość jednożyłowa (GHz, szczytowa) | Wydajność RDMA (Gb/s) | Obsługa MPI | Pamięć tymczasowa (GB) | Maks. liczba dysków danych | Maksymalne karty sieciowe Ethernet |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HC44rs | 44 | Intel Xeon Platinum 8168 | 352 | 191 | 2.7 | 3.4 | 3.7 | 100 | Wszystkie | 700 | 4 | 1 |

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