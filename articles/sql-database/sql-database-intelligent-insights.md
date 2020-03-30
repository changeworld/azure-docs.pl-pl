---
title: Monitorowanie wydajności bazy danych za pomocą aplikacji Intelligent Insights
description: Usługa Azure SQL Database Intelligent Insights używa wbudowanej analizy do ciągłego monitorowania użycia bazy danych za pomocą sztucznej inteligencji i wykrywania zdarzeń powodujących zakłócenia, które powodują niską wydajność.
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
ms.openlocfilehash: d7b9ada17871dc7882209b7a8a449a8edcd61a94
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79214073"
---
# <a name="intelligent-insights-using-ai-to-monitor-and-troubleshoot-database-performance-preview"></a>Inteligentne statystyki za pomocą ai do monitorowania i rozwiązywania problemów z wydajnością bazy danych (Wersja zapoznawcza)

Usługa Azure SQL Database Intelligent Insights informuje o tym, co dzieje się z wydajnością bazy danych.

Usługa Intelligent Insights wykorzystuje wbudowaną inteligencję do ciągłego monitorowania użycia bazy danych za pomocą sztucznej inteligencji i wykrywania zdarzeń powodujących zakłócenia, które powodują niską wydajność. Po wykryciu przeprowadzana jest szczegółowa analiza, która generuje dziennik zasobów intelligent insights (o nazwie SQLInsights) z inteligentną oceną problemu. Ocena ta składa się z analizy głównej przyczyny problemu z wydajnością bazy danych i, w miarę możliwości, zalecenia dotyczące poprawy wydajności.

## <a name="what-can-intelligent-insights-do-for-you"></a>Co można zrobić z inteligentnymi statystykami za Ciebie

Usługa Intelligent Insights to unikatowa funkcja wbudowanej analizy platformy Azure, która zapewnia następującą wartość:

- Aktywne monitorowanie
- Dostosowane informacje o wydajności
- Wczesne wykrywanie pogorszenia wydajności bazy danych
- Analiza głównej przyczyny wykrytych problemów
- Zalecenia dotyczące poprawy wydajności
- Skalowanie w poziomie w setkach tysięcy baz danych
- Pozytywny wpływ na zasoby DevOps i całkowity koszt posiadania

## <a name="how-does-intelligent-insights-work"></a>Jak działa aplikacja Intelligent Insights

Usługa Intelligent Insights analizuje wydajność bazy danych, porównując obciążenie bazy danych z ostatniej godziny z poprzednim siedmiodniowym obciążeniem linii bazowej. Obciążenie bazy danych składa się z zapytań określanych jako najbardziej istotne dla wydajności bazy danych, takich jak najczęściej powtarzane i największe zapytania. Ponieważ każda baza danych jest unikatowa na podstawie jego struktury, danych, użycia i aplikacji, każdy plan bazowy obciążenia, który jest generowany jest specyficzne i unikatowe dla tego obciążenia. Usługa Intelligent Insights, niezależnie od planu bazowego obciążenia, monitoruje również bezwzględne progi operacyjne i wykrywa problemy z nadmiernym czasem oczekiwania, wyjątkami krytycznymi i problemami z parametryzacjią zapytań, które mogą mieć wpływ na wydajność.

Po wykryciu problemu z obniżeniem wydajności z wielu obserwowanych metryk przy użyciu sztucznej inteligencji przeprowadzana jest analiza. Dziennik diagnostyki jest generowany z inteligentnym wglądem w to, co dzieje się z bazą danych. Usługa Intelligent Insights ułatwia śledzenie problemu z wydajnością bazy danych od pierwszego pojawienia się do rozwiązania. Każdy wykryty problem jest śledzony przez jego cykl życia, od początkowego wykrywania problemów i weryfikacji poprawy wydajności do jego zakończenia.

![Przepływ pracy analizy wydajności bazy danych](./media/sql-database-intelligent-insights/intelligent-insights-concept.png)

