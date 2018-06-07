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
ms.date: 04/01/2018
ms.author: carlrab
ms.openlocfilehash: 44d68d69a7034e80846fb44f3ae26c0d73c61f28
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34648313"
---
# <a name="monitoring-database-performance-in-azure-sql-database"></a>Monitorowanie wydajności bazy danych w usłudze Azure SQL Database
Monitorowanie wydajności bazy danych SQL na platformie Azure rozpoczyna się od monitorowania wykorzystania zasobów względem wybranego poziomu wydajności bazy danych. Monitorowanie pomaga ustalić, czy baza danych ma nadmiarowej pojemności lub czy nie występują problemy, ponieważ limit maksymalnego wykorzystania zasobów, a następnie zdecydować, czy nadszedł czas, aby dostosować poziom wydajności i warstwy bazy danych w usług [na podstawie jednostek dtu w warstwie model kupna](sql-database-service-tiers-dtu.md) lub [na podstawie vCore model kupna (wersja zapoznawcza)](sql-database-service-tiers-vcore.md). Bazę danych można monitorować za pomocą narzędzi graficznych w [witrynie Azure Portal](https://portal.azure.com) lub przy użyciu [dynamicznych widoków zarządzania](https://msdn.microsoft.com/library/ms188754.aspx) SQL.

> [!TIP]
> Użyj [Insights inteligentnego SQL Azure](sql-database-intelligent-insights.md) automatyczne monitorowania wydajności bazy danych. Po wykryciu problemu z wydajnością dzienników diagnostycznych jest generowany ze szczegółami i analizy przyczyny głównej (RCA) problemu. Zalecenie dotyczące poprawy wydajności jest dostępne, gdy jest to możliwe.
>

## <a name="monitor-databases-using-the-azure-portal"></a>Monitorowanie baz danych za pomocą witryny Azure Portal
W witrynie [Azure Portal](https://portal.azure.com/) możesz wybrać bazę danych i kliknąć wykres **Monitorowanie**, aby monitorować wykorzystanie pojedynczej bazy danych. Spowoduje to wyświetlenie okna **Metryka**, które możesz zmienić, klikając przycisk **Edytuj wykres**. Dodaj następujące metryki:

* Procent użycia procesora CPU
* Procent użycia jednostek DTU
* Procent użycia operacji we/wy na danych
* Procent użycia rozmiaru bazy danych

Po dodaniu tych metryk możesz przeglądać je na **monitorowanie** wykres z dodatkowymi informacjami na **Metryka** okna. Wszystkie cztery metryki pokazują średnią wartość procentową wykorzystania względem jednostek **DTU** bazy danych. Zobacz [na podstawie jednostek dtu w warstwie model kupna](sql-database-service-tiers-dtu.md) i [na podstawie vCore model kupna (wersja zapoznawcza)](sql-database-service-tiers-vcore.md) artykuły, aby uzyskać więcej informacji na temat warstwy usług.  

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

### <a name="monitor-resource-use"></a>Monitorowanie użycia zasobów

Można monitorować przy użyciu użycia zasobów [Insight wydajności kwerendy bazy danych SQL](sql-database-query-performance.md) i [magazyn zapytań](https://msdn.microsoft.com/library/dn817826.aspx).

Można również monitorować za pomocą tych dwóch widoków użycia:

* [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx)
* [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx)

#### <a name="sysdmdbresourcestats"></a>sys.dm_db_resource_stats
Można użyć [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) widoku w każdej bazie danych SQL. **Sys.dm_db_resource_stats** widok przedstawia dane wykorzystania zasobów względem warstwy usług. Średni procent dla procesora CPU, dane We/Wy zapisu dziennika i pamięci są rejestrowane co 15 sekund i mają być przechowywane przez godzinę.

Ponieważ ten widok udostępnia szczegółowe przyjrzeć się wykorzystania zasobów, użyj **sys.dm_db_resource_stats** pierwszego dla analizy bieżący stan lub rozwiązywania problemów. Na przykład to zapytanie wyświetla wykorzystanie zasobów średnia i maksymalna w bieżącej bazie danych za pośrednictwem ostatniej godziny:

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

Dla innych zapytań, zobacz przykłady w [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx).

#### <a name="sysresourcestats"></a>sys.resource_stats
[Sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) wyświetlić w **wzorca** bazy danych zawiera dodatkowe informacje, które pomaga monitorować wydajność bazy danych SQL na poziomie warstwy i wydajności określonej usługi. Dane są zbierane co 5 minut i jest zachowywana na potrzeby około 14 dni. Ten widok jest przydatna do długoterminowego historycznej analizy używaniu zasobów bazy danych SQL.

Wykres ukazuje Procesora wykorzystanie zasobów dla bazy danych — warstwa Premium P2 poziom wydajności dla każdej godziny w tygodniu. Ten wykres rozpoczyna się w poniedziałek, pokazuje 5 dni roboczych i następnie przedstawiono weekendy, w przypadku znacznie mniej w aplikacji.

![Wykorzystanie zasobów bazy danych SQL](./media/sql-database-performance-guidance/sql_db_resource_utilization.png)

Z danych, ta baza danych ma obecnie szczytowego obciążenia procesora CPU około 50 procent użycia Procesora względem P2 poziom wydajności (południe wtorek). Jeśli procesora CPU jest współczynnik dominującą w aplikacji profilu zasobu, można zdecydować, czy P2 jest poziom wydajności prawo, aby zagwarantować, że obciążenie zawsze pasuje do. Jeśli aplikacja wraz z upływem czasu, jest warto mieć bufor dodatkowych zasobów, tak aby aplikacja nigdy nie osiągnie limit poziom wydajności. Jeśli możesz zwiększyć poziom wydajności, możesz pomóc uniknąć błędów widoczne klienta, które mogą wystąpić, gdy baza danych nie ma wystarczająco dużo mocy do przetwarzania żądań skutecznie, szczególnie w środowiskach wrażliwy na opóźnienia. Przykładem jest bazy danych, która obsługuje aplikację, która umożliwia malowanie stron sieci Web na podstawie wyników wywołania bazy danych.

Inne typy aplikacji może interpretować tego samego wykresu. Na przykład jeśli aplikacja próbuje przetworzyć danych Lista płac każdego dnia i tego samego wykresu, tego rodzaju modelu "zadania wsadowego" może poradzić na poziom wydajności P1. Poziom wydajności P1 ma 100 jednostek Dtu w porównaniu do 200 Dtu na poziomie wydajności P2. Poziom wydajności P1 zapewnia połowa wydajność P2 poziomu wydajności. Tak 50 procent użycia procesora CPU w P2 jest równy 100 procent użycia procesora CPU w P1. Aplikacja nie ma przekroczeń limitu czasu, może być niezależnie od tego Jeśli zadanie ma 2 godziny lub 2,5 godzin, jeżeli pobiera dzisiaj. Prawdopodobnie aplikacji w tej kategorii można użyć poziomu wydajności P1. Można korzystać z faktu, że są okresów w ciągu dnia, gdy wykorzystanie zasobów jest starsza, tak, aby wszelkie "big szczytu" może być zostaną przeniesione za pośrednictwem do jednego z koryta później w dniu. Poziom wydajności P1 może być dobrym dla tego typu aplikacji (i Zapisz pieniędzy), tak długo, jak zakończyć zadania na czas każdego dnia.

Udostępnia bazę danych SQL Azure używane informacje o zasobie dla każdej bazy danych dla aktywnej w **sys.resource_stats** widoku **wzorca** bazy danych w każdym serwerze. Dane w tabeli są agregowane dla 5-minutowy. Z warstwy usług podstawowa, standardowa i Premium danych może być więcej niż 5 minut pojawią się w tabeli, tak aby bardziej użyteczna w przypadku historycznej analizy, a nie w pobliżu czasie rzeczywistym analizy danych. Zapytanie **sys.resource_stats** Wyświetl, aby wyświetlić najnowszą historię bazy danych i do zweryfikowania czy rezerwacji została wybrana opcja dostarczonych wydajności będą w razie potrzeby.

> [!NOTE]
> Musi być podłączony do **wzorca** bazy danych logicznych serwera bazy danych SQL w zapytaniu **sys.resource_stats** w poniższych przykładach.
> 
> 

Ten przykład przedstawia, jak jest widoczne dane w tym widoku:

    SELECT TOP 10 *
    FROM sys.resource_stats
    WHERE database_name = 'resource1'
    ORDER BY start_time DESC

![W widoku wykazu sys.resource_stats](./media/sql-database-performance-guidance/sys_resource_stats.png)

W kolejnym przykładzie pokazano różne sposoby, w którym można **sys.resource_stats** widoku w celu uzyskania informacji o używaniu zasobów w bazie danych SQL w katalogu:

1. Aby przyjrzeć się w poprzednim tygodniu zasobów dla userdb1 bazy danych, można uruchomić tego zapytania:
   
        SELECT *
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND
              start_time > DATEADD(day, -7, GETDATE())
        ORDER BY start_time DESC;
2. Aby ocenić, jak również obciążenie pasuje do poziomu wydajności, należy przejść do każdego aspektu zasobu metryki: procesora CPU, odczyty zapisy, liczbę procesów roboczych i liczba sesji. Oto poprawione zapytań przy użyciu **sys.resource_stats** zgłoszenia średniego i maksymalnego wartości tych metryk zasobów:
   
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
3. Dzięki tym informacjom o wartości średnia i maksymalna każdego zasobu metryki można ocenić, jak obciążenie jest dopasowywana do wybranego poziomu wydajności. Zazwyczaj średnie wartości z **sys.resource_stats** zapewniają dobrą linii bazowej przeciwko rozmiar docelowy. Należy go z dysku podstawowego miary. Na przykład być może używasz warstwie usług standardowa S2 poziom wydajności. Średnią Użyj wartości procentowe dla procesora CPU i we/wy odczyty i zapisy są poniżej 40 procent, średnia liczba procesów roboczych jest poniżej 50, a średnią liczbę sesji jest poniżej 200. Obciążenie może mieści się w poziomie wydajności S1. Jest łatwo sprawdzić, czy baza danych mieści się w granicach sesji i proces roboczy. Aby zobaczyć, czy bazy danych mieści się na niższy poziom wydajności w odniesieniu do Procesora, odczyty i zapisy, dzielenie liczby jednostek dtu w warstwie niższej wydajności według liczby jednostek dtu w warstwie aktualny poziom wydajności, a następnie pomnożyć wynik przez 100:
   
    **S1 DTU / S2 DTU * 100 = 20 / 50 * 100 = 40**
   
    Wynik różni się względną wydajność poziomy wydajności dwóch w procentach. Jeśli Twoje użycie zasobów nie przekracza tę wartość, obciążenie może pasuje do niższej wydajności. Jednak należy przyjrzeć się wszystkie zakresy wartości użycia zasobów i określić wartości procentowej, jak często obciążenie bazy danych mieści się na niższym poziomie wydajności. Następujące zapytanie Wyświetla dopasowania procent na wymiarze zasobów do progu 40 procent, możemy obliczona w tym przykładzie:
   
        SELECT
            (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU Fit Percent'
            ,(COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log Write Fit Percent'
            ,(COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical Data IO Fit Percent'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
   
    Oparty na celu poziomu usługi (SLO) bazy danych, można zdecydować, czy obciążenie jest dopasowywana do niższej wydajności. Jeśli obciążenie bazy danych usługi jest 99,9 procent i poprzedniego zapytania zwraca wartości większe niż 99,9% dla wszystkich wymiarów zasobów, obciążenie może pasuje do niższej wydajności.
   
    Spojrzenie na dopasowania procent daje również wgląd w Określa, czy należy przenieść dalej wyższy poziom wydajności spełnia Twoje SLO. Na przykład userdb1 przedstawia użycie procesora CPU w poprzednim tygodniu:
   
   | Średni procent procesora CPU | Maksymalny procent procesora CPU |
   | --- | --- |
   | 24.5 |100.00 |
   
    Średnie wykorzystanie Procesora jest o kwartału limitu poziom wydajności, który mieści się na poziom wydajności bazy danych. Jednak wartość maksymalna pokazuje bazy danych nie osiągnie limitu poziom wydajności. Czy trzeba przenieść dalej wyższy poziom wydajności? Zobacz, jak wiele razy z osiągnie obciążenia 100 procent, a następnie porównaj je z SLO obciążenie bazy danych.
   
        SELECT
        (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU fit percent'
        ,(COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log write fit percent'
        ,(COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical data IO fit percent'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
   
    Jeśli ta kwerenda zwraca wartość mniej niż 99,9% dla każdego z wymiarów zasobów, rozważ przeniesienie albo dalej wyższy poziom wydajności lub obniżyć obciążenie bazy danych SQL za pomocą techniki Dostrajanie aplikacji.
4. Tego ćwiczenia uwzględnia również zwiększenia Twojej planowane obciążenie w przyszłości.

W przypadku pul elastycznych można monitorować pojedyncze bazy danych w puli za pomocą metod opisanych w tej sekcji. Można jednak również monitorować pulę jako całość. Informacje na ten temat znajdziesz w artykule [Monitor and manage an elastic pool](sql-database-elastic-pool-manage-portal.md) (Monitorowanie puli elastycznej i zarządzanie nią).


### <a name="maximum-concurrent-requests"></a>Maksymalna liczba równoczesnych żądań
Aby wyświetlić liczbę jednoczesnych żądań, uruchom to zapytanie języka Transact-SQL w bazie danych SQL:

    SELECT COUNT(*) AS [Concurrent_Requests]
    FROM sys.dm_exec_requests R

Do analizowania na obciążenie związane z lokalną bazą danych programu SQL Server, zmodyfikuj to zapytanie, aby odfiltrować określonej bazy danych, do przeanalizowania. Na przykład jeśli masz lokalną bazą danych o nazwie mojabazadanych tego zapytania języka Transact-SQL zwraca liczbę jednoczesnych żądań w tej bazie danych:

    SELECT COUNT(*) AS [Concurrent_Requests]
    FROM sys.dm_exec_requests R
    INNER JOIN sys.databases D ON D.database_id = R.database_id
    AND D.name = 'MyDatabase'

To właśnie migawki w jednym punkcie w czasie. Aby uzyskać lepsze zrozumienie wymagań dotyczących równoczesnych żądań i obciążenia, na których, należy zebrać wiele próbek w czasie.

### <a name="maximum-concurrent-logins"></a>Maksymalna równoczesnych logowania
Można analizować Twoich wzorców użytkownika i aplikacji, aby poznać częstotliwość logowania. Można również uruchomić rzeczywistych obciążeń w środowisku testowym, aby upewnić się, że użytkownik jest nie naciśnięcie to lub inne ograniczenia, które omówiono w tym artykule. Nie ma jednego zapytania lub widoku dynamicznego zarządzania (DMV), który można wyświetlić równoczesnych się, że liczba logowania lub historii.

Jeśli wielu klientów używać tych samych parametrach połączenia, usługa uwierzytelnia każdego logowania. Jeśli 10 użytkowników jednocześnie połączyć się z bazą danych przy użyciu tej samej nazwy użytkownika i hasła, może to być 10 równoczesnych logowania. Ten limit dotyczy tylko w czasie trwania logowania i uwierzytelniania. Tej samej 10 użytkowników połączenia z bazą danych po kolei, liczba równoczesnych logowań nigdy nie będzie większa niż 1.

> [!NOTE]
> Obecnie ten limit nie ma zastosowania do bazy danych w puli elastycznej.
> 
> 

### <a name="maximum-sessions"></a>Maksymalna liczba sesji
Aby wyświetlić liczbę bieżące aktywne sesje, uruchom to zapytanie języka Transact-SQL w bazie danych SQL:

    SELECT COUNT(*) AS [Sessions]
    FROM sys.dm_exec_connections

Jeśli jest analiza obciążenia pracą lokalnego programu SQL Server, zmodyfikuj zapytanie, aby skupić się na określonej bazy danych. To zapytanie ułatwia określenie potrzeb możliwe sesji dla bazy danych, planując przeniesienie ich do bazy danych SQL Azure.

    SELECT COUNT(*)  AS [Sessions]
    FROM sys.dm_exec_connections C
    INNER JOIN sys.dm_exec_sessions S ON (S.session_id = C.session_id)
    INNER JOIN sys.databases D ON (D.database_id = S.database_id)
    WHERE D.name = 'MyDatabase'

Ponownie tych zapytań Zwróć liczba punktu w czasie. W przypadku zebrania wielu próbkach wraz z upływem czasu, będziesz mieć najlepsze zrozumienia sesji, użyj.

Analizy bazy danych SQL, historycznych statystyki można uzyskać na sesje, badając [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) widoku i przeglądanie **active_session_count** kolumny. 

## <a name="next-steps"></a>Kolejne kroki

- Automatycznie Dostosuj indeksy bazy danych i zapytania przy użyciu planów wykonania [automatycznego dostrajania bazy danych SQL Azure](sql-database-automatic-tuning.md).
- Monitorowanie wydajności bazy danych automatycznie za pomocą [Insights inteligentnego SQL Azure](sql-database-intelligent-insights.md). Ta funkcja udostępnia informacje diagnostyczne i analiza problemów z wydajnością przyczyna.
