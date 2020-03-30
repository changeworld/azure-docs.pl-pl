---
title: Rozwiązywanie problemów z wydajnością przy użyciu funkcji Intelligent Insights
description: Usługa Intelligent Insights ułatwia rozwiązywanie problemów z wydajnością usługi Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 03/10/2020
ms.openlocfilehash: 739bba7ed9ab4770a762c08fccc422ce048ae11d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79214090"
---
# <a name="troubleshoot-azure-sql-database-performance-issues-with-intelligent-insights"></a>Rozwiązywanie problemów z wydajnością usługi Azure SQL Database za pomocą aplikacji Intelligent Insights

Ta strona zawiera informacje na temat problemów z wydajnością usługi Azure SQL Database i wystąpienia zarządzanego wykrytych za pośrednictwem dziennika zasobów [usługi Intelligent Insights.](sql-database-intelligent-insights.md) Metryki i dzienniki zasobów mogą być przesyłane strumieniowo do [dzienników usługi Azure Monitor,](../azure-monitor/insights/azure-sql.md) [usługi Azure Event Hubs,](../azure-monitor/platform/resource-logs-stream-event-hubs.md) [usługi Azure Storage](sql-database-metrics-diag-logging.md#stream-into-azure-storage)lub rozwiązania innej firmy dla niestandardowych funkcji alertów i raportowania DevOps.

> [!NOTE]
> Aby uzyskać przewodnik dotyczący szybkiego rozwiązywania problemów z wydajnością bazy danych SQL przy użyciu aplikacji Intelligent Insights, zobacz [schemat blokowy zalecanego rozwiązywania problemów](sql-database-intelligent-insights-troubleshoot-performance.md#recommended-troubleshooting-flow) z przepływem w tym dokumencie.

## <a name="detectable-database-performance-patterns"></a>Wykrywalne wzorce wydajności bazy danych

Usługa Intelligent Insights automatycznie wykrywa problemy z wydajnością baz danych w bazie danych SQL azure na podstawie czasu oczekiwania wykonania kwerendy, błędów lub limitów czasu. Dane wyjściowe usługi Intelligent Insights wykryły wzorce wydajności w dzienniku zasobów bazy danych SQL. Wykrywalne wzorce wydajności są podsumowane w poniższej tabeli.

| Wykrywalne wzorce wydajności | Opis bazy danych SQL platformy Azure i pul elastycznych | Opis baz danych w wystąpieniu zarządzanym |
| :------------------- | ------------------- | ------------------- |
| [Osiąganie limitów zasobów](sql-database-intelligent-insights-troubleshoot-performance.md#reaching-resource-limits) | Zużycie dostępnych zasobów (DTU), wątków procesu roboczego bazy danych lub sesji logowania do bazy danych dostępnych w monitorowanej subskrypcji osiągnęło limity. Ma to wpływ na wydajność bazy danych SQL. | Zużycie zasobów procesora CPU osiąga limity wystąpienia zarządzanego. Ma to wpływ na wydajność bazy danych. |
| [Zwiększenie obciążenia pracą](sql-database-intelligent-insights-troubleshoot-performance.md#workload-increase) | Wykryto zwiększenie obciążenia lub ciągłą akumulację obciążenia w bazie danych. Ma to wpływ na wydajność bazy danych SQL. | Wykryto zwiększenie obciążenia. Ma to wpływ na wydajność bazy danych. |
| [Ciśnienie pamięci](sql-database-intelligent-insights-troubleshoot-performance.md#memory-pressure) | Pracownicy, którzy zażądali dotacji pamięci, muszą czekać na alokacje pamięci dla statystycznie istotnych ilości czasu lub zwiększonej akumulacji pracowników, którzy zażądali dotacji pamięci. Ma to wpływ na wydajność bazy danych SQL. | Pracownicy, którzy zażądali dotacji pamięci czekają na alokacje pamięci przez statystycznie istotną ilość czasu. Ma to wpływ na wydajność bazy danych. |
| [Blokowania](sql-database-intelligent-insights-troubleshoot-performance.md#locking) | Wykryto nadmierne blokowanie bazy danych wpływające na wydajność bazy danych SQL. | Wykryto nadmierne blokowanie bazy danych wpływające na wydajność bazy danych. |
| [Zwiększona wartość MAXDOP](sql-database-intelligent-insights-troubleshoot-performance.md#increased-maxdop) | Maksymalny stopień opcji równoległości (MAXDOP) został zmieniony wpływających na wydajność wykonywania kwerendy. Ma to wpływ na wydajność bazy danych SQL. | Maksymalny stopień opcji równoległości (MAXDOP) został zmieniony wpływających na wydajność wykonywania kwerendy. Ma to wpływ na wydajność bazy danych. |
| [Pagelatch rywalizacja](sql-database-intelligent-insights-troubleshoot-performance.md#pagelatch-contention) | Wiele wątków są jednocześnie próbuje uzyskać dostęp do tych samych stron buforu danych w pamięci, co powoduje wydłużenie czasu oczekiwania i powodując pagelatch rywalizacji. Ma to wpływ na wydajność bazy danych SQL. | Wiele wątków są jednocześnie próbuje uzyskać dostęp do tych samych stron buforu danych w pamięci, co powoduje wydłużenie czasu oczekiwania i powodując pagelatch rywalizacji. Ma to wpływ na wydajność bazy danych. |
| [Brakujący indeks](sql-database-intelligent-insights-troubleshoot-performance.md#missing-index) | Wykryto brak indeksu wpływającego na wydajność bazy danych SQL. | Wykryto brak indeksu wpływającego na wydajność bazy danych. |
| [Nowe zapytanie](sql-database-intelligent-insights-troubleshoot-performance.md#new-query) | Wykryto nową kwerendę wpływającą na ogólną wydajność bazy danych SQL. | Wykryto nową kwerendę wpływającą na ogólną wydajność bazy danych. |
| [Zwiększona statystyka oczekiwania](sql-database-intelligent-insights-troubleshoot-performance.md#increased-wait-statistic) | Wykryto wydłużony czas oczekiwania bazy danych wpływający na wydajność bazy danych SQL. | Wykryto wydłużony czas oczekiwania bazy danych wpływający na wydajność bazy danych. |
| [Rywalizacja o tempdb](sql-database-intelligent-insights-troubleshoot-performance.md#tempdb-contention) | Wiele wątków próbuje uzyskać dostęp do tego samego zasobu TempDB powodując wąskie gardło. Ma to wpływ na wydajność bazy danych SQL. | Wiele wątków próbuje uzyskać dostęp do tego samego zasobu TempDB powodując wąskie gardło. Ma to wpływ na wydajność bazy danych. |
| [Niedobór puli elastycznej DTU](sql-database-intelligent-insights-troubleshoot-performance.md#elastic-pool-dtu-shortage) | Niedobór dostępnych eDTU w puli elastycznej wpływa na wydajność bazy danych SQL. | Niedostępne dla wystąpienia zarządzanego, ponieważ używa modelu vCore. |
| [Regresj planu](sql-database-intelligent-insights-troubleshoot-performance.md#plan-regression) | Wykryto nowy plan lub zmianę obciążenia istniejącego planu. Ma to wpływ na wydajność bazy danych SQL. | Wykryto nowy plan lub zmianę obciążenia istniejącego planu. Ma to wpływ na wydajność bazy danych. |
| [Zmiana wartości konfiguracji o zakresie bazy danych](sql-database-intelligent-insights-troubleshoot-performance.md#database-scoped-configuration-value-change) | Wykryto zmianę konfiguracji w bazie danych SQL, która ma wpływ na wydajność bazy danych. | Wykryto zmianę konfiguracji w bazie danych wpływającą na wydajność bazy danych. |
| [Powolny klient](sql-database-intelligent-insights-troubleshoot-performance.md#slow-client) | Powolny klient aplikacji nie może zużywać danych wyjściowych z bazy danych wystarczająco szybko. Ma to wpływ na wydajność bazy danych SQL. | Powolny klient aplikacji nie może zużywać danych wyjściowych z bazy danych wystarczająco szybko. Ma to wpływ na wydajność bazy danych. |
| [Obniżenie poziomu cen](sql-database-intelligent-insights-troubleshoot-performance.md#pricing-tier-downgrade) | Akcja obniżenia poziomu cen zmniejszyła dostępne zasoby. Ma to wpływ na wydajność bazy danych SQL. | Akcja obniżenia poziomu cen zmniejszyła dostępne zasoby. Ma to wpływ na wydajność bazy danych. |

> [!TIP]
> Aby uzyskać ciągłą optymalizację wydajności bazy danych SQL, włącz [automatyczne dostrajanie bazy danych SQL Database platformy Azure](sql-database-automatic-tuning.md). Ta unikatowa funkcja wbudowanej analizy bazy danych SQL stale monitoruje bazę danych SQL, automatycznie dostosowuje indeksy i stosuje poprawki planu wykonywania zapytań.
>

W poniższej sekcji opisano wykrywalne wzorce wydajności bardziej szczegółowo.

## <a name="reaching-resource-limits"></a>Osiąganie limitów zasobów

### <a name="what-is-happening"></a>Co się dzieje

Ten wykrywalny wzorzec wydajności łączy problemy z wydajnością, które są związane z osiągnięciem dostępnych limitów zasobów, limitów procesu roboczego i limitów sesji. Po wykryciu tego problemu z wydajnością pole opisu dziennika diagnostyki wskazuje, czy problem z wydajnością jest związany z limitami zasobów, procesu roboczego lub sesji.

Zasoby w bazie danych SQL są zazwyczaj odwoływane do zasobów [DTU](sql-database-what-is-a-dtu.md) lub [vCore.](sql-database-service-tiers-vcore.md) Wzorzec osiągnięcia limitów zasobów jest rozpoznawany po wykryciu obniżenia wydajności kwerendy jest spowodowane osiągnięciem dowolnego z mierzonych limitów zasobów.

Zasób limitów sesji oznacza liczbę dostępnych równoczesnych logowań do bazy danych SQL. Ten wzorzec wydajności jest rozpoznawany, gdy aplikacje, które są połączone z bazami danych SQL osiągnęły liczbę dostępnych równoczesnych logowań do bazy danych. Jeśli aplikacje próbują użyć więcej sesji niż są dostępne w bazie danych, wpływa na wydajność kwerendy.

Osiągnięcie limitów procesu roboczego jest specyficznym przypadkiem osiągnięcia limitów zasobów, ponieważ dostępni pracownicy nie są wliczane do użycia DTU lub vCore. Osiągnięcie limitów procesu roboczego w bazie danych może spowodować wzrost czasu oczekiwania specyficzne dla zasobów, co powoduje obniżenie wydajności kwerendy.

### <a name="troubleshooting"></a>Rozwiązywanie problemów

Dane wyjściowe dziennika diagnostyki kwerendy skróty kwerend, które miały wpływ na procenty wydajności i zużycia zasobów. Można użyć tych informacji jako punkt wyjścia do optymalizacji obciążenia bazy danych. W szczególności można zoptymalizować kwerendy, które wpływają na spadek wydajności, dodając indeksy. Możesz też zoptymalizować aplikacje przy bardziej równomiernej dystrybucji obciążenia. Jeśli nie możesz zmniejszyć obciążeń lub wprowadzić optymalizacji, rozważ zwiększenie warstwy cenowej subskrypcji bazy danych SQL, aby zwiększyć ilość dostępnych zasobów.

Jeśli osiągnięto dostępne limity sesji, można zoptymalizować aplikacje, zmniejszając liczbę logowań wprowadzonych do bazy danych. Jeśli nie możesz zmniejszyć liczby logowań z aplikacji do bazy danych, należy rozważyć zwiększenie warstwy cenowej bazy danych. Można też podzielić i przenieść bazę danych do wielu baz danych w celu bardziej zrównoważonej dystrybucji obciążenia.

Aby uzyskać więcej sugestii dotyczących rozwiązywania limitów sesji, zobacz [Jak radzić sobie z ograniczeniami maksymalnych logowań bazy danych SQL](https://blogs.technet.microsoft.com/latam/20../../how-to-deal-with-the-limits-of-azure-sql-database-maximum-logins/). Zobacz [Omówienie limitów zasobów na serwerze bazy danych SQL, aby](sql-database-resource-limits-database-server.md) uzyskać informacje o limitach na poziomie serwera i subskrypcji.

## <a name="workload-increase"></a>Zwiększenie obciążenia pracą

### <a name="what-is-happening"></a>Co się dzieje

Ten wzorzec wydajności identyfikuje problemy spowodowane zwiększeniem obciążenia lub, w jego bardziej poważnej formie, stosem obciążenia.

Wykrywanie to odbywa się za pomocą kombinacji kilku metryk. Podstawową metryką mierzoną jest wykrywanie wzrostu obciążenia w porównaniu z poprzednim obciążeniem bazowym. Inna forma wykrywania opiera się na mierzeniu dużego wzrostu aktywnych wątków roboczych, który jest wystarczająco duży, aby wpłynąć na wydajność kwerendy.

W bardziej poważnej formie obciążenia może stale piętrzą się ze względu na niezdolność bazy danych SQL do obsługi obciążenia. Rezultatem jest stale rosnący rozmiar obciążenia, który jest warunkiem narastania obciążenia. Ze względu na ten warunek, czas, który czeka na wykonanie rośnie. Ten warunek reprezentuje jeden z najpoważniejszych problemów z wydajnością bazy danych. Ten problem jest wykrywany przez monitorowanie wzrostu liczby przerwanych wątków roboczych.

### <a name="troubleshooting"></a>Rozwiązywanie problemów

Dziennik diagnostyki wyprowadza liczbę zapytań, których wykonanie wzrosła i skrót zapytania kwerendy z największym udziałem w zwiększeniu obciążenia. Można użyć tych informacji jako punkt wyjścia do optymalizacji obciążenia. Kwerenda zidentyfikowana jako największy współautor wzrostu obciążenia jest szczególnie przydatna jako punkt wyjścia.

Można rozważyć dystrybucji obciążeń bardziej równomiernie do bazy danych. Należy rozważyć optymalizację kwerendy, która ma wpływ na wydajność, dodając indeksy. Można również rozdzielić obciążenie między wiele baz danych. Jeśli te rozwiązania nie są możliwe, należy rozważyć zwiększenie warstwy cenowej subskrypcji bazy danych SQL, aby zwiększyć ilość dostępnych zasobów.

## <a name="memory-pressure"></a>Wykorzystanie pamięci

### <a name="what-is-happening"></a>Co się dzieje

Ten wzorzec wydajności wskazuje na pogorszenie wydajności bieżącej bazy danych spowodowane ciśnieniem pamięci lub w jego bardziej poważnej formie stan stosu pamięci, w porównaniu do ostatnich siedmiu dni linii bazowej wydajności.

Ciśnienie pamięci oznacza warunek wydajności, w którym istnieje duża liczba wątków roboczych żądających dotacji pamięci w bazie danych SQL. Duża objętość powoduje, że stan wykorzystania pamięci wysokiej, w którym baza danych SQL nie jest w stanie skutecznie przydzielić pamięci do wszystkich pracowników, którzy jej żądają. Jednym z najczęstszych przyczyn tego problemu jest związane z ilością pamięci dostępnej do bazy danych SQL z jednej strony. Z drugiej strony wzrost obciążenia powoduje wzrost wątków roboczych i ciśnienie pamięci.

Cięższa forma ciśnienia pamięci jest stanem palowania pamięci. Ten warunek wskazuje, że większa liczba wątków roboczych żąda dotacji pamięci niż istnieją kwerendy zwalniające pamięć. Ta liczba wątków roboczych żądających dotacji pamięci również może być stale zwiększa (piętrząc się), ponieważ aparat bazy danych SQL nie jest w stanie przydzielić pamięci wystarczająco wydajnie, aby zaspokoić zapotrzebowanie. Warunek stosu pamięci reprezentuje jeden z najpoważniejszych problemów z wydajnością bazy danych.

### <a name="troubleshooting"></a>Rozwiązywanie problemów

Dziennik diagnostyki wyprowadza szczegóły magazynu obiektu pamięci z urzędnikiem (czyli wątkiem roboczym) oznaczonym jako najwyższa przyczyna wysokiego użycia pamięci i odpowiednich sygnatur czasowych. Te informacje można użyć jako podstawy do rozwiązywania problemów.

Można zoptymalizować lub usunąć zapytania związane z urzędnikami o najwyższym użyciu pamięci. Możesz również upewnić się, że nie wysyłasz zapytań o dane, których nie zamierzasz używać. Dobrą praktyką jest zawsze używać klauzuli WHERE w zapytaniach. Ponadto zaleca się tworzenie indeksów nieklastrowanych w celu wyszukiwania danych, a nie ich skanowania.

Można również zmniejszyć obciążenie, optymalizując lub rozprowadzając je w wielu bazach danych. Można też rozdzielić obciążenie między wiele baz danych. Jeśli te rozwiązania nie są możliwe, należy rozważyć zwiększenie warstwy cenowej subskrypcji bazy danych SQL, aby zwiększyć ilość zasobów pamięci dostępnych dla bazy danych.

Aby uzyskać dodatkowe sugestie dotyczące rozwiązywania problemów, zobacz [Pamięć udziela medytacji: Tajemniczy konsument pamięci PROGRAMU SQL Server z wieloma nazwami](https://techcommunity.microsoft.com/t5/sql-server-support/memory-grants-meditation-the-mysterious-sql-server-memory/ba-p/333994).

## <a name="locking"></a>Blokowanie

### <a name="what-is-happening"></a>Co się dzieje

Ten wzorzec wydajności wskazuje na pogorszenie wydajności bieżącej bazy danych, w którym wykryto nadmierne blokowanie bazy danych w porównaniu z poprzednimi siedmiodniowymi punktami bazowymi wydajności.

W nowoczesnych RDBMS blokowanie jest niezbędne do wdrażania systemów wielowątkowych, w których wydajność jest zmaksymalizowana przez uruchamianie wielu równoczesnych pracowników i równoległych transakcji bazy danych, jeśli to możliwe. Blokowanie w tym kontekście odnosi się do wbudowanego mechanizmu dostępu, w którym tylko pojedyncza transakcja może uzyskać dostęp wyłącznie do wierszy, stron, tabel i plików, które są wymagane i nie konkurują z inną transakcją dla zasobów. Gdy transakcja, która zablokowała zasoby do użycia odbywa się z nimi, blokada tych zasobów jest zwalniana, co umożliwia innym transakcjom dostęp do wymaganych zasobów. Aby uzyskać więcej informacji na temat blokowania, zobacz [Blokowanie w silniku bazy danych](https://msdn.microsoft.com/library/ms190615.aspx).

Jeśli transakcje wykonywane przez aparat SQL czekają na dłuższy okres czasu, aby uzyskać dostęp do zasobów zablokowanych do użycia, ten czas oczekiwania powoduje spowolnienie wydajności wykonywania obciążenia.

### <a name="troubleshooting"></a>Rozwiązywanie problemów

Dziennik diagnostyki wyprowadza szczegóły blokowania, których można użyć jako podstawy rozwiązywania problemów. Można analizować zgłoszone kwerendy blokujące, czyli kwerendy wprowadzające spadek wydajności blokowania i usuwać je. W niektórych przypadkach może być skuteczne w optymalizacji kwerend blokowania.

Najprostszym i najbezpieczniejszym sposobem złagodzenia problemu jest utrzymanie transakcji krótki i zmniejszenie śladu blokady najdroższych zapytań. Można podzielić dużą partię operacji na mniejsze operacje. Dobrą praktyką jest zmniejszenie śladu blokady zapytania przez uczynienie zapytania tak wydajnym, jak to możliwe. Zmniejsz duże skany, ponieważ zwiększają one szanse na zakleszczenie i niekorzystnie wpływają na ogólną wydajność bazy danych. W przypadku zidentyfikowanych kwerend, które powodują blokowanie, można utworzyć nowe indeksy lub dodać kolumny do istniejącego indeksu, aby uniknąć skanowania tabeli.

Aby uzyskać więcej sugestii, zobacz [Jak rozwiązać problemy z blokowaniem, które są spowodowane eskalacją blokady w programie SQL Server](https://support.microsoft.com/help/323630/how-to-resolve-blocking-problems-that-are-caused-by-lock-escalation-in).

## <a name="increased-maxdop"></a>Zwiększona wartość MAXDOP

### <a name="what-is-happening"></a>Co się dzieje

Ten wykrywalny wzorzec wydajności wskazuje warunek, w którym wybrany plan wykonywania kwerendy został zrównoleglizowany więcej niż powinien być. Optymalizator zapytań bazy danych SQL może zwiększyć wydajność obciążenia, wykonując zapytania równolegle, aby przyspieszyć, jeśli to możliwe. W niektórych przypadkach równoległych pracowników przetwarzania kwerendy spędzają więcej czasu oczekiwania na siebie, aby zsynchronizować i scalić wyniki w porównaniu do wykonywania tej samej kwerendy z mniejszą liczbą równoległych pracowników, a nawet w niektórych przypadkach w porównaniu do pojedynczego wątku roboczego.

System ekspertów analizuje bieżącą wydajność bazy danych w porównaniu z okresem bazowym. Określa, czy wcześniej uruchomione zapytanie jest uruchomione wolniej niż wcześniej, ponieważ plan wykonywania kwerendy jest bardziej równoległy niż powinien być.

Opcja konfiguracji serwera MAXDOP w bazie danych SQL służy do kontrolowania, ile rdzeni procesora CPU może być używanych do równoległego wykonywania tej samej kwerendy.

### <a name="troubleshooting"></a>Rozwiązywanie problemów

Skróty kwerendy danych wyjściowych dziennika diagnostyki związane z zapytaniami, dla których czas trwania wykonania wzrosła, ponieważ były one równoległe więcej niż powinny być. Dziennik również wyprowadza czas oczekiwania CXP. Tym razem reprezentuje czas pojedynczy organizator/koordynator wątku (wątek 0) oczekuje na zakończenie wszystkich innych wątków przed scalenie wyników i posuwanie się do przodu. Ponadto dziennik diagnostyki wyprowadza czas oczekiwania, które kwerendy o słabej wydajności czekały w wykonaniu. Te informacje można użyć jako podstawy do rozwiązywania problemów.

Najpierw zoptymalizuj lub uprość złożone zapytania. Dobrą praktyką jest rozbicie długich zadań wsadowych na mniejsze. Ponadto upewnij się, że utworzono indeksy do obsługi zapytań. Można również ręcznie wymusić maksymalny stopień równoległości (MAXDOP) dla kwerendy, która została oflagowana jako słaba wydajność. Aby skonfigurować tę operację przy użyciu funkcji T-SQL, zobacz [Konfigurowanie opcji konfiguracji serwera MAXDOP](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option).

Ustawienie opcji konfiguracji serwera MAXDOP na zero (0) jako wartości domyślnej oznacza, że baza danych SQL może używać wszystkich dostępnych logicznych rdzeni procesora CPU do równoległości wątków do wykonywania pojedynczej kwerendy. Ustawienie MAXDOP na jeden (1) oznacza, że tylko jeden rdzeń może służyć do wykonywania pojedynczego zapytania. W praktyce oznacza to, że równoległość jest wyłączona. W zależności od przypadku na podstawie przypadku, dostępnych rdzeni do bazy danych i informacji dziennika diagnostyki można dostroić opcję MAXDOP do liczby rdzeni używanych do wykonywania zapytań równoległych, które mogą rozwiązać problem w twoim przypadku.

## <a name="pagelatch-contention"></a>Rywalizacja pagelatch

### <a name="what-is-happening"></a>Co się dzieje

Ten wzorzec wydajności wskazuje spadek wydajności bieżącego obciążenia bazy danych z powodu rywalizacji pagelatch w porównaniu do ostatnich linii bazowej obciążenia siedmiodniowego.

Zatrzaski są lekkie mechanizmy synchronizacji używane przez bazę danych SQL, aby włączyć wielowątkowe. Gwarantują one spójność struktur w pamięci, które zawierają indeksy, strony danych i inne struktury wewnętrzne.

Istnieje wiele typów zamków dostępnych w bazie danych SQL. Dla uproszczenia zatrzaski buforu są używane do ochrony stron w pamięci w puli buforów. Zatrzaski we/wy są używane do ochrony stron, które nie zostały jeszcze załadowane do puli buforów. Za każdym razem, gdy dane są zapisywane lub odczytywane ze strony w puli buforów, wątek roboczy musi najpierw uzyskać zatrzask buforu dla strony. Za każdym razem, gdy wątek roboczy próbuje uzyskać dostęp do strony, która nie jest jeszcze dostępna w puli buforów w pamięci, jest wymagane żądanie we/wy w celu załadowania wymaganych informacji z magazynu. Ta sekwencja zdarzeń wskazuje na poważniejszą formę pogorszenia wydajności.

Rywalizacja na stronie zatrzaskuje występuje, gdy wiele wątków jednocześnie próbuje uzyskać zatrzaski na tej samej strukturze w pamięci, co wprowadza wydłużenie czasu oczekiwania do wykonania kwerendy. W przypadku pagelatch we/wy rywalizacji, gdy dane muszą być dostępne z magazynu, ten czas oczekiwania jest jeszcze większy. Może to znacząco wpłynąć na wydajność obciążenia. Rywalizacja pagelatch jest najbardziej typowym scenariuszem wątków oczekujących na siebie i konkurujących o zasoby w wielu systemach procesora CPU.

### <a name="troubleshooting"></a>Rozwiązywanie problemów

Dane rywalizacji pagelatch dziennika diagnostyki. Te informacje można użyć jako podstawy do rozwiązywania problemów.

Ponieważ pagelatch jest mechanizm kontroli wewnętrznej bazy danych SQL, automatycznie określa, kiedy z nich korzystać. Decyzje aplikacji, w tym projekt schematu, może mieć wpływ na zachowanie pagelatch ze względu na deterministyczne zachowanie zamków.

Jedną z metod obsługi rywalizacji zatrzask jest zastąpienie sekwencyjnego klucza indeksu kluczem niesekwencyjnym, aby równomiernie rozłożyć wstawia w całym zakresie indeksu. Zazwyczaj wiodąca kolumna w indeksie rozkłada obciążenie proporcjonalnie. Inną metodą do rozważenia jest partycjonowanie tabeli. Tworzenie schematu partycjonowania mieszania z kolumną obliczoną w tabeli podzielonej na partycje jest typowym podejściem do łagodzenia nadmiernego rywalizacji zatrzaśnięcia. W przypadku pagelatch we/wy rywalizacji, wprowadzenie indeksów pomaga złagodzić ten problem z wydajnością.

Aby uzyskać więcej informacji, zobacz [Diagnozowanie i rozwiązywanie rywalizacji zatrzaśnięcia na programie SQL Server](https://download.microsoft.com/download/B/9/E/B9EDF2CD-1DBF-4954-B81E-82522880A2DC/SQLServerLatchContention.pdf) (pobieranie w formacie PDF).

## <a name="missing-index"></a>Brakujący indeks

### <a name="what-is-happening"></a>Co się dzieje

Ten wzorzec wydajności wskazuje spadek wydajności bieżącego obciążenia bazy danych w porównaniu do ostatnich siedmiu dni linii bazowej z powodu braku indeksu.

Indeks jest używany do przyspieszenia wydajności zapytań. Zapewnia szybki dostęp do danych tabeli, zmniejszając liczbę stron zestawu danych, które muszą być odwiedzone lub zeskanowane.

Określone zapytania, które spowodowały obniżenie wydajności są identyfikowane za pomocą tego wykrywania, dla których tworzenie indeksów byłoby korzystne dla wydajności.

### <a name="troubleshooting"></a>Rozwiązywanie problemów

Raport diagnostyczny wyprowadza skróty kwerendy dla kwerend, które zostały zidentyfikowane w celu wy wpływu na wydajność obciążenia. Można tworzyć indeksy dla tych zapytań. Można również zoptymalizować lub usunąć te zapytania, jeśli nie są one wymagane. Dobrą praktyką wydajności jest unikanie wykonywania zapytań o dane, których nie używasz.

> [!TIP]
> Czy wiesz, że wbudowana inteligencja bazy danych SQL może automatycznie zarządzać najlepiej działającymi indeksami baz danych?
>
> Aby zapewnić ciągłą optymalizację wydajności bazy danych SQL, zaleca się włączenie [automatycznego dostrajania bazy danych SQL](sql-database-automatic-tuning.md). Ta unikalna funkcja wbudowanej analizy bazy danych SQL stale monitoruje bazę danych SQL i automatycznie dostraja i tworzy indeksy dla baz danych.
>

## <a name="new-query"></a>Nowe zapytanie

### <a name="what-is-happening"></a>Co się dzieje

Ten wzorzec wydajności wskazuje, że wykryto nową kwerendę, która działa słabo i wpływa na wydajność obciążenia w porównaniu z siedmiodniową linią bazową wydajności.

Pisanie kwerendy o dobrej skuteczności czasami może być trudnym zadaniem. Aby uzyskać więcej informacji na temat pisania zapytań, zobacz [Pisanie zapytań SQL](https://msdn.microsoft.com/library/bb264565.aspx). Aby zoptymalizować istniejącą wydajność kwerendy, zobacz [Dostrajanie kwerend](https://msdn.microsoft.com/library/ms176005.aspx).

### <a name="troubleshooting"></a>Rozwiązywanie problemów

Dziennik diagnostyki wyprowadza informacje do dwóch nowych kwerend zużywających procesor CPU, w tym ich skróty zapytań. Ponieważ wykryte zapytanie wpływa na wydajność obciążenia, można zoptymalizować kwerendę. Dobrą praktyką jest pobieranie tylko danych, których potrzebujesz. Zaleca się również użycie zapytań z klauzulą WHERE. Zaleca się również uproszczenie złożonych zapytań i podział ich na mniejsze zapytania. Inną dobrą praktyką jest rozbicie dużych zapytań wsadowych na mniejsze zapytania wsadowe. Wprowadzenie indeksów dla nowych zapytań jest zazwyczaj dobrą praktyką w celu złagodzenia tego problemu z wydajnością.

Rozważ użycie [usługi Azure SQL Database Query Performance Insight](sql-database-query-performance.md).

## <a name="increased-wait-statistic"></a>Zwiększona statystyka oczekiwania

### <a name="what-is-happening"></a>Co się dzieje

Ten wykrywalny wzorzec wydajności wskazuje spadek wydajności obciążenia, w którym identyfikowane są kwerendy o niskiej wydajności w porównaniu z poprzednimi siedmiodniowymi punktami bazowymi obciążenia.

W takim przypadku system nie może sklasyfikować kwerend o niskiej wydajności w ramach innych standardowych kategorii wydajności wykrywalnych, ale wykrył statystykę oczekiwania odpowiedzialną za regresję. W związku z tym uważa je za zapytania ze *zwiększoną statystyki oczekiwania*, gdzie statystyki oczekiwania odpowiedzialny za regresji jest również narażony.

### <a name="troubleshooting"></a>Rozwiązywanie problemów

Dziennik diagnostyki wyprowadza informacje o zwiększonym czasie oczekiwania szczegóły i skróty kwerend, których dotyczy problem.

Ponieważ system nie może pomyślnie zidentyfikować główną przyczynę kwerend o słabej skuteczności, informacje diagnostyczne są dobrym punktem wyjścia do ręcznego rozwiązywania problemów. Można zoptymalizować wydajność tych zapytań. Dobrą praktyką jest pobieranie tylko danych, których potrzebujesz, oraz uproszczenie i rozbicie złożonych zapytań na mniejsze.

Aby uzyskać więcej informacji na temat optymalizacji wydajności kwerendy, zobacz [dostrajanie kwerend](https://msdn.microsoft.com/library/ms176005.aspx).

## <a name="tempdb-contention"></a>Rywalizacja o tempdb

### <a name="what-is-happening"></a>Co się dzieje

Ten wykrywalny wzorzec wydajności wskazuje warunek wydajności bazy danych, w którym istnieje wąskie gardło wątków próbujących uzyskać dostęp do zasobów tempDB. (Ten warunek nie jest związany z we/wy). Typowy scenariusz dla tego problemu z wydajnością jest setki równoczesnych zapytań, które wszystkie tworzenie, używanie, a następnie upuszczanie małych tabel tempDB. System wykrył, że liczba równoczesnych zapytań przy użyciu tych samych tabel tempDB wzrosła z wystarczającym znaczeniem statystycznym, aby wpłynąć na wydajność bazy danych w porównaniu z poprzednimi siedmiodniowymi punktami bazowymi wydajności.

### <a name="troubleshooting"></a>Rozwiązywanie problemów

Dziennik diagnostyki wyprowadza szczegóły rywalizacji tempDB. Można użyć informacji jako punktu wyjścia do rozwiązywania problemów. Istnieją dwie rzeczy, które można realizować w celu złagodzenia tego rodzaju rywalizacji i zwiększenia przepływności ogólnego obciążenia: Można zatrzymać przy użyciu tabel tymczasowych. Można również użyć tabel zoptymalizowanych pod kątem pamięci.

Aby uzyskać więcej informacji, zobacz [Wprowadzenie do tabel zoptymalizowanych pod kątem pamięci](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/introduction-to-memory-optimized-tables).

## <a name="elastic-pool-dtu-shortage"></a>Niedobór puli elastycznej DTU

### <a name="what-is-happening"></a>Co się dzieje

Ten wykrywalny wzorzec wydajności wskazuje na spadek wydajności bieżącego obciążenia bazy danych w porównaniu z poprzednimi siedmiodniowymi punktami bazowymi. Jest to spowodowane brakiem dostępnych procesorów DTU w elastycznej puli subskrypcji.

Zasoby w bazie danych SQL są zazwyczaj określane jako [zasoby jednostki DTU,](sql-database-purchase-models.md#dtu-based-purchasing-model)które składają się z mieszanej miary zasobów procesora CPU i we/wy (we/wy dziennika danych i transakcji). [Zasoby puli elastycznej platformy Azure](sql-database-elastic-pool.md) są używane jako pula dostępnych zasobów eDTU współużytkowane przez wiele baz danych do celów skalowania. Jeśli dostępne zasoby eDTU w puli elastycznej nie są wystarczająco duże, aby obsługiwać wszystkie bazy danych w puli, system wykrywa problem z niedoborem identyfikatorów DTU w puli elastycznej.

### <a name="troubleshooting"></a>Rozwiązywanie problemów

Dziennik diagnostyki wyprowadza informacje z puli elastycznej, zawiera listę najlepszych baz danych korzystających z DTU i zapewnia procent identyfikatorów DTU puli używanych przez bazę danych o najwyższej wartości.

Ponieważ ten warunek wydajności jest powiązany z wieloma bazami danych przy użyciu tej samej puli eDTU w puli elastycznej, kroki rozwiązywania problemów koncentrują się na najlepszych bazach danych korzystających z DTU. Można zmniejszyć obciążenie w najlepiej zużywających baz danych, który obejmuje optymalizację kwerend y zużywające w tych bazach danych. Można również upewnić się, że nie są kwerendy danych, które nie są używane. Innym podejściem jest optymalizacja aplikacji przy użyciu najlepszych baz danych korzystających z DTU i redystrybucji obciążenia między wieloma bazami danych.

Jeśli zmniejszenie i optymalizacja bieżącego obciążenia w najlepszych bazach danych zużywających jednostki DTU nie jest możliwe, należy rozważyć zwiększenie warstwy cenowej puli elastycznej. Taki wzrost powoduje wzrost dostępnych DTU w puli elastycznej.

## <a name="plan-regression"></a>Regresj planu

### <a name="what-is-happening"></a>Co się dzieje

Ten wykrywalny wzorzec wydajności oznacza warunek, w którym baza danych SQL wykorzystuje nieoptymalny plan wykonywania zapytań. Plan nieoptymalny zazwyczaj powoduje zwiększenie wykonywania kwerendy, co prowadzi do dłuższego czasu oczekiwania dla bieżących i innych zapytań.

Baza danych SQL określa plan wykonywania kwerendy z najmniejszym kosztem wykonania kwerendy. W miarę zmiany typu zapytań i obciążeń, czasami istniejące plany nie są już wydajne lub być może baza danych SQL nie została dokonana dobra ocena. W ramach korekty plany wykonywania kwerend mogą być ręcznie wymuszone.

Ten wykrywalny wzorzec wydajności łączy w sobie trzy różne przypadki regresji planu: regresja nowego planu, regresja starego planu i istniejące plany zmieniły obciążenie. Określony typ regresji planu, który wystąpił znajduje się we właściwości *szczegóły* w dzienniku diagnostyki.

Warunek regresji nowego planu odnosi się do stanu, w którym baza danych SQL rozpoczyna wykonywanie nowego planu wykonywania kwerendy, który nie jest tak wydajny jak stary plan. Stary warunek regresji planu odnosi się do stanu, gdy baza danych SQL przełącza się z przy użyciu nowego, bardziej wydajnego planu do starego planu, który nie jest tak wydajny jak nowy plan. Istniejące plany zmienionej regresji obciążenia odnosi się do stanu, w którym stare i nowe plany stale naprzemiennie, z równowagi będzie bardziej w kierunku planu słabej wydajności.

Aby uzyskać więcej informacji na temat regresji planu, zobacz [Co to jest regresja planu w programie SQL Server?](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../what-is-plan-regression-in-sql-server/).

### <a name="troubleshooting"></a>Rozwiązywanie problemów

Dziennik diagnostyki wyprowadza skróty kwerendy, dobry identyfikator planu, zły identyfikator planu i identyfikatory zapytań. Te informacje można użyć jako podstawy do rozwiązywania problemów.

Można analizować, który plan jest lepiej działający dla określonych zapytań, które można zidentyfikować za pomocą dostarczonych skrótów kwerendy. Po określeniu, który plan działa lepiej dla zapytań, można ręcznie wymusić.

Aby uzyskać więcej informacji, zobacz [Dowiedz się, jak program SQL Server zapobiega regresjom planu](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../you-shall-not-regress-how-sql-server-2017-prevents-plan-regressions/).

> [!TIP]
> Czy wiesz, że wbudowana inteligencja bazy danych SQL może automatycznie zarządzać najlepiej działającymi planami wykonywania zapytań dla baz danych?
>
> Aby zapewnić ciągłą optymalizację wydajności bazy danych SQL, zaleca się włączenie [automatycznego dostrajania bazy danych SQL](sql-database-automatic-tuning.md). Ta unikalna funkcja wbudowanej analizy bazy danych SQL stale monitoruje bazę danych SQL i automatycznie dostraja i tworzy najlepsze plany wykonywania zapytań dla baz danych.
>

## <a name="database-scoped-configuration-value-change"></a>Zmiana wartości konfiguracji o zakresie bazy danych

### <a name="what-is-happening"></a>Co się dzieje

Ten wykrywalny wzorzec wydajności wskazuje warunek, w którym zmiana konfiguracji o zakresie bazy danych powoduje regresję wydajności, która jest wykrywana w porównaniu do ostatnich siedmiu dni zachowanie obciążenia bazy danych. Ten wzorzec oznacza, że ostatnie zmiany wprowadzone do konfiguracji o zakresie bazy danych nie wydają się być korzystne dla wydajności bazy danych.

Zmiany konfiguracji o zakresie bazy danych można ustawić dla każdej bazy danych. Ta konfiguracja jest używana indywidualnie dla każdego przypadku w celu optymalizacji indywidualnej wydajności bazy danych. Dla poszczególnych baz danych można skonfigurować następujące opcje: MAXDOP, LEGACY_CARDINALITY_ESTIMATION, PARAMETER_SNIFFING, QUERY_OPTIMIZER_HOTFIXES i CLEAR PROCEDURE_CACHE.

### <a name="troubleshooting"></a>Rozwiązywanie problemów

Dziennik diagnostyki wyprowadza zmiany konfiguracji o zakresie bazy danych, które zostały wprowadzone niedawno, które spowodowały spadek wydajności w porównaniu do poprzedniego zachowania obciążenia siedmiodniowego. Można przywrócić zmiany konfiguracji do poprzednich wartości. Można również dostroić wartość według wartości, aż do osiągnięcia żądanego poziomu wydajności. Można skopiować wartości konfiguracji zakresu bazy danych z podobnej bazy danych o zadowalającej wydajności. Jeśli nie możesz rozwiązać problemu z wydajnością, przywróć domyślne wartości domyślne bazy danych SQL i spróbuj dostosować począwszy od tej linii bazowej.

Aby uzyskać więcej informacji na temat optymalizacji konfiguracji o zakresie bazy danych i składni T-SQL dotyczące zmiany konfiguracji, zobacz [Zmienianie konfiguracji o zakresie bazy danych (Transact-SQL)](https://msdn.microsoft.com/library/mt629158.aspx).

## <a name="slow-client"></a>Powolny klient

### <a name="what-is-happening"></a>Co się dzieje

Ten wykrywalny wzorzec wydajności wskazuje warunek, w którym klient korzystający z bazy danych SQL nie może korzystać z danych wyjściowych z bazy danych tak szybko, jak baza danych wysyła wyniki. Ponieważ baza danych SQL nie przechowuje wyników wykonywanych kwerend w buforze, spowalnia i czeka na klienta do korzystania z przesłanych danych wyjściowych zapytania przed kontynuowaniem. Ten warunek może być również związane z siecią, która nie jest wystarczająco szybki, aby przesłać dane wyjściowe z bazy danych SQL do klienta zużywającego.

Ten warunek jest generowany tylko wtedy, gdy regresja wydajności zostanie wykryta w porównaniu do ostatnich siedmiu dni zachowanie obciążenia bazy danych. Ten problem z wydajnością jest wykrywany tylko wtedy, gdy występuje statystycznie istotna degradacja wydajności w porównaniu z poprzednim zachowaniem wydajności.

### <a name="troubleshooting"></a>Rozwiązywanie problemów

Ten wykrywalny wzorzec wydajności wskazuje warunek po stronie klienta. Rozwiązywanie problemów jest wymagane w aplikacji po stronie klienta lub sieci po stronie klienta. Dziennik diagnostyki wyprowadza skróty kwerendy i czas oczekiwania, które wydają się czekać najbardziej dla klienta do korzystania z nich w ciągu ostatnich dwóch godzin. Te informacje można użyć jako podstawy do rozwiązywania problemów.

Można zoptymalizować wydajność aplikacji do użycia tych zapytań. Można również rozważyć możliwe problemy z opóźnieniem sieci. Ponieważ problem z obniżeniem wydajności był oparty na zmianie w ciągu ostatnich siedmiu dni linii bazowej wydajności, można zbadać, czy ostatnie zmiany warunku aplikacji lub sieci spowodowały to zdarzenie regresji wydajności.

## <a name="pricing-tier-downgrade"></a>Obniżenie poziomu cen

### <a name="what-is-happening"></a>Co się dzieje

Ten wykrywalny wzorzec wydajności wskazuje warunek, w którym warstwa cenowa subskrypcji bazy danych SQL została obniżona. Ze względu na zmniejszenie zasobów (DTU) dostępnych dla bazy danych, system wykrył spadek wydajności bieżącej bazy danych w porównaniu do ostatnich siedmiu dni linii bazowej.

Ponadto może istnieć warunek, w którym warstwa cenowa subskrypcji bazy danych SQL została obniżona, a następnie uaktualniona do wyższej warstwy w krótkim okresie czasu. Wykrywanie tej tymczasowej degradacji wydajności jest wyprowadzane w sekcji szczegółów dziennika diagnostyki jako downgrade warstwy cenowej i uaktualnienia.

### <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli obniżysz warstwę cenową i w związku z tym jednostki DTU dostępne dla bazy danych SQL i jesteś zadowolony z wydajności, nie musisz nic robić. Jeśli obniżysz warstwę cenową i nie masz niezadowolenia z wydajności bazy danych SQL, zmniejsz obciążenia bazy danych lub rozważ zwiększenie warstwy cenowej do wyższego poziomu.

## <a name="recommended-troubleshooting-flow"></a>Zalecany przepływ rozwiązywania problemów

 Postępuj zgodnie z schematem blokowym, aby uzyskać zalecane podejście do rozwiązywania problemów z wydajnością przy użyciu aplikacji Intelligent Insights.

Uzyskaj dostęp do inteligentnej aplikacji Insights za pośrednictwem witryny Azure portal, przechodząc do usługi Azure SQL Analytics. Spróbuj zlokalizować przychodzący alert wydajności i wybierz go. Określ, co dzieje się na stronie wykrywania. Obserwuj analizę przyczyn źródłowych problemu, tekst kwerendy, trendy czasu kwerendy i ewolucję zdarzeń. Spróbuj rozwiązać ten problem przy użyciu zalecenia intelligent insights do łagodzenia problemu z wydajnością.

[![Rozwiązywanie problemów z schematem blokowym](./media/sql-database-intelligent-insights/intelligent-insights-troubleshooting-flowchart.png)](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/intelligent-insight/Troubleshoot%20Azure%20SQL%20Database%20performance%20issues%20using%20Intelligent%20Insight.pdf)

> [!TIP]
> Wybierz schemat blokowy, aby pobrać wersję PDF.

Usługa Intelligent Insights zwykle potrzebuje jednej godziny czasu na przeprowadzenie analizy głównej przyczyny problemu z wydajnością. Jeśli nie możesz zlokalizować problemu w inteligentnych statystykach i jest on dla Ciebie krytyczny, użyj Magazynu zapytań, aby ręcznie zidentyfikować główną przyczynę problemu z wydajnością. (Zazwyczaj te problemy mają mniej niż godzinę).) Aby uzyskać więcej informacji, zobacz [Monitorowanie wydajności przy użyciu Magazynu zapytań](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store).

## <a name="next-steps"></a>Następne kroki

- Poznaj koncepcje [inteligentnego wglądu w dane.](sql-database-intelligent-insights.md)
- Użyj [dziennika diagnostyki wydajności usługi Intelligent Insights Azure SQL Database](sql-database-intelligent-insights-use-diagnostics-log.md).
- Monitoruj [bazę danych SQL azure przy użyciu usługi Azure SQL Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-sql).
- Dowiedz [się, jak zbierać i wykorzystywać dane dziennika z zasobów platformy Azure.](../azure-monitor/platform/platform-logs-overview.md)
