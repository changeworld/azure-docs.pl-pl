---
title: Tworzenie zasad tworzenia kopii zapasowych przy użyciu interfejsu REST API
description: W tym artykule dowiesz się, jak tworzyć zasady tworzenia kopii zapasowych (harmonogram i przechowywanie) i zarządzać nimi przy użyciu interfejsu API REST.
ms.topic: conceptual
ms.date: 08/21/2018
ms.assetid: 5ffc4115-0ae5-4b85-a18c-8a942f6d4870
ms.openlocfilehash: 0718ebc3612f53f1c2cc279096dd92de69bb5ef6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76963856"
---
# <a name="create-azure-recovery-services-backup-policies-using-rest-api"></a>Tworzenie zasad tworzenia kopii zapasowych usług Azure Recovery Services przy użyciu interfejsu API REST

Kroki tworzenia zasad tworzenia kopii zapasowych dla magazynu usług Azure Recovery Services są opisane w [dokumencie interfejsu API REST zasad.](/rest/api/backup/protectionpolicies/createorupdate) Użyjmy tego dokumentu jako odwołania do utworzenia zasad dla kopii zapasowej maszyny Wirtualnej platformy Azure.

## <a name="create-or-update-a-policy"></a>Tworzenie lub aktualizowanie zasad

Aby utworzyć lub zaktualizować zasady usługi Azure Backup, wykonaj następującą operację *PUT*

```http
PUT https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupPolicies/{policyName}?api-version=2019-05-13
```

I `{policyName}` `{vaultName}` są dostarczane w identyfikatorze URI. Dodatkowe informacje znajdują się w treści wniosku.

## <a name="create-the-request-body"></a>Tworzenie treści żądania

Na przykład, aby utworzyć zasady dla kopii zapasowej maszyny Wirtualnej platformy Azure, poniżej są składniki treści żądania.

