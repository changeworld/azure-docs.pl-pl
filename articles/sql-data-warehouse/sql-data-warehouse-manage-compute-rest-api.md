---
title: Wstrzymywanie, wznawianie, Skalowanie z użyciem usług REST w usłudze Azure SQL Data Warehouse | Dokumentacja firmy Microsoft
description: Zarządzaj moc obliczeniową w usłudze SQL Data Warehouse za pomocą interfejsów API REST.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 03/29/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 658b35163e20d024118bc7a3142c86614540f00c
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59790250"
---
# <a name="rest-apis-for-azure-sql-data-warehouse"></a>Interfejsy API REST dla usługi Azure SQL Data Warehouse
Interfejsy API REST zarządzania zasobów obliczeniowych w usłudze Azure SQL Data Warehouse.

## <a name="scale-compute"></a>Skalowanie zasobów obliczeniowych
Aby zmienić liczbę jednostek magazynu danych, użyj [tworzenia lub aktualizacji bazy danych](/rest/api/sql/databases/createorupdate) interfejsu API REST. Poniższy przykład ustawia jednostki magazynu danych DW1000 MySQLDW, która jest hostowana na serwerze MyServer w bazie danych. Serwer jest w grupie zasobów platformy Azure o nazwie ResourceGroup1.

```
PATCH https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": DW1000
    }
}
```

## <a name="pause-compute"></a>Wstrzymaj obliczeń

Aby wstrzymać bazy danych, należy użyć [wstrzymać bazy danych](/rest/api/sql/databases/pause) interfejsu API REST. W następującym przykładzie wstrzymano bazę danych o nazwie znajdującej się na serwerze o nazwie Serwer01 Database02. Serwer jest w grupie zasobów platformy Azure o nazwie ResourceGroup1.

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/pause?api-version=2014-04-01-preview HTTP/1.1
```

## <a name="resume-compute"></a>Wznawianie obliczeń

Aby utworzyć bazę danych, należy użyć [wznawianie bazy danych](/rest/api/sql/databases/resume) interfejsu API REST. Poniższy przykład rozpoczyna się o nazwie znajdującej się na serwerze o nazwie Serwer01 Database02 bazy danych. Serwer jest w grupie zasobów platformy Azure o nazwie ResourceGroup1. 

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/resume?api-version=2014-04-01-preview HTTP/1.1
```

## <a name="check-database-state"></a>Sprawdź stan bazy danych

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01 HTTP/1.1
```

## <a name="get-maintenance-schedule"></a>Pobierz harmonogram konserwacji
Sprawdzanie harmonogramu konserwacji, która została ustawiona dla magazynu danych. 

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/maintenanceWindows/current?maintenanceWindowName=current&api-version=2017-10-01-preview HTTP/1.1

```

## <a name="set-maintenance-schedule"></a>Ustaw harmonogram konserwacji
Aby ustawić i zaktualizuj harmonogram maintnenance na istniejący magazyn danych.

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


## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji, zobacz [Zarządzanie obliczeniami](sql-data-warehouse-manage-compute-overview.md).

