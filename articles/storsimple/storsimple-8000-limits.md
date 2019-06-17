---
title: Ograniczenia systemu serii StorSimple 8000 | Dokumentacja firmy Microsoft
description: Opis ograniczeń systemowych i zalecane rozmiary dla składników serii StorSimple 8000 i połączeń.
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
ms.openlocfilehash: a0053f950b36351b06d08630cbf9977f53f2ed47
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64721692"
---
# <a name="what-are-storsimple-8000-series-system-limits"></a>Jakie są ograniczenia systemu serii StorSimple 8000?

## <a name="overview"></a>Omówienie

Usługa StorSimple zapewnia magazyn skalowalnej i elastycznej dla centrum danych. Istnieją jednak pewne ograniczenia, które należy mieć na uwadze, jak planowanie, wdrażanie i obsługiwanie rozwiązania StorSimple. Poniższa tabela opisuje te limity oraz przedstawiono powiązane zalecenia, dzięki czemu możesz można maksymalnie wykorzystać możliwości rozwiązania StorSimple.

| Identyfikator limitu | Limit | Komentarze |
| --- | --- | --- |
| Maksymalna liczba poświadczeń konta magazynu |64 | |
| Maksymalna liczba kontenerów woluminów |64 | |
| Maksymalna liczba woluminów |255 | |
| Maksymalna liczba woluminów przypiętych lokalnie |32 | |
| Maksymalna liczba harmonogramów dla szablonu przepustowości |168 |Harmonogram co godzinę każdego dnia, tygodnia (24 * 7). |
| Maksymalny rozmiar woluminu warstwowego na urządzeniach fizycznych |64 TB na 8100 i 8600 |8100 i 8600 są urządzeń fizycznych. |
| Maksymalny rozmiar woluminu warstwowego na urządzeń wirtualnych na platformie Azure |30 TB dla 8010 <br></br> 64 TB dla urządzenia 8020 |w wersji 8010 i 8020 są urządzeniami wirtualnymi na platformie Azure, korzystające z magazynu w warstwie standardowa i Premium Storage odpowiednio. |
| Maksymalny rozmiar woluminu przypiętego lokalnie na urządzeniach fizycznych |8,5 TB dla 8100 <br></br> 22,5 TB dla 8600 |8100 i 8600 są urządzeń fizycznych. |
| Maksymalna liczba połączeń iSCSI |512 | |
| Maksymalna liczba połączeń iSCSI z inicjatorów |512 | |
| Maksymalna liczba rekordów kontroli dostępu na urządzeniu |64 | |
| Maksymalna liczba woluminów, zgodnie z zasadami tworzenia kopii zapasowej |20 | |
| Maksymalna liczba kopii zapasowych przechowywane na harmonogram (w zasadach kopii zapasowych) |64 | |
| Maksymalna liczba harmonogramów zgodnie z zasadami tworzenia kopii zapasowej |10 | |
| Maksymalna liczba migawek dowolnego typu, które mogą być przechowywane na każdym woluminie |256 |Liczba ta obejmuje migawki lokalne i migawki w chmurze. |
| Maksymalna liczba migawek, które mogą być obecne w dowolnym urządzeniu |10 000 | |
| Maksymalna liczba woluminów, które mogą być przetwarzane równolegle do tworzenia kopii zapasowych, przywracania lub klonowania |16 |<ul><li>Jeśli istnieje więcej niż 16 woluminów, są przetwarzane sekwencyjnie miejsca przetwarzania stały się dostępne.</li><li>Nowe kopie zapasowe sklonowany lub przywrócony woluminu warstwowego nie może wystąpić dopiero po zakończeniu operacji. Jednak dla woluminu lokalnego kopie zapasowe są dozwolone po wolumin jest w trybie online.</li></ul> |
| Przywracanie i klonowanie czas odzyskiwanie woluminów warstwowych |< 2 minuty |<ul><li>Wolumin ma zostać udostępnione w ciągu 2 minut operacji przywracania lub klonowania, niezależnie od rozmiaru woluminu.</li><li>Wydajność woluminu może być początkowo wolniej niż zwykle, ponieważ większość danych i metadanych nadal znajduje się w chmurze. Wydajność może zwiększyć jako przepływów danych z chmury do urządzenia StorSimple.</li><li>Łączny czas wymagany do pobierania metadanych zależy od rozmiaru przydzielonego woluminu. Metadanych jest automatycznie przeniesione do urządzenia w tle stawki wynoszącej 5 minut na TB danych przydzielonego woluminu. Ten kurs mogą mieć wpływ na przepustowość internetowego do chmury.</li><li>Przywracanie lub Operacja klonowania została ukończona, gdy wszystkie metadane znajduje się na urządzeniu.</li><li>Nie można wykonać operacji tworzenia kopii zapasowej aż do przywracania lub w pełni zakończeniu operacji klonowania. |
| Godzina odzyskiwania dla woluminów przypiętych lokalnie przywracania |< 2 minuty |<ul><li>Wolumin ma zostać udostępnione w ciągu 2 minut operacji przywracania, niezależnie od rozmiaru woluminu.</li><li>Wydajność woluminu może być początkowo wolniej niż zwykle, ponieważ większość danych i metadanych nadal znajduje się w chmurze. Wydajność może zwiększyć jako przepływów danych z chmury do urządzenia StorSimple.</li><li>Łączny czas wymagany do pobierania metadanych zależy od rozmiaru przydzielonego woluminu. Metadanych jest automatycznie przeniesione do urządzenia w tle stawki wynoszącej 5 minut na TB danych przydzielonego woluminu. Ten kurs mogą mieć wpływ na przepustowość internetowego do chmury.</li><li>W przeciwieństwie do woluminów warstwowych, dla woluminów przypiętych lokalnie danych woluminu także jest pobierany lokalnie na urządzeniu. Operacja przywracania została zakończona, gdy wszystkie dane woluminu został sprowadzony z urządzeniem.</li><li>Operacje przywracania może być długi. Łączny czas wymagany do ukończenia przywracania zależy od rozmiaru aprowizowanego woluminu lokalnego, z wykorzystaniem przepustowości Internetu i istniejące dane na urządzeniu. Operacje tworzenia kopii zapasowych na wolumin przypięty lokalnie są dozwolone w trakcie operacji przywracania. |
| Szybkość przetwarzania dla migawki w chmurze |15 minut/TB |<ul><li>Minimalny czas do chmury migawki gotowe do przekazania na TB danych przydzielonego woluminu w kopii zapasowej. </li><li> Łączna liczba chmury migawki czas jest obliczany przez dodanie tego czasu na czas przekazywania migawki, która jest zależna od przepustowości Internetu do chmury. |
| Klient maksymalnej przepływności odczytu/zapisu (jeśli jest udostępniana z warstwy dysków SSD) * |920/720 MB/s przy użyciu pojedynczego interfejsu sieciowego 10 GbE |Do 2 x z funkcją wielościeżkowego wejścia/wyjścia i dwa interfejsy sieciowe. |
| Klient maksymalnej przepływności odczytu/zapisu (jeśli jest udostępniana z warstwy dysków HDD) * |120/250 MB/s | |
| Klient maksymalnej przepływności odczytu/zapisu (jeśli jest udostępniana z warstwy chmury) * Update 3 i nowszych ** |40/60 MB/s dla woluminy warstwowe<br><br>60/80 MB/s dla woluminy warstwowe archiwizacji opcja jest zaznaczona, podczas tworzenia woluminu |Przepływność odczytu zależy od klientów generowania i utrzymywania wystarczającej głębokości kolejki operacji We/Wy. <br><br>Szybkość osiągnąć zależy od szybkości bazowe konto magazynu używane. |

&#42;Maksymalna przepływność na typ operacji We/Wy został zmierzony za pomocą scenariuszy 100 procent zapisu i odczytu 100 procent. Rzeczywista przepływność może być niższa i zależy od operacji We/Wy mieszać i warunków sieciowych.

&#42;&#42;Wartości wydajności przed aktualizacją Update 3 może być niższa.

## <a name="next-steps"></a>Kolejne kroki
Przegląd [wymagania systemowe StorSimple](storsimple-8000-system-requirements.md).

