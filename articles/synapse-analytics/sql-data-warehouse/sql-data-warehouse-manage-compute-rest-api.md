---
title: Pauza, wznowienie, skalowanie za pomocą interfejsów API REST
description: Zarządzanie mocą obliczeniową w magazynie danych usługi Azure Synapse Analytics za pomocą interfejsów API REST.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/29/2019
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 4efd5c63af9f09d41733e8e172270410245977ec
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633198"
---
# <a name="rest-apis-for-azure-sql-data-warehouse"></a>INTERFEJSY API REST dla usługi Azure SQL Data Warehouse

Interfejsy API REST do zarządzania obliczeniami w magazynie danych usługi Azure Synapse Analytics.

## <a name="scale-compute"></a>Skalowanie zasobów obliczeniowych

Aby zmienić jednostki magazynu danych, użyj interfejsu API [Tworzenie lub Aktualizowanie rest bazy danych.](/rest/api/sql/databases/createorupdate?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) W poniższym przykładzie ustawia jednostki magazynu danych na DW1000 dla bazy danych MySQLDW, która jest hostowana na serwerze MyServer. Serwer znajduje się w grupie zasobów platformy Azure o nazwie ResourceGroup1.

```
PATCH https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": DW1000
    }
}
```

## <a name="pause-compute"></a>Wstrzymanie obliczeń

Aby wstrzymać bazę danych, użyj interfejsu API Wstrzymaj rest [bazy danych.](/rest/api/sql/databases/pause?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) Poniższy przykład wstrzymuje bazę danych o nazwie Database02 hostowane na serwerze o nazwie Server01. Serwer znajduje się w grupie zasobów platformy Azure o nazwie ResourceGroup1.

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/pause?api-version=2014-04-01-preview HTTP/1.1
```

## <a name="resume-compute"></a>Wznów obliczeń

Aby uruchomić bazę danych, użyj interfejsu API [REST bazy danych wznowić.](/rest/api/sql/databases/resume?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) W poniższym przykładzie rozpoczyna się baza danych o nazwie Database02 hostowana na serwerze o nazwie Server01. Serwer znajduje się w grupie zasobów platformy Azure o nazwie ResourceGroup1.

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/resume?api-version=2014-04-01-preview HTTP/1.1
```

## <a name="check-database-state"></a>Sprawdzanie stanu bazy danych

> [!NOTE]
> Obecnie sprawdź stan bazy danych może powrócić online, gdy baza danych jest ukończenie przepływu pracy online, co powoduje błędy połączenia. Może być konieczne dodanie 2 do 3 minut opóźnienia w kodzie aplikacji, jeśli używasz tego wywołania interfejsu API do wyzwalania prób połączenia.

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01 HTTP/1.1
```

## <a name="get-maintenance-schedule"></a>Uzyskaj harmonogram konserwacji

Sprawdź harmonogram konserwacji, który został ustawiony dla magazynu danych.

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/maintenanceWindows/current?maintenanceWindowName=current&api-version=2017-10-01-preview HTTP/1.1

```

## <a name="set-maintenance-schedule"></a>Ustawianie harmonogramu konserwacji

Aby ustawić i zaktualizować harmonogram konserwacji w istniejącym magazynie danych.

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/maintenanceWindows/current?maintenanceWindowName=current&api-version=2017-10-01-preview HTTP/1.1

{
    "properties": {
        "timeRanges": [
                {
                                "dayOfWeek": Saturday,
                                "startTime": 00:00,
                                "duration": 08:00,
                },
                {
                                "dayOfWeek": Wednesday
                                "startTime": 00:00,
                                "duration": 08:00,
                }
                ]
    }
}

```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz [Zarządzanie obliczeniami](sql-data-warehouse-manage-compute-overview.md).
