---
title: Monitorowanie i dostrajanie wydajności
description: Omówienie możliwości monitorowania i dostrajania wydajności i metodologii w usłudze Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: jrasnick, carlrab
ms.date: 03/10/2020
ms.openlocfilehash: 837d88665c1fdffe902c9c478e5d6dc65a2e402a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268732"
---
# <a name="monitoring-and-performance-tuning-in-azure-sql-database"></a>Monitorowanie i dostrajanie wydajności w bazie danych SQL usługi Azure

Aby monitorować wydajność bazy danych w usłudze Azure SQL Database, należy rozpocząć od monitorowania zasobów procesora CPU i we/wy używanych przez obciążenie w stosunku do poziomu wydajności bazy danych wybranego przy wyborze określonej warstwy usług i poziomu wydajności. Aby to osiągnąć, usługa Azure SQL Database emituje metryki zasobów, które można wyświetlić w witrynie Azure portal lub przy użyciu jednego z tych narzędzi do zarządzania SQL: [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) lub SQL Server Management [Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS).

W przypadku pojedynczych i pulchnych baz danych usługa Azure SQL Database udostępnia szereg doradców baz danych w celu zapewnienia zaleceń dotyczących inteligentnego dostrajania wydajności i opcji automatycznego dostrajania w celu zwiększenia wydajności. Ponadto query Performance Insight pokazuje szczegółowe informacje o zapytaniach odpowiedzialnych za użycie procesora CPU i we/wy dla pojedynczych i puli baz danych.

