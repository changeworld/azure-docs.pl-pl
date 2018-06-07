---
title: Monitorowanie bazy danych Azure SQL przy użyciu dynamicznych widoków zarządzania | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wykrywanie i diagnozowanie typowych problemów z wydajnością przy użyciu dynamicznych widoków zarządzania do monitorowania programu Microsoft Azure SQL Database.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: carlrab
ms.openlocfilehash: a1333680225923a4e27f96e61a5b6530f32a9329
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34647888"
---
# <a name="monitoring-azure-sql-database-using-dynamic-management-views"></a>Monitorowanie usługi Azure SQL Database przy użyciu dynamicznych widoków zarządzania
Baza danych SQL Azure Microsoft umożliwia podzbiór dynamicznych widoków zarządzania do diagnozowania problemów z wydajnością, które mogą być spowodowane przez zablokowane lub długotrwałe zapytań, wąskich gardeł w zasobach, plany zapytań niska i tak dalej. Ten temat zawiera informacje na temat sposobu wykryć typowych problemów z wydajnością przy użyciu dynamicznych widoków zarządzania.

Baza danych SQL obsługuje częściowo dynamicznych widoków zarządzania trzy kategorie:

* Związane z bazy danych dynamicznych widoków zarządzania.
* Powiązane z wykonywaniem dynamicznych widoków zarządzania.
* Związane z transakcji dynamicznych widoków zarządzania.

