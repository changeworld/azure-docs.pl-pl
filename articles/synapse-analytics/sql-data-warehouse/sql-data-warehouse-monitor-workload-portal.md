---
title: Monitorowanie obciążenia — witryna Azure portal
description: Monitorowanie analizy SQL za pomocą portalu Azure
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.openlocfilehash: 344e2cd03408b5bd1e966abc8abb72bce6078acc
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350333"
---
# <a name="monitor-workload---azure-portal"></a>Monitorowanie obciążenia — witryna Azure portal

W tym artykule opisano, jak używać witryny Azure Portal do monitorowania obciążenia. Obejmuje to konfigurowanie dzienników usługi Azure Monitor w celu zbadania trendów wykonywania zapytań i obciążenia przy użyciu analizy dzienników dla [usługi SQL Analytics.](https://azure.microsoft.com/blog/workload-insights-with-sql-data-warehouse-delivered-through-azure-monitor-diagnostic-logs-pass/)

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure: jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.
- Pula SQL: Będziemy zbierać dzienniki dla puli SQL. Jeśli nie masz aprowizowanego puli SQL, zobacz instrukcje w [aplikacji Tworzenie puli SQL](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-tutorial).

## <a name="create-a-log-analytics-workspace"></a>Tworzenie obszaru roboczego usługi Log Analytics

Przejdź do bloku przeglądania obszarów roboczych usługi Log Analytics i utwórz obszar roboczy 

![Obszary robocze usługi Log Analytics](./media/sql-data-warehouse-monitor-workload-portal/log_analytics_workspaces.png)

![Dodawanie obszaru roboczego analizy](./media/sql-data-warehouse-monitor-workload-portal/add_analytics_workspace.png)

![Dodawanie obszaru roboczego analizy](./media/sql-data-warehouse-monitor-workload-portal/add_analytics_workspace_2.png)

Aby uzyskać więcej informacji na temat obszarów roboczych, odwiedź następującą [dokumentację](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace#create-a-workspace).

## <a name="turn-on-diagnostic-logs"></a>Włączanie dzienników diagnostycznych

Skonfiguruj ustawienia diagnostyczne, aby emitować dzienniki z puli SQL. Dzienniki składają się z widoków telemetrycznych równoważnych najczęściej używanym rejestratorom dmv rozwiązywania problemów z wydajnością. Obecnie obsługiwane są następujące widoki:

- [sys.dm_pdw_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_request_steps](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_dms_workers](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_waits](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_sql_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?view=aps-pdw-2016-au7)


![Włączanie dzienników diagnostycznych](./media/sql-data-warehouse-monitor-workload-portal/enable_diagnostic_logs.png)

Dzienniki mogą być emitowane do usługi Azure Storage, Usługi Stream Analytics lub usługi Log Analytics. W tym samouczku wybierz pozycję Analiza dzienników.

![Określanie dzienników](./media/sql-data-warehouse-monitor-workload-portal/specify_logs.png)

## <a name="run-queries-against-log-analytics"></a>Uruchamianie zapytań względem usługi Log Analytics

Przejdź do obszaru roboczego usługi Log Analytics, w którym można wykonać następujące czynności:

- Analizowanie dzienników przy użyciu zapytań dziennika i zapisywanie zapytań do ponownego użycia
- Zapisywanie kwerend do ponownego użycia
- Tworzenie alertów dotyczących dzienników
- Przypinanie wyników kwerendy do pulpitu nawigacyjnego

Aby uzyskać szczegółowe informacje na temat możliwości zapytań dziennika, odwiedź następującą [dokumentację](https://docs.microsoft.com/azure/azure-monitor/log-query/query-language).

![Edytor obszaru roboczego usługi Log Analytics](./media/sql-data-warehouse-monitor-workload-portal/log_analytics_workspace_editor.png)



![Kwerendy obszaru roboczego usługi Log Analytics](./media/sql-data-warehouse-monitor-workload-portal/log_analytics_workspace_queries.png)

## <a name="sample-log-queries"></a>Przykładowe kwerendy dziennika



```Kusto
//List all queries 
AzureDiagnostics
| where Category contains "ExecRequests"
| project TimeGenerated, StartTime_t, EndTime_t, Status_s, Command_s, ResourceClass_s, duration=datetime_diff('millisecond',EndTime_t, StartTime_t)
```
```Kusto
//Chart the most active resource classes
AzureDiagnostics
| where Category contains "ExecRequests"
| where Status_s == "Completed"
| summarize totalQueries = dcount(RequestId_s) by ResourceClass_s
| render barchart 
```
```Kusto
//Count of all queued queries
AzureDiagnostics
| where Category contains "waits" 
| where Type_s == "UserConcurrencyResourceType"
| summarize totalQueuedQueries = dcount(RequestId_s)
```
## <a name="next-steps"></a>Następne kroki

Teraz, gdy masz skonfigurowane i skonfigurowane dzienniki monitora platformy Azure, [dostosuj pulpity nawigacyjne platformy Azure,](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards) aby udostępnić je zespołowi.
