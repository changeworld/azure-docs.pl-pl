---
title: Monitorowanie usługi Azure SQL Database przy użyciu dynamicznych widoków zarządzania | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wykrywać i diagnozować typowych problemów z wydajnością za pomocą dynamicznych widoków zarządzania do monitorowania Microsoft Azure SQL Database.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: conceptual
ms.date: 08/08/2018
ms.author: carlrab
ms.openlocfilehash: c4d1170bd2fe4acb135c88191b447f734e312723
ms.sourcegitcommit: d16b7d22dddef6da8b6cfdf412b1a668ab436c1f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2018
ms.locfileid: "39715961"
---
# <a name="monitoring-azure-sql-database-using-dynamic-management-views"></a>Monitorowanie usługi Azure SQL Database przy użyciu dynamicznych widoków zarządzania
Microsoft Azure SQL Database umożliwia podzbiór dynamicznych widoków zarządzania do diagnozowania problemów z wydajnością, których przyczyną może być zablokowany lub długotrwałe zapytania, zasobów, wąskich gardeł, planów zapytań niską i tak dalej. Ten temat zawiera informacje na temat sposobu wykrywania typowych problemów z wydajnością za pomocą dynamicznych widoków zarządzania.

Usługa SQL Database obsługuje częściowo trzy kategorie dynamicznych widoków zarządzania:

* Związane z bazy danych dynamicznych widoków zarządzania.
* Powiązane z wykonywaniem dynamicznych widoków zarządzania.
* Transakcji powiązanych z dynamicznych widoków zarządzania.

Aby uzyskać szczegółowe informacje o dynamicznych widoków zarządzania, zobacz [dynamicznych widoków zarządzania i funkcje (Transact-SQL)](https://msdn.microsoft.com/library/ms188754.aspx) w dokumentacji SQL Server — książki Online.

## <a name="permissions"></a>Uprawnienia
W usłudze SQL Database zapytań dynamicznych widoków zarządzania wymaga **VIEW DATABASE STATE** uprawnienia. **VIEW DATABASE STATE** uprawnienie zwraca informacje o wszystkich obiektach w bieżącej bazie danych.
Aby udzielić **VIEW DATABASE STATE** uprawnienie do użytkownika konkretnej bazy danych, uruchom następujące zapytanie:

```GRANT VIEW DATABASE STATE TO database_user; ```

W wystąpieniu programu SQL Server w środowisku lokalnym dynamicznych widoków zarządzania zwraca informacje o stanie serwera. W usłudze SQL Database zwracają informacje dotyczące bieżącej logicznej bazy danych tylko.

## <a name="calculating-database-size"></a>Obliczanie rozmiaru bazy danych
Następujące zapytanie zwraca rozmiar bazy danych (w megabajtach):

```
-- Calculates the size of the database.
SELECT SUM(CAST(FILEPROPERTY(name, 'SpaceUsed') AS bigint) * 8192.) / 1024 / 1024 AS DatabaseSizeInMB
FROM sys.database_files
WHERE type_desc = 'ROWS';
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
Możesz użyć [sys.dm_exec_connections](https://msdn.microsoft.com/library/ms181509.aspx) widok, aby pobrać informacje o połączeniach nawiązać z określonego serwera usługi Azure SQL Database i szczegółów dotyczących każdego połączenia. Ponadto [sys.dm_exec_sessions](https://msdn.microsoft.com/library/ms176013.aspx) widok faktycznie jest pomocny podczas pobierania informacji o wszystkich aktywnych sesji użytkowników, jak i wewnętrznych zadaniach.
Następujące zapytanie pobiera informacji na temat bieżącego połączenia:

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
> Podczas wykonywania **sys.dm_exec_requests** i **widoków sys.dm_exec_sessions**, jeśli masz **VIEW DATABASE STATE** uprawnień w bazie danych, zostanie wyświetlony, wykonywanie wszystkich Sesje w bazie danych. w przeciwnym razie zostanie wyświetlony w bieżącej sesji.
> 
> 

## <a name="monitoring-query-performance"></a>Monitorowanie wydajności zapytań
Wolno lub czas wykonywania kwerend mogą wykorzystywać zasoby systemowe znaczące. W tej sekcji pokazano, jak wykrywać kilka typowe problemy z wydajnością zapytań za pomocą dynamicznych widoków zarządzania. Odwołanie do starszych, ale nadal przydatne na potrzeby rozwiązywania problemów jest [Rozwiązywanie problemów z wydajnością programu SQL Server 2008](http://download.microsoft.com/download/D/B/D/DBDE7972-1EB9-470A-BA18-58849DB3EB3B/TShootPerfProbs2008.docx) artykuł w witrynie Microsoft TechNet.

### <a name="finding-top-n-queries"></a>Znajdowanie zapytań pierwszych N
Poniższy przykład zwraca informacje o najważniejszych pięciu zapytaniach uszeregowanych według średniego czasu procesora CPU. W tym przykładzie agreguje zapytania zgodnie z ich skrót zapytania, aby logicznie równoważne zapytania są pogrupowane według ich zużycia zasobów zbiorczej.

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

### <a name="monitoring-blocked-queries"></a>Monitorowanie zablokowanych zapytania
Wolne lub długotrwałe zapytania może przyczynić się do nadmiernego wykorzystania zasobów i być konsekwencją zablokowanych zapytania. Przyczyna blokady może być niewłaściwy projekt aplikacji, plany zapytania, brak przydatnych indeksów i tak dalej. Widok sys.dm_tran_locks można użyć, aby uzyskać informacje dotyczące bieżącego działania blokowania w usłudze Azure SQL Database. Przykładowy kod, zobacz [sys.dm_tran_locks (Transact-SQL)](https://msdn.microsoft.com/library/ms190345.aspx) w dokumentacji SQL Server — książki Online.

### <a name="monitoring-query-plans"></a>Monitorowanie planów zapytań
Plan zapytania nieefektywne może również zwiększyć użycie procesora CPU. W poniższym przykładzie użyto [sys.dm_exec_query_stats](https://msdn.microsoft.com/library/ms189741.aspx) widok, aby ustalić, które zapytanie używa najbardziej zbiorczą procesora CPU.

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
[Wprowadzenie do usługi SQL Database](sql-database-technical-overview.md)

