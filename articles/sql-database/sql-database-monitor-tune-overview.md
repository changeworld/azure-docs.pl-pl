---
title: Monitorowanie i dostrajanie wydajności — usługi Azure SQL Database | Dokumentacja firmy Microsoft
description: Porady dotyczące dostrajania w usłudze Azure SQL Database do oceny i poprawy wydajności.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 2a7a6ed5bd28bcc83500da6e82b6c4ff48b2989c
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62097780"
---
# <a name="monitoring-and-performance-tuning"></a>Monitorowanie i dostrajanie wydajności

Azure SQL Database to usługa danych automatycznie zarządzane i elastyczne, gdzie możesz łatwo monitorować użycie, dodawanie lub usuwanie zasobów (procesor CPU, pamięć, we/wy), zalecenia dotyczące wyszukiwania, których można zwiększyć wydajność bazy danych, lub pozwól bazie danych dostosowywać się do obciążenia i automatycznie zoptymalizować wydajność.

## <a name="monitoring-database-performance"></a>Monitorowanie wydajności bazy danych

Monitorowanie wydajności bazy danych SQL na platformie Azure rozpoczyna się od monitorowania wykorzystania zasobów względem wybranego poziomu wydajności bazy danych. Usługa Azure SQL Database umożliwia określenie możliwości w celu zwiększenia i zoptymalizowania wydajności zapytań bez konieczności zmieniania zasobów, przeglądając [zalecenia dotyczące dostrajania wydajności](sql-database-advisor.md). Częstymi przyczynami słabej wydajności bazy danych są brakujące indeksy i nieprawidłowo zoptymalizowane zapytania. Można zastosować te zalecenia dotyczące dostrajania, aby zwiększyć wydajność przetwarzania obciążenia. Możesz również pozwalają bazy danych Azure SQL, aby [automatycznie zoptymalizować wydajność kwerend](sql-database-automatic-tuning.md) , stosując wszystkie zidentyfikowane zalecenia i weryfikowanie, czy poprawiają wydajność bazy danych.

Masz następujące opcje monitorowania i rozwiązywanie problemów z wydajnością bazy danych:

