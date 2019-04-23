---
title: Tworzenie ról niestandardowych dla zasobów platformy Azure przy użyciu interfejsu API REST — Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć niestandardowe role przy użyciu kontroli dostępu opartej na rolach (RBAC) dla zasobów platformy Azure przy użyciu interfejsu API REST. W tym jak listy, tworzenia, aktualizacji i usuwania ról niestandardowych.
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
ms.date: 04/18/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 4024f6fdb40c752ef61f348d15f681e81d81c08c
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2019
ms.locfileid: "59999778"
---
# <a name="create-custom-roles-for-azure-resources-using-the-rest-api"></a>Tworzenie ról niestandardowych dla zasobów platformy Azure przy użyciu interfejsu API REST

Jeśli [wbudowane role dla zasobów platformy Azure](built-in-roles.md) nie spełniają potrzeb Twojej organizacji, możesz tworzyć własne role niestandardowe. W tym artykule opisano, jak tworzyć i zarządzać nimi przy użyciu interfejsu API REST ról niestandardowych.

## <a name="list-custom-roles"></a>Wyświetlanie ról niestandardowych

Aby wyświetlić listę wszystkich ról niestandardowych w katalogu, należy użyć [definicje ról — lista](/rest/api/authorization/roledefinitions/list) interfejsu API REST.

1. Uruchom przy użyciu następującego żądania:

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. Zastąp *{filter}* typu Rola.

    | Filtr | Opis |
    | --- | --- |
    | `$filter=type%20eq%20'CustomRole'` | Filtr oparty na typie CustomRole |

## <a name="list-custom-roles-at-a-scope"></a>Lista ról niestandardowych w zakresie

Aby wyświetlić listę ról niestandardowych w zakresie, należy użyć [definicje ról — lista](/rest/api/authorization/roledefinitions/list) interfejsu API REST.

1. Uruchom przy użyciu następującego żądania:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. W identyfikatorze URI, Zastąp *{zakresu}* z zakresem, dla którego chcesz wyświetlić listę ról.

    | Zakres | Typ |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Subskrypcja |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Grupa zasobów |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Zasób |

1. Zastąp *{filter}* typu Rola.

    | Filtr | Opis |
    | --- | --- |
    | `$filter=type%20eq%20'CustomRole'` | Filtr oparty na typie CustomRole |

## <a name="list-a-custom-role-definition-by-name"></a>Listy niestandardową definicję roli według nazwy

Aby uzyskać informacje o niestandardowych rolach według jego nazwy wyświetlanej, należy użyć [uzyskać definicje ról —](/rest/api/authorization/roledefinitions/get) interfejsu API REST.

1. Uruchom przy użyciu następującego żądania:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. W identyfikatorze URI, Zastąp *{zakresu}* z zakresem, dla którego chcesz wyświetlić listę ról.

    | Zakres | Typ |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Subskrypcja |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Grupa zasobów |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Zasób |

1. Zastąp *{filter}* z nazwą wyświetlaną dla roli.

    | Filtr | Opis |
    | --- | --- |
    | `$filter=roleName%20eq%20'{roleDisplayName}'` | Formularz zakodowane w adresie URL nazwę wyświetlaną dokładnie roli. Na przykład `$filter=roleName%20eq%20'Virtual%20Machine%20Contributor'` |

## <a name="list-a-custom-role-definition-by-id"></a>Listy niestandardową definicję roli według Identyfikatora

Aby uzyskać informacje o niestandardowych rolach według unikatowego identyfikatora, należy użyć [uzyskać definicje ról —](/rest/api/authorization/roledefinitions/get) interfejsu API REST.

1. Użyj [definicje ról — lista](/rest/api/authorization/roledefinitions/list) interfejsu API REST, aby uzyskać identyfikator GUID dla roli.

1. Uruchom przy użyciu następującego żądania:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. W identyfikatorze URI, Zastąp *{zakresu}* z zakresem, dla którego chcesz wyświetlić listę ról.

    | Zakres | Typ |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Subskrypcja |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Grupa zasobów |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Zasób |

1. Zastąp *{roleDefinitionId}* o identyfikatorze GUID definicji roli.

## <a name="create-a-custom-role"></a>Tworzenie roli niestandardowej

