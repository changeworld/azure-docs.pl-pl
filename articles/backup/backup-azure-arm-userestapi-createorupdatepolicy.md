---
title: 'Usługa Azure Backup: Tworzenie zasad kopii zapasowych, za pomocą interfejsu API REST'
description: Zarządzanie zasadami kopii zapasowych (harmonogram i okres przechowywania) przy użyciu interfejsu API REST
services: backup
author: pvrk
manager: shivamg
keywords: INTERFEJS API REST; Kopia zapasowa maszyny Wirtualnej platformy Azure; Przywracanie maszyny Wirtualnej platformy Azure;
ms.service: backup
ms.topic: conceptual
ms.date: 08/21/2018
ms.author: pullabhk
ms.assetid: 5ffc4115-0ae5-4b85-a18c-8a942f6d4870
ms.openlocfilehash: 657a777da0e984a145c1c617a6194bf4ef56306e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60648809"
---
# <a name="create-azure-recovery-services-backup-policies-using-rest-api"></a>Tworzenie zasad kopii zapasowych usługi Azure Recovery Services, za pomocą interfejsu API REST

Kroki umożliwiające utworzenie zasad kopii zapasowych dla magazynu usług odzyskiwania Azure zostały opisane w [zasad interfejsu API REST dokumentu](https://docs.microsoft.com/rest/api/backup/protectionpolicies/createorupdate). Daj nam należy używać tego dokumentu jako odwołanie, aby utworzyć zasady dla kopii zapasowej maszyny Wirtualnej platformy Azure.

## <a name="backup-policy-essentials"></a>Podstawowe informacje dotyczące zasad tworzenia kopii zapasowej

- Zasady tworzenia kopii zapasowych jest tworzony w jednym magazynie.
- Zasady tworzenia kopii zapasowych można utworzyć kopii zapasowej z następującymi pakietami roboczymi
  - Maszyna wirtualna platformy Azure
  - SQL na maszynie wirtualnej platformy Azure
  - Udział plików platformy Azure
- Zasady można przypisać do wielu zasobów. Zasady tworzenia kopii zapasowej maszyny Wirtualnej platformy Azure może służyć do ochrony wielu maszyn wirtualnych platformy Azure.
- Zasady składa się z dwóch składników:
  - Harmonogram: Kiedy należy wykonać kopię zapasową
  - Przechowywanie: Jak długo każda kopia zapasowa ma być przechowywana.
- Harmonogram można zdefiniować jako "codziennie" lub "co tydzień" z określonego punktu w czasie.
- Przechowywania mogą być definiowane dla "dzienny", "cotygodniowe", "miesięczne", "roczne" punktów kopii zapasowej.
- "cotygodniowe" odnosi się do kopii zapasowej w określonym dniu tygodnia, "miesięczne" oznacza, że tworzenie kopii zapasowej w określonym dniu miesiąca, i "roczne" odnosi się do kopii zapasowej na dzień roku.
- Przechowywanie punktów kopii zapasowej "miesięczna", "roczne", jest nazywana "LongTermRetention".
- Po utworzeniu magazynu zasad tworzenia kopii zapasowych maszyn wirtualnych platformy Azure o nazwie "DefaultPolicy" jest tworzona i może służyć do tworzenia kopii zapasowych maszyn wirtualnych platformy Azure.

Aby utworzyć lub zaktualizować zasady usługi Azure Backup, należy użyć następującego *umieścić* operacji

```http
PUT https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupPolicies/{policyName}?api-version=2016-12-01
```

`{policyName}` i `{vaultName}` znajdują się w identyfikatorze URI. Dodatkowe informacje są udostępniane w treści żądania.

## <a name="create-the-request-body"></a>Tworzenie treści żądania

Na przykład aby utworzyć zasady dla kopii zapasowej maszyny Wirtualnej platformy Azure, poniżej przedstawiono składniki w treści żądania.

|Name (Nazwa)  |Wymagane  |Typ  |Opis  |
|---------|---------|---------|---------|
|properties     |   True      |  ProtectionPolicy:[AzureIaaSVMProtectionPolicy](https://docs.microsoft.com/rest/api/backup/protectionpolicies/createorupdate#azureiaasvmprotectionpolicy)      | Właściwości ProtectionPolicyResource        |
|tags     |         | Object        |  Tagi zasobów       |

Aby uzyskać pełną listę definicji w treści żądania, zobacz [dokument interfejsu API REST zasad tworzenia kopii zapasowej](https://docs.microsoft.com/rest/api/backup/protectionpolicies/createorupdate).

### <a name="example-request-body"></a>Przykład treść żądania

Następującą treść żądania określa zasady tworzenia kopii zapasowych dla kopii zapasowych maszyn wirtualnych platformy Azure.

Zasady jest wyświetlany komunikat:

- Tworzenie kopii zapasowej co tydzień w każdy poniedziałek, środę, czwartek o godzinie 10:00:00 i Pacyfik (czas standardowy).
- Zachowaj kopie zapasowe wykonane w każdy poniedziałek, środę, czwartek przez jeden tydzień.
- Zachowaj kopie zapasowe wykonane w każdym pierwszy wtorek i czwartek trzeciego miesiąca przez dwa miesiące (zastąpienia poprzednie warunki przechowywania, jeśli istnieje).
- Zachowaj kopie zapasowe wykonane w poniedziałek czwarty i czwarty czwartek w lutym i listopada przez cztery lata (zastąpienia poprzednie warunki przechowywania, jeśli istnieje).

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
> Formaty czasu harmonogram i okres przechowywania obsługuje tylko daty/godziny. Nie obsługują tylko format godziny.

## <a name="responses"></a>Responses

Zasady tworzenia kopii zapasowej Tworzenie/aktualizowanie jest [operację asynchroniczną](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations). Oznacza to, że ta operacja tworzy inną operację, która musi być śledzona oddzielnie.

Zwraca ona dwie odpowiedzi: 202 (zaakceptowano), gdy inna operacja zostanie utworzony, a następnie 200 (OK), po zakończeniu tej operacji.

|Name (Nazwa)  |Typ  |Opis  |
|---------|---------|---------|
|200 OK     |    [PolicyResource ochrony](https://docs.microsoft.com/rest/api/backup/protectionpolicies/createorupdate#protectionpolicyresource)     |  OK       |
|202 zaakceptowano     |         |     Zaakceptowane    |

### <a name="example-responses"></a>Przykładowe odpowiedzi

Po przesłaniu *umieścić* żądania do tworzenia zasad lub aktualizowania, początkową odpowiedź jest 202 (zaakceptowano) za pomocą nagłówek lokalizacji lub Azure async nagłówka.

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

Następnie śledzenia wynikowych operacji przy użyciu nagłówek lokalizacji lub nagłówek operację asynchroniczną na platformie Azure przy użyciu prostego *UZYSKAĆ* polecenia.

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SwaggerTestRg/providers/Microsoft.RecoveryServices/vaults/testVault/backupPolicies/testPolicy1/operationResults/00000000-0000-0000-0000-000000000000?api-version=2016-06-01
```

Po zakończeniu tej operacji zwraca 200 (OK) z zawartością zasad w treści odpowiedzi.

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

Zasady ochrony element jest już używana, dowolna aktualizacja w ramach zasad spowoduje [modyfikowania ochrony](backup-azure-arm-userestapi-backupazurevms.md#changing-the-policy-of-protection) dla wszystkich takich elementów skojarzonych.

## <a name="next-steps"></a>Kolejne kroki

[Włącz ochronę dla niechronionych maszyn wirtualnych platformy Azure](backup-azure-arm-userestapi-backupazurevms.md).

Więcej informacji na temat interfejsów API REST usługi Azure Backup można znaleźć w następujących dokumentach:

- [Interfejs API REST platformy Azure w zakresie dostawcy usługi Recovery Services](/rest/api/recoveryservices/)
- [Wprowadzenie do interfejsu API REST platformy Azure](/rest/api/azure/)