- W [witryny Azure portal](https://portal.azure.com), kliknij przycisk **baz danych SQL**wybierz bazy danych, a następnie użyć wykres monitorowanie wyszukiwać zasoby zbliża się do ich maksymalnej. Użycie jednostek DTU jest wyświetlana domyślnie. Kliknij przycisk **Edytuj** zmienić zakres czasu i wartości wyświetlane.
- Użyj [Query Performance Insight](sql-database-query-performance.md) można identyfikować zapytania, które możesz wydać na w pełni korzystać z zasobów.
- Użyj [SQL Database Advisor](sql-database-advisor-portal.md) Aby wyświetlić zalecenia dotyczące tworzenia i usuwanie indeksów, parametryzacji zapytań i naprawia problemy ze schematem.
- Użyj [Azure SQL Intelligent Insights](sql-database-intelligent-insights.md) automatyczne monitorowanie wydajności bazy danych. Po wykryciu problemu z wydajnością, dziennik diagnostyczny jest generowany ze szczegółami i głównej przyczyny Analysis (analiza głównej przyczyny) problemu. Zalecenie dotyczące poprawy wydajności znajduje się, gdy jest to możliwe.
- [Włączanie automatycznego dostrajania](sql-database-automatic-tuning-enable.md) i pozwól SQL Azure, bazy danych automatycznego rozwiązywania zidentyfikowanych problemów z wydajnością.
- Użyj [dynamicznych widoków zarządzania (DMV)](sql-database-monitoring-with-dmvs.md), [zdarzeniom rozszerzonym](sql-database-xevent-db-diff-from-svr.md)i [Query Store](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) szczegółowe Rozwiązywanie problemów z wydajnością.

> [!TIP]
> Zobacz [wskazówki dotyczące wydajności](sql-database-performance-guidance.md) można znaleźć technik, które można użyć w celu poprawy wydajności usługi Azure SQL Database po zidentyfikowaniu problemu z wydajnością za pomocą co najmniej jednym z powyższych metod.

## <a name="monitor-databases-using-the-azure-portal"></a>Monitorowanie baz danych za pomocą witryny Azure Portal

W [witryny Azure portal](https://portal.azure.com/), możesz monitorować wykorzystanie pojedynczej bazy danych s wybrać bazę danych, a następnie klikając polecenie **monitorowanie** wykresu. Spowoduje to wyświetlenie okna **Metryka**, które możesz zmienić, klikając przycisk **Edytuj wykres**. Dodaj następujące metryki:

- Procent użycia procesora CPU
- Procent użycia jednostek DTU
- Procent użycia operacji we/wy na danych
- Procent użycia rozmiaru bazy danych

Po dodaniu tych metryk możesz nadal wyświetlać je w **monitorowanie** wykresu z dodatkowymi informacjami na **metryki** okna. Wszystkie cztery metryki pokazują średnią wartość procentową wykorzystania względem jednostek **DTU** bazy danych. Zobacz [modelu zakupu opartego na jednostkach DTU](sql-database-service-tiers-dtu.md) i [modelu zakupu opartego na rdzeniach wirtualnych](sql-database-service-tiers-vcore.md) artykuły, aby uzyskać więcej informacji na temat warstw usługi.  

![Monitorowanie warstw usług pod względem wydajności bazy danych.](./media/sql-database-single-database-monitoring/sqldb_service_tier_monitoring.png)

Możesz również skonfigurować alerty dotyczące metryk wydajności. Kliknij przycisk **Dodaj alert** w oknie **Metryka**. Użyj kreatora, aby skonfigurować alert. Istnieje możliwość ustawienia alertu, który będzie wysyłany, gdy metryki przekroczą określony próg lub spadną poniżej określonego progu.

Jeśli na przykład spodziewasz się, że obciążenie bazy danych wzrośnie, możesz skonfigurować alert polegający na wysłaniu wiadomości e-mail, gdy dowolna z metryk wydajności osiągnie 80%. Umożliwia to jako wczesne ostrzeżenie zorientować się, kiedy może być konieczne przejdź do następnego najwyższy rozmiaru obliczeń.

Metryki wydajności może również pomóc w określeniu, jeśli jesteś w stanie zmiany na starszą niższe rozmiaru obliczeń. Załóżmy, że używasz bazy danych, której warstwa i poziom to Standardowa S2, a wszystkie metryki wydajności pokazują, że baza danych wykorzystuje średnio nie więcej niż 10% w określonym czasie. Istnieje prawdopodobieństwo, że baza danych będzie działać równie dobrze na poziomie Standardowa S1. Jednak należy pamiętać o obciążeniach, które chwilowo wzrastają przed podjęciem decyzji o przeniesieniu na niższym rozmiaru obliczeń lub.

## <a name="troubleshoot-performance-issues"></a>Rozwiązywanie problemów z wydajnością

Aby zdiagnozować i rozwiązać problemy z wydajnością, Rozpocznij od zrozumienia stan każdej aktywnej kwerendy i warunki powodujące problemy z wydajnością istotne dla każdego stanu obciążenia. Aby poprawić wydajność usługi Azure SQL Database, Dowiedz się, że każde żądanie aktywne zapytanie z aplikacji znajduje się w bieżących lub stan oczekujący. Podczas rozwiązywania problemów z wydajnością w usłudze Azure SQL Database, pamiętać poniższej tabeli jako możesz przeczytać ten artykuł, aby zdiagnozować i rozwiązać problemy z wydajnością.

![Stany obciążenia](./media/sql-database-monitor-tune-overview/workload-states.png)

W przypadku obciążeń z problemów z wydajnością, to problem z wydajnością może być spowodowane rywalizacji o zasoby procesora CPU ( **dotyczące uruchamiania** warunku) lub poszczególnych zapytań oczekuje na coś ( **oczekiwania dotyczące** warunku ).

## <a name="running-related-performance-issues"></a>Problemy z wydajnością dotyczące uruchamiania

Ogólną wytyczną wykorzystaniu Procesora jest stale wynosiło co najmniej 80%, przypadku problemów z wydajnością powiązane działania. Jeśli masz problem z dotyczące uruchamiania, może być spowodowany przez niewystarczające zasoby procesora CPU lub może być związany z jednym z następujących warunków:

- Zbyt wiele uruchomionych zapytań
- Zbyt wiele kompilacji zapytań
- Jedna lub więcej wykonywanie kwerend korzystają z planu optymalne zapytania

Jeśli okaże się, że masz problemu z wydajnością dotyczące uruchamiania, celem użytkownika jest identyfikowanie problemu dokładne przy użyciu co najmniej jednej metody. Najczęściej stosowanymi metodami do identyfikowania problemów dotyczących uruchamiania są:

- Użyj [witryny Azure portal](#monitor-databases-using-the-azure-portal) monitorować wykorzystanie procesora CPU w procentach.
- Należy użyć następującego [dynamicznych widoków zarządzania](sql-database-monitoring-with-dmvs.md):

  - [sys.dm_db_resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) zwraca użycie procesora CPU, we/wy i pamięci dla bazy danych Azure SQL Database. Dla co 15 sekund, istnieje jeden wiersz, nawet jeśli nie ma działania w bazie danych. Dane historyczne są utrzymywane przez jedną godzinę.
  - [sys.resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) zwraca dane dotyczące użycia i magazynu procesora CPU dla usługi Azure SQL Database. Dane są zbierane i agregowane w 5 minutowych interwałach.

> [!IMPORTANT]
> Aby uzyskać zestaw zapytań T-SQL, przy użyciu tych widoków DMV, aby rozwiązać problemy dotyczące użycia procesora CPU, zobacz [problemy z wydajnością Procesora zidentyfikować](sql-database-monitoring-with-dmvs.md#identify-cpu-performance-issues).

### <a name="ParamSniffing"></a> Rozwiązywanie problemów z zapytań z problemami z planu wykonania zależne od parametru zapytania

Problem z parametrem plan poufnych (PSP) odwołuje się do scenariusza, w którym Optymalizator zapytań generuje planu wykonania zapytania, który jest optymalna tylko dla określonego parametru wartości (lub zestaw wartości) i buforowanego planu jest następnie optymalnej wartości parametrów używanych w kolejne wykonania. Inne niż optymalne plany następnie może spowodować problemy z wydajnością zapytań i ogólną degradacji przepływności obciążenia. Aby uzyskać więcej informacji na temat wykrywanie parametrów i przetwarzanie zapytań, zobacz [przewodnik dotyczący architektury przetwarzania zapytania](/sql/relational-databases/query-processing-architecture-guide#ParamSniffing).

Istnieje kilka obejść zastosować, aby zminimalizować problemy, każdy z nich skojarzone wady i zalety i wady:

- Użyj [ponownie SKOMPILOWAĆ](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) wskazówki zapytania na wykonanie każdego zapytania. To obejście Zamienia czas kompilacji i zwiększonej wyposażony w Procesor o lepszej jakości planu. Za pomocą `RECOMPILE` opcja często nie jest możliwe w przypadku obciążeń wymagających wysokiej przepływności.
- Użyj [opcji (Optymalizuj pod kątem...) ](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) wskazówki zapytania, aby zastąpić wartość rzeczywistego parametru z wartością typowy parametr, który tworzy plan wystarczające dla większości możliwości wartość parametru.   Ta opcja wymaga dobrej znajomości sposobu optymalne parametry i właściwości skojarzonego planu.
- Użyj [(OPTYMALIZUJ dla nieznany) opcja](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) wskazówki zapytania, aby zastąpić wartość rzeczywistego parametru w zamian za przy użyciu Średnia gęstość wektora. Innym sposobem wykonania tych czynności jest przechwytywanie przychodzących wartości parametrów do zmiennych lokalnych, a następnie używając zmiennych lokalnych w ramach predykaty zamiast przy użyciu parametrów, samodzielnie. Średnia gęstość musi być *wystarczająco dobre* przy użyciu tej określonej poprawki.
- Wyłączyć wykrywanie parametrów, korzystając wyłącznie z [DISABLE_PARAMETER_SNIFFING](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) wskazówki zapytania.
- Użyj [KEEPFIXEDPLAN](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) wskazówki zapytania, aby zapobiec następuje rekompilacja znajduje się w pamięci podręcznej. To rozwiązanie zakłada *dostatecznie dobrej* typowych plan jest już w pamięci podręcznej. Można także wyłączyć aktualizacje automatyczne będą statystyki, aby zmniejszyć prawdopodobieństwo dobrego planu, w której przeprowadzana jest eksmisja i nowy plan zły kompilowanego.
- Wymuś plan za pomocą jawnego [USE PLAN](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) wskazówki zapytania (przez jawne określenie, ustawiając określonego planu przy użyciu Query Store lub przez włączenie [dostrajania automatycznego](sql-database-automatic-tuning.md).
- Zastąp jednej procedury zagnieżdżony zestaw procedur, które mogą być używane na podstawie logikę warunkową i wartości parametrów skojarzonych.
- Tworzenie dynamicznych parametrów alternatywy wykonywania w definicji procedury statyczne.

Aby uzyskać dodatkowe informacje na temat rozwiązywania tego rodzaju problemów Zobacz:

- To [I powąchać parametr](https://blogs.msdn.microsoft.com/queryoptteam/2006/03/31/i-smell-a-parameter/) wpis w blogu
- To [dynamiczny język sql, a plan jakości w zapytaniach parametrycznych](https://blogs.msdn.microsoft.com/conor_cunningham_msft/2009/06/03/conor-vs-dynamic-sql-vs-procedures-vs-plan-quality-for-parameterized-queries/) wpis w blogu
- To [techniki optymalizacji zapytań SQL w programie SQL Server: Parametr Sniffing](https://www.sqlshack.com/query-optimization-techniques-in-sql-server-parameter-sniffing/) wpis w blogu

### <a name="troubleshooting-compile-activity-due-to-improper-parameterization"></a>Rozwiązywanie problemów z działania kompilacji z powodu nieprawidłowej parametryzacji

Gdy zapytanie ma literały, aparat bazy danych zdecyduje się na automatyczne parametryzacja instrukcji albo użytkownik jawnie możecie go w celu zmniejszenia liczby kompiluje. Duża liczba kompiluje zapytania przy użyciu tego samego wzorca, ale różnych wartości literałów może powodować wysokie wykorzystanie procesora CPU. Podobnie jeśli tylko częściowo Definiowanie parametrów zapytania, które mają literałów w dalszym ciągu aparatu bazy danych parametryzuj go dalej.  Poniżej przedstawiono przykład częściowo sparametryzowanych zapytań:

```sql
SELECT * FROM t1 JOIN t2 ON t1.c1 = t2.c1
WHERE t1.c1 = @p1 AND t2.c2 = '961C3970-0E54-4E8E-82B6-5545BE897F8F'
```

W poprzednim przykładem `t1.c1` przyjmuje `@p1` , ale `t2.c2` nadal wykonać GUID jako literał. W tym przypadku, jeśli zmienisz wartość `c2`, zapytanie będzie traktowane jako różne zapytania i nastąpi nowej kompilacji. Aby zmniejszyć kompilacje w poprzedniego przykładu, rozwiązaniem jest również zdefiniować parametry identyfikatora GUID.

Następujące zapytanie Wyświetla liczbę zapytań przez skrót zapytania, aby określić, jeśli zapytanie jest prawidłowo sparametryzowane, lub nie:

```sql
SELECT  TOP 10  
  q.query_hash
  , count (distinct p.query_id ) AS number_of_distinct_query_ids
  , min(qt.query_sql_text) AS sampled_query_text
FROM sys.query_store_query_text AS qt
  JOIN sys.query_store_query AS q
     ON qt.query_text_id = q.query_text_id
  JOIN sys.query_store_plan AS p 
     ON q.query_id = p.query_id
  JOIN sys.query_store_runtime_stats AS rs 
     ON rs.plan_id = p.plan_id
  JOIN sys.query_store_runtime_stats_interval AS rsi
     ON rsi.runtime_stats_interval_id = rs.runtime_stats_interval_id
WHERE
  rsi.start_time >= DATEADD(hour, -2, GETUTCDATE())
  AND query_parameterization_type_desc IN ('User', 'None')
GROUP BY q.query_hash
ORDER BY count (distinct p.query_id) DESC
```

### <a name="resolve-problem-queries-or-provide-more-resources"></a>Rozwiąż problematycznych zapytań lub podać więcej zasobów

Po zidentyfikowaniu problemu, można dostrajanie problematycznych zapytań lub Uaktualnij rozmiar obliczeń lub warstwę można zwiększyć pojemność usługi Azure SQL database w celu ochrony przed rozproszonymi wymagania dotyczące procesora CPU usługi. Aby uzyskać informacje na temat skalowania zasobów dla pojedynczych baz danych, zobacz [skalowanie pojedynczej bazy danych zasobów w usłudze Azure SQL Database](sql-database-single-database-scale.md) i skalowania zasobów dla pul elastycznych, zobacz [skalowanie elastycznej puli zasobów w usłudze Azure SQL Baza danych](sql-database-elastic-pool-scale.md). Aby uzyskać informacje na temat skalowania wystąpienia zarządzanego, zobacz [limity zasobów na poziomie wystąpienia](sql-database-managed-instance-resource-limits.md#instance-level-resource-limits).

### <a name="determine-if-running-issues-due-to-increase-workload-volume"></a>Sprawdza, czy uruchomione problemy ze względu na zwiększenie obciążenia woluminu

Wzrost ruchu aplikacji i obciążeń można uwzględnić zwiększone użycie procesora CPU, ale należy zachować ostrożność prawidłowo zdiagnozować ten problem. W przypadku wysokiej Procesora odpowiedzieć na następujące pytania do określenia, czy rzeczywiście zwiększenie użycia procesora CPU jest z powodu zmiany głośności obciążenia:

1. Czy kwerendy z aplikacji przyczyny tego problemu procesor CPU o wysokiej?
2. Najważniejsze korzystających z procesora CPU zapytań (które mogą być identyfikowane):

   - Ustal, czy wystąpiły wiele planów wykonywania skojarzony z tego samego zapytania. Jeśli tak, należy określić dlaczego.
   - Dla zapytań przy użyciu tego samego planu wykonania należy określić czasy wykonania były spójne i zwiększenie liczby wykonań. Jeśli tak, istnieją problemy z wydajnością prawdopodobnie z powodu wzrostu obciążenia.

Aby podsumować, jeśli planu wykonania zapytania nie wykonać inaczej, ale zwiększyć wykorzystanie procesora CPU, oraz liczby wykonań, prawdopodobnie wystąpił problem z wydajnością związanych z wzrostu obciążenia.

Nie zawsze jest łatwy do zawierania jest zmiana wolumenu obciążenia, umożliwiające obsługę problem procesora CPU.   Czynniki do rozważenia: 

- **Użycie zasobów zmienione**

  Na przykład Rozważmy scenariusz, w którym wzrosła procesora CPU do 80% przez dłuższy czas.  Użycie procesora CPU samodzielnie nie oznacza woluminu obciążenia zmienione.  Wyślij zapytanie do planu wykonania, że regresji i zmiany w zakresie dystrybucji danych można również przyczyniają się do więcej użycia zasobów mimo, że aplikacja wykonuje te same obciążenia dokładne.

- **Pojawiły się nowe zapytanie**

   Aplikacja może dysku nowego zestawu zapytań w różnym czasie.

- **Liczba żądań zwiększanie lub zmniejszanie**

   Ten scenariusz jest najbardziej oczywiste miary obciążenia. Liczba zapytań nie zawsze odpowiada więcej wykorzystanie zasobów. Ta metryka jest jednak nadal sygnał znaczące, przy założeniu, że inne czynniki nie ulegną zmianie.

## <a name="waiting-related-performance-issues"></a>Problemy z wydajnością związanych z oczekiwania

Gdy masz pewność, że nie występują wysoka-procesor CPU, problem z wydajnością związanych z uruchamiania, występują problemu z wydajnością związanych z oczekiwania. To znaczy zasobów procesora CPU nie są używane efektywne, ponieważ Procesor oczekuje na innego zasobu. W tym przypadku następnym krokiem jest do identyfikowania zasobów procesora CPU oczekują na. Najczęstsze metody wyświetlane u góry oczekiwania kategorii typów:

- [Query Store](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) udostępnia statystyki oczekiwania na zapytanie wraz z upływem czasu. W Store zapytania oczekiwania typów są połączone w kategoriach oczekiwania. Mapowanie oczekiwania kategorie typów oczekiwania jest dostępna w [sys.query_store_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql#wait-categories-mapping-table).
- [sys.dm_db_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database) zwraca informacje o wszystkich czeka napotykanych przez wątki, które są wykonywane podczas operacji. Ten widok zagregowane umożliwia diagnozowanie problemów z wydajnością za pomocą usługi Azure SQL Database, a także przy użyciu określonego zapytania i partie.
- [sys.dm_os_waiting_tasks](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) zwraca informacje o kolejce oczekiwania zadań, które oczekują na niektórych zasobów.

W scenariuszach wysokiej Procesora Store zapytań i statystyki oczekiwania nie zawsze odzwierciedla wykorzystanie procesora CPU w tych dwóch powodów:

- Nadal mogą być wykonywane zapytania zużywające procesor CPU o wysokiej i zapytania nie została zakończona.
- Zapytania zużywające procesor CPU o wysokiej były uruchomione po przejściu w tryb failover wystąpił

Query Store oczekiwania śledzenia statystyki dynamicznych widoków zarządzania tylko Pokaż wyniki dla pomyślnie zakończonych i upłynął limit czasu zapytania i nie są wyświetlane dane dotyczące (aż do zakończenia) w trakcie wykonywania instrukcji. Widok dynamiczny zarządzania [sys.dm_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) umożliwia śledzenie aktualnie wykonywanych zapytań i czas skojarzonego procesu roboczego.

Jak pokazano na poprzednim wykresie, czeka najczęściej są:

- Blokady (blokowanie)
- OPERACJE WE/WY
- `tempdb`-związane z rywalizacji o zasoby
- Czeka przydział pamięci

> [!IMPORTANT]
> Dla zestawu zapytań T-SQL, przy użyciu tych widoków DMV do rozwiązywania tych problemów związanych z oczekiwania, zobacz:
>
> - [Identyfikowanie problemów z wydajnością operacji We/Wy](sql-database-monitoring-with-dmvs.md#identify-io-performance-issues)
> - [Identyfikowanie `tempdb` problemy z wydajnością](sql-database-monitoring-with-dmvs.md#identify-io-performance-issues)
> - [Identyfikowanie czeka przydział pamięci](sql-database-monitoring-with-dmvs.md#identify-memory-grant-wait-performance-issues)
> - [TigerToolbox — w tym czasie czeka i zatrzaśnięcia](https://github.com/Microsoft/tigertoolbox/tree/master/Waits-and-Latches)
> - [TigerToolbox - usp_whatsup](https://github.com/Microsoft/tigertoolbox/tree/master/usp_WhatsUp)

## <a name="improving-database-performance-with-more-resources"></a>Poprawa wydajności bazy danych przy użyciu większej ilości zasobów

Na koniec Brak elementów informacje z możliwością działania, które może poprawić wydajność bazy danych, możesz zmienić ilość zasobów dostępnych w usłudze Azure SQL Database. Można przypisać więcej zasobów, zmieniając [warstwy usługi jednostki DTU](sql-database-service-tiers-dtu.md) pojedynczej bazy danych lub zwiększenie jednostek Edtu elastycznej puli w dowolnej chwili. Alternatywnie Jeśli używasz [modelu zakupu opartego na rdzeniach wirtualnych](sql-database-service-tiers-vcore.md), możesz zmienić warstwę usługi lub zwiększenie zasobów przydzielonych do bazy danych.

1. Dla pojedynczych baz danych, możesz [zmienianie warstw usług](sql-database-single-database-scale.md) lub [zasoby obliczeniowe](sql-database-single-database-scale.md) na żądanie w celu poprawy wydajności bazy danych.
2. W przypadku wielu baz danych można rozważyć użycie [pul elastycznych](sql-database-elastic-pool-guidance.md) automatycznie skalować zasoby.

## <a name="tune-and-refactor-application-or-database-code"></a>Dostrajanie i refaktoryzacji aplikacji lub kodu bazy danych

Możesz zmienić kod aplikacji, aby bardziej optymalnie korzystania z bazy danych, zmień indeksów, wymusić plany lub użyj wskazówek, aby ręcznie dostosować bazy danych do obciążenia. Znajdź niektóre wskazówki i porady dotyczące ręcznego dostosowywania i ponowne napisanie kodu w [tematu wskazówki dotyczące wydajności](sql-database-performance-guidance.md) artykułu.

## <a name="next-steps"></a>Kolejne kroki

- Aby włączyć automatyczne dostrajanie w usłudze Azure SQL Database i umożliwiają zarządzanie obciążeniem pracą zapewnia w pełni funkcja dostrajania automatycznego, zobacz [Włączanie automatycznego dostrajania](sql-database-automatic-tuning-enable.md).
- Aby korzystać z ręcznego dostrajania, możesz przejrzeć [dostrajania zalecenia w witrynie Azure portal](sql-database-advisor-portal.md) i ręcznie zastosować te, które poprawić wydajność zapytań.
- Zmienić zasoby, które są dostępne w bazie danych, zmieniając [warstwy usługi Azure SQL Database](sql-database-performance-guidance.md)
