---
title: Zarządzanie zadaniami tworzenia kopii zapasowych przy użyciu interfejsu API REST
description: W tym artykule dowiesz się, jak śledzić zadania tworzenia kopii zapasowych i przywracania kopii zapasowych usługi Azure backup przy użyciu interfejsu API REST oraz zarządzać nimi.
ms.topic: conceptual
ms.date: 08/03/2018
ms.assetid: b234533e-ac51-4482-9452-d97444f98b38
ms.openlocfilehash: 628569c547aa776ec2fbb7ec7e32edad7c1fe7dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273529"
---
# <a name="track-backup-and-restore-jobs-using-rest-api"></a>Śledzenie zadań tworzenia kopii zapasowych i przywracania przy użyciu interfejsu REST API

Usługa Azure Backup wyzwala zadania, które działają w tle w różnych scenariuszach, takich jak wyzwalanie kopii zapasowej, przywracanie operacji, wyłączanie kopii zapasowej. Te zadania mogą być śledzone przy użyciu ich identyfikatorów.

## <a name="fetch-job-information-from-operations"></a>Pobieranie informacji o zamówianiu z operacji

Operacja, taka jak wyzwalanie kopii zapasowej zawsze zwróci jobID. Na przykład: Ostateczna odpowiedź [operacji interfejsu API interfejsu REST kopii zapasowej wyzwalacza](backup-azure-arm-userestapi-backupazurevms.md#example-responses-3) jest następująca:

```http
{
  "id": "cd153561-20d3-467a-b911-cc1de47d4763",
  "name": "cd153561-20d3-467a-b911-cc1de47d4763",
  "status": "Succeeded",
  "startTime": "2018-09-12T02:16:56.7399752Z",
  "endTime": "2018-09-12T02:16:56.7399752Z",
  "properties": {
    "objectType": "OperationStatusJobExtendedInfo",
    "jobId": "41f3e94b-ae6b-4a20-b422-65abfcaf03e5"
  }
}
```

Zadanie tworzenia kopii zapasowej maszyny Wirtualnej platformy Azure jest identyfikowane przez pole "jobId" i może być śledzone zgodnie z powyższym w [tym miejscu](https://docs.microsoft.com/rest/api/backup/jobdetails/) przy użyciu prostego żądania *GET.*

## <a name="tracking-the-job"></a>Śledzenie zadania

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupJobs/{jobName}?api-version=2019-05-13
```

Jest `{jobName}` "jobId" wymienione powyżej. Odpowiedź jest zawsze 200 OK z pola "stan" wskazujący bieżący stan zadania. Po zakończeniu "Zakończone" lub "CompletedWithWarnings", sekcja "extendedInfo" ujawnia więcej szczegółów na temat zadania.

### <a name="response"></a>Odpowiedź

|Nazwa  |Typ  |Opis  |
|---------|---------|---------|
|200 ok.     | [Źródło pracy](https://docs.microsoft.com/rest/api/backup/jobdetails/get#jobresource)        | OK        |

#### <a name="example-response"></a>Przykładowa odpowiedź

Po przesłaniu *identyfikatora GET* URI zwracana jest odpowiedź 200 (OK).

```http
HTTP/1.1 200 OK
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: e9702101-9da2-4681-bdf3-a54e17329a56
x-ms-client-request-id: ba4dff71-1655-4c1d-a71f-c9869371b18b; ba4dff71-1655-4c1d-a71f-c9869371b18b
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14989
x-ms-correlation-request-id: e9702101-9da2-4681-bdf3-a54e17329a56
x-ms-routing-request-id: SOUTHINDIA:20180521T102317Z:e9702101-9da2-4681-bdf3-a54e17329a56
Cache-Control: no-cache
Date: Mon, 21 May 2018 10:23:17 GMT
Server: Microsoft-IIS/8.0
X-Powered-By: ASP.NET

{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Default-RecoveryServices-ResourceGroup-centralindia/providers/microsoft.recoveryservices/vaults/abdemovault/backupJobs/7ddead57-bcb9-4269-ac31-6a1b57588700",
  "name": "7ddead57-bcb9-4269-ac31-6a1b57588700",
  "type": "Microsoft.RecoveryServices/vaults/backupJobs",
  "properties": {
    "jobType": "AzureIaaSVMJob",
    "duration": "00:20:23.0896697",
    "actionsInfo": [
      1
    ],
    "virtualMachineVersion": "Compute",
    "extendedInfo": {
      "tasksList": [
        {
          "taskId": "Take Snapshot",
          "duration": "00:00:00",
          "status": "Completed"
        },
        {
          "taskId": "Transfer data to vault",
          "duration": "00:00:00",
          "status": "Completed"
        }
      ],
      "propertyBag": {
        "VM Name": "uttestvmub1",
        "Backup Size": "2332 MB"
      }
    },
    "entityFriendlyName": "uttestvmub1",
    "backupManagementType": "AzureIaasVM",
    "operation": "Backup",
    "status": "Completed",
    "startTime": "2018-05-21T08:35:40.9488967Z",
    "endTime": "2018-05-21T08:56:04.0385664Z",
    "activityId": "7df8e874-1d66-4f81-8e91-da2fe054811d"
  }
}
}

```
