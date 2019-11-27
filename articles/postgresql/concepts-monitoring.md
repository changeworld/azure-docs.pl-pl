---
title: Monitorowanie i dostrajanie w Azure Database for PostgreSQL-pojedynczym serwerze
description: W tym artykule opisano funkcje monitorowania i dostrajania w Azure Database for PostgreSQL-pojedynczym serwerze.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/19/2019
ms.openlocfilehash: 799b2b6ee6074472a951e69dec7e9a87056373b4
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74384019"
---
# <a name="monitor-and-tune-azure-database-for-postgresql---single-server"></a>Monitorowanie i dostrajanie Azure Database for PostgreSQL-pojedynczego serwera
Monitorowanie danych dotyczących serwerów ułatwia rozwiązywanie problemów i optymalizację w obciążeniu. Azure Database for PostgreSQL oferuje różne opcje monitorowania w celu zapewnienia wglądu w zachowanie serwera programu.

## <a name="metrics"></a>Metryki
Azure Database for PostgreSQL oferuje różne metryki, które dają wgląd w zachowanie zasobów obsługujących serwer PostgreSQL. Każda Metryka jest emitowana z częstotliwością jednej minuty i ma do 30 dni historii. Można skonfigurować alerty dotyczące metryk. Aby uzyskać wskazówki krok po kroku, zobacz [jak skonfigurować alerty](howto-alert-on-metric.md). Inne zadania obejmują Konfigurowanie zautomatyzowanych akcji, wykonywanie zaawansowanych analiz i archiwizowanie historii. Aby uzyskać więcej informacji, zobacz [Omówienie usługi Azure Metrics](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

### <a name="list-of-metrics"></a>Lista metryk
Te metryki są dostępne dla Azure Database for PostgreSQL:

|Metryka|Nazwa wyświetlana metryki|Jednostka|Opis|
|---|---|---|---|
|cpu_percent|Procent użycia procesora CPU|Procent|Procent użycia procesora CPU.|
|memory_percent|Procent pamięci|Procent|Procent używanej pamięci.|
|io_consumption_percent|Procent operacji we/wy|Procent|Procent operacji we/wy w użyciu.|
|storage_percent|Procent miejsca do magazynowania|Procent|Wartość procentowa używanej przestrzeni dyskowej poza maksymalną.|
|storage_used|Użyty magazyn|Bajty|Ilość używanej pamięci masowej. Magazyn używany przez usługę może obejmować pliki bazy danych, dzienniki transakcji i Dzienniki serwera.|
|storage_limit|Limit magazynu|Bajty|Maksymalny magazyn dla tego serwera.|
|serverlog_storage_percent|Procent magazynu dzienników serwera|Procent|Procent magazynu dzienników serwera używany poza maksymalnym magazynem dzienników serwera.|
|serverlog_storage_usage|Używany magazyn dzienników serwera|Bajty|Ilość używanego magazynu dzienników serwera.|
|serverlog_storage_limit|Limit magazynowania dziennika serwera|Bajty|Maksymalny magazyn dzienników serwera dla tego serwera.|
|active_connections|Aktywne połączenia|Liczba|Liczba aktywnych połączeń z serwerem.|
|connections_failed|Połączenia zakończone niepowodzeniem|Liczba|Liczba nieudanych połączeń z serwerem.|
|network_bytes_egress|Sieć — wyjście|Bajty|Nawiązywanie połączeń sieciowych między aktywnymi połączeniami.|
|network_bytes_ingress|Sieć — wejście|Bajty|Sieć w ramach aktywnych połączeń.|
|backup_storage_used|Używany magazyn kopii zapasowych|Bajty|Ilość używanego magazynu kopii zapasowych.|
|pg_replica_log_delay_in_bytes|Maksymalne opóźnienie między replikami|Bajty|Zwłoka w bajtach między wzorcem i najbardziej opóźnioną repliką. Ta Metryka jest dostępna tylko na serwerze głównym.|
|pg_replica_log_delay_in_seconds|Zwłoka repliki|Sekundy|Czas od ostatniej odtworzonej transakcji. Ta Metryka jest dostępna tylko dla serwerów repliki.|

## <a name="server-logs"></a>Dzienniki serwera
Możesz włączyć rejestrowanie na serwerze. Te dzienniki są również dostępne za pomocą dzienników diagnostycznych platformy Azure w [Azure monitor dziennikach](../azure-monitor/log-query/log-query-overview.md), Event Hubs i koncie magazynu. Aby dowiedzieć się więcej o rejestrowaniu, odwiedź stronę [Dzienniki serwera](concepts-server-logs.md) .

## <a name="query-store"></a>Magazyn zapytań
[Magazyn zapytań](concepts-query-store.md) śledzi wydajność zapytań w miarę upływu czasu, w tym statystyki środowiska uruchomieniowego zapytań i zdarzenia oczekiwania. Ta funkcja utrzymuje informacje o wydajności środowiska uruchomieniowego zapytań w systemowej bazie danych o nazwie **azure_sys** pod schematem query_store. Można kontrolować zbieranie i przechowywanie danych za pośrednictwem różnych pokręteł konfiguracyjnych.

## <a name="query-performance-insight"></a>Szczegółowe informacje o wydajności zapytań
[Szczegółowe informacje o wydajności zapytań](concepts-query-performance-insight.md) działa w połączeniu z magazynem zapytań, aby udostępnić wizualizacje dostępne z Azure Portal. Te wykresy umożliwiają identyfikowanie kluczowych zapytań, które mają wpływ na wydajność. Zajrzyj do szczegółowego wglądu w informacje o wydajności w sekcji **Pomoc techniczna i rozwiązywanie problemów** na stronie portalu serwera Azure Database for PostgreSQL.

## <a name="performance-recommendations"></a>Zalecenia dotyczące wydajności
Funkcja [zalecenia dotyczące wydajności](concepts-performance-recommendations.md) umożliwia zidentyfikowanie możliwości zwiększania wydajności obciążeń. Zalecenia dotyczące wydajności zapewniają zalecenia dotyczące tworzenia nowych indeksów, które mają możliwość zwiększenia wydajności obciążeń. Aby utworzyć zalecenia dotyczące indeksów, funkcja uwzględnia różne cechy bazy danych, w tym jej schemat i obciążenie zgłoszone przez magazyn zapytań. Po wdrożeniu wszelkich zaleceń dotyczących wydajności klienci powinni przetestować wydajność, aby oszacować wpływ tych zmian. 

## <a name="service-health"></a>Kondycja usługi
[Usługa Azure Service Health](../service-health/overview.md) udostępnia widok wszystkich powiadomień o kondycji usługi w ramach subskrypcji. Można skonfigurować alerty Service Health, aby powiadomienia użytkownika za pośrednictwem preferowanych kanałów komunikacyjnych w przypadku problemów lub zmian, które mogą mieć wpływ na używane usługi i regiony platformy Azure.

Zdarzenia zaplanowanej konserwacji dla Azure Database for PostgreSQL-pojedynczego serwera można wyświetlić przy użyciu **zaplanowanego** typu zdarzenia konserwacji. Aby dowiedzieć się, jak utworzyć **alerty dotyczące kondycji usługi**, odwiedź artykuł [tworzenie alertów dziennika aktywności w ramach powiadomień dotyczących usług](../service-health/alerts-activity-log-service-notifications.md) .

> [!IMPORTANT]
> Powiadomienia o planowanej konserwacji są dostępne tylko w wersji zapoznawczej dla regionu Wschodnie stany USA i tylko Południowe Zjednoczone Królestwo.

## <a name="next-steps"></a>Następne kroki
- Zobacz [jak skonfigurować alerty](howto-alert-on-metric.md) , aby uzyskać wskazówki dotyczące tworzenia alertu dotyczącego metryki.
- Aby uzyskać więcej informacji na temat uzyskiwania dostępu do metryk i eksportowania ich przy użyciu Azure Portal, interfejsu API REST, lub interfejsu wiersza polecenia, zobacz [Omówienie usługi Azure Metrics](../monitoring-and-diagnostics/monitoring-overview-metrics.md).
- Przeczytaj nasz blog, aby zapoznać się z [najlepszymi rozwiązaniami dotyczącymi monitorowania serwera](https://azure.microsoft.com/blog/best-practices-for-alerting-on-metrics-with-azure-database-for-postgresql-monitoring/).
