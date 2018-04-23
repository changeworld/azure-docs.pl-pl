---
title: Używanie rozszerzeń PostgreSQL w bazie danych Azure dla PostgreSQL
description: Zawiera opis możliwości rozszerzania funkcji bazy danych dla PostgreSQL przy użyciu rozszerzeń w bazie danych Azure.
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 0b4150fcd7d32c823173c3e2676e226634346a2b
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/28/2018
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql"></a>Rozszerzenia PostgreSQL w bazie danych PostgreSQL Azure
PostgreSQL pozwala, aby rozszerzyć funkcjonalność programu bazy danych przy użyciu rozszerzeń. Rozszerzenia umożliwiają grupowanie wielu powiązanych obiektów SQL razem w jednym pakiecie, który może być załadowany lub usunięte z bazy danych za pomocą jednego polecenia. Po ładowany w bazie danych, rozszerzenia może działać tak jak wbudowane funkcje. Aby uzyskać więcej informacji o rozszerzeniach PostgreSQL, zobacz [pakowania obiektów pokrewnych do rozszerzenia](https://www.postgresql.org/docs/9.6/static/extend-extensions.html).

## <a name="how-to-use-postgresql-extensions"></a>Jak użyć rozszerzenia PostgreSQL
Muszą być zainstalowane rozszerzenia PostgreSQL w bazie danych, przed ich użyciem. Aby zainstalować wybrane rozszerzenie, należy uruchomić [Tworzenie rozszerzenia](https://www.postgresql.org/docs/9.6/static/sql-createextension.html) polecenia narzędzia psql załadować spakowanych obiektów do bazy danych.

Bazy danych platformy Azure dla PostgreSQL obecnie obsługuje klucza rozszerzeń wymienione poniżej. Rozszerzenia innych niż wymienione są nieobsługiwane. Nie można utworzyć własne rozszerzenie z bazą danych Azure, PostgreSQL usługi.

## <a name="extensions-supported-by-azure-database-for-postgresql"></a>Rozszerzenia dla PostgreSQL przez bazę danych Azure
W poniższych tabelach przedstawiono standardowe rozszerzenia PostgreSQL, które są obecnie obsługiwane przez bazę danych Azure dla PostgreSQL. Te informacje jest również dostępna, badając `pg\_available\_extensions`.

### <a name="data-types-extensions"></a>Rozszerzenia typów danych

> [!div class="mx-tableFixed"]
| **Rozszerzenia** | **Opis** |
|---|---|
| [chkpass](https://www.postgresql.org/docs/9.6/static/chkpass.html) | Zawiera typ danych dla hasła szyfrowane automatycznie. |
| [citext](https://www.postgresql.org/docs/9.6/static/citext.html) | Miejsce na wpisanie ciągu znaków bez uwzględniania wielkości liter. |
| [cube](https://www.postgresql.org/docs/9.6/static/cube.html) | Zawiera typ danych wielowymiarowych modułów. |
| [hstore](https://www.postgresql.org/docs/9.6/static/hstore.html) | Zawiera typ danych do przechowywania zestawy par klucz/wartość. |
| [isn](https://www.postgresql.org/docs/9.6/static/isn.html) | Udostępnia typy danych dla produktu międzynarodowych numerów standardów. |
| [ltree](https://www.postgresql.org/docs/9.6/static/ltree.html) | Zawiera typ danych dla hierarchicznych struktur drzewa. |

### <a name="functions-extensions"></a>Funkcje rozszerzeń

> [!div class="mx-tableFixed"]
| **Rozszerzenia** | **Opis** |
|---|---|
| [earthdistance](https://www.postgresql.org/docs/9.6/static/earthdistance.html) | Zapewnia sposób obliczania koła wielkiego odległości na powierzchni ziemi. |
| [fuzzystrmatch](https://www.postgresql.org/docs/9.6/static/fuzzystrmatch.html) | Zawiera kilka funkcji do określenia podobieństwa i odległość między ciągami. |
| [intarray](https://www.postgresql.org/docs/9.6/static/intarray.html) | Udostępnia funkcje i operatory do manipulowania bez null tablice liczb całkowitych. |
| [pgcrypto](https://www.postgresql.org/docs/9.6/static/pgcrypto.html) | Udostępnia funkcje kryptograficzne. |
| [PG\_partman](https://pgxn.org/dist/pg_partman/doc/pg_partman.html) | Zarządza partycjonowane tabele według czasu lub identyfikator. |
| [pg\_trgm](https://www.postgresql.org/docs/9.6/static/pgtrgm.html) | Udostępnia funkcje i operatory określania podobieństwa alfanumeryczne w oparciu o dopasowanie trigram. |
| [tablefunc](https://www.postgresql.org/docs/9.6/static/tablefunc.html) | Udostępnia funkcje, które manipulowania całe tabele, w tym raporcie krzyżowym. |
| [uuid-ossp](https://www.postgresql.org/docs/9.6/static/uuid-ossp.html) | Generuje powszechnie unikatowe identyfikatory (UUID). |

### <a name="full-text-search-extensions"></a>Wyszukiwanie pełnotekstowe rozszerzeń

> [!div class="mx-tableFixed"]
| **Rozszerzenia** | **Opis** |
|---|---|
| [dict\_int](https://www.postgresql.org/docs/9.6/static/dict-int.html) | Udostępnia szablonu słownika wyszukiwania tekstowego liczb całkowitych. |
| [unaccent](https://www.postgresql.org/docs/9.6/static/unaccent.html) | Słownik wyszukiwania tekstu, która usuwa akcentów (znaków diakrytycznych) z lexemes. |

### <a name="index-types-extensions"></a>Rozszerzenia typu indeksu

> [!div class="mx-tableFixed"]
| **Rozszerzenia** | **Opis** |
|---|---|
| [btree\_gin](https://www.postgresql.org/docs/9.6/static/btree-gin.html) | Udostępnia przykładowy GIN operator klasy, które implementuje B-drzewa, takich jak zachowanie dla określonych typów danych. |
| [btree\_gist](https://www.postgresql.org/docs/9.6/static/btree-gist.html) | Udostępnia klasy operatora indeksu GiST, które implementuje B-drzewa. |

### <a name="language-extensions"></a>Rozszerzenia językowe

> [!div class="mx-tableFixed"]
| **Rozszerzenia** | **Opis** |
|---|---|
| [plpgsql](https://www.postgresql.org/docs/9.6/static/plpgsql.html) | PL/pgSQL obciążana język procedurach. |

### <a name="miscellaneous-extensions"></a>Różne rozszerzenia

> [!div class="mx-tableFixed"]
| **Rozszerzenia** | **Opis** |
|---|---|
| [pg\_buffercache](https://www.postgresql.org/docs/9.6/static/pgbuffercache.html) | Zapewnia to badania, co dzieje się w pamięci podręcznej udostępnionego buforu w czasie rzeczywistym. |
| [PG\_prewarm](https://www.postgresql.org/docs/9.6/static/pgprewarm.html) | Umożliwia ładowanie danych relacji do buforu pamięci podręcznej. |
| [PG\_stat\_— instrukcje](https://www.postgresql.org/docs/9.6/static/pgstatstatements.html) | Zapewnia możliwość śledzenia statystyki wykonania wszystkich instrukcji SQL wykonane przez serwer. |
| [pgrowlocks](https://www.postgresql.org/docs/9.6/static/pgrowlocks.html) | Umożliwia wyświetlanie informacji blokowania na poziomie wiersza. |
| [pgstattuple](https://www.postgresql.org/docs/9.6/static/pgstattuple.html) | Umożliwia wyświetlanie statystyki na poziomie spójnej kolekcji. |
| [postgres\_fdw](https://www.postgresql.org/docs/9.6/static/postgres-fdw.html) | Otoka obcy danych umożliwiają dostęp do danych przechowywanych w zewnętrznych serwerów PostgreSQL. |

### <a name="postgis-extensions"></a>Rozszerzenia PostGIS

> [!div class="mx-tableFixed"]
| **Rozszerzenia** | **Opis** |
|---|---|
| [PostGIS](http://www.postgis.net/), postgis\_topologii, postgis\_tiger\_geocoder, postgis\_sfcgal | Przestrzenne i geograficzne obiekty PostgreSQL. |
| adres\_standardizer, adres\_standardizer\_danych\_nam | Używany do analizowania adresu do elementów składowych. Używany do obsługi geokodowanie adres normalizacji kroku. |
| [pgrouting](http://pgrouting.org/) | Rozszerza PostGIS / funkcje routingu bazą danych PostgreSQL dane geograficzne zapewnienie dane geograficzne. |

## <a name="next-steps"></a>Kolejne kroki
Jeśli nie widzisz rozszerzenie, które chcesz użyć, Daj nam znać. Zagłosuj na istniejących żądań lub utworzyć nowy opinii i żądań w naszym [forum opinii klientów](https://feedback.azure.com/forums/597976-azure-database-for-postgresql).
