---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 93f4f74d435cc14130668da102d1246c5fad5872
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67183629"
---
| Identyfikator limitu | Limit | Komentarze |
| --- | --- | --- |
| Maksymalna liczba poświadczeń konta magazynu |64 | |
| Maksymalna liczba pojemników objętościowych |64 | |
| Maksymalna liczba woluminów |255 | |
| Maksymalna liczba harmonogramów na szablon przepustowości |168 |Harmonogram na każdą godzinę, każdy dzień tygodnia. |
| Maksymalny rozmiar woluminu warstwowego na urządzeniach fizycznych |64 TB dla StorSimple 8100 i StorSimple 8600 |StorSimple 8100 i StorSimple 8600 są urządzeniami fizycznymi. |
| Maksymalny rozmiar woluminu warstwowego na urządzeniach wirtualnych na platformie Azure |30 TB dla StorSimple 8010 <br></br> 64 TB dla StorSimple 8020 |StorSimple 8010 i StorSimple 8020 to urządzenia wirtualne na platformie Azure, które używają magazynu standardowego i magazynu w wersji Premium. |
| Maksymalny rozmiar woluminu przypiętego lokalnie na urządzeniach fizycznych |9 TB dla StorSimple 8100 <br></br> 24 TB dla StorSimple 8600 |StorSimple 8100 i StorSimple 8600 są urządzeniami fizycznymi. |
| Maksymalna liczba połączeń iSCSI |512 | |
| Maksymalna liczba połączeń iSCSI od inicjatorów |512 | |
| Maksymalna liczba rekordów kontroli dostępu na urządzenie |64 | |
| Maksymalna liczba woluminów na zasady tworzenia kopii zapasowych |24 | |
| Maksymalna liczba kopii zapasowych zachowanych na zasady tworzenia kopii zapasowych |64 | |
| Maksymalna liczba harmonogramów na zasady tworzenia kopii zapasowych |10 | |
| Maksymalna liczba migawek dowolnego typu, które mogą być przechowywane na woluminie |256 |Ta kwota obejmuje migawki lokalne i migawki w chmurze. |
| Maksymalna liczba migawek, które mogą być obecne w dowolnym urządzeniu |10 000 | |
| Maksymalna liczba woluminów, które mogą być przetwarzane równolegle do tworzenia kopii zapasowych, przywracania lub klonowania |16 |<ul><li>Jeśli istnieje więcej niż 16 woluminów, są one przetwarzane sekwencyjnie, jak miejsca przetwarzania stają się dostępne.</li><li>Nowe kopie zapasowe sklonowanego lub przywróconego woluminu warstwowego nie mogą wystąpić, dopóki operacja nie zostanie zakończona. W przypadku woluminu lokalnego kopie zapasowe są dozwolone po zakończeniu pracy woluminu w trybie online.</li></ul> |
| Przywracanie i klonowanie czasu odzyskiwania woluminów warstwowych |<2 minuty |<ul><li>Wolumin jest udostępniany w ciągu 2 minut od operacji przywracania lub klonowania, niezależnie od rozmiaru woluminu.</li><li>Wydajność woluminu może początkowo być wolniejsze niż normalnie, jak większość danych i metadanych nadal znajduje się w chmurze. Wydajność może wzrosnąć w miarę przepływu danych z chmury do urządzenia StorSimple.</li><li>Całkowity czas pobierania metadanych zależy od przydzielonego rozmiaru woluminu. Metadane są automatycznie wprowadzane do urządzenia w tle z szybkością 5 minut na TB przydzielonych danych woluminu. Na tę szybkość może mieć wpływ przepustowość Internetu do chmury.</li><li>Operacja przywracania lub klonowania została zakończona, gdy wszystkie metadane są na urządzeniu.</li><li>Nie można wykonać operacji tworzenia kopii zapasowej, dopóki operacja przywracania lub klonowania nie zostanie w pełni ukończona. |
| Przywracanie czasu odzyskiwania woluminów przypiętych lokalnie |<2 minuty |<ul><li>Wolumin jest udostępniany w ciągu 2 minut od operacji przywracania, niezależnie od rozmiaru woluminu.</li><li>Wydajność woluminu może początkowo być wolniejsze niż normalnie, jak większość danych i metadanych nadal znajduje się w chmurze. Wydajność może wzrosnąć w miarę przepływu danych z chmury do urządzenia StorSimple.</li><li>Całkowity czas pobierania metadanych zależy od przydzielonego rozmiaru woluminu. Metadane są automatycznie wprowadzane do urządzenia w tle z szybkością 5 minut na TB przydzielonych danych woluminu. Na tę szybkość może mieć wpływ przepustowość Internetu do chmury.</li><li>W przeciwieństwie do woluminów warstwowych, jeśli istnieją woluminy przypięte lokalnie, dane woluminu są również pobierane lokalnie na urządzeniu. Operacja przywracania została zakończona po doprowadzeniu wszystkich danych woluminu do urządzenia.</li><li>Operacje przywracania mogą być długie, a całkowity czas do ukończenia przywracania zależy od rozmiaru aprowizowanego woluminu lokalnego, przepustowości Internetu i istniejących danych na urządzeniu. Operacje tworzenia kopii zapasowych na woluminie przypiętym lokalnie są dozwolone, gdy trwa operacja przywracania. |
| Dostępność funkcji thin-restore |Ostatnia przewijówka awaryjna | |
| Maksymalna przepustowość odczytu/zapisu klienta, gdy jest obsługiwana z warstwy SSD* |920/720 MB/s z pojedynczym 10-gigabitowym interfejsem sieciowym Ethernet |Do dwóch razy z MPIO i dwoma interfejsami sieciowymi. |
| Maksymalna przepustowość odczytu/zapisu klienta, gdy jest obsługiwana z warstwy hdd* |120/250 MB/s | |
| Maksymalna przepływność odczytu/zapisu klienta, gdy jest obsługiwana z warstwy chmury* |11/41 MB/s |Przepływność odczytu zależy od klientów generujących i utrzymujących wystarczającą głębokość kolejki we/wy. |

&#42;Maksymalna przepływność na typ we/wy została zmierzona przy scenariuszach odczytu 100 procent i zapisu w 100 procentach. Rzeczywista przepustowość może być niższa i zależy od mieszania we/wy i warunków sieciowych.

