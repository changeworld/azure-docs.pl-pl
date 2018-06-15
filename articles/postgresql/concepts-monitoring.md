---
title: Monitorowanie bazy danych platformy Azure dla PostgreSQL
description: W tym artykule opisano metryki monitorowania oraz alertów dla bazy danych Azure dla PostgreSQL, w tym statystyk Procesora, pamięci masowej i połączenia.
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: d0a57fe6d7b1040c32f6d67e2bf0259176c72099
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/28/2018
ms.locfileid: "29687612"
---
# <a name="monitoring-in-azure-database-for-postgresql"></a>Monitorowanie bazy danych platformy Azure dla PostgreSQL
Monitorowanie danych dotyczących serwerów ułatwia rozwiązywanie problemów i Optymalizuj dla obciążenia. Bazy danych platformy Azure dla PostgreSQL zapewnia różnych metryk, które zapewniają wgląd w zachowania zasobów, do obsługi serwera PostgreSQL. 

## <a name="metrics"></a>Metryki
Wszystkie metryki Azure ma częstotliwość co minutę, a wszystkie metryki zapewnia 30 dni historii. Alerty można skonfigurować na metryki. Aby uzyskać instrukcje krok po kroku, zobacz [jak skonfigurować alerty](howto-alert-on-metric.md). Inne zadania obejmują konfigurowanie akcje automatyczne, wykonywanie zaawansowane metody analizy i archiwizowania historii. Aby uzyskać więcej informacji, zobacz [omówienie metryk usługi Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

### <a name="list-of-metrics"></a>Lista metryk
Te metryki są dostępne dla bazy danych Azure dla PostgreSQL:

|Metryka|Nazwa wyświetlana metryki|Jednostka|Opis|
|---|---|---|---|---|
|cpu_percent|Procent procesora CPU|Procent|Procent procesora CPU w użyciu.|
|memory_percent|Procent pamięci|Procent|Procent użycia pamięci.|
|io_consumption_percent|Procent we/wy|Procent|Procent we/wy w użyciu.|
|storage_percent|Procent użycia magazynu|Procent|Maksymalne przez procent magazynu używane poza serwera.|
|storage_used|Użyty magazyn|Bajty|Ilość pamięci masowej w użyciu. Magazyn używany przez usługę zawiera pliki bazy danych, dzienników transakcji i dzienniki serwera.|
|storage_limit|Limit magazynu|Bajty|Maksymalna przestrzeń magazynowa dla tego serwera.|
|active_connections|Całkowita liczba aktywnych połączeń|Licznik|Liczba aktywnych połączeń z serwerem.|
|connections_failed|Całkowita liczba połączeń nie powiodło się|Licznik|Liczba połączeń z serwerem nie powiodło się.|


## <a name="next-steps"></a>Kolejne kroki
- Zobacz [jak skonfigurować alerty](howto-alert-on-metric.md) wskazówki dotyczące tworzenia alertu na metryki.
- Aby uzyskać więcej informacji na temat dostępu i wyeksportować metryki przy użyciu portalu Azure, interfejsu API REST lub interfejsu wiersza polecenia, zobacz [omówienie metryk usługi Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).
