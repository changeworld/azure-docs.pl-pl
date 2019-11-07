---
title: Monitorowanie obciążenia — Azure Portal
description: Monitorowanie Azure SQL Data Warehouse przy użyciu Azure Portal
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 03/22/2019
ms.author: kevin
ms.reviewer: jrasnick
ms.openlocfilehash: 73d837c34dd5a480cae08a41c89939414899052a
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73645630"
---
# <a name="monitor-workload---azure-portal"></a>Monitorowanie obciążenia — Azure Portal

W tym artykule opisano, jak używać Azure Portal do monitorowania obciążenia. Obejmuje to Konfigurowanie dzienników Azure Monitor, aby zbadać trendy wykonywania i obciążeń zapytań za pomocą usługi log Analytics dla [Azure SQL Data Warehouse](https://azure.microsoft.com/blog/workload-insights-with-sql-data-warehouse-delivered-through-azure-monitor-diagnostic-logs-pass/).

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure: Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/) .
- Azure SQL Data Warehouse: będziemy zbierać dzienniki dla SQL Data Warehouse. Jeśli nie masz zainicjowanego SQL Data Warehouse, zapoznaj się z instrukcjami w artykule [tworzenie SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-tutorial).

## <a name="create-a-log-analytics-workspace"></a>Tworzenie obszaru roboczego Log Analytics

Przejdź do bloku Przeglądaj dla obszarów roboczych Log Analytics i Utwórz obszar roboczy 

![Obszary robocze usługi Log Analytics](media/sql-data-warehouse-monitor/log_analytics_workspaces.png)

![Dodaj obszar roboczy analizy](media/sql-data-warehouse-monitor/add_analytics_workspace.png)

![Dodaj obszar roboczy analizy](media/sql-data-warehouse-monitor/add_analytics_workspace_2.png)

Aby uzyskać więcej informacji na temat obszarów roboczych, zapoznaj się z poniższą [dokumentacją](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace#create-a-workspace).

## <a name="turn-on-diagnostic-logs"></a>Włączanie dzienników diagnostycznych 

Skonfiguruj ustawienia diagnostyczne, aby emitować dzienniki z SQL Data Warehouse. Dzienniki składają się z widoków telemetrii magazynu danych, które są równoważne z najczęściej używanymi rozwiązaniami do rozwiązywania problemów z wydajnością widoków DMV na potrzeby SQL Data Warehouse. Obecnie obsługiwane są następujące widoki:

- [sys.dm_pdw_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_request_steps](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_dms_workers](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_waits](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_sql_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?view=aps-pdw-2016-au7)


![Włączanie dzienników diagnostycznych](media/sql-data-warehouse-monitor/enable_diagnostic_logs.png)

Dzienniki mogą być emitowane do usługi Azure Storage, Stream Analytics lub Log Analytics. Na potrzeby tego samouczka wybierz pozycję Log Analytics.

![Określanie dzienników](media/sql-data-warehouse-monitor/specify_logs.png)

## <a name="run-queries-against-log-analytics"></a>Uruchom zapytania względem Log Analytics

Przejdź do obszaru roboczego Log Analytics, gdzie można wykonać następujące czynności:

- Analizuj dzienniki przy użyciu zapytań dzienników i zapisuj zapytania do ponownego użycia
- Zapisz zapytania do ponownego użycia
- Tworzenie alertów dotyczących dzienników
- Przypinanie wyników zapytania do pulpitu nawigacyjnego

Aby uzyskać szczegółowe informacje na temat możliwości kwerend dzienników, zapoznaj się z poniższą [dokumentacją](https://docs.microsoft.com/azure/azure-monitor/log-query/query-language).

![Log Analytics Edytor obszarów roboczych](media/sql-data-warehouse-monitor/log_analytics_workspace_editor.png)



![Log Analytics zapytań obszaru roboczego](media/sql-data-warehouse-monitor/log_analytics_workspace_queries.png)

## <a name="sample-log-queries"></a>Przykładowe zapytania dziennika



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

Teraz, po skonfigurowaniu i skonfigurowaniu dzienników usługi Azure monitor, [Dostosuj pulpity nawigacyjne platformy Azure](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards) tak, aby były udostępniane przez zespół.
