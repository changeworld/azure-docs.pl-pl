---
title: Wstrzymać, wznowić, skalować REST w usłudze Azure SQL Data Warehouse | Dokumentacja firmy Microsoft
description: Zarządzanie moc obliczeniową w usłudze SQL Data Warehouse za pośrednictwem interfejsów API REST.
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: kfile
editor: ''
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: manage
ms.date: 03/22/2018
ms.author: barbkess
ms.openlocfilehash: 518bbe23f1dcb9ffdffcfb67f875165617762c78
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="rest-apis-for-azure-sql-data-warehouse"></a>Interfejsy API REST dla magazynu danych Azure SQL
Interfejsy API REST zarządzania obliczeniowych w magazynie danych SQL Azure.

## <a name="scale-compute"></a>Skalowanie zasobów obliczeniowych
Aby zmienić jednostki magazynu danych, użyj [Tworzenie lub aktualizacja bazy danych](/rest/api/sql/databases/createorupdate) interfejsu API REST. Poniższy przykład ustawia jednostki magazynu danych do DW1000 MySQLDW, która jest hostowana na serwerze MyServer w bazie danych. Serwer jest w grupie zasobów platformy Azure o nazwie ResourceGroup1.

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

Aby wstrzymać bazy danych, należy użyć [wstrzymać bazy danych](/rest/api/sql/databases/pause) interfejsu API REST. W następującym przykładzie wstrzymano bazy danych o nazwie Database02 znajdującej się na serwerze o nazwie Serwer01. Serwer jest w grupie zasobów platformy Azure o nazwie ResourceGroup1.

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/pause?api-version=2014-04-01-preview HTTP/1.1
```

## <a name="resume-compute"></a>Wznów obliczeń

Aby utworzyć bazę danych, należy użyć [wznowienia bazy danych](/rest/api/sql/databases/resume) interfejsu API REST. W następującym przykładzie uruchomiono bazę danych o nazwie Database02 znajdującej się na serwerze o nazwie Serwer01. Serwer jest w grupie zasobów platformy Azure o nazwie ResourceGroup1. 

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/resume?api-version=2014-04-01-preview HTTP/1.1
```

## <a name="check-database-state"></a>Sprawdź stan bazy danych

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01 HTTP/1.1
```


## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji, zobacz [Zarządzanie obliczeń](sql-data-warehouse-manage-compute-overview.md).

