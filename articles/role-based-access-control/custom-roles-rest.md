---
title: Tworzenie lub aktualizowanie ról niestandardowych zasobów platformy Azure za pomocą interfejsu API REST
description: Dowiedz się, jak listy, tworzenia, aktualizowania lub usuwania ról niestandardowych z kontroli dostępu opartej na rolach (RBAC) dla zasobów platformy Azure przy użyciu interfejsu API REST.
services: active-directory
documentationcenter: na
author: rolyon
manager: mtillman
editor: ''
ms.assetid: 1f90228a-7aac-4ea7-ad82-b57d222ab128
ms.service: role-based-access-control
ms.workload: multiple
ms.tgt_pltfrm: rest-api
ms.devlang: na
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: fda0400310f46da64322654c42af75521746d679
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062185"
---
# <a name="create-or-update-custom-roles-for-azure-resources-using-the-rest-api"></a>Tworzenie lub aktualizowanie ról niestandardowych zasobów platformy Azure przy użyciu interfejsu API REST

> [!IMPORTANT]
> Dodawanie grupy zarządzania do `AssignableScopes` jest obecnie w wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Jeśli [wbudowane role dla zasobów platformy Azure](built-in-roles.md) nie spełniają potrzeb Twojej organizacji, możesz tworzyć własne role niestandardowe. W tym artykule opisano sposób listy, tworzenia, aktualizowania lub usuwania ról niestandardowych przy użyciu interfejsu API REST.

## <a name="list-custom-roles"></a>Wyświetlanie ról niestandardowych

Aby wyświetlić listę wszystkich ról niestandardowych w katalogu, użyj interfejsu API [Definicje ról — Lista](/rest/api/authorization/roledefinitions/list) REST.

1. Zacznij od następującego żądania:

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. Zastąp *{filter}* typem roli.

    > [!div class="mx-tableFixed"]
    > | Filtr | Opis |
    > | --- | --- |
    > | `$filter=type+eq+'CustomRole'` | Filtr na podstawie typu CustomRole |

## <a name="list-custom-roles-at-a-scope"></a>Wyświetlanie ról niestandardowych w zakresie

Aby wyświetlić listę ról niestandardowych w zakresie, użyj interfejsu API [definicje ról — lista](/rest/api/authorization/roledefinitions/list) REST.

1. Zacznij od następującego żądania:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. W ramach identyfikatora URI zastąp *{scope}* zakresem, dla którego chcesz wyświetlić listę ról.

    > [!div class="mx-tableFixed"]
    > | Zakres | Typ |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Subskrypcja |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Grupa zasobów |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}/providers/Microsoft.Web/sites/{site1}` | Zasób |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Grupa zarządzania |

1. Zastąp *{filter}* typem roli.

    > [!div class="mx-tableFixed"]
    > | Filtr | Opis |
    > | --- | --- |
    > | `$filter=type+eq+'CustomRole'` | Filtr na podstawie typu CustomRole |

## <a name="list-a-custom-role-definition-by-name"></a>Wyświetlanie nazwy definicji roli niestandardowej

Aby uzyskać informacje o roli niestandardowej według jej nazwy wyświetlanej, użyj [definicji ról — Pobierz](/rest/api/authorization/roledefinitions/get) interfejs API REST.

1. Zacznij od następującego żądania:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. W ramach identyfikatora URI zastąp *{scope}* zakresem, dla którego chcesz wyświetlić listę ról.

    > [!div class="mx-tableFixed"]
    > | Zakres | Typ |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Subskrypcja |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Grupa zasobów |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}/providers/Microsoft.Web/sites/{site1}` | Zasób |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Grupa zarządzania |

1. Zastąp *{filter}* nazwą wyświetlaną roli.

    > [!div class="mx-tableFixed"]
    > | Filtr | Opis |
    > | --- | --- |
    > | `$filter=roleName+eq+'{roleDisplayName}'` | Użyj formularza zakodowanego w adresie URL dokładnej nazwy wyświetlanej roli. Na przykład,`$filter=roleName+eq+'Virtual%20Machine%20Contributor'` |

## <a name="list-a-custom-role-definition-by-id"></a>Wyświetlanie wylińczania niestandardowej definicji roli według identyfikatora

Aby uzyskać informacje o roli niestandardowej przez jego unikatowy identyfikator, należy użyć [definicje ról — Pobierz](/rest/api/authorization/roledefinitions/get) interfejs API REST.

1. Użyj interfejsu API [definicje ról — lista](/rest/api/authorization/roledefinitions/list) REST, aby uzyskać identyfikator guid dla roli.

