---
title: Monitorowanie wydajności bazy danych za pomocą usługi Intelligent Insights — usługi Azure SQL Database | Dokumentacja firmy Microsoft
description: Usługa Azure SQL Database Intelligent Insights używa wbudowanym funkcjom analizy, aby stale monitorować użycie bazy danych za pomocą sztucznej inteligencji i wykrywać szkodliwe zdarzenia, które powodują spadek wydajności.
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
ms.date: 12/19/2018
ms.openlocfilehash: 15154844c954e53ca1add5d3fbaa3e9d02152ad2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60703215"
---
# <a name="intelligent-insights-using-ai-to-monitor-and-troubleshoot-database-performance"></a>Wydajność bazy danych Intelligent Insights za pomocą sztucznej Inteligencji do monitorowania i rozwiązywania problemów

Usługa Azure SQL Database Intelligent Insights poinformuje Cię o tym, co się dzieje z wydajność bazy danych SQL Database i wystąpienia zarządzanego.

Intelligent Insights używa wbudowanym funkcjom analizy, aby stale monitorować użycie bazy danych za pomocą sztucznej inteligencji i wykrywać szkodliwe zdarzenia, które powodują spadek wydajności. Po wykryciu, szczegółowej analizy jest wykonywane, która generuje dziennik diagnostyczny, wraz z oceną inteligentne problemu. Ocena składa się z główną przyczynę występowania problemu z wydajnością bazy danych i, jeśli jest to możliwe, rekomendacje dotyczące zwiększania wydajności.

## <a name="what-can-intelligent-insights-do-for-you"></a>Intelligent Insights czynności dla Ciebie

Intelligent Insights jest unikatowa funkcja platformy Azure wbudowane funkcje analizy, który zawiera następujące wartości:

- Aktywne monitorowanie
- Szczegółowe informacje o wydajności dostosowanych do potrzeb
- Wczesne wykrywanie obniżenia wydajności bazy danych
- Główna przyczyna analizy wykryte problemy
- Zalecenia dotyczące poprawy wydajności
- Skalowanie w poziomie funkcja na setki tysięcy baz danych
- Pozytywny wpływ na zasoby infrastruktury DevOps i całkowity koszt posiadania

## <a name="how-does-intelligent-insights-work"></a>Jak działa Intelligent Insights

Intelligent Insights analizuje wydajność bazy danych przez porównanie obciążenie bazy danych z ostatniej godziny z ostatnich 7 dniowy plan bazowy obciążeniem. Obciążenie bazy danych składa się z zapytania uznane za najbardziej znaczące wydajności bazy danych, takie jak najczęściej powtarzanych i największy zapytania. Ponieważ każda baza danych jest unikatowy, na podstawie jego struktury danych, użycia i aplikacji, każdej linii bazowej obciążenia, który jest generowany jest określone i unikatowe dla poszczególnych wystąpień. Intelligent Insights, niezależnie od linii bazowej obciążenia również monitoruje bezwzględne progi operacyjną i wykrywa problemy z czasy oczekiwania nadmierne, wyjątki krytyczne i problemy z parameterizations zapytania, które mogłyby wpłynąć na wydajność.

Po wykrycia problemu z wydajnością degradacji z wielu metryk obserwowanych za pomocą sztucznej inteligencji analiza jest wykonywana. Dziennik diagnostyczny jest generowany za pomocą inteligentnego wglądu, co się dzieje z bazą danych. Intelligent Insights ułatwia śledzenie problemów z wydajnością bazy danych od jego pierwszego pojawienia się do czasu rozwiązania problemu. Każdy wykryty problem jest śledzone przy użyciu jej cyklu projektowania z początkowej emisji, wykrywanie i weryfikację poprawę wydajności do jego zakończenia.

![Przepływu pracy analizy wydajności bazy danych](./media/sql-database-intelligent-insights/intelligent-insights-concept.png)

