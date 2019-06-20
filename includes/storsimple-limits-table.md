---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 93f4f74d435cc14130668da102d1246c5fad5872
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67183629"
---
| Identyfikator limitu | Limit | Komentarze |
| --- | --- | --- |
| Maksymalna liczba poświadczeń konta magazynu |64 | |
| Maksymalna liczba kontenerów woluminów |64 | |
| Maksymalna liczba woluminów |255 | |
| Maksymalna liczba harmonogramów dla szablonu przepustowości |168 |Harmonogram co godzinę każdego dnia tygodnia. |
| Maksymalny rozmiar woluminu warstwowego na urządzeniach fizycznych |64 TB na zainteresowanie rozwiązaniem StorSimple 8100 i StorSimple 8600 |StorSimple 8100 i StorSimple 8600 są urządzeń fizycznych. |
| Maksymalny rozmiar woluminu warstwowego na urządzeń wirtualnych na platformie Azure |30 TB for StorSimple 8010 <br></br> 64 TB for StorSimple 8020 |Urządzenie StorSimple 8010 i StorSimple 8020 są urządzeniami wirtualnymi na platformie Azure używających magazynu w warstwie standardowa i Premium storage, odpowiednio. |
| Maksymalny rozmiar woluminu przypiętego lokalnie na urządzeniach fizycznych |9 TB for StorSimple 8100 <br></br> 24 TB for StorSimple 8600 |StorSimple 8100 i StorSimple 8600 są urządzeń fizycznych. |
| Maksymalna liczba połączeń iSCSI |512 | |
| Maksymalna liczba połączeń iSCSI z inicjatorów |512 | |
| Maksymalna liczba rekordów kontroli dostępu na urządzeniu |64 | |
| Maksymalna liczba woluminów, zgodnie z zasadami tworzenia kopii zapasowej |24 | |
| Maksymalna liczba kopii zapasowych przechowywane na zasady tworzenia kopii zapasowej |64 | |
| Maksymalna liczba harmonogramów zgodnie z zasadami tworzenia kopii zapasowej |10 | |
| Maksymalna liczba migawek dowolnego typu, które mogą być przechowywane na każdym woluminie |256 |Ta wartość obejmuje migawki lokalne i migawki w chmurze. |
| Maksymalna liczba migawek, które mogą być obecne w dowolnym urządzeniu |10 000 | |
| Maksymalna liczba woluminów, które mogą być przetwarzane równolegle do tworzenia kopii zapasowych, przywracania lub klonowania |16 |<ul><li>W przypadku więcej niż 16 woluminów one są przetwarzane sekwencyjnie miejsca przetwarzania stały się dostępne.</li><li>Nowe kopie zapasowe sklonowany lub przywrócony woluminu warstwowego nie może wystąpić dopiero po zakończeniu operacji. Dla woluminu lokalnego kopie zapasowe są dozwolone po wolumin jest w trybie online.</li></ul> |
| Przywracanie i klonowanie czas odzyskiwanie woluminów warstwowych |< 2 minuty |<ul><li>Wolumin ma zostać udostępnione w ciągu 2 minut przywracania lub operacji klonowania, niezależnie od rozmiaru woluminu.</li><li>Wydajność woluminu może być początkowo wolniej niż zwykle, ponieważ większość danych i metadanych nadal znajduje się w chmurze. Wydajność może zwiększyć jako przepływów danych z chmury do urządzenia StorSimple.</li><li>Łączny czas wymagany do pobierania metadanych zależy od rozmiaru przydzielonego woluminu. Metadanych jest automatycznie przeniesione do urządzenia w tle stawki wynoszącej 5 minut na TB danych przydzielonego woluminu. Ten kurs może mieć wpływ przepustowością Internetu do chmury.</li><li>Przywracanie lub Operacja klonowania została ukończona, gdy wszystkie metadane znajduje się na urządzeniu.</li><li>Nie można wykonać operacji tworzenia kopii zapasowej aż do przywracania lub w pełni zakończeniu operacji klonowania. |
| Godzina odzyskiwania dla woluminów przypiętych lokalnie przywracania |< 2 minuty |<ul><li>Wolumin ma zostać udostępnione w ciągu 2 minut operacji przywracania, niezależnie od rozmiaru woluminu.</li><li>Wydajność woluminu może być początkowo wolniej niż zwykle, ponieważ większość danych i metadanych nadal znajduje się w chmurze. Wydajność może zwiększyć jako przepływów danych z chmury do urządzenia StorSimple.</li><li>Łączny czas wymagany do pobierania metadanych zależy od rozmiaru przydzielonego woluminu. Metadanych jest automatycznie przeniesione do urządzenia w tle stawki wynoszącej 5 minut na TB danych przydzielonego woluminu. Ten kurs może mieć wpływ przepustowością Internetu do chmury.</li><li>W przeciwieństwie do woluminów warstwowych Jeśli istnieją woluminy przypięte lokalnie danych woluminu także jest pobierany lokalnie na urządzeniu. Operacja przywracania została zakończona, gdy wszystkie dane woluminu został sprowadzony z urządzeniem.</li><li>Operacje przywracania może być długi i łączny czas wymagany do ukończenia przywracania będzie zależeć od rozmiaru aprowizowanego woluminu lokalnego, z wykorzystaniem przepustowości Internetu i istniejące dane na urządzeniu. Operacje tworzenia kopii zapasowych na wolumin przypięty lokalnie są dozwolone w trakcie operacji przywracania. |
| Dostępność alokowanych przywracania |Ostatnie trybu failover | |
| Maksymalnego przepływności odczytu/zapisu, gdy z warstwy dysków SSD * |920/720 MB na sekundę przy użyciu pojedynczego interfejsu sieciowego Ethernet 10 GB/s |Maksymalnie dwa razy z wielościeżkowego wejścia/wyjścia i dwa interfejsy sieciowe. |
| Klienta maksymalnej przepływności odczytu/zapisu, gdy udostępniane przez warstwę HDD * |120/250 MB/s | |
| Maksymalna klienta przepływności odczytu/zapisu, gdy z warstwy chmury * |11/41 MB/s |Przepływność odczytu zależy od klientów generowania i utrzymywania wystarczającej głębokości kolejki operacji We/Wy. |

&#42;Maksymalna przepływność na typ operacji We/Wy został zmierzony za pomocą scenariuszy 100 procent zapisu i odczytu 100 procent. Rzeczywista przepływność może być niższa i zależy od operacji We/Wy mieszać i warunków sieciowych.

