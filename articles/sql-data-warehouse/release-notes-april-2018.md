---
title: Informacje o wersji magazynu danych Azure SQL, kwietnia 2018 | Dokumentacja firmy Microsoft
description: Informacje o wersji dla usługi Azure SQL Data Warehouse.
services: sql-data-warehouse
author: twounder
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 05/28/2018
ms.author: twounder
ms.reviewer: twounder
ms.openlocfilehash: 49ffc3ddfd586964ae8a9688aeb48fffdd327b45
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34738937"
---
# <a name="whats-new-in-azure-sql-data-warehouse-april-2018"></a>Nowości w systemie Azure SQL Data Warehouse (kwiecień 2018)
Usługa Azure SQL Data Warehouse stale odbiera ulepszenia. W tym artykule opisano nowe funkcje i zmiany, które zostały wprowadzone w 2018 kwietnia.

## <a name="features"></a>Funkcje

### <a name="ability-to-truncate-a-partition-before-a-switch"></a>Możliwość obcięcia partycji przed przełącznik
Klienci często używane przełączania jako wzorzec partycji do ładowania danych z jednej tabeli do innego, zmieniając metadanych tabeli za pomocą `ALTER TABLE SourceTable SWITCH PARTITION X TO TargetTable PARTITION X` składni. Usługi SQL Data Warehouse nie obsługuje przełączenie partycji, gdy partycja docelowa zawiera dane. Jeśli partycja docelowa zawiera już dane, klient musi obcięcia partycja docelowa, a następnie wykonaj przełącznika.

Magazyn danych SQL obsługuje teraz tej operacji w jednej instrukcji T-SQL.

```sql
ALTER TABLE SourceTable 
    SWITCH PARTITION X TO TargetTable PARTITION X
    WITH (TRUNCATE_TARGET_PARTITION = ON)
```
Aby uzyskać więcej informacji, zobacz [instrukcji ALTER TABLE](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql) artykułu.

### <a name="improved-query-compilation-performance"></a>Wydajność kompilacji ulepszone zapytań
Usługa SQL Data Warehouse wprowadzono zmian do zwiększenia kroku kompilacji zapytania zapytań rozproszonych. Te zmiany skrócić czas kompilacji zapytania do **10 x** zmniejszenie ogólnego zapytania wykonywania środowisk uruchomieniowych. Te zmiany są bardziej widoczne w hurtowni danych z dużą liczbą obiektów (tabel, funkcji, widoki, procedury).

## <a name="behavior-changes"></a>Zmiany sposobu działania

### <a name="dbcc-commands-do-not-consume-concurrency-slots"></a>Polecenia DBCC nie korzystać z miejsc współbieżności
Magazyn danych SQL obsługuje podzbiór T-SQL [polecenia DBCC](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-transact-sql) takich jak [DBCC DROPCLEANBUFFERS](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-dropcleanbuffers-transact-sql). Wcześniej, będzie używać tych poleceń [miejsca współbieżności](https://docs.microsoft.com/azure/sql-data-warehouse/resource-classes-for-workload-management#concurrency-slots) zmniejszenie liczby użytkownika obciążeń/kwerendy, które mogą być wykonywane. `DBCC` Polecenia teraz są uruchamiane w kolejce lokalnego, który zużywa gnieździe zasobów poprawy ogólną wydajność wykonywania zapytań.

### <a name="updated-error-message-for-excessive-literals"></a>Zaktualizowano komunikat dla literałów nadmiernego
Wcześniej, to SQL Data Warehouse *przybliżonej* liczba podczas zapytania zawiera zbyt wiele literały.
```
Msg 100086
Cannot have more than 20,000 literals in the query. The query contains [n] literals.
```

Komunikat o błędzie została zaktualizowana, aby wskazać tylko osiągnięto limit literałów.
```
Msg 100086
The number of literals in the query is beyond the limit. Please rewrite your query.
```

Aby uzyskać więcej informacji, zobacz [zapytania](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-service-capacity-limits#queries) sekcji [limity pojemności](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-service-capacity-limits) artykułu, aby uzyskać więcej informacji na granicach.

### <a name="removed-the-syspdwdatabasemappings-view"></a>Usunięte SYS. Widok PDW_DATABASE_MAPPINGS
To `sys.pdw_database_mappings` widok jest nieużywane w usłudze SQL Data Warehouse. Wybierz tego widoku czy wcześniej, nie zwracała żadnych wyników. Widok został usunięty. 