Metryki używane do mierzenia i wykryć problemy z wydajnością bazy danych są oparte na czas trwania zapytania, limit czasu żądań, czasy oczekiwania nadmiernego i błędnych żądań. Aby uzyskać więcej informacji na temat metryk, zobacz [metryki wykrywania](sql-database-intelligent-insights.md#detection-metrics) części tego dokumentu.

Identyfikuje bazy danych SQL, spadku wydajności wydajności są rejestrowane w dzienniku diagnostyki, za pomocą inteligentnego wpisów, które składają się z następującymi właściwościami:

| Właściwość             | Szczegóły              |
| :------------------- | ------------------- |
| informacje o bazie danych | Metadane dotyczące bazy danych, na którym wykryto szczegółowe informacje, takie jak identyfikator URI zasobu. |
| Zakres czasu obserwowanych | Godzina rozpoczęcia i zakończenia dla okresu wykryte wgląd. |
| Metryki objęte wpływem | Metryki, który spowodował wygenerowanie szczegółowe informacje: <ul><li>Wyślij zapytanie do zwiększenia czasu trwania [s].</li><li>Nadmierne oczekiwania [s].</li><li>Upłynął limit czasu żądania [wartość procentowa].</li><li>Żądania raportuje [wartość procentowa].</li></ul>|
| Wartość wpływu | Wartość metryki są mierzone. |
| Kody błędów i zapytania objęte wpływem | Wyślij zapytanie do wyznaczania wartości skrótu lub kod błędu. Mogą one używane, aby łatwo odnoszą się do objęte zapytania. Metryki, które składają się z zwiększyć czas trwania zapytania, czas oczekiwania, liczby limitu czasu lub kody błędów są dostarczane. |
| Wykrycia | Wykrywanie określonych w bazie danych w czasie zdarzenia. Brak 15 wykrywanie wzorców. Aby uzyskać więcej informacji, zobacz [bazy danych Rozwiązywanie problemów z wydajnością dzięki inteligentnej analizie](sql-database-intelligent-insights-troubleshoot-performance.md). |
| Analiza głównej przyczyny | Główna przyczyna analizy problemu zidentyfikowanego w formacie czytelnym dla człowieka. Niektóre szczegółowe dane mogą zawierać zalecenie poprawy wydajności, jeśli jest to możliwe. |
|||

Praktyczne omówienie przy użyciu Intelligent Insights z usługą Azure SQL Analytics i typowe scenariusze użycia Zobacz osadzone wideo:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Get-Intelligent-Insights-for-Improving-Azure-SQL-Database-Performance/player]
>

Intelligent Insights świeci wykrywanie i rozwiązywanie problemów z wydajnością bazy danych SQL. Aby można było używać Intelligent Insights rozwiązywać problemy z wydajnością bazy danych SQL Database i wystąpienia zarządzanego, zobacz [problemy z wydajnością rozwiązywanie problemów z usługi Azure SQL Database dzięki inteligentnej analizie](sql-database-intelligent-insights-troubleshoot-performance.md).

## <a name="configure-intelligent-insights"></a>Skonfiguruj usługę Intelligent Insights

Dane wyjściowe funkcji Intelligent Insights jest dziennik diagnostyczny inteligentne wydajności. Ten dziennik może być zużyte na kilka sposobów — za pośrednictwem przesyłając strumieniowo do usługi Azure SQL Analytics, Azure Event Hubs i Azure storage lub innych firm produktu.

- Korzystanie z tego produktu za pomocą [usługi Azure SQL Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-sql) do wyświetlania szczegółowych informacji za pomocą interfejsu użytkownika witryny Azure Portal. Jest to zintegrowane rozwiązanie platformy Azure i najczęściej spotykaną metodą, aby wyświetlić szczegółowe informacje.
- Korzystanie z produktu za pomocą usługi Azure Event Hubs do tworzenia niestandardowego monitorowania i zgłaszania alertów scenariuszy
- Korzystanie z produktu z usługą Azure storage do programowania niestandardowych aplikacji, to takie, na przykład raportów niestandardowych, archiwizowanie długoterminowe dane itd.

