---
title: Rozszerzenia PostgreSQL w usłudze Azure Database for PostgreSQL — w Hiperskali (Citus) (wersja zapoznawcza)
description: W tym artykule opisano możliwości rozszerzania funkcji bazy danych przy użyciu rozszerzenia w usłudze Azure Database for PostgreSQL.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 4022c95bfda8cbdaed75876793bfbba4254a5c54
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2019
ms.locfileid: "65410247"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql---hyperscale-citus-preview"></a>Rozszerzenia PostgreSQL w usłudze Azure Database for PostgreSQL — w Hiperskali (Citus) (wersja zapoznawcza)

PostgreSQL zapewnia możliwość rozszerzania funkcji bazy danych za pomocą rozszerzeń. Rozszerzenia umożliwiają grupowanie wielu powiązanych obiektów SQL razem w jednym pakiecie, który można załadować lub usunięte z bazy danych za pomocą jednego polecenia. Po ładowany w bazie danych rozszerzenia może działać tak jak wbudowane funkcje. Aby uzyskać więcej informacji na temat rozszerzenia PostgreSQL, zobacz [pakowania powiązanych obiektów w rozszerzeniu](https://www.postgresql.org/docs/9.6/static/extend-extensions.html).

## <a name="how-to-use-postgresql-extensions"></a>Jak używać rozszerzenia PostgreSQL

Rozszerzenia PostgreSQL musi być zainstalowany w bazie danych, zanim będzie można ich użyć. Aby zainstalować określonego rozszerzenia, uruchom [Tworzenie rozszerzenia](https://www.postgresql.org/docs/9.6/static/sql-createextension.html) polecenia za pomocą narzędzia psql załadować spakowanych obiektów do bazy danych.

Usługa Azure Database for postgresql w warstwie obsługuje obecnie podzbiór rozszerzeń klucza wymienione poniżej. Rozszerzenia poza wymienione są nieobsługiwane. własne rozszerzenie nie można utworzyć z usługą Azure Database for postgresql w warstwie usługi.

## <a name="extensions-supported-by-azure-database-for-postgresql"></a>Rozszerzenia obsługiwane przez usługę Azure Database for PostgreSQL

W poniższej tabeli wymieniono standardowego rozszerzenia PostgreSQL, które są obecnie obsługiwane przez usługę Azure Database for PostgreSQL. Te informacje są także dostępne przez uruchomienie `SELECT * FROM pg_available_extensions;`.

### <a name="data-types-extensions"></a>Rozszerzenia typów danych

> [!div class="mx-tableFixed"]
> | **Rozszerzenie** | **Opis** |
> |---|---|
> | [citext](https://www.postgresql.org/docs/9.6/static/citext.html) | Zawiera typ ciągu znaków bez uwzględniania wielkości liter. |
> | [cube](https://www.postgresql.org/docs/9.6/static/cube.html) | Zawiera typ danych wielowymiarowych modułów. |
> | [hstore](https://www.postgresql.org/docs/9.6/static/hstore.html) | Zawiera typ danych do przechowywania zestawów pary klucz/wartość. |
> | [nie jest](https://www.postgresql.org/docs/9.6/static/isn.html) | Zawiera typy danych dla produktu międzynarodowych numerowanie standardy. |
> | [lo](https://www.postgresql.org/docs/current/lo.html) | Duże konserwacji obiektu. |
> | [ltree](https://www.postgresql.org/docs/9.6/static/ltree.html) | Zawiera typ danych hierarchicznej struktury drzewa. |
> | [seg —](https://www.postgresql.org/docs/current/seg.html) | Typ reprezentujący segmenty linii lub zmiennoprzecinkową interwałów danych. |
> | [naj](https://github.com/citusdata/postgresql-topn/) | Typ dla pierwszych n JSONB. |

### <a name="functions-extensions"></a>Funkcje rozszerzeń

> [!div class="mx-tableFixed"]
> | **Rozszerzenie** | **Opis** |
> |---|---|
> | [AUTOINC](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.7) | Funkcje dla pól typu AutoIncrement. |
> | [earthdistance](https://www.postgresql.org/docs/9.6/static/earthdistance.html) | Zapewnia sposób obliczania odległości great koła na powierzchni ziemi. |
> | [fuzzystrmatch](https://www.postgresql.org/docs/9.6/static/fuzzystrmatch.html) | Udostępnia kilka funkcji do określenia podobieństwa i odległości między ciągami. |
> | [Wstaw\_nazwy użytkownika](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.8) | Funkcje śledzenia, kto go zmienił tabeli. |
> | [intagg](https://www.postgresql.org/docs/current/intagg.html) | Liczba całkowita agregatora i modułu wyliczającego (przestarzałe). |
> | [intarray](https://www.postgresql.org/docs/9.6/static/intarray.html) | Udostępnia funkcje i operatory do manipulowania bezpłatne null tablice liczb całkowitych. |
> | [moddatetime](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.9) | Funkcje śledzenia czas ostatniej modyfikacji. |
> | [pgcrypto](https://www.postgresql.org/docs/9.6/static/pgcrypto.html) | Udostępnia funkcje kryptograficzne. |
> | [pg\_partman](https://pgxn.org/dist/pg_partman/doc/pg_partman.html) | Zarządza partycjonowane tabele, czasu lub identyfikatora. |
> | [PG\_trgm](https://www.postgresql.org/docs/9.6/static/pgtrgm.html) | Udostępnia funkcje i operatory określania podobieństwa alfanumeryczne, w oparciu o dopasowanie trigram. |
> | [refint](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.5) | Funkcje dotyczące implementowania integralność referencyjną (przestarzałe). |
> | Sesja\_analizy | Funkcje do wykonywania zapytań hstore tablic. |
> | [tablefunc](https://www.postgresql.org/docs/9.6/static/tablefunc.html) | Oferuje funkcje, które manipulują całe tabele, w tym krzyżowym. |
> | [tcn](https://www.postgresql.org/docs/current/tcn.html) | Wyzwalane, powiadomienia o zmianach. |
> | [timetravel](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.6) | Funkcje dotyczące implementowania czas podróży. |
> | [uuid-ossp](https://www.postgresql.org/docs/9.6/static/uuid-ossp.html) | Generuje powszechnie unikatowe identyfikatory (UUID). |

### <a name="full-text-search-extensions"></a>Wyszukiwanie pełnotekstowe rozszerzenia

> [!div class="mx-tableFixed"]
> | **Rozszerzenie** | **Opis** |
> |---|---|
> | [dict\_int](https://www.postgresql.org/docs/9.6/static/dict-int.html) | Zawiera szablon słownika wyszukiwania tekstu, liczb całkowitych. |
> | [dict\_xsyn](https://www.postgresql.org/docs/current/dict-xsyn.html) | Tekst wyszukiwania słownika szablon dla przetwarzania rozszerzonej synonimów. |
> | [unaccent](https://www.postgresql.org/docs/9.6/static/unaccent.html) | Słownik wyszukiwania tekstu, który usuwa akcentu (znaki diakrytyczne) z lexemes. |

### <a name="index-types-extensions"></a>Rozszerzenia typu indeksu

> [!div class="mx-tableFixed"]
> | **Rozszerzenie** | **Opis** |
> |---|---|
> | [bloom](https://www.postgresql.org/docs/current/bloom.html) | Metoda dostępu rozwiniętej — plik podpisu oparty indeksu. |
> | [btree\_gin](https://www.postgresql.org/docs/9.6/static/btree-gin.html) | Zawiera przykładowe GIN operator klasy, które implementują B-drzewa, takie jak zachowanie dla określonych typów danych. |
> | [btree\_gist](https://www.postgresql.org/docs/9.6/static/btree-gist.html) | Udostępnia GiST indeksu operator klasy, które implementują B-drzewa. |

### <a name="language-extensions"></a>Rozszerzenia językowe

> [!div class="mx-tableFixed"]
> | **Rozszerzenie** | **Opis** |
> |---|---|
> | [plpgsql](https://www.postgresql.org/docs/9.6/static/plpgsql.html) | PL/pgSQL obciążana język procedurach. |

### <a name="hyperscale-extensions"></a>Rozszerzenia w Hiperskali

> [!div class="mx-tableFixed"]
> | **Rozszerzenie** | **Opis** |
> |---|---|
> | [citus](https://github.com/citusdata/citus) | Citus rozproszoną bazę danych. |
> | fragment\_rebalancer | Bezpiecznie przeprowadzić ponowne równoważenie danych w grupie serwera w przypadku węzła dodawania lub usuwania. |

### <a name="miscellaneous-extensions"></a>Różne rozszerzenia

> [!div class="mx-tableFixed"]
> | **Rozszerzenie** | **Opis** |
> |---|---|
> | [adminpack](https://www.postgresql.org/docs/current/adminpack.html) | Funkcje administracyjne database for PostgreSQL. |
> | [amcheck](https://www.postgresql.org/docs/current/amcheck.html) | Funkcje w celu sprawdzenia integralności relacji. |
> | [plik\_fdw](https://www.postgresql.org/docs/current/file-fdw.html) | Otoka obcych danych uzyskać dostęp do plików prostych. |
> | [pageinspect](https://www.postgresql.org/docs/current/pageinspect.html) | Sprawdź, czy zawartość strony bazy danych na niskim poziomie. |
> | [pg\_buffercache](https://www.postgresql.org/docs/9.6/static/pgbuffercache.html) | Udostępnia metody badania, co dzieje się w pamięci podręcznej buforu w czasie rzeczywistym. |
> | [PG\_cron](https://github.com/citusdata/pg_cron) | Harmonogram zadań dla PostgreSQL. |
> | [PG\_freespacemap](https://www.postgresql.org/docs/current/pgfreespacemap.html) | Sprawdź mapy wolnego miejsca (FSM). |
> | [PG\_prewarm](https://www.postgresql.org/docs/9.6/static/pgprewarm.html) | Zapewnia sposób ładowania danych relacji w buforze pamięci podręcznej. |
> | [PG\_stat\_instrukcji](https://www.postgresql.org/docs/9.6/static/pgstatstatements.html) | Umożliwia śledzenie statystyk wykonywania instrukcji SQL wszystkie wykonane przez serwer. (Zobacz poniżej uwagi na to rozszerzenie). |
> | [PG\_widoczności](https://www.postgresql.org/docs/current/pgvisibility.html) | Sprawdź widoczność mapy (VM) i informacje o widoczności na poziomie strony. |
> | [pgrowlocks](https://www.postgresql.org/docs/9.6/static/pgrowlocks.html) | Umożliwia wyświetlanie informacje dotyczące blokowania na poziomie wiersza. |
> | [pgstattuple](https://www.postgresql.org/docs/9.6/static/pgstattuple.html) | Zapewnia to do wyświetlania statystyki na poziomie spójnej kolekcji. |
> | [postgres\_fdw](https://www.postgresql.org/docs/9.6/static/postgres-fdw.html) | Otoka obcych danych umożliwiają dostęp do danych przechowywanych w zewnętrznych serwerów PostgreSQL. (Zobacz poniżej uwagi na to rozszerzenie).|
> | [sslinfo](https://www.postgresql.org/docs/current/sslinfo.html) | Informacje dotyczące certyfikatów SSL. |
> | [TSM\_systemu\_wierszy](https://www.postgresql.org/docs/current/tsm-system-rows.html) | Metoda TABLESAMPLE, która przyjmuje liczbę wierszy jako limit. |
> | [tsm\_system\_time](https://www.postgresql.org/docs/current/tsm-system-time.html) | Metoda TABLESAMPLE, który akceptuje czas w milisekundach jako limit. |
> | [hypopg](https://hypopg.readthedocs.io/en/latest/) | Umożliwia tworzenie indeksów hipotetycznych, które nie koszt procesora CPU lub dysk. |
> | [dblink](https://www.postgresql.org/docs/current/dblink.html) | Moduł, który obsługuje połączenia do innych baz danych PostgreSQL z w ramach sesji bazy danych. (Zobacz poniżej uwagi na to rozszerzenie). |
> | [xml2](https://www.postgresql.org/docs/current/xml2.html) | Zapytania XPath i XSLT. |


### <a name="postgis-extensions"></a>Rozszerzenia PostGIS

> [!div class="mx-tableFixed"]
> | **Rozszerzenie** | **Opis** |
> |---|---|
> | [PostGIS](https://www.postgis.net/), postgis\_topologii, postgis\_tiger\_geocoder, postgis\_sfcgal | Obiekty przestrzennych i geograficzne database for PostgreSQL. |
> | adres\_standardizer, adres\_standardizer\_danych\_USA | Używany, można przeanalizować adresu na elementy składowe. Używane do obsługi geokodowania adres normalizacji kroku. |
> | postgis\_sfcgal | Funkcje PostGIS SFCGAL. |
> | postgis\_tiger\_geocoder | PostGIS tiger geocoder i geocoder wstecznego. |
> | postgis\_topologii | PostGIS topologii typów przestrzennych i funkcji. |


## <a name="pgstatstatements"></a>pg_stat_statements
[Pg\_stat\_rozszerzenia instrukcji](https://www.postgresql.org/docs/9.6/static/pgstatstatements.html) jest wstępnie na każdego serwera Azure Database for postgresql w warstwie zapewnienie oznacza śledzenia statystyk wykonywania instrukcji SQL.
Ustawienie `pg_stat_statements.track`, która kontroluje, jakie instrukcje są uwzględniane przez rozszerzenie, wartość domyślna to `top`, co oznacza wszystkie instrukcje wydane bezpośrednio przez klientów są śledzone. Są dwa inne poziomy śledzenia `none` i `all`. To ustawienie jest konfigurowane jako parametr serwera za pomocą [witryny Azure portal](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-portal) lub [wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-cli).

Istnieje zależność między informacje o wykonaniu zapytania zawiera pg_stat_statements i wpływ na wydajność serwera jako rejestruje każdej instrukcji SQL. Jeśli nie używasz aktywnie rozszerzenia pg_stat_statements, firma Microsoft zaleca ustawienie `pg_stat_statements.track` do `none`. Należy pamiętać, że niektóre innych firm, monitorowania usług mogą polegać na pg_stat_statements dostarczać szczegółowe informacje o wydajności zapytań, więc upewnij się, czy to w przypadku.

## <a name="dblink-and-postgresfdw"></a>dblink i postgres_fdw
dblink i postgres_fdw umożliwiają połączenie się z jednego serwera PostgreSQL do innego lub do innej bazy danych na tym samym serwerze. Serwer odbierający musi zezwalać na połączenia z serwerem wysyłania za pośrednictwem swojej zapory. Korzystając z tych rozszerzeń do połączenia między — Azure Database for postgresql — serwery, można to zrobić przez ustawienie "Zezwalaj na dostęp do usług platformy Azure" na wartość ON. Jest to również potrzebne, jeśli chcesz użyć rozszerzeń w pętli do tego samego serwera. Ustawienie "Zezwalaj na dostęp do usług platformy Azure", można znaleźć w witrynie Azure portal strona serwera Postgres, w obszarze zabezpieczeń połączenia. Włączenie opcji "Zezwalaj na dostęp do usług platformy Azure" na umieszczenie wszystkich adresów IP usługi Azure.

Obecnie połączenia wychodzące z usługi Azure Database for PostgreSQL nie są obsługiwane, z wyjątkiem połączeń z innymi — Azure Database for postgresql w warstwie serwerów.
