---
title: Monitorowanie i dostrajanie w usłudze Azure Database for PostgreSQL — pojedynczy serwer
description: W tym artykule opisano funkcje monitorowania i dostrajania w usłudze Azure Database for PostgreSQL — pojedynczy serwer.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 5e03a3e33d618c393241a649be50fe78632caafe
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65068996"
---
# <a name="monitor-and-tune-azure-database-for-postgresql---single-server"></a>Monitorowania i dostrajania bazy danych Azure Database for PostgreSQL — pojedynczy serwer
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
|active_connections|Aktywne połączenia|Count|Liczba aktywnych połączeń z serwerem.|
|connections_failed|Połączenia zakończone niepowodzeniem|Count|Liczba połączeń z serwerem nie powiodło się.|
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