|Nazwa  |Wymagany  |Typ  |Opis  |
|---------|---------|---------|---------|
|properties     |   True      |  OchronaPolicy:[AzureIaaSVMProtectionPolicy](/rest/api/backup/protectionpolicies/createorupdate#azureiaasvmprotectionpolicy)      | Właściwości ProtectionPolicyResource        |
|tags     |         | Obiekt        |  Tagi zasobów       |

Pełna lista definicji w treści żądania znajduje się w [dokumencie interfejsu API interfejsu API rest zasad kopii zapasowych](/rest/api/backup/protectionpolicies/createorupdate).

### <a name="example-request-body"></a>Przykładowa treść żądania

Następująca treść żądania definiuje zasady tworzenia kopii zapasowych dla kopii zapasowych maszyn wirtualnych platformy Azure.

Polityka mówi:

- Weź cotygodniową kopię zapasową w każdy poniedziałek, środę, czwartek o 10:00 czasu pacyficznego.
- Zachowaj kopie zapasowe wykonane w każdy poniedziałek, środę, czwartek przez tydzień.
- Zachowaj kopie zapasowe wykonane w każdą pierwszą środę i trzeci czwartek miesiąca przez dwa miesiące (zastępuje poprzednie warunki przechowywania, jeśli takie istnieją).
- Zachowaj kopie zapasowe wykonane w czwarty poniedziałek i czwarty czwartek w lutym i listopadzie przez cztery lata (zastępuje poprzednie warunki przechowywania, jeśli takie istnieją).

```json
{
  "properties": {
    "backupManagementType": "AzureIaasVM",
    "timeZone": "Pacific Standard Time",
    "schedulePolicy": {
      "schedulePolicyType": "SimpleSchedulePolicy",
      "scheduleRunFrequency": "Weekly",
      "scheduleRunTimes": [
        "2018-01-24T10:00:00Z"
      ],
      "scheduleRunDays": [
        "Monday",
        "Wednesday",
        "Thursday"
      ]
    },
    "retentionPolicy": {
      "retentionPolicyType": "LongTermRetentionPolicy",
      "weeklySchedule": {
        "daysOfTheWeek": [
          "Monday",
          "Wednesday",
          "Thursday"
        ],
        "retentionTimes": [
          "2018-01-24T10:00:00Z"
        ],
        "retentionDuration": {
          "count": 1,
          "durationType": "Weeks"
        }
      },
      "monthlySchedule": {
        "retentionScheduleFormatType": "Weekly",
        "retentionScheduleWeekly": {
          "daysOfTheWeek": [
            "Wednesday",
            "Thursday"
          ],
          "weeksOfTheMonth": [
            "First",
            "Third"
          ]
        },
        "retentionTimes": [
          "2018-01-24T10:00:00Z"
        ],
        "retentionDuration": {
          "count": 2,
          "durationType": "Months"
        }
      },
      "yearlySchedule": {
        "retentionScheduleFormatType": "Weekly",
        "monthsOfYear": [
          "February",
          "November"
        ],
        "retentionScheduleWeekly": {
          "daysOfTheWeek": [
            "Monday",
            "Thursday"
          ],
          "weeksOfTheMonth": [
            "Fourth"
          ]
        },
        "retentionTimes": [
          "2018-01-24T10:00:00Z"
        ],
        "retentionDuration": {
          "count": 4,
          "durationType": "Years"
        }
      }
    }
  }
}
```

> [!IMPORTANT]
> Formaty czasu dla harmonogramu i przechowywania obsługują tylko DateTime. Nie obsługują one tylko formatu Time.

## <a name="responses"></a>Odpowiedzi

Tworzenie/aktualizowanie zasad kopii zapasowej jest [operacją asynchronizacyjną](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations). Oznacza to, że ta operacja tworzy inną operację, która musi być śledzona oddzielnie.

Zwraca dwie odpowiedzi: 202 (Zaakceptowane) podczas tworzenia innej operacji, a następnie 200 (OK) po zakończeniu tej operacji.

|Nazwa  |Typ  |Opis  |
|---------|---------|---------|
|200 ok.     |    [Zasady ochronyŹródło](/rest/api/backup/protectionpolicies/createorupdate#protectionpolicyresource)     |  OK       |
|202 Zaakceptowane     |         |     Zaakceptowane    |

### <a name="example-responses"></a>Przykładowe odpowiedzi

Po przesłaniu żądania PUT do tworzenia lub aktualizowania zasad początkowa odpowiedź jest 202 (zaakceptowana) z nagłówkiem lokalizacji lub nagłówkiem Azure-async.Once submit the *PUT* request for policy creation or updating, the initial response is 202 (Accepted) with a location header or Azure-async-header.

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
Azure-AsyncOperation: https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SwaggerTestRg/providers/Microsoft.RecoveryServices/vaults/testVault/backupPolicies/testPolicy1/operations/00000000-0000-0000-0000-000000000000?api-version=2016-06-01
X-Content-Type-Options: nosniff
x-ms-request-id: db785be0-bb20-4598-bc9f-70c9428b170b
x-ms-client-request-id: e1f94eef-9b2d-45c4-85b8-151e12b07d03; e1f94eef-9b2d-45c4-85b8-151e12b07d03
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: db785be0-bb20-4598-bc9f-70c9428b170b
x-ms-routing-request-id: SOUTHINDIA:20180521T073907Z:db785be0-bb20-4598-bc9f-70c9428b170b
Cache-Control: no-cache
Date: Mon, 21 May 2018 07:39:06 GMT
Location: https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SwaggerTestRg/providers/Microsoft.RecoveryServices/vaults/testVault/backupPolicies/testPolicy1/operationResults/00000000-0000-0000-0000-000000000000?api-version=2019-05-13
X-Powered-By: ASP.NET
```

Następnie śledź wynikową operację przy użyciu nagłówka lokalizacji lub nagłówka Azure-AsyncOperation za pomocą prostego polecenia *GET.*

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SwaggerTestRg/providers/Microsoft.RecoveryServices/vaults/testVault/backupPolicies/testPolicy1/operationResults/00000000-0000-0000-0000-000000000000?api-version=2019-05-13
```

Po zakończeniu operacji zwraca 200 (OK) z zawartością zasad w treści odpowiedzi.

```json
{
  "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SwaggerTestRg/providers/Microsoft.RecoveryServices/vaults/testVault/backupPolicies/testPolicy1",
  "name": "testPolicy1",
  "type": "Microsoft.RecoveryServices/vaults/backupPolicies",
  "properties": {
    "backupManagementType": "AzureIaasVM",
    "schedulePolicy": {
      "schedulePolicyType": "SimpleSchedulePolicy",
      "scheduleRunFrequency": "Weekly",
      "scheduleRunDays": [
        "Monday",
        "Wednesday",
        "Thursday"
      ],
      "scheduleRunTimes": [
        "2018-01-24T10:00:00Z"
      ],
      "scheduleWeeklyFrequency": 0
    },
    "retentionPolicy": {
      "retentionPolicyType": "LongTermRetentionPolicy",
      "weeklySchedule": {
        "daysOfTheWeek": [
          "Monday",
          "Wednesday",
          "Thursday"
        ],
        "retentionTimes": [
          "2018-01-24T10:00:00Z"
        ],
        "retentionDuration": {
          "count": 1,
          "durationType": "Weeks"
        }
      },
      "monthlySchedule": {
        "retentionScheduleFormatType": "Weekly",
        "retentionScheduleWeekly": {
          "daysOfTheWeek": [
            "Wednesday",
            "Thursday"
          ],
          "weeksOfTheMonth": [
            "First",
            "Third"
          ]
        },
        "retentionTimes": [
          "2018-01-24T10:00:00Z"
        ],
        "retentionDuration": {
          "count": 2,
          "durationType": "Months"
        }
      },
      "yearlySchedule": {
        "retentionScheduleFormatType": "Weekly",
        "monthsOfYear": [
          "February",
          "November"
        ],
        "retentionScheduleWeekly": {
          "daysOfTheWeek": [
            "Monday",
            "Thursday"
          ],
          "weeksOfTheMonth": [
            "Fourth"
          ]
        },
        "retentionTimes": [
          "2018-01-24T10:00:00Z"
        ],
        "retentionDuration": {
          "count": 4,
          "durationType": "Years"
        }
      }
    },
    "timeZone": "Pacific Standard Time",
    "protectedItemsCount": 0
  }
}
```

Jeśli zasada jest już używana do ochrony elementu, każda aktualizacja w zasadach spowoduje [zmodyfikowanie ochrony](backup-azure-arm-userestapi-backupazurevms.md#changing-the-policy-of-protection) dla wszystkich takich powiązanych elementów.

## <a name="next-steps"></a>Następne kroki

[Włącz ochronę niechronionej maszyny Wirtualnej platformy Azure](backup-azure-arm-userestapi-backupazurevms.md).

Aby uzyskać więcej informacji na temat interfejsów API rest kopii zapasowej platformy Azure, zobacz następujące dokumenty:

- [Interfejs API REST dostawcy usług odzyskiwania platformy Azure](/rest/api/recoveryservices/)
- [Get started with Azure REST API (Rozpoczęcie pracy z interfejsem API REST platformy Azure)](/rest/api/azure/)
