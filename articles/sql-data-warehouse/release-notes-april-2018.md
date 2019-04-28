---
title: Usługa Azure SQL Data Warehouse — informacje o wersji z kwietnia 2018 r | Dokumentacja firmy Microsoft
description: Informacje o wersji dla usługi Azure SQL Data Warehouse.
services: sql-data-warehouse
author: twounder
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 07/23/2018
ms.author: twounder
ms.reviewer: twounder
ms.openlocfilehash: a9fae37acd653b05502737756baf463188ef631d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61475018"
---
# <a name="whats-new-in-azure-sql-data-warehouse-april-2018"></a>Co nowego w usłudze Azure SQL Data Warehouse? Kwiecień 2018 r.
Usługa Azure SQL Data Warehouse odbiera ulepszenia stale. W tym artykule opisano nowe funkcje i zmiany, które zostały wprowadzone w kwietniu 2018.

## <a name="ability-to-truncate-a-partition-before-a-switch"></a>Możliwość obciąć partycję przed wykonaniem Switch
Klienci często używają przełączania jako wzorzec partycji do ładowania danych z jednej tabeli do innej, zmieniając metadanych tabeli za pośrednictwem `ALTER TABLE SourceTable SWITCH PARTITION X TO TargetTable PARTITION X` składni. Usługa SQL Data Warehouse nie obsługuje przełączanie partycji, gdy partycja docelowa zawiera dane. Jeśli partycja docelowa zawiera już dane, klient musi obciąć partycji docelowej, a następnie wykonaj przełącznika.

Usługa SQL Data Warehouse obsługuje teraz tej operacji w pojedynczą instrukcję języka T-SQL.

```sql
ALTER TABLE SourceTable 
    SWITCH PARTITION X TO TargetTable PARTITION X
    WITH (TRUNCATE_TARGET_PARTITION = ON)
```
Aby uzyskać więcej informacji, zobacz [instrukcji ALTER TABLE](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql) artykułu.

## <a name="improved-query-compilation-performance"></a>Wydajność kompilacji ulepszone zapytań
Usługa SQL Data Warehouse wprowadza zestaw zmian do zwiększenia kroku kompilacji zapytań rozproszonych zapytań. Te zmiany poprawić czasy kompilacji zapytania do **10 x** ogólne zmniejszenie zapytania wykonywania środowisk uruchomieniowych. Te zmiany są bardziej widoczne na magazyny danych z dużą liczbą obiektów (tabele, funkcje, widoki, procedury).

## <a name="dbcc-commands-do-not-consume-concurrency-slots-behavior-change"></a>Polecenia DBCC nie używają gniazd współbieżności (Zmiana zachowania)
Usługa SQL Data Warehouse obsługuje podzbiór języka T-SQL [polecenia DBCC](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-transact-sql) takich jak [DBCC DROPCLEANBUFFERS](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-dropcleanbuffers-transact-sql). Wcześniej, będzie używać tych poleceń [miejsca współbieżności](https://docs.microsoft.com/azure/sql-data-warehouse/resource-classes-for-workload-management#concurrency-slots) zmniejszenie liczby obciążeń/zapytania użytkowników, które mogą być wykonywane. `DBCC` Polecenia są teraz uruchamiane w kolejce lokalne, które nie zużywają zasobów miejsca poprawę ogólnej wydajności wykonywania zapytań.

## <a name="updated-error-message-for-excessive-literals-behavior-change"></a>Komunikat o błędzie zaktualizowane nadmierne literałów (Zmiana zachowania)
Wcześniej usługa SQL Data Warehouse zawierałoby *przybliżony* count, gdy zapytanie zawierało literały zbyt wiele.
```
Msg 100086
Cannot have more than 20,000 literals in the query. The query contains [n] literals.
```

Komunikat o błędzie został zaktualizowany do tylko wskazuje, że osiągnięto limit literału.
```
Msg 100086
The number of literals in the query is beyond the limit. Please rewrite your query.
```

Aby uzyskać więcej informacji, zobacz [zapytania](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-service-capacity-limits#queries) części [limitów pojemności](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-service-capacity-limits) artykuł, aby uzyskać więcej informacji na temat maksymalnych limitów.

## <a name="removed-the-syspdwdatabasemappings-view-behavior-change"></a>Usunięte SYS. Widok PDW_DATABASE_MAPPINGS (Zmiana zachowania)
To `sys.pdw_database_mappings` widok jest nieużywana w usłudze SQL Data Warehouse. Wcześniej wybierz ten widok zwróci żadnych wyników. Widok został usunięty. 

## <a name="next-steps"></a>Kolejne kroki
Po użytkownik podstawową wiedzę na temat usługi SQL Data Warehouse, Dowiedz się, jak szybko [utworzyć SQL Data Warehouse][create a SQL Data Warehouse]. Jeśli dopiero zaczynasz korzystać z platformy Azure, [słownik platformy Azure][Azure glossary] może pomóc Ci zaznajomić się z nową terminologią. Możesz też zwrócić uwagę na inne zasoby dotyczące usługi SQL Data Warehouse.  

* [Historie sukcesu klientów]
* [Blogi]
* [Żądania funkcji]
* [Filmy wideo]
* [Blogi zespołu doradczego klientów]
* [Forum Stack Overflow]
* [Twitter]


[Blogi]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Blogi zespołu doradczego klientów]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Historie sukcesu klientów]: https://azure.microsoft.com/case-studies/?service=sql-data-warehouse
[Żądania funkcji]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Forum Stack Overflow]: https://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Filmy wideo]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[create a SQL Data Warehouse]: ./create-data-warehouse-portal.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md
