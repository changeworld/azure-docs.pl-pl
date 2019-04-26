---
title: Monitorowanie usługi Azure Database for MySQL
description: W tym artykule opisano metryki dotyczące monitorowania i alertów dla usługi Azure Database for MySQL, w tym statystyk procesora CPU, Magazyn i połączenia.
author: rachel-msft
ms.author: raagyema
ms.service: mysql
ms.topic: conceptual
ms.date: 11/05/2018
ms.openlocfilehash: 9dcb79e7f4ebd43da3f6c6fd35fa0707898d7ec8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60525568"
---
# <a name="monitoring-in-azure-database-for-mysql"></a>Monitorowanie usługi Azure Database for MySQL
Dane dotyczące serwerów monitorowania ułatwia rozwiązywanie problemów i zoptymalizować dla obciążenia. Usługa Azure Database for MySQL zapewnia różne metryki, które zapewniają wgląd w zachowania serwera.

## <a name="metrics"></a>Metryki
Wszystkie metryki usługi Azure mieć częstotliwość co minutę, a wszystkie metryki zapewnia 30 dni historii. Można skonfigurować alerty dotyczące metryk. Aby uzyskać wskazówki krok po kroku, zobacz [jak skonfigurować alerty](howto-alert-on-metric.md). Inne zadania obejmują skonfigurowanie zautomatyzowanych akcji, wykonywanie zaawansowanych analiz i archiwizowanie historii. Aby uzyskać więcej informacji, zobacz [Przegląd metryk usługi Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

### <a name="list-of-metrics"></a>Listy metryk
Te metryki są dostępne dla usługi Azure Database for MySQL:

|Metryka|Nazwa wyświetlana metryki|Jednostka|Opis|
|---|---|---|---|
|cpu_percent|Procent użycia Procesora|Procent|Procent użycia Procesora w użyciu.|
|memory_percent|Procent pamięci|Procent|Procent pamięci w użyciu.|
|io_consumption_percent|% Operacji We/Wy|Procent|Procent we/wy w użyciu.|
|storage_percent|Procent użycia magazynu|Procent|Maksymalne przez wartość procentowa magazynu używane poza serwerem.|
|storage_used|Użyty magazyn|Bajty|Ilość miejsca w magazynie. Magazyn używany przez usługę mogą obejmować pliki bazy danych, dzienników transakcji i dzienniki serwera.|
|serverlog_storage_percent|Procent magazynu dziennika serwera|Procent|Wartość procentowa używane poza magazyn dzienników serwera maksymalna serwera magazynu dziennika serwera.|
|serverlog_storage_usage|Używany Magazyn dzienników serwera|Bajty|Ilość miejsca w magazynie dziennika na serwerze.|
|serverlog_storage_limit|Limit magazynu dziennika serwera|Bajty|Przechowywanie dziennika serwera maksymalną dla tego serwera.|
|storage_limit|Limit magazynu|Bajty|Maksymalny rozmiar magazynu dla tego serwera.|
|active_connections|Aktywne połączenia|Licznik|Liczba aktywnych połączeń z serwerem.|
|connections_failed|Połączenia zakończone niepowodzeniem|Licznik|Liczba połączeń z serwerem nie powiodło się.|
|seconds_behind_master|Opóźnienie replikacji w ciągu kilku sekund|Licznik|Liczba sekund, przez serwer repliki jest opóźnione względem serwera głównego.|
|network_bytes_egress|Sieć — wyjście|Bajty|Sieć się między aktywnych połączeń.|
|network_bytes_ingress|Sieć — wejście|Bajty|Sieć w różnych aktywnych połączeń.|
|backup_storage_used|Używany magazyn kopii zapasowych|Bajty|Wielkość wykorzystanego magazynu kopii zapasowych.|

## <a name="server-logs"></a>Dzienniki serwera
Można włączyć wolnych zapytań rejestrowania na serwerze. Te dzienniki są również dostępne dzienniki diagnostyczne platformy Azure w dzienniki usługi Azure Monitor, usługa Event Hubs i konta magazynu. Aby dowiedzieć się więcej na temat rejestrowania, odwiedź stronę [dzienniki serwera](concepts-server-logs.md) strony.

## <a name="next-steps"></a>Kolejne kroki
- Zobacz [jak skonfigurować alerty](howto-alert-on-metric.md) wskazówki dotyczące tworzenia alertu na metrykę.
- Aby uzyskać więcej informacji na temat sposobu uzyskania dostępu i eksportowanie metryk przy użyciu witryny Azure portal, interfejsu API REST lub interfejsu wiersza polecenia, zobacz [Przegląd metryk usługi Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).
- Przeczytaj nasz blog na [najlepsze rozwiązania dotyczące monitorowania serwera](https://azure.microsoft.com/blog/best-practices-for-alerting-on-metrics-with-azure-database-for-mysql-monitoring/).