Usługa Azure SQL Database zapewnia dodatkowe funkcje monitorowania i dostrajania wspierane przez sztuczną inteligencję, aby pomóc w rozwiązywaniu problemów i maksymalizacji wydajności baz danych i rozwiązań. Można skonfigurować eksport [przesyłania strumieniowego](sql-database-metrics-diag-logging.md) tych [inteligentnych aplikacji Insights](sql-database-intelligent-insights.md) i innych dzienników i metryk zasobów bazy danych SQL do jednego z kilku miejsc docelowych do użycia i analizy, szczególnie przy użyciu [usługi SQL Analytics](../azure-monitor/insights/azure-sql.md)). Usługa Azure SQL Analytics to zaawansowane rozwiązanie do monitorowania chmury do monitorowania wydajności wszystkich baz danych SQL platformy Azure na dużą skalę i w wielu subskrypcjach w jednym widoku. Aby uzyskać listę dzienników i metryk, które można wyeksportować, zobacz [dane telemetryczne diagnostyczne do eksportu](sql-database-metrics-diag-logging.md#diagnostic-telemetry-for-export-for-azure-sql-database)

Na koniec SQL ma własne możliwości monitorowania i diagnostyki z [SQL Server Query Store](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) i [dynamicznymi widokami zarządzania (DMV).](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views) Zobacz [Monitorowanie przy użyciu dmvs](sql-database-monitoring-with-dmvs.md) dla skryptów do monitorowania różnych problemów z wydajnością.

## <a name="monitoring-and-tuning-capabilities-in-the-azure-portal"></a>Funkcje monitorowania i dostrajania w witrynie Azure portal

W witrynie Azure portal usługa Azure SQL Database zapewnia monitorowanie metryk zasobów dla wszystkich typów wdrożeń. Dodatkowe, dla pojedynczych i puli baz danych, doradców bazy danych i Query Performance Insight zapewniają zalecenia dotyczące dostrajania zapytań i analizy wydajności kwerend. Na koniec w witrynie Azure portal można włączyć automatyczne dla serwerów logicznych i ich pojedynczych i puli baz danych.

### <a name="sql-database-resource-monitoring"></a>Monitorowanie zasobów bazy danych SQL

Możesz szybko monitorować następujące metryki zasobów w witrynie Azure portal w widoku **Metryki:**

- **Użycie funkcji DTU**

  Sprawdź, czy baza danych lub pula elastyczna osiąga 100 procent użycia DTU przez dłuższy czas. Wysokie użycie jednostki DTU wskazuje, że obciążenie może wymagać więcej zasobów procesora CPU lub we/wy. Może również wskazywać kwerendy, które należy zoptymalizować.
- **Użycie procesora**

  Sprawdź, czy baza danych, pula elastyczna lub wystąpienie zarządzane osiągają 100 procent użycia procesora CPU przez dłuższy czas. Wysoki procesor wskazuje, że obciążenie może wymagać więcej zasobów procesora CPU lub we/wy. Może również wskazywać kwerendy, które należy zoptymalizować.
- **Użycie we/wy**

  Sprawdź, czy wystąpienie bazy danych, puli elastycznej lub zarządzania osiąga limity we/wy podstawowej pamięci masowej. Wysokie użycie we/wy wskazuje, że obciążenie może wymagać więcej zasobów procesora CPU lub we/wy. Może również wskazywać kwerendy, które należy zoptymalizować.

  ![Metryki zasobów](./media/sql-database-monitor-tune-overview/resource-metrics.png)

### <a name="database-advisors"></a>Doradcy bazy danych
' Usługa Azure SQL Database zawiera [doradców bazy danych,](sql-database-advisor.md) które zapewniają zalecenia dotyczące dostrajania wydajności dla pojedynczych i puli baz danych. Te zalecenia są dostępne w witrynie Azure portal, a także przy użyciu programu [PowerShell.](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaseadvisor) Można również włączyć [automatyczne dostrajanie,](sql-database-automatic-tuning.md) dzięki czemu baza danych SQL może automatycznie zaimplementować te zalecenia dotyczące dostrajania.

### <a name="query-performance-insight"></a>Szczegółowe informacje o wydajności zapytań

[Query Performance Insight](sql-database-query-performance.md) pokazuje wydajność w witrynie Azure portal najlepiej zużywających i najdłużej działających zapytań dla pojedynczych i puli baz danych.

## <a name="generate-intelligent-assessments-of-performance-issues"></a>Generowanie inteligentnych ocen problemów z wydajnością

Usługa Azure SQL Database [Intelligent Insights](sql-database-intelligent-insights.md) używa wbudowanej analizy do ciągłego monitorowania użycia bazy danych za pomocą sztucznej inteligencji i wykrywania zdarzeń powodujących zakłócenia, które powodują niską wydajność. Usługa Intelligent Insights automatycznie wykrywa problemy z wydajnością baz danych w bazie danych SQL azure na podstawie czasu oczekiwania wykonania kwerendy, błędów lub limitów czasu. Po wykryciu przeprowadzana jest szczegółowa analiza, która generuje dziennik zasobów (o nazwie SQLInsights) z [inteligentną oceną problemów.](sql-database-intelligent-insights-troubleshoot-performance.md) Ocena ta składa się z analizy głównej przyczyny problemu z wydajnością bazy danych i, w miarę możliwości, zalecenia dotyczące poprawy wydajności.

Usługa Intelligent Insights to unikatowa funkcja wbudowanej analizy platformy Azure, która zapewnia następującą wartość:

- Aktywne monitorowanie
- Dostosowane informacje o wydajności
- Wczesne wykrywanie pogorszenia wydajności bazy danych
- Analiza głównej przyczyny wykrytych problemów
- Zalecenia dotyczące poprawy wydajności
- Skalowanie w poziomie w setkach tysięcy baz danych
- Pozytywny wpływ na zasoby DevOps i całkowity koszt posiadania

## <a name="enable-the-streaming-export-of-metrics-and-resource-logs"></a>Włączanie eksportu danych i dzienników zasobów przesyłania strumieniowego

Można włączyć i skonfigurować [eksport danych telemetrycznych przesyłania strumieniowego](sql-database-metrics-diag-logging.md) do jednego z kilku miejsc docelowych, w tym dziennika zasobów intelligent insights. Użyj [analizy SQL](../azure-monitor/insights/azure-sql.md) i innych funkcji, aby korzystać z tej dodatkowej danych telemetrycznych diagnostycznych w celu zidentyfikowania i rozwiązania problemów z wydajnością.

Ustawienia diagnostyczne można skonfigurować do przesyłania strumieniowego kategorii metryk i dzienników zasobów dla pojedynczych baz danych, puli baz danych, pul elastycznych, wystąpień zarządzanych i baz danych wystąpień do jednego z następujących zasobów platformy Azure.

### <a name="log-analytics-workspace-in-azure-monitor"></a>Obszar roboczy usługi Log Analytics na monitorze platformy Azure

Metryki i dzienniki zasobów można przesyłać strumieniowo do [obszaru roboczego usługi Log Analytics w usłudze Azure Monitor.](../azure-monitor/platform/resource-logs-collect-workspace.md) Dane przesyłane strumieniowo w tym miejscu mogą być używane przez [usługę SQL Analytics,](../azure-monitor/insights/azure-sql.md)która jest rozwiązaniem do monitorowania tylko w chmurze, które zapewnia inteligentne monitorowanie baz danych, które obejmują raporty wydajności, alerty i zalecenia dotyczące ograniczania ryzyka. Dane przesyłane strumieniowo do obszaru roboczego usługi Log Analytics mogą być analizowane przy użyciu innych zebranych danych monitorowania, a także umożliwia korzystanie z innych funkcji usługi Azure Monitor, takich jak alerty i wizualizacje.

### <a name="azure-event-hubs"></a>Azure Event Hubs

Metryki i dzienniki zasobów można przesyłać strumieniowo do [usługi Azure Event Hubs.](../azure-monitor/platform/resource-logs-stream-event-hubs.md) Przesyłanie strumieniowe danych telemetrycznych diagnostycznych do centrów zdarzeń w celu zapewnienia następujących funkcji:

- **Dzienniki strumienia do systemów rejestrowania i telemetrii innych firm**

  Przesyłaj strumieniowo wszystkie metryki i dzienniki zasobów do centrum zdarzeń w jednym centrum zdarzeń, aby potokować dane dziennika do narzędzia SIEM lub analizy dzienników innej firmy.
- **Tworzenie niestandardowej platformy telemetrycznej i rejestrowania**

  Wysoce skalowalny charakter publikowania i subskrybowania centrów zdarzeń umożliwia elastyczne pozyskiwania metryk i dzienników zasobów na niestandardowej platformie telemetrycznej. Zobacz [projektowanie i tworzenie rozmiaru globalnej platformy telemetrii skali w usłudze Azure Event Hubs, aby](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/) uzyskać szczegółowe informacje.
- **Wyświetlanie kondycji usługi przez przesyłanie strumieniowe danych do usługi Power BI**

  Za pomocą centrów zdarzeń, usługi Stream Analytics i usługi Power BI możesz przekształcać dane diagnostyczne w niemal w czasie rzeczywistym, aby uzyskać szczegółowe informacje o usługach platformy Azure. Zobacz [Usługa Stream Analytics i Usługa Power BI: pulpit nawigacyjny analizy w czasie rzeczywistym, aby uzyskać](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-power-bi-dashboard) szczegółowe informacje na temat tego rozwiązania.

### <a name="azure-storage"></a>Azure Storage

Przesyłaj strumieniowo metryki i dzienniki zasobów do [usługi Azure Storage](../azure-monitor/platform/resource-logs-collect-storage.md). Użyj magazynu platformy Azure do archiwizacji ogromnych ilości danych telemetrycznych diagnostycznych za ułamek kosztu poprzednich dwóch opcji przesyłania strumieniowego.

## <a name="use-extended-events-in-the-sql-database-engine"></a>Używanie zdarzeń rozszerzonych w silniku bazy danych SQL

Ponadto można użyć [zdarzeń rozszerzonych](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events) w języku SQL do dodatkowego zaawansowanego monitorowania i rozwiązywania problemów. Architektura zdarzeń rozszerzonych umożliwia użytkownikom zbieranie jak najwięcej lub tak mało danych, jak jest to konieczne do rozwiązania lub zidentyfikowania problemu z wydajnością. Aby uzyskać informacje dotyczące używania zdarzeń rozszerzonych w bazie danych SQL, zobacz [Zdarzenia rozszerzone w bazie danych SQL](sql-database-xevent-db-diff-from-svr.md).

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji na temat zaleceń dotyczących inteligentnej wydajności dla pojedynczych i pulchnych baz danych, zobacz [Zalecenia dotyczące wydajności doradcy bazy danych](sql-database-advisor.md).
- Aby uzyskać więcej informacji na temat automatycznego monitorowania wydajności bazy danych za pomocą zautomatyzowanej diagnostyki i analizy przyczyn głównych problemów z wydajnością, zobacz [Azure SQL Intelligent Insights](sql-database-intelligent-insights.md).
'''''''''