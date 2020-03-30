---
title: Typy problemów z wydajnością kwerend w bazie danych SQL usługi Azure
description: W tym artykule dowiesz się o typach problemów z wydajnością kwerend w usłudze Azure SQL Database, a także dowiedz się, jak identyfikować i rozwiązywać kwerendy z tymi problemami
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: jrasnick, carlrab
ms.date: 03/10/2020
ms.openlocfilehash: e155321c2727bf4ee871ef7be7b61b6a523ec1fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256135"
---
# <a name="detectable-types-of-query-performance-bottlenecks-in-azure-sql-database"></a>Wykrywalne typy wąskich gardeł wydajności zapytań w bazie danych SQL azure

Podczas próby rozwiązania wąskiego gardła wydajności, należy rozpocząć od określenia, czy wąskie gardło występuje, gdy kwerenda jest w stanie uruchomionym lub oczekiwania. W zależności od tego określenia stosuje się różne rozdzielczości. Użyj poniższego diagramu, aby zrozumieć czynniki, które mogą powodować problem związany z uruchomieniem lub problem związany z oczekiwaniem. Problemy i rozwiązania dotyczące każdego typu problemu są omówione w tym artykule.

Za pomocą usługi Azure SQL Database [Intelligent Insights](sql-database-intelligent-insights-troubleshoot-performance.md#detectable-database-performance-patterns) lub SQL Server [DMV](sql-database-monitoring-with-dmvs.md) można wykryć tego typu wąskie gardła wydajności.

![Stany obciążenia pracą](./media/sql-database-monitor-tune-overview/workload-states.png)

**Problemy związane z bieganiem:** Problemy związane z uruchamianiem są zazwyczaj związane z problemami kompilacji, co powoduje nieoptymalny plan kwerend lub problemy z wykonywaniem związane z niewystarczającymi lub nadużywanymi zasobami.
**Problemy związane z czekaniem**: Problemy związane z czekaniem są zazwyczaj związane z:

- Zamki (blokowanie)
- WE/WY
- Rywalizacja związana z użyciem bazy danych TempDB
- Dotacja pamięci czeka

## <a name="compilation-problems-resulting-in-a-suboptimal-query-plan"></a>Problemy z kompilacją skutkujące nieoptymalnym planem zapytań

Nieoptymalny plan wygenerowany przez optymalizator zapytań SQL może być przyczyną powolnej wydajności kwerendy. Optymalizator zapytań SQL może spowodować nieoptymalny plan z powodu brakującego indeksu, starych statystyk, niepoprawnego oszacowania liczby wierszy do przetworzenia lub niedokładnego oszacowania wymaganej pamięci. Jeśli wiesz, że kwerenda została wykonana szybciej w przeszłości lub w innym wystąpieniu (wystąpienie zarządzane lub wystąpienie programu SQL Server), porównaj rzeczywiste plany wykonania, aby sprawdzić, czy są one różne.

- Identyfikowanie brakujących indeksów przy użyciu jednej z następujących metod:

  - Użyj [inteligentnego wglądu w dane szczegółowe](sql-database-intelligent-insights-troubleshoot-performance.md#missing-index).
  - [Doradca bazy danych](sql-database-advisor.md) dla pojedynczych i pulowych baz danych.
  - Dmvs. W tym przykładzie pokazano wpływ brakującego indeksu, jak wykryć [brakujące indeksy](sql-database-performance-guidance.md#identifying-and-adding-missing-indexes) przy użyciu dmvs i wpływ implementowania zalecenia brakujące indeksu.
- Spróbuj zastosować [wskazówki dotyczące kwerend,](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) [zaktualizować statystyki](https://docs.microsoft.com/sql/t-sql/statements/update-statistics-transact-sql)lub [odbudować indeksy,](https://docs.microsoft.com/sql/relational-databases/indexes/reorganize-and-rebuild-indexes) aby uzyskać lepszy plan. Włącz [automatyczną korektę planu](sql-database-automatic-tuning.md) w usłudze Azure SQL Database, aby automatycznie ograniczyć te problemy.

  W tym [przykładzie](sql-database-performance-guidance.md#query-tuning-and-hinting) pokazano wpływ nieoptymalnego planu kwerendy z powodu sparametryzowanej kwerendy, jak wykryć ten warunek i jak użyć wskazówki kwerendy, aby rozwiązać.

- Spróbuj zmienić poziom zgodności bazy danych i zaimplementowanie inteligentnego przetwarzania zapytań. Optymalizator zapytań SQL może generować inny plan kwerend w zależności od poziomu zgodności bazy danych. Wyższe poziomy zgodności zapewniają bardziej [inteligentne możliwości przetwarzania zapytań.](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing)

  - Aby uzyskać więcej informacji na temat przetwarzania kwerend, zobacz [Przewodnik architektury przetwarzania zapytań](https://docs.microsoft.com/sql/relational-databases/query-processing-architecture-guide).
  - Aby zmienić poziomy zgodności bazy danych i dowiedzieć się więcej o różnicach między poziomami zgodności, zobacz [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level).
  - Aby dowiedzieć się więcej o szacowaniu kardynalności, zobacz [Oszacowanie kardynalności](https://docs.microsoft.com/sql/relational-databases/performance/cardinality-estimation-sql-server)

## <a name="resolving-queries-with-suboptimal-query-execution-plans"></a>Rozwiązywanie zapytań za pomocą nieoptymalnych planów wykonywania zapytań

W poniższych sekcjach omówiono sposób rozwiązywania zapytań za pomocą planu wykonania kwerend nieoptymalnych.

### <a name="queries-that-have-parameter-sensitive-plan-psp-problems"></a><a name="ParamSniffing"></a>Kwerendy z problemami z planem zależnym od parametrów (PSP)

Problem z planem zależnym od parametrów (PSP) występuje, gdy optymalizator kwerend generuje plan wykonania kwerendy, który jest optymalny tylko dla określonej wartości parametru (lub zestawu wartości), a buforowany plan nie jest optymalny dla wartości parametrów, które są używane w kolejnych Egzekucji. Plany, które nie są optymalne, mogą następnie powodować problemy z wydajnością kwerend i obniżać ogólną przepływność obciążenia.

Aby uzyskać więcej informacji na temat wąchania parametrów i przetwarzania zapytań, zobacz [przewodnik po architekturze przetwarzania zapytań](/sql/relational-databases/query-processing-architecture-guide#ParamSniffing).

Kilka obejść może ograniczyć problemy psp. Każde obejście ma związane z nimi wady i wady:

- Użyj wskazówki [kwerendy RECOMPILE](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) przy każdym wykonaniu kwerendy. To obejście handluje czasem kompilacji i zwiększonym procesorem CPU w celu uzyskania lepszej jakości planu. Opcja `RECOMPILE` często nie jest możliwa dla obciążeń, które wymagają wysokiej przepływności.
- Użyj wskazówki [kwerendy OPTION (OPTIMIZE FOR...),](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) aby zastąpić rzeczywistą wartość parametru typową wartością parametru, która tworzy plan, który jest wystarczająco dobry dla większości możliwości wartości parametru. Ta opcja wymaga dobrego zrozumienia optymalnych wartości parametrów i skojarzonych cech planu.
- Użyj wskazówki [kwerendy OPTION (OPTIMIZE FOR UNKNOWN),](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) aby zastąpić rzeczywistą wartość parametru i zamiast tego użyć średniej wektorowej gęstości. Można to również zrobić, przechwytując wartości parametrów przychodzących w zmiennych lokalnych, a następnie przy użyciu zmiennych lokalnych w predykatach zamiast przy użyciu samych parametrów. Dla tej poprawki średnia gęstość musi być *wystarczająco dobra*.
- Wyłącz parametr wąchania całkowicie za pomocą [DISABLE_PARAMETER_SNIFFING](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) wskazówka kwerendy.
- Użyj wskazówki dotyczącej kwerendy [KEEPFIXEDPLAN,](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) aby zapobiec ponownej kompilacji w pamięci podręcznej. To obejście zakłada, że wystarczająco dobry wspólny plan jest już w pamięci podręcznej. Można również wyłączyć automatyczne aktualizacje statystyk, aby zmniejszyć szanse, że dobry plan zostanie eksmitowany i nowy zły plan zostanie skompilowany.
- Wymusz plan, jawnie używając wskazówki kwerendy [USE PLAN,](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) przepisując kwerendę i dodając wskazówkę w tekście kwerendy. Możesz też ustawić określony plan za pomocą Magazynu zapytań lub włączając [automatyczne dostrajanie](sql-database-automatic-tuning.md).
- Zastąp pojedynczą procedurę zagnieżdżoną zestawem procedur, z których każda może być używana na podstawie logiki warunkowej i skojarzonych wartości parametrów.
- Tworzenie dynamicznych alternatyw wykonywania ciągów do definicji procedury statycznej.

Aby uzyskać więcej informacji na temat rozwiązywania problemów z psp, zobacz następujące wpisy w blogu:

- [Czuję parametr](https://docs.microsoft.com/archive/blogs/queryoptteam/i-smell-a-parameter)
- [Conor vs. dynamiczny SQL vs. procedury a jakość planu dla sparametryzowanych zapytań](https://blogs.msdn.microsoft.com/conor_cunningham_msft/2009/06/03/conor-vs-dynamic-sql-vs-procedures-vs-plan-quality-for-parameterized-queries/)
- [Techniki optymalizacji zapytań SQL w programie SQL Server: wąchanie parametrów](https://www.sqlshack.com/query-optimization-techniques-in-sql-server-parameter-sniffing/)

### <a name="compile-activity-caused-by-improper-parameterization"></a>Skompilowanie działania spowodowanego nieprawidłową parametryzacją

Gdy kwerenda ma literały, aparat bazy danych automatycznie parametryzuje instrukcję lub użytkownik jawnie parametryzuje instrukcję, aby zmniejszyć liczbę kompilacji. Duża liczba kompilacji dla kwerendy przy użyciu tego samego wzorca, ale różne wartości literału może spowodować wysokie użycie procesora CPU. Podobnie, jeśli tylko częściowo sparametryzować kwerendę, która nadal ma literały, aparat bazy danych nie parametryzuje kwerendy dalej.

Oto przykład częściowo sparametryzowanej kwerendy:

```sql
SELECT *
FROM t1 JOIN t2 ON t1.c1 = t2.c1
WHERE t1.c1 = @p1 AND t2.c2 = '961C3970-0E54-4E8E-82B6-5545BE897F8F'
```

W tym `t1.c1` przykładzie `@p1` `t2.c2` przyjmuje , ale nadal przyjmuje identyfikator GUID jako literał. W takim przypadku, jeśli zmienisz wartość dla `c2`, kwerenda jest traktowana jako inna kwerenda i nastąpi nowa kompilacja. Aby zmniejszyć kompilacje w tym przykładzie, należy również sparametryzować identyfikator GUID.

Następująca kwerenda przedstawia liczbę zapytań według skrótu kwerendy w celu ustalenia, czy kwerenda jest poprawnie sparametryzowana:

```sql
SELECT TOP 10
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

### <a name="factors-that-affect-query-plan-changes"></a>Czynniki wpływające na zmiany planu kwerend

Ponowna kompilacja planu wykonania kwerendy może spowodować wygenerowany plan kwerendy, który różni się od oryginalnego planu buforowanego. Istniejący pierwotny plan może zostać automatycznie ponownie skompilowany z różnych powodów:

- Zmiany w schemacie są odwoływane przez kwerendę
- Zmiany danych w tabelach są odwoływane przez kwerendę
- Zmieniono opcje kontekstu kwerendy

Skompilowany plan może zostać wyrzucony z pamięci podręcznej z różnych powodów, takich jak:

- Wystąpienie jest ponownie uruchamiane
- Zmiany konfiguracji o zakresie bazy danych
- Wykorzystanie pamięci
- Jawne żądania wyczyszczenie pamięci podręcznej

Jeśli używasz wskazówki RECOMPILE, plan nie będzie buforowany.

Ponowna kompilacja (lub kompilacja fresh po eksmisji pamięci podręcznej) może nadal spowodować generowanie planu wykonywania kwerendy, który jest identyczny z oryginałem. Gdy plan zmienia się w stosunku do poprzedniego lub pierwotnego planu, te wyjaśnienia są prawdopodobne:

- **Zmieniono projekt fizyczny:** Na przykład nowo utworzone indeksy skuteczniej obejmują wymagania kwerendy. Nowe indeksy mogą być używane w nowej kompilacji, jeśli optymalizator kwerendy zdecyduje, że przy użyciu tego nowego indeksu jest bardziej optymalne niż przy użyciu struktury danych, który został pierwotnie wybrany dla pierwszej wersji wykonywania kwerendy. Wszelkie zmiany fizyczne do obiektów, do których istnieje odwołanie może spowodować nowy wybór planu w czasie kompilacji.

- **Różnice zasobów serwera:** Gdy plan w jednym systemie różni się od planu w innym systemie, dostępność zasobów, takich jak liczba dostępnych procesorów, może mieć wpływ na to, który plan zostanie wygenerowany. Na przykład jeśli jeden system ma więcej procesorów, można wybrać plan równoległy.

- **Różne statystyki:** Statystyki skojarzone z obiektami, do których istnieją odwołania, mogły ulec zmianie lub mogły się istotnie różnić od statystyk oryginalnego systemu. Jeśli statystyki zmienić i rekompilacji dzieje, optymalizator kwerendy używa statystyk, począwszy od kiedy zostały zmienione. Skorygowane rozkłady i częstotliwości danych statystycznych mogą różnić się od tych z pierwotnej kompilacji. Zmiany te są używane do tworzenia szacunków kardynalność. (Szacunki*kardynalności* to liczba wierszy, które mają przepływać przez drzewo zapytań logicznych). Zmiany w szacowaności kardynalności może prowadzić do wyboru różnych operatorów fizycznych i skojarzonych zleceń operacji. Nawet niewielkie zmiany w statystykach może spowodować zmieniony plan wykonania kwerendy.

- **Zmieniono poziom zgodności bazy danych lub wersję estymatora kardynalności:** Zmiany na poziomie zgodności bazy danych mogą włączyć nowe strategie i funkcje, które mogą spowodować inny plan wykonania kwerendy. Poza poziomem zgodności bazy danych, flaga śledzenia wyłączone lub włączone 4199 lub zmieniony stan konfiguracji o zakresie bazy danych QUERY_OPTIMIZER_HOTFIXES mogą również wpływać na wybór planu wykonywania kwerend w czasie kompilacji. Flagi śledzenia 9481 (siła starsza CE) i 2312 (wymuszanie domyślnej CE) również wpływają na plan.

## <a name="resource-limits-issues"></a>Problemy z limitami zasobów

Powolna wydajność kwerendy niezwiązane z nieoptymalnymi planami kwerend i brakami indeksów są zazwyczaj związane z niewystarczającymi lub nadużywającymi zasobami. Jeśli plan kwerend jest optymalny, kwerenda (i baza danych) może być uderzenie limitów zasobów dla bazy danych, puli elastycznej lub wystąpienia zarządzanego. Przykładem może być nadmiar przepływności zapisu dziennika dla poziomu usługi.

- Wykrywanie problemów z zasobami przy użyciu witryny Azure portal: Aby sprawdzić, czy problemem są limity zasobów, zobacz [Monitorowanie zasobów bazy danych SQL](sql-database-monitor-tune-overview.md#sql-database-resource-monitoring). W przypadku pojedynczych baz danych i pul elastycznych zobacz [Zalecenia dotyczące wydajności Klasyfikatora baz danych](sql-database-advisor.md) i Szczegółowe informacje o wydajności [kwerend](sql-database-query-performance.md).
- Wykrywanie limitów zasobów przy użyciu [aplikacji Intelligent Insights](sql-database-intelligent-insights-troubleshoot-performance.md#reaching-resource-limits)
- Wykrywanie problemów z zasobami przy użyciu [dmv:](sql-database-monitoring-with-dmvs.md)

  - [Plik sys.dm_db_resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) DMV zwraca zużycie procesora CPU, we/wy i pamięci dla bazy danych SQL. Jeden wiersz istnieje dla każdego interwału 15-sekundowego, nawet jeśli w bazie danych nie ma żadnych działań. Dane historyczne są przechowywane przez godzinę.
  - [Plik sys.resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) DMV zwraca dane użycia procesora i magazynu dla usługi Azure SQL Database. Dane są zbierane i agregowane w odstępach pięciu minut.
  - [Wiele pojedynczych zapytań, które łącznie zużywają wysoki poziom procesora](sql-database-monitoring-with-dmvs.md#many-individual-queries-that-cumulatively-consume-high-cpu)
  - 

Jeśli problem zostanie zidentyfikować jako za mało zasobów, można uaktualnić zasoby, aby zwiększyć pojemność bazy danych SQL, aby wchłonąć wymagania procesora CPU. Aby uzyskać więcej informacji, zobacz [Skalowanie zasobów pojedynczej bazy danych w bazie danych SQL azure](sql-database-single-database-scale.md) i [skalowanie zasobów puli elastycznej w bazie danych SQL azure.](sql-database-elastic-pool-scale.md) Aby uzyskać informacje dotyczące skalowania wystąpienia zarządzanego, zobacz [Limity zasobów warstwy usług](sql-database-managed-instance-resource-limits.md#service-tier-characteristics).

## <a name="performance-problems-caused-by-increased-workload-volume"></a>Problemy z wydajnością spowodowane zwiększoną liczbą obciążeń

Zwiększenie ruchu aplikacji i woluminu obciążenia może spowodować zwiększone użycie procesora CPU. Ale musisz uważać, aby prawidłowo zdiagnozować ten problem. Gdy pojawi się problem z wysokim procesorem, odpowiedz na te pytania, aby ustalić, czy wzrost jest spowodowany zmianami w woluminie obciążenia:

- Czy zapytania z aplikacji są przyczyną problemu z wysokim procesorem CPU?
- W przypadku [najlepszych zapytań zużywających procesor, które można zidentyfikować:](sql-database-monitoring-with-dmvs.md#the-cpu-issue-occurred-in-the-past)

  - Czy z tą samą kwerendą skojarzono wiele planów wykonania? Jeśli tak, to dlaczego?
  - W przypadku zapytań z tym samym planem wykonania, czy czasy wykonywania były spójne? Czy liczba wykonań wzrosła? Jeśli tak, wzrost obciążenia jest prawdopodobnie przyczyną problemów z wydajnością.

Podsumowując, jeśli plan wykonywania kwerendy nie wykonać inaczej, ale użycie procesora CPU wzrosła wraz z liczbą wykonania, problem z wydajnością jest prawdopodobnie związane ze wzrostem obciążenia.

Nie zawsze jest łatwo zidentyfikować zmianę głośności obciążenia, która napędza problem z procesorem. Należy wziąć pod uwagę następujące czynniki:

- **Zmieniono użycie zasobów:** Rozważmy na przykład scenariusz, w którym użycie procesora CPU wzrosło do 80 procent przez dłuższy okres czasu. Samo użycie procesora CPU nie oznacza zmiany woluminu obciążenia. Regresje w planie wykonywania kwerend i zmiany w dystrybucji danych może również przyczynić się do większego użycia zasobów, nawet jeśli aplikacja wykonuje to samo obciążenie.

- **Wygląd nowej kwerendy:** Aplikacja może napędzać nowy zestaw zapytań w różnym czasie.

- **Zwiększenie lub zmniejszenie liczby żądań:** Ten scenariusz jest najbardziej oczywistą miarą obciążenia pracą. Liczba zapytań nie zawsze odpowiada większe wykorzystanie zasobów. Jednak ta metryka jest nadal znaczącym sygnałem, przy założeniu, że inne czynniki pozostają niezmienione.

Użyj funkcji Intelligent Insights, aby wykryć [zwiększenie obciążenia](sql-database-intelligent-insights-troubleshoot-performance.md#workload-increase) i [zaplanować regresji.](sql-database-intelligent-insights-troubleshoot-performance.md#plan-regression)

## <a name="waiting-related-problems"></a>Problemy związane z oczekiwaniem

Po wyeliminowaniu planu nieoptymalnego i problemów *związanych z oczekiwaniem,* które są związane z problemami z wykonywaniem, problem z wydajnością jest zazwyczaj kwerendy prawdopodobnie czekają na jakiś zasób. Problemy związane z oczekiwaniem mogą być spowodowane przez:

- **Blokowanie**:

  Jedna kwerenda może zawierać blokadę obiektów w bazie danych, podczas gdy inne próbują uzyskać dostęp do tych samych obiektów. Zapytania blokujące można zidentyfikować za pomocą [modułów DMV](sql-database-monitoring-with-dmvs.md#monitoring-blocked-queries) lub [aplikacji Intelligent Insights](sql-database-intelligent-insights-troubleshoot-performance.md#locking).
- **Problemy z io**

  Kwerendy mogą czekać na strony, które mają być zapisywane w plikach danych lub dziennika. W takim przypadku `INSTANCE_LOG_RATE_GOVERNOR`sprawdź `WRITE_LOG`statystyki `PAGEIOLATCH_*` , , lub oczekiwania w DMV. Zobacz korzystanie z dmvs do [identyfikacji problemów z wydajnością we/wy](sql-database-monitoring-with-dmvs.md#identify-io-performance-issues).
- **Problemy z bazą danych TempDB**

  Jeśli obciążenie używa tabel tymczasowych lub istnieją wycieki bazy danych TempDB w planach, kwerendy mogą mieć problem z przepływnością bazy danych TempDB. Zobacz używanie dmvs do [tożsamości TempDB problemy](sql-database-monitoring-with-dmvs.md#identify-tempdb-performance-issues).
- **Problemy związane z pamięcią**

  Jeśli obciążenie nie ma wystarczającej ilości pamięci, średnia długość życia strony może spaść lub zapytania mogą uzyskać mniej pamięci niż potrzebują. W niektórych przypadkach wbudowana inteligencja w Optymalizatorze zapytań rozwiąże problemy związane z pamięcią. Zobacz za pomocą DMVs do [identyfikowania problemów dotacji pamięci](sql-database-monitoring-with-dmvs.md#identify-memory-grant-wait-performance-issues).

### <a name="methods-to-show-top-wait-categories"></a>Metody pokazywania najlepszych kategorii oczekiwania

Metody te są często używane do pokazywania najlepszych kategorii typów oczekiwania:

- Użyj inteligentnej analizy do identyfikowania zapytań z pogorszeniem wydajności z powodu [zwiększonego oczekiwania](sql-database-intelligent-insights-troubleshoot-performance.md#increased-wait-statistic)
- Użyj [Magazynu zapytań,](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) aby znaleźć statystyki oczekiwania dla każdej kwerendy w czasie. W Magazynie zapytań typy oczekiwania są łączone w kategorie oczekiwania. Mapowanie kategorii oczekiwania można czekać na [typy w pliku sys.query_store_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql#wait-categories-mapping-table).
- Użyj [sys.dm_db_wait_stats,](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database) aby zwrócić informacje o wszystkich oczekiwaniach napotkanych przez wątki, które zostały wykonane podczas operacji kwerendy. Ten widok zagregowany służy do diagnozowania problemów z wydajnością usługi Azure SQL Database, a także z określonymi zapytaniami i partiami. Kwerendy mogą czekać na zasoby, oczekiwania w kolejce lub oczekiwania zewnętrzne.
- Użyj [pliku sys.dm_os_waiting_tasks,](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) aby zwrócić informacje o kolejce zadań oczekujących na niektóre zasoby.

W scenariuszach o wysokim procesorze CPU statystyki magazynu zapytań i oczekiwania mogą nie odzwierciedlać użycia procesora CPU, jeśli:

- Kwerendy o wysokiej wydajności procesora CPU są nadal wykonywane.
- Kwerendy o wysokiej wydajności procesora CPU były uruchomione, gdy doszło do pracy awaryjnej.

DmVs, które śledzą query store i statystyki oczekiwania pokazują wyniki tylko pomyślnie zakończone i limit czasu kwerend. Nie pokazują one danych dla aktualnie wykonywanych instrukcji, dopóki instrukcje nie zakończą się. Użyj dynamicznego widoku zarządzania [sys.dm_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) do śledzenia aktualnie wykonywanych zapytań i skojarzonego czasu procesu roboczego.

> [!TIP]
> Dodatkowe narzędzia:
>
> - [TigerToolbox czeka i zatrzaski](https://github.com/Microsoft/tigertoolbox/tree/master/Waits-and-Latches)
> - [usp_whatsup TigerToolbox](https://github.com/Microsoft/tigertoolbox/tree/master/usp_WhatsUp)

## <a name="next-steps"></a>Następne kroki

[Omówienie monitorowania i dostrajania bazy danych SQL](sql-database-monitor-tune-overview.md)