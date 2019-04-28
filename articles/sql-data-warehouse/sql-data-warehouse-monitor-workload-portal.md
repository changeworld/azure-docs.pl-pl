---
title: Monitor obciążenia — witryna Azure portal | Dokumentacja firmy Microsoft
description: Monitor Azure SQL Data Warehouse przy użyciu witryny Azure portal
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 03/22/2019
ms.author: kevin
ms.reviewer: jrasnick
ms.openlocfilehash: 6c8ce090039e3d5cc85c86d920710294de2165f9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60748909"
---
# <a name="monitor-workload---azure-portal"></a>Monitor obciążenia — witryna Azure portal

W tym artykule opisano sposób użycia witryny Azure portal do monitorowania obciążenia. Obejmuje to konfigurowanie usługi Azure Monitor dzienniki, aby zbadać trendy obciążenia i wykonywanie zapytań za pomocą usługi log analytics dla [Azure SQL Data Warehouse](https://azure.microsoft.com/blog/workload-insights-with-sql-data-warehouse-delivered-through-azure-monitor-diagnostic-logs-pass/).

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure: Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).
- Azure SQL Data Warehouse: Firma Microsoft będzie zbierania dzienników dla SQL data warehouse. Jeśli nie masz aprowizowane SQL data warehouse, zapoznaj się z instrukcjami w [utworzyć SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-tutorial).

## <a name="create-a-log-analytics-workspace"></a>Utwórz obszar roboczy usługi Log Analytics

Przejdź do bloku przeglądania dla obszarów roboczych usługi Log Analytics, a następnie utwórz obszar roboczy 

![Obszary robocze usługi Log Analytics](media/sql-data-warehouse-monitor/log_analytics_workspaces.png)

![Dodaj obszar roboczy analizy](media/sql-data-warehouse-monitor/add_analytics_workspace.png)

![Dodaj obszar roboczy analizy](media/sql-data-warehouse-monitor/add_analytics_workspace_2.png)

Szczegółowe informacje na temat obszarów roboczych, można znaleźć w następującej [dokumentacji](https://docs.microsoft.com/azure/azure-monitor/platform/manage-access#create-a-workspace).

## <a name="turn-on-diagnostic-logs"></a>Włączanie dzienników diagnostycznych 

Konfigurowanie ustawień diagnostycznych do emitowania dzienników z usługi SQL data warehouse. Dzienniki składają się z widoków danych telemetrycznych usługi data warehouse jest równoważne z najczęściej używanych Rozwiązywanie problemów z wydajnością dynamicznych widoków zarządzania dla usługi SQL Data Warehouse. Obecnie obsługiwane są następujące widoki:

- [sys.dm_pdw_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_request_steps](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_dms_workers](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_waits](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_sql_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?view=aps-pdw-2016-au7)


![Włączanie dzienników diagnostycznych](media/sql-data-warehouse-monitor/enable_diagnostic_logs.png)

Może być emitowana dzienników do usługi Azure Storage, Stream Analytics lub usługi Log Analytics. Na potrzeby tego samouczka wybierz usługi Log Analytics.

![Określ dzienników](media/sql-data-warehouse-monitor/specify_logs.png)

## <a name="run-queries-against-log-analytics"></a>Uruchamiać zapytania usługi Log Analytics

Przejdź do obszaru roboczego usługi Log Analytics, gdzie można wykonać następujące czynności:

- Analizowanie dzienników przy użyciu dziennika zapytań i zapisać zapytania do ponownego wykorzystania
- Zapisywanie zapytań do ponownego wykorzystania
- Tworzenie alertów dziennika
- Wyniki zapytania Przypnij do pulpitu nawigacyjnego

Szczegółowe informacje dotyczące możliwości dziennika zapytań, znajduje się w następującej [dokumentacji](https://docs.microsoft.com/azure/azure-monitor/log-query/query-language).

![Edytor obszaru roboczego analizy dzienników](media/sql-data-warehouse-monitor/log_analytics_workspace_editor.png)



![Zaloguj się analitycznych zapytań obszaru roboczego](media/sql-data-warehouse-monitor/log_analytics_workspace_queries.png)

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
## <a name="next-steps"></a>Kolejne kroki

Skoro zdefiniowano i skonfigurowano dzienników usługi Azure monitor [dostosowywania pulpitów nawigacyjnych platformy Azure](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards) udostępnianie całemu zespołowi.
