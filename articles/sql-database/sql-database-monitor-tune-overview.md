---
title: Monitorowanie wydajności & monitorowania — Azure SQL Database | Microsoft Docs
description: Wskazówki dotyczące dostrajania wydajności w Azure SQL Database oceny i ulepszeń.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: jrasnik, carlrab
ms.date: 01/25/2019
ms.openlocfilehash: c52b41c4e6d0618b4df9b2aed985bbd22d89f419
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68567190"
---
# <a name="monitoring-and-performance-tuning"></a>Monitorowanie i dostrajanie wydajności

Azure SQL Database udostępnia narzędzia i metody umożliwiające łatwe monitorowanie użycia, Dodawanie lub usuwanie zasobów (procesor CPU, pamięć, we/wy), rozwiązywanie problemów z potencjalnymi problemami oraz znalezienie zaleceń, które mogą poprawić wydajność bazy danych. Azure SQL Database ma wiele funkcji, które umożliwiają automatyczne rozwiązanie problemów z bazami danych, które umożliwią dostosowanie bazy danych do obciążenia i automatyczne zoptymalizowanie wydajności. Istnieją jednak pewne problemy, które mogą wymagać rozwiązania problemu. W tym artykule wyjaśniono niektóre najlepsze rozwiązania i narzędzia, których można użyć do rozwiązywania problemów z wydajnością.

