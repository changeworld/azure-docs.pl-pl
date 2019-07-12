---
title: Monitorowanie usługi Azure Database dla serwera MariaDB
description: W tym artykule opisano metryki dotyczące monitorowania i alertów dla usługi Azure Database dla serwera MariaDB, w tym statystyk procesora CPU, Magazyn i połączenia.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 06/12/2019
ms.openlocfilehash: fb998edffed290bb7bc59945163f0fd48c55cbf5
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/07/2019
ms.locfileid: "67612528"
---
# <a name="monitoring-in-azure-database-for-mariadb"></a>Monitorowanie usługi Azure Database dla serwera MariaDB
Dane dotyczące serwerów monitorowania ułatwia rozwiązywanie problemów i zoptymalizować dla obciążenia. Usługa Azure Database dla serwera MariaDB oferuje różne metryki, które zapewniają wgląd w zachowania serwera.

## <a name="metrics"></a>Metryki
Wszystkie metryki usługi Azure mieć częstotliwość co minutę, a wszystkie metryki zapewnia 30 dni historii. Można skonfigurować alerty dotyczące metryk. Inne zadania obejmują skonfigurowanie zautomatyzowanych akcji, wykonywanie zaawansowanych analiz i archiwizowanie historii. Aby uzyskać więcej informacji, zobacz [Przegląd metryk usługi Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

Aby uzyskać wskazówki krok po kroku, zobacz [jak skonfigurować alerty](howto-alert-metric.md).

### <a name="list-of-metrics"></a>Listy metryk
Te metryki są dostępne dla usługi Azure Database dla serwera MariaDB:

|Metryka|Nazwa wyświetlana metryki|Jednostka|Opis|
|---|---|---|---|
|cpu_percent|Procent użycia Procesora|Percent|Procent użycia Procesora w użyciu.|
|memory_percent|Procent pamięci|Percent|Procent pamięci w użyciu.|
|io_consumption_percent|% Operacji We/Wy|Percent|Procent we/wy w użyciu.|
|storage_percent|Procent użycia magazynu|Percent|Maksymalne przez wartość procentowa magazynu używane poza serwerem.|
|storage_used|Użyty magazyn|Bajty|Ilość miejsca w magazynie. Magazyn używany przez usługę mogą obejmować pliki bazy danych, dzienników transakcji i dzienniki serwera.|
|serverlog_storage_percent|Procent magazynu dziennika serwera|Percent|Wartość procentowa używane poza magazyn dzienników serwera maksymalna serwera magazynu dziennika serwera.|
|serverlog_storage_usage|Używany Magazyn dzienników serwera|Bajty|Ilość miejsca w magazynie dziennika na serwerze.|
|serverlog_storage_limit|Limit magazynu dziennika serwera|Bajty|Przechowywanie dziennika serwera maksymalną dla tego serwera.|
|storage_limit|Limit magazynu|Bajty|Maksymalny rozmiar magazynu dla tego serwera.|
|active_connections|Aktywne połączenia|Count|Liczba aktywnych połączeń z serwerem.|
|connections_failed|Połączenia zakończone niepowodzeniem|Count|Liczba połączeń z serwerem nie powiodło się.|
|network_bytes_egress|Sieć — wyjście|Bajty|Sieć się między aktywnych połączeń.|
|network_bytes_ingress|Sieć — wejście|Bajty|Sieć w różnych aktywnych połączeń.|

## <a name="server-logs"></a>Dzienniki serwera

Można włączyć wolnych zapytań rejestrowania na serwerze. Te dzienniki są również dostępne dzienniki diagnostyczne platformy Azure w dzienniki usługi Azure Monitor, usługa Event Hubs i konta magazynu. Aby dowiedzieć się więcej na temat rejestrowania, odwiedź stronę [dzienniki serwera](concepts-server-logs.md) strony.

## <a name="query-store"></a>Magazyn zapytań

[Query Store](concepts-query-store.md) to funkcja publicznej wersji zapoznawczej, która śledzi zapytania wydajność w tym czasie statystyki czasu wykonywania zapytań i poczekaj zdarzenia. Ta funkcja będzie nadal występować kwerendy informacji o wydajności środowiska uruchomieniowego w **mysql** schematu. Można kontrolować zbieranie i przechowywanie danych za pośrednictwem różnych konfiguracji pokrętła.

## <a name="query-performance-insight"></a>Szczegółowe informacje o wydajności zapytań

[Szczegółowe informacje o wydajności zapytań](concepts-query-performance-insight.md) działa w połączeniu z Query Store umożliwia prezentowanie wizualizacji dostępne w witrynie Azure portal. Te wykresy umożliwiają identyfikowanie zapytań klucza tego obniżenie wydajności. Query Performance Insight w publicznej wersji zapoznawczej i jest dostępny w **inteligentne wydajności** sekcji usługi Azure Database dla serwera MariaDB strony portalu.

## <a name="performance-recommendations"></a>Zalecenia dotyczące wydajności

[Zalecenia dotyczące wydajności](concepts-performance-recommendations.md) funkcji identyfikuje możliwości poprawy wydajność obciążenia. Publiczna wersja zapoznawcza zalecenia dotyczące wydajności zapewnia zaleceń dotyczących tworzenia nowych indeksy, które potencjalnie mogą zwiększyć wydajność obciążeń. Aby uzyskać zalecenia dotyczące indeksu, funkcja bierze pod uwagę różne cechy bazy danych, w tym jego schematu i obciążenia zgłoszonej Query Store. Po zaimplementowaniu żadnych rekomendacji wydajności, klientów należy przetestować wydajność, aby ocenić wpływ tych zmian.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji na temat sposobu uzyskania dostępu i eksportowanie metryk przy użyciu witryny Azure portal, interfejsu API REST lub interfejsu wiersza polecenia, zobacz [Przegląd metryk usługi Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).
  - Zobacz [jak skonfigurować alerty](howto-alert-metric.md) wskazówki dotyczące tworzenia alertu na metrykę.