Integracja Intelligent Insights z innymi produktami do usługi Azure SQL Analytics, Azure Event Hub, Azure storage lub produktów innych firm do użycia odbywa się za pośrednictwem pierwszego Włączanie Intelligent Insights rejestrowania (Dziennik "SQLInsights") w diagnostyczne dane przesyłane strumieniowo do jednej z tych produktów Zaloguj się w bloku ustawienia bazy danych, a następnie konfigurując Intelligent Insights.

Aby uzyskać więcej informacji na temat włączania rejestrowania Intelligent Insights i konfigurowania danych dziennika strumieniowe konsumencki produktu, zobacz [metryk usługi Azure SQL Database i rejestrowania diagnostycznego](sql-database-metrics-diag-logging.md).

### <a name="set-up-with-azure-sql-analytics"></a>Konfigurowanie przy użyciu usługi Azure SQL Analytics

Rozwiązanie SQL Analytics na platformie Azure zapewnia graficzny interfejs użytkownika, raportowanie i alertów na wydajność bazy danych, wraz z Intelligent Insights danych dziennika diagnostycznego.

> [!TIP]
> Szybkie wprowadzenie do: Najłatwiejszym sposobem na wyższy, przy użyciu Intelligent Insights jest z niego korzystać, wraz z usługi Azure SQL Analytics, która zapewnia graficzny interfejs użytkownika do problemów z wydajnością bazy danych. Dodawanie rozwiązania do usługi Azure SQL Analytics w portalu Marketplace, Utwórz obszar roboczy, w tym rozwiązaniu i dla każdej bazy danych, które ma zostać włączone Intelligent Insights na, skonfiguruj przesyłania strumieniowego dziennika "SQLInsights" w bloku ustawienia diagnostyki bazy danych do obszar roboczy usługi Azure SQL Analytics.
>

