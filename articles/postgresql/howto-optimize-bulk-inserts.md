---
title: Optymalizuj wstawia zbiorcze — usługa Azure Database for PostgreSQL — single server
description: W tym artykule opisano, jak zoptymalizować operacje wstawiania zbiorczego w usłudze Azure Database dla postgreSQL — pojedynczy serwer.
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 4c4bac16917be0064ebb111328753d378d462a2a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74770139"
---
# <a name="optimize-bulk-inserts-and-use-transient-data-on-an-azure-database-for-postgresql---single-server"></a>Optymalizuj wstawia zbiorcze i używaj danych przejściowych w bazie danych azure dla postgreSQL — pojedynczy serwer 
W tym artykule opisano, jak zoptymalizować operacje wstawiania zbiorczego i używać danych przejściowych na serwerze Usługi Azure Database dla postgreSql.

## <a name="use-unlogged-tables"></a>Używanie niezarejestrowanych tabel
Jeśli masz operacje obciążenia, które obejmują dane przejściowe lub które wstawiają duże zestawy danych zbiorczo, należy rozważyć użycie niezarejestrowanych tabel.

Niezałaszane tabele to funkcja PostgreSQL, która może być skutecznie używana do optymalizacji wstawiaków zbiorczych. PostgreSQL używa rejestrowania zapisu z wyprzedzeniem (WAL). Zapewnia niepodzielność i trwałość, domyślnie. Niepodzielność, konsystencja, izolacja i trwałość tworzą właściwości ACID. 

Wstawienie do nielogowanego tabeli oznacza, że PostgreSQL wstawia bez zapisu do dziennika transakcji, która sama w sobie jest operacją we/wy. W rezultacie tabele te są znacznie szybsze niż zwykłe tabele.

Aby utworzyć niezalogowany stół, użyj następujących opcji:
- Utwórz nową nielogowany stół przy `CREATE UNLOGGED TABLE <tableName>`użyciu składni .
- Konwertuj istniejącą zarejestrowaną tabelę na niezalogowane tabele przy użyciu składni `ALTER TABLE <tableName> SET UNLOGGED`.  

Aby odwrócić proces, użyj `ALTER TABLE <tableName> SET LOGGED`składni .

## <a name="unlogged-table-tradeoff"></a>Niezalogowany kompromis tabeli
Niezałaszane tabele nie są bezpieczne dla awarii. Niezalogowana tabela jest automatycznie obcinana po awarii lub podlega nieczystemu zamknięciu. Zawartość niezalogowanej tabeli również nie są replikowane do serwerów wstrzymania. Wszystkie indeksy utworzone w niezalogowane tabeli są automatycznie nieobejmowane, jak również. Po zakończeniu operacji wstawiania przekonwertuj tabelę na zarejestrowaną, tak aby płytka była trwała.

Niektóre obciążenia klientów doświadczyły około 15 procent do 20 procent poprawy wydajności, gdy nieobsłane tabele były używane.

## <a name="next-steps"></a>Następne kroki
Przejrzyj obciążenie pod kątem użycia danych przejściowych i dużych wstawiaków zbiorczych. Zobacz następującą dokumentację PostgreSQL:
 
- [Tworzenie poleceń SQL tabeli](https://www.postgresql.org/docs/current/static/sql-createtable.html)
