---
title: Rozszerzenia — usługa Azure Database for PostgreSQL — pojedynczy serwer
description: Dowiedz się więcej o dostępnych rozszerzeniach postgres w usłudze Azure Database for PostgreSQL — Single Server
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: a12738f5de783c8a34718b8d9cb4bbf54f230589
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77201275"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql---single-server"></a>PostgreSQL rozszerzenia w bazie danych platformy Azure dla PostgreSQL — pojedynczy serwer
PostgreSQL zapewnia możliwość rozszerzenia funkcji bazy danych za pomocą rozszerzeń. Rozszerzenia pakiet wielu powiązanych obiektów SQL razem w jednym pakiecie, które mogą być ładowane lub usuwane z bazy danych za pomocą jednego polecenia. Po załadowaniu do bazy danych rozszerzenia działają jak wbudowane funkcje.

## <a name="how-to-use-postgresql-extensions"></a>Jak korzystać z rozszerzeń PostgreSQL
Przed użyciem należy zainstalować rozszerzenia PostgreSQL w bazie danych. Aby zainstalować określone rozszerzenie, uruchom polecenie [CREATE EXTENSION](https://www.postgresql.org/docs/current/sql-createextension.html) z narzędzia psql, aby załadować spakowane obiekty do bazy danych.

Usługa Azure Database for PostgreSQL obsługuje podzbiór rozszerzeń kluczy, jak wymieniono poniżej. Informacje te są również `SELECT * FROM pg_available_extensions;`dostępne po uruchomieniu . Rozszerzenia poza wymienione rozszerzenia nie są obsługiwane. Nie można utworzyć własnego rozszerzenia w usłudze Azure Database for PostgreSQL.

## <a name="postgres-11-extensions"></a>Rozszerzenia postgres 11

Poniższe rozszerzenia są dostępne w usłudze Azure Database dla serwerów PostgreSQL, które mają postgres w wersji 11. 

> [!div class="mx-tableFixed"]
> | **Wewnętrzny**| **Wersja rozszerzenia** | **Opis** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.5.1           | Służy do analizować adres do elementów składowych. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.5.1           | Przykład zestawu danych Standardizatora adresów w USA|
> |[btree_gin](https://www.postgresql.org/docs/11/btree-gin.html)                    | 1.3             | obsługa indeksowania wspólnych typów danych w GIN|
> |[btree_gist](https://www.postgresql.org/docs/11/btree-gist.html)                   | 1.5             | obsługa indeksowania wspólnych typów danych w GiST|
> |[citext (tekst)](https://www.postgresql.org/docs/11/citext.html)                       | 1.5             | typ danych dla ciągów znaków bez uwzględniania wielkości liter|
> |[Moduł](https://www.postgresql.org/docs/11/cube.html)                         | 1.4             | typ danych dla modułów wielowymiarowych|
> |[dblink (dblink)](https://www.postgresql.org/docs/11/dblink.html)                       | 1.2             | łączenie się z innymi bazami danych PostgreSQL z poziomu bazy danych|
> |[dict_int](https://www.postgresql.org/docs/11/dict-int.html)                     | 1.0             | szablon słownika wyszukiwania tekstu dla liczby całkowite|
> |[ziemnych](https://www.postgresql.org/docs/11/earthdistance.html)                | 1.1             | obliczyć odległości wielkich kół na powierzchni Ziemi|
> |[rozmycie](https://www.postgresql.org/docs/11/fuzzystrmatch.html)                | 1.1             | określanie podobieństw i odległości między ciągami znaków|
> |[sklep z hstore](https://www.postgresql.org/docs/11/hstore.html)                       | 1.5             | typ danych do przechowywania zestawów par (klucz, wartość)|
> |[hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.2           | Hipotetyczne indeksy dla PostgreSQL|
> |[intarray (intarray)](https://www.postgresql.org/docs/11/intarray.html)                     | 1.2             | funkcje, operatory i obsługa indeksów dla tablic 1-W liczby całkowite|
> |[Isn](https://www.postgresql.org/docs/11/isn.html)                          | 1.2             | typy danych dla międzynarodowych standardów numerowania produktów|
> |[ltree (drzewo ltree)](https://www.postgresql.org/docs/11/ltree.html)                        | 1.1             | typ danych dla hierarchicznych struktur przypominających drzewa|
> |[orafce](https://github.com/orafce/orafce)                       | 3.7             | Funkcje i operatory, które emulują podzbiór funkcji i pakietów z komercyjnego RDBMS|
> |[pgaudit ( pgaudit )](https://www.pgaudit.org/)                     | 1.3.1             | zapewnia funkcję inspekcji|
> |[pgcrypto](https://www.postgresql.org/docs/11/pgcrypto.html)                     | 1.3             | funkcje kryptograficzne|
> |[pgrouting (pgrouting)](https://pgrouting.org/)                    | 2.6.2           | pgRouting Rozszerzenie|
> |[pgrowlocks](https://www.postgresql.org/docs/11/pgrowlocks.html)                   | 1.2             | pokaż informacje o blokowaniu na poziomie wiersza|
> |[pgstattuple](https://www.postgresql.org/docs/11/pgstattuple.html)                  | 1.5             | pokaż statystyki na poziomie krotki|
> |[pg_buffercache](https://www.postgresql.org/docs/11/pgbuffercache.html)               | 1.3             | sprawdzanie pamięci podręcznej buforu współdzielonego|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 4.0.0           | Rozszerzenie do zarządzania tabelami podzielonymi na partycje według czasu lub identyfikatora|
> |[pg_prewarm](https://www.postgresql.org/docs/11/pgprewarm.html)                   | 1.2             | dane relacji przedogrzamienia|
> |[pg_stat_statements](https://www.postgresql.org/docs/11/pgstatstatements.html)           | 1.6             | śledzenie statystyk wykonania wszystkich wykonanych instrukcji SQL|
> |[pg_trgm](https://www.postgresql.org/docs/11/pgtrgm.html)                      | 1.4             | pomiar podobieństwa tekstu i wyszukiwanie indeksu na podstawie trygramów|
> |[plpgsql ( plpgsql )](https://www.postgresql.org/docs/11/plpgsql.html)                      | 1.0             | Język proceduralny PL/pgSQL|
> |[plv8](https://plv8.github.io/)                         | 2.3.11          | PL/JavaScript (v8) zaufany język proceduralny|
> |[Postgis](https://www.postgis.net/)                      | 2.5.1           | Geometria, geografia i rastrowe typy i funkcje przestrzenne postGIS|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.5.1           | Funkcje PostGIS SFCGAL|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.5.1           | Geokoder tygrysa PostGIS i geokoder odwrotny|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.5.1           | Typy i funkcje przestrzenne topologii PostGIS|
> |[postgres_fdw](https://www.postgresql.org/docs/11/postgres-fdw.html)                 | 1.0             | obce otoki danych dla zdalnych serwerów PostgreSQL|
> |[tablefunc](https://www.postgresql.org/docs/11/tablefunc.html)                    | 1.0             | funkcje, które manipulują całymi tabelami, w tym|
> |[skala czasudb](https://docs.timescale.com/latest)                    | 1.3.2             | Umożliwia skalowalne wstawia i złożone zapytania dla danych szeregów czasowych|
> |[unaccent](https://www.postgresql.org/docs/11/unaccent.html)                     | 1.1             | słownik wyszukiwania tekstu, który usuwa akcenty|
> |[uuid-ossp](https://www.postgresql.org/docs/11/uuid-ossp.html)                    | 1.1             | generowanie uniwersalnych unikatowych identyfikatorów (UUID)|

## <a name="postgres-10-extensions"></a>Rozszerzenia Postgres 10 

Poniższe rozszerzenia są dostępne w usłudze Azure Database dla serwerów PostgreSQL, które mają postgres w wersji 10.

> [!div class="mx-tableFixed"]
> | **Wewnętrzny**| **Wersja rozszerzenia** | **Opis** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.5.1           | Służy do analizować adres do elementów składowych. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.5.1           | Przykład zestawu danych Standardizatora adresów w USA|
> |[btree_gin](https://www.postgresql.org/docs/10/btree-gin.html)                    | 1.3             | obsługa indeksowania wspólnych typów danych w GIN|
> |[btree_gist](https://www.postgresql.org/docs/10/btree-gist.html)                   | 1.5             | obsługa indeksowania wspólnych typów danych w GiST|
> |[chkpass (chkpass)](https://www.postgresql.org/docs/10/chkpass.html)                       | 1.0             | typ danych dla automatycznie zaszyfrowanych haseł|
> |[citext (tekst)](https://www.postgresql.org/docs/10/citext.html)                       | 1.4             | typ danych dla ciągów znaków bez uwzględniania wielkości liter|
> |[Moduł](https://www.postgresql.org/docs/10/cube.html)                         | 1.2             | typ danych dla modułów wielowymiarowych|
> |[dblink (dblink)](https://www.postgresql.org/docs/10/dblink.html)                       | 1.2             | łączenie się z innymi bazami danych PostgreSQL z poziomu bazy danych|
> |[dict_int](https://www.postgresql.org/docs/10/dict-int.html)                     | 1.0             | szablon słownika wyszukiwania tekstu dla liczby całkowite|
> |[ziemnych](https://www.postgresql.org/docs/10/earthdistance.html)                | 1.1             | obliczyć odległości wielkich kół na powierzchni Ziemi|
> |[rozmycie](https://www.postgresql.org/docs/10/fuzzystrmatch.html)                | 1.1             | określanie podobieństw i odległości między ciągami znaków|
> |[sklep z hstore](https://www.postgresql.org/docs/10/hstore.html)                       | 1.4             | typ danych do przechowywania zestawów par (klucz, wartość)|
> |[hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.1           | Hipotetyczne indeksy dla PostgreSQL|
> |[intarray (intarray)](https://www.postgresql.org/docs/10/intarray.html)                     | 1.2             | funkcje, operatory i obsługa indeksów dla tablic 1-W liczby całkowite|
> |[Isn](https://www.postgresql.org/docs/10/isn.html)                          | 1.1             | typy danych dla międzynarodowych standardów numerowania produktów|
> |[ltree (drzewo ltree)](https://www.postgresql.org/docs/10/ltree.html)                        | 1.1             | typ danych dla hierarchicznych struktur przypominających drzewa|
> |[orafce](https://github.com/orafce/orafce)                       | 3.7             | Funkcje i operatory, które emulują podzbiór funkcji i pakietów z komercyjnego RDBMS|
> |[pgaudit ( pgaudit )](https://www.pgaudit.org/)                     | 1.2             | zapewnia funkcję inspekcji|
> |[pgcrypto](https://www.postgresql.org/docs/10/pgcrypto.html)                     | 1.3             | funkcje kryptograficzne|
> |[pgrouting (pgrouting)](https://pgrouting.org/)                    | 2.5.2           | pgRouting Rozszerzenie|
> |[pgrowlocks](https://www.postgresql.org/docs/10/pgrowlocks.html)                   | 1.2             | pokaż informacje o blokowaniu na poziomie wiersza|
> |[pgstattuple](https://www.postgresql.org/docs/10/pgstattuple.html)                  | 1.5             | pokaż statystyki na poziomie krotki|
> |[pg_buffercache](https://www.postgresql.org/docs/10/pgbuffercache.html)               | 1.3             | sprawdzanie pamięci podręcznej buforu współdzielonego|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 2.6.3           | Rozszerzenie do zarządzania tabelami podzielonymi na partycje według czasu lub identyfikatora|
> |[pg_prewarm](https://www.postgresql.org/docs/10/pgprewarm.html)                   | 1.1             | dane relacji przedogrzamienia|
> |[pg_stat_statements](https://www.postgresql.org/docs/10/pgstatstatements.html)           | 1.6             | śledzenie statystyk wykonania wszystkich wykonanych instrukcji SQL|
> |[pg_trgm](https://www.postgresql.org/docs/10/pgtrgm.html)                      | 1.3             | pomiar podobieństwa tekstu i wyszukiwanie indeksu na podstawie trygramów|
> |[plpgsql ( plpgsql )](https://www.postgresql.org/docs/10/plpgsql.html)                      | 1.0             | Język proceduralny PL/pgSQL|
> |[plv8](https://plv8.github.io/)                         | 2.1.0          | PL/JavaScript (v8) zaufany język proceduralny|
> |[Postgis](https://www.postgis.net/)                      | 2.4.3           | Geometria, geografia i rastrowe typy i funkcje przestrzenne postGIS|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.4.3           | Funkcje PostGIS SFCGAL|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.4.3           | Geokoder tygrysa PostGIS i geokoder odwrotny|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.4.3           | Typy i funkcje przestrzenne topologii PostGIS|
> |[postgres_fdw](https://www.postgresql.org/docs/10/postgres-fdw.html)                 | 1.0             | obce otoki danych dla zdalnych serwerów PostgreSQL|
> |[tablefunc](https://www.postgresql.org/docs/10/tablefunc.html)                    | 1.0             | funkcje, które manipulują całymi tabelami, w tym|
> |[skala czasudb](https://docs.timescale.com/latest)                    | 1.1.1             | Umożliwia skalowalne wstawia i złożone zapytania dla danych szeregów czasowych|
> |[unaccent](https://www.postgresql.org/docs/10/unaccent.html)                     | 1.1             | słownik wyszukiwania tekstu, który usuwa akcenty|
> |[uuid-ossp](https://www.postgresql.org/docs/10/uuid-ossp.html)                    | 1.1             | generowanie uniwersalnych unikatowych identyfikatorów (UUID)|

## <a name="postgres-96-extensions"></a>Rozszerzenia postgres 9.6 

Poniższe rozszerzenia są dostępne w usłudze Azure Database dla serwerów PostgreSQL, które mają postgres w wersji 9.6.

> [!div class="mx-tableFixed"]
> | **Wewnętrzny**| **Wersja rozszerzenia** | **Opis** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.3.2           | Służy do analizować adres do elementów składowych. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.3.2           | Przykład zestawu danych Standardizatora adresów w USA|
> |[btree_gin](https://www.postgresql.org/docs/9.6/btree-gin.html)                    | 1.0             | obsługa indeksowania wspólnych typów danych w GIN|
> |[btree_gist](https://www.postgresql.org/docs/9.6/btree-gist.html)                   | 1.2             | obsługa indeksowania wspólnych typów danych w GiST|
> |[chkpass (chkpass)](https://www.postgresql.org/docs/9.6/chkpass.html)                       | 1.0             | typ danych dla automatycznie zaszyfrowanych haseł|
> |[citext (tekst)](https://www.postgresql.org/docs/9.6/citext.html)                       | 1.3             | typ danych dla ciągów znaków bez uwzględniania wielkości liter|
> |[Moduł](https://www.postgresql.org/docs/9.6/cube.html)                         | 1.2             | typ danych dla modułów wielowymiarowych|
> |[dblink (dblink)](https://www.postgresql.org/docs/9.6/dblink.html)                       | 1.2             | łączenie się z innymi bazami danych PostgreSQL z poziomu bazy danych|
> |[dict_int](https://www.postgresql.org/docs/9.6/dict-int.html)                     | 1.0             | szablon słownika wyszukiwania tekstu dla liczby całkowite|
> |[ziemnych](https://www.postgresql.org/docs/9.6/earthdistance.html)                | 1.1             | obliczyć odległości wielkich kół na powierzchni Ziemi|
> |[rozmycie](https://www.postgresql.org/docs/9.6/fuzzystrmatch.html)                | 1.1             | określanie podobieństw i odległości między ciągami znaków|
> |[sklep z hstore](https://www.postgresql.org/docs/9.6/hstore.html)                       | 1.4             | typ danych do przechowywania zestawów par (klucz, wartość)|
> |[hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.1           | Hipotetyczne indeksy dla PostgreSQL|
> |[intarray (intarray)](https://www.postgresql.org/docs/9.6/intarray.html)                     | 1.2             | funkcje, operatory i obsługa indeksów dla tablic 1-W liczby całkowite|
> |[Isn](https://www.postgresql.org/docs/9.6/isn.html)                          | 1.1             | typy danych dla międzynarodowych standardów numerowania produktów|
> |[ltree (drzewo ltree)](https://www.postgresql.org/docs/9.6/ltree.html)                        | 1.1             | typ danych dla hierarchicznych struktur przypominających drzewa|
> |[orafce](https://github.com/orafce/orafce)                       | 3.7             | Funkcje i operatory, które emulują podzbiór funkcji i pakietów z komercyjnego RDBMS|
> |[pgaudit ( pgaudit )](https://www.pgaudit.org/)                     | 1.1.2             | zapewnia funkcję inspekcji|
> |[pgcrypto](https://www.postgresql.org/docs/9.6/pgcrypto.html)                     | 1.3             | funkcje kryptograficzne|
> |[pgrouting (pgrouting)](https://pgrouting.org/)                    | 2.3.2           | pgRouting Rozszerzenie|
> |[pgrowlocks](https://www.postgresql.org/docs/9.6/pgrowlocks.html)                   | 1.2             | pokaż informacje o blokowaniu na poziomie wiersza|
> |[pgstattuple](https://www.postgresql.org/docs/9.6/pgstattuple.html)                  | 1.4             | pokaż statystyki na poziomie krotki|
> |[pg_buffercache](https://www.postgresql.org/docs/9.6/pgbuffercache.html)               | 1.2             | sprawdzanie pamięci podręcznej buforu współdzielonego|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 2.6.3           | Rozszerzenie do zarządzania tabelami podzielonymi na partycje według czasu lub identyfikatora|
> |[pg_prewarm](https://www.postgresql.org/docs/9.6/pgprewarm.html)                   | 1.1             | dane relacji przedogrzamienia|
> |[pg_stat_statements](https://www.postgresql.org/docs/9.6/pgstatstatements.html)           | 1.4             | śledzenie statystyk wykonania wszystkich wykonanych instrukcji SQL|
> |[pg_trgm](https://www.postgresql.org/docs/9.6/pgtrgm.html)                      | 1.3             | pomiar podobieństwa tekstu i wyszukiwanie indeksu na podstawie trygramów|
> |[plpgsql ( plpgsql )](https://www.postgresql.org/docs/9.6/plpgsql.html)                      | 1.0             | Język proceduralny PL/pgSQL|
> |[plv8](https://plv8.github.io/)                         | 2.1.0          | PL/JavaScript (v8) zaufany język proceduralny|
> |[Postgis](https://www.postgis.net/)                      | 2.3.2           | Geometria, geografia i rastrowe typy i funkcje przestrzenne postGIS|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.3.2           | Funkcje PostGIS SFCGAL|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.3.2           | Geokoder tygrysa PostGIS i geokoder odwrotny|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.3.2           | Typy i funkcje przestrzenne topologii PostGIS|
> |[postgres_fdw](https://www.postgresql.org/docs/9.6/postgres-fdw.html)                 | 1.0             | obce otoki danych dla zdalnych serwerów PostgreSQL|
> |[tablefunc](https://www.postgresql.org/docs/9.6/tablefunc.html)                    | 1.0             | funkcje, które manipulują całymi tabelami, w tym|
> |[skala czasudb](https://docs.timescale.com/latest)                    | 1.1.1             | Umożliwia skalowalne wstawia i złożone zapytania dla danych szeregów czasowych|
> |[unaccent](https://www.postgresql.org/docs/9.6/unaccent.html)                     | 1.1             | słownik wyszukiwania tekstu, który usuwa akcenty|
> |[uuid-ossp](https://www.postgresql.org/docs/9.6/uuid-ossp.html)                    | 1.1             | generowanie uniwersalnych unikatowych identyfikatorów (UUID)|

## <a name="postgres-95-extensions"></a>Rozszerzenia postgres 9.5 

Poniższe rozszerzenia są dostępne w usłudze Azure Database dla serwerów PostgreSQL, które mają postgres w wersji 9.5.

> [!div class="mx-tableFixed"]
> | **Wewnętrzny**| **Wersja rozszerzenia** | **Opis** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.3.0           | Służy do analizować adres do elementów składowych. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.3.0           | Przykład zestawu danych Standardizatora adresów w USA|
> |[btree_gin](https://www.postgresql.org/docs/9.5/btree-gin.html)                    | 1.0             | obsługa indeksowania wspólnych typów danych w GIN|
> |[btree_gist](https://www.postgresql.org/docs/9.5/btree-gist.html)                   | 1.1             | obsługa indeksowania wspólnych typów danych w GiST|
> |[chkpass (chkpass)](https://www.postgresql.org/docs/9.5/chkpass.html)                       | 1.0             | typ danych dla automatycznie zaszyfrowanych haseł|
> |[citext (tekst)](https://www.postgresql.org/docs/9.5/citext.html)                       | 1.1             | typ danych dla ciągów znaków bez uwzględniania wielkości liter|
> |[Moduł](https://www.postgresql.org/docs/9.5/cube.html)                         | 1.0             | typ danych dla modułów wielowymiarowych|
> |[dblink (dblink)](https://www.postgresql.org/docs/9.5/dblink.html)                       | 1.1             | łączenie się z innymi bazami danych PostgreSQL z poziomu bazy danych|
> |[dict_int](https://www.postgresql.org/docs/9.5/dict-int.html)                     | 1.0             | szablon słownika wyszukiwania tekstu dla liczby całkowite|
> |[ziemnych](https://www.postgresql.org/docs/9.5/earthdistance.html)                | 1.0             | obliczyć odległości wielkich kół na powierzchni Ziemi|
> |[rozmycie](https://www.postgresql.org/docs/9.5/fuzzystrmatch.html)                | 1.0             | określanie podobieństw i odległości między ciągami znaków|
> |[sklep z hstore](https://www.postgresql.org/docs/9.5/hstore.html)                       | 1.3             | typ danych do przechowywania zestawów par (klucz, wartość)|
> |[hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.1           | Hipotetyczne indeksy dla PostgreSQL|
> |[intarray (intarray)](https://www.postgresql.org/docs/9.5/intarray.html)                     | 1.0             | funkcje, operatory i obsługa indeksów dla tablic 1-W liczby całkowite|
> |[Isn](https://www.postgresql.org/docs/9.5/isn.html)                          | 1.0             | typy danych dla międzynarodowych standardów numerowania produktów|
> |[ltree (drzewo ltree)](https://www.postgresql.org/docs/9.5/ltree.html)                        | 1.0             | typ danych dla hierarchicznych struktur przypominających drzewa|
> |[orafce](https://github.com/orafce/orafce)                       | 3.7             | Funkcje i operatory, które emulują podzbiór funkcji i pakietów z komercyjnego RDBMS|
> |[pgaudit ( pgaudit )](https://www.pgaudit.org/)                     | 1.0.7             | zapewnia funkcję inspekcji|
> |[pgcrypto](https://www.postgresql.org/docs/9.5/pgcrypto.html)                     | 1.2             | funkcje kryptograficzne|
> |[pgrouting (pgrouting)](https://pgrouting.org/)                    | 2.3.0           | pgRouting Rozszerzenie|
> |[pgrowlocks](https://www.postgresql.org/docs/9.5/pgrowlocks.html)                   | 1.1             | pokaż informacje o blokowaniu na poziomie wiersza|
> |[pgstattuple](https://www.postgresql.org/docs/9.5/pgstattuple.html)                  | 1.3             | pokaż statystyki na poziomie krotki|
> |[pg_buffercache](https://www.postgresql.org/docs/9.5/pgbuffercache.html)               | 1.1             | sprawdzanie pamięci podręcznej buforu współdzielonego|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 2.6.3           | Rozszerzenie do zarządzania tabelami podzielonymi na partycje według czasu lub identyfikatora|
> |[pg_prewarm](https://www.postgresql.org/docs/9.5/pgprewarm.html)                   | 1.0             | dane relacji przedogrzamienia|
> |[pg_stat_statements](https://www.postgresql.org/docs/9.5/pgstatstatements.html)           | 1.3             | śledzenie statystyk wykonania wszystkich wykonanych instrukcji SQL|
> |[pg_trgm](https://www.postgresql.org/docs/9.5/pgtrgm.html)                      | 1.1             | pomiar podobieństwa tekstu i wyszukiwanie indeksu na podstawie trygramów|
> |[plpgsql ( plpgsql )](https://www.postgresql.org/docs/9.5/plpgsql.html)                      | 1.0             | Język proceduralny PL/pgSQL|
> |[Postgis](https://www.postgis.net/)                      | 2.3.0           | Geometria, geografia i rastrowe typy i funkcje przestrzenne postGIS|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.3.0           | Funkcje PostGIS SFCGAL|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.3.0           | Geokoder tygrysa PostGIS i geokoder odwrotny|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.3.0           | Typy i funkcje przestrzenne topologii PostGIS|
> |[postgres_fdw](https://www.postgresql.org/docs/9.5/postgres-fdw.html)                 | 1.0             | obce otoki danych dla zdalnych serwerów PostgreSQL|
> |[tablefunc](https://www.postgresql.org/docs/9.5/tablefunc.html)                    | 1.0             | funkcje, które manipulują całymi tabelami, w tym|
> |[unaccent](https://www.postgresql.org/docs/9.5/unaccent.html)                     | 1.0             | słownik wyszukiwania tekstu, który usuwa akcenty|
> |[uuid-ossp](https://www.postgresql.org/docs/9.5/uuid-ossp.html)                    | 1.0             | generowanie uniwersalnych unikatowych identyfikatorów (UUID)|


## <a name="pg_stat_statements"></a>pg_stat_statements
[Rozszerzenie pg_stat_statements](https://www.postgresql.org/docs/current/pgstatstatements.html) jest wstępnie ładowane na każdym serwerze usługi Azure Database for PostgreSQL, aby zapewnić możliwość śledzenia statystyk wykonywania instrukcji SQL.
Ustawienie `pg_stat_statements.track`, które kontroluje, jakie instrukcje są liczone przez rozszerzenie, domyślnie `top`, co oznacza, że wszystkie instrukcje wydane bezpośrednio przez klientów są śledzone. Dwa inne poziomy `none` śledzenia `all`są i . To ustawienie można skonfigurować jako parametr serwera za pośrednictwem [witryny Azure portal](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-portal) lub [interfejsu wiersza polecenia platformy Azure.](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-cli)

Istnieje kompromis między informacjami o wykonaniu kwerendy, pg_stat_statements zapewnia i wpływ na wydajność serwera, jak rejestruje każdą instrukcję SQL. Jeśli nie używasz aktywnie rozszerzenia pg_stat_statements, zalecamy ustawienie `pg_stat_statements.track` . `none` Należy zauważyć, że niektóre usługi monitorowania innych firm mogą polegać na pg_stat_statements do dostarczania szczegółowych informacji o wydajności zapytań, więc potwierdź, czy tak jest w przypadku Ciebie, czy nie.

## <a name="dblink-and-postgres_fdw"></a>dblink i postgres_fdw
[dblink](https://www.postgresql.org/docs/current/contrib-dblink-function.html) i [postgres_fdw](https://www.postgresql.org/docs/current/postgres-fdw.html) umożliwiają łączenie się z jednego serwera PostgreSQL z innym lub z inną bazą danych na tym samym serwerze. Serwer odbiorczy musi zezwolić na połączenia z serwera wysyłającego za pośrednictwem zapory. Korzystając z tych rozszerzeń do łączenia się między usługą Azure Database dla serwerów PostgreSQL, można to zrobić, ustawiając "Zezwalaj na dostęp do usług platformy Azure" na ON. Jest to również potrzebne, jeśli chcesz użyć rozszerzeń do pętli z powrotem do tego samego serwera. Ustawienie "Zezwalaj na dostęp do usług platformy Azure" można znaleźć na stronie portalu Azure dla serwera Postgres w obszarze Zabezpieczenia połączeń. Włączenie opcji "Zezwalaj na dostęp do usług platformy Azure" powoduje wyświetlenie wszystkich usług IP platformy Azure na liście dozwolonych usług.

Obecnie połączenia wychodzące z usługi Azure Database for PostgreSQL nie są obsługiwane, z wyjątkiem połączeń z innymi serwerami usługi Azure Database dla serwerów PostgreSQL.

## <a name="uuid"></a>uuid
Jeśli planujesz używać `uuid_generate_v4()` z [rozszerzenia uuid-ossp](https://www.postgresql.org/docs/current/uuid-ossp.html), `gen_random_uuid()` rozważ porównanie z [rozszerzeniem pgcrypto](https://www.postgresql.org/docs/current/pgcrypto.html) dla korzyści wydajności.

## <a name="pgaudit"></a>pgAudit
[Rozszerzenie pgAudit](https://github.com/pgaudit/pgaudit/blob/master/README.md) zapewnia rejestrowanie inspekcji sesji i obiektów. Aby dowiedzieć się, jak używać tego rozszerzenia w usłudze Azure Database for PostgreSQL, zapoznaj się z [artykułem pojęcia o inspekcji.](concepts-audit.md) 

## <a name="pg_prewarm"></a>pg_prewarm
Rozszerzenie pg_prewarm ładuje dane relacyjne do pamięci podręcznej. Wstępne ogrzanie pamięci podręcznej oznacza, że zapytania mają lepsze czasy odpowiedzi przy pierwszym uruchomieniu po ponownym uruchomieniu. W postgres 10 i poniżej, przedogrzewanie odbywa się ręcznie za pomocą [funkcji przedogrzewarem](https://www.postgresql.org/docs/10/pgprewarm.html).

W postgres 11 i powyżej, można skonfigurować przedogrzewanie się odbywa się [automatycznie](https://www.postgresql.org/docs/current/pgprewarm.html). Należy dołączyć pg_prewarm do listy `shared_preload_libraries` parametrów i ponownie uruchomić serwer, aby zastosować zmianę. Parametry można ustawić za pomocą [szablonu Azure portal,](howto-configure-server-parameters-using-portal.md) [CLI,](howto-configure-server-parameters-using-cli.md)REST API lub ARM. 

## <a name="timescaledb"></a>Baza danych o skali czasu
TimescaleDB to baza danych szeregów czasowych, która jest pakowana jako rozszerzenie dla PostgreSQL. TimescaleDB zapewnia zorientowane na czas funkcje analityczne, optymalizacje i skaluje Postgres dla obciążeń szeregów czasowych.

[Dowiedz się więcej o TimescaleDB](https://docs.timescale.com/latest), zarejestrowanym znaku towarowym [Timescale, Inc.](https://www.timescale.com/). Usługa Azure Database for PostgreSQL udostępnia wersję typu open source w skali czasu. Aby dowiedzieć się, które funkcje skali czasu są dostępne w tej wersji, zobacz [porównanie produktów skala czasu](https://www.timescale.com/products/).

### <a name="installing-timescaledb"></a>Instalowanie usługi TimescaleDB
Aby zainstalować timescaledb, należy dołączyć go do bibliotek wstępnego ładowania współużytkowane na serwerze. Zmiana parametru `shared_preload_libraries` Postgres wymaga **ponownego uruchomienia serwera.** Parametry można zmieniać za pomocą [witryny Azure portal](howto-configure-server-parameters-using-portal.md) lub [interfejsu wiersza polecenia platformy Azure.](howto-configure-server-parameters-using-cli.md)

Korzystanie z [portalu Azure:](https://portal.azure.com/)

1. Wybierz serwer usługi Azure Database for PostgreSQL.

2. Na pasku bocznym wybierz pozycję **Parametry serwera**.

3. Wyszukaj `shared_preload_libraries` parametr.

4. Wybierz **opcję TimescaleDB**.

5. Wybierz **pozycję Zapisz,** aby zachować zmiany. Otrzymasz powiadomienie po zapisaniu zmiany. 

6. Po powiadomieniu **uruchom ponownie** serwer, aby zastosować te zmiany. Aby dowiedzieć się, jak ponownie uruchomić serwer, zobacz [Ponowne uruchamianie usługi Azure Database dla serwera PostgreSQL](howto-restart-server-portal.md).


Teraz można włączyć TimescaleDB w bazie danych Postgres. Połącz się z bazą danych i wydaj następujące polecenie:
```sql
CREATE EXTENSION IF NOT EXISTS timescaledb CASCADE;
```
> [!TIP]
> Jeśli zostanie wyświetlony błąd, upewnij [się,](howto-restart-server-portal.md) że serwer został ponownie uruchomiony po zapisaniu shared_preload_libraries. 

Teraz można utworzyć hypertable TimescaleDB [od podstaw](https://docs.timescale.com/getting-started/creating-hypertables) lub migracji [istniejących danych szeregów czasowych w PostgreSQL](https://docs.timescale.com/getting-started/migrating-data).

### <a name="restoring-a-timescale-database"></a>Przywracanie bazy danych skali czasu
Aby przywrócić bazę danych skali czasu przy użyciu pg_dump i pg_restore, należy uruchomić dwie `timescaledb_pre_restore()` `timescaledb_post restore()`procedury pomocnicze w docelowej bazie danych: i .

Najpierw przygotuj docelową bazę danych:

```SQL
--create the new database where you'll perform the restore
CREATE DATABASE tutorial;
\c tutorial --connect to the database 
CREATE EXTENSION timescaledb;

SELECT timescaledb_pre_restore();
```

Teraz możesz uruchomić pg_dump w oryginalnej bazie danych, a następnie wykonać pg_restore. Po przywróceniu należy uruchomić następujące polecenie w przywróconej bazie danych:

```SQL
SELECT timescaledb_post_restore();
```


## <a name="next-steps"></a>Następne kroki
Jeśli nie widzisz rozszerzenia, którego chcesz użyć, poucz nas o tym. Głosuj na istniejące prośby lub twórz nowe prośby o opinie na naszym [forum opinii.](https://feedback.azure.com/forums/597976-azure-database-for-postgresql)
