---
title: Monitorowanie i dostrajanie w usłudze Azure Database for PostgreSQL
description: W tym artykule opisano, monitorowania i dostrajania funkcji w usłudze Azure Database for PostgreSQL.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/05/2018
ms.openlocfilehash: 0d5508fe6b07bd5664bd0708960559537d8f6be1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61456227"
---
# <a name="monitor-and-tune"></a>Monitorowanie i dostrajanie
Dane dotyczące serwerów monitorowania ułatwia rozwiązywanie problemów i zoptymalizować dla obciążenia. Usługa Azure Database for PostgreSQL udostępnia różne opcje monitorowania zapewniają wgląd w zachowanie serwera.

## <a name="metrics"></a>Metryki
Usługa Azure Database for PostgreSQL udostępnia różne metryki, które zapewniają wgląd w zachowanie zasobach obsługujących serwera PostgreSQL. Wszystkie metryki jest emitowane z częstotliwością co minutę, a także zawiera maksymalnie 30 dni historii. Można skonfigurować alerty dotyczące metryk. Aby uzyskać wskazówki krok po kroku, zobacz [jak skonfigurować alerty](howto-alert-on-metric.md). Inne zadania obejmują skonfigurowanie zautomatyzowanych akcji, wykonywanie zaawansowanych analiz i archiwizowanie historii. Aby uzyskać więcej informacji, zobacz [Przegląd metryk usługi Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

### <a name="list-of-metrics"></a>Listy metryk
Te metryki są dostępne dla usługi Azure Database for PostgreSQL:

|Metryka|Nazwa wyświetlana metryki|Jednostka|Opis|
|---|---|---|---|
|cpu_percent|Procent użycia Procesora|Procent|Procent użycia Procesora w użyciu.|
|memory_percent|Procent pamięci|Procent|Procent pamięci w użyciu.|
|io_consumption_percent|% Operacji We/Wy|Procent|Procent we/wy w użyciu.|
|storage_percent|Procent użycia magazynu|Procent|Maksymalne przez wartość procentowa magazynu używane poza serwerem.|
|storage_used|Użyty magazyn|Bajty|Ilość miejsca w magazynie. Magazyn używany przez usługę mogą obejmować pliki bazy danych, dzienników transakcji i dzienniki serwera.|
|storage_limit|Limit magazynu|Bajty|Maksymalny rozmiar magazynu dla tego serwera.|
|serverlog_storage_percent|Procent magazynu dziennika serwera|Procent|Wartość procentowa używane poza magazyn dzienników serwera maksymalna serwera magazynu dziennika serwera.|
|serverlog_storage_usage|Używany Magazyn dzienników serwera|Bajty|Ilość miejsca w magazynie dziennika na serwerze.|
|serverlog_storage_limit|Limit magazynu dziennika serwera|Bajty|Przechowywanie dziennika serwera maksymalną dla tego serwera.|
|active_connections|Aktywne połączenia|Licznik|Liczba aktywnych połączeń z serwerem.|
|connections_failed|Połączenia zakończone niepowodzeniem|Licznik|Liczba połączeń z serwerem nie powiodło się.|
|network_bytes_egress|Sieć — wyjście|Bajty|Sieć się między aktywnych połączeń.|
|network_bytes_ingress|Sieć — wejście|Bajty|Sieć w różnych aktywnych połączeń.|
|backup_storage_used|Używany magazyn kopii zapasowych|Bajty|Wielkość wykorzystanego magazynu kopii zapasowych.|

## <a name="server-logs"></a>Dzienniki serwera
Można włączyć rejestrowanie na serwerze. Te dzienniki są również dostępne za pośrednictwem dzienników diagnostycznych platformy Azure w [dzienniki usługi Azure Monitor](../azure-monitor/log-query/log-query-overview.md), usługa Event Hubs i konta magazynu. Aby dowiedzieć się więcej na temat rejestrowania, odwiedź stronę [dzienniki serwera](concepts-server-logs.md) strony.

## <a name="query-store"></a>Magazyn zapytań
[Query Store](concepts-query-store.md) to funkcja publicznej wersji zapoznawczej, która śledzi zapytania wydajność w tym czasie statystyki czasu wykonywania zapytań i poczekaj zdarzenia. Ta funkcja będzie nadal występować kwerendy informacji o wydajności środowiska uruchomieniowego w systemowej bazie danych o nazwie **azure_sys** w ramach schematu query_store. Można kontrolować zbieranie i przechowywanie danych za pośrednictwem różnych konfiguracji pokrętła.

## <a name="query-performance-insight"></a>Szczegółowe informacje o wydajności zapytań
[Szczegółowe informacje o wydajności zapytań](concepts-query-performance-insight.md) działa w połączeniu z Query Store umożliwia prezentowanie wizualizacji dostępne w witrynie Azure portal. Te wykresy umożliwiają identyfikowanie zapytań klucza tego obniżenie wydajności. Query Performance Insight w publicznej wersji zapoznawczej i jest dostępny w **pomoc techniczna i rozwiązywanie problemów z** sekcji usługi Azure Database dla serwera PostgreSQL strony portalu.

## <a name="performance-recommendations"></a>Zalecenia dotyczące wydajności
[Zalecenia dotyczące wydajności](concepts-performance-recommendations.md) funkcji identyfikuje możliwości poprawy wydajność obciążenia. Publiczna wersja zapoznawcza zalecenia dotyczące wydajności zapewnia zaleceń dotyczących tworzenia nowych indeksy, które potencjalnie mogą zwiększyć wydajność obciążeń. Aby uzyskać zalecenia dotyczące indeksu, funkcja bierze pod uwagę różne cechy bazy danych, w tym jego schematu i obciążenia zgłoszonej Query Store. Po zaimplementowaniu żadnych rekomendacji wydajności, klientów należy przetestować wydajność, aby ocenić wpływ tych zmian. 

## <a name="next-steps"></a>Kolejne kroki
- Zobacz [jak skonfigurować alerty](howto-alert-on-metric.md) wskazówki dotyczące tworzenia alertu na metrykę.
- Aby uzyskać więcej informacji na temat sposobu uzyskania dostępu i eksportowanie metryk przy użyciu witryny Azure portal, interfejsu API REST lub interfejsu wiersza polecenia, zobacz [Przegląd metryk usługi Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).
- Przeczytaj nasz blog na [najlepsze rozwiązania dotyczące monitorowania serwera](https://azure.microsoft.com/blog/best-practices-for-alerting-on-metrics-with-azure-database-for-postgresql-monitoring/).
