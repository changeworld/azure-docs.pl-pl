---
title: Rozwiązywanie problemów z wydajnością usługi Azure SQL Database dzięki inteligentnej analizie | Dokumentacja firmy Microsoft
description: Intelligent Insights pomagają w rozwiązywaniu problemów z wydajnością usługi Azure SQL Database.
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
ms.openlocfilehash: fff4aa947f878974d2d0f18f373b8c0917ed7d70
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60703513"
---
# <a name="troubleshoot-azure-sql-database-performance-issues-with-intelligent-insights"></a>Rozwiązywanie problemów z wydajnością usługi Azure SQL Database dzięki inteligentnej analizie

Ta strona zawiera informacje dotyczące usługi Azure SQL Database i problemy z wydajnością w wystąpieniu zarządzanym wykrytych za pośrednictwem [Intelligent Insights](sql-database-intelligent-insights.md) dziennik diagnostyczny wydajności bazy danych. Dane telemetryczne dziennik diagnostyczny funkcji może być przesyłany strumieniowo do [dzienniki usługi Azure Monitor](../azure-monitor/insights/azure-sql.md), [usługi Azure Event Hubs](../azure-monitor/platform/diagnostic-logs-stream-event-hubs.md), [usługi Azure Storage](sql-database-metrics-diag-logging.md#stream-into-storage), lub rozwiązania innych firm dotyczące alertów niestandardowych DevOps i funkcje raportowania.

> [!NOTE]
> Szybkie wydajności bazy danych SQL przy użyciu Intelligent Insights przewodnik rozwiązywania problemów, zobacz [zalecane Rozwiązywanie problemów z przepływem](sql-database-intelligent-insights-troubleshoot-performance.md#recommended-troubleshooting-flow) schematu blokowego, w tym dokumencie.
>

## <a name="detectable-database-performance-patterns"></a>Wzorce wydajności wykrywalna bazy danych

Intelligent Insights automatycznie wykrywa problemy z wydajnością bazy danych SQL i wystąpienie zarządzane bazy danych, na podstawie czasy oczekiwania na wykonanie zapytań, błędów lub błędy przekroczenia limitu czasu. Wyświetla wydajności wykryte wzorce do dzienników diagnostycznych. Wzorce wydajności wykrywalny są podsumowane w poniższej tabeli.

| Wzorce wydajności wykrywalny | Opis usługi Azure SQL Database i pul elastycznych | Opis dla baz danych w wystąpieniu zarządzanym |
| :------------------- | ------------------- | ------------------- |
| [Osiągnięcia limitów zasobów](sql-database-intelligent-insights-troubleshoot-performance.md#reaching-resource-limits) | Użycia dostępnych zasobów (Dtu), wątków roboczych bazy danych lub bazy danych sesji logowania jest dostępna w ramach subskrypcji monitorowanych osiągnął limity. Ma to wpływ na wydajność bazy danych SQL. | Użycie zasobów procesora CPU zbliża się do wystąpienia zarządzanego limitów. Ma to wpływ na wydajność bazy danych. |
| [Wzrost obciążenia](sql-database-intelligent-insights-troubleshoot-performance.md#workload-increase) | Wykryto akumulacja ciągłego obciążenia w bazie danych lub wzrost obciążenia. Ma to wpływ na wydajność bazy danych SQL. | Wykryto wzrostu obciążenia. Ma to wpływ na wydajność bazy danych. |
| [Wykorzystanie pamięci](sql-database-intelligent-insights-troubleshoot-performance.md#memory-pressure) | Procesy robocze, które zażądał przydziałów pamięci trzeba będzie poczekać dla alokacji pamięci są statystycznie istotne ilości czasu. Lub nagromadzenia zwiększone pracowników, którzy żądany przydział pamięci nie istnieje. Ma to wpływ na wydajność bazy danych SQL. | Oczekiwania pracowników, którzy zażądali przydział pamięci dla alokacji pamięci na statystycznie znaczną ilość czasu. Ma to wpływ na wydajność bazy danych. |
| [Blokowanie](sql-database-intelligent-insights-troubleshoot-performance.md#locking) | Blokowanie nadmierne bazy danych zostało wykryte w wpływających na wydajność bazy danych SQL. | Blokowanie nadmierne bazy danych zostało wykryte w wpływających na wydajność bazy danych. |
| [Zwiększona MAXDOP](sql-database-intelligent-insights-troubleshoot-performance.md#increased-maxdop) | Maksymalny stopień równoległości (MAXDOP)-opcja została zmieniona, wpływających na wydajność wykonywania zapytań. Ma to wpływ na wydajność bazy danych SQL. | Maksymalny stopień równoległości (MAXDOP)-opcja została zmieniona, wpływających na wydajność wykonywania zapytań. Ma to wpływ na wydajność bazy danych. |
| [Pagelatch rywalizacji o zasoby](sql-database-intelligent-insights-troubleshoot-performance.md#pagelatch-contention) | Wiele wątków jednocześnie próbujesz uzyskać dostęp do tych samych stron buforu danych w pamięci skutkuje czasy oczekiwania zwiększone i powodują, że pagelatch rywalizacji o zasoby. Ma to wpływ na wydajność bazy danych SQL. | Wiele wątków jednocześnie próbujesz uzyskać dostęp do tych samych stron buforu danych w pamięci skutkuje czasy oczekiwania zwiększone i powodują, że pagelatch rywalizacji o zasoby. Ma to wpływ na wydajność bazy danych. |
| [Brakujący indeks](sql-database-intelligent-insights-troubleshoot-performance.md#missing-index) | Wykryto brakujący indeks wpływających na wydajność bazy danych SQL. | Wykryto brakujący indeks wpływających na wydajność bazy danych. |
| [Nowe zapytanie](sql-database-intelligent-insights-troubleshoot-performance.md#new-query) | Nowe zapytanie zostało wykryte, mających wpływ na ogólną wydajność bazy danych SQL. | Nowe zapytanie zostało wykryte, mających wpływ na ogólną wydajność bazy danych. |
| [Statystyka zwiększone oczekiwania](sql-database-intelligent-insights-troubleshoot-performance.md#increased-wait-statistic) | Czasy oczekiwania zwiększone bazy danych zostały wykryte wpływających na wydajność bazy danych SQL. | Czasy oczekiwania zwiększone bazy danych zostały wykryte wpływających na wydajność bazy danych. |
| [Bazy danych TempDB rywalizacji o zasoby](sql-database-intelligent-insights-troubleshoot-performance.md#tempdb-contention) | Wiele wątków próbuje uzyskać dostęp do tego samego zasobu bazy danych TempDB, powodując "wąskie gardło". Ma to wpływ na wydajność bazy danych SQL. | Wiele wątków próbuje uzyskać dostęp do tego samego zasobu bazy danych TempDB, powodując "wąskie gardło". Ma to wpływ na wydajność bazy danych. |
| [Mała liczba jednostek DTU w puli elastycznej](sql-database-intelligent-insights-troubleshoot-performance.md#elastic-pool-dtu-shortage) | Brak dostępnych jednostek Edtu w puli elastycznej ma wpływ na wydajność bazy danych SQL. | Niedostępne dla wystąpienia zarządzanego ponieważ używa ona modelu rdzenia wirtualnego. |
| [Planowanie regresji](sql-database-intelligent-insights-troubleshoot-performance.md#plan-regression) | Wykryto nowy plan lub zmiany w obciążeniu istniejącego planu. Ma to wpływ na wydajność bazy danych SQL. | Wykryto nowy plan lub zmiany w obciążeniu istniejącego planu. Ma to wpływ na wydajność bazy danych. |
| [Zmiana wartości w zakresie bazy danych konfiguracji](sql-database-intelligent-insights-troubleshoot-performance.md#database-scoped-configuration-value-change) | Wykryto zmianę konfiguracji w bazie danych SQL wpływających na wydajność bazy danych. | Wykryto zmianę konfiguracji w bazie danych wpływających na wydajność bazy danych. |
| [Powolne klienta](sql-database-intelligent-insights-troubleshoot-performance.md#slow-client) | Nie może wystarczająco szybko wykorzystać dane wyjściowe z bazy danych jest powolne aplikacji klienta. Ma to wpływ na wydajność bazy danych SQL. | Nie może wystarczająco szybko wykorzystać dane wyjściowe z bazy danych jest powolne aplikacji klienta. Ma to wpływ na wydajność bazy danych. |
| [Obniżenie warstwy cenowej](sql-database-intelligent-insights-troubleshoot-performance.md#pricing-tier-downgrade) | Ceny akcji obniżenia poziomu warstwy spadły dostępnych zasobów. Ma to wpływ na wydajność bazy danych SQL. | Ceny akcji obniżenia poziomu warstwy spadły dostępnych zasobów. Ma to wpływ na wydajność bazy danych. |

> [!TIP]
> Aby zoptymalizować wydajność ciągłe bazy danych SQL, należy włączyć [dostrajania automatycznego usługi Azure SQL Database](sql-database-automatic-tuning.md). Ta funkcja unikatowy wbudowane funkcje analizy bazy danych SQL Database stale monitoruje bazę danych SQL, automatycznie Dostraja indeksy i stosuje poprawki planu wykonania zapytania.
>

W poniższej sekcji opisano wzorców wydajności wykrywalny bardziej szczegółowo.

## <a name="reaching-resource-limits"></a>Osiągnięcia limitów zasobów

### <a name="what-is-happening"></a>Co się dzieje

Wzorzec ten wykrywalny wydajność łączy problemy z wydajnością, that are related to osiągnięcia limitów dostępnych zasobów, procesów roboczych ograniczenia i limity sesji. Po wykryciu problemu z wydajnością, pole opisu dziennik diagnostyczny wskazuje, czy problem z wydajnością powiązany zasobów, procesów roboczych lub ograniczenia sesji.

Zasoby w bazie danych SQL są zwykle określane [jednostek DTU](sql-database-what-is-a-dtu.md) lub [vCore](sql-database-service-tiers-vcore.md) zasobów. Wzorzec osiągnięcia limitów zasobów jest rozpoznawany w przypadku wykrycia obniżenia wydajności zapytań jest spowodowany przez żadnego ograniczenia zasobów mierzonego osiągnięcia.

Zasób limity sesji wskazuje, że liczba dostępnych współbieżnych logowań w bazie danych SQL. Tego wzorca wydajności został rozpoznany, gdy liczba dostępnych współbieżnych logowań do bazy danych osiągnęła aplikacje, które są podłączone do bazy danych SQL. Jeśli aplikacje próbują używać więcej sesji, niż jest dostępne w bazie danych, ma wpływ wydajności zapytań.

Osiągnięcia limitów proces roboczy jest konkretnym przypadku osiągnięcia limitów zasobów, ponieważ dostępne procesy robocze nie są liczone w użycie jednostek DTU i rdzeniach wirtualnych. Osiągnięcia limitów procesu roboczego w bazie danych może spowodować powstanie czasy oczekiwania specyficznych dla zasobów, co powoduje spadek wydajności zapytania.

### <a name="troubleshooting"></a>Rozwiązywanie problemów

Dziennik diagnostyczny generuje skróty zapytania zapytań, które miała wpływ na wydajność i wartości procentowych zużycia zasobów. Te informacje można użyć jako punktu wyjścia do optymalizacji obciążenia baz danych. W szczególności można optymalizować zapytania, które mają wpływ na pogorszenie wydajności, dodając indeksów. Lub można zoptymalizować aplikacje za pomocą więcej nawet rozkład obciążenia. Jeśli nie możesz zmniejszyć obciążenia lub ustawić optymalizacje, należy rozważyć zwiększenie warstwy cenowej subskrypcji bazy danych SQL, aby zwiększyć ilość dostępnych zasobów.

Jeśli osiągnięto limitów dostępnych sesji można zoptymalizować swoje aplikacje dzięki zmniejszeniu liczby logowań wprowadzonych w bazie danych. Jeśli nie możesz zmniejszyć liczbę logowania z poziomu aplikacji w bazie danych, należy rozważyć zwiększenie warstwy cenowej bazy danych. Lub możesz podzielić i przenieść bazę danych do wielu baz danych w celu zrównoważonego dystrybucji obciążenia.

Aby uzyskać więcej wskazówek na temat stosowania ograniczeń sesji, zobacz [radzenia sobie z limitów maksymalnej logowania do bazy danych SQL](https://blogs.technet.microsoft.com/latam/20../../how-to-deal-with-the-limits-of-azure-sql-database-maximum-logins/). Zobacz [Przegląd zasobów limity na serwerze bazy danych SQL](sql-database-resource-limits-database-server.md) uzyskać informacji dotyczących ograniczeń na poziomach serwera i subskrypcji.

## <a name="workload-increase"></a>Wzrost obciążenia

### <a name="what-is-happening"></a>Co się dzieje

Ten wzorzec wydajności identyfikuje problemy spowodowane przez zwiększenie obciążenia, lub w postaci przeprowadzanie bardziej dotkliwych pile-up obciążenia.

To wykrywanie następuje przy użyciu kombinacji kilku metryk. Podstawowe metryki, mierzony jest wykrywania wzrostu obciążenia w porównaniu z ostatnim odniesienia obciążenia. Druga forma funkcji wykrywania opiera się na mierzeniu duży wzrost aktywnych wątków roboczych, która jest wystarczająco duża, aby wpłynąć na wydajność zapytań.

W postaci przeprowadzanie bardziej dotkliwych obciążenie może stale Ustawianie ze względu na brak możliwości bazy danych SQL do obsługi obciążenia. Wynik jest stale rosnącej rozmiaru obciążenia, co jest warunkiem pile-up obciążenia. Z powodu tego stanu zwiększa się obciążenie czeka na wykonanie czas. Ten stan reprezentuje jedną z najbardziej poważne problemy z wydajnością bazy danych. Ten problem zostanie wykryty przez monitorowanie wzrost liczbę wątków roboczych zostało przerwane. 

### <a name="troubleshooting"></a>Rozwiązywanie problemów

Dziennik diagnostyczny generuje liczby zapytań, których wykonanie umożliwiło zwiększenie i skrót zapytania zapytanie o największy wkład wzrostu obciążenia. Te informacje można użyć jako punktu wyjścia do optymalizacji obciążenia. Zapytanie zidentyfikowane jako współautor największy wzrost obciążenia jest szczególnie przydatne, zapoznając się.

Należy rozważyć, dystrybucji obciążenia bardziej równomiernie w bazie danych. Należy wziąć pod uwagę, optymalizowanie zapytanie, które ma wpływ na wydajność, dodając indeksów. Mogą również rozpowszechniać obciążenia między wieloma bazami danych. Jeśli te rozwiązania nie są możliwe, należy rozważyć zwiększenie warstwy cenowej subskrypcji bazy danych SQL, aby zwiększyć ilość dostępnych zasobów.

## <a name="memory-pressure"></a>Wykorzystanie pamięci

### <a name="what-is-happening"></a>Co się dzieje

Tego wzorca wydajności wskazuje pogorszenie wydajności bazy danych bieżącej, spowodowane przez wykorzystanie pamięci lub w postaci przeprowadzanie bardziej dotkliwych warunek pile-up pamięci w porównaniu do poprzednich odniesienia wydajności siedmiu dni.

Wykorzystanie pamięci wskazuje, że warunek wydajności, w którym jest dużą liczbę wątków roboczych żądania pamięci przydziela w bazie danych SQL. Duża powoduje, że stan użycia dużą ilość pamięci, w którym bazy danych SQL nie jest w stanie efektywnie przydzielić pamięci na wszystkie procesy robocze, które wysyłają żądanie. Jedną z najbardziej typowe przyczyny tego problemu jest powiązana z ilość pamięci dostępnej w bazie danych SQL w jednej strony. Z drugiej strony wzrost obciążenia powoduje zwiększenie wątków roboczych i wykorzystania pamięci.

Przeprowadzanie bardziej dotkliwych formularza wykorzystania pamięci jest warunkiem pile-up pamięci. Ten stan wskazuje, większej liczby wątków roboczych żądasz przydziałów pamięci niż zapytania zwalnianie pamięci. Tę liczbę wątków roboczych, które żądania pamięci przyznaje również może się stale zwiększać (gromadzeniu się) ponieważ nie można przydzielić pamięci wystarczająco wydajnego, aby sprostać aparatu bazy danych SQL. Stan pile-up pamięci reprezentuje jedną z najbardziej poważne problemy z wydajnością bazy danych.

### <a name="troubleshooting"></a>Rozwiązywanie problemów

Dziennik diagnostyczny Wyświetla szczegóły pamięci obiektu magazynu z pracownik. (to znaczy wątku roboczego) oznaczone jako najwyższy przyczyny wysokiego użycia pamięci i sygnatury czasowe istotne. Te informacje można użyć jako podstawy do rozwiązywania problemów. 

Można zoptymalizować i usuwać zapytań dotyczących urzędników o największym wykorzystaniu pamięci. Możesz również upewnić się, że nie są wykonywanie zapytań o dane, która nie planujesz używania. Dobrym rozwiązaniem jest zawsze używać klauzuli WHERE w zapytaniach. Ponadto zaleca się, tworzenie indeksów nieklastrowanych wyszukiwać dane, a nie Przeprowadź jego skanowanie.

Możesz także zmniejszyć obciążenie, optymalizacja lub ich dystrybucję za pośrednictwem wielu baz danych. Lub można rozdzielić obciążenia między wieloma bazami danych. Jeśli te rozwiązania nie są możliwe, należy rozważyć zwiększenie warstwy cenowej subskrypcji bazy danych SQL, aby zwiększyć ilość zasobów pamięci, dostępne w bazie danych.

Aby uzyskać dodatkowe porady dotyczące rozwiązywania problemów, zobacz [pamięci przydziela skalnym: Tajemniczymi konsumenta pamięci programu SQL Server z wieloma nazwami](https://blogs.msdn.microsoft.com/sqlmeditation/20../../memory-meditation-the-mysterious-sql-server-memory-consumer-with-many-names/).

## <a name="locking"></a>Blokowanie

### <a name="what-is-happening"></a>Co się dzieje

Tego wzorca wydajności wskazuje Bieżąca wydajność bazy danych, w którym blokowania nadmierne bazy danych zostanie wykryty w porównaniu do poprzednich odniesienia wydajności 7 dniowy obniżenie wydajności. 

Nowoczesne RDBMS blokowanie jest istotne dla wdrożenia wielowątkowe systemy, w których wydajność jest maksymalizowana, uruchamiając wiele równoczesnych procesów roboczych i transakcji równoległych bazy danych, jeśli jest to możliwe. Blokowanie w tym kontekście odnosi się do mechanizmu dostęp do wbudowanych, w którym tylko w ramach jednej transakcji dostęp wyłącznie wierszy, strony, tabele i pliki, które są wymagane, a nie rywalizować z innej transakcji dla zasobów. Po zakończeniu transakcji, która zablokowane zasoby przeznaczone do użycia z nich jest zwalniana blokady w odniesieniu do tych zasobów, co pozwala innych transakcji dostęp do wymaganych zasobów. Aby uzyskać więcej informacji na temat blokowania, zobacz [blokowanie aparatu bazy danych](https://msdn.microsoft.com/library/ms190615.aspx).

Jeśli transakcje wykonywany przez aparat SQL oczekuje na dłuższe okresy czasu, aby uzyskać dostęp do zasobów zablokowany do użytku, czas oczekiwania powoduje, że spowolnienia wydajności wykonywania obciążenia. 

### <a name="troubleshooting"></a>Rozwiązywanie problemów

Dziennik diagnostyczny generuje blokowanie szczegółowe informacje, które służy jako podstawa do rozwiązywania problemów. Można analizować zgłoszone blokowania zapytania, oznacza to, że zapytania, które wprowadzają blokowania spadek wydajności, i usuń je. W niektórych przypadkach może się niepowodzeniem z optymalizacją zapytań blokowania.

Najprostszy i najbezpieczniejszy sposób, aby rozwiązać ten problem jest zapewnienie transakcji krótki i zmniejszenie śladu blokady najbardziej kosztowne zapytania. Możesz rozbić dużych partii operacji na mniejsze czynności. Dobrą praktyką jest, aby ograniczyć wpływ blokady zapytania, wprowadzając jak najbardziej efektywne zapytania. Zmniejsz dużych skanowania, ponieważ niekorzystnie wpłynąć na ogólną wydajność bazy danych i zwiększyć szanse zakleszczenia. Zidentyfikowanych zapytań, które powodują blokowanie można tworzyć nowe indeksy lub dodawanie kolumn do istniejącego indeksu, aby uniknąć skanów tabeli. 

Aby uzyskać więcej sugestii, zobacz [sposobu rozwiązywania problemów z blokowaniem, które są spowodowane przez przekazanie blokady w programie SQL Server](https://support.microsoft.com/help/323630/how-to-resolve-blocking-problems-that-are-caused-by-lock-escalation-in).

## <a name="increased-maxdop"></a>Zwiększona MAXDOP

### <a name="what-is-happening"></a>Co się dzieje

Wzorzec ten wykrywalny wydajności wskazuje, że warunek, w których był planu wykonania zapytania w wybranym zrównoleglona więcej niż powinien być. Optymalizator zapytań bazy danych SQL może zwiększyć wydajność obciążeń przez wykonanie zapytania równolegle w celu przyspieszenia rzeczy, jeśli jest to możliwe. W niektórych przypadkach pracowników równoległe przetwarzanie zapytania poświęcić więcej czasu, na oczekiwanie na siebie, aby zsynchronizować i scal wyniki w porównaniu do wykonywania tego samego zapytania, z mniejszą liczbę równoległych procesów roboczych lub nawet w niektórych przypadkach, w porównaniu z jednego wątku.

System eksperta analizuje Bieżąca wydajność bazy danych w porównaniu do okresu linii bazowej. Określa, czy wcześniej uruchomione zapytanie działa wolniej niż wcześniej, ponieważ plan wykonania zapytania jest bardziej zrównoleglona niż należy go.

Opcja MAXDOP serwera konfiguracji w bazie danych SQL jest używana do kontrolowania, jak wiele rdzeni Procesora może służyć do wykonania tego samego zapytania równolegle. 

### <a name="troubleshooting"></a>Rozwiązywanie problemów

Dziennik diagnostyczny generuje skróty zapytania związane z zapytania, dla których czas realizacji podwyższony, ponieważ zostały one więcej niż powinien być zrównoleglona. Dziennik również generuje CXP czasy oczekiwania. Tym razem reprezentuje czas oczekiwania dla wszystkich wątków na zakończenie scalania wyników i realizowanie wątku pojedynczego organizatora/koordynatora (wątek 0). Ponadto dziennik diagnostyczny generuje czasy oczekiwania, niska, wykonywanie zapytań oczekujących w ogólnej wykonywania. Te informacje można użyć jako podstawy do rozwiązywania problemów.

Najpierw należy zoptymalizować lub uproszczenie kwerend złożonych. Dobrym rozwiązaniem jest podział zadań wsadowych długa na mniejsze. Upewnij się też, czy utworzony indeksów w celu obsługi zapytań. Można również ręcznie wymusić maksymalnego stopnia równoległości (MAXDOP) dla zapytania, które zostało oznaczone jako niska wykonywania. Aby skonfigurować tę operację za pomocą języka T-SQL, zobacz [Konfigurowanie opcji konfiguracji serwera MAXDOP](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option).

Ustawienie serwera MAXDOP opcji konfiguracji na wartość zero (0), zgodnie z domyślną wartość oznacza bazy danych SQL za pomocą wszystkich dostępnych rdzeni Procesora logicznego zrównoleglić wątków do wykonywania pojedynczego zapytania. Ustawienia MAXDOP do jednego (1) oznacza, że tylko jednego rdzenia może służyć do wykonywania pojedynczego zapytania. W praktyce oznacza to, że równoległości jest wyłączona. W zależności od podstawy dla przypadku dostępnych rdzeni do bazy danych i diagnostyki rejestrować informacje, można dostrajanie opcji MAXDOP, liczbę rdzeni używanych na potrzeby równoległego wykonywania zapytań, który może rozwiązać ten problem w Twoim przypadku.

## <a name="pagelatch-contention"></a>Pagelatch rywalizacji o zasoby

### <a name="what-is-happening"></a>Co się dzieje

Ten wzorzec wydajności wskazuje bieżący spadek wydajności obciążeń bazy danych z powodu rywalizacji pagelatch w porównaniu do poprzednich 7 dniowy obciążenia linii bazowej.

Zamków to mechanizmy synchronizacji uproszczone, użyta przez usługę SQL Database, aby włączyć wielowątkowości. Gwarantują one zgodności struktur w pamięci, które obejmują indeksy, strony danych i innych wewnętrznych struktur.

Istnieje wiele typów zamków dostępne w bazie danych SQL. Dla uproszczenia zamków buforu są używane do ochrony stron w pamięci w puli buforów. Zamków We/Wy są używane do ochrony stron, które nie zostały jeszcze załadowane do puli buforów. Zawsze, gdy dane są zapisywane do lub odczytywanie strony w puli buforów, wątek roboczy musi najpierw uzyskać zatrzaśnięcie buforu dla strony. Zawsze, gdy wątek roboczy próbuje uzyskać dostęp do strony, które nie są już dostępne w puli buforów w pamięci, we/wy się żądanie załadować wymaganych informacji z magazynu. Następująca sekwencja zdarzeń wskazuje przeprowadzanie bardziej dotkliwych formie spadek wydajności.

Rywalizacja o zamków strony występuje, gdy wiele wątków jednocześnie podjąć próbę uzyskania zamków na tę samą strukturę w pamięci, które wprowadza czas oczekiwania zwiększona do wykonywania zapytań. W przypadku rywalizacji o zasoby we/wy pagelatch gdy dane muszą być dostępne z magazynu, czas oczekiwania jest jeszcze większym. To może wpłynąć na wydajność obciążeń znacznie. Rywalizacja Pagelatch jest najbardziej typowym scenariuszem wątki, które oczekuje na siebie nawzajem i rywalizując o zasoby w wielu systemach procesora CPU.

### <a name="troubleshooting"></a>Rozwiązywanie problemów

Dziennik diagnostyczny generuje pagelatch szczegóły rywalizacji. Te informacje można użyć jako podstawy do rozwiązywania problemów.

Ponieważ pagelatch jest mechanizm kontroli wewnętrznej bazy danych SQL Database, automatycznie określają, kiedy ich używać. Decyzji aplikacji, w tym projektu schematu może mieć wpływ na zachowanie pagelatch ze względu na zachowanie deterministyczne zatrzaśnięcia.

Jedną z metod obsługi rywalizacji zatrzaśnięcia jest wymienić klucz indeksu sekwencyjne kluczem niesekwencyjną sieć w celu równomiernego rozkładania wstawia w zakresie indeksu. Zazwyczaj wiodących kolumn w indeksie dystrybuuje obciążenie proporcjonalnie. Inną metodą należy wziąć pod uwagę, jest Partycjonowanie tabel. Tworzenie skrótu schemat z kolumną obliczaną w tabeli partycjonowanej partycji jest typowym podejściem łagodzenia zatrzaśnięcia nadmierną rywalizację. W przypadku rywalizacji o zasoby we/wy pagelatch wprowadzenie do indeksów pomaga rozwiązać ten problem z wydajnością. 

Aby uzyskać więcej informacji, zobacz [diagnozowanie i rozwiązywanie zatrzasnąć rywalizacji o zasoby w programie SQL Server](https://download.microsoft.com/download/B/9/E/B9EDF2CD-1DBF-4954-B81E-82522880A2DC/SQLServerLatchContention.pdf) (pobrania w formacie PDF).

## <a name="missing-index"></a>Brakujący indeks

### <a name="what-is-happening"></a>Co się dzieje

Ten wzorzec wydajności oznacza spadek wydajności bieżące obciążenie bazy danych w porównaniu do poprzednich 7 dniowy linii bazowej aktualizacji ze względu na Brak indeksu.

Indeks jest używany w celu przyspieszenia wydajność zapytań. Zapewnia szybki dostęp do danych w tabelach, zmniejszając liczbę stron zestawu danych, które muszą być odwiedzi lub skanowania.

Określonego zapytania, które spowodowało obniżenie wydajności są identyfikowane za pomocą tego wykrywania, dla której będzie korzystne wydajność tworzenia indeksów.

### <a name="troubleshooting"></a>Rozwiązywanie problemów

Dziennik diagnostyczny generuje skróty zapytania dla zapytania, które zostały zidentyfikowane wpłynąć na wydajność obciążenia. Umożliwia tworzenie indeksów dla tych zapytań. Można także zoptymalizować lub usunąć te zapytania, jeśli nie są wymagane. Jest rozwiązaniem w zakresie dobrą wydajność w celu uniknięcia, wykonywanie zapytań o dane, które nie są używane.

> [!TIP]
> Czy wiesz, wbudowana funkcja analizy SQL Database automatycznie zarządzać sprzedające indeksami swoich baz danych?
>
> Optymalizacja ciągłej wydajności bazy danych SQL Database, zaleca się włączenie [dostrajania automatycznego usługi SQL Database](sql-database-automatic-tuning.md). Ta funkcja unikatowy wbudowane funkcje analizy bazy danych SQL Database stale monitoruje bazę danych SQL i automatycznie Dostraja i tworzy indeksami swoich baz danych.
>

## <a name="new-query"></a>Nowe zapytanie

### <a name="what-is-happening"></a>Co się dzieje

Ten wzorzec wydajności wskazuje, że nowe zapytanie wykryto który jest najlepiej i wpływu na wydajność obciążenia w porównaniu do linii bazowej wydajności 7 dniowy.

Czasami napisanie zapytania dobrej wydajności może być trudniejszym zadaniem. Aby uzyskać więcej informacji o pisaniu zapytań, zobacz [zapytania SQL pisania](https://msdn.microsoft.com/library/bb264565.aspx). Aby zoptymalizować wydajność zapytań, zobacz [dostrajanie zapytań](https://msdn.microsoft.com/library/ms176005.aspx).

### <a name="troubleshooting"></a>Rozwiązywanie problemów

Diagnostyka rejestrować informacje o danych wyjściowych maksymalnie dwa nowe większość korzystających z procesora CPU zapytania, łącznie z ich skrótów zapytania. Ponieważ wykryto zapytanie ma wpływ na wydajność obciążeń, można zoptymalizować zapytania. Dobrą praktyką jest, aby pobierać tylko te dane, które należy użyć. Firma Microsoft zaleca, za pomocą zapytań z klauzulą WHERE. Zalecamy również uprościć złożone zapytania, a następnie Podziel na mniejsze zapytania. Innym dobrym rozwiązaniem jest podział dużych partii zapytań na mniejsze zapytania usługi batch. Wprowadzenie do indeksów dla nowych zapytań zazwyczaj jest dobrą praktyką, aby rozwiązać ten problem z wydajnością.

Należy rozważyć użycie [usługi Azure SQL Database Query Performance Insight](sql-database-query-performance.md).

## <a name="increased-wait-statistic"></a>Statystyka zwiększone oczekiwania

### <a name="what-is-happening"></a>Co się dzieje

Wzorzec ten wykrywalny wydajności wskazuje obciążenia spadek wydajności, w którym niska, wykonywanie zapytań są identyfikowane w porównaniu z ostatnim punktem odniesienia 7 dniowy obciążenia.

W takim przypadku system nie można sklasyfikować słabej wydajności zapytań w innych kategoriach wydajności warstwy standardowa wykrywalny, ale on wykryty odpowiedzialny za regresji statystyki oczekiwania. Dlatego traktuje je jako zapytań przy użyciu *wzrosła statystyki oczekiwania*, gdzie również odpowiedzialny za regresji statystyki oczekiwania jest widoczna. 

### <a name="troubleshooting"></a>Rozwiązywanie problemów

Diagnostyki rejestrowanie danych wyjściowych informacji o szczegółach czas oczekiwania zwiększone i skrótami query objęte zapytania.

Ponieważ system nie może pomyślnie Identyfikuj główne przyczyny niska, wykonywania zapytań, informacje diagnostyczne jest dobry punkt wyjścia do ręcznego rozwiązywania problemów. Można zoptymalizować wydajność kwerend. Dobrą praktyką jest, aby pobrać tylko te dane, które są potrzebne do użycia, a także uprościć i podzielenie złożonych zapytań na mniejsze. 

Aby uzyskać więcej informacji na temat optymalizowania wydajności zapytań, zobacz [dostrajanie zapytań](https://msdn.microsoft.com/library/ms176005.aspx).

## <a name="tempdb-contention"></a>Bazy danych TempDB rywalizacji o zasoby

### <a name="what-is-happening"></a>Co się dzieje

Wzorzec ten wykrywalny wydajności wskazuje warunek wydajności bazy danych, w której istnieje "wąskie gardło" wątków podjęcie próby dostępu do zasobów bazy danych tempDB. (Ten warunek nie jest związane z We/Wy). Typowy scenariusz, w tym problemów z wydajnością znajduje się setki równoczesnych zapytań, wszystkie tworzenie, użycia, a następnie upuść tabele małych bazy danych tempDB. System wykrył, że liczba jednoczesnych kwerend, przy użyciu tych samych tabelach bazy danych tempDB powiększyć przy użyciu wystarczających obserwowane wpłynąć na wydajność bazy danych w porównaniu do poprzednich odniesienia wydajności 7 dniowy.

### <a name="troubleshooting"></a>Rozwiązywanie problemów

Dziennik diagnostyczny Wyświetla szczegóły rywalizacji o zasoby bazy danych tempDB. Dane można użyć jako punktu wyjścia do rozwiązywania problemów. Istnieją dwie rzeczy, które mogą prowadzić do zmniejszenia tego rodzaju rywalizacji i zwiększyć przepływność całkowitego obciążenia: Aby zatrzymać, użycie tabel tymczasowych. Możesz także użyć tabele zoptymalizowane pod kątem pamięci. 

Aby uzyskać więcej informacji, zobacz [wprowadzenie do tabel zoptymalizowanych pod kątem pamięci](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/introduction-to-memory-optimized-tables). 

## <a name="elastic-pool-dtu-shortage"></a>Mała liczba jednostek DTU w puli elastycznej

### <a name="what-is-happening"></a>Co się dzieje

Wzorzec ten wykrywalny wydajności wskazuje pogorszenie Bieżąca wydajność obciążeń bazy danych w porównaniu do poprzednich 7 dniowy linii bazowej. Jest ono spowodowane brakiem dostępnych jednostek Dtu w puli elastycznej w Twojej subskrypcji. 

Zasoby w bazie danych SQL są zwykle nazywane [zasobów jednostek DTU](sql-database-purchase-models.md#dtu-based-purchasing-model), które składają się z mieszany pomiar procesora CPU i we/wy zasobów (danych i dziennika transakcji we/wy). [Zasoby platformy Azure puli elastycznej](sql-database-elastic-pool.md) są używane jako pula zasobów eDTU dostępnych współużytkowane przez wiele baz danych dla celów skalowania. W przypadku zasobów eDTU dostępnych w elastycznej puli nie są wystarczająco duże, aby obsługiwać wszystkie bazy danych w puli, problem z wydajnością mała liczba pul elastycznych jednostek DTU została wykryta przez system.

### <a name="troubleshooting"></a>Rozwiązywanie problemów

Dziennik diagnostyczny Wyświetla informacje o puli elastycznej, wymieniono najważniejsze baz danych korzystanie z jednostek DTU i zapewnia procent jednostek DTU puli używane przez bazy danych korzystające z góry.

Ponieważ ten warunek wydajności jest związany z wieloma bazami danych przy użyciu tej samej puli jednostek Edtu w puli elastycznej, kroki rozwiązywania problemów skoncentrować się na najważniejszych baz danych korzystanie z jednostek DTU. Można zmniejszyć obciążenie bazy korzystanie z góry, w tym optymalizację zapytania korzystające z góry w tych bazach danych. Możesz również zapewnić, że nie są wykonywanie zapytań o dane, które nie są używane. Innym rozwiązaniem jest Optymalizuj aplikacje za pomocą górnej baz danych korzystanie z jednostek DTU i ponowne rozdzielanie obciążenia między wieloma bazami danych.

Jeśli redukcji oraz optymalizację bieżące obciążenie na górnym korzystające z jednostek DTU bazy danych nie są możliwe, należy rozważyć zwiększenie warstwy cenowej puli elastycznej. Takie zwiększenie powoduje zwiększenie liczby jednostek Dtu dostępne w puli elastycznej.

## <a name="plan-regression"></a>Planowanie regresji

### <a name="what-is-happening"></a>Co się dzieje

Wzorzec ten wykrywalny wydajności wskazuje, że warunek, w którym korzysta z planu wykonania zapytania nieoptymalne bazy danych SQL. Suboptymalny plan zwykle powoduje, że wykonanie zapytania zwiększona, co prowadzi do już czekać czasy dla zapytania bieżącej i innych.

Bazy danych SQL określa plan wykonywania zapytań o najmniejszej kosztów do wykonywania zapytań. Jako typ zmiany zapytań i obciążeń czasami istniejących planów nie są już wydajne, lub być może baza danych SQL zostało zgłoszone przez Ciebie dobrą ocenę. Jako korekty można ręcznie wymusić plany wykonywania zapytań.

Wzorzec ten wykrywalny wydajność łączy trzy różne przypadki regresji plan: nowy plan regresji, stary plan regresji i istniejące obciążenie zmienić plany. Jest podawany jako określonego typu regresji planu, który wystąpił *szczegóły* właściwości w dzienniku diagnostyki.

Nowy warunek regresji plan odnosi się do stanu, w którym uruchamia nowy plan wykonania zapytania, które nie są wydajne niż stary plan wykonywania bazy danych SQL. Stary plan regresji warunek odnosi się do stanu kiedy bazy danych SQL przełącza z przy użyciu nowego, bardziej wydajne plan stary plan, który nie jest tak wydajna, jak nowy plan. Istniejące regresji obciążenia zmienić plany odnosi się do stanu, w którym stare i nowe plany stale alternatywnych, z saldem bardziej przechodząc w kierunku plan wykonywania niska.

Aby uzyskać więcej informacji na temat planu regresji, zobacz [co to jest plan regresji w programie SQL Server?](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../what-is-plan-regression-in-sql-server/). 

### <a name="troubleshooting"></a>Rozwiązywanie problemów

Dziennik diagnostyczny generuje skróty zapytania, identyfikator dobrego planu, identyfikator planu zły i zapytania identyfikatorów. Te informacje można użyć jako podstawy do rozwiązywania problemów.

Możesz analizować, który plan jest lepsze wykonywanie dla określonego zapytania, które można zidentyfikować za pomocą wartości zapytania. Po ustaleniu, jakiego planu sprawdzi się najlepiej w zapytaniach, możesz ją ręcznie wymusić. 

Aby uzyskać więcej informacji, zobacz [Dowiedz się, jak program SQL Server zapobiega regresji plan](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../you-shall-not-regress-how-sql-server-2017-prevents-plan-regressions/).

> [!TIP]
> Czy wiesz, wbudowana funkcja analizy SQL Database automatycznie zarządzać sprzedające plany wykonywania zapytań dla bazy danych?
>
> Optymalizacja ciągłej wydajności bazy danych SQL Database, zaleca się włączenie [dostrajania automatycznego usługi SQL Database](sql-database-automatic-tuning.md). Ta funkcja unikatowy wbudowane funkcje analizy bazy danych SQL Database stale monitoruje bazę danych SQL i automatycznie Dostraja i tworzy sprzedające zapytania plany wykonywania dla bazy danych.
>

## <a name="database-scoped-configuration-value-change"></a>Zmiana wartości w zakresie bazy danych konfiguracji

### <a name="what-is-happening"></a>Co się dzieje

Wzorzec ten wykrywalny wydajności wskazuje warunek, w którym zmiany w konfiguracji o zakresie bazy danych powoduje, że regresji wydajności, który został wykryty w porównaniu do poprzednich zachowania obciążeń 7 dniowy bazy danych. Tego wzorca oznacza, że ostatnie zmiany wprowadzone w zakresie bazy danych konfiguracji wydają się być korzystne wydajność bazy danych.

Zmiany w zakresie bazy danych konfiguracji można ustawić dla każdej pojedynczej bazy danych. Ta konfiguracja jest używana w przypadku, aby zoptymalizować wydajność poszczególne bazy danych. Dla każdej pojedynczej bazy danych można skonfigurować następujące opcje: MAXDOP, LEGACY_CARDINALITY_ESTIMATION, PARAMETER_SNIFFING, QUERY_OPTIMIZER_HOTFIXES i wyczyść PROCEDURE_CACHE.

### <a name="troubleshooting"></a>Rozwiązywanie problemów

Diagnostyka dziennika danych wyjściowych o zakresie bazy danych zmian konfiguracji, które wprowadzono niedawno powodujące obniżenie wydajności w porównaniu do poprzedniego zachowania obciążeń siedmiu dni. Możesz przywrócić zmiany konfiguracji do poprzedniej wartości. Możesz również określić wartości przez wartość aż do osiągnięcia poziomu żądaną wydajność. Możesz skopiować wartości konfiguracji w zakresie bazy danych, z podobnych bazy danych za pomocą zadowalającą wydajność. Jeśli nie możesz rozwiązywać problemy z wydajnością, Przywróć domyślne wartości domyślne bazy danych SQL, a następnie spróbuj dostrajania, począwszy od tej linii bazowej.

Aby uzyskać więcej informacji na temat optymalizowania o zakresie bazy danych konfiguracji i składni języka T-SQL na zmiany konfiguracji, zobacz [Alter o zakresie bazy danych konfiguracji (Transact-SQL)](https://msdn.microsoft.com/library/mt629158.aspx).

## <a name="slow-client"></a>Powolne klienta

### <a name="what-is-happening"></a>Co się dzieje

Wzorzec ten wykrywalny wydajności wskazuje warunek, w którym klienta przy użyciu bazy danych SQL nie mogą korzystać z danych wyjściowych z bazy danych, tak szybko, jak baza danych wysyła wyniki. Ponieważ bazy danych SQL nie zapisuje wyniki wykonywanych zapytań w buforze, spowalnia i czeka, aż klient do korzystania z danych wyjściowych przesyłane zapytanie przed kontynuowaniem. Ten warunek może być również związany z siecią, która nie jest wystarczająco wystarczająco szybko przesyłać dane wyjściowe z bazy danych SQL klientowi korzystanie.

Ten stan jest generowany tylko wtedy, gdy wykryto regresji wydajności w porównaniu do poprzednich zachowania obciążeń 7 dniowy bazy danych. Ten problem z wydajnością jest wykrył, że tylko wtedy, gdy występuje statystycznie znacznego obniżenia wydajności w porównaniu do poprzedniego zachowania wydajności.

### <a name="troubleshooting"></a>Rozwiązywanie problemów

Wzorzec ten wykrywalny wydajności wskazuje na stan po stronie klienta. Rozwiązywanie problemów z jest wymagany w aplikacji po stronie klienta lub sieci po stronie klienta. Dziennik diagnostyczny generuje skróty zapytań i czasy oczekiwania, które wydaje się, że oczekuje większość dla klienta w celu korzystania z nich w ciągu ostatnich dwóch godzin. Te informacje można użyć jako podstawy do rozwiązywania problemów.

Można zoptymalizować wydajność aplikacji do użycia tych zapytań. Należy również rozważyć problemów z opóźnieniem sieci. Ponieważ problem spadku wydajności była oparta na zmiany w ostatni punkt odniesienia wydajności siedmiu dni, można zbadać, czy ostatnie zmiany stanu aplikacji lub sieci przyczyną tego zdarzenia dotyczącego wydajności regresji. 

## <a name="pricing-tier-downgrade"></a>Obniżenie warstwy cenowej

### <a name="what-is-happening"></a>Co się dzieje

Wzorzec ten wykrywalny wydajności wskazuje warunek, w którym został obniżony warstwy cenowej subskrypcji bazy danych SQL. Ze względu na zmniejszenie zasobów (Dtu) dostępnych w bazie danych system wykrył zrzutu bieżącej bazy danych wydajność w porównaniu do poprzednich 7 dniowy linii bazowej.

Ponadto może być warunek, w której zmienić na starszą wersję i następnie przeprowadzono uaktualnienie do wyższej warstwy w krótkim okresie czasu, przez warstwę cenową subskrypcji bazy danych SQL. Wykrywanie ten spadek wydajności tymczasowy jest wyświetlona w sekcji szczegółów dziennik diagnostyczny jako obniżenie warstwy cenowej i uaktualniania.

### <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli zmniejszyć warstwę cenową, a tym samym dostępności dla bazy danych SQL jednostek Dtu i jesteś zadowolony z wydajności, nie ma nic, które należy wykonać. Jeśli zmniejszyć warstwy cenowej i jesteś niezadowolony z wydajność bazy danych SQL, zmniejszenia obciążenia bazy danych lub Rozważ zwiększenie warstwę cenową na wyższym poziomie.

## <a name="recommended-troubleshooting-flow"></a>Zaleca się Rozwiązywanie problemów z przepływem

 Postępuj zgodnie z schematu blokowego, aby uzyskać zalecane podejście do rozwiązywania problemów z wydajnością za pomocą Intelligent Insights.

Dostęp Intelligent Insights w witrynie Azure portal, przechodząc do usługi Azure SQL Analytics. Próba Znajdź przychodzących alertów wydajności i zaznacz je. Określ, co dzieje się na stronie wykrywania. Sprawdź podane analiza głównych przyczyn problemu, tekst zapytania, trendów czasu kwerendy i rozwój zdarzenia. Podjęto próbę, aby rozwiązać ten problem za pomocą zalecenie Intelligent Insights łagodzenia problem z wydajnością. 

[![Schemat blokowy rozwiązywania problemów](./media/sql-database-intelligent-insights/intelligent-insights-troubleshooting-flowchart.png)](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/intelligent-insight/Troubleshoot%20Azure%20SQL%20Database%20performance%20issues%20using%20Intelligent%20Insight.pdf)

> [!TIP]
> Wybierz schemat blokowy, aby pobrać wersję PDF.

Intelligent Insights zwykle wymaga jednej godziny moment na wykonanie analizy głównych przyczyn problemu z wydajnością. Jeśli nie możesz znaleźć swój problem w usłudze Intelligent Insights, koniecznie do Ciebie umożliwia Store zapytania ręcznie odkryć ich główną przyczynę problemu z wydajnością. (Zazwyczaj te problemy są mniej niż o jedną godzinę). Aby uzyskać więcej informacji, zobacz [monitorowanie wydajności za pomocą Store zapytania](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store).

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się, [Intelligent Insights](sql-database-intelligent-insights.md) pojęcia.
- Użyj [dziennik diagnostyczny wydajności Intelligent Insights usługi Azure SQL Database](sql-database-intelligent-insights-use-diagnostics-log.md).
- Monitor [usługi Azure SQL Database przy użyciu usługi Azure SQL Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-sql).
- Dowiedz się, jak [zbieranie i używanie dane dzienników z zasobów platformy Azure](../azure-monitor/platform/diagnostic-logs-overview.md).