Wymaganie wstępne dotyczy zostały dodane do pulpitu nawigacyjnego witryny Azure portal z witryny marketplace usługi Azure SQL Analytics i Utwórz obszar roboczy, zobacz [Konfigurowanie usługi Azure SQL Analytics](../azure-monitor/insights/azure-sql.md#configuration)

Aby Intelligent Insights za pomocą usługi Azure SQL Analytics, należy skonfigurować dane dzienników Intelligent Insights strumieniowe przesyłanie do obszaru roboczego usługi Azure SQL Analytics, został utworzony w poprzednim kroku, zobacz [metryk usługi Azure SQL Database i diagnostyki rejestrowanie](sql-database-metrics-diag-logging.md).

Poniższy przykład pokazuje, że Intelligent Insights wyświetlane za pomocą usługi Azure SQL Analytics:

![Intelligent Insights raportu](./media/sql-database-intelligent-insights/intelligent-insights-azure-sql-analytics.png)

### <a name="set-up-with-event-hubs"></a>Konfigurowanie przy użyciu usługi Event Hubs

Aby Intelligent Insights za pomocą usługi Event Hubs, należy skonfigurować dane dzienników Intelligent Insights przesyłane strumieniowo do usługi Event Hubs, zobacz [dzienniki diagnostyczne usługi Azure Stream do usługi Event Hubs](../azure-monitor/platform/diagnostic-logs-stream-event-hubs.md).

Aby skonfigurować niestandardowe monitorowania i zgłaszania alertów za pomocą usługi Event Hubs, zobacz [co należy zrobić metryki i Diagnostyka dzienników w usłudze Event Hubs](sql-database-metrics-diag-logging.md#what-to-do-with-metrics-and-diagnostics-logs-in-event-hubs).

### <a name="set-up-with-azure-storage"></a>Konfigurowanie usługi Azure Storage

Intelligent Insights za pomocą magazynu, należy skonfigurować dane dzienników Intelligent Insights przesyłane do magazynu, zobacz [Stream do usługi Azure Storage](sql-database-metrics-diag-logging.md#stream-into-storage).

### <a name="custom-integrations-of-intelligent-insights-log"></a>Niestandardowych integracji dziennik funkcji Intelligent Insights

Aby użyć Intelligent Insights z narzędziami innych firm lub niestandardowych alertów i monitorowania rozwoju, zobacz [Użyj dziennika diagnostyki wydajności bazy danych Intelligent Insights](sql-database-intelligent-insights-use-diagnostics-log.md).

## <a name="detection-metrics"></a>Wykrywanie metryki

Metryki używane do wykrywania modeli, które generują Intelligent Insights są oparte na temat monitorowania:

- Czas trwania zapytania
- Limit czasu żądania
- Czas oczekiwania nadmierne
- Dotyczącego żądań

Czas trwania i limit czasu żądania zapytań są używane jako podstawowe modele podczas wykrywania problemów z wydajnością obciążeń bazy danych. Służą one, ponieważ pomiaru ich bezpośrednio, co się dzieje z obciążeniem. W celu wykrycia wszystkich możliwych przypadków wydajność obciążeń degradacji nadmierne czas oczekiwania i raportuje żądania są używane jako dodatkowe modele do wskazują na problemy, które mają wpływ na wydajność obciążenia.

System automatycznie uwzględnia zmiany obciążenia i zmian w liczbie żądań zapytań dynamicznie wprowadzonych w bazie danych, aby określić progi wydajności normalnych i poza zwykłej bazy danych.

Wszystkie metryki weźmie się w różnych relacjach za pomocą modelu naukowo pochodnego danych, który kategoryzuje każdego wykrył problem z wydajnością. Informacji otrzymanych za pośrednictwem inteligentnego wglądu obejmuje:

- Szczegóły wykrył problem z wydajnością.
- Analiza głównych przyczyn wykrytego problemu.
- Zalecenia dotyczące sposobu zwiększenia wydajności monitorowanych bazy danych SQL, gdzie to możliwe.

## <a name="query-duration"></a>Czas trwania zapytania

Model spadek czasu trwania zapytań analizuje pojedynczych zapytań i wykrywa wzrost w czasie, potrzebnego do kompilowania i wykonywanie zapytania w porównaniu do linii bazowej wydajności.

Jeśli wbudowane narzędzie analizy bazy danych SQL wykryje znaczny wzrost w kompilacji zapytania lub czas wykonywania zapytania, który wpływa na wydajność obciążenia, te zapytania są oznaczane jako czas trwania zapytania problemach powodujących spadek wydajności.

Dziennik diagnostyczny Intelligent Insights generuje skrót zapytania zapytania pogorszenie wydajności. Skrót zapytania wskazuje, czy spadek wydajności był powiązany z zwiększenie kwerendy kompilacji lub czasu wykonania, która wzrosła czasu trwania zapytania.

## <a name="timeout-requests"></a>Limit czasu żądania

Limit czasu żądania degradacji modelu analizuje pojedynczych zapytań i wykrywa zwiększenie limitów czasu na poziomie wykonywania zapytań i ogólną przekroczeń limitu czasu żądania na poziomie bazy danych w porównaniu do okresu linii bazowej wydajności.

Nawet w przypadku, zanim dotrą etap wykonywania niektórych kwerend może upłynąć limit czasu. Za pomocą środków przerwane pracowników, a żądania wbudowana funkcja analizy SQL Database mierzy i analizuje wszystkie zapytania, które osiągnęły bazy danych, czy zapewnia etapowi wykonywania lub nie.

Po liczba przekroczeń limitu czasu dla wykonywanych zapytań lub liczbę procesów roboczych żądanie zostało przerwane przekracza wartość progową kontrolowany przez system, dziennik diagnostyczny jest wypełniana dzięki inteligentnej analizie.

Szczegółowych informacji generowanych zawierają liczbę żądań przekroczyła limit czasu i liczby zapytań upłynął limit czasu. Oznaczenie spadek wydajności jest powiązany z zwiększenie limitu czasu na etapie wykonywania lub znajduje się ogólny poziom bazy danych. Gdy wzrost przekroczeń limitu czasu jest uznawany za istotny dla wydajności bazy danych, te zapytania są oznaczane jako problemach powodujących spadek wydajności limitu czasu.

## <a name="excessive-wait-times"></a>Czasy oczekiwania nadmierne

Model czas oczekiwania nadmierne monitoruje zapytania poszczególnych baz danych. Wykrywa statystyki oczekiwania nietypowo dużą zapytania, które przekroczony bezwzględny wartości progowe kontrolowany przez system. Następujące metryki czas oczekiwania nadmierne zapytania są przestrzegane przy użyciu nowych funkcji programu SQL Server, statystyki oczekiwania Query Store (sys.query_store_wait_stats):

- Osiągnięcia limitów zasobów
- Osiągnięcia limitów zasobów puli elastycznej
- Zbyt wiele wątków procesów roboczych lub sesji
- Blokowanie nadmierne bazy danych
- Wykorzystanie pamięci
- Inne statystyki oczekiwania

Osiągnięcia limitów zasobów lub ograniczenia zasobów puli elastycznej oznaczają, że użycia dostępnych zasobów w ramach subskrypcji lub elastycznej puli przekroczony bezwzględny progów. Te statystyki wskazują spadek wydajności obciążeń. Zbyt wiele wątków procesów roboczych lub sesji wskazuje, że warunek, w którym liczbę wątków roboczych lub sesje zainicjowane przekroczony bezwzględny wartości progowe. Te statystyki wskazują spadek wydajności obciążeń.

Blokowanie nadmierne bazy danych wskazuje, że warunek, w którym licznik blokady w bazie danych przekroczyło bezwzględne wartości progowe. Ten stan wskazuje spadek wydajności obciążeń. Wykorzystanie jest warunek, w którym liczba wątków żądania pamięci przydziela pamięci przekroczony bezwzględny próg. Ten stan wskazuje spadek wydajności obciążeń.

Inne wykrywania statystyki oczekiwania wskazuje warunek, w którym różne metryki mierzy się za pomocą statystyki oczekiwania Query Store przekroczony bezwzględny próg. Te statystyki wskazują spadek wydajności obciążeń.

Po wykryciu czasy oczekiwania nadmierne, w zależności od dostępnych danych, Diagnostyka Intelligent Insights dziennika skróty dane wyjściowe zapytań obejmujące i dotyczy pogorszenie wydajności, szczegóły metryki powodującą, że zapytania, aby czekać na wykonanie, i czas oczekiwania mierzone.

## <a name="errored-requests"></a>Błędnych żądań

Błędnych żądań w spadku model poszczególne monitory wykonuje kwerendę i wykrywa zwiększenie liczby zapytań, że dotyczącego w porównaniu do okresu linii bazowej. Ten model monitoruje również wyjątki krytyczne przekroczony bezwzględny progi zarządza wbudowanym funkcjom analizy bazy danych SQL. System automatycznie uwzględnia liczbę żądań zapytania do bazy danych i konta na zmiany obciążenia w okresie monitorowane.

Podczas mierzonego wzrost błędnych żądań w stosunku do całkowitej liczby żądań jest uznawany za istotny dla wydajność obciążeń, objęte zapytania są oznaczane jako problemach powodujących spadek wydajności błędnych żądań.

Dziennik Intelligent Insights generuje liczba błędnych żądań. Oznacza to, czy spadek wydajności był powiązany wzrost błędnych żądań lub przekraczających próg monitorowanych wyjątek krytyczny i czasem obniżenia wydajności.

Jeśli monitorowana wyjątki krytyczne cross bezwzględne wartości progowe, zarządzane przez system, inteligentnego wglądu jest generowany ze szczegółami wyjątek krytyczny.

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się, jak [bazy danych SQL Rozwiązywanie problemów z wydajnością dzięki inteligentnej analizie](sql-database-intelligent-insights-troubleshoot-performance.md).
- Użyj [dziennik diagnostyczny wydajności bazy danych SQL Intelligent Insights](sql-database-intelligent-insights-use-diagnostics-log.md).
- Dowiedz się, jak [monitorowanie bazy danych SQL przy użyciu SQL Analytics](../azure-monitor/insights/azure-sql.md).
- Dowiedz się, jak [zbieranie i używanie dane dzienników z zasobów platformy Azure](../azure-monitor/platform/diagnostic-logs-overview.md).
