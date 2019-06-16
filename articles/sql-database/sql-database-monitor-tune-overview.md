---
title: Monitorowanie i dostrajanie wydajności — usługi Azure SQL Database | Dokumentacja firmy Microsoft
description: Porady dotyczące dostrajania w usłudze Azure SQL Database do oceny i poprawy wydajności.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: jrasnik, carlrab
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 2fa43fcd48736a3d044deb07ed690af580c3b987
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66416281"
---
# <a name="monitoring-and-performance-tuning"></a>Monitorowanie i dostrajanie wydajności

Usługa Azure umożliwia bazy danych SQL Database można łatwo monitorować użycie, dodawanie lub usuwanie zasobów (procesora CPU, pamięć, we/wy), rozwiązywania potencjalnych problemów i znaleźć zaleceń, które może poprawić wydajność bazy danych. Usługa Azure SQL Database ma wiele funkcji, które automatycznie można rozwiązać problemy w bazach danych jeśli chcesz zezwolić bazy danych, dostosowywać się do obciążenia i automatycznie optymalizacji wydajności. Istnieją jednak niestandardowe problemy, które być może trzeba rozwiązać. W tym artykule opisano niektóre najlepsze rozwiązania i narzędzia, które umożliwiają rozwiązywanie problemów z wydajnością.

