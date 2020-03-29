---
title: Zabezpieczenia i uwierzytelnianie usługi Azure Event Grid
description: Opis usługi Azure Event Grid i pojęć z nią związanych.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: babanisa
ms.openlocfilehash: 03bc2f9de6f50f08c9f62f86a3d1791a067cecd0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78899284"
---
# <a name="authorizing-access-to-event-grid-resources"></a>Autoryzowanie dostępu do zasobów siatki zdarzeń
Usługa Azure Event Grid umożliwia kontrolowanie poziomu dostępu przyznanego różnym użytkownikom w celu wykonywania różnych operacji zarządzania, takich jak subskrypcje zdarzeń listy, tworzenie nowych i generowanie kluczy. Usługa Event Grid używa kontroli dostępu opartej na rolach platformy Azure (RBAC).

## <a name="operation-types"></a>Typy operacji

Usługa Event Grid obsługuje następujące akcje:

* Microsoft.EventGrid/*/odczyt
* Microsoft.EventGrid/*/napisz
* Microsoft.EventGrid/*/delete
* Microsoft.EventGrid/eventSubscriptions/getFullUrl/action
* Microsoft.EventGrid/topics/listKeys/action
* Microsoft.EventGrid/topics/regenerateKey/action

Ostatnie trzy operacje zwracają potencjalnie tajne informacje, które są filtrowane z normalnych operacji odczytu. Zaleca się ograniczenie dostępu do tych operacji. 

## <a name="built-in-roles"></a>Wbudowane role

Usługa Event Grid udostępnia dwie wbudowane role do zarządzania subskrypcjami zdarzeń. Są one ważne podczas implementowania [domen zdarzeń,](event-domains.md) ponieważ dają użytkownikom uprawnienia potrzebne do subskrybowania tematów w domenie zdarzeń. Te role koncentrują się na subskrypcjach zdarzeń i nie udzielają dostępu do akcji, takich jak tworzenie tematów.

Te [role](../role-based-access-control/quickstart-assign-role-user-portal.md)można przypisać do użytkownika lub grupy .

**EventGrid EventSubscription Contributor**: zarządzanie operacjami subskrypcji usługi Event Grid

```json
[
  {
    "Description": "Lets you manage EventGrid event subscription operations.",
    "IsBuiltIn": true,
    "Id": "428e0ff05e574d9ca2212c70d0e0a443",
    "Name": "EventGrid EventSubscription Contributor",
    "IsServiceRole": false,
    "Permissions": [
      {
        "Actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.EventGrid/eventSubscriptions/*",
          "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Resources/deployments/*",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Support/*"
        ],
        "NotActions": [],
        "DataActions": [],
        "NotDataActions": [],
        "Condition": null
      }
    ],
    "Scopes": [
      "/"
    ]
  }
]
```

**EventGrid EventSubscription Reader**: przeczytaj subskrypcje usługi Event Grid

```json
[
  {
    "Description": "Lets you read EventGrid event subscriptions.",
    "IsBuiltIn": true,
    "Id": "2414bbcf64974faf8c65045460748405",
    "Name": "EventGrid EventSubscription Reader",
    "IsServiceRole": false,
    "Permissions": [
      {
        "Actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.EventGrid/eventSubscriptions/read",
          "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read"
        ],
        "NotActions": [],
        "DataActions": [],
        "NotDataActions": []
       }
    ],
    "Scopes": [
      "/"
    ]
  }
]
```

## <a name="custom-roles"></a>Role niestandardowe

Jeśli trzeba określić uprawnienia, które są inne niż wbudowane role, można utworzyć role niestandardowe.

Poniżej przedstawiono przykładowe definicje ról siatki zdarzeń, które umożliwiają użytkownikom podejmowanie różnych akcji. Te role niestandardowe różnią się od ról wbudowanych, ponieważ zapewniają szerszy dostęp niż tylko subskrypcje zdarzeń.

**EventGridReadOnlyRole.json:** Zezwalaj tylko na operacje tylko do odczytu.

```json
{
  "Name": "Event grid read only role",
  "Id": "7C0B6B59-A278-4B62-BA19-411B70753856",
  "IsCustom": true,
  "Description": "Event grid read only role",
  "Actions": [
    "Microsoft.EventGrid/*/read"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<Subscription Id>"
  ]
}
```

**EventGridNoDeleteListKeysRole.json**: Zezwalaj na akcje wpisów z ograniczeniami, ale nie zezwalaj na usuwanie akcji.

```json
{
  "Name": "Event grid No Delete Listkeys role",
  "Id": "B9170838-5F9D-4103-A1DE-60496F7C9174",
  "IsCustom": true,
  "Description": "Event grid No Delete Listkeys role",
  "Actions": [
    "Microsoft.EventGrid/*/write",
    "Microsoft.EventGrid/eventSubscriptions/getFullUrl/action"
    "Microsoft.EventGrid/topics/listkeys/action",
    "Microsoft.EventGrid/topics/regenerateKey/action"
  ],
  "NotActions": [
    "Microsoft.EventGrid/*/delete"
  ],
  "AssignableScopes": [
    "/subscriptions/<Subscription id>"
  ]
}
```

**EventGridContributorRole.json**: Umożliwia wszystkie akcje siatki zdarzeń.

```json
{
  "Name": "Event grid contributor role",
  "Id": "4BA6FB33-2955-491B-A74F-53C9126C9514",
  "IsCustom": true,
  "Description": "Event grid contributor role",
  "Actions": [
    "Microsoft.EventGrid/*/write",
    "Microsoft.EventGrid/*/delete",
    "Microsoft.EventGrid/topics/listkeys/action",
    "Microsoft.EventGrid/topics/regenerateKey/action",
    "Microsoft.EventGrid/eventSubscriptions/getFullUrl/action"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/<Subscription id>"
  ]
}
```

Role niestandardowe można tworzyć za pomocą programów [PowerShell,](../role-based-access-control/custom-roles-powershell.md) [Azure CLI](../role-based-access-control/custom-roles-cli.md)i [REST](../role-based-access-control/custom-roles-rest.md).



### <a name="encryption-at-rest"></a>Szyfrowanie w spoczynku

Wszystkie zdarzenia lub dane zapisane na dysku przez usługę Event Grid są szyfrowane przez klucz zarządzany przez firmę Microsoft, zapewniając, że jest zaszyfrowany w spoczynku. Ponadto maksymalny okres przechowywania zdarzeń lub danych wynosi 24 godziny w przestrzeganiu [zasad ponawiania prób w siatce zdarzeń.](delivery-and-retry.md) Usługa Event Grid automatycznie usunie wszystkie zdarzenia lub dane po 24 godzinach lub czas zdarzenia na żywo, w zależności od tego, która z tych wartości jest mniejsza.

## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z wprowadzeniem do siatki zdarzeń, zobacz [Informacje o siatce zdarzeń](overview.md)