1. Zacznij od następującego żądania:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. W ramach identyfikatora URI zastąp *{scope}* zakresem, dla którego chcesz wyświetlić listę ról.

    > [!div class="mx-tableFixed"]
    > | Zakres | Typ |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Subskrypcja |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Grupa zasobów |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}/providers/Microsoft.Web/sites/{site1}` | Zasób |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Grupa zarządzania |

1. Zastąp *{roleDefinitionId}* identyfikatorem GUID definicji roli.

## <a name="create-a-custom-role"></a>Tworzenie roli niestandardowej

Aby utworzyć rolę niestandardową, użyj interfejsu API [Definicje ról — tworzenie lub aktualizowanie](/rest/api/authorization/roledefinitions/createorupdate) rest. Aby wywołać ten interfejs API, musisz zalogować się z użytkownikiem, który ma przypisaną rolę, która ma `Microsoft.Authorization/roleDefinitions/write` uprawnienia do wszystkich `assignableScopes`. Z wbudowanych ról tylko [administrator dostępu właściciela](built-in-roles.md#owner) i [użytkownika](built-in-roles.md#user-access-administrator) dołącza to uprawnienie.

1. Przejrzyj listę [operacji dostawcy zasobów,](resource-provider-operations.md) które są dostępne do tworzenia uprawnień dla roli niestandardowej.

1. Narzędzie GUID służy do generowania unikatowego identyfikatora, który będzie używany dla niestandardowego identyfikatora roli. Identyfikator ma format:`00000000-0000-0000-0000-000000000000`

1. Zacznij od następującego żądania i treści:

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

    ```json
    {
      "name": "{roleDefinitionId}",
      "properties": {
        "roleName": "",
        "description": "",
        "type": "CustomRole",
        "permissions": [
          {
            "actions": [
    
            ],
            "notActions": [
    
            ]
          }
        ],
        "assignableScopes": [
          "/subscriptions/{subscriptionId1}",
          "/subscriptions/{subscriptionId2}",
          "/subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}",
          "/subscriptions/{subscriptionId2}/resourceGroups/{resourceGroup2}",
          "/providers/Microsoft.Management/managementGroups/{groupId1}"
        ]
      }
    }
    ```

1. W ramach identyfikatora URI zastąp *{scope}* pierwszą `assignableScopes` rolą niestandardową.

    > [!div class="mx-tableFixed"]
    > | Zakres | Typ |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Subskrypcja |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Grupa zasobów |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Grupa zarządzania |

1. Zastąp *{roleDefinitionId}* identyfikatorem GUID roli niestandardowej.

1. W treści żądania zastąp *{roleDefinitionId}* identyfikatorem GUID.

1. Jeśli `assignableScopes` jest to subskrypcja lub grupa zasobów, zastąp wystąpienia *{subscriptionId}* lub *{resourceGroup}* identyfikatorami.

1. Jeśli `assignableScopes` jest to grupa zarządzania, zastąp wystąpienie *{groupId}* identyfikatorem grupy zarządzania. Dodawanie grupy zarządzania do `assignableScopes` jest obecnie w wersji zapoznawczej.

1. W `actions` właściwości dodaj operacje, które umożliwia wykonanie roli.

1. W `notActions` właściwości dodaj operacje, które są wykluczone z dozwolonej `actions`.

1. W `roleName` i `description` właściwości, określ unikatową nazwę roli i opis. Aby uzyskać więcej informacji na temat właściwości, zobacz [Role niestandardowe](custom-roles.md).

    Poniżej przedstawiono przykład treści żądania:

    ```json
    {
      "name": "88888888-8888-8888-8888-888888888888",
      "properties": {
        "roleName": "Virtual Machine Operator",
        "description": "Can monitor and restart virtual machines.",
        "type": "CustomRole",
        "permissions": [
          {
            "actions": [
              "Microsoft.Storage/*/read",
              "Microsoft.Network/*/read",
              "Microsoft.Compute/*/read",
              "Microsoft.Compute/virtualMachines/start/action",
              "Microsoft.Compute/virtualMachines/restart/action",
              "Microsoft.Authorization/*/read",
              "Microsoft.ResourceHealth/availabilityStatuses/read",
              "Microsoft.Resources/subscriptions/resourceGroups/read",
              "Microsoft.Insights/alertRules/*",
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "assignableScopes": [
          "/subscriptions/00000000-0000-0000-0000-000000000000",
          "/providers/Microsoft.Management/managementGroups/marketing-group"
        ]
      }
    }
    ```

## <a name="update-a-custom-role"></a>Aktualizacja roli niestandardowej

Aby zaktualizować rolę niestandardową, użyj interfejsu API [Definicje ról — tworzenie lub aktualizowanie](/rest/api/authorization/roledefinitions/createorupdate) rest. Aby wywołać ten interfejs API, musisz zalogować się z użytkownikiem, który ma przypisaną rolę, która ma `Microsoft.Authorization/roleDefinitions/write` uprawnienia do wszystkich `assignableScopes`. Z wbudowanych ról tylko [administrator dostępu właściciela](built-in-roles.md#owner) i [użytkownika](built-in-roles.md#user-access-administrator) dołącza to uprawnienie.

1. Użyj [definicji ról — lista](/rest/api/authorization/roledefinitions/list) lub [definicje ról — Pobierz](/rest/api/authorization/roledefinitions/get) interfejs API REST, aby uzyskać informacje o roli niestandardowej. Aby uzyskać więcej informacji, zobacz sekcję [Role niestandardowe listy.](#list-custom-roles)

1. Zacznij od następującego żądania:

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. W ramach identyfikatora URI zastąp *{scope}* pierwszą `assignableScopes` rolą niestandardową.

    > [!div class="mx-tableFixed"]
    > | Zakres | Typ |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Subskrypcja |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Grupa zasobów |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Grupa zarządzania |

1. Zastąp *{roleDefinitionId}* identyfikatorem GUID roli niestandardowej.

1. Na podstawie informacji o roli niestandardowej utwórz treść żądania o następującym formacie:

    ```json
    {
      "name": "{roleDefinitionId}",
      "properties": {
        "roleName": "",
        "description": "",
        "type": "CustomRole",
        "permissions": [
          {
            "actions": [
    
            ],
            "notActions": [
    
            ]
          }
        ],
        "assignableScopes": [
          "/subscriptions/{subscriptionId1}",
          "/subscriptions/{subscriptionId2}",
          "/subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}",
          "/subscriptions/{subscriptionId2}/resourceGroups/{resourceGroup2}",
          "/providers/Microsoft.Management/managementGroups/{groupId1}"
        ]
      }
    }
    ```

1. Zaktualizuj treść żądania o zmiany, które chcesz wprowadzić w roli niestandardowej.

    Poniżej przedstawiono przykład treści żądania z nową akcją ustawień diagnostycznych:

    ```json
    {
      "name": "88888888-8888-8888-8888-888888888888",
      "properties": {
        "roleName": "Virtual Machine Operator",
        "description": "Can monitor and restart virtual machines.",
        "type": "CustomRole",
        "permissions": [
          {
            "actions": [
              "Microsoft.Storage/*/read",
              "Microsoft.Network/*/read",
              "Microsoft.Compute/*/read",
              "Microsoft.Compute/virtualMachines/start/action",
              "Microsoft.Compute/virtualMachines/restart/action",
              "Microsoft.Authorization/*/read",
              "Microsoft.ResourceHealth/availabilityStatuses/read",
              "Microsoft.Resources/subscriptions/resourceGroups/read",
              "Microsoft.Insights/alertRules/*",
              "Microsoft.Insights/diagnosticSettings/*",
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "assignableScopes": [
          "/subscriptions/00000000-0000-0000-0000-000000000000",
          "/providers/Microsoft.Management/managementGroups/marketing-group"
        ]
      }
    }
    ```

## <a name="delete-a-custom-role"></a>Usuwanie roli niestandardowej

Aby usunąć rolę niestandardową, użyj interfejsu API [Definicje ról — Usuń](/rest/api/authorization/roledefinitions/delete) REST. Aby wywołać ten interfejs API, musisz zalogować się z użytkownikiem, który ma przypisaną rolę, która ma `Microsoft.Authorization/roleDefinitions/delete` uprawnienia do wszystkich `assignableScopes`. Z wbudowanych ról tylko [administrator dostępu właściciela](built-in-roles.md#owner) i [użytkownika](built-in-roles.md#user-access-administrator) dołącza to uprawnienie.

1. Użyj [definicji ról — lista](/rest/api/authorization/roledefinitions/list) lub [definicje ról — Pobierz](/rest/api/authorization/roledefinitions/get) interfejs API REST, aby uzyskać identyfikator GUID roli niestandardowej. Aby uzyskać więcej informacji, zobacz sekcję [Role niestandardowe listy.](#list-custom-roles)

1. Zacznij od następującego żądania:

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. W ramach identyfikatora URI zastąp *{scope}* zakresem, który chcesz usunąć rolę niestandardową.

    > [!div class="mx-tableFixed"]
    > | Zakres | Typ |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Subskrypcja |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Grupa zasobów |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Grupa zarządzania |

1. Zastąp *{roleDefinitionId}* identyfikatorem GUID roli niestandardowej.

## <a name="next-steps"></a>Następne kroki

- [Niestandardowe role dla zasobów platformy Azure](custom-roles.md)
- [Zarządzanie dostępem do zasobów platformy Azure przy użyciu interfejsu RBAC i interfejsu API REST](role-assignments-rest.md)
- [Odwołanie interfejsu API usługi Azure REST](/rest/api/azure/)
