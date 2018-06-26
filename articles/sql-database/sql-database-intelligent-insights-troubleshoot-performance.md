---
title: Baza danych SQL Azure Rozwiązywanie problemów z wydajnością z informacjami dotyczącymi inteligentnego | Dokumentacja firmy Microsoft
description: Inteligentnego szczegółowe informacje pomocy w rozwiązywaniu problemów z wydajnością bazy danych SQL Azure.
services: sql-database
author: danimir
manager: craigg
ms.reviewer: carlrab
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: conceptual
ms.date: 04/04/2018
ms.author: v-daljep
ms.openlocfilehash: bcc33eb7e5050c991c89b7f0998eec3707f62ebb
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2018
ms.locfileid: "36751347"
---
# <a name="troubleshoot-azure-sql-database-performance-issues-with-intelligent-insights"></a>Baza danych SQL Azure Rozwiązywanie problemów z wydajnością z informacjami dotyczącymi inteligentnego

Ta strona zawiera informacje dotyczące problemów z wydajnością bazy danych SQL Azure wykrywane przez [Insights inteligentnego](sql-database-intelligent-insights.md) dziennika diagnostycznego wydajności bazy danych. Ten dziennik diagnostyki mogą być wysyłane do [Azure Log Analytics](../log-analytics/log-analytics-azure-sql.md), [Azure Event Hubs](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md), [usługi Azure Storage](sql-database-metrics-diag-logging.md#stream-into-storage), lub rozwiązania innych firm dla niestandardowych DevOps funkcje alertów i raportów możliwości.

> [!NOTE]
> Aby baza danych SQL wydajności rozwiązywania problemów z przewodnika po za pośrednictwem inteligentnego szczegółowe informacje, zobacz [zalecane Rozwiązywanie problemów z przepływem](sql-database-intelligent-insights-troubleshoot-performance.md#recommended-troubleshooting-flow) schemat blokowy w tym dokumencie.
>

## <a name="detectable-database-performance-patterns"></a>Wzorce wydajności wykrywalny bazy danych

Inteligentnego Insights automatycznie wykrywa problemy z wydajnością z bazą danych SQL na podstawie czasy oczekiwania na wykonanie kwerendy, błędy lub przekroczeń limitu czasu. Następnie danych wyjściowych wydajności wykrytych wzorców w dzienniku diagnostyki. Wzorce wydajności wykrywalny podsumowano w poniższej tabeli:

| Wzorce wykrywalny wydajności | Szczegóły wyjściowych |
| :------------------- | ------------------- |
| [Osiągnięcia limitów zasobów](sql-database-intelligent-insights-troubleshoot-performance.md#reaching-resource-limits) | Zużycie dostępnych zasobów (Dtu), wątków roboczych bazy danych lub sesje logowania bazy danych na monitorowanym subskrypcji Osiągnięto limity, co powoduje, że problemy z wydajnością bazy danych SQL. |
| [Zwiększenie obciążenia](sql-database-intelligent-insights-troubleshoot-performance.md#workload-increase) | Zwiększenie obciążenia lub ciągłe akumulacji obciążenie bazy danych wykryto, co powoduje, że problemy z wydajnością bazy danych SQL. |
| [Wykorzystanie pamięci](sql-database-intelligent-insights-troubleshoot-performance.md#memory-pressure) | Pracownicy, którzy zażądali przyznaje pamięci musi czekać do przydzielania pamięci statystycznie znacznej ilości czasu. Lub istnieje zwiększona akumulacji pracowników, którzy zażądali przyznaje pamięci, co ma wpływ na wydajność bazy danych SQL. |
| [Blokowanie](sql-database-intelligent-insights-troubleshoot-performance.md#locking) | Blokowanie nadmiernego bazy danych został wykryty, co ma wpływ na wydajność bazy danych SQL. |
| [Zwiększona MAXDOP](sql-database-intelligent-insights-troubleshoot-performance.md#increased-maxdop) | Maksymalny stopień równoległości opcji (MAXDOP) została zmieniona, a ma to wpływ na wydajność wykonywania zapytań. |
| [Pagelatch Contention](sql-database-intelligent-insights-troubleshoot-performance.md#pagelatch-contention) | Wykryto rywalizacji Pagelatch, co ma wpływ na wydajność bazy danych SQL. Wiele wątków jednocześnie próba uzyskania dostępu tej samej strony buforu danych w pamięci. Powoduje to czasy oczekiwania zwiększona, co ma wpływ na wydajność bazy danych SQL. |
| [Brak indeksu](sql-database-intelligent-insights-troubleshoot-performance.md#missing-index) | Brak indeksu wykrycia problemu, co ma wpływ na wydajność bazy danych SQL. |
| [Nowe zapytanie](sql-database-intelligent-insights-troubleshoot-performance.md#new-query) | Wykryto nowe zapytanie, co ma wpływ na ogólną wydajność bazy danych SQL. |
| [Statystyka nietypowe zachowanie podczas oczekiwania](sql-database-intelligent-insights-troubleshoot-performance.md#unusual-wait-statistic) | Czasy oczekiwania nietypowe bazy danych zostały wykryte, co ma wpływ na wydajność bazy danych SQL. |
| [Bazy danych TempDB rywalizacji](sql-database-intelligent-insights-troubleshoot-performance.md#tempdb-contention) | Wiele wątków próbuje uzyskać dostępu do tych samych zasobów bazy danych tempDB, co powoduje, że "wąskie gardło", który ma wpływ na wydajność bazy danych SQL. |
| [Brak jednostek DTU puli elastycznej](sql-database-intelligent-insights-troubleshoot-performance.md#elastic-pool-dtu-shortage) | Braku dostępnych jednostek Edtu w puli elastycznej wpływa na wydajność bazy danych SQL. |
| [Planowanie regresji](sql-database-intelligent-insights-troubleshoot-performance.md#plan-regression) | Opcje nowego planu lub zmiana obciążenie istniejący plan został wykryty, co ma wpływ na wydajność bazy danych SQL. |
| [Zmiana wartości o zakresie bazy danych konfiguracji](sql-database-intelligent-insights-troubleshoot-performance.md#database-scoped-configuration-value-change) | Zmiana konfiguracji w bazie danych wpływa na wydajność bazy danych SQL. |
| [Powolna klienta](sql-database-intelligent-insights-troubleshoot-performance.md#slow-client) | Powolne aplikacji klienta, który nie może korzystać z danych wyjściowych z bazy danych SQL wystarczająca został wykryty, co ma wpływ na wydajność bazy danych SQL. |
| [Obniżenie warstwy cenowej](sql-database-intelligent-insights-troubleshoot-performance.md#pricing-tier-downgrade) | Akcję obniżenia poziomu warstwy cenowej zmniejszyć dostępnych zasobów, co ma wpływ na wydajność bazy danych SQL. |

> [!TIP]
> Aby zoptymalizować wydajność ciągłego bazy danych SQL, należy włączyć [automatycznego dostrajania bazy danych SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-database-automatic-tuning). To rozwiązanie wbudowane narzędzie analizy bazy danych SQL stale monitoruje bazę danych SQL, automatycznie Dostraja indeksy i stosuje poprawki planu wykonania zapytania.
>

W poniższej sekcji opisano wzorce wymienione wcześniej wydajności wykrywalny bardziej szczegółowo.

## <a name="reaching-resource-limits"></a>Osiągnięcia limitów zasobów

### <a name="what-is-happening"></a>Co się dzieje

Ten wzorzec wykrywalny wydajności łączy problemy z wydajnością, które odnoszą się do osiągnięcia limitów dostępnego zasobu, proces roboczy ograniczenia i limity sesji. Po wykryciu ten problem z wydajnością pola Opis dziennika diagnostyki wskazuje, czy problem z wydajnością powiązany zasobu, proces roboczy lub limity sesji.

Zasoby w bazie danych SQL są zwykle nazywane [zasobów jednostek DTU](https://docs.microsoft.com/azure/sql-database/sql-database-what-is-a-dtu). Składają się z kombinacji miar: procesora CPU i we/wy zasobów (danych i dziennika transakcji we/wy). Wzorzec osiągnięcia limitów zasobów jest rozpoznawany w przypadku wykrycia obniżenia wydajności kwerendy jest spowodowany dotarcie do żadnego z limitami zasobów zmierzona.

Zasób limity sesji oznacza liczbę dostępnych równoczesnych logowania do bazy danych SQL. Ten wzorzec wydajności została rozpoznana, gdy aplikacje, które są podłączone do bazy danych SQL maksymalną liczbę dostępnych równoczesnych logowania do bazy danych. Jeśli aplikacje próbują używać sesji więcej niż jest dostępne w bazie danych, jest wpływ na wydajność kwerend.

Osiągnięcia limitów proces roboczy jest konkretnym przypadku osiągnięcia limitów zasobu, ponieważ pracowników dostępne nie są zliczane w użycie jednostek DTU. Osiągnięcia limitów procesu roboczego w bazie danych może spowodować wzrost czasy oczekiwania określonych zasobów, co powoduje spadek wydajności zapytania.

### <a name="troubleshooting"></a>Rozwiązywanie problemów

Dziennik diagnostyki generuje skróty zapytania zapytań, na które miała wpływ na wydajność i zużycie zasobów w wartości procentowe. Te informacje jako punkt początkowy służy do optymalizacji obciążenie bazy danych. W szczególności może zoptymalizować zapytania, które mają wpływ na spadek wydajności, dodając indeksów. Lub zoptymalizować aplikacji za pomocą więcej nawet obciążenia dystrybucji. Jeśli nie można zmniejszyć obciążenie pracą lub wprowadzić optymalizacji, należy rozważyć zwiększenie warstwy cenowej subskrypcji bazy danych SQL, aby zwiększyć liczbę dostępnych zasobów.

Czy osiągnięto limity dostępne sesji, aby zoptymalizować aplikacji dzięki zmniejszeniu liczby logowania wprowadzone w bazie danych. Jeśli nie możesz zmniejszyć liczbę logowania z aplikacji do bazy danych, należy rozważyć zwiększenie warstwy cenowej bazy danych. Lub możesz podzielić i przenieść bazę danych do wielu baz danych dla bardziej zrównoważony rozkład obciążenia.

Aby uzyskać więcej sugestie dotyczące rozwiązywania ograniczeń sesji, zobacz [sposób postępowania z wartościami granicznymi maksymalną logowania do bazy danych SQL](https://blogs.technet.microsoft.com/latam/2015/06/01/how-to-deal-with-the-limits-of-azure-sql-database-maximum-logins/). Aby dowiedzieć się, limity zasobu dostępności dla warstwy Twojej subskrypcji, zobacz [limity zasobów bazy danych SQL](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits).

## <a name="workload-increase"></a>Zwiększenie obciążenia

### <a name="what-is-happening"></a>Co się dzieje

Ten wzorzec wydajności identyfikuje problemy spowodowane przez zwiększenie obciążenia, lub w postaci poważniejsze pile-up obciążenia.

To wykrywanie następuje przy użyciu kombinacji kilka miar. Podstawowe Metryka mierzy wykrywa zwiększenie obciążenia pracą w porównaniu z ostatnich linii bazowej obciążenia. Inne formy wykrywania jest oparta na mierzeniu duży wzrost aktywnych wątków roboczych duży wpływ na wydajność.

W postaci poważniejsze obciążenie może stale Ustawianie z powodu braku możliwości bazy danych SQL do obsługi obciążenia. Wynik jest stale rosnącym rozmiar obciążenia, który jest warunkiem pile-up obciążenia. Z powodu tego stanu rozwoju czas Obciążenie oczekuje na wykonanie. Ta sytuacja reprezentuje jedną z najbardziej poważne problemy z wydajnością bazy danych. Ten problem zostanie wykryty przez monitorowanie wzrost liczbę wątków roboczych zostało przerwane. 

### <a name="troubleshooting"></a>Rozwiązywanie problemów

Dziennik diagnostyki generuje liczba zapytań, których wykonanie wzrosło i skrót zapytania zapytanie o największy wkład wzrostu obciążenia. Te informacje jako punkt początkowy służy do optymalizacji obciążenie. Zapytanie zidentyfikowane jako współautor największy zwiększenie obciążenia jest szczególnie przydatna jako punktu początkowego.

Należy rozważyć dystrybucji obciążeń bardziej równomiernie w bazie danych. Należy rozważyć optymalizacji zapytania, które ma wpływ na wydajność, dodając indeksów. Możesz również może dystrybuować obciążenia między wieloma bazami danych. Jeśli te rozwiązania nie jest możliwe, należy rozważyć zwiększenie warstwy cenowej subskrypcji bazy danych SQL, aby zwiększyć liczbę dostępnych zasobów.

## <a name="memory-pressure"></a>Wykorzystanie pamięci

### <a name="what-is-happening"></a>Co się dzieje

Ten wzorzec wydajności wskazuje spadek wydajności bieżącego wydajność bazy danych to spowodowane wykorzystania pamięci lub w postaci poważniejsze warunek pile-up pamięci w porównaniu do poprzednich linii bazowej wydajności siedmiu dni.

Wykorzystania pamięci określa warunek wydajności, w którym jest dużą liczbę wątków roboczych żądania pamięci przyznaje w bazie danych SQL. Dużych obciążeń powoduje, że stan użycia pamięci wysokiej, w którym baza danych SQL nie jest w stanie wydajnie przydzielić pamięci do wszystkich pracowników, które żądania. Najbardziej typowe przyczyny tego problemu jest powiązany z ilość dostępnej pamięci, aby baza danych SQL z jednej strony. Z drugiej strony wzrost obciążenia powoduje zwiększenie wątków roboczych i wykorzystania pamięci.

Formularz poważniejsze wykorzystania pamięci jest warunek pile-up pamięci. Ten stan wskazuje większej liczby wątków roboczych żądają przyznaje pamięci niż zapytań, zwalniając pamięć. To liczba wątków roboczych, które żądania pamięci daje również może być stale zwiększenie (gromadzeniu się), ponieważ aparat bazy danych SQL nie może przydzielić pamięci wystarczająco wydajnie, aby sprostać zapotrzebowaniu. Warunek pile-up pamięci przedstawia jedną z najbardziej poważne problemy z wydajnością bazy danych.

### <a name="troubleshooting"></a>Rozwiązywanie problemów

Dziennik diagnostyki generuje szczegółów pamięci obiektu magazynu z elementu clerk (to znaczy wątku roboczego) oznaczona jako najwyższą przyczynę wysokie użycie pamięci i sygnatury czasowe istotne. Te informacje można użyć jako podstawy do rozwiązywania problemów. 

Można zoptymalizować lub usunąć zapytań dotyczących urzędników o najwyższym użycia pamięci. Możesz również można upewnij się, że nie są badania dane, które nie będą używane. Dobrym rozwiązaniem jest zawsze używać klauzuli WHERE zapytania. Ponadto zaleca się utworzenie nieklastrowanych indeksów do wyszukiwania danych, a nie skanowania.

Można również zmniejszyć obciążenie optymalizacji lub rozpowszechniania przez wiele baz danych. Lub może rozkładanie obciążenia między wieloma bazami danych. Jeśli te rozwiązania nie jest możliwe, należy rozważyć zwiększenie warstwy cenowej subskrypcji bazy danych SQL, aby zwiększyć ilość zasobów pamięci dostępne w bazie danych.

Aby uzyskać dodatkowe porady dotyczące rozwiązywania problemów, zobacz [pamięci przyznaje skalnym: brzmieć tajemniczo konsumenta pamięć programu SQL Server z wieloma nazwami](https://blogs.msdn.microsoft.com/sqlmeditation/2013/01/01/memory-meditation-the-mysterious-sql-server-memory-consumer-with-many-names/).

## <a name="locking"></a>Blokowanie

### <a name="what-is-happening"></a>Co się dzieje

Ten wzorzec wydajności wskazuje degradacji w bieżącym wydajność bazy danych, w którym blokowania nadmiernego bazy danych zostanie wykryte w porównaniu do poprzednich linii bazowej wydajności siedmiu dni. 

W nowoczesnych RDBMS blokowanie jest istotne dla wdrożenia wielowątkowe systemy, w których wydajności jest zmaksymalizowane uruchamiając wiele równoczesnych pracowników i transakcje równoległe bazy danych, jeśli jest to możliwe. Blokowanie w tym kontekście odnosi się do mechanizmu wbudowanych dostępu, w którym jednej transakcji można wyłącznie dostęp do wierszy, strony, tabel i plików, które są wymagane i nie powodowały konfliktu z inną transakcję dla zasobów. Po zakończeniu transakcji, który zablokowany zasobów przeznaczonych do użycia z nimi jest zwalniany blokady na te zasoby, dzięki czemu inne transakcje dostępu do wymaganych zasobów. Aby uzyskać więcej informacji dotyczących blokowania, zobacz [zablokować aparatu bazy danych](https://msdn.microsoft.com/library/ms190615.aspx).

Jeśli transakcje wykonywane przez aparat SQL oczekują na dłuższe okresy dostępu do zasobów zablokowany do użytku, czas oczekiwania powoduje spowolnienie wydajności wykonywania obciążenia. 

### <a name="troubleshooting"></a>Rozwiązywanie problemów

Dziennik diagnostyki generuje blokowania szczegóły, które służy jako podstawa dla rozwiązywania problemów. Można analizować zgłoszone blokowania zapytania, oznacza to, że zapytania wprowadzające blokowania spadek wydajności, i usuń je. W niektórych przypadkach może się niepowodzeniem z optymalizacją blokowania zapytania.

Najprostsza i najbezpieczniejszy sposób ograniczyć problem jest zachować krótki transakcji i ograniczyć wpływ blokady najdroższych zapytań. Można podzielić dużych partii operacje na mniejsze operacje. Dobrym rozwiązaniem jest, aby ograniczyć wpływ blokady kwerendy poprzez jak najbardziej efektywne zapytania. Zmniejszenia dużych skanowania, ponieważ zwiększyć ryzyko zakleszczenia i negatywnie wpłynąć na ogólną wydajność bazy danych. Dla określonych zapytań, które powodują blokowanie można utworzyć nowe indeksy lub dodać kolumny do istniejącego indeksu, aby uniknąć skanowania tabeli. 

Aby uzyskać więcej wskazówek, zobacz [sposobu rozwiązywania problemów z blokowaniem spowodowanych przez eskalacji blokady w programie SQL Server](https://support.microsoft.com/help/323630/how-to-resolve-blocking-problems-that-are-caused-by-lock-escalation-in).

## <a name="increased-maxdop"></a>Zwiększona MAXDOP

### <a name="what-is-happening"></a>Co się dzieje

Ten wzorzec wykrywalny wydajności wskazuje, że został planu wykonania zapytania wybrany warunek zarządzana z przetwarzaniem więcej niż powinien być. Optymalizator zapytań bazy danych SQL można zwiększyć wydajność przetwarzania obciążenia, wykonując zapytania równolegle w celu przyspieszenia rzeczy, jeśli jest to możliwe. W niektórych przypadkach pracowników równoległego przetwarzania zapytania poświęcić więcej czasu na oczekiwanie na inne do synchronizacji i wyniki w porównaniu do wykonywania tego samego zapytania z mniejszą liczbę pracowników równoległe lub nawet w niektórych przypadkach, w porównaniu do pojedynczego wątku scalania.

System ekspert analizuje bieżące wydajność bazy danych w porównaniu do okresu linii bazowej. Określa, czy wcześniej uruchomione zapytania działa wolniej niż przed ponieważ plan wykonania zapytania jest zarządzana przetwarzaniem więcej niż powinien być.

Opcji konfiguracji serwera MAXDOP w bazie danych SQL służy do kontrolowania liczby rdzeni procesora CPU może służyć do wykonania tego samego zapytania równolegle. 

### <a name="troubleshooting"></a>Rozwiązywanie problemów

Dziennik diagnostyki generuje skróty zapytania dotyczące zapytań, dla których czas realizacji podwyższony, ponieważ zostały one zarządzana przetwarzaniem więcej niż powinien być. Dziennik generuje również CXP czasy oczekiwania. Teraz reprezentuje czas oczekiwania dla wszystkich innych wątków zakończyć działanie przed scaleniem wyniki i realizowanie wątku pojedynczego organizatora/koordynatora (wątek 0). Ponadto dziennika diagnostyki danych wyjściowych wykonywania słaby zapytań oczekujących w ogólnej wykonywania czasy oczekiwania. Te informacje można użyć jako podstawy do rozwiązywania problemów.

Po pierwsze Optymalizacja lub Uprość złożonych zapytań. Dobrym rozwiązaniem jest podzielić długie partii zadań na mniejsze. Ponadto upewnij się, że utworzono indeksów do obsługi zapytań. Można też ręcznie wymusić maksymalny stopień równoległości (MAXDOP) dla zapytania, który został oznaczony jako niska wykonywania. Aby skonfigurować tę operację za pomocą T-SQL, zobacz [Konfigurowanie opcji konfiguracji serwera MAXDOP](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option).

Ustawienie serwera MAXDOP opcji konfiguracji na zero (0), jako wartość domyślną oznacza bazy danych SQL za pomocą wszystkie dostępne rdzenie Procesora logicznego parallelize wątków wykonywania pojedynczego zapytania. Ustawienie MAXDOP do jednego (1) oznacza, że tylko jeden rdzeń może służyć do wykonywania pojedynczego zapytania. W praktyce oznacza to, że równoległości jest wyłączona. W zależności od przypadku na podstawie dostępne rdzenie do bazy danych i informacji diagnostycznych rejestrowania informacji, można dostosować w opcji MAXDOP liczby rdzeni używany do wykonywania zapytania równoległe, która może rozwiązać problem w Twoim przypadku.

## <a name="pagelatch-contention"></a>Pagelatch rywalizacji

### <a name="what-is-happening"></a>Co się dzieje

Ten wzorzec wydajności wskazuje bieżący spadek wydajności obciążenie bazy danych z powodu niezgodności pagelatch w porównaniu do linii bazowej ostatnich siedmiu dni obciążenia.

Zamków to mechanizmy lekkie synchronizacji używane przez bazy danych SQL, aby włączyć wielowątkowości. One zagwarantować spójności struktur w pamięci, które obejmują indeksów, strony danych i innych struktur wewnętrznych.

Istnieje wiele typów zamków dostępne w bazie danych SQL. Dla uproszczenia zamków buforu służą do ochrony stron w pamięci w puli bufora. We/Wy zamków służą do ochrony nie została jeszcze załadowana do puli buforów stron. Zawsze, gdy dane są zapisywania lub odczytywania ze strony w puli buforów, musi najpierw uzyskać zatrzaśnięcie buforu, na stronie wątku roboczego. Zawsze, gdy wątek roboczy próbuje uzyskać dostęp do strony, która nie jest już dostępny w puli buforów w pamięci, we/wy żądań załadować wymaganych informacji z magazynu. Ta sekwencja zdarzeń wskazuje poważniejsze formę spadku wydajności.

Rywalizacji zamków strony występuje, gdy wiele wątków jednocześnie prób uzyskania zamków na tej samej struktury w pamięci, która przedstawia czas oczekiwania zwiększona do wykonywania zapytań. W przypadku niezgodności we/wy pagelatch gdy dane trzeba można uzyskać dostępu do magazynu i czas oczekiwania jest jeszcze większym. To może wpłynąć na wydajność obciążenia znacznie. Rywalizacji Pagelatch jest najbardziej typowym scenariuszem wątków oczekujących na każdym z nich i konkurowanie o zasoby w wielu systemach procesora CPU.

### <a name="troubleshooting"></a>Rozwiązywanie problemów

Dziennik diagnostyki generuje pagelatch szczegóły rywalizacji. Te informacje można użyć jako podstawy do rozwiązywania problemów.

Ponieważ pagelatch mechanizmu kontroli wewnętrznej bazy danych SQL, automatycznie określają, kiedy należy używać ich. Decyzji aplikacji, w tym projektowanie schematu może wpłynąć na zachowanie pagelatch ze względu na zachowanie deterministyczne zamków.

Jedną z metod obsługi rywalizacji zatrzaśnięcia jest Zastąp klucz indeksu sekwencyjnych niesekwencyjną sieć kluczem równomierne rozłożenie wstawia między zakresem. Zazwyczaj wiodących kolumn w indeksie dystrybuuje obciążenie proporcjonalnie. Inną metodą należy wziąć pod uwagę jest partycjonowania tabeli. Tworzenie skrót schemat z kolumną obliczaną w tabeli partycjonowanej partycji jest typowym podejściem łagodzenia nadmiernego zatrzaśnięcia rywalizacji. W przypadku niezgodności we/wy pagelatch wprowadzenie indeksów pomaga ograniczyć ten problem z wydajnością. 

Aby uzyskać więcej informacji, zobacz [Diagnozuj i rozwiąż zatrzaśnięć rywalizacji w programie SQL Server](http://download.microsoft.com/download/B/9/E/B9EDF2CD-1DBF-4954-B81E-82522880A2DC/SQLServerLatchContention.pdf) (do pobrania plików PDF).

## <a name="missing-index"></a>Brak indeksu

### <a name="what-is-happening"></a>Co się dzieje

Ten wzorzec wydajności wskazuje spadek wydajności bieżące obciążenie bazy danych w porównaniu do linii bazowej ostatnich siedmiu dni ze względu na Brak indeksu.

Indeks jest używany w celu przyspieszenia wydajność kwerend. Dzięki zmniejszeniu liczby stron zestawu danych, które należy odwiedzi lub skanowania zapewnia szybki dostęp do danych z tabeli.

Określonego zapytania, które spowodować spadek wydajności są identyfikowane za pomocą tego wykrycia, dla której będzie korzystne na wydajność tworzenia indeksów.

### <a name="troubleshooting"></a>Rozwiązywanie problemów

Dziennik diagnostyki generuje skróty zapytania dla zapytania, które zostały zidentyfikowane wpłynąć na wydajność obciążenia. Umożliwia tworzenie indeksów dla tych zapytań. Możesz również zoptymalizować lub usunąć te zapytania, jeśli nie są wymagane. Jest rozwiązaniem dobrą wydajność w celu uniknięcia wykonywanie zapytania na danych, które nie są używane.

> [!TIP]
> Czy wiesz, że wbudowane narzędzie analizy bazy danych SQL można automatycznie zarządzać najlepiej wykonywania indeksów dla bazy danych?
>
> Aby zoptymalizować wydajność ciągłego bazy danych SQL, zaleca się włączenie [automatycznego dostrajania bazy danych SQL](sql-database-automatic-tuning.md). To rozwiązanie wbudowane narzędzie analizy bazy danych SQL stale monitoruje bazę danych SQL i automatycznie Dostraja i tworzy indeksów dla bazy danych.
>

## <a name="new-query"></a>Nowe zapytanie

### <a name="what-is-happening"></a>Co się dzieje

Ten wzorzec wydajności wskazuje, że nowe zapytanie wykryto jest najlepiej i wpływu na wydajność obciążenia w porównaniu do linii bazowej wydajności siedmiu dni.

Zapisywanie zapytania wykonywania dobrej czasami może być trudne zadaniem. Aby uzyskać więcej informacji o pisaniu zapytań, zobacz [zapytania SQL zapisywania](https://msdn.microsoft.com/library/bb264565.aspx). Aby zoptymalizować istniejących wydajność zapytań, zobacz [dostrajanie zapytania](https://msdn.microsoft.com/library/ms176005.aspx).

### <a name="troubleshooting"></a>Rozwiązywanie problemów

Diagnostyki rejestrowania danych wyjściowych informacji maksymalnie dwa nowe większości używające Procesora zapytania, łącznie z ich skróty zapytania. Ponieważ wykryto zapytania ma wpływ na wydajność przetwarzania obciążenia, aby zoptymalizować zapytania. Dobrym rozwiązaniem jest pobierać tylko te dane, które należy użyć. Zalecamy również za pomocą zapytań z klauzuli WHERE. Zalecamy również uprościć złożonych zapytań, a następnie podzielić na mniejsze zapytania. Innym dobrym rozwiązaniem jest podział duży wsadu zapytania na mniejsze zapytania partii. Wprowadzenie do indeksów dla nowego zapytania jest zwykle dobrym rozwiązaniem w celu ograniczenia tego problemu z wydajnością.

Należy rozważyć użycie [szczegółowe informacje o usłudze Azure SQL bazy danych zapytań wydajności](sql-database-query-performance.md).

## <a name="unusual-wait-statistic"></a>Statystyka nietypowe zachowanie podczas oczekiwania

### <a name="what-is-happening"></a>Co się dzieje

Ten wzorzec wykrywalny wydajności wskazuje obciążenia spadek wydajności, w którym wykonywanie słaby zapytania są identyfikowane w porównaniu do linii bazowej ostatnich siedmiu dni obciążenia.

W takim przypadku system nie można sklasyfikować wykonywania słaby zapytań w innych kategorii wydajności wykrywalny standardowe, ale wykryto on odpowiedzialny za regresji Statystyka oczekiwania. Dlatego traktuje je jako zapytania z *statystyki nietypowe zachowanie podczas oczekiwania*, gdzie uwidocznioną również odpowiedzialne za regresji Statystyka nietypowe zachowanie podczas oczekiwania. 

### <a name="troubleshooting"></a>Rozwiązywanie problemów

Dziennik diagnostyki danych wyjściowych informacji na temat szczegóły czasu oczekiwania nietypowe, skróty zapytania uwzględnione kwerendy i czasy oczekiwania.

Ponieważ system nie można pomyślnie zidentyfikować przyczynę główną dla zapytania wykonywania słaby, informacje diagnostyczne jest dobry punkt wyjścia do ręcznego rozwiązywania problemów. Aby zoptymalizować wydajność kwerend. Dobrym rozwiązaniem jest pobierane tylko dane potrzebne do użycia, a także uprościć i rozbić złożonych zapytań na mniejsze. 

Aby uzyskać więcej informacji o optymalizacji wydajności zapytań, zobacz [dostrajanie zapytania](https://msdn.microsoft.com/library/ms176005.aspx).

## <a name="tempdb-contention"></a>Bazy danych TempDB rywalizacji

### <a name="what-is-happening"></a>Co się dzieje

Ten wzorzec wykrywalny wydajności wskazuje warunek wydajności bazy danych, w którym występuje wąskie gardło wątków próby dostępu do zasobów w bazie danych tempDB. (Ten warunek nie jest związane z We/Wy). Typowy scenariusz ten problem z wydajnością jest setki zapytania jednoczesne, że wszystkie tworzenie, używanie i następnie porzucić tabel tempDB mała. System wykrył, że liczba równoczesnych zapytań przy użyciu tego samego tabele bazy danych tempDB zwiększone z wystarczającą obserwowane wpłynąć na wydajność bazy danych w porównaniu do poprzednich linii bazowej wydajności siedmiu dni.

### <a name="troubleshooting"></a>Rozwiązywanie problemów

Dziennik diagnostyki Wyświetla szczegóły rywalizacji bazy danych tempDB. Możesz skorzystać z informacji jako punktu wyjścia do rozwiązywania problemów. Istnieją dwie czynności można wykonywać rozwiązanie tego rodzaju rywalizacji i zwiększyć przepływność ogólną obciążenie: można wyłączyć przy użyciu tabel tymczasowych. Można też użyć tabel zoptymalizowanych pod kątem pamięci. 

Aby uzyskać więcej informacji, zobacz [wprowadzenie do tabel zoptymalizowanych pod kątem pamięci](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/introduction-to-memory-optimized-tables). 

## <a name="elastic-pool-dtu-shortage"></a>Brak jednostek DTU puli elastycznej

### <a name="what-is-happening"></a>Co się dzieje

Ten wzorzec wykrywalny wydajności wskazuje degradacji bieżącej bazy danych obciążenia wydajność w porównaniu do linii bazowej ostatnich siedmiu dni. Należy się z powodu braku dostępnych jednostek Dtu w puli elastycznej subskrypcji. 

Zasoby w bazie danych SQL są zwykle nazywane [zasobów jednostek DTU](sql-database-service-tiers.md#what-are-database-transaction-units-dtus), które składają się z kombinacji miar: procesora CPU i we/wy zasobów (danych i dziennika transakcji we/wy). [Zasoby platformy Azure puli elastycznej](sql-database-elastic-pool.md) są używane jako pula zasobów eDTU dostępnych współużytkowane przez wiele baz danych na potrzeby skalowania. Gdy zasobów eDTU dostępnych w puli elastycznej nie są wystarczająco duże, aby obsługiwać wszystkie bazy danych w puli, problem z wydajnością braku puli elastycznej jednostek dtu w warstwie została wykryta przez system.

### <a name="troubleshooting"></a>Rozwiązywanie problemów

Dziennika diagnostyki generuje informacje o puli elastycznej, wymieniono top baz danych korzystających z jednostek dtu w warstwie i zapewnia procent używanej przez bazę danych korzystających z góry DTU puli.

Ponieważ ten warunek wydajności nie jest powiązana z wielu baz danych przy użyciu tej samej puli, liczby jednostek Edtu w puli elastycznej, kroki rozwiązywania problemów skupić się na najwyższym baz danych korzystających z jednostek dtu w warstwie. Można zmniejszyć obciążenie bazy danych korzystających z góry, w tym optymalizacji kwerendy korzystających z góry dla tych baz danych. Można również zagwarantować, że nie są badania dane, które nie są używane. Innym rozwiązaniem jest Optymalizowanie aplikacji za pomocą górnej baz danych korzystających z jednostek dtu w warstwie i ponowne rozdzielanie obciążenia między wieloma bazami danych.

Jeśli redukcji i optymalizacji bieżące obciążenie na najwyższym baz danych korzystających z jednostek dtu w warstwie nie jest możliwe, należy rozważyć zwiększenie z puli elastycznej warstwy cenowej. Takie zwiększenie powoduje zwiększenie liczby dostępnych jednostek Dtu w puli elastycznej.

## <a name="plan-regression"></a>Planowanie regresji

### <a name="what-is-happening"></a>Co się dzieje

Ten wzorzec wykrywalny wydajności określa warunek, w którym korzysta z planu wykonania zapytania nieoptymalne bazy danych SQL. Nieoptymalne plan zwykle powoduje, że wykonywanie zapytania zwiększona, co prowadzi do już oczekiwania razy dla zapytań dotyczących bieżących i inne.

Baza danych SQL określa plan wykonania zapytania o najmniejszej koszt do wykonywania zapytań. Jako typ zmiany zapytania i obciążeń czasami istniejących planów nie są już wydajne lub być może baza danych SQL nie zostały wprowadzone dobrej oceny. Jako korekty można ręcznie wymusić planów wykonania zapytania.

Ten wzorzec wykrywalny wydajności łączy trzy uwzględniana regresji plan: nowego planu regresji, stare regresji planu i istniejących planów zmianie obciążenia. Określonego typu regresji planu, który wystąpił znajduje się w *szczegóły* właściwości dziennika diagnostyki.

Nowy warunek regresji planu odwołuje się do stanu, w którym baza danych SQL rozpoczyna wykonywanie nowy plan wykonania zapytania, który nie jest efektywne starego planu. Stary warunek planu w regresji odnosi się do stanu, gdy zmienia bazy danych SQL z za pomocą nowego, bardziej wydajne planu do starego planu, który nie jest tak wydajna, jak nowy plan. Istniejące regresji obciążenia planów zmienić odwołuje się do stanu, w której stary i nowy planów stale alternatywny, z saldo będzie bardziej kierunku planu wykonywania słaby.

Aby uzyskać więcej informacji na regresji planu, zobacz [co to jest plan regresji w programie SQL Server?](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2017/06/09/what-is-plan-regression-in-sql-server/). 

### <a name="troubleshooting"></a>Rozwiązywanie problemów

Dziennik diagnostyki generuje skróty zapytania, dobry plan ID, identyfikator planu zły i zapytania identyfikatorów. Te informacje można użyć jako podstawy do rozwiązywania problemów.

Można analizować, które plan jest lepszym rozwiązaniem, wykonywania zapytań określonych identyfikujące z skrótów zapytania, pod warunkiem. Po ustaleniu, które plan działa lepiej dla zapytań, możesz go ręcznie wymusić. 

Aby uzyskać więcej informacji, zobacz [Dowiedz się, jak program SQL Server zapobiega regresji planu](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2017/04/25/you-shall-not-regress-how-sql-server-2017-prevents-plan-regressions/).

> [!TIP]
> Czy wiesz, że wbudowane narzędzie analizy bazy danych SQL można automatycznie zarządzać najlepiej wykonywania planów wykonanie zapytania dla baz danych?
>
> Aby zoptymalizować wydajność ciągłego bazy danych SQL, zaleca się włączenie [automatycznego dostrajania bazy danych SQL](sql-database-automatic-tuning.md). To rozwiązanie wbudowane narzędzie analizy bazy danych SQL stale monitoruje bazę danych SQL i automatycznie Dostraja i tworzy najlepiej wykonywania kwerendy planów wykonania dla baz danych.
>

## <a name="database-scoped-configuration-value-change"></a>Zmiana wartości o zakresie bazy danych konfiguracji

### <a name="what-is-happening"></a>Co się dzieje

Ten wzorzec wykrywalny wydajności wskazuje stan, w którym zmiany w konfiguracji o zakresie bazy danych powoduje, że wydajności regresji, która została wykryta w porównaniu do poprzednich zachowania obciążeń siedmiu dni bazy danych. Ten wzorzec oznacza, że ostatnie zmiany wprowadzone w konfiguracji o zakresie bazy danych nie powiodła się optymalnym wydajność bazy danych.

Zmiany w konfiguracji o zakresie bazy danych można ustawić dla każdego poszczególne bazy danych. Ta konfiguracja jest używana w przypadku — w celu zoptymalizowania wydajności poszczególne bazy danych. Dla każdej poszczególne bazy danych można skonfigurować następujące opcje: MAXDOP, LEGACY_CARDINALITY_ESTIMATION PARAMETER_SNIFFING, QUERY_OPTIMIZER_HOTFIXES i wyczyść PROCEDURE_CACHE.

### <a name="troubleshooting"></a>Rozwiązywanie problemów

Dane wyjściowe o zakresie bazy danych zmian konfiguracji, które zostały niedawno wprowadzone powodujące spadek wydajności w porównaniu do poprzednich zachowania obciążeń siedmiu dni dziennika diagnostyki. Możesz przywrócić do poprzedniej wartości zmiany konfiguracji. Wartości przez wartość można również dostosować w aż do osiągnięcia poziomu żądaną wydajność. Możesz skopiować wartości z zakresu bazy danych konfiguracji z podobnych bazy danych z zadowalającą wydajność. Jeśli nie możesz się Rozwiązywanie problemów z wydajnością, Przywróć wartości domyślne domyślna baza danych SQL i próbę dopasowania, począwszy od tej linii bazowej.

Aby uzyskać więcej informacji na optymalizowanie o zakresie bazy danych konfiguracji i składnia T-SQL w zmiany konfiguracji, zobacz [Alter o zakresie bazy danych konfiguracji (Transact-SQL)](https://msdn.microsoft.com/library/mt629158.aspx).

## <a name="slow-client"></a>Powolna klienta

### <a name="what-is-happening"></a>Co się dzieje

Ten wzorzec wykrywalny wydajności wskazuje tak szybko, jak baza danych wysyła wyniki warunek, w którym klienta przy użyciu bazy danych SQL nie może korzystać z danych wyjściowych z bazy danych. Ponieważ baza danych SQL nie jest przechowywanie wyników zapytania wykonywane w buforze, spowalnia i czeka na klienta korzystać z zapytania przesyłane dane wyjściowe przed kontynuowaniem. Ten warunek może być również związany z siecią, która nie jest wystarczająco wystarczająca do przesyłania danych wyjściowych z bazy danych SQL klientowi korzystanie.

Ten stan jest generowany tylko wtedy, gdy wykryto regresji wydajność w porównaniu do poprzednich zachowania obciążeń siedmiu dni bazy danych. Ten problem z wydajnością jest wykrył, że tylko wtedy, gdy występuje statystycznie znacznego obniżenia wydajności w porównaniu do poprzednich zachowanie wydajności.

### <a name="troubleshooting"></a>Rozwiązywanie problemów

Ten wzorzec wykrywalny wydajności wskazuje stan po stronie klienta. Rozwiązywanie problemów z jest wymagany w aplikacji po stronie klienta lub w sieci po stronie klienta. Dziennik diagnostyki generuje skróty zapytania i czasy oczekiwania, które wydają się być Trwa oczekiwanie na większości dla klienta użycie ich w ciągu poprzednich dwóch godzin. Te informacje można użyć jako podstawy do rozwiązywania problemów.

Aby zoptymalizować wydajność aplikacji korzystania z tych kwerend. Należy również rozważyć problemów z opóźnieniem w sieci. Ponieważ problem spadek wydajności została oparta na zmiany w ostatniej linii bazowej wydajności siedmiu dni, można zbadać, czy ostatnie zmiany stanu aplikacji lub sieci przyczyną tego zdarzenia wydajności regresji. 

## <a name="pricing-tier-downgrade"></a>Obniżenie warstwy cenowej

### <a name="what-is-happening"></a>Co się dzieje

Ten wzorzec wykrywalny wydajności wskazuje stan, w którym został obniżony warstwy cenowej subskrypcji bazy danych SQL. Ze względu na ograniczenia zasobów (Dtu) dostępnych w bazie danych system wykrył spadek bieżącej bazy danych wydajność w porównaniu do linii bazowej ostatnich siedmiu dni.

Ponadto może być warunek, w której warstwie cenowej subskrypcji bazy danych SQL został obniżony a następnie uaktualnienia do wyższego poziomu w krótkim czasie. Wykrywanie to pogorszenie wydajności tymczasowego wyjściowych w sekcji szczegółów dziennika diagnostyki jako obniżenia poziomu warstwy cenowej i uaktualnienia.

### <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli zmniejszona warstwę cenową, a w związku z tym Dtu dostępne dla bazy danych SQL i zakończeniu wydajność, nie ma nic, które należy wykonać. Jeśli zmniejszona warstwę cenową i masz niezadowolony z wydajność bazy danych SQL, zmniejszyć obciążeń bazy danych lub należy rozważyć zwiększenie warstwy cenowej na wyższy poziom.

## <a name="recommended-troubleshooting-flow"></a>Zalecane przepływu rozwiązywania problemów

 Wykonaj schemat blokowy dla zalecane podejście do rozwiązywania problemów z wydajnością przy użyciu inteligentnego szczegółowych informacji.

Insights inteligentnego dostęp za pośrednictwem portalu Azure, przechodząc do analiz SQL Azure. Próba Znajdź przychodzących alertów wydajności i zaznacz je. Określ, co dzieje się na stronie wykryć. Sprawdź podane analiza głównych przyczyn problemu, tekst zapytania, trendów czasu zapytania i zdarzenia zmiany. Spróbuj rozwiązać problem przy użyciu zalecenie inteligentnego szczegółowe informacje umożliwiające rozwiązanie tego problemu z wydajnością. 

[![Schemat blokowy rozwiązywania problemów](./media/sql-database-intelligent-insights/intelligent-insights-troubleshooting-flowchart.png)](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/intelligent-insight/Troubleshoot%20Azure%20SQL%20Database%20performance%20issues%20using%20Intelligent%20Insight.pdf)

> [!TIP]
> Wybierz schemat blokowy, aby pobrać wersję pliku PDF.

Insights inteligentnego musi zazwyczaj jedna godzina czas na przeprowadzenie analiza głównych przyczyn problem z wydajnością. Jeśli nie można zlokalizować problem w usłudze inteligentnego Insights i jest bardzo istotne dla Ciebie, umożliwia ręcznie Określ przyczynę problemu z wydajnością magazynu zapytań. (Zwykle tych problemów są mniej niż co godzinę). Aby uzyskać więcej informacji, zobacz [monitorowania wydajności przy użyciu magazynu zapytań](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store).

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się [Insights inteligentnego](sql-database-intelligent-insights.md) pojęcia.
- Użyj [dziennika diagnostyki wydajności inteligentnego bazy danych SQL Azure Insights](sql-database-intelligent-insights-use-diagnostics-log.md).
- Monitor [bazy danych SQL Azure za pomocą usługi Azure SQL Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-sql).
- Dowiedz się, jak [zbierania i wykorzystywania danych dziennika z zasobów platformy Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md).