Metryki używane do pomiaru i wykrywania problemów z wydajnością bazy danych są oparte na czasie trwania kwerendy, żądaniach limitu czasu, nadmiernym czasie oczekiwania i żądaniach błędów. Aby uzyskać więcej informacji na temat metryk, zobacz [Metryki wykrywania](#detection-metrics).

Zidentyfikowane obniżenie wydajności bazy danych SQL są rejestrowane w dzienniku SQLInsights z inteligentnymi wpisami, które składają się z następujących właściwości:

| Właściwość | Szczegóły |
| :------------------- | ------------------- |
| Informacje o bazie danych | Metadane dotyczące bazy danych, w której wykryto wgląd, takich jak identyfikator URI zasobu. |
| Obserwowany zakres czasu | Czas rozpoczęcia i zakończenia okresu wykrytego wglądu. |
| Dane, na które ma to wpływ | Metryki, które spowodowały wygenerowanie wglądu: <ul><li>Zwiększenie czasu trwania kwerendy [sekundy].</li><li>Nadmierne oczekiwanie [sekundy].</li><li>Żądania przesuwu czasu [procent].</li><li>Błędy żądań [procent].</li></ul>|
| Wartość uderzenia | Wartość zmierzonej metryki. |
| Zapytania i kody błędów, których dotyczy problem | Kod skrótu lub błędu kwerendy. Mogą one służyć do łatwego skorelowania z zapytaniami, których dotyczy problem. Metryki, które składają się z zwiększenia czasu trwania kwerendy, czas oczekiwania, liczniki limitu czasu lub kody błędów są dostarczane. |
| Wykrywania | Wykrywanie zidentyfikowane w bazie danych w czasie zdarzenia. Istnieje 15 wzorców wykrywania. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z wydajnością bazy danych za pomocą aplikacji Intelligent Insights](sql-database-intelligent-insights-troubleshoot-performance.md). |
| Analiza głównej przyczyny | Analiza głównej przyczyny problemu zidentyfikowanego w formacie czytelnym dla człowieka. Niektóre szczegółowe informacje mogą zawierać zalecenie dotyczące poprawy wydajności, jeśli to możliwe. |
|||

Aby zapoznać się z praktycznym omówieniem korzystania z usługi Intelligent Insights w usłudze Azure SQL Analytics i typowych scenariuszy użycia, zobacz ten klip wideo:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Get-Intelligent-Insights-for-Improving-Azure-SQL-Database-Performance/player]
>

Usługa Intelligent Insights wyróżnia się wykrywaniem i rozwiązywaniem problemów z wydajnością bazy danych SQL. Aby używać aplikacji Intelligent Insights do rozwiązywania problemów z wydajnością bazy danych, zobacz [Rozwiązywanie problemów z wydajnością usługi Azure SQL Database za pomocą aplikacji Intelligent Insights](sql-database-intelligent-insights-troubleshoot-performance.md).

## <a name="intelligent-insights-options"></a>Opcje inteligentnego wglądu w dane szczegółowe

Opcje inteligentnej usługi Insights dostępne w bazie danych SQL azure to:

| Opcja Inteligentna statystyka | Obsługa pojedynczej bazy danych i puli baz danych | Obsługa bazy danych wystąpień |
| :----------------------------- | ----- | ----- |
| **Konfigurowanie inteligentnej analizy —** konfigurowanie analizy inteligentnej analizy aplikacji Insights dla baz danych. | Tak | Tak |
| **Przesyłaj strumieniowo szczegółowe informacje do usługi Azure SQL Analytics** — przesyłaj szczegółowe informacje do rozwiązania do monitorowania usługi Azure SQL Analytics dla usługi Azure SQL Database. | Tak | Tak |
| **Przesyłaj strumieniowo szczegółowe informacje do Centrum zdarzeń** — przesyłaj szczegółowe informacje do centrów zdarzeń w celu uzyskania dalszych niestandardowych integracji. | Tak | Tak |
| **Przesyłaj strumieniowo szczegółowe informacje do usługi Azure Storage** — przesyłaj strumieniowo szczegółowe informacje do usługi Azure Storage w celu dalszej analizy i długoterminowego archiwizacji. | Tak | Tak |

## <a name="configure-the-export-of-the-intelligent-insights-log"></a>Konfigurowanie eksportu dziennika inteligentnej aplikacji Insights

Dane wyjściowe inteligentnej analizy można przesyłać strumieniowo do jednego z kilku miejsc docelowych do analizy:

- Dane wyjściowe przesyłane strumieniowo do obszaru roboczego usługi Log Analytics mogą być używane z [usługą Azure SQL Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-sql) do wyświetlania szczegółowych informacji za pośrednictwem interfejsu użytkownika witryny Azure portal. Jest to zintegrowane rozwiązanie platformy Azure i najbardziej typowy sposób wyświetlania szczegółowych informacji.
- Dane wyjściowe przesyłane strumieniowo do usługi Azure Event Hubs mogą być używane do opracowywania scenariuszy niestandardowego monitorowania i alertów
- Dane wyjściowe przesyłane strumieniowo do usługi Azure Storage mogą służyć do tworzenia aplikacji niestandardowych, takich jak na przykład raportowanie niestandardowe, archiwizacji danych długoterminowych i tak dalej.

Integracja usługi Azure SQL Analytics, usługi Azure Event Hub, usługi Azure Storage lub produktów innych firm do użycia jest wykonywana za pomocą pierwszego włączania rejestrowania intelligent insights (dziennika "SQLInsights") w bloku ustawienia diagnostyczne bazy danych, a następnie konfigurowanie danych dziennika intelligent insights do przesyłania strumieniowego do jednego z tych miejsc docelowych.

Aby uzyskać więcej informacji na temat włączania rejestrowania usługi Intelligent Insights oraz konfigurowania danych metryk i dzienników zasobów do przesyłania strumieniowego do produktu zużywającego się, zobacz [Rejestrowanie metryk i diagnostyki usługi Azure SQL Database.](sql-database-metrics-diag-logging.md)

### <a name="set-up-with-azure-sql-analytics"></a>Konfigurowanie za pomocą usługi Azure SQL Analytics

Rozwiązanie usługi Azure SQL Analytics zapewnia graficzny interfejs użytkownika, funkcje raportowania i alertów dotyczące wydajności bazy danych przy użyciu danych dziennika zasobów intelligent insights.

