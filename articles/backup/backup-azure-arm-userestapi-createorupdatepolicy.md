---
title: 'Azure Backup: Tworzenie zasad kopii zapasowych przy użyciu interfejsu API REST'
description: Zarządzanie zasadami tworzenia kopii zapasowych (harmonogram i przechowywanie) przy użyciu interfejsu API REST
ms.reviewer: pullabhk
author: dcurwin
manager: carmonm
keywords: INTERFEJS API REST; Kopia zapasowa maszyny wirtualnej platformy Azure; Przywracanie maszyny wirtualnej platformy Azure;
ms.service: backup
ms.topic: conceptual
ms.date: 08/21/2018
ms.author: dacurwin
ms.assetid: 5ffc4115-0ae5-4b85-a18c-8a942f6d4870
ms.openlocfilehash: 8b812ea053cb8e9da7cd3ef021ab6b74196d36ca
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/12/2019
ms.locfileid: "68954967"
---
# <a name="create-azure-recovery-services-backup-policies-using-rest-api"></a>Tworzenie zasad usługi Kopia zapasowa Azure Recovery Services przy użyciu interfejsu API REST

Kroki tworzenia zasad tworzenia kopii zapasowych dla magazynu usługi Azure Recovery Services są opisane w [dokumencie interfejsu API REST zasad](https://docs.microsoft.com/rest/api/backup/protectionpolicies/createorupdate). Poinformuj nas o tym, jak utworzyć zasady dla kopii zapasowej maszyny wirtualnej platformy Azure.

## <a name="backup-policy-essentials"></a>Podstawy zasad tworzenia kopii zapasowych

- Tworzone są zasady tworzenia kopii zapasowych na magazyn.
- Zasady tworzenia kopii zapasowych można utworzyć dla tworzenia kopii zapasowych następujących obciążeń
  - Azure VM
  - SQL na maszynie wirtualnej platformy Azure
  - Udział plików platformy Azure
- Zasady mogą być przypisane do wielu zasobów. Zasady kopii zapasowych maszyny wirtualnej platformy Azure mogą służyć do ochrony wielu maszyn wirtualnych platformy Azure.
- Zasady składają się z dwóch składników
  - Rozkład Kiedy należy wykonać kopię zapasową
  - Noszą Czas przechowywania poszczególnych kopii zapasowych.
- Harmonogram może być definiowany jako "dzienny" lub "tygodniowy" w określonym punkcie czasu.
- Przechowywanie można zdefiniować dla punktów kopii zapasowych "dziennych", "cotygodniowych", "Monthly", "rocznie".
- "tydzień" odnosi się do kopii zapasowej w określonym dniu tygodnia, "miesięcznie" oznacza, że kopia zapasowa w określonym dniu miesiąca i "corocznie" odnosi się do kopii zapasowej w określonym dniu roku.
- Przechowywanie "miesięcznie", "rocznie" punktów kopii zapasowych jest określane jako "LongTermRetention".
- Po utworzeniu magazynu tworzone są również zasady tworzenia kopii zapasowych maszyny wirtualnej platformy Azure o nazwie "DefaultPolicy" i mogą służyć do tworzenia kopii zapasowych maszyn wirtualnych platformy Azure.

Aby utworzyć lub zaktualizować zasady Azure Backup, użyj następującej operacji *Put*

```http
PUT https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupPolicies/{policyName}?api-version=2016-12-01
```

`{policyName}` I`{vaultName}` są podane w identyfikatorze URI. Dodatkowe informacje znajdują się w treści żądania.

## <a name="create-the-request-body"></a>Tworzenie treści żądania

Na przykład, aby utworzyć zasady kopii zapasowej maszyny wirtualnej platformy Azure, poniżej przedstawiono składniki treści żądania.

|Name (Nazwa)  |Wymagane  |Typ  |Opis  |
|---------|---------|---------|---------|
|properties     |   Prawda      |  ProtectionPolicy:[AzureIaaSVMProtectionPolicy](https://docs.microsoft.com/rest/api/backup/protectionpolicies/createorupdate#azureiaasvmprotectionpolicy)      | Właściwości ProtectionPolicyResource        |
|tags     |         | Object        |  Tagi zasobów       |

Aby zapoznać się z pełną listą definicji w treści żądania, zapoznaj się z [dokumentem interfejsu API REST zasad tworzenia kopii zapasowych](https://docs.microsoft.com/rest/api/backup/protectionpolicies/createorupdate).

### <a name="example-request-body"></a>Przykładowa treść żądania

Następująca treść żądania definiuje zasady tworzenia kopii zapasowych maszyn wirtualnych platformy Azure.

Zasady są następujące:

- Zrób cotygodniową kopię zapasową co poniedziałek, środa, czwartek o godzinie 10:00 czasu pacyficznego.
- Przechowuj kopie zapasowe wykonane w każdy poniedziałek, środa, czwartek przez tydzień.
- Przechowuj kopie zapasowe wykonane w każdej pierwszej środę i trzeciego czwartek miesiąca przez dwa miesiące (zastępuje poprzednie warunki przechowywania, jeśli istnieją).
- Zachowaj kopie zapasowe wykonane w czwartym poniedziałek i czwartym czwartek w lutym i listopadzie przez cztery lata (jeśli istnieją).

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
> Formaty czasu dla usługi Harmonogram i przechowywanie obsługują tylko datę i godzinę. Nie obsługują one wyłącznie formatu czasu.

## <a name="responses"></a>Responses

Tworzenie/aktualizowanie zasad kopii zapasowych jest [operacją asynchroniczną](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations). Oznacza to, że ta operacja tworzy kolejną operację, która musi być śledzona oddzielnie.

Zwraca dwie odpowiedzi: 202 (zaakceptowane) podczas tworzenia innej operacji, a następnie 200 (OK) po zakończeniu tej operacji.

|Name (Nazwa)  |Typ  |Opis  |
|---------|---------|---------|
|200 OK     |    [PolicyResource ochrony](https://docs.microsoft.com/rest/api/backup/protectionpolicies/createorupdate#protectionpolicyresource)     |  OK       |
|202 zaakceptowane     |         |     Zaakceptowany    |

### <a name="example-responses"></a>Przykładowe odpowiedzi

Po przesłaniu żądania *Put* w celu utworzenia lub zaktualizowania zasad początkowa odpowiedź to 202 (zaakceptowany) z nagłówkiem lokalizacji lub z nagłówkiem Azure-Async-header.

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
Location: https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SwaggerTestRg/providers/Microsoft.RecoveryServices/vaults/testVault/backupPolicies/testPolicy1/operationResults/00000000-0000-0000-0000-000000000000?api-version=2016-06-01
X-Powered-By: ASP.NET
```

Następnie Śledź wyniki operacji przy użyciu nagłówka lokalizacji lub nagłówka Azure-AsyncOperation z prostym poleceniem *Get* .

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SwaggerTestRg/providers/Microsoft.RecoveryServices/vaults/testVault/backupPolicies/testPolicy1/operationResults/00000000-0000-0000-0000-000000000000?api-version=2016-06-01
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

Jeśli zasady są już używane do ochrony elementu, każda aktualizacja zasad spowoduje [zmodyfikowanie ochrony](backup-azure-arm-userestapi-backupazurevms.md#changing-the-policy-of-protection) dla wszystkich takich skojarzonych elementów.

## <a name="next-steps"></a>Następne kroki

[Włącz ochronę niechronionej maszyny wirtualnej platformy Azure](backup-azure-arm-userestapi-backupazurevms.md).

Aby uzyskać więcej informacji na temat Azure Backup interfejsów API REST, zapoznaj się z następującymi dokumentami:

- [Interfejs API REST dostawcy usługi Azure Recovery Services](/rest/api/recoveryservices/)
- [Wprowadzenie do interfejsu API REST platformy Azure](/rest/api/azure/)