---
title: Seria DC-Virtual Machines platformy Azure
description: Specyfikacje dotyczące maszyn wirtualnych z serii DC.
services: virtual-machines
author: susaxen
ms.service: virtual-machines
ms.topic: article
ms.date: 02/20/2020
ms.author: lahugh
ms.openlocfilehash: d500f7a1f28eff1f638a244022226b0aa0e715cb
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78199601"
---
# <a name="preview-dcv2-series"></a>Wersja zapoznawcza: Seria DCv2


Seria DCv2 może pomóc w ochronie poufności i integralności danych oraz kodu podczas przetwarzania w chmurze publicznej. Te maszyny są obsługiwane przez najnowszą generację procesora Intel XEON E-2288G z technologią SGX. Dzięki technologii zwiększania procesora Intel Turbo te maszyny mogą mieć wartość do 5,0 GHz. Wystąpienia serii DCv2 umożliwiają klientom tworzenie bezpiecznych aplikacji opartych na enklawy w celu ochrony kodu i danych, gdy jest używany.

Przykładowe przypadki użycia obejmują poufne udostępnianie danych wieloczęściowych, wykrywanie oszustw, pranie antypieniężne, łańcucha bloków, poufne analizy użycia, analizę analizy oraz poufne Uczenie maszynowe.

Premium Storage: obsługiwane *

Buforowanie Premium Storage: obsługiwane *

Migracja na żywo: nieobsługiwane

Aktualizacje z zachowaniem pamięci: nieobsługiwane

\* Z wyjątkiem Standard_DC8_v2



| Rozmiar             | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maks. liczba dysków danych | Maksymalna przepływność magazynu buforowanego i tymczasowego: liczba operacji we/wy na sekundę / MB/s (rozmiar pamięci podręcznej w GiB) | Maksymalna przepływność niebuforowanych dysków: liczba operacji we/wy na sekundę / MB/s | Maksymalna liczba kart sieciowych/oczekiwana przepustowość sieci (MB/s) |
|------------------|------|-------------|------------------------|----------------|-------------------------------------------------------------------------|-------------------------------------------|----------------------------------------------|
| Standard_DC1s_v2 | 1    | 4           | 50                     | 1              | 2000/16 (21)                                                            | 1600/24                                   | 2                                            |
| Standard_DC2s_v2 | 2    | 8           | 100                    | 2              | 4000/32 (43)                                                            | 3200/48                                   | 2                                            |
| Standard_DC4s_v2 | 4    | 16          | 200                    | 4              | 8000/64 (86)                                                            | 6400/96                                   | 2                                            |
| Standard_DC8_v2  | 8   | 32          | 400                    | 8              | 16000/128 (172)                                                         | 12800/192                                 | 2                                            |

- Maszyny wirtualne z serii DCv2 to [maszyny wirtualne 2. generacji](./linux/generation-2.md#creating-a-generation-2-vm) i obsługują tylko obrazy `Gen2`.




## <a name="other-sizes"></a>Inne rozmiary

- [Zastosowania ogólne](sizes-general.md)
- [Optymalizacja pod kątem pamięci](sizes-memory.md)
- [Optymalizacja pod kątem magazynu](sizes-storage.md)
- [Optymalizacja pod kątem procesora GPU](sizes-gpu.md)
- [Obliczenia o wysokiej wydajności](sizes-hpc.md)
- [Poprzednie generacje](sizes-previous-gen.md)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o tym, jak [usługa Azure COMPUTE units (ACU)](acu.md) może pomóc w porównaniu wydajności obliczeniowej w ramach jednostek SKU platformy Azure.
