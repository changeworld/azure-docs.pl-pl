---
title: Monitoruj i dostrajaj — usługa Azure Database for PostgreSQL — pojedynczy serwer
description: W tym artykule opisano funkcje monitorowania i dostrajania w usłudze Azure Database for PostgreSQL — Single Server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/19/2019
ms.openlocfilehash: 2d2bb5440e6d23a4cb5781244ba33ab4c5be2612
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78252581"
---
# <a name="monitor-and-tune-azure-database-for-postgresql---single-server"></a>Monitorowanie i dostrajanie bazy danych platformy Azure dla postgreSql — pojedynczy serwer
Monitorowanie danych dotyczących serwerów pomaga w rozwiązywaniu problemów i optymalizacji pod kątem obciążenia. Usługa Azure Database for PostgreSQL udostępnia różne opcje monitorowania, aby zapewnić wgląd w zachowanie serwera.

## <a name="metrics"></a>Metryki
Usługa Azure Database for PostgreSQL udostępnia różne metryki, które zapewniają wgląd w zachowanie zasobów obsługujących serwer PostgreSQL. Każda metryka jest emitowana z częstotliwością jednominutową i ma do 30 dni historii. Alerty można skonfigurować na metrykach. Aby uzyskać wskazówki krok po kroku, zobacz [Jak skonfigurować alerty](howto-alert-on-metric.md). Inne zadania obejmują konfigurowanie akcji zautomatyzowanych, wykonywanie zaawansowanych analiz i archiwizowanie historii. Aby uzyskać więcej informacji, zobacz [Omówienie metryk platformy Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

### <a name="list-of-metrics"></a>Lista danych
Te metryki są dostępne dla usługi Azure Database for PostgreSQL:

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Opis|
|---|---|---|---|
|cpu_percent|Procent procesora|Wartość procentowa|Procent procesora CPU w użyciu.|
|memory_percent|Procent pamięci|Wartość procentowa|Procent używanej pamięci.|
|io_consumption_percent|Procent IO|Wartość procentowa|Procent używanego we/wy.|
|storage_percent|Procent magazynowania|Wartość procentowa|Procent miejsca używanego z maksymalnego wartości serwera.|
|storage_used|Używany magazyn|Bajty|Ilość używanego miejsca. Magazyn używany przez usługę może zawierać pliki bazy danych, dzienniki transakcji i dzienniki serwera.|
|storage_limit|Limit miejsca do magazynowania|Bajty|Maksymalna ilość miejsca na tym serwerze.|
|serverlog_storage_percent|Procent magazynu dziennika serwera|Wartość procentowa|Procent magazynu dziennika serwera używanego z maksymalnego magazynu dziennika serwera serwera.|
|serverlog_storage_usage|Używany magazyn dzienników serwera|Bajty|Ilość używanego magazynu dziennika serwera.|
|serverlog_storage_limit|Limit magazynu dziennika serwera|Bajty|Maksymalna ilość magazynu dziennika serwera dla tego serwera.|
|active_connections|Aktywne połączenia|Liczba|Liczba aktywnych połączeń z serwerem.|
|connections_failed|Połączenia zakończone niepowodzeniem|Liczba|Liczba nieudanych połączeń z serwerem.|
|network_bytes_egress|Sieć — wyjście|Bajty|Wyjście sieciowe w aktywnych połączeniach.|
|network_bytes_ingress|Sieć — wejście|Bajty|W sieci w różnych aktywnych połączeniach.|
|backup_storage_used|Użyto magazynu kopii zapasowych|Bajty|Ilość używanego magazynu kopii zapasowej.|
|pg_replica_log_delay_in_bytes|Maksymalne opóźnienie w replikach|Bajty|Opóźnienie w bajtach między wzorcem a repliką najbardziej opóźnionych. Ta metryka jest dostępna tylko na serwerze głównym.|
|pg_replica_log_delay_in_seconds|Opóźnienie repliki|Sekundy|Czas od ostatniej powtórzona transakcja. Ta metryka jest dostępna tylko dla serwerów replik.|

## <a name="server-logs"></a>Dzienniki serwera
Można włączyć rejestrowanie na serwerze. Te dzienniki są również dostępne za pośrednictwem dzienników diagnostycznych platformy Azure w [dziennikach usługi Azure Monitor,](../azure-monitor/log-query/log-query-overview.md)centra zdarzeń i konto magazynu. Aby dowiedzieć się więcej o rejestrowaniu, odwiedź stronę [dzienników serwera.](concepts-server-logs.md)

## <a name="query-store"></a>Magazyn zapytań
[Query Store](concepts-query-store.md) śledzi wydajność zapytań w czasie, w tym statystyki środowiska uruchomieniowego kwerendy i zdarzenia oczekiwania. Funkcja będzie zachowywać informacje o wydajności środowiska uruchomieniowego kwerendy w systemowej bazie danych o nazwie **azure_sys** w schemacie query_store. Zbieranie i przechowywanie danych można kontrolować za pomocą różnych pokręteł konfiguracyjnych.

## <a name="query-performance-insight"></a>Szczegółowe informacje o wydajności zapytań
[Query Performance Insight](concepts-query-performance-insight.md) działa w połączeniu z Query Store, aby zapewnić wizualizacje dostępne z witryny Azure portal. Te wykresy umożliwiają identyfikowanie kluczowych zapytań, które mają wpływ na wydajność. Usługa Query Performance Insight jest dostępna z sekcji **Pomoc techniczna + rozwiązywanie problemów** na stronie portalu serwera PostgreSQL dla usługi PostgreSQL.

## <a name="performance-recommendations"></a>Zalecenia dotyczące wydajności
Funkcja [Zalecenia dotyczące wydajności](concepts-performance-recommendations.md) identyfikuje możliwości poprawy wydajności obciążenia. Zalecenia dotyczące wydajności zawiera zalecenia dotyczące tworzenia nowych indeksów, które mają potencjał, aby poprawić wydajność obciążeń. Aby utworzyć zalecenia indeksu, funkcja bierze pod uwagę różne cechy bazy danych, w tym jego schematu i obciążenia, zgodnie z raportami magazynu zapytań. Po wdrożeniu zalecenia dotyczące wydajności klienci powinni przetestować wydajność, aby ocenić wpływ tych zmian. 

## <a name="planned-maintenance-notification"></a>Powiadomienie o planowanej konserwacji

**Powiadomienia o planowanej konserwacji** umożliwiają otrzymywanie alertów o planowanej planowanej konserwacji w bazie danych Azure database dla postgreSql — single server. Powiadomienia te są zintegrowane z [planowanej](../service-health/overview.md) konserwacji usługi Service Health i umożliwiają wyświetlanie wszystkich zaplanowanych konserwacji subskrypcji w jednym miejscu. Pomaga również skalować powiadomienie do odpowiednich odbiorców dla różnych grup zasobów, ponieważ możesz mieć różne kontakty odpowiedzialne za różne zasoby. Otrzymasz powiadomienie o nadchodzących konserwacji 72 godziny przed wydarzeniem.

> [!Note]
> Dołożymy wszelkich starań, aby zapewnić **powiadomienie o planowanej konserwacji** z powiadomieniem o wszystkich zdarzeniach. Jednak w przypadku krytycznych lub zabezpieczeń poprawki, powiadomienia mogą być wysyłane bliżej zdarzenia lub zostać pominięte.

### <a name="to-receive-planned-maintenance-notification"></a>Aby otrzymać powiadomienie o planowanej konserwacji

1. W [portalu](https://portal.azure.com)wybierz pozycję **Kondycja usługi**.
2. W sekcji **Alerty** wybierz pozycję **Alerty dotyczące kondycji**.
3. Wybierz **+ Dodaj alert kondycji usługi** i wypełnij pola.
4. Wypełnij wymagane pola. 
5. Wybierz **typ zdarzenia,** wybierz **opcję Planowana konserwacja** lub **Wybierz wszystkie**
6. W **grupach akcji** określ, jak chcesz otrzymywać alerty (otrzymuj wiadomość e-mail, wyzwalaj aplikację logiki itp.)  
7. Upewnij się, że reguła włączania podczas tworzenia jest ustawiona na Tak.
8. Wybierz **pozycję Utwórz regułę alertu,** aby ukończyć alert

Szczegółowe instrukcje dotyczące tworzenia **alertów dotyczących kondycji usługi można**znaleźć w obszarze Tworzenie [alertów dziennika aktywności w powiadomieniach o usługach](../service-health/alerts-activity-log-service-notifications.md).

> [!IMPORTANT]
> Powiadomienia o planowanej konserwacji są obecnie w wersji zapoznawczej

## <a name="next-steps"></a>Następne kroki
- Zobacz, [jak skonfigurować alerty, aby](howto-alert-on-metric.md) uzyskać wskazówki dotyczące tworzenia alertu na metryki.
- Aby uzyskać więcej informacji na temat uzyskiwania dostępu do metryk i eksportowania ich przy użyciu witryny Azure portal, interfejsu API REST lub interfejsu wiersza polecenia, zobacz [omówienie metryk platformy Azure.](../monitoring-and-diagnostics/monitoring-overview-metrics.md)
- Przeczytaj nasz blog na temat [najlepszych praktyk monitorowania serwera.](https://azure.microsoft.com/blog/best-practices-for-alerting-on-metrics-with-azure-database-for-postgresql-monitoring/)
