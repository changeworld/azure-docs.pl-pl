---
title: Optymalizowanie zbiorcze operacje wstawiania w usłudze Azure Database for PostgreSQL — pojedynczy serwer
description: W tym artykule opisano, jak można optymalizować operacji wstawiania zbiorczego w usłudze Azure Database for PostgreSQL — pojedynczy serwer.
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: c1ae29f7c498a79af09aaaf6d7aeae29561aa500
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65067043"
---
# <a name="optimize-bulk-inserts-and-use-transient-data-on-an-azure-database-for-postgresql---single-server"></a>Optymalizuj zbiorcze operacje wstawiania i używać danych przejściowych w usłudze Azure Database for PostgreSQL — pojedynczy serwer 
W tym artykule opisano, jak zoptymalizować operacji wstawiania zbiorczego i używać danych przejściowych w usłudze Azure Database dla serwera PostgreSQL.

## <a name="use-unlogged-tables"></a>Korzystanie z tabel niezarejestrowanych
Jeśli masz obciążenie operacje pociągające danych przejściowych lub dużych zestawów danych, wstawianie zbiorcze, należy rozważyć użycie tabel niezarejestrowanych.

Tabele niezarejestrowanych jest funkcją PostgreSQL, która można skutecznie zoptymalizować zbiorcze operacje wstawiania. PostgreSQL używa zapisu z wyprzedzeniem rejestrowania (WAL). Udostępnia niepodzielność i odporność, domyślnie. Niepodzielności, spójności, izolacji i trwałości tworzą właściwości ACID. 

Wstawianie do tabeli niezarejestrowanych oznacza że PostgreSQL wstawia bez pisania na transakcji dziennika, która sama jest operacji We/Wy. Co w efekcie te tabele są znacznie szybsze niż zwykłe tabel.

Aby utworzyć tabelę niezarejestrowanych, należy użyć następujących opcji:
- Utwórz nową tabelę niezarejestrowanych przy użyciu składni `CREATE UNLOGGED TABLE <tableName>`.
- Konwertuj istniejącą rejestrowane tabeli niezarejestrowanych tabeli przy użyciu składni `ALTER TABLE <tableName> SET UNLOGGED`.  

Aby cofnąć ten proces, należy użyć składni `ALTER TABLE <tableName> SET LOGGED`.

## <a name="unlogged-table-tradeoff"></a>Kosztem niezarejestrowanych tabeli
Niezarejestrowanych tabele nie są bezpieczne pod względem awarii. Tabela niezarejestrowanych automatycznie został obcięty po awarii lub zastrzeżeniem nieczyste zamknięcie. Zawartość niezarejestrowanych tabeli nie są replikowane serwery w stanie wstrzymania. Żadnych indeksów utworzona na niezarejestrowanych tabeli są także automatycznie niezarejestrowanych. Insert — po zakończeniu operacji, przekonwertuj tabelę rejestrowane tak, aby insert jest trwały.

Niektórych obciążeń klientów wystąpić około 15 do 20 procent zwiększenie wydajności stosowania niezarejestrowanych tabel.

## <a name="next-steps"></a>Kolejne kroki
Przejrzyj swoje obciążenia do użycia dla danych przejściowych i dużych zbiorcze operacje wstawiania. Znajduje się poniższej dokumentacji PostgreSQL:
 
- [Tworzenie poleceń tabeli SQL](https://www.postgresql.org/docs/current/static/sql-createtable.html)