Aby uzyskać szczegółowe informacje o dynamicznych widoków zarządzania, zobacz [dynamicznych widoków zarządzania i funkcji (Transact-SQL)](https://msdn.microsoft.com/library/ms188754.aspx) w dokumentacji SQL Server — książki Online.

## <a name="permissions"></a>Uprawnienia
W bazie danych SQL, zapytanie widoku dynamicznego zarządzania wymaga **stan bazy danych WIDOKU** uprawnienia. **Stan bazy danych WIDOKU** uprawnienia zwraca informacje o wszystkich obiektów w bieżącej bazie danych.
Aby przyznać **stan WIDOKU bazy danych** uprawnienia do użytkownika określonej bazy danych, uruchom następujące zapytanie:

```GRANT VIEW DATABASE STATE TO database_user; ```

W wystąpieniu programu SQL Server, lokalną dynamicznych widoków zarządzania zwraca informacje o stanie serwera. W bazie danych SQL zwracają informacje dotyczące bieżącej logicznej bazy danych tylko.

## <a name="calculating-database-size"></a>Obliczanie rozmiaru bazy danych
Następujące zapytanie zwraca rozmiar bazy danych (w MB):

```
-- Calculates the size of the database.
SELECT SUM(reserved_page_count)*8.0/1024
FROM sys.dm_db_partition_stats;
GO
```

Następujące zapytanie zwraca rozmiar poszczególnych obiektów (w MB) w bazie danych:

```
-- Calculates the size of individual database objects.
SELECT sys.objects.name, SUM(reserved_page_count) * 8.0 / 1024
FROM sys.dm_db_partition_stats, sys.objects
WHERE sys.dm_db_partition_stats.object_id = sys.objects.object_id
GROUP BY sys.objects.name;
GO
```

## <a name="monitoring-connections"></a>Monitorowanie połączeń
Można użyć [sys.dm_exec_connections](https://msdn.microsoft.com/library/ms181509.aspx) widoku można pobrać informacji dotyczących połączeń ustanowionych na określonym serwerze bazy danych SQL Azure i szczegóły każdego połączenia. Ponadto [widoku sys.dm_exec_sessions](https://msdn.microsoft.com/library/ms176013.aspx) widok jest przydatne podczas pobierania informacji o wszystkich aktywnych sesji użytkowników i zadań wewnętrznych.
Następujące zapytanie pobiera informacje o bieżącym połączeniem:

```
SELECT
    c.session_id, c.net_transport, c.encrypt_option,
    c.auth_scheme, s.host_name, s.program_name,
    s.client_interface_name, s.login_name, s.nt_domain,
    s.nt_user_name, s.original_login_name, c.connect_time,
    s.login_time
FROM sys.dm_exec_connections AS c
JOIN sys.dm_exec_sessions AS s
    ON c.session_id = s.session_id
WHERE c.session_id = @@SPID;
```

> [!NOTE]
> Podczas wykonywania **sys.dm_exec_requests** i **widoków widoku sys.dm_exec_sessions**, jeśli masz **stan WIDOKU bazy danych** uprawnień w bazie danych, zostanie wyświetlony, wszystkie wykonywania Sesje w bazie danych. w przeciwnym razie zostanie wyświetlony w bieżącej sesji.
> 
> 

## <a name="monitoring-query-performance"></a>Monitorowanie wydajności kwerendy
Wolno lub czas uruchamiania zapytań może wykorzystać zasoby systemowe znaczące. W tej sekcji przedstawiono sposób użycia dynamicznych widoków zarządzania do wykrywania kilka typowych problemów wydajności zapytania. Starsze, ale nadal przydatne informacje dotyczące rozwiązywania problemów, jest [Rozwiązywanie problemów z wydajnością programu SQL Server 2008](http://download.microsoft.com/download/D/B/D/DBDE7972-1EB9-470A-BA18-58849DB3EB3B/TShootPerfProbs2008.docx) artykułu w serwisie Microsoft TechNet.

### <a name="finding-top-n-queries"></a>Znajdowanie zapytania pierwszych N
Poniższy przykład zwraca informacje o najważniejszych zapytań pięć uszeregowane według Średni czas procesora CPU. W tym przykładzie agreguje zapytania zgodnie z ich skrótu zapytania, aby zapytania logicznie równoważne są pogrupowane według ich wykorzystania zasobów zbiorczą.

```
SELECT TOP 5 query_stats.query_hash AS "Query Hash",
    SUM(query_stats.total_worker_time) / SUM(query_stats.execution_count) AS "Avg CPU Time",
    MIN(query_stats.statement_text) AS "Statement Text"
FROM
    (SELECT QS.*,
    SUBSTRING(ST.text, (QS.statement_start_offset/2) + 1,
    ((CASE statement_end_offset
        WHEN -1 THEN DATALENGTH(ST.text)
        ELSE QS.statement_end_offset END
            - QS.statement_start_offset)/2) + 1) AS statement_text
     FROM sys.dm_exec_query_stats AS QS
     CROSS APPLY sys.dm_exec_sql_text(QS.sql_handle) as ST) as query_stats
GROUP BY query_stats.query_hash
ORDER BY 2 DESC;
```

### <a name="monitoring-blocked-queries"></a>Monitorowanie zablokowanych zapytań
Wolne lub długotrwałe zapytania może przyczynić się do nadmiernego wykorzystania zasobów i być skutkiem zablokowanych zapytań. Przyczyna blokady może być niewłaściwy projekt aplikacji, plany zapytania, brak przydatne indeksy i tak dalej. Widok sys.dm_tran_locks można użyć, aby uzyskać informacje dotyczące bieżącego działania blokady w bazie danych SQL Azure. Na przykład kodu, zobacz [sys.dm_tran_locks (Transact-SQL)](https://msdn.microsoft.com/library/ms190345.aspx) w dokumentacji SQL Server — książki Online.

### <a name="monitoring-query-plans"></a>Monitorowanie plany zapytań
Plan zapytania nieefektywne także może zwiększyć użycie procesora CPU. W poniższym przykładzie użyto [sys.dm_exec_query_stats](https://msdn.microsoft.com/library/ms189741.aspx) widoku, aby określić, które zapytanie używa najbardziej zbiorczą procesora CPU.

```
SELECT
    highest_cpu_queries.plan_handle,
    highest_cpu_queries.total_worker_time,
    q.dbid,
    q.objectid,
    q.number,
    q.encrypted,
    q.[text]
FROM
    (SELECT TOP 50
        qs.plan_handle,
        qs.total_worker_time
    FROM
        sys.dm_exec_query_stats qs
    ORDER BY qs.total_worker_time desc) AS highest_cpu_queries
    CROSS APPLY sys.dm_exec_sql_text(plan_handle) AS q
ORDER BY highest_cpu_queries.total_worker_time DESC;
```

## <a name="see-also"></a>Zobacz także
[Wprowadzenie do bazy danych SQL](sql-database-technical-overview.md)

