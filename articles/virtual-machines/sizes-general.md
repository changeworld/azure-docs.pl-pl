---
title: Rozmiary maszyn wirtualnych platformy Azure — ogólnego przeznaczenia | Microsoft Docs
description: Wyświetla różne rozmiary ogólnego przeznaczenia dostępne dla maszyn wirtualnych na platformie Azure. Wyświetla listę informacji o liczbie procesorów wirtualnych vCPU, dyskach danych i kartach sieciowych, a także przepływności magazynu i przepustowości sieci dla rozmiarów w tej serii.
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
ms.date: 02/03/2020
ms.author: jonbeck
ms.openlocfilehash: 674197bd4d1562d8492a2605d8929572d450af90
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/20/2020
ms.locfileid: "77493581"
---
# <a name="general-purpose-virtual-machine-sizes"></a>Rozmiary maszyn wirtualnych ogólnego przeznaczenia

Rozmiary maszyn wirtualnych ogólnego przeznaczenia zapewniają zrównoważony współczynnik procesora CPU do pamięci. Idealny do testowania i opracowywania, małych i średnich baz danych oraz do serwerów sieci Web o niskiej i średnim ruchu. Ten artykuł zawiera informacje o ofertach dla obliczeń ogólnego przeznaczenia.

- Maszyny wirtualne z [serii Av2](av2-series.md) można wdrażać na różnych typach sprzętu i procesorach. Maszyny wirtualne z serii a mają wydajność procesora CPU i konfiguracje pamięci najlepiej dopasowane do obciążeń poziomu wejścia, takich jak programowanie i testowanie. Rozmiar jest ograniczany w zależności od sprzętu, aby zapewnić spójną wydajność procesora dla uruchomionego wystąpienia niezależnie od sprzętu, na którym jest ono wdrożone. Aby określić sprzęt fizyczny, na którym jest wdrażany dany rozmiar, utwórz zapytanie o sprzęt wirtualny z poziomu maszyny wirtualnej. Przykładowe przypadki użycia obejmują serwery deweloperskie i testowe, serwery sieci Web o małym ruchu, małe i średnie bazy danych, sprawdzanie poprawności koncepcji i repozytoria kodu.

- [Seria B](sizes-b-series-burstable.md) Maszyny wirtualne doskonale nadają się do obciążeń, które nie wymagają pełnej wydajności procesora CPU, takich jak serwery sieci Web, małe bazy danych i środowiska deweloperskie i testowe. Te obciążenia zwykle mają wymagania dotyczące wydajności. Seria B zapewnia tym klientom możliwość kupowania rozmiaru maszyny wirtualnej przy użyciu ceny linii bazowej, która pozwala na wystąpienie maszyny wirtualnej do kompilowania kredytów, gdy maszyna wirtualna korzysta z mniejszej niż jej wydajność podstawowa. Po rozliczeniu na maszynę wirtualną, maszyna wirtualna może przekroczyć obciążenie maszyny wirtualnej za pomocą do 100% procesora, gdy aplikacja wymaga wyższych wydajności procesora CPU.

- [Serie Dav4 i Dasv4](dav4-dasv4-series.md) to nowe rozmiary wykorzystujące procesor AMD 2.35 GHz EPYC<sup>TM</sup> 7452 w ramach konfiguracji wielowątkowej z maksymalnie 256 MB pamięci podręcznej L3 o pojemności 8 GB pamięci podręcznej L3 do każdego 8 rdzeni, które zwiększają możliwości obsługi obciążeń ogólnego przeznaczenia. Serie Dav4 i Dasv4 mają takie same konfiguracje pamięci i dysków jak seria D & Dsv3.

- [Seria DC](dc-series.md) jest rodziną maszyn wirtualnych na platformie Azure, które mogą pomóc w ochronie poufności i integralności danych oraz kodu podczas przetwarzania w chmurze publicznej. Te maszyny są obsługiwane przez najnowszą generację procesorów Intel® Xeon® E-2176G z technologią SGX. Dzięki technologii zwiększania procesora Intel Turbo te maszyny mogą mieć wartość do 4,7 GHz. Wystąpienia serii DC umożliwiają klientom tworzenie bezpiecznych aplikacji opartych na enklawy w celu ochrony kodu i danych, gdy jest używany.

- [Dv2 i Dsv2 — seria](dv2-dsv2-series.md) Maszyny wirtualne, kolejne z serii D, oferują bardziej wydajny procesor i optymalną konfigurację procesora CPU i pamięci, które są odpowiednie dla większości obciążeń produkcyjnych. Seria Dv2 jest o około 35% szybsza niż seria D. Seria Dv2 jest uruchamiana na procesorach Intel® Xeon® 8171M 2.1 GHz (Skylake), Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) lub procesora Intel® Xeon® E5-2673 v3 2,4 GHz (Haswell) z technologią Intel Turbo — rozwiązanie 2,0. Konfiguracje pamięci i dysków serii Dv2 są takie same jak w przypadku serii D.

- [Dv3 i Dsv3 — seria](dv3-dsv3-series.md) Maszyny wirtualne działają na procesorach Intel® Xeon® 8171M 2.1 GHz (Skylake), Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) lub Intel® Xeon® E5-2673 v3 2,4 GHz (Haswell) w konfiguracji wielowątkowej, co zapewnia lepszą wartość propozycji dla większości obciążeń ogólnego przeznaczenia. Pamięć została rozszerzona (od ~ 3,5 GiB/vCPU do 4 GiB/vCPU), podczas gdy limity dysku i sieci zostały skorygowane w oparciu o rdzeń, aby dostosować je do przechodzenia do wielowątkowości. Seria Dv3 nie ma już rozmiaru maszyn wirtualnych z serii D/Dv2, które zostały przeniesione do zoptymalizowanej pod kątem pamięci [i serii Esv3](ev3-esv3-series.md).

Przykładowe przypadki użycia serii D obejmują aplikacje klasy korporacyjnej, relacyjne bazy danych, buforowanie w pamięci i analizy.

## <a name="other-sizes"></a>Inne rozmiary

- [Optymalizacja pod kątem obliczeń](sizes-compute.md)
- [Optymalizacja pod kątem pamięci](sizes-memory.md)
- [Optymalizacja pod kątem magazynu](sizes-storage.md)
- [Optymalizacja pod kątem procesora GPU](sizes-gpu.md)
- [Obliczenia o wysokiej wydajności](sizes-hpc.md)
- [Poprzednie generacje](sizes-previous-gen.md)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o tym, jak [usługa Azure COMPUTE units (ACU)](acu.md) może pomóc w porównaniu wydajności obliczeniowej w ramach jednostek SKU platformy Azure.