---
title: Korzystanie z rozszerzeń PostgreSQL w Azure Database for PostgreSQL-pojedynczym serwerze
description: W tym artykule opisano możliwości rozszerzania funkcjonalności bazy danych przy użyciu rozszerzeń w Azure Database for PostgreSQL-pojedynczym serwerze.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/23/2019
ms.openlocfilehash: 04b17d2e3acba7f003325ca7fdef2107108aea4d
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/23/2019
ms.locfileid: "70013410"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql---single-server"></a>Rozszerzenia PostgreSQL w Azure Database for PostgreSQL — pojedynczy serwer
PostgreSQL zapewnia możliwość rozszerzenia funkcjonalności bazy danych przy użyciu rozszerzeń. Rozszerzenia rozszerzają wiele powiązanych obiektów SQL razem w jednym pakiecie, który można załadować lub usunąć z bazy danych za pomocą jednego polecenia. Po załadowaniu do bazy danych programu rozszerzenia działają jak wbudowane funkcje.

## <a name="how-to-use-postgresql-extensions"></a>Jak używać rozszerzeń PostgreSQL
Rozszerzenia PostgreSQL muszą być zainstalowane w bazie danych, zanim będzie można z nich korzystać. Aby zainstalować określone rozszerzenie, uruchom polecenie [Utwórz rozszerzenie](https://www.postgresql.org/docs/current/sql-createextension.html) z narzędzia PSQL w celu załadowania spakowanych obiektów do bazy danych.

Azure Database for PostgreSQL obsługuje podzestaw rozszerzeń kluczy, jak pokazano poniżej. Te informacje są również dostępne po uruchomieniu `SELECT * FROM pg_available_extensions;`programu. Rozszerzenia poza wymienionymi listami nie są obsługiwane. Nie można utworzyć własnego rozszerzenia w Azure Database for PostgreSQL.

## <a name="postgres-11-extensions"></a>Rozszerzenia Postgres 11

Następujące rozszerzenia są dostępne na serwerach Azure Database for PostgreSQL, które mają Postgres w wersji 11. 

> [!div class="mx-tableFixed"]
> | **Rozszerzenia**| **Wersja rozszerzenia** | **Opis** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.5.1           | Służy do analizowania adresu w elementach składowych. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.5.1           | Przykład dotyczący zestawu danych standaryzacji z nami|
> |[btree_gin](https://www.postgresql.org/docs/11/btree-gin.html)                    | 1.3             | Obsługa indeksowania wspólnych typów danych w ĄTEK|
> |[btree_gist](https://www.postgresql.org/docs/11/btree-gist.html)                   | 1,5             | Obsługa indeksowania wspólnych typów danych w rejestrze|
> |[citext](https://www.postgresql.org/docs/11/citext.html)                       | 1,5             | Typ danych dla ciągów znaków bez uwzględniania wielkości liter|
> |[cube](https://www.postgresql.org/docs/11/cube.html)                         | 1.4             | Typ danych dla modułów wielowymiarowych|
> |[dblink](https://www.postgresql.org/docs/11/dblink.html)                       | 1.2             | Łączenie z innymi bazami danych PostgreSQL z poziomu bazy danych|
> |[dict_int](https://www.postgresql.org/docs/11/dict-int.html)                     | 1.0             | szablon słownika wyszukiwania tekstu dla liczb całkowitych|
> |[earthdistance](https://www.postgresql.org/docs/11/earthdistance.html)                | 1.1             | Oblicz duże odległości na powierzchni ziemi|
> |[fuzzystrmatch](https://www.postgresql.org/docs/11/fuzzystrmatch.html)                | 1.1             | Określ podobieństwa i odległość między ciągami|
> |[hstore](https://www.postgresql.org/docs/11/hstore.html)                       | 1,5             | Typ danych do przechowywania zestawów par (klucz, wartość)|
> |[hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.2           | Indeksy hipotetyczne dla PostgreSQL|
> |[intarray](https://www.postgresql.org/docs/11/intarray.html)                     | 1.2             | Obsługa funkcji, operatorów i indeksów dla tablic 1-D liczb całkowitych|
> |[jest](https://www.postgresql.org/docs/11/isn.html)                          | 1.2             | typy danych dla międzynarodowych standardów numerowania produktów|
> |[ltree](https://www.postgresql.org/docs/11/ltree.html)                        | 1.1             | Typ danych dla struktur hierarchicznych, takich jak drzewa|
> |[orafce](https://github.com/orafce/orafce)                       | 3.7             | Funkcje i operatory, które emulują podzestaw funkcji i pakietów z RDBMS komercyjnych|
> |[pgcrypto](https://www.postgresql.org/docs/11/pgcrypto.html)                     | 1.3             | funkcje kryptograficzne|
> |[pgrouting](https://pgrouting.org/)                    | 2.6.2           | Rozszerzenie pgRouting|
> |[pgrowlocks](https://www.postgresql.org/docs/11/pgrowlocks.html)                   | 1.2             | Pokaż informacje o blokowaniu na poziomie wiersza|
> |[pgstattuple](https://www.postgresql.org/docs/11/pgstattuple.html)                  | 1,5             | Pokaż statystyki na poziomie krotki|
> |[pg_buffercache](https://www.postgresql.org/docs/11/pgbuffercache.html)               | 1.3             | Sprawdzanie udostępnionej pamięci podręcznej buforu|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 4.0.0           | Rozszerzenie do zarządzania tabelami partycjonowanymi według czasu lub identyfikatora|
> |[pg_prewarm](https://www.postgresql.org/docs/11/pgprewarm.html)                   | 1.2             | dane relacji przedgrzanej|
> |[pg_stat_statements](https://www.postgresql.org/docs/11/pgstatstatements.html)           | 1.6             | Śledź statystyki wykonywania wszystkich wykonanych instrukcji SQL|
> |[pg_trgm](https://www.postgresql.org/docs/11/pgtrgm.html)                      | 1.4             | Pomiar podobieństwa tekstu i wyszukiwanie indeksu na podstawie trigrams|
> |[plpgsql](https://www.postgresql.org/docs/11/plpgsql.html)                      | 1.0             | Język proceduralny PL/pgSQL|
> |[plv8](https://plv8.github.io/)                         | 2.3.11          | PL/JavaScript (V8) — zaufany Język proceduralny|
> |[postgis](https://www.postgis.net/)                      | 2.5.1           | PostGIS geometrii, Geografia i typy przestrzenne i funkcje rastrowe|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.5.1           | PostGIS funkcje SFCGAL|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.5.1           | PostGIS geokodera Tiger i odwrotna geokod|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.5.1           | Typy i funkcje przestrzenne topologii PostGIS|
> |[postgres_fdw](https://www.postgresql.org/docs/11/postgres-fdw.html)                 | 1.0             | otoka danych obcych dla zdalnych serwerów PostgreSQL|
> |[tablefunc](https://www.postgresql.org/docs/11/tablefunc.html)                    | 1.0             | funkcje, które manipulują całymi tabelami, w tym krzyżowo|
> |[unaccent](https://www.postgresql.org/docs/11/unaccent.html)                     | 1.1             | słownik wyszukiwania tekstu, który usuwa akcenty|
> |[uuid-ossp](https://www.postgresql.org/docs/11/uuid-ossp.html)                    | 1.1             | Generuj unikatowe identyfikatory uniwersalne (UUID)|

## <a name="postgres-10-extensions"></a>Rozszerzenia Postgres 10 

Następujące rozszerzenia są dostępne na serwerach Azure Database for PostgreSQL z Postgres wersja 10.

> [!div class="mx-tableFixed"]
> | **Rozszerzenia**| **Wersja rozszerzenia** | **Opis** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.5.1           | Służy do analizowania adresu w elementach składowych. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.5.1           | Przykład dotyczący zestawu danych standaryzacji z nami|
> |[btree_gin](https://www.postgresql.org/docs/10/btree-gin.html)                    | 1.3             | Obsługa indeksowania wspólnych typów danych w ĄTEK|
> |[btree_gist](https://www.postgresql.org/docs/10/btree-gist.html)                   | 1,5             | Obsługa indeksowania wspólnych typów danych w rejestrze|
> |[chkpass](https://www.postgresql.org/docs/10/chkpass.html)                       | 1.0             | Typ danych dla haseł szyfrowanych autoszyfrowanie|
> |[citext](https://www.postgresql.org/docs/10/citext.html)                       | 1.4             | Typ danych dla ciągów znaków bez uwzględniania wielkości liter|
> |[cube](https://www.postgresql.org/docs/10/cube.html)                         | 1.2             | Typ danych dla modułów wielowymiarowych|
> |[dblink](https://www.postgresql.org/docs/10/dblink.html)                       | 1.2             | Łączenie z innymi bazami danych PostgreSQL z poziomu bazy danych|
> |[dict_int](https://www.postgresql.org/docs/10/dict-int.html)                     | 1.0             | szablon słownika wyszukiwania tekstu dla liczb całkowitych|
> |[earthdistance](https://www.postgresql.org/docs/10/earthdistance.html)                | 1.1             | Oblicz duże odległości na powierzchni ziemi|
> |[fuzzystrmatch](https://www.postgresql.org/docs/10/fuzzystrmatch.html)                | 1.1             | Określ podobieństwa i odległość między ciągami|
> |[hstore](https://www.postgresql.org/docs/10/hstore.html)                       | 1.4             | Typ danych do przechowywania zestawów par (klucz, wartość)|
> |[hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.1           | Indeksy hipotetyczne dla PostgreSQL|
> |[intarray](https://www.postgresql.org/docs/10/intarray.html)                     | 1.2             | Obsługa funkcji, operatorów i indeksów dla tablic 1-D liczb całkowitych|
> |[jest](https://www.postgresql.org/docs/10/isn.html)                          | 1.1             | typy danych dla międzynarodowych standardów numerowania produktów|
> |[ltree](https://www.postgresql.org/docs/10/ltree.html)                        | 1.1             | Typ danych dla struktur hierarchicznych, takich jak drzewa|
> |[orafce](https://github.com/orafce/orafce)                       | 3.7             | Funkcje i operatory, które emulują podzestaw funkcji i pakietów z RDBMS komercyjnych|
> |[pgcrypto](https://www.postgresql.org/docs/10/pgcrypto.html)                     | 1.3             | funkcje kryptograficzne|
> |[pgrouting](https://pgrouting.org/)                    | 2.5.2           | Rozszerzenie pgRouting|
> |[pgrowlocks](https://www.postgresql.org/docs/10/pgrowlocks.html)                   | 1.2             | Pokaż informacje o blokowaniu na poziomie wiersza|
> |[pgstattuple](https://www.postgresql.org/docs/10/pgstattuple.html)                  | 1,5             | Pokaż statystyki na poziomie krotki|
> |[pg_buffercache](https://www.postgresql.org/docs/10/pgbuffercache.html)               | 1.3             | Sprawdzanie udostępnionej pamięci podręcznej buforu|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 2.6.3           | Rozszerzenie do zarządzania tabelami partycjonowanymi według czasu lub identyfikatora|
> |[pg_prewarm](https://www.postgresql.org/docs/10/pgprewarm.html)                   | 1.1             | dane relacji przedgrzanej|
> |[pg_stat_statements](https://www.postgresql.org/docs/10/pgstatstatements.html)           | 1.6             | Śledź statystyki wykonywania wszystkich wykonanych instrukcji SQL|
> |[pg_trgm](https://www.postgresql.org/docs/10/pgtrgm.html)                      | 1.3             | Pomiar podobieństwa tekstu i wyszukiwanie indeksu na podstawie trigrams|
> |[plpgsql](https://www.postgresql.org/docs/10/plpgsql.html)                      | 1.0             | Język proceduralny PL/pgSQL|
> |[plv8](https://plv8.github.io/)                         | 2.1.0          | PL/JavaScript (V8) — zaufany Język proceduralny|
> |[postgis](https://www.postgis.net/)                      | zasadniczy           | PostGIS geometrii, Geografia i typy przestrzenne i funkcje rastrowe|
> |[postgis_sfcgal](https://www.postgis.net/)               | zasadniczy           | PostGIS funkcje SFCGAL|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | zasadniczy           | PostGIS geokodera Tiger i odwrotna geokod|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | zasadniczy           | Typy i funkcje przestrzenne topologii PostGIS|
> |[postgres_fdw](https://www.postgresql.org/docs/10/postgres-fdw.html)                 | 1.0             | otoka danych obcych dla zdalnych serwerów PostgreSQL|
> |[tablefunc](https://www.postgresql.org/docs/10/tablefunc.html)                    | 1.0             | funkcje, które manipulują całymi tabelami, w tym krzyżowo|
> |[timescaledb](https://docs.timescale.com/latest)                    | 1.1.1             | Włącza skalowalne i złożone zapytania dla danych szeregów czasowych|
> |[unaccent](https://www.postgresql.org/docs/10/unaccent.html)                     | 1.1             | słownik wyszukiwania tekstu, który usuwa akcenty|
> |[uuid-ossp](https://www.postgresql.org/docs/10/uuid-ossp.html)                    | 1.1             | Generuj unikatowe identyfikatory uniwersalne (UUID)|

## <a name="postgres-96-extensions"></a>Rozszerzenia Postgres 9,6 

Następujące rozszerzenia są dostępne na serwerach Azure Database for PostgreSQL, które mają Postgres w wersji 9,6.

> [!div class="mx-tableFixed"]
> | **Rozszerzenia**| **Wersja rozszerzenia** | **Opis** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.3.2           | Służy do analizowania adresu w elementach składowych. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.3.2           | Przykład dotyczący zestawu danych standaryzacji z nami|
> |[btree_gin](https://www.postgresql.org/docs/9.6/btree-gin.html)                    | 1.0             | Obsługa indeksowania wspólnych typów danych w ĄTEK|
> |[btree_gist](https://www.postgresql.org/docs/9.6/btree-gist.html)                   | 1.2             | Obsługa indeksowania wspólnych typów danych w rejestrze|
> |[chkpass](https://www.postgresql.org/docs/9.6/chkpass.html)                       | 1.0             | Typ danych dla haseł szyfrowanych autoszyfrowanie|
> |[citext](https://www.postgresql.org/docs/9.6/citext.html)                       | 1.3             | Typ danych dla ciągów znaków bez uwzględniania wielkości liter|
> |[cube](https://www.postgresql.org/docs/9.6/cube.html)                         | 1.2             | Typ danych dla modułów wielowymiarowych|
> |[dblink](https://www.postgresql.org/docs/9.6/dblink.html)                       | 1.2             | Łączenie z innymi bazami danych PostgreSQL z poziomu bazy danych|
> |[dict_int](https://www.postgresql.org/docs/9.6/dict-int.html)                     | 1.0             | szablon słownika wyszukiwania tekstu dla liczb całkowitych|
> |[earthdistance](https://www.postgresql.org/docs/9.6/earthdistance.html)                | 1.1             | Oblicz duże odległości na powierzchni ziemi|
> |[fuzzystrmatch](https://www.postgresql.org/docs/9.6/fuzzystrmatch.html)                | 1.1             | Określ podobieństwa i odległość między ciągami|
> |[hstore](https://www.postgresql.org/docs/9.6/hstore.html)                       | 1.4             | Typ danych do przechowywania zestawów par (klucz, wartość)|
> |[hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.1           | Indeksy hipotetyczne dla PostgreSQL|
> |[intarray](https://www.postgresql.org/docs/9.6/intarray.html)                     | 1.2             | Obsługa funkcji, operatorów i indeksów dla tablic 1-D liczb całkowitych|
> |[jest](https://www.postgresql.org/docs/9.6/isn.html)                          | 1.1             | typy danych dla międzynarodowych standardów numerowania produktów|
> |[ltree](https://www.postgresql.org/docs/9.6/ltree.html)                        | 1.1             | Typ danych dla struktur hierarchicznych, takich jak drzewa|
> |[orafce](https://github.com/orafce/orafce)                       | 3.7             | Funkcje i operatory, które emulują podzestaw funkcji i pakietów z RDBMS komercyjnych|
> |[pgcrypto](https://www.postgresql.org/docs/9.6/pgcrypto.html)                     | 1.3             | funkcje kryptograficzne|
> |[pgrouting](https://pgrouting.org/)                    | 2.3.2           | Rozszerzenie pgRouting|
> |[pgrowlocks](https://www.postgresql.org/docs/9.6/pgrowlocks.html)                   | 1.2             | Pokaż informacje o blokowaniu na poziomie wiersza|
> |[pgstattuple](https://www.postgresql.org/docs/9.6/pgstattuple.html)                  | 1.4             | Pokaż statystyki na poziomie krotki|
> |[pg_buffercache](https://www.postgresql.org/docs/9.6/pgbuffercache.html)               | 1.2             | Sprawdzanie udostępnionej pamięci podręcznej buforu|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 2.6.3           | Rozszerzenie do zarządzania tabelami partycjonowanymi według czasu lub identyfikatora|
> |[pg_prewarm](https://www.postgresql.org/docs/9.6/pgprewarm.html)                   | 1.1             | dane relacji przedgrzanej|
> |[pg_stat_statements](https://www.postgresql.org/docs/9.6/pgstatstatements.html)           | 1.4             | Śledź statystyki wykonywania wszystkich wykonanych instrukcji SQL|
> |[pg_trgm](https://www.postgresql.org/docs/9.6/pgtrgm.html)                      | 1.3             | Pomiar podobieństwa tekstu i wyszukiwanie indeksu na podstawie trigrams|
> |[plpgsql](https://www.postgresql.org/docs/9.6/plpgsql.html)                      | 1.0             | Język proceduralny PL/pgSQL|
> |[plv8](https://plv8.github.io/)                         | 2.1.0          | PL/JavaScript (V8) — zaufany Język proceduralny|
> |[postgis](https://www.postgis.net/)                      | 2.3.2           | PostGIS geometrii, Geografia i typy przestrzenne i funkcje rastrowe|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.3.2           | PostGIS funkcje SFCGAL|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.3.2           | PostGIS geokodera Tiger i odwrotna geokod|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.3.2           | Typy i funkcje przestrzenne topologii PostGIS|
> |[postgres_fdw](https://www.postgresql.org/docs/9.6/postgres-fdw.html)                 | 1.0             | otoka danych obcych dla zdalnych serwerów PostgreSQL|
> |[tablefunc](https://www.postgresql.org/docs/9.6/tablefunc.html)                    | 1.0             | funkcje, które manipulują całymi tabelami, w tym krzyżowo|
> |[timescaledb](https://docs.timescale.com/latest)                    | 1.1.1             | Włącza skalowalne i złożone zapytania dla danych szeregów czasowych|
> |[unaccent](https://www.postgresql.org/docs/9.6/unaccent.html)                     | 1.1             | słownik wyszukiwania tekstu, który usuwa akcenty|
> |[uuid-ossp](https://www.postgresql.org/docs/9.6/uuid-ossp.html)                    | 1.1             | Generuj unikatowe identyfikatory uniwersalne (UUID)|

## <a name="postgres-95-extensions"></a>Rozszerzenia Postgres 9,5 

Następujące rozszerzenia są dostępne na serwerach Azure Database for PostgreSQL, które mają Postgres w wersji 9,5.

> [!div class="mx-tableFixed"]
> | **Rozszerzenia**| **Wersja rozszerzenia** | **Opis** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.3.0           | Służy do analizowania adresu w elementach składowych. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.3.0           | Przykład dotyczący zestawu danych standaryzacji z nami|
> |[btree_gin](https://www.postgresql.org/docs/9.5/btree-gin.html)                    | 1.0             | Obsługa indeksowania wspólnych typów danych w ĄTEK|
> |[btree_gist](https://www.postgresql.org/docs/9.5/btree-gist.html)                   | 1.1             | Obsługa indeksowania wspólnych typów danych w rejestrze|
> |[chkpass](https://www.postgresql.org/docs/9.5/chkpass.html)                       | 1.0             | Typ danych dla haseł szyfrowanych autoszyfrowanie|
> |[citext](https://www.postgresql.org/docs/9.5/citext.html)                       | 1.1             | Typ danych dla ciągów znaków bez uwzględniania wielkości liter|
> |[cube](https://www.postgresql.org/docs/9.5/cube.html)                         | 1.0             | Typ danych dla modułów wielowymiarowych|
> |[dblink](https://www.postgresql.org/docs/9.5/dblink.html)                       | 1.1             | Łączenie z innymi bazami danych PostgreSQL z poziomu bazy danych|
> |[dict_int](https://www.postgresql.org/docs/9.5/dict-int.html)                     | 1.0             | szablon słownika wyszukiwania tekstu dla liczb całkowitych|
> |[earthdistance](https://www.postgresql.org/docs/9.5/earthdistance.html)                | 1.0             | Oblicz duże odległości na powierzchni ziemi|
> |[fuzzystrmatch](https://www.postgresql.org/docs/9.5/fuzzystrmatch.html)                | 1.0             | Określ podobieństwa i odległość między ciągami|
> |[hstore](https://www.postgresql.org/docs/9.5/hstore.html)                       | 1.3             | Typ danych do przechowywania zestawów par (klucz, wartość)|
> |[hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.1           | Indeksy hipotetyczne dla PostgreSQL|
> |[intarray](https://www.postgresql.org/docs/9.5/intarray.html)                     | 1.0             | Obsługa funkcji, operatorów i indeksów dla tablic 1-D liczb całkowitych|
> |[jest](https://www.postgresql.org/docs/9.5/isn.html)                          | 1.0             | typy danych dla międzynarodowych standardów numerowania produktów|
> |[ltree](https://www.postgresql.org/docs/9.5/ltree.html)                        | 1.0             | Typ danych dla struktur hierarchicznych, takich jak drzewa|
> |[orafce](https://github.com/orafce/orafce)                       | 3.7             | Funkcje i operatory, które emulują podzestaw funkcji i pakietów z RDBMS komercyjnych|
> |[pgcrypto](https://www.postgresql.org/docs/9.5/pgcrypto.html)                     | 1.2             | funkcje kryptograficzne|
> |[pgrouting](https://pgrouting.org/)                    | 2.3.0           | Rozszerzenie pgRouting|
> |[pgrowlocks](https://www.postgresql.org/docs/9.5/pgrowlocks.html)                   | 1.1             | Pokaż informacje o blokowaniu na poziomie wiersza|
> |[pgstattuple](https://www.postgresql.org/docs/9.5/pgstattuple.html)                  | 1.3             | Pokaż statystyki na poziomie krotki|
> |[pg_buffercache](https://www.postgresql.org/docs/9.5/pgbuffercache.html)               | 1.1             | Sprawdzanie udostępnionej pamięci podręcznej buforu|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 2.6.3           | Rozszerzenie do zarządzania tabelami partycjonowanymi według czasu lub identyfikatora|
> |[pg_prewarm](https://www.postgresql.org/docs/9.5/pgprewarm.html)                   | 1.0             | dane relacji przedgrzanej|
> |[pg_stat_statements](https://www.postgresql.org/docs/9.5/pgstatstatements.html)           | 1.3             | Śledź statystyki wykonywania wszystkich wykonanych instrukcji SQL|
> |[pg_trgm](https://www.postgresql.org/docs/9.5/pgtrgm.html)                      | 1.1             | Pomiar podobieństwa tekstu i wyszukiwanie indeksu na podstawie trigrams|
> |[plpgsql](https://www.postgresql.org/docs/9.5/plpgsql.html)                      | 1.0             | Język proceduralny PL/pgSQL|
> |[postgis](https://www.postgis.net/)                      | 2.3.0           | PostGIS geometrii, Geografia i typy przestrzenne i funkcje rastrowe|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.3.0           | PostGIS funkcje SFCGAL|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.3.0           | PostGIS geokodera Tiger i odwrotna geokod|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.3.0           | Typy i funkcje przestrzenne topologii PostGIS|
> |[postgres_fdw](https://www.postgresql.org/docs/9.5/postgres-fdw.html)                 | 1.0             | otoka danych obcych dla zdalnych serwerów PostgreSQL|
> |[tablefunc](https://www.postgresql.org/docs/9.5/tablefunc.html)                    | 1.0             | funkcje, które manipulują całymi tabelami, w tym krzyżowo|
> |[unaccent](https://www.postgresql.org/docs/9.5/unaccent.html)                     | 1.0             | słownik wyszukiwania tekstu, który usuwa akcenty|
> |[uuid-ossp](https://www.postgresql.org/docs/9.5/uuid-ossp.html)                    | 1.0             | Generuj unikatowe identyfikatory uniwersalne (UUID)|


## <a name="pg_stat_statements"></a>pg_stat_statements
Rozszerzenie pg_stat_statements jest wstępnie załadowane na każdym serwerze Azure Database for PostgreSQL, aby umożliwić śledzenie statystyk wykonywania instrukcji SQL.
Ustawienie `pg_stat_statements.track`, które kontroluje, jakie instrukcje są zliczane przez rozszerzenie, domyślnie to `top`, oznacza, że wszystkie instrukcje wydawane bezpośrednio przez klientów są śledzone. Dwa inne poziomy śledzenia to `none` i. `all` To ustawienie można skonfigurować jako parametr serwera za pomocą [Azure Portal](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-portal) lub [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-cli).

Istnieje kompromis między pg_stat_statements informacji o wykonywaniu zapytania i wpływem na wydajność serwera podczas rejestrowania każdej instrukcji SQL. Jeśli nie korzystasz aktywnie z rozszerzenia pg_stat_statements, zalecamy ustawienie wartości `pg_stat_statements.track`. `none` Należy zauważyć, że niektóre usługi monitorowania innych firm mogą polegać na pg_stat_statements na dostarczenie szczegółowych informacji o wydajności zapytań, dlatego należy sprawdzić, czy tak się dzieje.

## <a name="dblink-and-postgres_fdw"></a>dblink i postgres_fdw
dblink i postgres_fdw umożliwiają łączenie się z jednego serwera PostgreSQL z innym lub z inną bazą danych na tym samym serwerze. Serwer otrzymujący musi zezwalać na połączenia z serwera wysyłającego za pośrednictwem jego zapory. Korzystając z tych rozszerzeń do łączenia się między serwerami Azure Database for PostgreSQL, można to zrobić przez ustawienie opcji "Zezwalaj na dostęp do usług platformy Azure" na wartość włączone. Jest to również wymagane, jeśli chcesz użyć rozszerzeń do zapętlenia z powrotem do tego samego serwera. Ustawienie "Zezwalaj na dostęp do usług platformy Azure" można znaleźć na stronie Azure Portal serwera Postgres, w obszarze zabezpieczenia połączeń. Włączenie opcji "Zezwalaj na dostęp do usług platformy Azure" spowoduje umieszczenie wszystkich adresów IP platformy Azure na liście dozwolonych.

Obecnie połączenia wychodzące z Azure Database for PostgreSQL nie są obsługiwane, z wyjątkiem połączeń z innymi serwerami Azure Database for PostgreSQL.

## <a name="uuid"></a>uuid
Jeśli planujesz użyć `uuid_generate_v4()` z rozszerzenia UUID-OSSP, rozważ porównanie z `gen_random_uuid()` rozszerzeniem pgcrypto w celu uzyskania korzyści z wydajności.


## <a name="timescaledb"></a>TimescaleDB
TimescaleDB to baza danych szeregów czasowych spakowana jako rozszerzenie dla PostgreSQL. TimescaleDB zapewnia funkcje analityczne zorientowane czasowo, optymalizacje oraz skaluje Postgres dla obciążeń szeregów czasowych.

[Dowiedz się więcej na temat TimescaleDB](https://docs.timescale.com/latest), zarejestrowanego znaku towarowego [, Inc.](https://www.timescale.com/)

### <a name="installing-timescaledb"></a>Instalowanie TimescaleDB
Aby zainstalować TimescaleDB, należy dołączyć go do udostępnionych bibliotek wstępnego ładowania serwera. Zmiana `shared_preload_libraries` parametru Postgres wymaga **ponownego uruchomienia serwera** . Parametry można zmienić za pomocą [Azure Portal](howto-configure-server-parameters-using-portal.md) lub [interfejsu wiersza polecenia platformy Azure](howto-configure-server-parameters-using-cli.md).

> [!NOTE]
> TimescaleDB można włączyć na Azure Database for PostgreSQL wersje 9,6 i 10

Przy użyciu [Azure Portal](https://portal.azure.com/):

1. Wybierz serwer usługi Azure Database for PostgreSQL.

2. Na pasku bocznym wybierz opcję **parametry serwera**.

3. `shared_preload_libraries` Wyszukaj parametr.

4. Wybierz pozycję **TimescaleDB**.

5. Wybierz pozycję **Zapisz** , aby zachować zmiany. Po zapisaniu zmiany otrzymujesz powiadomienie. 

6. Po powiadomieniu **ponownie uruchom** serwer, aby zastosować te zmiany. Aby dowiedzieć się, jak ponownie uruchomić serwer, zobacz [Ponowne uruchamianie serwera Azure Database for PostgreSQL](howto-restart-server-portal.md).


Teraz można włączyć TimescaleDB w bazie danych Postgres. Nawiąż połączenie z bazą danych i wydaj następujące polecenie:
```sql
CREATE EXTENSION IF NOT EXISTS timescaledb CASCADE;
```
> [!TIP]
> Jeśli zobaczysz błąd, upewnij się, że [serwer został ponownie uruchomiony](howto-restart-server-portal.md) po zapisaniu shared_preload_libraries. 

Teraz można utworzyć tabelę TimescaleDB [od podstaw](https://docs.timescale.com/getting-started/creating-hypertables) lub migrować [istniejące dane szeregów czasowych w PostgreSQL](https://docs.timescale.com/getting-started/migrating-data).


## <a name="next-steps"></a>Następne kroki
Jeśli nie widzisz rozszerzenia, którego chcesz użyć, daj nam znać. Zagłosuj na istniejące żądania lub Utwórz nowe żądania opinii na naszym [forum opinii](https://feedback.azure.com/forums/597976-azure-database-for-postgresql).
