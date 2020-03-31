---
title: Rozmiary maszyn wirtualnych platformy Azure — pamięć | Dokumenty firmy Microsoft
description: Wyświetla listę różnych rozmiarów zoptymalizowanych pod kątem pamięci dostępnych dla maszyn wirtualnych na platformie Azure. Wyświetla informacje o liczbie procesorów wirtualnych, dysków danych i kart sieciowych, a także przepustowości magazynu i przepustowości sieci dla rozmiarów w tej serii.
services: virtual-machines
documentationcenter: ''
author: jonbeck7
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
keywords: Izolacja maszyny Wirtualnej,izolowana maszyna wirtualna,izolacja,odizolowana
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: jonbeck
ms.openlocfilehash: 26a44cdf3e57508cebb070669a8c1f473a59df8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77493529"
---
# <a name="memory-optimized-virtual-machine-sizes"></a>Rozmiary maszyn wirtualnych zoptymalizowane pod kątem pamięci

Rozmiary maszyn wirtualnych zoptymalizowane pod kątem pamięci oferują wysoki współczynnik pamięci do procesora, który doskonale sprawdza się w przypadku relacyjnych serwerów baz danych, średnich i dużych pamięci podręcznych oraz analizy w pamięci. Ten artykuł zawiera informacje o liczbie procesorów wirtualnych, dysków danych i kart sieciowych, a także przepływności i przepustowości sieci magazynu dla każdego rozmiaru w tym grupowaniu.

- [Dv2 i DSv2-series](dv2-dsv2-series-memory.md), kontynuacja oryginalnej serii D, posiada mocniejszy procesor. Seria Dv2 jest o około 35% szybsza niż seria D. Działa na Intel® Xeon® 8171M 2.1 GHz (Skylake) lub Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell) lub Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) (Haswell), oraz z technologią Intel Turbo Boost 2.0. Konfiguracje pamięci i dysków serii Dv2 są takie same jak w przypadku serii D.

    Seria Dv2 i DSv2 idealnie nadaje się do zastosowań wymagających szybszych procesorów wirtualnych, lepszej wydajności pamięci tymczasowej lub wyższych wymagań dotyczących pamięci. Oferują one kombinację opcji o dużych możliwościach dla wielu aplikacji klasy korporacyjnej.

- [Seria Eav4 i Easv4](eav4-easv4-series.md) wykorzystuje procesor AMD 2.35Ghz EPYC<sup>TM</sup> 7452 w konfiguracji wielowątkowej z pamięcią podręczną L3 o rozmiarze do 256 MB, zwiększając możliwości uruchamiania większości obciążeń zoptymalizowanych pod kątem pamięci. Seria Eav4 i Easv4 mają taką samą konfigurację pamięci i dysków jak ev3 & serii Esv3.

- Procesor Intel® Xeon z [serii Ev3 i Esv3®](ev3-esv3-series.md) 8171M 2.1 GHz (Skylake) lub procesor Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell) w konfiguracji hiperwątkowa, zapewnia lepszą ofertę wartości dla większości obciążeń ogólnego przeznaczenia i dostosowania Ev3 do maszyn wirtualnych ogólnego przeznaczenia większości innych chmur. Pamięć została rozszerzona (z 7 GiB/vCPU do 8 GiB/vCPU), podczas gdy limity dysków i sieci zostały dostosowane na podstawie rdzenia, aby wyrównać z przejściem do hiperwątkowości. Ev3 jest kontynuacją wysokiej pamięci rozmiarów maszyn wirtualnych z rodzin D/Dv2.

- Seria [M](m-series.md) oferuje wysoką liczbę procesorów wirtualnych (do 128 procesorów wirtualnych) i dużą ilość pamięci (do 3,8 TiB). Jest również idealny dla bardzo dużych baz danych lub innych aplikacji, które korzystają z wysokiej liczby procesorów wirtualnych i dużych ilości pamięci.

- [Seria Mv2](mv2-series.md) oferuje najwyższą liczbę procesorów wirtualnych (do 416 procesorów wirtualnych) i największą pamięć (do 8,19 TiB) dowolnej maszyny wirtualnej w chmurze. Jest to idealne rozwiązanie dla bardzo dużych baz danych lub innych aplikacji, które korzystają z dużej liczby procesorów wirtualnych vCPU i dużych ilości pamięci.

Usługa Azure Compute oferuje rozmiary maszyn wirtualnych, które są izolowane do określonego typu sprzętu i dedykowane jednemu klientowi. Te rozmiary maszyn wirtualnych najlepiej nadają się do obciążeń, które wymagają wysokiego stopnia izolacji od innych klientów dla obciążeń obejmujących elementy, takie jak zgodność i wymagania prawne. Klienci mogą również zdecydować się na dalsze podział zasobów tych izolowanych maszyn wirtualnych przy użyciu [obsługi platformy Azure dla zagnieżdżonych maszyn wirtualnych.](https://azure.microsoft.com/blog/nested-virtualization-in-azure/) Zobacz strony dla rodzin maszyn wirtualnych poniżej dla izolowanych opcji maszyny wirtualnej.

## <a name="other-sizes"></a>Inne rozmiary

- [Zastosowania ogólne](sizes-general.md)
- [Optymalizacja pod kątem obliczeń](sizes-compute.md)
- [Optymalizacja pod kątem magazynu](sizes-storage.md)
- [Optymalizacja pod kątem procesora GPU](sizes-gpu.md)
- [Obliczenia o wysokiej wydajności](sizes-hpc.md)
- [Poprzednie generacje](sizes-previous-gen.md)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o tym, jak [jednostki obliczeniowe platformy Azure (ACU)](acu.md) mogą pomóc w porównywaniu wydajności obliczeniowej w jednostkach SKU platformy Azure.