Istnieją dwa główne działania, które należy wykonać, aby upewnić się, że baza danych działa bez problemów:
- [Monitorowanie wydajności bazy danych](#monitoring-database-performance) w celu upewnienia się, że zasoby przypisane do bazy danych mogą obsługiwać obciążenie. Jeśli widzisz, że baza danych zwiększa limity zasobów, należy zidentyfikować i zoptymalizować najważniejsze zapytania zużywające zasoby, a następnie dodać więcej zasobów, uaktualniając warstwę usług.
- [Rozwiązywanie problemów z wydajnością](#troubleshoot-performance-issues) w celu zidentyfikowania przyczyny wystąpienia problemu, zidentyfikowania głównej przyczyny problemu i wykonania akcji, która spowoduje rozwiązanie tego problemu.

## <a name="monitoring-database-performance"></a>Monitorowanie wydajności bazy danych

Monitorowanie wydajności bazy danych SQL na platformie Azure rozpoczyna się od monitorowania wykorzystania zasobów względem wybranego poziomu wydajności bazy danych. Następujące zasoby powinny być monitorowane w taki sam sposób:
 - **Użycie procesora** — Sprawdź, czy baza danych osiągnie 100% użycia procesora CPU przez dłuższy czas. Może to wskazywać, że baza danych lub wystąpienie musi zostać uaktualnione do wyższej warstwy usług lub że zapytania wykorzystujące większość mocy obliczeniowej powinny być zidentyfikowane i dostrojone.
 - **Statystyka oczekiwania** — Sprawdź, dlaczego zapytania oczekują na niektóre zasoby. Zapytania oczekują na pobranie lub zapisanie danych w plikach bazy danych, oczekiwanie, ponieważ osiągnięto limit zasobów itd.
 - **Użycie we/wy** — Sprawdź, czy baza danych zbliża się do limitów operacji we/wy magazynu bazowego.
 - **Użycie pamięci** — ilość pamięci dostępnej dla bazy danych lub wystąpienia jest proporcjonalna do liczby rdzeni wirtualnych i sprawdza, czy jest to wystarczające dla obciążenia. Stron życia stron to jeden z parametrów, które mogą wskazywać na szybkość usuwania stron z pamięci.

Usługa Azure SQL Database **obejmuje narzędzia i zasoby pomagające w rozwiązywaniu problemów i usuwaniu potencjalnych problemów z wydajnością**. Możliwości można łatwo zidentyfikować, aby usprawnić i zoptymalizować wydajność zapytań bez zmiany zasobów, przeglądając [zalecenia dotyczące dostrajania wydajności](sql-database-advisor.md). Częstymi przyczynami słabej wydajności bazy danych są brakujące indeksy i nieprawidłowo zoptymalizowane zapytania. Te zalecenia dostosowawcze mogą być stosowane w celu zwiększenia wydajności obciążeń. Możemy również umożliwić usłudze Azure SQL Database [Automatyczne Optymalizowanie wydajności zapytań](sql-database-automatic-tuning.md) , stosując wszystkie zidentyfikowane zalecenia i sprawdzając, czy zwiększa to wydajność bazy danych.

Dostępne są następujące opcje monitorowania i rozwiązywania problemów z wydajnością bazy danych:

- W [Azure Portal](https://portal.azure.com)kliknij pozycję **bazy danych SQL**, wybierz bazę danych, a następnie użyj wykresu monitorowania, aby wyszukać zasoby zbliżające się do ich maksymalnego wykorzystania. Użycie jednostek DTU jest domyślnie wyświetlane. Kliknij przycisk **Edytuj** , aby zmienić zakres czasu i wartości.
- Narzędzia, takie jak SQL Server Management Studio zapewniają wiele przydatnych raportów, takich jak [pulpit nawigacyjny wydajności](https://docs.microsoft.com/sql/relational-databases/performance/performance-dashboard?view=sql-server-2017) , do monitorowania wykorzystania zasobów i identyfikowania najważniejszych zapytań zużywających zasoby lub [magazynu zapytań](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store#Regressed) w celu identyfikowania zapytań za pomocą uległa pogorszeniu skuteczności.
- Użyj [szczegółowe informacje o wydajności zapytań](sql-database-query-performance.md) w [Azure Portal](https://portal.azure.com) , aby zidentyfikować zapytania, które spędzają najwięcej zasobów. Ta funkcja jest dostępna tylko w pojedyncza baza danych i elastycznych pulach.
- Użyj [SQL Database Advisor](sql-database-advisor-portal.md) , aby wyświetlić zalecenia dotyczące tworzenia i usuwania indeksów, zapytań parametryzacja i rozwiązywania problemów ze schematem. Ta funkcja jest dostępna tylko w pojedyncza baza danych i elastycznych pulach.
- Automatyczne monitorowanie wydajności bazy danych przy użyciu [usługi Azure SQL Intelligent Insights](sql-database-intelligent-insights.md) . Po wykryciu problemu z wydajnością zostanie wygenerowany dziennik diagnostyczny ze szczegółami i analizą głównych przyczyn problemu. Zalecenia dotyczące poprawy wydajności są udostępniane, gdy jest to możliwe.
- [Włączenie dostrajania automatycznego](sql-database-automatic-tuning-enable.md) i umożliwienie usłudze Azure SQL Database automatycznego rozwiązywania zidentyfikowanych problemów z wydajnością.
- Użyj [dynamicznych widoków zarządzania (widoków DMV)](sql-database-monitoring-with-dmvs.md), [zdarzeń rozszerzonych](sql-database-xevent-db-diff-from-svr.md)i [magazynu zapytań](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) , aby uzyskać bardziej szczegółowe informacje dotyczące rozwiązywania problemów z wydajnością.

> [!TIP]
> Zobacz [wskazówki dotyczące wydajności](sql-database-performance-guidance.md) , aby znaleźć techniki, których można użyć w celu poprawy wydajności Azure SQL Database po zidentyfikowaniu problemu z wydajnością przy użyciu co najmniej jednej z powyższych metod.

## <a name="troubleshoot-performance-issues"></a>Rozwiązywanie problemów z wydajnością

Aby zdiagnozować i rozwiązać problemy z wydajnością, Zacznij od ustalenia stanu poszczególnych aktywnych zapytań oraz warunków, które powodują problemy z wydajnością związane z poszczególnymi Stanami obciążeń. Aby zwiększyć wydajność Azure SQL Database, należy zrozumieć, że każde aktywne żądanie zapytania z aplikacji jest w stanie uruchomionym lub oczekującym. W przypadku rozwiązywania problemów z wydajnością w programie Azure SQL Database należy pamiętać o tym, jak opisano w tym artykule, aby zdiagnozować i rozwiązać problemy z wydajnością.

![Stany obciążeń](./media/sql-database-monitor-tune-overview/workload-states.png)

W przypadku obciążeń z problemami z wydajnością problem z wydajnością może być spowodowany rywalizacją o procesor CPU (warunek **związany** z uruchomieniem) lub pojedyncze zapytania oczekują na coś (warunku **oczekiwania** ).

Przyczyny problemów **związanych** z uruchamianiem mogą być następujące:
- **Problemy** z kompilacją — optymalizator zapytań SQL może generować nieoptymalny plan ze względu na nieodświeżone statystyki, nieprawidłowe oszacowanie liczby wierszy, które będą przetwarzane, lub oszacowanie wymaganej ilości pamięci. Jeśli wiemy, że zapytanie zostało wykonane szybciej w przeszłości lub w innym wystąpieniu (wystąpienie zarządzane lub SQL Server wystąpienie), należy wykonać rzeczywiste plany wykonania i porównać je w celu sprawdzenia, czy są różne. Spróbuj zastosować wskazówki dotyczące zapytania lub ponownie skompilować statystyki lub ponownie skompilować indeksy, aby uzyskać lepszy plan. Włącz automatyczne korekcje planu w Azure SQL Database, aby automatycznie wyeliminować te problemy.
- **Problemy** z wykonywaniem — Jeśli plan zapytania jest optymalny, prawdopodobnie spowoduje to, że niektóre limity zasobów w bazie danych, takie jak przepływność zapisu dziennika, lub używają pofragmentowanych indeksów, które powinny być ponownie skompilowane. Duża liczba współbieżnych zapytań, które spędzają zasoby, może być również przyczyną problemów z wykonywaniem. Problemy **związane** z oczekiwaniem w większości przypadków są związane z problemami z wykonywaniem, ponieważ zapytania, które nie są wykonywane efektywnie, prawdopodobnie oczekują na niektóre zasoby.

Przyczyny problemów **związanych z oczekiwaniem** na problemy mogą być następujące:
- **Blokowanie** — jedno zapytanie może utrzymywać blokadę na niektórych obiektach w bazie danych, podczas gdy inne próbują uzyskać dostęp do tych samych obiektów. Zablokowanie zapytań można łatwo zidentyfikować za pomocą DMV lub narzędzi do monitorowania.
- **Problemy we/wy** — zapytania mogą oczekiwać na zapisanie stron w plikach danych lub dziennika. W tym przypadku zobacz `INSTANCE_LOG_RATE_GOVERNOR`, `WRITE_LOG`lub `PAGEIOLATCH_*` zaczekaj statystyki w DMV.
- **Problemy** z bazą danych tempdb — jeśli obciążenie używa wielu tabel tymczasowych lub wiele wycieków bazy danych tempdb w planach, w których zapytania mogą występować problemy związane z przepływem danych tempdb. 
- **Problemy związane z pamięcią** — może brakować wystarczającej ilości pamięci do obciążania, dzięki czemu mogą zostać porzucane stron życia strony lub zapytania pobierają mniejszą ilość pamięci niż jest to konieczne. W niektórych przypadkach wbudowana analiza w optymalizatora zapytań naprawi te problemy.
 
 W poniższych sekcjach opisano sposób identyfikowania i rozwiązywania niektórych z tych problemów.

## <a name="running-related-performance-issues"></a>Problemy z wydajnością związane z działaniem

Ogólnie rzecz biorąc, jeśli wykorzystanie procesora CPU jest spójne na poziomie 80%, występuje problem z wydajnością. Jeśli występuje problem związany z działaniem, może to być spowodowane przez niewystarczające zasoby procesora CPU lub może być związany z jednym z następujących warunków:

- Zbyt wiele uruchomionych zapytań
- Zbyt wiele zapytań kompilacji
- Co najmniej jedno z wykonywanych zapytań używa podrzędnego planu zapytania

Jeśli okaże się, że występuje problem z wydajnością związany z działaniem, celem jest zidentyfikowanie dokładnego problemu przy użyciu jednej lub kilku metod. Najczęstszymi metodami identyfikowania problemów związanych z działaniem są:

- Użyj [Azure Portal](sql-database-manage-after-migration.md#monitor-databases-using-the-azure-portal) , aby monitorować użycie procentowe procesora CPU.
- Użyj następujących [dynamicznych widoków zarządzania](sql-database-monitoring-with-dmvs.md):

  - [sys. DM _db_resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) zwraca procesor, we/wy i użycie pamięci dla Azure SQL Database. Jeden wiersz istnieje dla każdego 15-sekundowego interwału, nawet jeśli w bazie danych nie ma żadnych działań. Dane historyczne są przechowywane przez jedną godzinę.
  - [sys. resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) zwraca dane użycia procesora CPU i magazynu dla Azure SQL Database. Dane są zbierane i agregowane w ciągu pięciu minut.

> [!IMPORTANT]
> Aby ustawić zapytania T-SQL korzystające z tych widoków DMV do rozwiązywania problemów z użyciem procesora, zobacz temat [identyfikowanie problemów z wydajnością procesora CPU](sql-database-monitoring-with-dmvs.md#identify-cpu-performance-issues).

### <a name="ParamSniffing"></a>Rozwiązywanie problemów z kwerendami przy użyciu zależnych od parametrów problemów dotyczących planu wykonywania zapytań

Problem z planem uwzględniania parametrów (PSP) odnosi się do scenariusza, w którym optymalizator zapytań generuje plan wykonywania zapytania, który jest optymalny tylko dla określonej wartości parametru (lub zestawu wartości), a w pamięci podręcznej jest nieoptymalny dla wartości parametrów używanych w kolejne wykonania. Plany nieoptymalne mogą następnie prowadzić do problemów z wydajnością zapytań i ogólnego obniżenia przepływności obciążeń. Aby uzyskać więcej informacji na temat wykrywania parametrów i przetwarzania zapytań, zobacz [Przewodnik po architekturze przetwarzania zapytań](/sql/relational-databases/query-processing-architecture-guide#ParamSniffing).

Istnieje kilka obejść, które służą do ograniczania tych problemów, z których każdy ma powiązane wady i wady:

- Użyj wskazówki dotyczącej ponownej [kompilacji](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) w każdym wykonaniu zapytania. To obejście umożliwia wymianę czasu kompilacji i zwiększenie procesora w celu zapewnienia lepszej jakości planu. Korzystanie z `RECOMPILE` opcji nie jest często możliwe w przypadku obciążeń wymagających dużej przepływności.
- Użyj [opcji zapytania (Optymalizacja dla...)](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) , aby zastąpić rzeczywistą wartość parametru wartością typowego parametru, która tworzy dobry plan dla większości możliwości wartości parametrów.   Ta opcja wymaga dobrego poznania optymalnych wartości parametrów i skojarzonych cech planu.
- Użyj [opcji (Optymalizuj dla nieznane)](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) warunek zapytania, aby zastąpić rzeczywistą wartość parametru w wymianie przy użyciu średniej gęstości wektora. Innym sposobem jest przechwycenie przychodzących wartości parametrów do zmiennych lokalnych, a następnie użycie zmiennych lokalnych w predykatach zamiast używania samych parametrów. Średnia gęstość musi być *wystarczająca* dla tej konkretnej poprawki.
- Wyłącz wykrywanie parametrów całkowicie za pomocą wskazówki zapytania [DISABLE_PARAMETER_SNIFFING](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) .
- Użyj wskazówki zapytania [KEEPFIXEDPLAN](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) , aby zapobiec ponownym kompilacjom w pamięci podręcznej. W tym obejść założono, że jest to *ten sam plan* wspólny, który jest już w pamięci podręcznej. Możesz również wyłączyć automatyczne aktualizacje w ramach statystyk, aby zmniejszyć prawdopodobieństwo wykluczenia dobrego planu i nowego nieprawidłowego planu.
- Wymuś plan jawnie za pomocą wskazówki zapytania [use plan](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) (jawnie określając, przez ustawienie określonego planu przy użyciu magazynu zapytań lub przez włączenie [dostrajania automatycznego](sql-database-automatic-tuning.md).
- Zastąp pojedynczą procedurę z zagnieżdżonym zestawem procedur, które mogą być używane na podstawie logiki warunkowej i skojarzonych wartości parametrów.
- Utwórz alternatywny sposób wykonywania ciągów dynamicznych do definicji procedury statycznej.

Aby uzyskać dodatkowe informacje dotyczące rozwiązywania tych problemów, zobacz:

- Ten [zapach wpis w](https://blogs.msdn.microsoft.com/queryoptteam/2006/03/31/i-smell-a-parameter/) blogu
- Ta [dynamiczna jakość SQL a Planowanie dla zapytań parametrycznych wpis na](https://blogs.msdn.microsoft.com/conor_cunningham_msft/2009/06/03/conor-vs-dynamic-sql-vs-procedures-vs-plan-quality-for-parameterized-queries/) blogu
- Te [techniki optymalizacji zapytań SQL w SQL Server: Wpis w](https://www.sqlshack.com/query-optimization-techniques-in-sql-server-parameter-sniffing/) blogu z wykrywaniem parametrów

### <a name="troubleshooting-compile-activity-due-to-improper-parameterization"></a>Rozwiązywanie problemów z kompilacją z powodu nieprawidłowych parametryzacja

Gdy zapytanie zawiera literały, aparat bazy danych wybiera automatyczne Sparametryzuj instrukcji lub użytkownik może jawnie Sparametryzuj go w celu zmniejszenia liczby kompilacji. Duża liczba kompilacji zapytania przy użyciu tego samego wzorca, ale różne wartości literałów mogą spowodować wysokie wykorzystanie procesora CPU. Podobnie, jeśli tylko częściowo Sparametryzuj zapytanie, które nadal ma literały, aparat bazy danych nie Sparametryzuj go dalej.  Poniżej znajduje się przykład zapytania częściowo sparametryzowanego:

```sql
SELECT * FROM t1 JOIN t2 ON t1.c1 = t2.c1
WHERE t1.c1 = @p1 AND t2.c2 = '961C3970-0E54-4E8E-82B6-5545BE897F8F'
```

W poprzednim przykładzie, przyjmuje `t1.c1` `@p1` , ale `t2.c2` nadal przyjmuje identyfikator GUID jako literał. W takim przypadku, jeśli zmienisz wartość dla `c2`, kwerenda będzie traktowana jako inna kwerenda i wystąpi Nowa kompilacja. Aby zmniejszyć kompilacje w poprzednim przykładzie, rozwiązanie ma również Sparametryzuj identyfikator GUID.

Następujące zapytanie pokazuje liczbę zapytań według skrótu zapytania, aby określić, czy zapytanie jest prawidłowo sparametryzowane, czy nie:

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
### <a name="factors-influencing-query-plan-changes"></a>Czynniki wpływające na zmiany planu zapytania

Ponowna kompilacja planu wykonania zapytania może spowodować wygenerowanie planu zapytania, który różni się od tego, co zostało pierwotnie zapisane w pamięci podręcznej. Istnieją różne przyczyny, dla których istniejący oryginalny plan może zostać automatycznie ponownie skompilowany:
- Zmiany w schemacie, do których odwołuje się zapytanie
- Zmiany danych w tabelach, do których odwołuje się zapytanie 
- Zmiany w opcjach kontekstu zapytania 

Skompilowany plan może zostać wysunięty z pamięci podręcznej z różnych przyczyn, w tym ponownego uruchomienia wystąpienia, zmiany konfiguracji w zakresie bazy danych, wykorzystanie pamięci i jawne żądania w celu wyczyszczenia pamięci podręcznej. Ponadto użycie wskazówki RECOMPILE oznacza, że plan nie zostanie zapisany w pamięci podręcznej.

Ponowna kompilacja (lub Nowa kompilacja po wykluczeniu pamięci podręcznej) nadal może skutkować generowaniem tego samego planu wykonania zapytania od pierwotnie zaobserwowanego.  Jeśli jednak istnieją zmiany w planie w porównaniu z planem wcześniejszym lub oryginalnym, poniżej przedstawiono najbardziej typowe wyjaśnienia dotyczące przyczyny zmiany planu wykonywania zapytania:

- **Zmieniono projekt fizyczny**. Na przykład zostały utworzone nowe indeksy, które bardziej efektywnie obejmują wymagania zapytania. Mogą one być używane w przypadku nowej kompilacji, jeśli optymalizator zapytań zdecyduje, że jest bardziej optymalny, aby wykorzystać ten nowy indeks niż pierwotnie wybrana dla pierwszej wersji wykonywania zapytania.  Wszelkie zmiany fizyczne obiektów, do których występują odwołania, mogą spowodować powstanie nowego planu w czasie kompilacji.

- **Różnice zasobów serwera**. W scenariuszu, w którym jeden plan różni się od "system A", a "system B" — dostępność zasobów, takich jak liczba dostępnych procesorów, może mieć wpływ na sposób generowania planu.  Na przykład jeśli jeden system ma większą liczbę procesorów, można wybrać plan równoległy. 

- **Różne statystyki**. Statystyki związane z obiektami, do których się odwołuje się, uległy zmianie lub różnią się w istotny sposób od statystyk oryginalnego systemu.  W przypadku zmiany statystyk i ponownej kompilacji optymalizator zapytań będzie używać statystyk w tym konkretnym momencie. Zrewidowane statystyki mogą mieć znacznie różne dystrybucje danych i częstotliwości, które nie były używane w oryginalnej kompilacji.  Te zmiany są używane do oszacowania oszacowania kardynalności (liczba wierszy przepływających do przepływu przez logiczne drzewo zapytań).  Zmiany w oszacowaniach kardynalności mogą pozwolić nam wybrać różne operatory fizyczne i powiązane z nimi zamówienie.  Nawet niewielkie zmiany w statystyce mogą spowodować zmianę planu wykonywania zapytania.

- **Zmieniono poziom zgodności bazy danych lub wersję szacowania kardynalności**.  Zmiany poziomu zgodności bazy danych mogą umożliwić nowe strategie i funkcje, które mogą spowodować powstanie innego planu wykonywania zapytań.  Poza poziomem zgodności bazy danych wyłączenie lub włączenie flagi śledzenia 4199 lub zmiana stanu QUERY_OPTIMIZER_HOTFIXES konfiguracji bazy danych może również mieć wpływ na Opcje planu wykonywania zapytania w czasie kompilacji.  Flagi śledzenia 9481 (Wymuś starsze CE) i 2312 (Wymuś domyślne CE) również mają wpływ na plan. 

### <a name="resolve-problem-queries-or-provide-more-resources"></a>Rozwiązywanie zapytań dotyczących problemów lub podawanie większej ilości zasobów

Po zidentyfikowaniu problemu można dostosować zapytania dotyczące problemów lub uaktualnić rozmiar lub warstwę usługi, aby zwiększyć pojemność usługi Azure SQL Database w celu wchłonięcia wymagań procesora. Aby uzyskać informacje na temat skalowania zasobów dla pojedynczych baz danych, zobacz skalowanie zasobów [pojedynczej bazy danych w Azure SQL Database](sql-database-single-database-scale.md) i skalowanie zasobów dla pul elastycznych, zobacz [skalowanie zasobów puli elastycznej w Azure SQL Database](sql-database-elastic-pool-scale.md). Aby uzyskać informacje na temat skalowania wystąpienia zarządzanego, zobacz [limity zasobów na poziomie wystąpienia](sql-database-managed-instance-resource-limits.md#instance-level-resource-limits).

### <a name="determine-if-running-issues-due-to-increase-workload-volume"></a>Określanie, czy występują problemy z powodu zwiększenia ilości obciążenia

Zwiększenie ruchu i obciążenia aplikacji może obsłużyć zwiększone użycie procesora CPU, ale należy zachować ostrożność w celu prawidłowego zdiagnozowania tego problemu. W scenariuszu o wysokim procesorze CPU Odpowiedz na te pytania, aby określić, czy istotny wzrost procesora jest spowodowany zmianami głośności obciążeń:

1. Czy zapytania z aplikacji są przyczyną problemu z dużym procesorem CPU?
2. Dla najważniejszych zapytań zużywających procesor CPU (które można zidentyfikować):

   - Ustal, czy w tym samym zapytaniu są skojarzone wiele planów wykonania. Jeśli tak, określ przyczynę.
   - W przypadku zapytań z tym samym planem wykonania Ustal, czy czasy wykonywania były spójne i czy licznik wykonywania został zwiększony. Jeśli tak, prawdopodobnie występują problemy z wydajnością spowodowane wzrostem obciążenia.

Podsumowując, jeśli plan wykonywania zapytania nie został wykonany inaczej, ale użycie procesora zwiększyło się wraz z licznikiem wykonywania, prawdopodobnie występuje problem z wydajnością zwiększający obciążenie.

Nie zawsze można łatwo stwierdzić, że istnieje zmiana woluminu obciążenia, która zapewnia problem z procesorem CPU.   Czynniki, które należy wziąć pod uwagę: 

- **Zmieniono użycie zasobów**

  Rozważmy na przykład scenariusz, w którym procesor CPU zwiększył się do 80% przez dłuższy czas.  Samo użycie procesora CPU nie oznacza, że wolumin obciążenia został zmieniony.  Regresje planu wykonywania zapytań i zmiany dystrybucji danych mogą również przyczynić się do większej ilości zasobów, nawet jeśli aplikacja wykonuje te same dokładne obciążenie.

- **Pojawiło się nowe zapytanie**

   Aplikacja może uzyskać nowy zestaw zapytań w różnym czasie.

- **Liczba żądań zwiększonych lub obniżonych**

   Ten scenariusz to najbardziej oczywisty pomiar obciążeń. Liczba zapytań nie zawsze odpowiada większej ilości zasobów. Jednak ta Metryka nadal jest znaczącym sygnałem, przy założeniu, że inne czynniki nie są zmieniane.

## <a name="waiting-related-performance-issues"></a>Problemy z wydajnością związane z oczekiwaniem

Po upewnieniu się, że nie występuje problem z wydajnością o dużym procesorze CPU, jest to problem z wydajnością związany z oczekiwaniem. Oznacza to, że zasoby procesora CPU nie są używane efektywnie, ponieważ procesor CPU czeka na inne zasoby. W takim przypadku następnym krokiem jest zidentyfikowanie zasobów procesora CPU. Najczęściej używane metody wyświetlania najważniejszych kategorii typu oczekiwania:

- [Magazyn zapytań](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) zawiera statystyki oczekiwania na zapytanie w czasie. W magazynie zapytań typy oczekiwania są łączone w kategorie oczekiwania. Mapowanie kategorii oczekiwania na typy oczekiwania jest dostępne w pliku [sys. query_store_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql#wait-categories-mapping-table).
- [sys. DM _db_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database) zwraca informacje o wszystkich oczekiwaniach napotkanych przez wątki, które są wykonywane podczas operacji. Tego widoku zagregowanego można użyć do diagnozowania problemów z wydajnością Azure SQL Database a także z określonymi kwerendami i partiami.
- [sys. DM _os_waiting_tasks](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) zwraca informacje o kolejce oczekiwania zadań oczekujących na jakiś zasób.

W scenariuszach z wysokim procesorem CPU magazyn zapytań i statystyka oczekiwania nie zawsze odzwierciedlają użycie procesora z następujących dwóch przyczyn:

- Nadal mogą być wykonywane zapytania zużywające wysoką moc OBLICZENIOWą, a zapytania nie zakończyły się
- Uruchomione w trybie failover zapytania zużywające wysokie użycie procesora CPU były uruchamiane

Magazyn zapytań i statystyka oczekiwania — śledzenie dynamicznych widoków zarządzania wyświetla tylko wyniki dla pomyślnie ukończonych i limitów czasu zapytań oraz nie wyświetlają danych dla aktualnie wykonywanych instrukcji (aż do zakończenia). Dynamiczny widok zarządzania [sys. DM _exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) umożliwia śledzenie aktualnie wykonywanych zapytań i skojarzonego czasu procesu roboczego.

Jak pokazano na poprzednim wykresie, Najczęstsze oczekiwania to:

- Blokady (blokowanie)
- WE/WY
- `tempdb`Rywalizacja dotycząca
- Oczekiwanie na przydzielenie pamięci

> [!IMPORTANT]
> Aby ustawić zapytania T-SQL używające tych widoków DMV do rozwiązywania problemów związanych z oczekiwaniem, zobacz:
>
> - [Identyfikowanie problemów z wydajnością we/wy](sql-database-monitoring-with-dmvs.md#identify-io-performance-issues)
> - [Identyfikowanie `tempdb` problemów z wydajnością](sql-database-monitoring-with-dmvs.md#identify-io-performance-issues)
> - [Oczekiwanie na określenie przydzielenia pamięci](sql-database-monitoring-with-dmvs.md#identify-memory-grant-wait-performance-issues)
> - [TigerToolbox — czeka i Zamky](https://github.com/Microsoft/tigertoolbox/tree/master/Waits-and-Latches)
> - [TigerToolbox - usp_whatsup](https://github.com/Microsoft/tigertoolbox/tree/master/usp_WhatsUp)

## <a name="improving-database-performance-with-more-resources"></a>Poprawianie wydajności bazy danych za pomocą większej liczby zasobów

Na koniec Jeśli nie ma elementów, które mogą zwiększyć wydajność bazy danych, można zmienić ilość zasobów dostępnych w Azure SQL Database. Więcej zasobów można przypisywać przez zmianę [warstwy usługi DTU](sql-database-service-tiers-dtu.md) pojedynczej bazy danych lub zwiększenie jednostek eDTU puli elastycznej w dowolnym momencie. Alternatywnie, jeśli używasz [modelu zakupu opartego na rdzeń wirtualny](sql-database-service-tiers-vcore.md), możesz zmienić warstwę usług lub zwiększyć zasoby przydzieloną do bazy danych.

1. W przypadku pojedynczych baz danych można [zmienić warstwy usług](sql-database-single-database-scale.md) lub [zasoby obliczeniowe](sql-database-single-database-scale.md) na żądanie, aby zwiększyć wydajność bazy danych.
2. W przypadku wielu baz danych należy rozważyć użycie [pul elastycznych](sql-database-elastic-pool-guidance.md) w celu automatycznego skalowania zasobów.

## <a name="tune-and-refactor-application-or-database-code"></a>Dostrajanie i Refaktoryzacja aplikacji lub kodu bazy danych

Można zmienić kod aplikacji, aby bardziej optymalnie korzystać z bazy danych, zmienić indeksy, zaplanować plany lub użyć wskazówek, aby ręcznie dostosować bazę danych do obciążenia. Znajdź wskazówki i porady dotyczące dostrajania ręcznego i ponownego zapisywania kodu w [temacie Wskazówki dotyczące wydajności](sql-database-performance-guidance.md) .

## <a name="next-steps"></a>Kolejne kroki

- Aby włączyć dostrajanie automatyczne w Azure SQL Database i zezwolić funkcji dostrajania automatycznego w pełni zarządzać obciążeniem, zobacz [Włączanie dostrajania automatycznego](sql-database-automatic-tuning-enable.md).
- Aby skorzystać z dostrajania ręcznego, możesz przejrzeć [zalecenia dotyczące dostrajania w Azure Portal](sql-database-advisor-portal.md) i ręcznie zastosować te, które zwiększają wydajność zapytań.
- Zmień zasoby, które są dostępne w bazie danych, zmieniając [Azure SQL Database warstwy usług](sql-database-performance-guidance.md)
