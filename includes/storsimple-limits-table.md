---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 8a349916d0c0efc0f7895368a614897e3c6a15dc
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/26/2018
ms.locfileid: "50964581"
---
<!--author=alkohli last changed: 12/15/15-->

| Identyfikator limitu | Limit | Komentarze |
| --- | --- | --- |
| Maksymalna liczba poświadczeń konta magazynu |64 | |
| Maksymalna liczba kontenerów woluminów |64 | |
| Maksymalna liczba woluminów |255 | |
| Maksymalna liczba harmonogramów dla szablonu przepustowości |168 |Harmonogram co godzinę każdego dnia, tygodnia (24 * 7). |
| Maksymalny rozmiar woluminu warstwowego na urządzeniach fizycznych |64 TB na 8100 i 8600 |8100 i 8600 są urządzeń fizycznych. |
| Maksymalny rozmiar woluminu warstwowego na urządzeń wirtualnych na platformie Azure |30 TB dla 8010 <br></br> 64 TB dla urządzenia 8020 |w wersji 8010 i 8020 są urządzeniami wirtualnymi na platformie Azure, korzystające z magazynu w warstwie standardowa i Premium Storage odpowiednio. |
| Maksymalny rozmiar woluminu przypiętego lokalnie na urządzeniach fizycznych |9 TB dla 8100 <br></br> 24 TB dla 8600 |8100 i 8600 są urządzeń fizycznych. |
| Maksymalna liczba połączeń iSCSI |512 | |
| Maksymalna liczba połączeń iSCSI z inicjatorów |512 | |
| Maksymalna liczba rekordów kontroli dostępu na urządzeniu |64 | |
| Maksymalna liczba woluminów, zgodnie z zasadami tworzenia kopii zapasowej |24 | |
| Maksymalna liczba kopii zapasowych przechowywane na zasady tworzenia kopii zapasowej |64 | |
| Maksymalna liczba harmonogramów zgodnie z zasadami tworzenia kopii zapasowej |10 | |
| Maksymalna liczba migawek dowolnego typu, które mogą być przechowywane na każdym woluminie |256 |Obejmuje to migawki lokalne i migawki w chmurze. |
| Maksymalna liczba migawek, które mogą być obecne w dowolnym urządzeniu |10 000 | |
| Maksymalna liczba woluminów, które mogą być przetwarzane równolegle do tworzenia kopii zapasowych, przywracania lub klonowania |16 |<ul><li>W przypadku więcej niż 16 woluminów one będą przetwarzane sekwencyjnie miejsca przetwarzania stały się dostępne.</li><li>Nowe kopie zapasowe sklonowany lub przywrócony woluminu warstwowego nie może wystąpić dopiero po zakończeniu operacji. Jednak dla woluminu lokalnego kopie zapasowe są dozwolone po wolumin jest w trybie online.</li></ul> |
| Przywracanie i klonowanie czas odzyskiwanie woluminów warstwowych |< 2 minuty |<ul><li>Wolumin ma zostać udostępnione w ciągu 2 minut operacji przywracania lub klonowania, niezależnie od rozmiaru woluminu.</li><li>Wydajność woluminu może być początkowo wolniej niż zwykle, ponieważ większość danych i metadanych nadal znajduje się w chmurze. Wydajność może zwiększyć jako przepływów danych z chmury do urządzenia StorSimple.</li><li>Łączny czas wymagany do pobierania metadanych zależy od rozmiaru przydzielonego woluminu. Metadanych jest automatycznie przeniesione do urządzenia w tle stawki wynoszącej 5 minut na TB danych przydzielonego woluminu. Ten kurs mogą mieć wpływ na przepustowość internetowego do chmury.</li><li>Przywracanie lub Operacja klonowania została ukończona, gdy wszystkie metadane znajduje się na urządzeniu.</li><li>Nie można wykonać operacji tworzenia kopii zapasowej aż do przywracania lub w pełni zakończeniu operacji klonowania. |
| Godzina odzyskiwania dla woluminów przypiętych lokalnie przywracania |< 2 minuty |<ul><li>Wolumin ma zostać udostępnione w ciągu 2 minut operacji przywracania, niezależnie od rozmiaru woluminu.</li><li>Wydajność woluminu może być początkowo wolniej niż zwykle, ponieważ większość danych i metadanych nadal znajduje się w chmurze. Wydajność może zwiększyć jako przepływów danych z chmury do urządzenia StorSimple.</li><li>Łączny czas wymagany do pobierania metadanych zależy od rozmiaru przydzielonego woluminu. Metadanych jest automatycznie przeniesione do urządzenia w tle stawki wynoszącej 5 minut na TB danych przydzielonego woluminu. Ten kurs mogą mieć wpływ na przepustowość internetowego do chmury.</li><li>W przeciwieństwie do woluminów warstwowych, w przypadku woluminów przypiętych lokalnie danych woluminu także jest pobierany lokalnie na urządzeniu. Operacja przywracania została zakończona, gdy wszystkie dane woluminu został sprowadzony z urządzeniem.</li><li>Operacje przywracania może być długi i łączny czas wymagany do ukończenia przywracania będzie zależeć od rozmiaru aprowizowanego woluminu lokalnego, z wykorzystaniem przepustowości Internetu i istniejące dane na urządzeniu. Operacje tworzenia kopii zapasowych na wolumin przypięty lokalnie są dozwolone w trakcie operacji przywracania. |
| Dostępność alokowanych przywracania |Ostatnie trybu failover | |
| Klient maksymalnej przepływności odczytu/zapisu (jeśli jest udostępniana z warstwy dysków SSD) * |920/720 MB/s z jednym 10 GbE interfejs sieciowy |Do 2 x z funkcją wielościeżkowego wejścia/wyjścia i dwa interfejsy sieciowe. |
| Klient maksymalnej przepływności odczytu/zapisu (jeśli jest udostępniana z warstwy dysków HDD) * |120/250 MB/s | |
| Klient maksymalnej przepływności odczytu/zapisu (jeśli jest udostępniana z warstwy chmury) * |11/41 MB/s |Przepływność odczytu zależy od klientów generowania i utrzymywania wystarczającej głębokości kolejki operacji We/Wy. |

&#42;Maksymalna przepływność na typ operacji We/Wy został zmierzony za pomocą scenariuszy 100 procent zapisu i odczytu 100 procent. Rzeczywista przepływność może być niższa i zależy od operacji We/Wy mieszać i warunków sieciowych.

