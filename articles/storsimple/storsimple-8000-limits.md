---
title: Ograniczenia systemu serii StorSimple 8000 | Dokumentacja firmy Microsoft
description: W tym artykule opisano limity systemu i rozmiary zalecane dla połączeń i składniki serii StorSimple 8000.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: c7392678-0924-46c6-9c59-1665cb9b6586
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 03/28/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: cc3c0ad193af7625c8c4c1c2e82b6bdc8be33310
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
ms.locfileid: "23874850"
---
# <a name="what-are-storsimple-8000-series-system-limits"></a>Jakie są ograniczenia systemu serii StorSimple 8000?

## <a name="overview"></a>Przegląd

StorSimple zapewnia skalowalny i elastyczna pamięć centrum danych. Istnieją jednak niektóre limity, które należy mieć na uwadze, jak planowania, wdrażania i obsługi rozwiązania StorSimple. Poniższa tabela opisuje te limity oraz przedstawiono powiązane zalecenia, dzięki czemu można uzyskać na maksymalne wykorzystanie możliwości rozwiązania StorSimple.

| Identyfikator limitu | Limit | Komentarze |
| --- | --- | --- |
| Maksymalna liczba poświadczeń konta magazynu |64 | |
| Maksymalna liczba kontenery woluminów |64 | |
| Maksymalna liczba woluminów |255 | |
| Maksymalna liczba woluminów przypiętych lokalnie |32 | |
| Maksymalna liczba harmonogramów na szablon przepustowości |168 |Harmonogram dla każdej godziny, każdego dnia tygodnia (24 * 7). |
| Maksymalny rozmiar woluminu warstwowego na fizycznych urządzeniach |64 TB 8100 i 8600 |8100 i 8600 są urządzenia fizyczne. |
| Maksymalny rozmiar woluminu warstwowego na urządzenia wirtualne na platformie Azure |30 TB dla 8010 <br></br> 64 TB dla urządzenia 8020 |urządzenia 8010 i 8020 są urządzeń wirtualnych na platformie Azure używających magazynu w warstwie standardowa i Premium Storage odpowiednio. |
| Maksymalny rozmiar woluminu przypiętego lokalnie na fizycznych urządzeniach |Szerokość 8,5 TB dla 8100 <br></br> 22,5 TB dla 8600 |8100 i 8600 są urządzenia fizyczne. |
| Maksymalna liczba połączeń iSCSI |512 | |
| Maksymalna liczba połączeń iSCSI z inicjatorów |512 | |
| Maksymalna liczba rekordów kontroli dostępu na urządzeniu |64 | |
| Maksymalna liczba woluminów na zasad tworzenia kopii zapasowej |20 | |
| Maksymalna liczba kopii zapasowych zachowane na harmonogram (w zasadach tworzenia kopii zapasowej) |64 | |
| Maksymalna liczba harmonogramy dla poszczególnych zasad tworzenia kopii zapasowej |10 | |
| Maksymalna liczba migawek dowolnego typu, które mogą być przechowywane na każdym woluminie |256 |Ta liczba uwzględnia również migawki lokalne i migawki w chmurze. |
| Maksymalna liczba migawek, które mogą być obecne w dowolnym urządzeniu |10 000 | |
| Maksymalna liczba woluminów, które mogą być przetwarzane równolegle do tworzenia kopii zapasowych, przywracania lub klonowania |16 |<ul><li>Jeśli istnieje więcej niż 16 woluminów, są przetwarzane sekwencyjnie jako udostępnienie gniazda przetwarzania.</li><li>Nowe kopie zapasowe sklonowany lub przywrócony wolumin warstwowy może nastąpić dopiero po zakończeniu operacji. Jednak na lokalnym woluminie, kopie zapasowe są dozwolone po wolumin jest w trybie online.</li></ul> |
| Przywracanie i klonowanie czas Odzyskaj woluminy warstwowe |< 2 minuty |<ul><li>Wolumin ma zostać udostępnione w ciągu 2 minut operacji przywracania lub klonowania, niezależnie od rozmiaru woluminu.</li><li>Wydajność woluminu początkowo może przebiegać wolniej niż zwykle, ponieważ znajduje się w chmurze większość danych i metadanych. Wydajność może zwiększyć jako przepływów danych z chmury do urządzenia StorSimple.</li><li>Łączny czas pobierania metadanych zależy od rozmiaru woluminu przydzielone. Urządzenie w tle w wysokości 5 minut na TB danych przydzielonego woluminu automatycznie wejścia metadanych. Ta stawka może zależeć od przepustowości połączenia z Internetem w chmurze.</li><li>Przywracanie lub Operacja klonowania została ukończona, gdy wszystkie metadane jest na urządzeniu.</li><li>Nie można wykonać operacji tworzenia kopii zapasowej do przywrócenia lub pełni zakończeniu operacji klonowania. |
| Przywracanie odzyskać czas dla woluminów przypiętych lokalnie |< 2 minuty |<ul><li>Wolumin ma zostać udostępnione w ciągu 2 minut operacji przywracania, niezależnie od rozmiaru woluminu.</li><li>Wydajność woluminu początkowo może przebiegać wolniej niż zwykle, ponieważ znajduje się w chmurze większość danych i metadanych. Wydajność może zwiększyć jako przepływów danych z chmury do urządzenia StorSimple.</li><li>Łączny czas pobierania metadanych zależy od rozmiaru woluminu przydzielone. Urządzenie w tle w wysokości 5 minut na TB danych przydzielonego woluminu automatycznie wejścia metadanych. Ta stawka może zależeć od przepustowości połączenia z Internetem w chmurze.</li><li>W odróżnieniu od woluminów warstwowych dla woluminów przypiętych lokalnie danych woluminu jest również pobierane lokalnie na urządzeniu. Operacja przywracania została zakończona, po wprowadzeniu wszystkich danych woluminu na urządzeniu.</li><li>Operacje przywracania może trwać długo. Łączny czas do zakończenia operacji przywracania zależy od rozmiaru alokowanego woluminu lokalnego, przepustowości połączenia z Internetem i istniejące dane na urządzeniu. Operacje tworzenia kopii zapasowej woluminu przypiętego lokalnie są dozwolone w trakcie operacji przywracania jest w toku. |
| Szybkość przetwarzania dla migawki w chmurze |15 minut/TB |<ul><li>Minimalny czas, aby uniemożliwić chmury migawek gotowe do przekazania na TB danych przydzielonego woluminu w kopii zapasowej. </li><li> Czas migawki całkowita chmury jest obliczany przez dodanie tego czasu do czasu przekazywania migawki, którego dotyczy przepustowości Internetu do chmury. |
| Przepływność odczytu/zapisu maksymalną klienta (jeśli są udostępniane przez warstwę dysków SSD) * |920/720 MB/s z jednego interfejsu sieciowego 10 GbE |Maksymalnie 2 x z wielościeżkowego wejścia/wyjścia i dwa interfejsy sieciowe. |
| Przepływność odczytu/zapisu maksymalną klienta (jeśli są udostępniane przez warstwę HDD) * |120/250 MB/s | |
| Przepływność odczytu/zapisu maksymalną klienta (jeśli jest obsługiwana w ramach warstwy chmury) * dla aktualizacji 3 i nowszych ** |40/60 MB/s dla woluminami warstwowymi<br><br>60/80 MB/s dla woluminami warstwowymi o archiwizacji opcji podczas tworzenia woluminu |Przepływność odczytu zależy od klientów generowania i utrzymywania wystarczające głębokość kolejki operacji We/Wy. <br><br>Szybkość osiągnięte zależy od prędkości użyć konta magazynu. |

&#42;Maksymalna przepustowość na typ operacji We/Wy zmierzono z scenariusze 100 procent zapisu i odczytu 100 procent. Aktualna przepływność może być niższa i zależy od operacji We/Wy mieszać i warunki sieciowe.

&#42;&#42;Numery wydajności przed Update 3 może być niższa.

## <a name="next-steps"></a>Kolejne kroki
Przegląd [wymagania systemowe StorSimple](storsimple-8000-system-requirements.md).

