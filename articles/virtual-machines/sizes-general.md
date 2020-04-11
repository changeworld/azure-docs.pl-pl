---
title: Rozmiary maszyn wirtualnych platformy Azure — przeznaczenie ogólne | Dokumenty firmy Microsoft
description: Wyświetla listę różnych rozmiarów ogólnego przeznaczenia dostępnych dla maszyn wirtualnych na platformie Azure. Wyświetla informacje o liczbie procesorów wirtualnych, dysków danych i kart sieciowych, a także przepustowości magazynu i przepustowości sieci dla rozmiarów w tej serii.
services: virtual-machines
documentationcenter: ''
author: jonbeck7
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/20/2020
ms.author: jonbeck
ms.openlocfilehash: 22826c32dc2aee0e580ec0b2a05c7eb8f08b7570
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/10/2020
ms.locfileid: "81115313"
---
# <a name="general-purpose-virtual-machine-sizes"></a>Rozmiary maszyn wirtualnych ogólnego przeznaczenia

Rozmiary maszyn wirtualnych ogólnego przeznaczenia zapewniają zrównoważony stosunek procesora CPU do pamięci. Idealne rozwiązanie na potrzeby testowania i wdrażania, małych i średnich baz danych oraz serwerów internetowych o małym lub średnim ruchu. Ten artykuł zawiera informacje o ofertach do komputerów ogólnego przeznaczenia.

- Maszyny [wirtualne z serii Av2](av2-series.md) można wdrożyć na różnych typach sprzętu i procesorach. Maszyny wirtualne z serii A mają wydajność procesora i konfiguracje pamięci najlepiej dostosowane do obciążeń na poziomie podstawowym, takich jak programistyczne i testowe. Rozmiar jest ograniczany w zależności od sprzętu, aby zapewnić spójną wydajność procesora dla uruchomionego wystąpienia niezależnie od sprzętu, na którym jest ono wdrożone. Aby określić sprzęt fizyczny, na którym jest wdrażany dany rozmiar, utwórz zapytanie o sprzęt wirtualny z poziomu maszyny wirtualnej. Przykładowe przypadki użycia obejmują serwery deweloperów i testów, serwery sieci web o małym i małym ruchu, małe i średnie bazy danych, weryfikacji koncepcji i repozytoria kodu.

  > [!NOTE]
  > Maszyny wirtualne A8 – A11 są planowane na emeryturę w dniu 3/2021. Aby uzyskać więcej informacji, zobacz [Przewodnik po migracji HPC](https://azure.microsoft.com/resources/hpc-migration-guide/).

- [Seria B z rozerwana](sizes-b-series-burstable.md) Maszyny wirtualne są idealne dla obciążeń, które nie wymagają pełnej wydajności procesora CPU w sposób ciągły, takich jak serwery sieci web, małe bazy danych oraz środowiska programistyczne i testowe. Te obciążenia zazwyczaj mają wymagania dotyczące wydajności serii. Seria B zapewnia tym klientom możliwość zakupu rozmiaru maszyny Wirtualnej z wydajnością linii bazowej ze względu na cenę, która umożliwia wystąpieniu maszyny Wirtualnej tworzenie kredytów, gdy maszyna wirtualna wykorzystuje mniej niż jego podstawowa wydajność. Gdy maszyna wirtualna zgromadziła środki, maszyna wirtualna może pęknąć powyżej linii bazowej maszyny Wirtualnej przy użyciu do 100% procesora CPU, gdy aplikacja wymaga wyższej wydajności procesora CPU.

- [Dav4 i Dasv4-series](dav4-dasv4-series.md) to nowe rozmiary wykorzystujące procesor AMD 2.35Ghz EPYC<sup>TM</sup> 7452 w konfiguracji wielowątkowej z pamięcią podręczną L3 o mocy do 256 MB, poświęcającą 8 MB tej pamięci podręcznej L3 na każde 8 rdzeni zwiększających opcje klientów do uruchamiania obciążeń ogólnego przeznaczenia. Seria Dav4 i Dasv4 mają taką samą konfigurację pamięci i dysków jak seria D & Dsv3.

- Seria [DCv2](dcv2-series.md) może pomóc chronić poufność i integralność danych i kodu podczas przetwarzania w chmurze publicznej. Maszyny te są wspierane przez najnowszą generację procesora Intel XEON E-2288G z technologią SGX. Dzięki technologii Intel Turbo Boost maszyny te mogą zwiększyć 5,0 GHz. Wystąpienia serii DCv2 umożliwiają klientom tworzenie bezpiecznych aplikacji opartych na enklawie w celu ochrony ich kodu i danych, gdy są używane.

- [Seria Dv2 i Dsv2](dv2-dsv2-series.md) Maszyny wirtualne, kontynuacja oryginalnej serii D, są wyposażone w wydajniejszy procesor i optymalną konfigurację procesora CPU-to-memory, dzięki czemu są odpowiednie dla większości obciążeń produkcyjnych. Seria Dv2 jest o około 35% szybsza niż seria D. Seria Dv2 działa na procesorze Intel® Xeon® 8171M 2.1GHz (Skylake), Intel® Xeon® E5-2673 v4 2.2.. 3 GHz (Broadwell) lub procesory Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) z technologią Intel Turbo Boost 2.0. Konfiguracje pamięci i dysków serii Dv2 są takie same jak w przypadku serii D.

- [Seria Dv3 i Dsv3](dv3-dsv3-series.md) Maszyny wirtualne działają na intel® Xeon® 8171M 2.1GHz (Skylake), Intel® Xeon® E5-2673 v4 2.3 G Hz (Broadwell) lub procesory Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) w konfiguracji hiperwątkowej, zapewniając lepszą ofertę wartości dla większości obciążeń ogólnego przeznaczenia. Pamięć została rozszerzona (z ~3,5 GiB/vCPU do 4 GiB/vCPU), podczas gdy limity dysków i sieci zostały dostosowane na podstawie rdzenia, aby wyrównać z przejściem do hiperwątkowości. Seria Dv3 nie ma już wysokiej pamięci rozmiarów maszyn wirtualnych z serii D/Dv2, które zostały przeniesione do zoptymalizowanych pod kątem pamięci [serii Ev3 i Esv3.](ev3-esv3-series.md)

Przykładowe przypadki użycia serii D obejmują aplikacje klasy korporacyjnej, relacyjne bazy danych, buforowanie w pamięci i analizy.

## <a name="other-sizes"></a>Inne rozmiary

- [Optymalizacja pod kątem obliczeń](sizes-compute.md)
- [Optymalizacja pod kątem pamięci](sizes-memory.md)
- [Optymalizacja pod kątem magazynu](sizes-storage.md)
- [Optymalizacja pod kątem procesora GPU](sizes-gpu.md)
- [Obliczenia o wysokiej wydajności](sizes-hpc.md)
- [Poprzednie generacje](sizes-previous-gen.md)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o tym, jak [jednostki obliczeniowe platformy Azure (ACU)](acu.md) mogą pomóc w porównywaniu wydajności obliczeniowej w jednostkach SKU platformy Azure.
