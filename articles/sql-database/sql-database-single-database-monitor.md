---
title: Monitorowanie wydajności bazy danych w usłudze Azure SQL Database | Microsoft Docs
description: Poznaj opcje monitorowania bazy danych za pomocą narzędzi Azure i dynamicznych widoków zarządzania.
keywords: monitorowanie bazy danych, wydajność bazy danych w chmurze
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: conceptual
ms.date: 07/16/2018
ms.author: carlrab
ms.openlocfilehash: dc04a9334b63656719a7633a8dd7154ed6cd6993
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/17/2018
ms.locfileid: "39092583"
---
# <a name="monitoring-database-performance-in-azure-sql-database"></a>Monitorowanie wydajności bazy danych w usłudze Azure SQL Database
Monitorowanie wydajności bazy danych SQL na platformie Azure rozpoczyna się od monitorowania wykorzystania zasobów względem wybranego poziomu wydajności bazy danych. Monitorowanie pomaga ustalić, czy baza danych ma nadmiarowej pojemności lub występują problemy, ponieważ zasoby maksymalnego limitu, a następnie zdecydować, czy nadszedł czas, aby dostosować poziom wydajności i warstwę bazy danych w usługi [oparte na jednostkach DTU model zakupu](sql-database-service-tiers-dtu.md) lub [modelu zakupu opartego na rdzeniach wirtualnych](sql-database-service-tiers-vcore.md). Bazę danych można monitorować za pomocą narzędzi graficznych w [witrynie Azure Portal](https://portal.azure.com) lub przy użyciu [dynamicznych widoków zarządzania](https://msdn.microsoft.com/library/ms188754.aspx) SQL.

> [!TIP]
> Użyj [Azure SQL Intelligent Insights](sql-database-intelligent-insights.md) automatyczne monitorowanie wydajności bazy danych. Po wykryciu problemu z wydajnością, dziennik diagnostyczny jest generowany ze szczegółami i głównej przyczyny Analysis (analiza głównej przyczyny) problemu. Zalecenie dotyczące poprawy wydajności znajduje się, gdy jest to możliwe.
>

## <a name="monitor-databases-using-the-azure-portal"></a>Monitorowanie baz danych za pomocą witryny Azure Portal
W witrynie [Azure Portal](https://portal.azure.com/) możesz wybrać bazę danych i kliknąć wykres **Monitorowanie**, aby monitorować wykorzystanie pojedynczej bazy danych. Spowoduje to wyświetlenie okna **Metryka**, które możesz zmienić, klikając przycisk **Edytuj wykres**. Dodaj następujące metryki:

* Procent użycia procesora CPU
* Procent użycia jednostek DTU
* Procent użycia operacji we/wy na danych
* Procent użycia rozmiaru bazy danych

Po dodaniu tych metryk możesz nadal wyświetlać je w **monitorowanie** wykresu z dodatkowymi informacjami na **metryki** okna. Wszystkie cztery metryki pokazują średnią wartość procentową wykorzystania względem jednostek **DTU** bazy danych. Zobacz [modelu zakupu opartego na jednostkach DTU](sql-database-service-tiers-dtu.md) i [modelu zakupu opartego na rdzeniach wirtualnych](sql-database-service-tiers-vcore.md) artykuły, aby uzyskać więcej informacji na temat warstw usługi.  

![Monitorowanie warstw usług pod względem wydajności bazy danych.](./media/sql-database-single-database-monitoring/sqldb_service_tier_monitoring.png)

Możesz również skonfigurować alerty dotyczące metryk wydajności. Kliknij przycisk **Dodaj alert** w oknie **Metryka**. Użyj kreatora, aby skonfigurować alert. Istnieje możliwość ustawienia alertu, który będzie wysyłany, gdy metryki przekroczą określony próg lub spadną poniżej określonego progu.

Jeśli na przykład spodziewasz się, że obciążenie bazy danych wzrośnie, możesz skonfigurować alert polegający na wysłaniu wiadomości e-mail, gdy dowolna z metryk wydajności osiągnie 80%. To wczesne ostrzeżenie jest przydatne, ponieważ pozwala zorientować się, kiedy warto zmienić poziom wydajności na kolejny, wyższy.

Metryki wydajności mogą także pomóc określić, czy można obniżyć poziom wydajności. Załóżmy, że używasz bazy danych, której warstwa i poziom to Standardowa S2, a wszystkie metryki wydajności pokazują, że baza danych wykorzystuje średnio nie więcej niż 10% w określonym czasie. Istnieje prawdopodobieństwo, że baza danych będzie działać równie dobrze na poziomie Standardowa S1. Przed podjęciem decyzji o przeniesieniu na poziom o mniejszej wydajności należy jednak pamiętać o obciążeniach, które chwilowo wzrastają lub zmieniają się w czasie.

## <a name="monitor-databases-using-dmvs"></a>Monitorowanie baz danych przy użyciu widoków DMV
Te same metryki, które są przedstawiane w portalu, są również dostępne za pośrednictwem widoków systemowych: [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) w logicznej bazie danych **master** na serwerze oraz [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) w bazie danych użytkownika. Użyj widoku **sys.resource_stats**, jeśli chcesz monitorować mniej szczegółowe dane przez dłuższy okres. Użyj widoku **sys.dm_db_resource_stats**, jeśli chcesz monitorować bardziej szczegółowe dane w mniejszych odcinkach czasu. Aby uzyskać więcej informacji, zobacz [wytyczne dotyczące wydajności usługi Azure SQL Database](sql-database-single-database-monitor.md#monitor-resource-use)

> [!NOTE]
> Widok **sys.dm_db_resource_stats** zwraca pusty zestaw wyników w przypadku używania baz danych w wersjach Web i Business, które zostały wycofane.
>
>

### <a name="monitor-resource-use"></a>Monitorowanie wykorzystania zasobów

Można monitorować za pomocą użycia zasobów [SQL Database Query Performance Insight](sql-database-query-performance.md) i [Query Store](https://msdn.microsoft.com/library/dn817826.aspx).

Można również monitorować użycie za pomocą te dwa widoki:

* [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx)
* [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx)

#### <a name="sysdmdbresourcestats"></a>sys.dm_db_resource_stats
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

#### <a name="sysresourcestats"></a>sys.resource_stats
[Sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) wyświetlać w **wzorca** bazy danych zawiera dodatkowe informacje, które mogą ułatwić monitorowanie wydajności bazy danych SQL na poziomie warstwy i wydajności określonej usługi. Dane są zbierane, co 5 minut i jest zachowywana na potrzeby około 14 dni. Ten widok jest przydatne w przypadku dłuższy okres historycznej analizy używaniu zasobów w bazie danych SQL.

Poniższy wykres pokazuje CPU wykorzystanie zasobów bazy danych Premium z poziomem wydajności P2 za każdą godzinę w ciągu tygodnia. Ten wykres jest uruchamiany w każdy poniedziałek, pokazuje 5 dni roboczych i następnie wyświetla weekendy, znacznie mniej sytuacji w aplikacji.

![Wykorzystanie zasobów bazy danych SQL](./media/sql-database-performance-guidance/sql_db_resource_utilization.png)

Dane, ta baza danych ma obecnie szczytowe obciążenie procesora CPU, nieco ponad 50 procent użycia Procesora względem poziom wydajności P2 (południe we wtorek). Jeśli procesora CPU jest dominującym czynnikiem profilu zasobów aplikacji, można zdecydować, czy P2 jest poziom wydajności prawo, aby zagwarantować, że obciążenie zawsze pasuje do. Jeśli spodziewasz się aplikacji z upływem czasu, to dobry pomysł, aby mieć buforu dodatkowych zasobów, aby aplikacja nigdy nie dociera do poziomu wydajności limit. Zwiększenie poziomu wydajności może pomóc uniknąć błędów widoczne dla klientów, które mogą wystąpić, gdy baza danych nie ma wystarczająco dużo mocy do przetwarzania żądań, szczególnie w środowiskach wrażliwy na opóźnienia. Przykładem jest bazy danych, który obsługuje aplikację, która umożliwia malowanie stron sieci Web na podstawie wyników wywołań bazy danych.

Pozostałe typy aplikacji może interpretować tego samego wykresu. Na przykład jeśli aplikacja podejmuje próbę przetwarzania listy płac danych każdego dnia i ma ten sam wykres, tego rodzaju "zadania usługi batch" model może poradzić na poziom wydajności P1. Poziom wydajności P1 ma 100 jednostek Dtu w porównaniu do 200 jednostek Dtu na poziomie wydajności P2. Poziom wydajności P1 zapewnia połowa wydajność poziom wydajności P2. Dlatego 50 procent użycia procesora CPU w P2 jest równa 100 procent użycia procesora CPU w P1. Aplikacja nie ma limitów czasu, może być niezależnie od tego, jeżeli wykonanie zadania trwa 2 godziny, czyli 2,5 godzin, jeśli zostanie wykonana już dziś. Prawdopodobnie aplikację z tej kategorii można użyć poziom wydajności P1. Możesz korzystać z zalet fakt, że są okresy w ciągu dnia, podczas wykorzystania zasobów jest krótszy, dzięki czemu wszelkie "duże szczytowe" może być rozlane dane za pośrednictwem w jednym z koryta w dalszej części tego dnia. Poziom wydajności P1 może być dobrym dla tego rodzaju aplikacji i oszczędzać pieniądze, tak długo, jak zakończyć zadania w czasie każdego dnia.

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
2. Aby ocenić, jak dobrze obciążenia pasuje do poziomu wydajności, należy przejść do każdego aspektu metryk zasobów: procesora CPU, operacje odczytu, zapisu, liczba procesów roboczych i liczba sesji. Poniżej przedstawiono poprawioną wykonywać zapytania za pomocą **sys.resource_stats** zgłosić średnie i maksymalne wartości tych metryk zasobów:
   
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
3. Dzięki tym informacjom o średnie i maksymalne wartości Wszystkie metryki zasobu można ocenić, jak dobrze obciążenia pasuje do poziomu wydajności, który został wybrany. Zazwyczaj średnie wartości z **sys.resource_stats** zapewniają dobrą punktu odniesienia, aby używać względem rozmiar docelowy. Powinna to być Twoje hokejowego pomiaru podstawowego. Na przykład być może używasz warstwie usług standardowa na poziomie wydajności S2. Średniego użycia wartości procentowe dla procesora CPU i we/wy operacji odczytu i zapisu czy poniżej 40 procent, średnia liczba procesów roboczych jest poniżej 50 i średnia liczba sesji znajduje się poniżej 200. Obciążenie może mieści się w poziomie wydajności S1. To łatwo sprawdzić, czy baza danych mieści się w granicach procesu roboczego i sesji. Aby zobaczyć, czy bazy danych jest dopasowywana do niższego poziomu wydajności w odniesieniu do Procesora, odczytuje i zapisu, dzielenie liczby jednostek DTU niższego poziomu wydajności według liczby jednostek DTU Twój bieżący poziom wydajności, a następnie pomnożyć wynik przez 100:
   
    **S1 DTU / S2 DTU * 100 = 20 / 50 * 100 = 40**
   
    Wynik jest względnej wydajności różnica między poziomami wydajności dwa w postaci wartości procentowej. Jeśli swoje użycie zasobów nie przekracza kwoty, obciążenie może pasuje do niższego poziomu wydajności. Jednak należy przyjrzeć się wszystkie zakresy wartości użycia zasobów w celu określenia według wartości procentowej, jak często obciążenia baz danych czy mieściły się w niższego poziomu wydajności. Następujące zapytanie Wyświetla Dopasuj procent na wymiarze zasobu, na podstawie progu o 40 procent, które firma Microsoft obliczona w tym przykładzie:
   
        SELECT
            (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU Fit Percent'
            ,(COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log Write Fit Percent'
            ,(COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical Data IO Fit Percent'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
   
    Oparte na swojej bazy danych poziomu usług (SLO), możesz zdecydować, czy obciążenie jest dopasowywana do niższego poziomu wydajności. Jeśli obciążenie bazy danych SLO jest 99,9% i poprzednie zapytanie zwraca wartości większe niż 99,9% dla wszystkich zasobów trzech wymiarów, prawdopodobnie obciążenie jest dopasowywana do niższego poziomu wydajności.
   
    Patrząc Dopasuj wartość procentową umożliwia również wgląd tego, czy należy przenieść do następnego wyższy poziom wydajności aby spełnić swoje SLO. Na przykład userdb1 przedstawia użycie procesora CPU dla zeszłego tygodnia:
   
   | Średni procent użycia Procesora | Maksymalny procent procesora CPU |
   | --- | --- |
   | 24.5 |100.00 |
   
    Średnie użycie procesora CPU jest o kwartał limitu poziom wydajności, który będzie pasują do poziomu wydajności bazy danych. Jednak wartość maksymalna pokazuje, że bazy danych osiągnie limit poziom wydajności. Czy muszą przejść do następnego wyższy poziom wydajności? Przyjrzyj się jak wiele razy Twoje obciążenie osiągnie 100 procent, a następnie porównaj ją z SLO obciążenia baz danych.
   
        SELECT
        (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU fit percent'
        ,(COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log write fit percent'
        ,(COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical data IO fit percent'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
   
    Jeśli to zapytanie zwraca wartość typu mniej niż 99,9% dla każdego zasobu trzech wymiarów, należy wziąć pod uwagę albo przejście do następnego wyższy poziom wydajności lub zmniejsz obciążenie bazy danych SQL za pomocą technik Dostrajanie aplikacji.
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

## <a name="next-steps"></a>Kolejne kroki

- Automatyczne dostrajanie indeksów bazy danych i zapytania plany wykonywania za pomocą [dostrajania automatycznego usługi Azure SQL Database](sql-database-automatic-tuning.md).
- Monitorowanie wydajności bazy danych, które automatycznie przy użyciu [Azure SQL Intelligent Insights](sql-database-intelligent-insights.md). Ta funkcja udostępnia informacje diagnostyczne i główna przyczyna analizy problemy z wydajnością.
