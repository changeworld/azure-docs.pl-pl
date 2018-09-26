---
title: Monitorowanie usługi Azure SQL Database przy użyciu dynamicznych widoków zarządzania | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wykrywać i diagnozować typowych problemów z wydajnością za pomocą dynamicznych widoków zarządzania do monitorowania Microsoft Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 08/08/2018
ms.openlocfilehash: 97907eee9982fdf6a804bc13edbf8c14efa4ce42
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2018
ms.locfileid: "47161390"
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

## <a name="monitor-resource-use"></a>Monitorowanie wykorzystania zasobów

Można monitorować za pomocą użycia zasobów [SQL Database Query Performance Insight](sql-database-query-performance.md) i [Query Store](https://msdn.microsoft.com/library/dn817826.aspx).

Można również monitorować użycie za pomocą te dwa widoki:

* [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx)
* [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx)

### <a name="sysdmdbresourcestats"></a>sys.dm_db_resource_stats
Możesz użyć [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) widoku w każdej bazie danych SQL. **Sys.dm_db_resource_stats** widok pokazuje ostatnie dane wykorzystania zasobów względem warstwy usług. Średni procent procesora CPU, we/wy danych, zapisuje dziennik i pamięci są rejestrowane co 15 sekund i są przechowywane przez 1 godzinę.

Ponieważ ten widok zawiera bardziej szczegółowe Sprawdź wykorzystanie zasobów, użyj **sys.dm_db_resource_stats** pierwszy do dowolnej analizy bieżący stan lub dotyczącymi rozwiązywania problemów. Na przykład to zapytanie Wyświetla średniego i maksymalnego wykorzystanie w bieżącej bazie danych w ciągu ostatniej godziny:

    SELECT  
        AVG(avg_cpu_percent) AS 'Average CPU use in percent',
        MAX(avg_cpu_percent) AS 'Maximum CPU use in percent',
        AVG(avg_data_io_percent) AS 'Average data IO in percent',
        MAX(avg_data_io_percent) AS 'Maximum data IO in percent',
        AVG(avg_log_write_percent) AS 'Average log write use in percent',
        MAX(avg_log_write_percent) AS 'Maximum log write use in percent',
        AVG(avg_memory_usage_percent) AS 'Average memory use in percent',
        MAX(avg_memory_usage_percent) AS 'Maximum memory use in percent'
    FROM sys.dm_db_resource_stats;  

W przypadku innych kwerend, zobacz przykłady w [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx).

### <a name="sysresourcestats"></a>sys.resource_stats
[Sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) wyświetlać w **wzorca** bazy danych zawiera dodatkowe informacje, które mogą ułatwić monitorowanie wydajności usługi SQL database w warstwie jego określonej usługi i obliczenia rozmiaru. Dane są zbierane, co 5 minut i jest zachowywana na potrzeby około 14 dni. Ten widok jest przydatne w przypadku dłuższy okres historycznej analizy używaniu zasobów w bazie danych SQL.

Poniższy wykres pokazuje CPU wykorzystanie zasobów bazy danych Premium o rozmiarze obliczeń P2 za każdą godzinę w ciągu tygodnia. Ten wykres jest uruchamiany w każdy poniedziałek, pokazuje 5 dni roboczych i następnie wyświetla weekendy, znacznie mniej sytuacji w aplikacji.

![Wykorzystanie zasobów bazy danych SQL](./media/sql-database-performance-guidance/sql_db_resource_utilization.png)

Dane, ta baza danych ma obecnie szczytowego obciążenia procesora CPU nieco ponad 50 procent użycia Procesora względem P2 obliczenia rozmiaru (południe we wtorek). Jeśli procesora CPU jest dominującym czynnikiem profil zasobów aplikacji, następnie można zdecydować, czy P2 jest rozmiar obliczeń prawo, aby zagwarantować, że obciążenie zawsze pasuje do. Jeśli spodziewasz się aplikacji z upływem czasu, to dobry pomysł, aby mieć buforu dodatkowych zasobów, aby aplikacja nigdy nie dociera do poziomu wydajności limit. Zwiększenie rozmiaru obliczeń może pomóc uniknąć błędów widoczne dla klientów, które mogą wystąpić, gdy baza danych nie ma wystarczająco dużo mocy do przetwarzania żądań, szczególnie w środowiskach wrażliwy na opóźnienia. Przykładem jest bazy danych, który obsługuje aplikację, która umożliwia malowanie stron sieci Web na podstawie wyników wywołań bazy danych.

Pozostałe typy aplikacji może interpretować tego samego wykresu. Na przykład jeśli aplikacja podejmuje próbę przetwarzania listy płac danych każdego dnia i ma ten sam wykres, tego rodzaju "zadania usługi batch" model może poradzić w rozmiarze obliczeniowe P1. Rozmiar obliczeniowe P1 ma 100 jednostek Dtu w porównaniu do 200 jednostek Dtu w P2 obliczenia rozmiaru. Rozmiar obliczeniowe P1 zapewnia połowa wydajności P2 obliczenia rozmiaru. Dlatego 50 procent użycia procesora CPU w P2 jest równa 100 procent użycia procesora CPU w P1. Aplikacja nie ma limitów czasu, może być niezależnie od tego, jeżeli wykonanie zadania trwa 2 godziny, czyli 2,5 godzin, jeśli zostanie wykonana już dziś. Prawdopodobnie aplikację z tej kategorii można użyć rozmiaru obliczeń P1. Możesz korzystać z zalet fakt, że są okresy w ciągu dnia, podczas wykorzystania zasobów jest krótszy, dzięki czemu wszelkie "duże szczytowe" może być rozlane dane za pośrednictwem w jednym z koryta w dalszej części tego dnia. Rozmiar obliczeniowe P1 może być dobrym dla tego rodzaju aplikacji i oszczędzać pieniądze, tak długo, jak zakończyć zadania w czasie każdego dnia.

Usługa Azure SQL Database udostępnia używane informacje o zasobach dla każdej bazy danych dla aktywnej w **sys.resource_stats** widoku **wzorca** bazy danych na każdym serwerze. Dane w tabeli są agregowane dla 5-minutowych interwałach. Przy użyciu warstwy usług podstawowa, standardowa i Premium danych może zająć więcej niż 5 minut, aby są wyświetlane w tabeli, dzięki czemu te dane są bardziej użyteczna w przypadku analizy historycznej, a nie analizy prawie w czasie rzeczywistym. Zapytanie **sys.resource_stats** wyświetlania, aby wyświetlić najnowszą historię bazy danych i do sprawdzania, czy rezerwacja została wybrana opcja dostarczonych wydajności, chcesz, aby w razie.

> [!NOTE]
> Musi być podłączony do **wzorca** bazy danych serwera logicznego bazy danych SQL do wykonywania zapytań **sys.resource_stats** w poniższych przykładach.
> 
> 

Ten przykład pokazuje, jak dane w tym widoku jest uwidaczniany:

    SELECT TOP 10 *
    FROM sys.resource_stats
    WHERE database_name = 'resource1'
    ORDER BY start_time DESC

![W widoku wykazu sys.resource_stats](./media/sql-database-performance-guidance/sys_resource_stats.png)

W kolejnym przykładzie pokazano różne sposoby, w którym można **sys.resource_stats** widoku, aby uzyskać informacje o tym, jak usługi SQL database korzysta z zasobów w katalogu:

1. Aby przyjrzeć się zasobu z ostatniego tygodnia dla userdb1 bazy danych, można uruchomić tego zapytania:
   
        SELECT *
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND
              start_time > DATEADD(day, -7, GETDATE())
        ORDER BY start_time DESC;
2. Aby ocenić, jak dobrze pasuje do rozmiaru obliczeń obciążenia, musisz przejść do każdego aspektu metryk zasobów: procesora CPU, operacje odczytu, zapisu, liczba procesów roboczych i liczba sesji. Poniżej przedstawiono poprawioną wykonywać zapytania za pomocą **sys.resource_stats** zgłosić średnie i maksymalne wartości tych metryk zasobów:
   
        SELECT
            avg(avg_cpu_percent) AS 'Average CPU use in percent',
            max(avg_cpu_percent) AS 'Maximum CPU use in percent',
            avg(avg_data_io_percent) AS 'Average physical data IO use in percent',
            max(avg_data_io_percent) AS 'Maximum physical data IO use in percent',
            avg(avg_log_write_percent) AS 'Average log write use in percent',
            max(avg_log_write_percent) AS 'Maximum log write use in percent',
            avg(max_session_percent) AS 'Average % of sessions',
            max(max_session_percent) AS 'Maximum % of sessions',
            avg(max_worker_percent) AS 'Average % of workers',
            max(max_worker_percent) AS 'Maximum % of workers'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
3. Dzięki tym informacjom o średnie i maksymalne wartości Wszystkie metryki zasobu można ocenić, jak dobrze obciążenia pasuje do rozmiaru obliczeń, który został wybrany. Zazwyczaj średnie wartości z **sys.resource_stats** zapewniają dobrą punktu odniesienia, aby używać względem rozmiar docelowy. Powinna to być Twoje hokejowego pomiaru podstawowego. Aby uzyskać przykład być może używasz warstwie usługi standardowa o rozmiarze obliczeń S2. Średniego użycia wartości procentowe dla procesora CPU i we/wy operacji odczytu i zapisu czy poniżej 40 procent, średnia liczba procesów roboczych jest poniżej 50 i średnia liczba sesji znajduje się poniżej 200. Obciążenie może pasuje do rozmiaru obliczeń S1. To łatwo sprawdzić, czy baza danych mieści się w granicach procesu roboczego i sesji. Aby zobaczyć, czy bazy danych jest dopasowywana do niższych rozmiaru obliczeń w odniesieniu do procesora CPU, operacji odczytu i zapisu, dzielenie liczby jednostek DTU niższym obliczenia rozmiaru przez liczbę jednostek DTU bieżącego rozmiaru obliczeń, a następnie pomnożyć wynik przez 100:
   
    **S1 DTU / S2 DTU * 100 = 20 / 50 * 100 = 40**
   
    Wynik jest względnej wydajności różnicy między tymi dwoma obliczenia rozmiarów w postaci wartości procentowej. Swoje użycie zasobów nie przekracza kwoty, obciążenie, może być mieści się w dolnym rozmiaru obliczeń. Jednak należy przyjrzeć się wszystkie zakresy wartości użycia zasobów w celu określenia według wartości procentowej, jak często obciążenia baz danych czy mieściły się w dolnym rozmiaru obliczeń. Następujące zapytanie Wyświetla Dopasuj procent na wymiarze zasobu, na podstawie progu o 40 procent, które firma Microsoft obliczona w tym przykładzie:
   
        SELECT
            (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU Fit Percent'
            ,(COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log Write Fit Percent'
            ,(COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical Data IO Fit Percent'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
   
    Oparte na warstwie usługi bazy danych, możesz zdecydować, czy obciążenie jest dopasowywana do niższych rozmiaru obliczeń. Jeśli poprzednie zapytanie zwraca wartości większe niż 99,9% dla wszystkich wymiarów zasobów celu obciążenie bazy danych to dostępność na poziomie 99,9 procent, obciążenie może pasuje do niższych rozmiaru obliczeń.
   
    Patrząc Dopasuj wartość procentową umożliwia również wgląd tego, czy należy przenieść do następnego większy rozmiar obliczeń celu usługi. Na przykład userdb1 przedstawia użycie procesora CPU dla zeszłego tygodnia:
   
   | Średni procent użycia Procesora | Maksymalny procent procesora CPU |
   | --- | --- |
   | 24.5 |100.00 |
   
    Średnie użycie procesora CPU jest o kwartał limit rozmiaru obliczeń, który będzie pasują do rozmiaru obliczeń bazy danych. Jednak wartość maksymalna pokazuje, że baza danych osiągnie limit rozmiaru obliczeń. Czy muszą przejść do następnego większy rozmiar obliczeniowych? Przyjrzyj się jak wiele razy Twoje obciążenie osiągnie 100 procent, a następnie porównaj ją z celu obciążenie bazy danych.
   
        SELECT
        (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU fit percent'
        ,(COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log write fit percent'
        ,(COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical data IO fit percent'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
   
    Jeśli to zapytanie zwraca wartość typu mniej niż 99,9% dla każdego zasobu trzech wymiarów, rozważ albo przeniesienie na następny większy rozmiar obliczeń lub zmniejsz obciążenie bazy danych SQL za pomocą technik Dostrajanie aplikacji.
4. To ćwiczenie uwzględnia również zwiększenie obciążenia przewidywany w przyszłości.

W przypadku pul elastycznych można monitorować pojedyncze bazy danych w puli za pomocą metod opisanych w tej sekcji. Można jednak również monitorować pulę jako całość. Informacje na ten temat znajdziesz w artykule [Monitor and manage an elastic pool](sql-database-elastic-pool-manage-portal.md) (Monitorowanie puli elastycznej i zarządzanie nią).


### <a name="maximum-concurrent-requests"></a>Maksymalna liczba równoczesnych żądań
Aby wyświetlić liczbę jednoczesnych żądań, należy uruchomić to zapytanie Transact-SQL w bazie danych SQL:

    SELECT COUNT(*) AS [Concurrent_Requests]
    FROM sys.dm_exec_requests R

Aby analizować obciążenia lokalnej bazy danych programu SQL Server, należy zmodyfikować tego zapytania do filtrowania konkretnej bazy danych, czy mają być analizowane. Na przykład w przypadku lokalnej bazy danych o nazwie mojabazadanych to zapytanie Transact-SQL zwraca liczbę jednoczesnych żądań w tej bazie danych:

    SELECT COUNT(*) AS [Concurrent_Requests]
    FROM sys.dm_exec_requests R
    INNER JOIN sys.databases D ON D.database_id = R.database_id
    AND D.name = 'MyDatabase'

Jest to po prostu migawki w jednym punkcie w czasie. Aby lepiej zrozumieć swoje wymagania współbieżne żądania i obciążenia, należy zebrać wiele przykładów wraz z upływem czasu.

### <a name="maximum-concurrent-logins"></a>Maksymalna współbieżnych logowań
Możesz analizować Twoich wzorców użytkownika i aplikacji, aby poznać częstotliwość logowania. Można również uruchomić rzeczywistych warunkach w środowisku testowym, aby upewnić się, że nie występują to lub inne ograniczenia, które omówimy w tym artykule. Nie ma jednego zapytania lub dynamicznego widoku zarządzania (DMV), można wyświetlić równoczesnych czy zlicza logowania lub historii.

Jeśli wielu klientów używa tych samych parametrach połączenia, usługa uwierzytelnia każdego logowania. Jeśli 10 użytkowników jednocześnie połączyć się z bazą danych przy użyciu tej samej nazwy użytkownika i hasła, może to być 10 współbieżnych logowań. Ten limit dotyczy tylko czas trwania uwierzytelniania i logowania. Tych samych użytkowników 10 połączenia z bazą danych po kolei, liczba współbieżnych logowań nigdy nie będzie większa niż 1.

> [!NOTE]
> Obecnie ten limit nie ma zastosowania do baz danych w elastycznej puli.
> 
> 

### <a name="maximum-sessions"></a>Maksymalna liczba sesji
Aby wyświetlić liczbę bieżących aktywnych sesji, należy uruchomić to zapytanie Transact-SQL w bazie danych SQL:

    SELECT COUNT(*) AS [Sessions]
    FROM sys.dm_exec_connections

Jeśli masz analizowanie obciążeń programu SQL Server w środowisku lokalnym, należy zmodyfikować zapytanie, aby skoncentrować się na konkretnej bazy danych. To zapytanie zawarto informacje ułatwiające określenie potrzeb możliwe sesji dla bazy danych, jeżeli rozważasz przeniesienie ich do usługi Azure SQL Database.

    SELECT COUNT(*)  AS [Sessions]
    FROM sys.dm_exec_connections C
    INNER JOIN sys.dm_exec_sessions S ON (S.session_id = C.session_id)
    INNER JOIN sys.databases D ON (D.database_id = S.database_id)
    WHERE D.name = 'MyDatabase'

Ponownie tych zapytań Zwróć liczba punktu w czasie. W przypadku zebrania wielu próbkach wraz z upływem czasu, będziesz mieć najlepsze zrozumienia sesji, użyj.

Analiza bazy danych SQL, statystyki historyczne można uzyskać w ramach sesji, badając [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) widoku i przeglądając **active_session_count** kolumny. 

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

