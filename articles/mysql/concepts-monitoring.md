---
title: Monitorowanie — Azure Database for MySQL
description: W tym artykule opisano metryki dotyczące monitorowania i generowania alertów dla Azure Database for MySQL, w tym danych statystycznych dotyczących procesora, magazynu i połączeń.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: ec99db9406c5c83cdcbf322c45cea40c6643ee8f
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74770887"
---
# <a name="monitoring-in-azure-database-for-mysql"></a>Monitorowanie w Azure Database for MySQL
Monitorowanie danych dotyczących serwerów ułatwia rozwiązywanie problemów i optymalizację w obciążeniu. Azure Database for MySQL oferuje różne metryki, które dają wgląd w zachowanie serwera.

## <a name="metrics"></a>Metryki
Wszystkie metryki platformy Azure mają częstotliwość jednominutową, a każda Metryka zapewnia 30-dniową historię. Można skonfigurować alerty dotyczące metryk. Aby uzyskać wskazówki krok po kroku, zobacz [jak skonfigurować alerty](howto-alert-on-metric.md). Inne zadania obejmują Konfigurowanie zautomatyzowanych akcji, wykonywanie zaawansowanych analiz i archiwizowanie historii. Aby uzyskać więcej informacji, zobacz [Omówienie usługi Azure Metrics](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

### <a name="list-of-metrics"></a>Lista metryk
Te metryki są dostępne dla Azure Database for MySQL:

|Metryka|Nazwa wyświetlana metryki|Jednostka|Opis|
|---|---|---|---|
|cpu_percent|Procent użycia procesora CPU|Procent|Procent użycia procesora CPU.|
|memory_percent|Procent pamięci|Procent|Procent używanej pamięci.|
|io_consumption_percent|Procent operacji we/wy|Procent|Procent operacji we/wy w użyciu.|
|storage_percent|Procent miejsca do magazynowania|Procent|Wartość procentowa używanej przestrzeni dyskowej poza maksymalną.|
|storage_used|Użyty magazyn|Szybkość|Ilość używanej pamięci masowej. Magazyn używany przez usługę może obejmować pliki bazy danych, dzienniki transakcji i Dzienniki serwera.|
|serverlog_storage_percent|Procent magazynu dzienników serwera|Procent|Procent magazynu dzienników serwera używany poza maksymalnym magazynem dzienników serwera.|
|serverlog_storage_usage|Używany magazyn dzienników serwera|Szybkość|Ilość używanego magazynu dzienników serwera.|
|serverlog_storage_limit|Limit magazynowania dziennika serwera|Szybkość|Maksymalny magazyn dzienników serwera dla tego serwera.|
|storage_limit|Limit magazynu|Szybkość|Maksymalny magazyn dla tego serwera.|
|active_connections|Aktywne połączenia|Liczba|Liczba aktywnych połączeń z serwerem.|
|connections_failed|Połączenia zakończone niepowodzeniem|Liczba|Liczba nieudanych połączeń z serwerem.|
|seconds_behind_master|Opóźnienie replikacji w sekundach|Liczba|Liczba sekund, przez jaką serwer repliki jest opóźniony względem serwera głównego.|
|network_bytes_egress|Sieć — wyjście|Szybkość|Nawiązywanie połączeń sieciowych między aktywnymi połączeniami.|
|network_bytes_ingress|Sieć — wejście|Szybkość|Sieć w ramach aktywnych połączeń.|
|backup_storage_used|Używany magazyn kopii zapasowych|Szybkość|Ilość używanego magazynu kopii zapasowych.|

## <a name="server-logs"></a>Dzienniki serwera
Na serwerze można włączyć opcję wolnego zapytania i rejestrowania inspekcji. Te dzienniki są również dostępne za pomocą dzienników diagnostycznych platformy Azure w Azure Monitor dziennikach, Event Hubs i koncie magazynu. Aby dowiedzieć się więcej o rejestrowaniu, odwiedź artykuły [dzienniki inspekcji](concepts-audit-logs.md) i [dzienniki wolnych zapytań](concepts-server-logs.md) .

## <a name="query-store"></a>Magazyn zapytań
[Magazyn zapytań](concepts-query-store.md) to funkcja, która śledzi wydajność zapytań w miarę upływu czasu, w tym statystyki środowiska uruchomieniowego zapytań i zdarzenia oczekiwania. Ta funkcja utrzymuje informacje o wydajności środowiska uruchomieniowego zapytań w schemacie **MySQL** . Można kontrolować zbieranie i przechowywanie danych za pośrednictwem różnych pokręteł konfiguracyjnych.

## <a name="query-performance-insight"></a>Szczegółowe informacje o wydajności zapytań
[Szczegółowe informacje o wydajności zapytań](concepts-query-performance-insight.md) działa w połączeniu z magazynem zapytań, aby udostępnić wizualizacje dostępne z Azure Portal. Te wykresy umożliwiają identyfikowanie kluczowych zapytań, które mają wpływ na wydajność. Szczegółowe informacje o wydajności zapytań jest dostępny w sekcji **Intelligent Performance** na stronie portalu Azure Database for MySQL Server.

## <a name="performance-recommendations"></a>Zalecenia dotyczące wydajności
Funkcja [zalecenia dotyczące wydajności](concepts-performance-recommendations.md) umożliwia zidentyfikowanie możliwości zwiększania wydajności obciążeń. Zalecenia dotyczące wydajności zapewniają zalecenia dotyczące tworzenia nowych indeksów, które mają możliwość zwiększenia wydajności obciążeń. Aby utworzyć zalecenia dotyczące indeksów, funkcja uwzględnia różne cechy bazy danych, w tym jej schemat i obciążenie zgłoszone przez magazyn zapytań. Po wdrożeniu wszelkich zaleceń dotyczących wydajności klienci powinni przetestować wydajność, aby oszacować wpływ tych zmian.

## <a name="service-health"></a>Kondycja usługi
[Usługa Azure Service Health](../service-health/overview.md) udostępnia widok wszystkich powiadomień o kondycji usługi w ramach subskrypcji. Można skonfigurować alerty Service Health, aby powiadomienia użytkownika za pośrednictwem preferowanych kanałów komunikacyjnych w przypadku problemów lub zmian, które mogą mieć wpływ na używane usługi i regiony platformy Azure.

Zdarzenia zaplanowanej konserwacji dla Azure Database for MySQL można wyświetlić za pomocą typu zdarzenia **planowanej konserwacji** . Aby dowiedzieć się, jak utworzyć **alerty dotyczące kondycji usługi**, odwiedź artykuł [tworzenie alertów dziennika aktywności w ramach powiadomień dotyczących usług](../service-health/alerts-activity-log-service-notifications.md) .

> [!IMPORTANT]
> Powiadomienia o planowanej konserwacji są dostępne tylko w wersji zapoznawczej dla regionu Wschodnie stany USA i tylko Południowe Zjednoczone Królestwo.

## <a name="next-steps"></a>Następne kroki
- Zobacz [jak skonfigurować alerty](howto-alert-on-metric.md) , aby uzyskać wskazówki dotyczące tworzenia alertu dotyczącego metryki.
- Aby uzyskać więcej informacji na temat uzyskiwania dostępu do metryk i eksportowania ich przy użyciu Azure Portal, interfejsu API REST, lub interfejsu wiersza polecenia, zobacz [Omówienie usługi Azure Metrics](../monitoring-and-diagnostics/monitoring-overview-metrics.md).
- Przeczytaj nasz blog, aby zapoznać się z [najlepszymi rozwiązaniami dotyczącymi monitorowania serwera](https://azure.microsoft.com/blog/best-practices-for-alerting-on-metrics-with-azure-database-for-mysql-monitoring/).
