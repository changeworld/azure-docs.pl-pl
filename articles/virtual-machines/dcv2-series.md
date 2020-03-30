---
title: Seria DC — maszyny wirtualne platformy Azure
description: Dane techniczne maszyn wirtualnych serii DC.
services: virtual-machines
author: susaxen
ms.service: virtual-machines
ms.topic: article
ms.date: 02/20/2020
ms.author: lahugh
ms.openlocfilehash: 7834c8a32d4d85fc354bac209e13f19f3b8315fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80256933"
---
# <a name="preview-dcsv2-series"></a>Podgląd: seria DCsv2


Seria DCsv2 może pomóc chronić poufność i integralność danych i kodu podczas przetwarzania w chmurze publicznej. Maszyny te są wspierane przez najnowszą generację procesora Intel XEON E-2288G z technologią SGX. Dzięki technologii Intel Turbo Boost maszyny te mogą zwiększyć 5,0 GHz. Wystąpienia serii DCsv2 umożliwiają klientom tworzenie bezpiecznych aplikacji opartych na enklawie w celu ochrony ich kodu i danych, gdy są używane.

Przykładowe przypadki użycia obejmują poufne wielopartyjne udostępnianie danych, wykrywanie oszustw, przeciwdziałanie praniu pieniędzy, blockchain, analizę poufnych zastosowań, analizę danych wywiadowczych i poufne uczenie maszynowe.

Pamięć masowa w wersji Premium: obsługiwana*

Buforowanie pamięci masowej w wersji premium: obsługiwane*

Migracja na żywo: nie jest obsługiwana

Aktualizacje zachowywania pamięci: nie są obsługiwane

*Z wyjątkiem Standard_DC8_v2



| Rozmiar             | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maks. liczba dysków danych | Maksymalna przepływność magazynu buforowanego i tymczasowego: liczba operacji we/wy na sekundę / MB/s (rozmiar pamięci podręcznej w GiB) | Maksymalna przepływność niebuforowanych dysków: liczba operacji we/wy na sekundę / MB/s | Maksymalna karta sieciowa / oczekiwana przepustowość sieci (MB/s) |
|------------------|------|-------------|------------------------|----------------|-------------------------------------------------------------------------|-------------------------------------------|----------------------------------------------|
| Standard_DC1s_v2 | 1    | 4           | 50                     | 1              | 2000/16 (21)                                                            | 1600/24                                   | 2                                            |
| Standard_DC2s_v2 | 2    | 8           | 100                    | 2              | 4000/32 (43)                                                            | 3200/48                                   | 2                                            |
| Standard_DC4s_v2 | 4    | 16          | 200                    | 4              | 8000/64 (86)                                                            | 6400/96                                   | 2                                            |
| Standard_DC8_v2  | 8   | 32          | 400                    | 8              | 16000/128 (172)                                                         | 12800/192                                 | 2                                            |

- Maszyny wirtualne z serii DCsv2 są [generacjami maszyn wirtualnych 2](./linux/generation-2.md#creating-a-generation-2-vm) i obsługują `Gen2` tylko obrazy.
- Obecnie dostępne tylko w Wielkiej Brytanii Południowej i Kanadzie Środkowej.
- Poprzednia generacja poufnych maszyn wirtualnych obliczeniowych: [seria DC](sizes-previous-gen.md)
- Tworzenie maszyn wirtualnych DCsv2 przy użyciu maszyny [wirtualnej](./linux/quick-create-portal.md) Azure Portal Create — portal



## <a name="other-sizes"></a>Inne rozmiary

- [Zastosowania ogólne](sizes-general.md)
- [Optymalizacja pod kątem pamięci](sizes-memory.md)
- [Optymalizacja pod kątem magazynu](sizes-storage.md)
- [Optymalizacja pod kątem procesora GPU](sizes-gpu.md)
- [Obliczenia o wysokiej wydajności](sizes-hpc.md)
- [Poprzednie generacje](sizes-previous-gen.md)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o tym, jak [jednostki obliczeniowe platformy Azure (ACU)](acu.md) mogą pomóc w porównywaniu wydajności obliczeniowej w jednostkach SKU platformy Azure.
