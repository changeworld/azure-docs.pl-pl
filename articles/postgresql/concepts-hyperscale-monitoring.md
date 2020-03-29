---
title: Monitoruj i dostrajaj — Hiperskala (Citus) — usługa Azure Database for PostgreSQL
description: W tym artykule opisano funkcje monitorowania i dostrajania w usłudze Azure Database for PostgreSQL — Hiperskala (Citus)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: d2e9fcd6f6292c1da76e725e90deda4547b3682d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74975520"
---
# <a name="monitor-and-tune-azure-database-for-postgresql---hyperscale-citus"></a>Monitorowanie i dostrajanie usługi Azure Database dla postgreSQL — hiperskali (Citus)

Monitorowanie danych dotyczących serwerów pomaga w rozwiązywaniu problemów i optymalizacji pod kątem obciążenia. Hiperskala (Citus) zapewnia różne opcje monitorowania, aby zapewnić wgląd w zachowanie węzłów w grupie serwerów.

## <a name="metrics"></a>Metryki

Hiperskala (Citus) udostępnia metryki dla każdego węzła w grupie serwerów. Metryki dają wgląd w zachowanie obsługi zasobów. Każda metryka jest emitowana z częstotliwością jednominutową i ma do 30 dni historii.

Oprócz wyświetlania wykresów metryk można skonfigurować alerty. Aby uzyskać wskazówki krok po kroku, zobacz [Jak skonfigurować alerty](howto-hyperscale-alert-on-metric.md).  Inne zadania obejmują konfigurowanie akcji zautomatyzowanych, uruchamianie zaawansowanej analizy i archiwizowanie historii. Aby uzyskać więcej informacji, zobacz [Omówienie metryk platformy Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

### <a name="list-of-metrics"></a>Lista danych

Te metryki są dostępne dla węzłów hiperskali (Citus):

|Metryka|Metryka Nazwa wyświetlana|Jednostka|Opis|
|---|---|---|---|
|active_connections|Aktywne połączenia|Liczba|Liczba aktywnych połączeń z serwerem.|
|cpu_percent|Procent procesora|Wartość procentowa|Procent procesora CPU w użyciu.|
|Iops|Liczba operacji we/wy na sekundę|Liczba|Zobacz [definicji IOPS](../virtual-machines/linux/premium-storage-performance.md#iops) i [przepustowość hiperskala](concepts-hyperscale-configuration-options.md)|
|memory_percent|Procent pamięci|Wartość procentowa|Procent używanej pamięci.|
|network_bytes_ingress|Sieć — wejście|Bajty|W sieci w różnych aktywnych połączeniach.|
|network_bytes_egress|Sieć — wyjście|Bajty|Wyjście sieciowe w aktywnych połączeniach.|
|storage_percent|Procent magazynowania|Wartość procentowa|Procent miejsca używanego z maksymalnego wartości serwera.|
|storage_used|Używany magazyn|Bajty|Ilość używanego miejsca. Magazyn używany przez usługę może zawierać pliki bazy danych, dzienniki transakcji i dzienniki serwera.|

Platforma Azure nie dostarcza żadnych metryk agregacji dla klastra jako całości, ale metryki dla wielu węzłów można umieścić na tym samym wykresie.

## <a name="next-steps"></a>Następne kroki

- Zobacz, [jak skonfigurować alerty, aby](howto-hyperscale-alert-on-metric.md) uzyskać wskazówki dotyczące tworzenia alertu na metryki.
