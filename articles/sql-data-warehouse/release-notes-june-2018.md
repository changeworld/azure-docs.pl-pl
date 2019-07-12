---
title: Usługa Azure SQL Data Warehouse — informacje o wersji czerwca 2018 r. | Dokumentacja firmy Microsoft
description: Informacje o wersji dla usługi Azure SQL Data Warehouse.
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: ''
ms.date: 07/23/2018
ms.author: anjangsh
ms.reviewer: jrasnick
ms.openlocfilehash: 4348a634fd5b2b33f36d8e79f28caf659b82ccf4
ms.sourcegitcommit: c0419208061b2b5579f6e16f78d9d45513bb7bbc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/08/2019
ms.locfileid: "67626149"
---
# <a name="whats-new-in-azure-sql-data-warehouse-june-2018"></a>Co nowego w usłudze Azure SQL Data Warehouse? Czerwiec 2018 r.
Usługa Azure SQL Data Warehouse odbiera ulepszenia stale. W tym artykule opisano nowe funkcje i zmiany, które zostały wprowadzone w czerwca 2018 r. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="user-defined-restore-points"></a>Punkty przywracania zdefiniowane przez użytkownika
Usługa SQL Data Warehouse automatycznie tworzy migawki magazynu danych, co 8 godzin, gwarantując, że cel punktu odzyskiwania ośmiu godzin (RPO). Chociaż to automatyczne migawki łatwość zarządzaniem uruchamiania usługi data warehouse ma trzeba wykonać migawki w czasie krytycznych, zgodnie z potrzebami firmy. Na przykład robienie bezpośrednio poprzedzający obciążenia duża ilość danych lub wdrażania nowych skryptów do magazynu danych, aby włączyć punkt przywracania, bezpośrednio przed wykonaniem operacji. 

Usługa SQL Data Warehouse obsługuje teraz [punkty przywracania na zdefiniowanych przez użytkownika](https://azure.microsoft.com/blog/quick-recovery-time-with-sql-data-warehouse-using-user-defined-restore-points/) za pośrednictwem [New AzSqlDatabaseRestorePoint](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabaserestorepoint) polecenia cmdlet.

```powershell
New-AzSqlDatabaseRestorePoint
    -ResourceGroupName $ResourceGroupName
    -ServerName $ServerName
    -DatabaseName $DatabaseName
    -RestorePointLabel $RestorePointName
```

## <a name="column-level-security"></a>Zabezpieczenia na poziomie kolumny
Zarządzanie dostępem do danych i zabezpieczeń w magazynie danych ma kluczowe znaczenie, do tworzenia relacji zaufania z klientami i partnerami. Usługa SQL Data Warehouse [obsługuje teraz zabezpieczenia na poziomie kolumny (CLS)](https://azure.microsoft.com/blog/column-level-security-is-now-supported-in-azure-sql-data-warehouse/) , pozwala na dostosowanie uprawnień do wyświetlania danych poufnych, ograniczając dostęp użytkowników do określonych kolumn w tabelach bez konieczności zmiany projektu magazynu danych.

Zgodny ze specyfikacją pozwala na kontrolowanie dostępu do kolumny tabeli na podstawie kontekstu wykonania przez użytkownika lub członkostwa w grupie przy użyciu standardu [GRANT](https://docs.microsoft.com/azure/sql-data-warehouse/column-level-security) instrukcję języka T-SQL. Logika ograniczeń dostępu znajduje się w warstwie bazy danych sam, a nie od danych w innej aplikacji, co upraszcza ogólnej implementacji zabezpieczeń.


```sql
CREATE USER Nurse WITHOUT LOGIN;   
GRANT SELECT ON Membership(MemberID, FirstName, LastName, Phone, Email) TO Nurse;   
EXECUTE AS USER = 'Nurse';
SELECT * FROM Membership;

Msg 230, Level 14, State 1, Line 12 
The SELECT permission was denied on the column 'SSN' of the object 'Membership', database 'CLS_TestDW', schema 'dbo'.
```

## <a name="objectschemaname"></a>OBJECT_SCHEMA_NAME
[OBJECT_SCHEMA_NAME()](https://docs.microsoft.com/sql/t-sql/functions/object-schema-name-transact-sql) funkcja zwraca nazwę schematu bazy danych dla obiektów o zakresie schematu. Ta funkcja stał się typowe w narzędziach ETL obiektu podczas sprawdzania poprawności schematu. 

```sql
SELECT
    OBJECT_SCHEMA_NAME([object_id]) [table_schema]
    , [name]                        [table_name]
FROM
    [sys].[tables];
```

**Przykładowe wyniki**
```
table_schema    | table_name
-----------------------------
dbo               customer
dbo               lineitem
dbo               nation
dbo               orders
```

## <a name="support-for-the-systimezoneinfo-view"></a>Obsługa widoku sys.time_zone_info
[Sys.time_zone_info](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-time-zone-info-transact-sql) widoku zwraca informacje o obsługiwanych stref czasowych w usłudze Azure SQL Data Warehouse.

```sql
SELECT * FROM [sys].[time_zone_info];
```

**Przykładowe wyniki**
```
name                            | current_utc_offset | is_currently_dst
-------------------------------------------------------------------------
Pacific Standard Time (Mexico)    -07:00               1
US Mountain Standard Time         -07:00               0
Mountain Standard Time (Mexico)   -06:00               1
Central Standard Time             -05:00               1
```

## <a name="auto-stats-operations-appear-in-sysdmpdwexecrequests-behavior-change"></a>Automatyczne statystyki operacje są wyświetlane w sys.dm_pdw_exec_requests (Zmiana zachowania)

Wraz z wprowadzeniem [automatycznie Create Statistics](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics#automatic-creation-of-statistic), Azure SQL Data Warehouse spowoduje wygenerowanie statystyki, aby zoptymalizować wykonywania zapytania. Wersji czerwca 2018 r. dodano możliwość monitorowania, gdy statystyki są generowane automatycznie przez dodanie rekordu do [sys.dm_pdw_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) wyświetlić zawsze wtedy, gdy dowolne [CREATE STATISTICS](https://docs.microsoft.com/sql/t-sql/statements/create-statistics-transact-sql) operacja jest wykonywana.

```sql
SELECT
    [start_time]
    , [end_time]
    , [command]
FROM
    [sys].[dm_pdw_exec_requests]
WHERE
    [command] LIKE 'CREATE STATISTICS _WA_Sys%';
```
**Przykładowe wyniki**
```
start_time                | end_time                | command
------------------------------------------------------------------------------------------------------------------------------
2018-06-06 19:06:26.173    2018-06-06 19:06:26.173    CREATE STATISTICS _WA_Sys_00000001_63D998CC ON dbo.LineItem(l_orderkey);
```

## <a name="next-steps"></a>Kolejne kroki
Po użytkownik podstawową wiedzę na temat usługi SQL Data Warehouse, Dowiedz się, jak szybko [utworzyć SQL Data Warehouse][create a SQL Data Warehouse] . If you are new to Azure, you may find the [Azure glossary][Azure glossary] pomóc Ci zaznajomić nową terminologią. Możesz też zwrócić uwagę na inne zasoby dotyczące usługi SQL Data Warehouse.  

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
