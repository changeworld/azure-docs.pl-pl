<!--author=alkohli last changed: 12/15/15-->

| Identyfikator limitu | Limit | Komentarze |
| --- | --- | --- |
| Maksymalna liczba poświadczeń konta magazynu |64 | |
| Maksymalna liczba kontenery woluminów |64 | |
| Maksymalna liczba woluminów |255 | |
| Maksymalna liczba harmonogramów na szablon przepustowości |168 |Harmonogram dla każdej godziny, każdego dnia tygodnia (24 * 7). |
| Maksymalny rozmiar woluminu warstwowego na fizycznych urządzeniach |64 TB 8100 i 8600 |8100 i 8600 są urządzenia fizyczne. |
| Maksymalny rozmiar woluminu warstwowego na urządzenia wirtualne na platformie Azure |30 TB dla 8010 <br></br> 64 TB dla urządzenia 8020 |urządzenia 8010 i 8020 są urządzeń wirtualnych na platformie Azure używających magazynu w warstwie standardowa i Premium Storage odpowiednio. |
| Maksymalny rozmiar woluminu przypiętego lokalnie na fizycznych urządzeniach |9 TB dla 8100 <br></br> 24 TB dla 8600 |8100 i 8600 są urządzenia fizyczne. |
| Maksymalna liczba połączeń iSCSI |512 | |
| Maksymalna liczba połączeń iSCSI z inicjatorów |512 | |
| Maksymalna liczba rekordów kontroli dostępu na urządzeniu |64 | |
| Maksymalna liczba woluminów na zasad tworzenia kopii zapasowej |24 | |
| Maksymalna liczba kopii zapasowych przechowywane dla poszczególnych zasad tworzenia kopii zapasowej |64 | |
| Maksymalna liczba harmonogramy dla poszczególnych zasad tworzenia kopii zapasowej |10 | |
| Maksymalna liczba migawek dowolnego typu, które mogą być przechowywane na każdym woluminie |256 |Dotyczy to również migawki lokalne i migawki w chmurze. |
| Maksymalna liczba migawek, które mogą być obecne w dowolnym urządzeniu |10 000 | |
| Maksymalna liczba woluminów, które mogą być przetwarzane równolegle do tworzenia kopii zapasowych, przywracania lub klonowania |16 |<ul><li>Jeśli istnieje więcej niż 16 woluminów, będą przetwarzane sekwencyjnie jako udostępnienie gniazda przetwarzania.</li><li>Nowe kopie zapasowe sklonowany lub przywrócony wolumin warstwowy może nastąpić dopiero po zakończeniu operacji. Jednak na lokalnym woluminie, kopie zapasowe są dozwolone po wolumin jest w trybie online.</li></ul> |
| Przywracanie i klonowanie czas Odzyskaj woluminy warstwowe |< 2 minuty |<ul><li>Wolumin ma zostać udostępnione w ciągu 2 minut operacji przywracania lub klonowania, niezależnie od rozmiaru woluminu.</li><li>Wydajność woluminu początkowo może przebiegać wolniej niż zwykle, ponieważ znajduje się w chmurze większość danych i metadanych. Wydajność może zwiększyć jako przepływów danych z chmury do urządzenia StorSimple.</li><li>Łączny czas pobierania metadanych zależy od rozmiaru woluminu przydzielone. Urządzenie w tle w wysokości 5 minut na TB danych przydzielonego woluminu automatycznie wejścia metadanych. Ta stawka może zależeć od przepustowości połączenia z Internetem w chmurze.</li><li>Przywracanie lub Operacja klonowania została ukończona, gdy wszystkie metadane jest na urządzeniu.</li><li>Nie można wykonać operacji tworzenia kopii zapasowej do przywrócenia lub pełni zakończeniu operacji klonowania. |
| Przywracanie odzyskać czas dla woluminów przypiętych lokalnie |< 2 minuty |<ul><li>Wolumin ma zostać udostępnione w ciągu 2 minut operacji przywracania, niezależnie od rozmiaru woluminu.</li><li>Wydajność woluminu początkowo może przebiegać wolniej niż zwykle, ponieważ znajduje się w chmurze większość danych i metadanych. Wydajność może zwiększyć jako przepływów danych z chmury do urządzenia StorSimple.</li><li>Łączny czas pobierania metadanych zależy od rozmiaru woluminu przydzielone. Urządzenie w tle w wysokości 5 minut na TB danych przydzielonego woluminu automatycznie wejścia metadanych. Ta stawka może zależeć od przepustowości połączenia z Internetem w chmurze.</li><li>W odróżnieniu od woluminów warstwowych, w przypadku woluminów przypiętych lokalnie danych woluminu jest również pobierane lokalnie na urządzeniu. Operacja przywracania została zakończona, po wprowadzeniu wszystkich danych woluminu na urządzeniu.</li><li>Operacje przywracania może trwać długo i łączny czas do zakończenia operacji przywracania zależy od rozmiaru alokowanego woluminu lokalnego, przepustowości połączenia z Internetem i istniejące dane na urządzeniu. Operacje tworzenia kopii zapasowej woluminu przypiętego lokalnie są dozwolone w trakcie operacji przywracania jest w toku. |
| Dostępność elastycznej przywracania |Ostatni trybu failover | |
| Przepływność odczytu/zapisu maksymalną klienta (jeśli są udostępniane przez warstwę dysków SSD) * |920/720 MB/s z jednym 10 GbE interfejsu sieciowego |Maksymalnie 2 x z wielościeżkowego wejścia/wyjścia i dwa interfejsy sieciowe. |
| Przepływność odczytu/zapisu maksymalną klienta (jeśli są udostępniane przez warstwę HDD) * |120/250 MB/s | |
| Przepływność odczytu/zapisu maksymalną klienta (jeśli jest obsługiwana w ramach warstwy chmury) * |11/41 MB/s |Przepływność odczytu zależy od klientów generowania i utrzymywania wystarczające głębokość kolejki operacji We/Wy. |

&#42;Maksymalna przepustowość na typ operacji We/Wy zmierzono z scenariusze 100 procent zapisu i odczytu 100 procent. Aktualna przepływność może być niższa i zależy od operacji We/Wy mieszać i warunki sieciowe.

