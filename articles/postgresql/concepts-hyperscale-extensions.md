---
title: Rozszerzenia PostgreSQL w Azure Database for PostgreSQL — funkcja do skalowania (Citus)
description: Opisuje możliwość rozszerzenia funkcjonalności bazy danych przy użyciu rozszerzeń w Azure Database for PostgreSQL.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 41edcc40bae7915e321a278592dd2f73621aae05
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73482419"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql--hyperscale-citus"></a>Rozszerzenia PostgreSQL w Azure Database for PostgreSQL — funkcja do skalowania (Citus)

Program PostgreSQL umożliwia rozszerzanie funkcjonalności bazy danych za pomocą rozszerzeń. Rozszerzenia umożliwiają zgrupowanie wielu powiązanych obiektów SQL razem w jednym pakiecie, który można załadować lub usunąć z bazy danych za pomocą jednego polecenia. Po załadowaniu do bazy danych rozszerzenia mogą działać jak wbudowane funkcje. Aby uzyskać więcej informacji na temat rozszerzeń PostgreSQL, zobacz [obiekty powiązane z pakietami w rozszerzeniu](https://www.postgresql.org/docs/9.6/static/extend-extensions.html).

## <a name="use-postgresql-extensions"></a>Korzystanie z rozszerzeń PostgreSQL

Rozszerzenia PostgreSQL muszą być zainstalowane w bazie danych, zanim będzie można z nich korzystać. Aby zainstalować określone rozszerzenie, uruchom polecenie [Create extension](https://www.postgresql.org/docs/9.6/static/sql-createextension.html) w narzędziu PSQL w celu załadowania spakowanych obiektów do bazy danych.

Azure Database for PostgreSQL-Citus) obecnie obsługuje podzestaw głównych rozszerzeń, jak pokazano tutaj. Rozszerzenia inne niż wymienione na liście nie są obsługiwane. Nie można utworzyć własnego rozszerzenia za pomocą Azure Database for PostgreSQL.

## <a name="extensions-supported-by-azure-database-for-postgresql"></a>Rozszerzenia obsługiwane przez Azure Database for PostgreSQL

W poniższej tabeli wymieniono standardowe rozszerzenia PostgreSQL, które są obecnie obsługiwane przez Azure Database for PostgreSQL. Te informacje są również dostępne przez uruchomienie `SELECT * FROM pg_available_extensions;`.

### <a name="data-types-extensions"></a>Rozszerzenia typów danych

> [!div class="mx-tableFixed"]
> | **Rozszerzenia** | **Opis** |
> |---|---|
> | [citext](https://www.postgresql.org/docs/9.6/static/citext.html) | Zawiera typ ciągu znaków bez uwzględniania wielkości liter. |
> | [cube](https://www.postgresql.org/docs/9.6/static/cube.html) | Udostępnia typ danych dla modułów wielowymiarowych. |
> | [hstore](https://www.postgresql.org/docs/9.6/static/hstore.html) | Dostarcza typ danych do przechowywania zestawów par klucz-wartość. |
> | [jest](https://www.postgresql.org/docs/9.6/static/isn.html) | Zapewnia typy danych dla międzynarodowych standardów numerowania produktów. |
> | [Lo](https://www.postgresql.org/docs/current/lo.html) | Konserwacja dużego obiektu. |
> | [ltree](https://www.postgresql.org/docs/9.6/static/ltree.html) | Dostarcza typ danych dla hierarchicznych struktur przypominających drzewa. |
> | [seg](https://www.postgresql.org/docs/current/seg.html) | Typ danych służący do reprezentowania segmentów linii lub interwałów zmiennoprzecinkowych. |
> | [TopN](https://github.com/citusdata/postgresql-topn/) | Wpisz dla elementu Top-n JSONB. |

### <a name="full-text-search-extensions"></a>Rozszerzenia wyszukiwania pełnotekstowego

> [!div class="mx-tableFixed"]
> | **Rozszerzenia** | **Opis** |
> |---|---|
> | [DICT\_int](https://www.postgresql.org/docs/9.6/static/dict-int.html) | Udostępnia szablon słownika wyszukiwania tekstu dla liczb całkowitych. |
> | [DICT\_xsyn](https://www.postgresql.org/docs/current/dict-xsyn.html) | Szablon słownika wyszukiwania tekstu dla rozszerzonego przetwarzania synonimów. |
> | [unaccent](https://www.postgresql.org/docs/9.6/static/unaccent.html) | Słownik wyszukiwania tekstu, który usuwa akcenty (znaki diakrytyczne) z lexemes. |

### <a name="functions-extensions"></a>Rozszerzenia funkcji

> [!div class="mx-tableFixed"]
> | **Rozszerzenia** | **Opis** |
> |---|---|
> | [autoinc](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.7) | Funkcje do autozwiększania wartości pól. |
> | [earthdistance](https://www.postgresql.org/docs/9.6/static/earthdistance.html) | Zapewnia środki do obliczania dużej liczby okręgów na powierzchni ziemi. |
> | [fuzzystrmatch](https://www.postgresql.org/docs/9.6/static/fuzzystrmatch.html) | Udostępnia kilka funkcji, aby określić podobieństwa i odległość między ciągami. |
> | [Wstaw nazwę użytkownika\_](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.8) | Funkcje śledzenia, kto zmienił tabelę. |
> | [intagg](https://www.postgresql.org/docs/current/intagg.html) | Agregator i moduł wyliczający liczby całkowite (przestarzałe). |
> | [intarray](https://www.postgresql.org/docs/9.6/static/intarray.html) | Udostępnia funkcje i operatory umożliwiające manipulowanie tablicami całkowitymi bez wartości null. |
> | [moddatetime](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.9) | Funkcje śledzenia czasu ostatniej modyfikacji. |
> | [pgcrypto](https://www.postgresql.org/docs/9.6/static/pgcrypto.html) | Udostępnia funkcje kryptograficzne. |
> | [PG\_część](https://pgxn.org/dist/pg_partman/doc/pg_partman.html) | Zarządza tabelami partycjonowanymi według czasu lub identyfikatora. |
> | [PG\_trgm](https://www.postgresql.org/docs/9.6/static/pgtrgm.html) | Oferuje funkcje i operatory umożliwiające określanie podobieństwa tekstu alfanumerycznego na podstawie dopasowywania trigram. |
> | [refint](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.5) | Funkcje do implementowania integralności referencyjnej (przestarzałe). |
> | Analiza\_sesji | Funkcje do wykonywania zapytań dotyczących tablic HStore. |
> | [tablefunc](https://www.postgresql.org/docs/9.6/static/tablefunc.html) | Udostępnia funkcje, które manipulują całymi tabelami, w tym krzyżowo. |
> | [tcn](https://www.postgresql.org/docs/current/tcn.html) | Wyzwolone powiadomienia o zmianach. |
> | [timetravel](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.6) | Funkcje do implementowania czasu podróży. |
> | [uuid-ossp](https://www.postgresql.org/docs/9.6/static/uuid-ossp.html) | Generuje unikatowe identyfikatory uniwersalne (UUID). |

### <a name="hyperscale-extensions"></a>Rozszerzenia do skalowania

> [!div class="mx-tableFixed"]
> | **Rozszerzenia** | **Opis** |
> |---|---|
> | [citus](https://github.com/citusdata/citus) | Citus rozproszonej bazy danych. |
> | Moduł równoważenia\_fragmentu | Bezpiecznie ponownie Zrównoważ dane w grupie serwerów na wypadek dodania lub usunięcia węzła. |

### <a name="index-types-extensions"></a>Rozszerzenia typów indeksów

> [!div class="mx-tableFixed"]
> | **Rozszerzenia** | **Opis** |
> |---|---|
> | [lotem](https://www.postgresql.org/docs/current/bloom.html) | Metoda dostępu rozwiniętego — indeks oparty na plikach sygnatur. |
> | [btree\_gin](https://www.postgresql.org/docs/9.6/static/btree-gin.html) | Dostarcza przykładowe klasy operatorów ĄTEK, które implementują zachowanie typu B-drzewo dla określonych typów danych. |
> | [btree\_gist](https://www.postgresql.org/docs/9.6/static/btree-gist.html) | Dostarcza klasy operatorów indeksów indeksu, które implementują B-Tree. |

### <a name="language-extensions"></a>Rozszerzenia językowe

> [!div class="mx-tableFixed"]
> | **Rozszerzenia** | **Opis** |
> |---|---|
> | [plpgsql](https://www.postgresql.org/docs/9.6/static/plpgsql.html) | Język proceduralny z możliwością ładowania PL/pgSQL. |

### <a name="miscellaneous-extensions"></a>Różne rozszerzenia

> [!div class="mx-tableFixed"]
> | **Rozszerzenia** | **Opis** |
> |---|---|
> | [adminpack](https://www.postgresql.org/docs/current/adminpack.html) | Funkcje administracyjne dla PostgreSQL. |
> | [amcheck](https://www.postgresql.org/docs/current/amcheck.html) | Funkcje do sprawdzania integralności relacji. |
> | [FDW pliku\_](https://www.postgresql.org/docs/current/file-fdw.html) | Otoka danych obcych na potrzeby prostego dostępu do plików. |
> | [pageinspect](https://www.postgresql.org/docs/current/pageinspect.html) | Sprawdź zawartość stron bazy danych na niskim poziomie. |
> | [pg\_buffercache](https://www.postgresql.org/docs/9.6/static/pgbuffercache.html) | Zapewnia metodę sprawdzania, co dzieje się w pamięci podręcznej buforów udostępnionych w czasie rzeczywistym. |
> | [PG\_firmy CRONUS](https://github.com/citusdata/pg_cron) | Harmonogram zadań dla PostgreSQL. |
> | [PG\_freespacemap](https://www.postgresql.org/docs/current/pgfreespacemap.html) | Obejrzyj mapę wolnego miejsca (FSM). |
> | [przedgrzana\_PG](https://www.postgresql.org/docs/9.6/static/pgprewarm.html) | Zapewnia sposób ładowania danych relacji do pamięci podręcznej buforów. |
> | [\_PG — instrukcje\_](https://www.postgresql.org/docs/9.6/static/pgstatstatements.html) | Zapewnia metodę śledzenia statystyk wykonywania wszystkich instrukcji SQL wykonywanych przez serwer. Informacje o tym rozszerzeniu można znaleźć w sekcji "pg_stat_statements". |
> | [widoczność\_PG](https://www.postgresql.org/docs/current/pgvisibility.html) | Obejrzyj mapę widoczności (VM) i informacje o widoczności na poziomie strony. |
> | [pgrowlocks](https://www.postgresql.org/docs/9.6/static/pgrowlocks.html) | Zapewnia sposób wyświetlania informacji o blokowaniu na poziomie wiersza. |
> | [pgstattuple](https://www.postgresql.org/docs/9.6/static/pgstattuple.html) | Zapewnia sposób wyświetlania statystyk na poziomie krotki. |
> | [Postgres\_FDW](https://www.postgresql.org/docs/9.6/static/postgres-fdw.html) | Otoka danych obcych używana do uzyskiwania dostępu do danych przechowywanych na zewnętrznych serwerach PostgreSQL. Informacje o tym rozszerzeniu można znaleźć w sekcji "dblink i postgres_fdw".|
> | [sslinfo](https://www.postgresql.org/docs/current/sslinfo.html) | Informacje o certyfikatach SSL. |
> | [\_TSM\_systemowe wierszy](https://www.postgresql.org/docs/current/tsm-system-rows.html) | Metoda reklasy, która akceptuje liczbę wierszy jako limit. |
> | [czas\_system\_TSM](https://www.postgresql.org/docs/current/tsm-system-time.html) | Metoda potraktować jako limit czas (w milisekundach). |
> | [hypopg](https://hypopg.readthedocs.io/en/latest/) | Zapewnia metodę tworzenia indeksów hipotetycznych, które nie są kosztami procesora lub dysku. |
> | [dblink](https://www.postgresql.org/docs/current/dblink.html) | Moduł obsługujący połączenia z innymi bazami danych PostgreSQL z poziomu sesji bazy danych. Informacje o tym rozszerzeniu można znaleźć w sekcji "dblink i postgres_fdw". |
> | [xml2](https://www.postgresql.org/docs/current/xml2.html) | Zapytania XPath i XSLT. |


### <a name="postgis-extensions"></a>Rozszerzenia PostGIS

> [!div class="mx-tableFixed"]
> | **Rozszerzenia** | **Opis** |
> |---|---|
> | [PostGIS](https://www.postgis.net/), PostGIS\_Topology, PostGIS\_Tiger\_geocodeer, PostGIS\_sfcgal | Obiekty przestrzenne i geograficzne dla PostgreSQL. |
> | adres\_normalizacji, adres\_normalizacji\_danych\_US | Służy do analizowania adresu w elementach składowych. Używane do obsługi kroku normalizacji adresów geokodowania. |
> | PostGIS\_sfcgal | Funkcje PostGIS SFCGAL. |
> | PostGIS\_Tiger\_geocode | PostGIS geokodny Tiger i odwrotny geokod. |
> | Topologia\_PostGIS | Typy i funkcje przestrzenne topologii PostGIS. |


## <a name="pg_stat_statements"></a>pg_stat_statements
[Rozszerzenie pg\_stat\_instrukcji](https://www.postgresql.org/docs/current/pgstatstatements.html) jest wstępnie załadowane na każdym serwerze Azure Database for PostgreSQL, aby zapewnić możliwość śledzenia statystyk wykonywania instrukcji SQL.

Ustawienie `pg_stat_statements.track` określa, jakie instrukcje są zliczane przez rozszerzenie. Domyślnie `top`, co oznacza, że wszystkie instrukcje wydawane bezpośrednio przez klientów są śledzone. Dwa inne poziomy śledzenia są `none` i `all`. To ustawienie można skonfigurować jako parametr serwera za pomocą [Azure Portal](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-portal) lub [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-cli).

Istnieje kompromis między informacjami o wykonywaniu zapytania pg_stat_statements zapewnia i wpływ na wydajność serwera podczas rejestrowania każdej instrukcji SQL. Jeśli nie korzystasz aktywnie z rozszerzenia pg_stat_statements, zalecamy ustawienie `pg_stat_statements.track` na `none`. Niektóre usługi monitorowania innych firm mogą polegać na pg_stat_statements w celu dostarczenia szczegółowych informacji o wydajności zapytań, dlatego należy sprawdzić, czy tak się dzieje.

## <a name="dblink-and-postgres_fdw"></a>dblink i postgres_fdw
Można użyć dblink i postgres_fdw, aby nawiązać połączenie z jednego serwera PostgreSQL do innego lub do innej bazy danych na tym samym serwerze. Serwer otrzymujący musi zezwalać na połączenia z serwera wysyłającego za pośrednictwem jego zapory. Aby użyć tych rozszerzeń do łączenia się między serwerami Azure Database for PostgreSQL, ustaw opcję **Zezwalaj na dostęp do usług platformy Azure** . To ustawienie należy również włączyć, jeśli chcesz użyć rozszerzeń do zapętlenia z powrotem do tego samego serwera. Ustawienie **Zezwalaj na dostęp do usług platformy Azure** można znaleźć na stronie Azure Portal serwera Postgres w obszarze **zabezpieczenia połączeń**. Włączenie opcji **Zezwalaj na dostęp do usług platformy Azure** w usłudze dozwolonych wszystkie adresy IP platformy Azure.

Obecnie połączenia wychodzące z Azure Database for PostgreSQL nie są obsługiwane, z wyjątkiem połączeń z innymi serwerami Azure Database for PostgreSQL.
