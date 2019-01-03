---
title: Optymalizacja operacji wstawiania zbiorczego w usłudze Azure Database dla serwera PostgreSQL
description: W tym artykule opisano, jak można optymalizować operacji wstawiania zbiorczego w usłudze Azure Database dla serwera PostgreSQL.
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/22/2018
ms.openlocfilehash: 9d2bfcddc649e4fff68bdba49df0945e88067036
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/17/2018
ms.locfileid: "53545240"
---
# <a name="optimizing-bulk-inserts-and-use-of-transient-data-on-azure-database-for-postgresql-server"></a>Optymalizacja zbiorcze operacje wstawiania danych i korzystania z przejściowych w usłudze Azure Database dla serwera PostgreSQL 
W tym artykule opisano, jak można zoptymalizować pod kątem operacji wstawiania zbiorczego i umożliwia korzystanie z danych przejściowych w usłudze Azure Database dla serwera PostgreSQL.

## <a name="using-unlogged-tables"></a>Za pomocą niezarejestrowanych tabel
Dla klientów, którzy mają obciążenie operacje pociągające danych przejściowych lub dużych zestawów danych, wstawianie zbiorcze należy rozważyć użycie tabel niezarejestrowanych.

Tabele niezarejestrowanych jest funkcją PostgreSQL, która można skutecznie zoptymalizować zbiorcze operacje wstawiania. PostgreSQL używa zapisu z wyprzedzeniem rejestrowania (WAL), który zapewnia niepodzielność i odporność dwie właściwości ACID domyślnie. Wstawianie do tabeli niezarejestrowanych średniej PostgreSQL będzie jak wstawia bez konieczności pisania w dzienniku transakcji, która sama jest operacji We/Wy, co znacznie szybciej niż zwykłe tabele te tabele.

Poniżej przedstawiono opcje tworzenia niezarejestrowanych tabeli:
- Utwórz nową tabelę niezarejestrowanych przy użyciu składni: `CREATE UNLOGGED TABLE <tableName>`
- Konwertuj istniejącą rejestrowane tabeli do tabeli niezarejestrowanych przy użyciu składni: `ALTER <tableName> SET UNLOGGED`.  Można tego cofnąć przy użyciu składni: `ALTER <tableName> SET LOGGED`

## <a name="unlogged-table-tradeoff"></a>Kosztem niezarejestrowanych tabeli
Niezarejestrowanych tabele nie są bezpieczne pod względem awarii. Tabela niezarejestrowanych automatycznie został obcięty po awarii lub zastrzeżeniem nieczyste zamknięcie. Zawartość tabeli niezarejestrowanych również nie są replikowane do serwery w stanie wstrzymania. Żadnych indeksów utworzona na niezarejestrowanych tabeli są także automatycznie niezarejestrowanych.  Po zakończeniu operacji wstawiania może przekonwertować tabelę, aby rejestrowany, więc insert jest trwały.

Jednak w niektórych obciążeń klientów wystąpił około 15 – 20 procent zwiększenie wydajności podczas korzystania z niezarejestrowanych tabel.

## <a name="next-steps"></a>Kolejne kroki
Przejrzyj swoje obciążenia do użycia dla danych przejściowych i dużych zbiorcze operacje wstawiania.  

Zapoznaj się z następującą dokumentacją PostgreSQL - [Tworzenie tabeli poleceń SQL](https://www.postgresql.org/docs/current/static/sql-createtable.html)