---
title: 'Usługa Azure Backup: Zadania tworzenia kopii zapasowej przy użyciu interfejsu API REST zarządzania'
description: Zarządzanie kopiami zapasowymi i przywrócić zadania usługi Azure Backup przy użyciu interfejsu API REST
services: backup
author: pvrk
manager: shivamg
keywords: INTERFEJS API REST; Kopia zapasowa maszyny Wirtualnej platformy Azure; Przywracanie maszyny Wirtualnej platformy Azure;
ms.service: backup
ms.topic: conceptual
ms.date: 08/03/2018
ms.author: pullabhk
ms.assetid: b234533e-ac51-4482-9452-d97444f98b38
ms.openlocfilehash: eb8b7dc77d180eb56c2585e93e60a36742f6c84c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60646626"
---
# <a name="track-backup-and-restore-jobs-using-rest-api"></a>Śledź zadania tworzenia kopii zapasowych i przywracania przy użyciu interfejsu API REST

Usługa Azure Backup wyzwala zadania, w których są wykonywane w tle w różnych scenariuszach, takich jak wyzwolenie tworzenia kopii zapasowej, przywracanie operacji wyłączania kopii zapasowej. Te zadania można śledzić przy użyciu ich identyfikatorów.

## <a name="fetch-job-information-from-operations"></a>Pobierz informacje o zadaniu z operacji

Operacja, taka jak wyzwolenie tworzenia kopii zapasowej zawsze zwraca identyfikator zadania. Na przykład: Ostatecznej odpowiedzi [wyzwalanie operacji interfejsu API REST tworzenia kopii zapasowej](backup-azure-arm-userestapi-backupazurevms.md#example-responses-3) jest następująca:

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

Zadanie tworzenia kopii zapasowej maszyny Wirtualnej platformy Azure są identyfikowane przez pole "Identyfikator zadania" i mogą być śledzone, jak wspomniano wcześniej [tutaj](https://docs.microsoft.com/rest/api/backup/jobdetails/) przy użyciu prostego *UZYSKAĆ* żądania.

## <a name="tracking-the-job"></a>Śledzenie zadania

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupJobs/{jobName}?api-version=2017-07-01
```

`{jobName}` "JobId" został podany powyżej. Odpowiedź jest zawsze 200 OK z polem "status", wskazująca bieżący stan zadania. Gdy będzie "Completed" lub "CompletedWithWarnings" w sekcji "extendedInfo" wykazuje, szczegółowe informacje o zadaniu.

### <a name="response"></a>Odpowiedź

|Name (Nazwa)  |Typ  |Opis  |
|---------|---------|---------|
|200 OK     | [JobResource](https://docs.microsoft.com/rest/api/backup/jobdetails/get#jobresource)        | OK        |

#### <a name="example-response"></a>Przykładowa odpowiedź

Raz *UZYSKAĆ* identyfikatora URI jest przesyłany, zwrócił odpowiedź 200 (OK).

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