Istnieją dwa główne czynności, które należy wykonać w celu zapewnienia, że bazy danych możesz działa bez problemów:
- [Monitorowanie wydajności bazy danych](#monitoring-database-performance) aby upewnić się, że zasoby przydzielone do bazy danych może obsłużyć obciążenie. Jeśli widzisz, że w przypadku osiągnięcia limitów zasobów, będziesz potrzebować do identyfikacji zasobu najważniejsze zapytań zużywających najwięcej zasobów i optymalizujesz je lub dodać więcej zasobów, zmieniając warstwę usług.
- [Rozwiązywanie problemów z wydajnością](#troubleshoot-performance-issues) w celu ustaleniu, dlaczego wystąpiło kilka potencjalny problem, odkryć ich główną przyczynę problemu i akcji, która rozwiąże problem.

## <a name="monitoring-database-performance"></a>Monitorowanie wydajności bazy danych

Monitorowanie wydajności bazy danych SQL na platformie Azure rozpoczyna się od monitorowania wykorzystania zasobów względem wybranego poziomu wydajności bazy danych. Należy monitorować następujące zasoby:
 - **Użycie procesora CPU** — należy sprawdzić, są osiągnięcia 100% użycia procesora CPU w dłuższy okres czasu. To może wskazywać, czy może być konieczne uaktualnienie, bazy danych lub wystąpienia lub zidentyfikować i dostrojenia zapytań, które używają najczęściej, mocy obliczeniowej.
 - **Statystyki oczekiwania** — należy sprawdzić, jakie Dlaczego zapytań oczekują na zasoby. Queriesmig poczekaj, aż dane mają być pobierane lub zapisane pliki bazy danych, oczekiwanie, ponieważ osiągnięto limit niektórych zasobów itp.
 - **Użycie operacji We/Wy** — należy sprawdzić, są osiągnięcia limitów we/wy magazynu.
 - **Użycie pamięci** — ilość pamięci dostępnej dla bazy danych lub wystąpienia jest proporcjonalna do liczby rdzeni wirtualnych i należy sprawdzić jest wystarczająco dla obciążenia. Oczekiwana długość życia strony jest jeden z parametrów, które mogą wskazywać są strony szybko usuwane z pamięci.

Usługa Azure SQL Database **zapewnia zawiadomień, które ułatwiają rozwiązywanie problemów i naprawy potencjalne problemy z wydajnością**. Można łatwo zidentyfikować możliwości w celu zwiększenia i zoptymalizowania wydajności zapytań bez konieczności zmieniania zasobów, przeglądając [zalecenia dotyczące dostrajania wydajności](sql-database-advisor.md). Częstymi przyczynami słabej wydajności bazy danych są brakujące indeksy i nieprawidłowo zoptymalizowane zapytania. Można zastosować te zalecenia dotyczące dostrajania, aby zwiększyć wydajność przetwarzania obciążenia. Możesz również pozwalają bazy danych Azure SQL, aby [automatycznie zoptymalizować wydajność kwerend](sql-database-automatic-tuning.md) , stosując wszystkie zidentyfikowane zalecenia i weryfikowanie, czy poprawiają wydajność bazy danych.

Masz następujące opcje monitorowania i rozwiązywanie problemów z wydajnością bazy danych:

- W [witryny Azure portal](https://portal.azure.com), kliknij przycisk **baz danych SQL**wybierz bazy danych, a następnie użyć wykres monitorowanie wyszukiwać zasoby zbliża się do ich maksymalnej. Użycie jednostek DTU jest wyświetlana domyślnie. Kliknij przycisk **Edytuj** zmienić zakres czasu i wartości wyświetlane.
- Narzędzia, takie jak SQL Server Management Studio zapewnia wiele przydatnych raportów, takich jak [pulpit nawigacyjny wydajności](https://docs.microsoft.com/sql/relational-databases/performance/performance-dashboard?view=sql-server-2017) w przypadku, gdy było monitorowania wykorzystania zasobów i zidentyfikować zapytania, korzystające z najważniejszych zasobów lub [Query Store](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store#Regressed)gdzie możesz zidentyfikować zapytania o pogorszonej wydajności.
- Użyj [Query Performance Insight](sql-database-query-performance.md) [witryny Azure portal](https://portal.azure.com) Aby identyfikować zapytania, które możesz wydać na w pełni korzystać z zasobów. Ta funkcja jest dostępna w pojedynczej bazy danych i pul elastycznych tylko.
- Użyj [SQL Database Advisor](sql-database-advisor-portal.md) Aby wyświetlić zalecenia dotyczące tworzenia i usuwanie indeksów, parametryzacji zapytań i naprawia problemy ze schematem. Ta funkcja jest dostępna w pojedynczej bazy danych i pul elastycznych tylko.
- Użyj [Azure SQL Intelligent Insights](sql-database-intelligent-insights.md) automatyczne monitorowanie wydajności bazy danych. Po wykryciu problemu z wydajnością, dziennik diagnostyczny jest generowany ze szczegółami i głównej przyczyny Analysis (analiza głównej przyczyny) problemu. Zalecenie dotyczące poprawy wydajności znajduje się, gdy jest to możliwe.
- [Włączanie automatycznego dostrajania](sql-database-automatic-tuning-enable.md) i pozwól SQL Azure, bazy danych automatycznego rozwiązywania zidentyfikowanych problemów z wydajnością.
- Użyj [dynamicznych widoków zarządzania (DMV)](sql-database-monitoring-with-dmvs.md), [zdarzeniom rozszerzonym](sql-database-xevent-db-diff-from-svr.md)i [Query Store](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) szczegółowe Rozwiązywanie problemów z wydajnością.

> [!TIP]
> Zobacz [wskazówki dotyczące wydajności](sql-database-performance-guidance.md) można znaleźć technik, które można użyć w celu poprawy wydajności usługi Azure SQL Database po zidentyfikowaniu problemu z wydajnością za pomocą co najmniej jednym z powyższych metod.

## <a name="troubleshoot-performance-issues"></a>Rozwiązywanie problemów z wydajnością

Aby zdiagnozować i rozwiązać problemy z wydajnością, Rozpocznij od zrozumienia stan każdej aktywnej kwerendy i warunki powodujące problemy z wydajnością istotne dla każdego stanu obciążenia. Aby poprawić wydajność usługi Azure SQL Database, Dowiedz się, że każde żądanie aktywne zapytanie z aplikacji znajduje się w bieżących lub stan oczekujący. Podczas rozwiązywania problemów z wydajnością w usłudze Azure SQL Database, pamiętać poniższej tabeli jako możesz przeczytać ten artykuł, aby zdiagnozować i rozwiązać problemy z wydajnością.

![Stany obciążenia](./media/sql-database-monitor-tune-overview/workload-states.png)

W przypadku obciążeń z problemów z wydajnością, to problem z wydajnością może być spowodowane rywalizacji o zasoby procesora CPU ( **dotyczące uruchamiania** warunku) lub poszczególnych zapytań oczekuje na coś ( **oczekiwania dotyczące** warunku ).

Przyczyny lub **dotyczące uruchamiania** problemów może być:
- **Problemy z kompilacji** -Optymalizator zapytań SQL może powodować generowanie optymalne planu z powodu starych statystyki, niepoprawny szacowania liczby wierszy, które będą przetwarzane lub oszacowania wymaganej ilości pamięci. Jeśli wiesz, że to zapytanie zostało wykonane szybciej w przeszłości, lub na inne wystąpienia (wystąpienia programu SQL Server lub wystąpienia zarządzanego), wykonaj plany rzeczywiste wykonanie i porównaj je, aby zobaczyć, czy są różne. Spróbuj zastosować wskazówki zapytania lub ponowne kompilowanie statystyk ani indeksów, aby uzyskać lepsze planu. Włącz automatyczna korekta planu usługi Azure SQL Database, aby automatycznie rozwiązać te problemy.
- **Brak problemów z wykonywaniem** — Jeśli to ustawienie jest optymalne, planu zapytania, a następnie prawdopodobnie wykorzystuje pewne ograniczenia zasobów w bazie danych, takie jak dziennik przepływność zapisu lub może zostać użyty zdefragmentowanej indeksy, które powinien zostać ponownie skompilowany. Można również przyczyny problemów z wykonywaniem dużej liczby równoczesnych zapytań, które są wydatków na zasoby. **Oczekiwania dotyczące** problemy są w większości przypadków związane z problemami wykonywania, ponieważ zapytania, które nie są wykonywane efektywnie prawdopodobnie oczekiwania na zasoby.

Przyczyny lub **oczekiwania dotyczące** problemów może być:
- **Blokowanie** — jedno zapytanie może być Zaczekaj, aż blokady niektórych obiektów w bazie danych innych próby dostępu do tych samych obiektów. Można łatwo zidentyfikować blokowania zapytań przy użyciu DMV lub narzędzia do monitorowania.
- **Problemy z operacji We/Wy** -zapytania mogą oczekiwać dla stron, które są zapisywane w plikach dziennika i danych. W takim przypadku zobaczysz `INSTANCE_LOG_RATE_GOVERNOR`, `WRITE_LOG`, lub `PAGEIOLATCH_*` czekać w DMV statystyk.
- **Problemy z bazy danych TempDB** — Jeśli używasz wiele tabel tymczasowych lub zobaczysz, że wiele TempDB wylewa w planów zapytań może być problem z przepływności bazy danych TempDB. 
- **Problemy związane z pamięcią** — możesz nie mieć wystarczającej ilości pamięci dla obciążenia, może usunąć swoje oczekiwanej długości życia strony lub zapytań pojawiają się mniej przydział pamięci, niż jest to potrzebne. W niektórych przypadkach wbudowanych funkcji analizy w Optymalizator zapytań rozwiąże tych problemów.
 
W poniższych sekcjach opisano sposób zidentyfikować i rozwiązać niektóre z tych problemów.

## <a name="running-related-performance-issues"></a>Problemy z wydajnością dotyczące uruchamiania

Ogólną wytyczną wykorzystaniu Procesora jest stale wynosiło co najmniej 80%, przypadku problemów z wydajnością powiązane działania. Jeśli masz problem z dotyczące uruchamiania, może być spowodowany przez niewystarczające zasoby procesora CPU lub może być związany z jednym z następujących warunków:

- Zbyt wiele uruchomionych zapytań
- Zbyt wiele kompilacji zapytań
- Jedna lub więcej wykonywanie kwerend korzystają z planu optymalne zapytania

Jeśli okaże się, że masz problemu z wydajnością dotyczące uruchamiania, celem użytkownika jest identyfikowanie problemu dokładne przy użyciu co najmniej jednej metody. Najczęściej stosowanymi metodami do identyfikowania problemów dotyczących uruchamiania są:

- Użyj [witryny Azure portal](sql-database-manage-after-migration.md#monitor-databases-using-the-azure-portal) monitorować wykorzystanie procesora CPU w procentach.
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
### <a name="factors-influencing-query-plan-changes"></a>Czynniki mające wpływ na zmiany planu zapytań

Plan wygenerowane zapytanie, która różni się od co pierwotnie był buforowany może spowodować ponowną kompilację planu wykonania zapytania. Istnieją różne powody, dlaczego istniejący plan oryginalnego może automatycznie ponownie skompilowana:
- Zmiany w schemacie odwołuje się zapytanie
- Zmiany danych w tabelach odwołuje się zapytanie 
- Zmiany opcji kontekst zapytania 

Skompilowany plan, może wysunąć z pamięci podręcznej z różnych powodów, takich jak ponowne uruchomienie wystąpienia, zmiany konfiguracji, wykorzystanie pamięci i jawnego żądania wyczyszczenia pamięci podręcznej w zakresie bazy danych. Ponadto za pomocą wskazówka RECOMPILE oznacza, że nie będzie można buforować planu.

Ponownej kompilacji (lub nowej kompilacji po eksmisji pamięci podręcznej) nadal może spowodować generowanie plan wykonania zapytania identyczne niż pierwotnie przestrzegane.  Jeśli jednak istnieją zmiany do planu w porównaniu do poprzedniego lub oryginalny plan, poniżej przedstawiono najbardziej typowe wyjaśnienia Przyczyna zmiany planu wykonania zapytania:

- **Zmienione projektu fizycznego**. Na przykład utworzyć nowe indeksy bardziej efektywnie cover, których wymagania zapytanie może być używana dla nowych kompilacji, jeśli Optymalizator zapytań postanawia go to bardziej optymalne wykorzystanie tego nowego indeksu niż używanie struktury danych początkowo wybrane do pierwszej wersji wykonanie kwerendy.  Zmiany fizyczne przywoływanych obiektów może spowodować nowy wybór planu w czasie kompilacji.

- **Różnic między zasobami serwera**. W przypadku, gdy jeden plan różni się w systemie"A" a "system B" — dostępność zasobów, takich jak liczba dostępnych procesorów mogą mieć wpływ na jakie plan pobiera wygenerowany.  Na przykład jeśli jeden system ma większej liczby procesorów, można wybrać plan równoległy. 

- **Różne statystyki**. Statystyki związane z obiektami odwołania lub nazwę istotnie różnią się od oryginalnego systemu statystyk.  Jeśli statystyki i występuje ponownej kompilacji, optymalizator zapytań użyje statystyki, począwszy od tego konkretnego punktu w czasie. Poprawione statystyki mogą mieć dystrybucji znacząco odmiennych danych oraz częstotliwości, które nie zostały uwzględnione w przypadku oryginalnej kompilacji.  Zmiany te są używane do szacowania kardynalności szacuje (liczba wierszy przewidywanym przepływają przez drzewo logiczne zapytań).  Zmiany do szacowania kardynalności może prowadzić nam wybrać różne operatory fizycznych i skojarzone zamówienia dla operacji.  Nawet drobne zmiany do statystyk może spowodować plan wykonania zmienionym zapytaniem.

- **Zmieniono wersję bazy danych zgodności poziomu lub jej kardynalności narzędzie do szacowania**.  Zmiany poziomu zgodności bazy danych można włączyć nowe strategie i funkcje, które może spowodować planu wykonanie innego zapytania.  Poza poziom zgodności bazy danych wyłączenie lub włączenie flagi śledzenia 4199 lub zmianę stanu bazy danych konfiguracji o określonym zakresie, który QUERY_OPTIMIZER_HOTFIXES także mogą mieć wpływ na zapytania opcje planu wykonania w czasie kompilacji.  Flagi śledzenia 9481 (CE starszych force) i 2312 (zmusza domyślne CE) są również zaplanować wpływających na. 

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
