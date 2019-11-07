---
title: Monitorowanie i dostrajanie wydajności — Azure SQL Database
description: Wskazówki dotyczące dostrajania wydajności w Azure SQL Database oceny i ulepszeń.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: jrasnick, carlrab
ms.date: 01/25/2019
ms.openlocfilehash: c11112963ec82a0e53df156048495e7b5141bcb7
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73687769"
---
# <a name="monitoring-and-performance-tuning"></a>Monitorowanie i dostrajanie wydajności

Azure SQL Database udostępnia narzędzia i metody umożliwiające łatwe monitorowanie użycia, Dodawanie lub usuwanie zasobów (takich jak procesor CPU, pamięć lub we/wy), rozwiązywanie potencjalnych problemów oraz podejmowanie zaleceń w celu poprawy wydajności bazy danych. Funkcje w Azure SQL Database mogą automatycznie rozwiązywać problemy z bazami danych. 

Dostrajanie automatyczne umożliwia bazie danych adaptację do obciążenia i automatyczne Optymalizowanie wydajności. Jednak niektóre problemy niestandardowe mogą wymagać rozwiązania problemu. W tym artykule opisano niektóre najlepsze rozwiązania i narzędzia, których można użyć do rozwiązywania problemów z wydajnością.

Aby upewnić się, że baza danych działa bez problemów, należy:
- [Monitoruj wydajność bazy danych](#monitor-database-performance) , aby upewnić się, że zasoby przypisane do bazy danych mogą obsługiwać obciążenie. Jeśli baza danych powoduje przekroczenie limitów zasobów, należy rozważyć następujące kwestie:
   - Identyfikowanie i optymalizowanie najważniejszych zapytań zużywających zasoby.
   - Dodawanie większej liczby zasobów przez [uaktualnienie warstwy usług](https://docs.microsoft.com/azure/sql-database/sql-database-scale-resources).
- [Rozwiązywanie problemów z wydajnością](#troubleshoot-performance-problems) w celu zidentyfikowania przyczyny wystąpienia potencjalnego problemu i zidentyfikowania głównej przyczyny problemu. Po zidentyfikowaniu głównej przyczyny należy wykonać kroki w celu rozwiązania problemu.

## <a name="monitor-database-performance"></a>Monitorowanie wydajności bazy danych

Aby monitorować wydajność bazy danych SQL na platformie Azure, Zacznij od monitorowania zasobów używanych względem wybranego poziomu wydajności bazy danych. Monitoruj następujące zasoby:
 - **Użycie procesora**: Sprawdź, czy baza danych osiągnie 100% użycia procesora CPU przez dłuższy czas. Duże użycie procesora CPU może wskazywać na to, że trzeba identyfikować i dostrajać zapytania, które wykorzystują największą moc obliczeniową. Duże użycie procesora CPU może również oznaczać, że baza danych lub wystąpienie należy uaktualnić do wyższej warstwy usług. 
 - **Statystyka oczekiwania**: Użyj [widoku sys. DM _os_wait_stats (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql) , aby określić, jak długo zapytania oczekują. Zapytania oczekują na zasoby, czekają na kolejki lub czekają na zewnątrz. 
 - **Użycie we/wy**: Sprawdź, czy baza danych zbliża się do limitów operacji we/wy magazynu bazowego.
 - **Użycie pamięci**: ilość pamięci dostępnej dla bazy danych lub wystąpienia jest proporcjonalna do liczby rdzeni wirtualnych. Upewnij się, że pamięć jest wystarczająca dla obciążenia. Stron życia stron to jeden z parametrów, które mogą wskazywać na szybkość usuwania stron z pamięci.

Usługa Azure SQL Database obejmuje narzędzia i zasoby pomagające w rozwiązywaniu problemów z wydajnością i naprawianiu potencjalnych problemów. Możesz identyfikować możliwości ulepszania i optymalizowania wydajności zapytań bez zmieniania zasobów, przeglądając [zalecenia dotyczące dostrajania wydajności](sql-database-advisor.md). 

Częstymi przyczynami słabej wydajności bazy danych są brakujące indeksy i nieprawidłowo zoptymalizowane zapytania. Możesz zastosować zalecenia strojenia, aby zwiększyć wydajność obciążenia. Możesz również pozwolić Azure SQL Database [automatycznie zoptymalizować wydajność zapytań](sql-database-automatic-tuning.md) , stosując wszystkie zidentyfikowane zalecenia. Następnie sprawdź, czy zaleceń poprawia wydajność bazy danych.

> [!NOTE]
> Indeksowanie jest dostępne tylko w ramach pojedynczej bazy danych i pul elastycznych. Indeksowanie nie jest dostępne w wystąpieniu zarządzanym.

Wybierz jedną z następujących opcji, aby monitorować i rozwiązywać problemy z wydajnością bazy danych:

- W [Azure Portal](https://portal.azure.com)wybierz opcję **bazy danych SQL** , a następnie wybierz bazę danych. Na wykresie **monitorowania** poszukaj zasobów zbliżających się do maksymalnego wykorzystania. Użycie jednostek DTU jest domyślnie wyświetlane. Wybierz pozycję **Edytuj** , aby zmienić pokazany zakres czasu i wartości.
- Narzędzia, takie jak SQL Server Management Studio, udostępniają wiele przydatnych raportów, takich jak [pulpit nawigacyjny wydajności](https://docs.microsoft.com/sql/relational-databases/performance/performance-dashboard). Te raporty służą do monitorowania użycia zasobów i identyfikowania najważniejszych zapytań zużywających zasoby. [Magazynu zapytań](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store#Regressed) można używać do identyfikowania zapytań, których wydajność ma uległa pogorszeniu.
- W [Azure Portal](https://portal.azure.com)Użyj [szczegółowe informacje o wydajności zapytań](sql-database-query-performance.md) , aby zidentyfikować zapytania, które korzystają z większości zasobów. Ta funkcja jest dostępna tylko w ramach pojedynczej bazy danych i pul elastycznych.
- Użyj [SQL Database Advisor](sql-database-advisor-portal.md) , aby wyświetlić zalecenia ułatwiające tworzenie i upuszczanie indeksów, zapytań Sparametryzuj oraz Rozwiązywanie problemów ze schematem. Ta funkcja jest dostępna tylko w ramach pojedynczej bazy danych i pul elastycznych.
- [Usługa Azure SQL Intelligent Insights](sql-database-intelligent-insights.md) umożliwia automatyczne monitorowanie wydajności bazy danych. W przypadku wykrycia problemu z wydajnością jest generowany dziennik diagnostyczny. Dziennik zawiera szczegółowe informacje i główną przyczynę problemu. Zalecenie dotyczące poprawy wydajności jest udostępniane, gdy jest to możliwe.
- [Włącz automatyczne dostrajanie](sql-database-automatic-tuning-enable.md) , aby umożliwić Azure SQL Database automatyczne rozwiązywanie problemów z wydajnością.
- Użyj [dynamicznych widoków zarządzania (widoków DMV)](sql-database-monitoring-with-dmvs.md), [zdarzeń rozszerzonych](sql-database-xevent-db-diff-from-svr.md)i [magazynu zapytań](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) , aby uzyskać pomoc w bardziej szczegółowym rozwiązywaniu problemów z wydajnością.

> [!TIP]
> Po zidentyfikowaniu problemu z wydajnością zapoznaj się z naszymi [wskazówkami dotyczącymi wydajności](sql-database-performance-guidance.md) , aby znaleźć techniki zwiększające wydajność Azure SQL Database.

## <a name="troubleshoot-performance-problems"></a>Rozwiązywanie problemów z wydajnością

Aby zdiagnozować i rozwiązać problemy z wydajnością, Zacznij od znalezienia stanu poszczególnych aktywnych zapytań oraz warunków, które powodują problemy z wydajnością związane z każdym stanem obciążenia. Aby zwiększyć wydajność Azure SQL Database, należy zrozumieć, że każde aktywne żądanie zapytania z aplikacji jest w stanie uruchomienia lub w stanie oczekiwania. Podczas rozwiązywania problemów z wydajnością w Azure SQL Database należy pamiętać o poniższym diagramie.

![Stany obciążeń](./media/sql-database-monitor-tune-overview/workload-states.png)

Problem z wydajnością w obciążeniu może być spowodowany przez rywalizację procesora (warunek *związany z uruchomieniem* ) lub pojedyncze zapytania, które oczekują na coś (warunek *oczekujący* ).

Problemy związane z uruchamianiem mogą być spowodowane przez:
- **Problemy z kompilacją**: optymalizator zapytań SQL może utworzyć nieoptymalny plan ze względu na nieodświeżone statystyki, nieprawidłowe oszacowanie liczby wierszy do przetworzenia lub niedokładne oszacowanie wymaganej pamięci. Jeśli wiesz, że zapytanie zostało wykonane szybciej w przeszłości lub w innym wystąpieniu (wystąpienie zarządzane lub SQL Server wystąpienie), porównaj rzeczywiste plany wykonywania, aby zobaczyć, czy są one różne. Spróbuj zastosować wskazówki dotyczące zapytania lub ponownie skompilować statystyki lub indeksy, aby uzyskać lepszy plan. Włącz automatyczną korektę planu w Azure SQL Database, aby automatycznie wyeliminować te problemy.
- **Problemy z wykonywaniem**: Jeśli plan zapytania jest optymalny, prawdopodobnie spowoduje to przekroczenie limitów zasobów bazy danych, takich jak przepływność zapisu w dzienniku. Lub mogą używać pofragmentowanych indeksów, które powinny być ponownie skompilowane. Problemy z wykonywaniem mogą również wystąpić, gdy duża liczba współbieżnych zapytań wymaga tych samych zasobów. Problemy *zależne* są zwykle związane z problemami z wykonywaniem, ponieważ zapytania, które nie wykonują wydajnej pracy, prawdopodobnie oczekują na niektóre zasoby.

Problemy zależne mogą być spowodowane przez:
- **Blokowanie**: jedno zapytanie może blokować blokowanie obiektów w bazie danych, podczas gdy inne próbują uzyskać dostęp do tych samych obiektów. Można zidentyfikować blokowanie zapytań za pomocą narzędzi widoków DMV lub monitorowania.
- **Problemy we/wy**: zapytania mogą oczekiwać na zapisanie stron w plikach danych lub dziennika. W takim przypadku należy sprawdzić dane statystyczne `INSTANCE_LOG_RATE_GOVERNOR`, `WRITE_LOG`lub `PAGEIOLATCH_*` zaczekać w DMV.
- **Problemy**z bazą danych tempdb: Jeśli obciążenie używa tabel tymczasowych lub w planach występuje Relanie bazy danych tempdb, zapytania mogą mieć problem z przepływem pracy w bazie danych tempdb. 
- **Problemy związane z pamięcią**: Jeśli obciążenie nie ma wystarczającej ilości pamięci, może to oznaczać, że stron życia strony lub zapytania mogą uzyskać mniejszą ilość pamięci niż jest to potrzebne. W niektórych przypadkach wbudowana analiza w optymalizatora zapytań naprawi problemy związane z pamięcią.
 
W poniższych sekcjach opisano sposób identyfikowania i rozwiązywania problemów niektórych typów problemów.

## <a name="performance-problems-related-to-running"></a>Problemy z wydajnością związane z uruchamianiem

Ogólnie rzecz biorąc, jeśli użycie procesora CPU jest spójne na poziomie 80%, problem z wydajnością jest uruchomiony. Problem związany z działaniem może być spowodowany przez niewystarczające zasoby procesora CPU. Może to być związane z jednym z następujących warunków:

- Zbyt wiele uruchomionych zapytań
- Zbyt wiele zapytań kompilacji
- Co najmniej jedno wykonywanie zapytań korzystających z nieoptymalnego planu zapytania

Jeśli znajdziesz problem z wydajnością związany z działaniem, celem jest zidentyfikowanie dokładnego problemu przy użyciu jednej lub kilku metod. Metody te są najczęstszymi sposobami identyfikowania problemów związanych z działaniem:

- Użyj [Azure Portal](sql-database-manage-after-migration.md#monitor-databases-using-the-azure-portal) , aby monitorować użycie procentowe procesora CPU.
- Użyj następujących [widoków DMV](sql-database-monitoring-with-dmvs.md):

  - [Sys. DM _db_resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) DMV zwraca procesor, we/wy i użycie pamięci dla bazy danych SQL. Jeden wiersz istnieje dla każdego 15-sekundowego interwału, nawet jeśli w bazie danych nie ma żadnych działań. Dane historyczne są przechowywane przez jedną godzinę.
  - [Sys. resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) DMV zwraca dane użycia procesora CPU i magazynu dla Azure SQL Database. Dane są zbierane i agregowane w odstępach 5-minutowych.

> [!IMPORTANT]
> Aby rozwiązać problemy dotyczące użycia procesora przez zapytania T-SQL, które używają sys. DM _db_resource_stats i sys. resource_stats widoków DMV, zobacz temat [identyfikowanie problemów z wydajnością procesora CPU](sql-database-monitoring-with-dmvs.md#identify-cpu-performance-issues).

### <a name="ParamSniffing"></a>Zapytania, które mają problemy z PSP

Problem z planem uwzględniania parametrów (PSP) występuje, gdy optymalizator zapytań generuje plan wykonywania zapytania, który jest optymalny tylko dla określonej wartości parametru (lub zestawu wartości), a w pamięci podręcznej nie jest optymalny dla wartości parametrów, które są używane w kolejnych wykonań. Plany, które nie są optymalne, mogą powodować problemy z wydajnością zapytań i obniżać ogólną przepływność obciążeń. 

Aby uzyskać więcej informacji na temat wykrywania parametrów i przetwarzania zapytań, zobacz [Przewodnik po architekturze przetwarzania zapytań](/sql/relational-databases/query-processing-architecture-guide#ParamSniffing).

Kilka obejść może rozwiązać problemy związane z PSP. Każde obejście ma powiązane kompromisy i wady:

- Użyj wskazówki dotyczącej ponownej [kompilacji](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) w każdym wykonaniu zapytania. To obejście umożliwia wymianę czasu kompilacji i zwiększenie procesora w celu zapewnienia lepszej jakości planu. Opcja `RECOMPILE` jest często niemożliwa w przypadku obciążeń wymagających dużej przepływności.
- Użyj [opcji zapytania (Optymalizacja dla...)](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) , aby zastąpić rzeczywistą wartość parametru wartością typowego parametru, która generuje plan, który jest wystarczająco dobry dla większości wartości parametrów. Ta opcja wymaga dobrego poznania optymalnych wartości parametrów i skojarzonych cech planu.
- Użyj [opcji (Optymalizacja dla NIEznanej)](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) wskazówki, aby zastąpić rzeczywistą wartość parametru, a zamiast tego użyć średniej gęstości wektora. Można to zrobić również, przechwytując wartości parametrów przychodzących w zmiennych lokalnych, a następnie używając zmiennych lokalnych w predykatach zamiast używać samych parametrów. W przypadku tej poprawki średnia gęstość musi być *wystarczająca*.
- Wyłącz wykrywanie parametrów za pomocą wskazówki zapytania [DISABLE_PARAMETER_SNIFFING](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) .
- Użyj wskazówki zapytania [KEEPFIXEDPLAN](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) , aby uniemożliwić ponowne kompilacje w pamięci podręcznej. W tym rozwiązaniu przyjęto założenie, że jest to ten sam plan, który jest już w pamięci podręcznej. Możesz również wyłączyć automatyczne aktualizacje statystyk, aby zmniejszyć prawdopodobieństwo wykluczenia dobrego planu i skompilowania nowego nieprawidłowego planu.
- Wymuś planowanie jawnie za pomocą wskazówki zapytania [use plan](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) przez ponowne zapisanie zapytania i dodanie wskazówki w tekście zapytania. Lub ustawić konkretny plan przy użyciu magazynu zapytań lub przez włączenie [dostrajania automatycznego](sql-database-automatic-tuning.md).
- Zastąp pojedynczą procedurę z zagnieżdżonym zestawem procedur, które mogą być używane na podstawie logiki warunkowej i skojarzonych wartości parametrów.
- Utwórz alternatywny sposób wykonywania ciągów dynamicznych do definicji procedury statycznej.

Aby uzyskać więcej informacji na temat rozwiązywania problemów z PSP, zobacz następujące wpisy w blogu:

- [Zapach I parametr](https://blogs.msdn.microsoft.com/queryoptteam/2006/03/31/i-smell-a-parameter/)
- [Conor a dynamiczne SQL a procedury i planowanie jakości zapytań parametrycznych](https://blogs.msdn.microsoft.com/conor_cunningham_msft/2009/06/03/conor-vs-dynamic-sql-vs-procedures-vs-plan-quality-for-parameterized-queries/)
- [Techniki optymalizacji zapytań SQL w SQL Server: wykrywanie parametrów](https://www.sqlshack.com/query-optimization-techniques-in-sql-server-parameter-sniffing/)

### <a name="compile-activity-caused-by-improper-parameterization"></a>Działanie kompilowania spowodowane przez niewłaściwe parametryzacja

Gdy zapytanie zawiera literały, aparat bazy danych automatycznie parameterizes instrukcję lub użytkownik jawnie parameterizes instrukcji w celu zmniejszenia liczby kompilacji. Duża liczba kompilacji dla zapytania korzystającego z tego samego wzorca, ale różne wartości literałów mogą spowodować wysokie użycie procesora CPU. Podobnie, jeśli tylko częściowo Sparametryzuj zapytanie, które nadal ma literały, aparat bazy danych nie Sparametryzuj zapytania dalej.  

Oto przykład zapytania częściowo sparametryzowanego:

```sql
SELECT * 
FROM t1 JOIN t2 ON t1.c1 = t2.c1
WHERE t1.c1 = @p1 AND t2.c2 = '961C3970-0E54-4E8E-82B6-5545BE897F8F'
```

W tym przykładzie `t1.c1` pobiera `@p1`, ale `t2.c2` nadal przyjmuje identyfikator GUID jako literał. W takim przypadku, jeśli zmienisz wartość `c2`, zapytanie jest traktowane jako inne zapytanie i zostanie wykonana Nowa kompilacja. Aby zmniejszyć kompilacje w tym przykładzie, należy również Sparametryzuj identyfikator GUID.

Następujące zapytanie pokazuje liczbę zapytań według skrótu zapytania, aby określić, czy zapytanie jest prawidłowo sparametryzowane:

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

### <a name="factors-that-affect-query-plan-changes"></a>Czynniki wpływające na zmiany planu zapytania

Ponowna kompilacja planu wykonania zapytania może spowodować wygenerowanie planu zapytania, który różni się od oryginalnego buforowanego planu. Istniejący oryginalny plan może zostać automatycznie ponownie skompilowany z różnych powodów:
- Do zmian w schemacie odwołuje się zapytanie.
- Do zmian danych w tabelach odwołuje się zapytanie. 
- Opcje kontekstu zapytania zostały zmienione.

Skompilowany plan może zostać wysunięty z pamięci podręcznej z różnych powodów, takich jak:

- Ponowne uruchomienie wystąpienia.
- Zmiany konfiguracji w zakresie bazy danych.
- Wykorzystanie pamięci.
- Jawne żądania wyczyszczenia pamięci podręcznej.

Jeśli używasz wskazówki RECOMPILE, plan nie zostanie zapisany w pamięci podręcznej.

Ponowna kompilacja (lub Nowa kompilacja po wykluczeniu pamięci podręcznej) nadal może skutkować generowaniem planu wykonywania zapytania, który jest identyczny z oryginałem. W przypadku zmiany planu z planu poprzedniego lub oryginalnego te wyjaśnienia mogą być następujące:

- **Zmieniony projekt fizyczny**: na przykład nowo utworzone indeksy bardziej efektywnie obejmują wymagania zapytania. Nowe indeksy mogą być używane w nowej kompilacji, jeśli optymalizator zapytań zdecyduje, że użycie tego nowego indeksu jest bardziej optymalne niż użycie struktury danych, która została pierwotnie wybrana dla pierwszej wersji wykonywania zapytania.  Wszelkie zmiany fizyczne obiektów, do których istnieją odwołania, mogą spowodować powstanie nowego planu w czasie kompilacji.

- **Różnice zasobów serwera**: gdy plan w jednym systemie różni się od planu w innym systemie, dostępność zasobów, taka jak liczba dostępnych procesorów, może mieć wpływ na sposób generowania planu.  Na przykład jeśli jeden system ma więcej procesorów, można wybrać plan równoległy. 

- **Różne statystyki**: Statystyka skojarzona z obiektami, do których istnieją odwołania, mogła ulec zmianie lub może być istotnie różna od statystyk oryginalnego systemu.  W przypadku zmiany statystyk i ponownej kompilacji optymalizator zapytań używa statystyk rozpoczynających się od momentu zmiany. Dane statystyczne i częstotliwości dla zaktualizowanych statystyk mogą się różnić od tych, które zostały oryginalnie skompilowane.  Te zmiany są używane do tworzenia oszacowań kardynalności. (*Oszacowania kardynalności* to liczba wierszy, które powinny przepływać przez drzewo zapytania logicznego). Zmiany w oszacowaniach kardynalności mogą prowadzić do wyboru różnych operatorów fizycznych i skojarzonych z nimi zamówień.  Nawet niewielkie zmiany w statystyce mogą spowodować zmianę planu wykonywania zapytania.

- **Zmieniony poziom zgodności bazy danych lub wersja szacowaniaa Kardynalność**: zmiany poziomu zgodności bazy danych mogą umożliwić nowe strategie i funkcje, które mogą spowodować powstanie innego planu wykonywania zapytań.  Poza poziomem zgodności bazy danych wyłączona lub włączona flaga śledzenia 4199 lub zmieniony stan QUERY_OPTIMIZER_HOTFIXES konfiguracji bazy danych może również mieć wpływ na Opcje planu wykonywania zapytania w czasie kompilacji.  Flagi śledzenia 9481 (Wymuś starszą wersję CE) i 2312 (Wymuś ustawienie domyślne CE) wpływają również na plan. 

### <a name="resolve-problem-queries-or-provide-more-resources"></a>Rozwiązywanie zapytań dotyczących problemów lub podawanie większej ilości zasobów

Po zidentyfikowaniu problemu można dostosować zapytania dotyczące problemów lub uaktualnić rozmiar lub warstwę usługi, aby zwiększyć pojemność bazy danych SQL w celu zaabsorbowania wymagań procesora. 

Aby uzyskać więcej informacji, zobacz [skalowanie zasobów pojedynczej bazy danych w Azure SQL Database](sql-database-single-database-scale.md) i [skalowanie zasobów puli elastycznej w Azure SQL Database](sql-database-elastic-pool-scale.md). Aby uzyskać informacje na temat skalowania wystąpienia zarządzanego, zobacz [limity zasobów w warstwie usług](sql-database-managed-instance-resource-limits.md#service-tier-characteristics).

### <a name="performance-problems-caused-by-increased-workload-volume"></a>Problemy z wydajnością spowodowane przez zwiększony wolumin obciążeń

Zwiększenie natężenia ruchu i obciążenia aplikacji może spowodować zwiększenie użycia procesora CPU. Jednak należy zachować ostrożność, aby prawidłowo zdiagnozować ten problem. Gdy widzisz problem z dużym procesorem CPU, Odpowiedz na te pytania, aby określić, czy wzrost jest spowodowany przez zmiany w woluminie obciążeń:

- Czy zapytania z aplikacji są przyczyną problemu z dużym procesorem CPU?
- Dla najważniejszych zapytań zużywających procesor CPU, które można zidentyfikować:

   - Czy wiele planów wykonywania jest skojarzonych z tym samym zapytaniem? Jeśli tak, dlaczego?
   - Czy w przypadku zapytań z tym samym planem wykonania były spójne czasy wykonywania? Czy Liczba wykonań została zwiększona? W takim przypadku zwiększenie obciążenia prawdopodobnie spowoduje problemy z wydajnością.

Podsumowując, jeśli plan wykonywania zapytania nie został wykonany inaczej, ale użycie procesora CPU wzrosło wraz z licznikiem wykonania, problem z wydajnością jest prawdopodobnie związany z wzrostem obciążenia.

Nie zawsze można łatwo zidentyfikować zmiany woluminu obciążenia, który zapewnia problem z procesorem CPU. Należy wziąć pod uwagę następujące czynniki: 

- **Zmieniono użycie zasobów**: na przykład Rozważmy scenariusz, w którym użycie procesora CPU wzrosło do 80% przez dłuższy czas.  Samo użycie procesora CPU nie oznacza, że wolumin obciążenia został zmieniony. Regresje w planie wykonywania zapytań oraz zmiany w dystrybucji danych mogą również przyczynić się do większej ilości zasobów, nawet jeśli aplikacja wykonuje to samo obciążenie.

- **Wygląd nowej kwerendy**: aplikacja może uzyskać nowy zestaw zapytań w różnym czasie.

- **Zwiększenie lub zmniejszenie liczby żądań**: ten scenariusz jest najbardziej oczywistą miarą obciążenia. Liczba zapytań nie zawsze odpowiada większej ilości zasobów. Jednak ten pomiar jest nadal znaczący, przy założeniu, że inne czynniki nie są zmieniane.

## <a name="waiting-related-performance-problems"></a>Problemy z wydajnością związane z oczekiwaniem 

Jeśli masz pewność, że problem z wydajnością nie jest związany z dużym użyciem procesora CPU lub uruchomiony, problem jest związany z oczekiwaniem. Oznacza to, że zasoby procesora CPU nie są używane efektywnie, ponieważ procesor CPU czeka na inne zasoby. W takim przypadku należy określić, jakie zasoby procesora CPU oczekują. 

Te metody są często używane do wyświetlania najważniejszych kategorii typów oczekiwania:

- Użyj [magazynu zapytań](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) , aby znaleźć statystyki oczekiwania dla każdego zapytania w czasie. W magazynie zapytań typy oczekiwania są łączone w kategorie oczekiwania. Można znaleźć Mapowanie kategorii oczekiwania na typy oczekiwania w [tabeli sys. query_store_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql#wait-categories-mapping-table).
- Użyj [widoku sys. DM _db_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database) , aby zwrócić informacje o wszystkich oczekiwaniach napotkanych przez wątki, które są wykonywane podczas operacji. Ten Zagregowany widok służy do diagnozowania problemów z wydajnością Azure SQL Database a także z określonymi kwerendami i partiami.
- Użyj [widoku sys. DM _os_waiting_tasks](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) , aby zwrócić informacje o kolejce zadań oczekujących na jakiś zasób.

W scenariuszach z wysokim procesorem CPU magazyn zapytań i statystyki oczekiwania mogą nie odzwierciedlać użycia procesora, jeśli:

- Nadal są wykonywane zapytania o wysokim zużyciu procesora CPU.
- W przypadku przełączenia w tryb failover działały zapytania o wysokim zużyciu procesora CPU.

Widoków DMV, który śledzi magazyn zapytań i statystyki oczekiwania, pokazują wyniki tylko dla zapytań zakończonych pomyślnie i przekroczenia limitu czasu. Nie wyświetlają one danych dla aktualnie wykonywanych instrukcji do momentu zakończenia instrukcji. Dynamiczny widok zarządzania [sys. DM _exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) służy do śledzenia aktualnie wykonywanych zapytań i skojarzonego czasu procesu roboczego.

Wykres na początku tego artykułu pokazuje, że najczęstsze oczekiwania są następujące:

- Blokady (blokowanie)
- WE/WY
- Rywalizacja dotycząca bazy danych TempDB
- Oczekiwanie na przydzielenie pamięci

> [!IMPORTANT]
> Aby uzyskać zestaw zapytań T-SQL, które używają widoków DMV do rozwiązywania problemów związanych z oczekiwaniem, zobacz:
>
> - [Identyfikowanie problemów z wydajnością we/wy](sql-database-monitoring-with-dmvs.md#identify-io-performance-issues)
> - [Oczekiwanie na określenie przydzielenia pamięci](sql-database-monitoring-with-dmvs.md#identify-memory-grant-wait-performance-issues)
> - [TigerToolbox czeka i Zamky](https://github.com/Microsoft/tigertoolbox/tree/master/Waits-and-Latches)
> - [TigerToolbox usp_whatsup](https://github.com/Microsoft/tigertoolbox/tree/master/usp_WhatsUp)

## <a name="improve-database-performance-with-more-resources"></a>Poprawianie wydajności bazy danych za pomocą większej liczby zasobów

Jeśli żadne elementy funkcjonalne nie mogą zwiększyć wydajności bazy danych, można zmienić ilość zasobów dostępnych w Azure SQL Database. Przydziel więcej zasobów, zmieniając [warstwę usługi DTU](sql-database-service-tiers-dtu.md) pojedynczej bazy danych. Lub Zwiększ jednostek eDTU puli elastycznej w dowolnym momencie. Alternatywnie, jeśli używasz [modelu zakupu opartego na rdzeń wirtualny](sql-database-service-tiers-vcore.md), Zmień warstwę usługi lub Zwiększ zasoby przydzieloną do bazy danych.

W przypadku pojedynczych baz danych można [zmienić warstwy usług lub zasoby obliczeniowe](sql-database-single-database-scale.md) na żądanie, aby zwiększyć wydajność bazy danych. W przypadku wielu baz danych należy rozważyć użycie [pul elastycznych](sql-database-elastic-pool-guidance.md) w celu automatycznego skalowania zasobów.

## <a name="tune-and-refactor-application-or-database-code"></a>Dostrajanie i Refaktoryzacja aplikacji lub kodu bazy danych

Możesz zoptymalizować kod aplikacji dla bazy danych, zmiany indeksów, planów wymuszonych lub użyć wskazówek, aby ręcznie dostosować bazę danych do obciążenia. Aby uzyskać informacje na temat ręcznego dostrajania i ponownego pisania kodu, zobacz [wskazówki dotyczące dostrajania wydajności](sql-database-performance-guidance.md).

## <a name="next-steps"></a>Następne kroki

- Aby włączyć dostrajanie automatyczne w Azure SQL Database i zezwolić funkcji dostrajania automatycznego w pełni zarządzać obciążeniem, zobacz [Włączanie dostrajania automatycznego](sql-database-automatic-tuning-enable.md).
- Aby skorzystać z dostrajania ręcznego, przejrzyj [zalecenia dotyczące dostrajania w Azure Portal](sql-database-advisor-portal.md). Ręcznie Zastosuj zalecenia, które zwiększają wydajność zapytań.
- Zmień zasoby, które są dostępne w bazie danych, zmieniając [Azure SQL Database warstwy usług](sql-database-performance-guidance.md).