Dodawanie usługi Azure SQL Analytics do pulpitu nawigacyjnego portalu Azure z witryny marketplace i tworzenie obszaru roboczego można [znaleźć w konfigurowani usłudze Azure SQL Analytics](../azure-monitor/insights/azure-sql.md#configuration)

Aby korzystać z usługi Intelligent Insights w usłudze Azure SQL Analytics, skonfiguruj dane dziennika usługi Intelligent Insights, które mają być przesyłane strumieniowo do obszaru roboczego usługi Azure SQL Analytics utworzonego w poprzednim kroku, zobacz [Rejestrowanie metryk i diagnostyki usługi Azure SQL Database.](sql-database-metrics-diag-logging.md)

W poniższym przykładzie przedstawiono inteligentną usługę Insights wyświetlaną za pośrednictwem usługi Azure SQL Analytics:

![Raport Intelligent Insights](./media/sql-database-intelligent-insights/intelligent-insights-azure-sql-analytics.png)

### <a name="set-up-with-event-hubs"></a>Konfigurowanie za pomocą centrów zdarzeń

Aby korzystać z usługi Intelligent Insights z centrami zdarzeń, skonfiguruj dane dziennika usługi Intelligent Insights, które mają być przesyłane strumieniowo do centrów zdarzeń, zobacz [rejestrowanie metryk i diagnostyki usługi Azure azure oraz](sql-database-metrics-diag-logging.md) [dzienniki diagnostyczne platformy Azure można przesyłać strumieniowo do centrów zdarzeń.](../azure-monitor/platform/resource-logs-stream-event-hubs.md)

Aby skonfigurować niestandardowe monitorowanie i alerty za pomocą Centrum zdarzeń, zobacz [Co zrobić z metrykami i dziennikami diagnostycznymi w Centrach zdarzeń](sql-database-metrics-diag-logging.md#what-to-do-with-metrics-and-resource-logs-in-event-hubs).

### <a name="set-up-with-azure-storage"></a>Konfigurowanie usługi Azure Storage

Aby korzystać z usługi Intelligent Insights with Storage, skonfiguruj dane dziennika inteligentnej usługi Insights, które mają być przesyłane strumieniowo do magazynu, zobacz [Rejestrowanie i rejestrowanie diagnostyki usługi Azure Database usługi Azure database oraz przesyłanie strumieniowe](sql-database-metrics-diag-logging.md) [do usługi Azure Storage.](sql-database-metrics-diag-logging.md#stream-into-azure-storage)

### <a name="custom-integrations-of-intelligent-insights-log"></a>Niestandardowe integracje dziennika intelligent insights

Aby korzystać z aplikacji Intelligent Insights z narzędziami innych firm lub do tworzenia niestandardowych alertów i monitorowania, zobacz [Korzystanie z dziennika diagnostyki wydajności bazy danych Intelligent Insights](sql-database-intelligent-insights-use-diagnostics-log.md).

## <a name="detection-metrics"></a>Metryki wykrywania

Metryki używane dla modeli wykrywania, które generują inteligentne usługi Insights, są oparte na monitorowaniu:

- Czas trwania kwerendy
- Żądania limitu czasu
- Nadmierny czas oczekiwania
- Błędy żądań

Żądania czasu trwania i limitu czasu kwerendy są używane jako modele podstawowe w wykrywaniu problemów z wydajnością obciążenia bazy danych. Są one używane, ponieważ bezpośrednio mierzą, co dzieje się z obciążeniem. Aby wykryć wszystkie możliwe przypadki pogorszenia wydajności obciążenia, nadmierny czas oczekiwania i żądania błędów są używane jako dodatkowe modele, aby wskazać problemy, które wpływają na wydajność obciążenia.

System automatycznie uwzględnia zmiany w obciążeniu i zmiany liczby żądań zapytań do bazy danych, aby dynamicznie określać normalne i nietypowe progi wydajności bazy danych.

Wszystkie metryki są uwzględniane razem w różnych relacjach za pośrednictwem naukowo pochodnego modelu danych, który kategoryzuje każdy wykryty problem z wydajnością. Informacje przekazywane za pośrednictwem inteligentnego wglądu obejmują:

- Szczegóły wykrytego problemu z wydajnością.
- Analiza głównej przyczyny wykrytego problemu.
- Zalecenia dotyczące poprawy wydajności monitorowanej bazy danych SQL, jeśli to możliwe.

## <a name="query-duration"></a>Czas trwania kwerendy

Model degradacji czasu trwania kwerendy analizuje poszczególne zapytania i wykrywa wzrost czasu potrzebny do skompilowania i wykonania kwerendy w porównaniu do linii bazowej wydajności.

Jeśli wbudowana inteligencja bazy danych SQL wykryje znaczny wzrost czasu kompilacji kwerend lub wykonywania kwerend, który wpływa na wydajność obciążenia, te kwerendy są oflagowane jako problemy z obniżeniem wydajności czasu trwania kwerendy.

Dziennik diagnostyki inteligentnej analizy danych wyjściowych skrót zapytania kwerendy obniżona w wydajności. Skrót kwerendy wskazuje, czy obniżenie wydajności było związane z kompilacji kwerendy lub zwiększenie czasu wykonywania, co wydłużyło czas trwania kwerendy.

## <a name="timeout-requests"></a>Żądania limitu czasu

Model degradacji żądań limitu czasu analizuje poszczególne kwerendy i wykrywa wzrost limitów czasu na poziomie wykonywania kwerendy i ogólny limit czasu żądania na poziomie bazy danych w porównaniu z okresem planu bazowego wydajności.

Niektóre zapytania może limit czasu, nawet zanim dotrą do etapu wykonywania. Za pomocą przerwanych pracowników w porównaniu z żądaniami, wbudowana baza danych SQL mierzy i analizuje wszystkie zapytania, które dotarły do bazy danych, niezależnie od tego, czy dotarły do etapu wykonywania, czy nie.

Po liczbie limitów czasu dla wykonanych kwerend lub liczba przerwanych pracowników żądań przekracza próg zarządzany przez system, dziennik diagnostyki jest wypełniany inteligentnymi spostrzeżeniami.

Wygenerowane szczegółowe informacje zawierają liczbę żądań z limitem czasu i liczbę zapytań z limitem czasu. Wskazanie spadek wydajności jest związane ze wzrostem limitu czasu na etapie wykonywania lub ogólny poziom bazy danych jest dostarczany. Gdy wzrost limitów czasu jest uważany za istotne dla wydajności bazy danych, te zapytania są oflagowane jako problemy z obniżeniem wydajności limitu czasu.

## <a name="excessive-wait-times"></a>Nadmierny czas oczekiwania

Model nadmiernego czasu oczekiwania monitoruje zapytania poszczególnych baz danych. Wykrywa niezwykle wysokie statystyki oczekiwania na kwerendy, które przekroczyły progi bezwzględne zarządzane przez system. Następujące metryki nadmiernego czasu oczekiwania kwerendy są obserwowane przy użyciu nowej funkcji programu SQL Server, statystyki oczekiwania magazynu zapytań (sys.query_store_wait_stats):

- Osiąganie limitów zasobów
- Osiąganie limitów zasobów puli elastycznej
- Nadmierna liczba wątków roboczych lub sesji
- Nadmierne blokowanie bazy danych
- Wykorzystanie pamięci
- Inne statystyki oczekiwania

Osiągnięcie limitów zasobów lub limitów zasobów puli elastycznej oznacza, że zużycie dostępnych zasobów w ramach subskrypcji lub puli elastycznej przekroczyło progi bezwzględne. Te statystyki wskazują na obniżenie wydajności obciążenia. Nadmierna liczba wątków roboczych lub sesji oznacza warunek, w którym liczba wątków roboczych lub sesji zainicjowanych przekroczyła progi bezwzględne. Te statystyki wskazują na obniżenie wydajności obciążenia.

Nadmierne blokowanie bazy danych oznacza warunek, w którym liczba blokad w bazie danych przekroczyła progi bezwzględne. Ten statystyka wskazuje obniżenie wydajności obciążenia. Ciśnienie pamięci jest warunkiem, w którym liczba wątków żądających dotacji pamięci przekroczyła próg bezwzględny. Ten statystyka wskazuje obniżenie wydajności obciążenia.

Inne wykrywanie statystyk oczekiwania wskazuje warunek, w którym różne metryki mierzone za pośrednictwem statystyki oczekiwania magazynu zapytań przekroczyły próg bezwzględny. Te statystyki wskazują na obniżenie wydajności obciążenia.

Po wykryciu nadmiernego czasu oczekiwania, w zależności od dostępnych danych, przepływy danych diagnostycznych intelligent insights powoduje skróty zapytań mających wpływ i dotkniętych, a także szczegóły dotyczące metryk, które powodują, że kwerendy czekają w wykonaniu, oraz zmierzony czas oczekiwania.

## <a name="errored-requests"></a>Żądania błędów

Model degradacji żądań błędowych monitoruje poszczególne zapytania i wykrywa wzrost liczby kwerend, które wystąpiły z błędem w porównaniu z okresem bazowym. Ten model monitoruje również wyjątki krytyczne, które przekroczyły progi bezwzględne zarządzane przez wbudowaną inteligencję bazy danych SQL. System automatycznie uwzględnia liczbę żądań zapytań wysyłanych do bazy danych i uwzględnia wszelkie zmiany obciążenia w monitorowanym okresie.

Gdy zmierzony wzrost liczby żądań błędowych w stosunku do ogólnej liczby żądań jest uważany za istotny dla wydajności obciążenia, zapytania, których dotyczy problem, są oflagowane jako problemy z obniżeniem wydajności żądań błędów.

Dziennik inteligentnej usługi Insights wyprowadza liczbę żądań, których się nie udało. Wskazuje, czy spadek wydajności był związany ze wzrostem liczby błędnych żądań lub z przekroczeniem monitorowanego progu wyjątku krytycznego i zmierzonego czasu pogorszenia wydajności.

Jeśli którykolwiek z monitorowanych wyjątków krytycznych przekroczyć progi bezwzględne zarządzane przez system, inteligentny wgląd jest generowany z krytycznych szczegółów wyjątku.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [monitorować bazę danych SQL przy użyciu usługi SQL Analytics](../azure-monitor/insights/azure-sql.md).
- Dowiedz się, jak [rozwiązywać problemy z wydajnością bazy danych SQL za pomocą aplikacji Intelligent Insights](sql-database-intelligent-insights-troubleshoot-performance.md).