Aby utworzyć rolę niestandardową, należy użyć [definicje ról — Utwórz lub zaktualizuj](/rest/api/authorization/roledefinitions/createorupdate) interfejsu API REST. Aby wywołać ten interfejs API, użytkownik musi być zalogowany użytkownik, któremu przypisano rolę, która ma `Microsoft.Authorization/roleDefinitions/write` uprawnienia na wszystkich `assignableScopes`. Z wbudowanych ról, tylko [właściciela](built-in-roles.md#owner) i [Administrator dostępu użytkowników](built-in-roles.md#user-access-administrator) obejmują to uprawnienie.

1. Przejrzyj listę [operacji dostawcy zasobów](resource-provider-operations.md) dostępnych utworzyć uprawnienia dla swojej roli niestandardowej.

1. Narzędzie identyfikator GUID można wygenerować unikatowy identyfikator, który będzie używany jako identyfikator roli niestandardowej. Identyfikator ma następujący format: `00000000-0000-0000-0000-000000000000`

1. Uruchom za pomocą następujących żądania i treści:

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
          "/subscriptions/{subscriptionId}"
        ]
      }
    }
    ```

1. W identyfikatorze URI, Zastąp *{zakresu}* z pierwszym `assignableScopes` roli niestandardowej.

    | Zakres | Typ |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Subskrypcja |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Grupa zasobów |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Zasób |

1. Zastąp *{roleDefinitionId}* o identyfikatorze GUID roli niestandardowej.

1. W treści żądania w `assignableScopes` właściwości, Zastąp *{roleDefinitionId}* z identyfikatorem GUID.

1. Zastąp *{subscriptionId}* z identyfikatorem Twojej subskrypcji.

1. W `actions` właściwości, Dodaj dozwolone przez rolę do wykonania operacji.

1. W `notActions` właściwości, Dodaj operacje, które są wykluczone z dozwolonych `actions`.

1. W `roleName` i `description` właściwości, określ nazwę roli unikatowy i opis. Aby uzyskać więcej informacji na temat właściwości, zobacz [niestandardowych ról](custom-roles.md).

    Poniżej przedstawiono przykład treść żądania:

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
          "/subscriptions/00000000-0000-0000-0000-000000000000"
        ]
      }
    }
    ```

## <a name="update-a-custom-role"></a>Aktualizacja roli niestandardowej

Aby zaktualizować rolę niestandardową, użyj [definicje ról — Tworzenie lub aktualizowanie](/rest/api/authorization/roledefinitions/createorupdate) interfejsu API REST. Aby wywołać ten interfejs API, użytkownik musi być zalogowany użytkownik, któremu przypisano rolę, która ma `Microsoft.Authorization/roleDefinitions/write` uprawnienia na wszystkich `assignableScopes`. Z wbudowanych ról, tylko [właściciela](built-in-roles.md#owner) i [Administrator dostępu użytkowników](built-in-roles.md#user-access-administrator) obejmują to uprawnienie.

1. Użyj [definicje ról — lista](/rest/api/authorization/roledefinitions/list) lub [uzyskać definicje ról —](/rest/api/authorization/roledefinitions/get) interfejsu API REST w celu uzyskania informacji o roli niestandardowej. Aby uzyskać więcej informacji, zobacz wcześniej [listy ról niestandardowych](#list-custom-roles) sekcji.

1. Uruchom przy użyciu następującego żądania:

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. W identyfikatorze URI, Zastąp *{zakresu}* z pierwszym `assignableScopes` roli niestandardowej.

    | Zakres | Typ |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Subskrypcja |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Grupa zasobów |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Zasób |

1. Zastąp *{roleDefinitionId}* o identyfikatorze GUID roli niestandardowej.

1. Na podstawie informacji o roli niestandardowej, Utwórz treści żądania o następującym formacie:

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
          "/subscriptions/{subscriptionId}"
        ]
      }
    }
    ```

1. Zaktualizować treść żądania zmiany, które chcesz dokonać roli niestandardowej.

    Poniżej przedstawiono przykład treści żądania o dodanie nowej akcji ustawień diagnostycznych:

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
          "/subscriptions/00000000-0000-0000-0000-000000000000"
        ]
      }
    }
    ```

## <a name="delete-a-custom-role"></a>Usuwanie roli niestandardowej

Aby usunąć niestandardową rolę, użyj [Usuń definicje ról —](/rest/api/authorization/roledefinitions/delete) interfejsu API REST. Aby wywołać ten interfejs API, użytkownik musi być zalogowany użytkownik, któremu przypisano rolę, która ma `Microsoft.Authorization/roleDefinitions/delete` uprawnienia na wszystkich `assignableScopes`. Z wbudowanych ról, tylko [właściciela](built-in-roles.md#owner) i [Administrator dostępu użytkowników](built-in-roles.md#user-access-administrator) obejmują to uprawnienie.

1. Użyj [definicje ról — lista](/rest/api/authorization/roledefinitions/list) lub [uzyskać definicje ról —](/rest/api/authorization/roledefinitions/get) interfejsu API REST, aby uzyskać identyfikator GUID w roli niestandardowej. Aby uzyskać więcej informacji, zobacz wcześniej [listy ról niestandardowych](#list-custom-roles) sekcji.

1. Uruchom przy użyciu następującego żądania:

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. W identyfikatorze URI, Zastąp *{zakresu}* z zakresem, który chcesz usunąć rolę niestandardową.

    | Zakres | Typ |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Subskrypcja |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Grupa zasobów |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Zasób |

1. Zastąp *{roleDefinitionId}* o identyfikatorze GUID roli niestandardowej.

## <a name="next-steps"></a>Kolejne kroki

- [Niestandardowe role dla zasobów platformy Azure](custom-roles.md)
- [Zarządzanie dostępem do zasobów platformy Azure przy użyciu RBAC i interfejsu API REST](role-assignments-rest.md)
- [Dokumentacja interfejsu API REST platformy Azure](/rest/api/azure/)