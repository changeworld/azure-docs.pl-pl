---
title: Limity systemowe storSimple serii 8000 | Dokumenty firmy Microsoft
description: W tym artykule opisano limity systemowe i zalecane rozmiary komponentów i połączeń serii StorSimple 8000.
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
ms.openlocfilehash: 70f2d9542082ddf7ecf1d1e7361b0ecdb14c5ef8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68963387"
---
# <a name="what-are-storsimple-8000-series-system-limits"></a>Jakie są limity systemowe storsimple serii 8000?

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Omówienie

StorSimple zapewnia skalowalną i elastyczną pamięć masową dla twojego centrum danych. Istnieją jednak pewne ograniczenia, o których należy pamiętać podczas planowania, wdrażania i obsługi rozwiązania StorSimple. W poniższej tabeli opisano te limity i przedstawiono pewne zalecenia, dzięki czemu można uzyskać jak najwięcej z rozwiązania StorSimple.

| Identyfikator limitu | Limit | Komentarze |
| --- | --- | --- |
| Maksymalna liczba poświadczeń konta magazynu |64 | |
| Maksymalna liczba pojemników objętościowych |64 | |
| Maksymalna liczba woluminów |255 | |
| Maksymalna liczba woluminów przypiętych lokalnie |32 | |
| Maksymalna liczba harmonogramów na szablon przepustowości |168 |Harmonogram na każdą godzinę, każdego dnia tygodnia (24*7). |
| Maksymalny rozmiar woluminu warstwowego na urządzeniach fizycznych |64 TB dla 8100 i 8600 |8100 i 8600 to urządzenia fizyczne. |
| Maksymalny rozmiar woluminu warstwowego na urządzeniach wirtualnych na platformie Azure |30 TB dla 8010 <br></br> 64 TB dla 8020 |8010 i 8020 to urządzenia wirtualne na platformie Azure, które używają odpowiednio magazynu standardowego i magazynu w wersji premium. |
| Maksymalny rozmiar woluminu przypiętego lokalnie na urządzeniach fizycznych |8,5 TB dla 8100 <br></br> 22,5 TB dla 8600 |8100 i 8600 to urządzenia fizyczne. |
| Maksymalna liczba połączeń iSCSI |512 | |
| Maksymalna liczba połączeń iSCSI od inicjatorów |512 | |
| Maksymalna liczba rekordów kontroli dostępu na urządzenie |64 | |
| Maksymalna liczba woluminów na zasady tworzenia kopii zapasowych |20 | |
| Maksymalna liczba kopii zapasowych zachowanych zgodnie z harmonogramem (w zasadach tworzenia kopii zapasowych) |64 | |
| Maksymalna liczba harmonogramów na zasady tworzenia kopii zapasowych |10 | |
| Maksymalna liczba migawek dowolnego typu, które mogą być przechowywane na woluminie |256 |Liczba ta obejmuje migawki lokalne i migawki w chmurze. |
| Maksymalna liczba migawek, które mogą być obecne w dowolnym urządzeniu |10 000 | |
| Maksymalna liczba woluminów, które mogą być przetwarzane równolegle do tworzenia kopii zapasowych, przywracania lub klonowania |16 |<ul><li>Jeśli istnieje więcej niż 16 woluminów, są one przetwarzane sekwencyjnie, jak miejsca przetwarzania stają się dostępne.</li><li>Nowe kopie zapasowe sklonowanego lub przywróconego woluminu warstwowego nie mogą wystąpić, dopóki operacja nie zostanie zakończona. Jednak dla woluminu lokalnego kopie zapasowe są dozwolone po woluminie jest w trybie online.</li></ul> |
| Przywracanie i klonowanie czasu odzyskiwania woluminów warstwowych |< 2 minuty |<ul><li>Wolumin jest udostępniany w ciągu 2 minut od operacji przywracania lub klonowania, niezależnie od rozmiaru woluminu.</li><li>Wydajność woluminu może początkowo być wolniejsze niż normalnie, jak większość danych i metadanych nadal znajduje się w chmurze. Wydajność może wzrosnąć w miarę przepływu danych z chmury do urządzenia StorSimple.</li><li>Całkowity czas pobierania metadanych zależy od przydzielonego rozmiaru woluminu. Metadane są automatycznie wprowadzane do urządzenia w tle z szybkością 5 minut na TB przydzielonych danych woluminu. Na tę szybkość może mieć wpływ przepustowość Internetu do chmury.</li><li>Operacja przywracania lub klonowania została zakończona, gdy wszystkie metadane są na urządzeniu.</li><li>Nie można wykonać operacji tworzenia kopii zapasowej, dopóki operacja przywracania lub klonowania nie zostanie w pełni ukończona. |
| Przywracanie czasu odzyskiwania woluminów przypiętych lokalnie |< 2 minuty |<ul><li>Wolumin jest udostępniany w ciągu 2 minut od operacji przywracania, niezależnie od rozmiaru woluminu.</li><li>Wydajność woluminu może początkowo być wolniejsze niż normalnie, jak większość danych i metadanych nadal znajduje się w chmurze. Wydajność może wzrosnąć w miarę przepływu danych z chmury do urządzenia StorSimple.</li><li>Całkowity czas pobierania metadanych zależy od przydzielonego rozmiaru woluminu. Metadane są automatycznie wprowadzane do urządzenia w tle z szybkością 5 minut na TB przydzielonych danych woluminu. Na tę szybkość może mieć wpływ przepustowość Internetu do chmury.</li><li>W przeciwieństwie do woluminów warstwowych dla woluminów przypiętych lokalnie dane woluminu są również pobierane lokalnie na urządzeniu. Operacja przywracania została zakończona po doprowadzeniu wszystkich danych woluminu do urządzenia.</li><li>Operacje przywracania mogą być długie. Całkowity czas do ukończenia przywracania zależy od rozmiaru aprowizowanego woluminu lokalnego, przepustowości Internetu i istniejących danych na urządzeniu. Operacje tworzenia kopii zapasowych na woluminie przypiętym lokalnie są dozwolone, gdy trwa operacja przywracania. |
| Szybkość przetwarzania migawek w chmurze |15 minut/TB |<ul><li>Minimalny czas, aby migawka w chmurze była gotowa do przesłania, na TB przydzielonych danych woluminu w kopii zapasowej. </li><li> Całkowity czas migawki w chmurze jest obliczany przez dodanie tego czasu do czasu przekazywania migawki, na który ma wpływ przepustowość Internetu do chmury. |
| Maksymalna przepustowość odczytu/zapisu klienta (obsługiwana z warstwy SSD)* |920/720 MB/s z jednym interfejsem sieciowym 10 GbE |Do 2x z MPIO i dwoma interfejsami sieciowymi. |
| Maksymalna przepustowość odczytu/zapisu klienta (obsługiwana z warstwy HDD)* |120/250 MB/s | |
| Maksymalna przepustowość odczytu/zapisu klienta (obsługiwana z warstwy chmury)* dla aktualizacji 3 i nowszych** |40/60 MB/s dla woluminów warstwowych<br><br>60/80 MB/s dla woluminów warstwowych z opcją archiwizacji wybraną podczas tworzenia woluminu |Przepływność odczytu zależy od klientów generujących i utrzymujących wystarczającą głębokość kolejki we/wy. <br><br>Osiągnięta szybkość zależy od szybkości używanego konta magazynu bazowego. |

&#42; Maksymalna przepływność na typ we/wy została zmierzona przy scenariuszach odczytu 100 procent i zapisu w 100 procentach. Rzeczywista przepustowość może być niższa i zależy od mieszania we/wy i warunków sieciowych.

&#42;&#42; numery wydajności przed aktualizacją 3 mogą być niższe.

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z [wymaganiami systemowymi StorSimple](storsimple-8000-system-requirements.md).

