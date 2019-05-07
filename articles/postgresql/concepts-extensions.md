---
title: Użyj rozszerzenia PostgreSQL w usłudze Azure Database for PostgreSQL — pojedynczy serwer
description: W tym artykule opisano możliwości rozszerzania funkcji bazy danych za pomocą rozszerzeń w usłudze Azure Database for PostgreSQL — pojedynczy serwer.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 1e3a27f953246ce29de419949362c23b8932b0d5
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65073493"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql---single-server"></a>Rozszerzenia PostgreSQL w usłudze Azure Database for PostgreSQL — pojedynczy serwer
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
> | [chkpass](https://www.postgresql.org/docs/9.6/static/chkpass.html) | Zawiera typ danych dla hasła szyfrowane automatycznie. |
> | [citext](https://www.postgresql.org/docs/9.6/static/citext.html) | Zawiera typ ciągu znaków bez uwzględniania wielkości liter. |
> | [cube](https://www.postgresql.org/docs/9.6/static/cube.html) | Zawiera typ danych wielowymiarowych modułów. |
> | [hstore](https://www.postgresql.org/docs/9.6/static/hstore.html) | Zawiera typ danych do przechowywania zestawów pary klucz/wartość. |
> | [nie jest](https://www.postgresql.org/docs/9.6/static/isn.html) | Zawiera typy danych dla produktu międzynarodowych numerowanie standardy. |
> | [ltree](https://www.postgresql.org/docs/9.6/static/ltree.html) | Zawiera typ danych hierarchicznej struktury drzewa. |

### <a name="functions-extensions"></a>Funkcje rozszerzeń

> [!div class="mx-tableFixed"]
> | **Rozszerzenie** | **Opis** |
> |---|---|
> | [earthdistance](https://www.postgresql.org/docs/9.6/static/earthdistance.html) | Zapewnia sposób obliczania odległości great koła na powierzchni ziemi. |
> | [fuzzystrmatch](https://www.postgresql.org/docs/9.6/static/fuzzystrmatch.html) | Udostępnia kilka funkcji do określenia podobieństwa i odległości między ciągami. |
> | [intarray](https://www.postgresql.org/docs/9.6/static/intarray.html) | Udostępnia funkcje i operatory do manipulowania bezpłatne null tablice liczb całkowitych. |
> | [pgcrypto](https://www.postgresql.org/docs/9.6/static/pgcrypto.html) | Udostępnia funkcje kryptograficzne. |
> | [pg\_partman](https://pgxn.org/dist/pg_partman/doc/pg_partman.html) | Zarządza partycjonowane tabele, czasu lub identyfikatora. |
> | [PG\_trgm](https://www.postgresql.org/docs/9.6/static/pgtrgm.html) | Udostępnia funkcje i operatory określania podobieństwa alfanumeryczne, w oparciu o dopasowanie trigram. |
> | [tablefunc](https://www.postgresql.org/docs/9.6/static/tablefunc.html) | Oferuje funkcje, które manipulują całe tabele, w tym krzyżowym. |
> | [uuid-ossp](https://www.postgresql.org/docs/9.6/static/uuid-ossp.html) | Generuje powszechnie unikatowe identyfikatory (UUID). |
> | [orafce](https://github.com/orafce/orafce) | Zawiera podzbiór funkcji i pakietów emulowane z komercyjnego baz danych. |

### <a name="full-text-search-extensions"></a>Wyszukiwanie pełnotekstowe rozszerzenia

> [!div class="mx-tableFixed"]
> | **Rozszerzenie** | **Opis** |
> |---|---|
> | [dict\_int](https://www.postgresql.org/docs/9.6/static/dict-int.html) | Zawiera szablon słownika wyszukiwania tekstu, liczb całkowitych. |
> | [unaccent](https://www.postgresql.org/docs/9.6/static/unaccent.html) | Słownik wyszukiwania tekstu, który usuwa akcentu (znaki diakrytyczne) z lexemes. |

### <a name="index-types-extensions"></a>Rozszerzenia typu indeksu

> [!div class="mx-tableFixed"]
> | **Rozszerzenie** | **Opis** |
> |---|---|
> | [btree\_gin](https://www.postgresql.org/docs/9.6/static/btree-gin.html) | Zawiera przykładowe GIN operator klasy, które implementują B-drzewa, takie jak zachowanie dla określonych typów danych. |
> | [btree\_gist](https://www.postgresql.org/docs/9.6/static/btree-gist.html) | Udostępnia GiST indeksu operator klasy, które implementują B-drzewa. |

### <a name="language-extensions"></a>Rozszerzenia językowe

> [!div class="mx-tableFixed"]
> | **Rozszerzenie** | **Opis** |
> |---|---|
> | [plpgsql](https://www.postgresql.org/docs/9.6/static/plpgsql.html) | PL/pgSQL obciążana język procedurach. |
> | [plv8](https://plv8.github.io/) | Rozszerzenie języka Javascript dla PostgreSQL, który może służyć do procedur składowanych, wyzwalaczy, itp. |

### <a name="miscellaneous-extensions"></a>Różne rozszerzenia

> [!div class="mx-tableFixed"]
> | **Rozszerzenie** | **Opis** |
> |---|---|
> | [pg\_buffercache](https://www.postgresql.org/docs/9.6/static/pgbuffercache.html) | Udostępnia metody badania, co dzieje się w pamięci podręcznej buforu w czasie rzeczywistym. |
> | [PG\_prewarm](https://www.postgresql.org/docs/9.6/static/pgprewarm.html) | Zapewnia sposób ładowania danych relacji w buforze pamięci podręcznej. |
> | [PG\_stat\_instrukcji](https://www.postgresql.org/docs/9.6/static/pgstatstatements.html) | Umożliwia śledzenie statystyk wykonywania instrukcji SQL wszystkie wykonane przez serwer. (Zobacz poniżej uwagi na to rozszerzenie). |
> | [pgrowlocks](https://www.postgresql.org/docs/9.6/static/pgrowlocks.html) | Umożliwia wyświetlanie informacje dotyczące blokowania na poziomie wiersza. |
> | [pgstattuple](https://www.postgresql.org/docs/9.6/static/pgstattuple.html) | Zapewnia to do wyświetlania statystyki na poziomie spójnej kolekcji. |
> | [postgres\_fdw](https://www.postgresql.org/docs/9.6/static/postgres-fdw.html) | Otoka obcych danych umożliwiają dostęp do danych przechowywanych w zewnętrznych serwerów PostgreSQL. (Zobacz poniżej uwagi na to rozszerzenie).|
> | [hypopg](https://hypopg.readthedocs.io/en/latest/) | Umożliwia tworzenie indeksów hipotetycznych, które nie koszt procesora CPU lub dysk. |
> | [dblink](https://www.postgresql.org/docs/current/dblink.html) | Moduł, który obsługuje połączenia do innych baz danych PostgreSQL z w ramach sesji bazy danych. (Zobacz poniżej uwagi na to rozszerzenie). |


### <a name="postgis-extensions"></a>Rozszerzenia PostGIS

> [!div class="mx-tableFixed"]
> | **Rozszerzenie** | **Opis** |
> |---|---|
> | [PostGIS](https://www.postgis.net/), postgis\_topologii, postgis\_tiger\_geocoder, postgis\_sfcgal | Obiekty przestrzennych i geograficzne database for PostgreSQL. |
> | adres\_standardizer, adres\_standardizer\_danych\_USA | Używany, można przeanalizować adresu na elementy składowe. Używane do obsługi geokodowania adres normalizacji kroku. |
> | [pgrouting](https://pgrouting.org/) | Rozszerza PostGIS / bazy danych geoprzestrzennych PostgreSQL zapewnienie geoprzestrzenne routingu funkcji. |


### <a name="time-series-extensions"></a>Rozszerzenia szeregów czasowych

> [!div class="mx-tableFixed"]
> | **Rozszerzenie** | **Opis** |
> |---|---|
> | [TimescaleDB](https://docs.timescale.com/latest) | Szeregi czasowe bazy danych SQL, która obsługuje automatyczne partycjonowanie szybciej pozyskiwania zapytania. Udostępnia zorientowane na czas funkcje analityczne, optymalizacje i umożliwia skalowanie PostgreSQL dla obciążeń szeregów czasowych. TimescaleDB jest opracowany przez i zastrzeżonym znakiem towarowym [skali czasu, Inc.](https://www.timescale.com/) (Zobacz poniżej uwagi na to rozszerzenie). |


## <a name="pgstatstatements"></a>pg_stat_statements
[Pg\_stat\_rozszerzenia instrukcji](https://www.postgresql.org/docs/9.6/static/pgstatstatements.html) jest wstępnie na każdego serwera Azure Database for postgresql w warstwie zapewnienie oznacza śledzenia statystyk wykonywania instrukcji SQL.
Ustawienie `pg_stat_statements.track`, która kontroluje, jakie instrukcje są uwzględniane przez rozszerzenie, wartość domyślna to `top`, co oznacza wszystkie instrukcje wydane bezpośrednio przez klientów są śledzone. Są dwa inne poziomy śledzenia `none` i `all`. To ustawienie jest konfigurowane jako parametr serwera za pomocą [witryny Azure portal](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-portal) lub [wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-cli).

Istnieje zależność między informacje o wykonaniu zapytania zawiera pg_stat_statements i wpływ na wydajność serwera jako rejestruje każdej instrukcji SQL. Jeśli nie używasz aktywnie rozszerzenia pg_stat_statements, firma Microsoft zaleca ustawienie `pg_stat_statements.track` do `none`. Należy pamiętać, że niektóre innych firm, monitorowania usług mogą polegać na pg_stat_statements dostarczać szczegółowe informacje o wydajności zapytań, więc upewnij się, czy to w przypadku.

## <a name="dblink-and-postgresfdw"></a>dblink i postgres_fdw
dblink i postgres_fdw umożliwiają połączenie się z jednego serwera PostgreSQL do innego lub do innej bazy danych na tym samym serwerze. Serwer odbierający musi zezwalać na połączenia z serwerem wysyłania za pośrednictwem swojej zapory. Korzystając z tych rozszerzeń do połączenia między — Azure Database for postgresql — serwery, można to zrobić przez ustawienie "Zezwalaj na dostęp do usług platformy Azure" na wartość ON. Jest to również potrzebne, jeśli chcesz użyć rozszerzeń w pętli do tego samego serwera. Ustawienie "Zezwalaj na dostęp do usług platformy Azure", można znaleźć w witrynie Azure portal strona serwera Postgres, w obszarze zabezpieczeń połączenia. Włączenie opcji "Zezwalaj na dostęp do usług platformy Azure" na umieszczenie wszystkich adresów IP usługi Azure.

Obecnie połączenia wychodzące z usługi Azure Database for PostgreSQL nie są obsługiwane, z wyjątkiem połączeń z innymi — Azure Database for postgresql w warstwie serwerów.

## <a name="timescaledb"></a>TimescaleDB
TimescaleDB jest bazy danych szeregów czasowych, który jest spakowany jako rozszerzenie database for PostgreSQL. TimescaleDB udostępnia zorientowane na czas funkcje analityczne, optymalizacje i skaluje Postgres dla obciążeń szeregów czasowych.

[Dowiedz się więcej o TimescaleDB](https://docs.timescale.com/latest), znakiem towarowym [skali czasu, Inc.](https://www.timescale.com/)

### <a name="installing-timescaledb"></a>Instalowanie TimescaleDB
Do zainstalowania TimescaleDB, musisz uwzględnić go w udostępnionej biblioteki wstępnego ładowania serwera. Zmiana do biblioteki udostępnione wstępnego ładowania Postgres firmy wymaga **ponownego rozruchu serwera** zaczęły obowiązywać.

> [!NOTE]
> TimescaleDB można włączyć dla usługi Azure Database for PostgreSQL w wersji 9.6 i 10

Za pomocą [witryny Azure portal](https://portal.azure.com/):

1. Wybierz serwer usługi Azure Database for PostgreSQL.

2. Na pasku bocznym wybierz **parametrów serwera**.

3. Wyszukaj `shared_preload_libraries` parametru.

4. Skopiuj i wklej następujący kod jako wartość pozycji `shared_preload_libraries`
   ```
   timescaledb
   ```

5. Wybierz **Zapisz** Aby zachować zmiany. Otrzymasz powiadomienie po zapisaniu zmian. 

6. Po powiadomieniu **ponowne uruchomienie** serwera, aby zastosować te zmiany. Aby dowiedzieć się, jak uruchomić ponownie serwer, zobacz [ponownego uruchomienia usługi Azure Database for postgresql w warstwie serwera](howto-restart-server-portal.md).


TimescaleDB można teraz włączyć w bazie danych Postgres. Połączenia z bazą danych i wydać następujące polecenie:
```sql
CREATE EXTENSION IF NOT EXISTS timescaledb CASCADE;
```
> [!TIP]
> Jeśli zostanie wyświetlony błąd, upewnij się, że [ponownego uruchomienia serwera](howto-restart-server-portal.md) po zapisaniu shared_preload_libraries. 

Teraz możesz tworzyć TimescaleDB hypertable [od podstaw](https://docs.timescale.com/getting-started/creating-hypertables) lub migrowania [istniejące dane szeregów czasowych w PostgreSQL](https://docs.timescale.com/getting-started/migrating-data).


## <a name="next-steps"></a>Kolejne kroki
Jeśli nie widzisz rozszerzenia, które chcesz użyć, Daj nam znać. Zagłosuj na istniejących żądań lub Utwórz nową opinię i żądań w naszym [forum opinii klientów dotyczących](https://feedback.azure.com/forums/597976-azure-database-for-postgresql).
