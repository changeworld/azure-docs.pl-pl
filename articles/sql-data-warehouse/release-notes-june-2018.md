---
title: Informacje o wersji Azure SQL Data Warehouse czerwiec 2018 | Microsoft Docs
description: Informacje o wersji dla Azure SQL Data Warehouse.
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
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "67626149"
---
# <a name="whats-new-in-azure-sql-data-warehouse-june-2018"></a>Co nowego w Azure SQL Data Warehouse? Czerwiec 2018 r.
Azure SQL Data Warehouse ciągle otrzymuje ulepszenia. W tym artykule opisano nowe funkcje i zmiany wprowadzone w czerwcu 2018. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="user-defined-restore-points"></a>Punkty przywracania zdefiniowane przez użytkownika
SQL Data Warehouse automatycznie wykonuje migawki hurtowni danych co 8 godzin, co gwarantuje osiem-godzinny cel punktu odzyskiwania (RPO). Chociaż te zautomatyzowane migawki ułatwiają obciążenie związane z uruchamianiem hurtowni danych, istnieje potrzeba podjęcia migawek o krytycznym czasie w zależności od potrzeb firmy. Na przykład utworzenie migawki bezpośrednio przed znaczącym obciążeniem danych lub wdrożeniem nowych skryptów w magazynie danych w celu włączenia punktu przywracania bezpośrednio przed operacją. 

SQL Data Warehouse obsługuje teraz [zdefiniowane przez użytkownika punkty przywracania](https://azure.microsoft.com/blog/quick-recovery-time-with-sql-data-warehouse-using-user-defined-restore-points/) za pomocą polecenia cmdlet [New-AzSqlDatabaseRestorePoint](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabaserestorepoint) .

```powershell
New-AzSqlDatabaseRestorePoint
    -ResourceGroupName $ResourceGroupName
    -ServerName $ServerName
    -DatabaseName $DatabaseName
    -RestorePointLabel $RestorePointName
```

## <a name="column-level-security"></a>Zabezpieczenia na poziomie kolumny
Zarządzanie dostępem do danych i zabezpieczeniami w hurtowni danych ma kluczowe znaczenie dla tworzenia relacji zaufania z klientami i partnerami. SQL Data Warehouse [obsługuje teraz zabezpieczenia na poziomie kolumny (CLS)](https://azure.microsoft.com/blog/column-level-security-is-now-supported-in-azure-sql-data-warehouse/) , które pozwala na dostosowanie uprawnień do wyświetlania poufnych danych przez ograniczenie dostępu użytkowników do określonych kolumn w tabelach bez konieczności ponownego projektowania magazynu danych.

CLS pozwala kontrolować dostęp do kolumn tabeli na podstawie kontekstu wykonywania użytkownika lub ich przynależności do grupy przy [użyciu standardowej instrukcji](https://docs.microsoft.com/azure/sql-data-warehouse/column-level-security) T-SQL. Logika ograniczeń dostępu znajduje się w warstwie bazy danych, a nie z danych w innej aplikacji, upraszczając ogólną implementację zabezpieczeń.


```sql
CREATE USER Nurse WITHOUT LOGIN;   
GRANT SELECT ON Membership(MemberID, FirstName, LastName, Phone, Email) TO Nurse;   
EXECUTE AS USER = 'Nurse';
SELECT * FROM Membership;

Msg 230, Level 14, State 1, Line 12 
The SELECT permission was denied on the column 'SSN' of the object 'Membership', database 'CLS_TestDW', schema 'dbo'.
```

## <a name="objectschemaname"></a>OBJECT_SCHEMA_NAME
Funkcja [OBJECT_SCHEMA_NAME ()](https://docs.microsoft.com/sql/t-sql/functions/object-schema-name-transact-sql) zwraca nazwę schematu bazy danych dla obiektów należących do zakresu schematu. Ta funkcja stała się wspólna w narzędziach ETL, gdy jest sprawdzana poprawność schematu obiektu. 

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

## <a name="support-for-the-systimezoneinfo-view"></a>Obsługa widoku sys. time_zone_info
Widok [sys. time_zone_info](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-time-zone-info-transact-sql) zwraca informacje o obsługiwanych strefach czasowych w ramach Azure SQL Data Warehouse.

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

## <a name="auto-stats-operations-appear-in-sysdmpdwexecrequests-behavior-change"></a>Operacje dotyczące automatycznych statystyk pojawiają się w widoku sys. DM _pdw_exec_requests (zmiana zachowania)

Po wprowadzeniu [statystyk AutoCreate](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics#automatic-creation-of-statistic)Azure SQL Data Warehouse generuje statystykę w celu zoptymalizowania wykonywania zapytań. Wydanie z czerwca 2018 dodaje możliwość monitorowania, kiedy statystyki są generowane automatycznie przez dodanie rekordu do widoku [sys. DM _pdw_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) , gdy jest wykonywana jakakolwiek operacja [tworzenia statystyk](https://docs.microsoft.com/sql/t-sql/statements/create-statistics-transact-sql) .

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
Teraz, gdy znasz już nieco SQL Data Warehouse, Dowiedz się, jak szybko [utworzyć SQL Data Warehouse][create a SQL Data Warehouse]. Jeśli dopiero zaczynasz korzystać z platformy Azure, [słownik platformy Azure][Azure glossary] może pomóc Ci zaznajomić się z nową terminologią. Możesz też zwrócić uwagę na inne zasoby dotyczące usługi SQL Data Warehouse.  

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
