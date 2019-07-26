---
title: Monitorowanie w Azure Database for MySQL
description: W tym artykule opisano metryki dotyczące monitorowania i generowania alertów dla Azure Database for MySQL, w tym danych statystycznych dotyczących procesora, magazynu i połączeń.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 06/05/2019
ms.openlocfilehash: 0122f952e586d0535fc2e482c7b78266f8809272
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/25/2019
ms.locfileid: "67062441"
---
# <a name="monitoring-in-azure-database-for-mysql"></a>Monitorowanie w Azure Database for MySQL
Monitorowanie danych dotyczących serwerów ułatwia rozwiązywanie problemów i optymalizację w obciążeniu. Azure Database for MySQL oferuje różne metryki, które dają wgląd w zachowanie serwera.

## <a name="metrics"></a>Metryki
Wszystkie metryki platformy Azure mają częstotliwość jednominutową, a każda Metryka zapewnia 30-dniową historię. Można skonfigurować alerty dotyczące metryk. Aby uzyskać wskazówki krok po kroku, zobacz [jak skonfigurować alerty](howto-alert-on-metric.md). Inne zadania obejmują Konfigurowanie zautomatyzowanych akcji, wykonywanie zaawansowanych analiz i archiwizowanie historii. Aby uzyskać więcej informacji, zobacz [Omówienie usługi Azure Metrics](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

### <a name="list-of-metrics"></a>Lista metryk
Te metryki są dostępne dla Azure Database for MySQL:

|Metryka|Nazwa wyświetlana metryki|Jednostka|Opis|
|---|---|---|---|
|cpu_percent|Procent użycia procesora CPU|Percent|Procent użycia procesora CPU.|
|memory_percent|Procent pamięci|Percent|Procent używanej pamięci.|
|io_consumption_percent|Procent operacji we/wy|Percent|Procent operacji we/wy w użyciu.|
|storage_percent|Procent miejsca do magazynowania|Percent|Wartość procentowa używanej przestrzeni dyskowej poza maksymalną.|
|storage_used|Używane miejsce|Bajty|Ilość używanej pamięci masowej. Magazyn używany przez usługę może obejmować pliki bazy danych, dzienniki transakcji i Dzienniki serwera.|
|serverlog_storage_percent|Procent magazynu dzienników serwera|Percent|Procent magazynu dzienników serwera używany poza maksymalnym magazynem dzienników serwera.|
|serverlog_storage_usage|Używany magazyn dzienników serwera|Bajty|Ilość używanego magazynu dzienników serwera.|
|serverlog_storage_limit|Limit magazynowania dziennika serwera|Bajty|Maksymalny magazyn dzienników serwera dla tego serwera.|
|storage_limit|Limit magazynu|Bajty|Maksymalny magazyn dla tego serwera.|
|active_connections|Aktywne połączenia|Count|Liczba aktywnych połączeń z serwerem.|
|connections_failed|Połączenia zakończone niepowodzeniem|Count|Liczba nieudanych połączeń z serwerem.|
|seconds_behind_master|Opóźnienie replikacji w sekundach|Count|Liczba sekund, przez jaką serwer repliki jest opóźniony względem serwera głównego.|
|network_bytes_egress|Sieć — wyjście|Bajty|Nawiązywanie połączeń sieciowych między aktywnymi połączeniami.|
|network_bytes_ingress|Sieć — wejście|Bajty|Sieć w ramach aktywnych połączeń.|
|backup_storage_used|Używany magazyn kopii zapasowych|Bajty|Ilość używanego magazynu kopii zapasowych.|

## <a name="server-logs"></a>Dzienniki serwera
Na serwerze można włączyć opcję wolnego zapytania i rejestrowania inspekcji. Te dzienniki są również dostępne za pomocą dzienników diagnostycznych platformy Azure w Azure Monitor dziennikach, Event Hubs i koncie magazynu. Aby dowiedzieć się więcej o rejestrowaniu, odwiedź artykuły [dzienniki inspekcji](concepts-audit-logs.md) i [dzienniki wolnych zapytań](concepts-server-logs.md) .

## <a name="query-store"></a>Magazyn zapytań
[Magazyn zapytań](concepts-query-store.md) jest publiczną funkcją w wersji zapoznawczej, która śledzi wydajność zapytań w miarę upływu czasu, w tym statystyk środowiska uruchomieniowego zapytań i zdarzeń oczekiwania. Ta funkcja utrzymuje informacje o wydajności środowiska uruchomieniowego zapytań w schemacie **MySQL** . Można kontrolować zbieranie i przechowywanie danych za pośrednictwem różnych pokręteł konfiguracyjnych.

## <a name="query-performance-insight"></a>Szczegółowe informacje o wydajności zapytań
[Szczegółowe informacje o wydajności zapytań](concepts-query-performance-insight.md) działa w połączeniu z magazynem zapytań, aby udostępnić wizualizacje dostępne z Azure Portal. Te wykresy umożliwiają identyfikowanie kluczowych zapytań, które mają wpływ na wydajność. Szczegółowe informacje o wydajności zapytań znajduje się w publicznej wersji zapoznawczej i jest dostępna w sekcji **Intelligent Performance** na stronie portalu Azure Database for MySQL Server.

## <a name="performance-recommendations"></a>Rekomendacje dotyczące wydajności
Funkcja [zalecenia dotyczące wydajności](concepts-performance-recommendations.md) umożliwia zidentyfikowanie możliwości zwiększania wydajności obciążeń. Publiczna wersja zapoznawcza zaleceń dotyczących wydajności zawiera zalecenia dotyczące tworzenia nowych indeksów, które mają możliwość zwiększenia wydajności obciążeń. Aby utworzyć zalecenia dotyczące indeksów, funkcja uwzględnia różne cechy bazy danych, w tym jej schemat i obciążenie zgłoszone przez magazyn zapytań. Po wdrożeniu wszelkich zaleceń dotyczących wydajności klienci powinni przetestować wydajność, aby oszacować wpływ tych zmian.

## <a name="next-steps"></a>Następne kroki
- Zobacz [jak skonfigurować alerty](howto-alert-on-metric.md) , aby uzyskać wskazówki dotyczące tworzenia alertu dotyczącego metryki.
- Aby uzyskać więcej informacji na temat uzyskiwania dostępu do metryk i eksportowania ich przy użyciu Azure Portal, interfejsu API REST, lub interfejsu wiersza polecenia, zobacz [Omówienie usługi Azure Metrics](../monitoring-and-diagnostics/monitoring-overview-metrics.md).
- Przeczytaj nasz blog, aby zapoznać się z [najlepszymi rozwiązaniami dotyczącymi monitorowania serwera](https://azure.microsoft.com/blog/best-practices-for-alerting-on-metrics-with-azure-database-for-mysql-monitoring/).
