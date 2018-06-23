---
title: Tworzenie niestandardowych ról przy użyciu interfejsu API REST - Azure | Dokumentacja firmy Microsoft
description: Informacje o sposobie tworzenia niestandardowych ról dla kontroli dostępu opartej na rolach (RBAC) przy użyciu interfejsu API REST. W tym jak listy, tworzenie, aktualizowanie i usuwanie ról niestandardowych.
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
ms.topic: article
ms.date: 06/20/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 8267846fed30baf2c37dcddd453ae9ead9341da9
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/22/2018
ms.locfileid: "36320590"
---
# <a name="create-custom-roles-using-the-rest-api"></a>Tworzenie niestandardowych ról przy użyciu interfejsu API REST

Jeśli [wbudowane role](built-in-roles.md) nie spełnienia specyficznych potrzeb Twojej organizacji, możesz utworzyć własne niestandardowe role. W tym artykule opisano sposób tworzenia i zarządzania nimi przy użyciu interfejsu API REST role niestandardowe.

## <a name="list-roles"></a>Lista ról

Aby wyświetlić listę wszystkich ról lub uzyskać informacje na temat pojedynczej roli przy użyciu nazwy wyświetlanej, użyj [definicje ról — lista](/rest/api/authorization/roledefinitions/list) interfejsu API REST. Aby wywołać tego interfejsu API, musi mieć dostęp do `Microsoft.Authorization/roleDefinitions/read` operacji w tym zakresie. Kilka [wbudowane role](built-in-roles.md) udzielany jest dostęp do tej operacji.

1. Uruchom następujące żądania:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. W identyfikatorze URI, Zastąp *{zakresu}* z zakresem, dla którego chcesz wyświetlić listę ról.

    | Zakres | Typ |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Subskrypcja |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Grupa zasobów |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Zasób |

1. Zastąp *{filtru}* warunek, który chcesz zastosować, aby filtrować listę ról.

    | Filtr | Opis |
    | --- | --- |
    | `$filter=atScopeAndBelow()` | Lista ról dostępne do przypisania w określonym zakresie i w żadnym z jego zakresy podrzędne. |
    | `$filter=roleName%20eq%20'{roleDisplayName}'` | Formularz zakodowane w adresie URL nazwę wyświetlaną dokładne roli. Na przykład `$filter=roleName%20eq%20'Virtual%20Machine%20Contributor'` |

### <a name="get-information-about-a-role"></a>Uzyskiwanie informacji o roli

Aby uzyskać informacje o roli za pomocą identyfikatora definicji roli, należy użyć [uzyskać definicje ról -](/rest/api/authorization/roledefinitions/get) interfejsu API REST. Aby wywołać tego interfejsu API, musi mieć dostęp do `Microsoft.Authorization/roleDefinitions/read` operacji w tym zakresie. Kilka [wbudowane role](built-in-roles.md) udzielany jest dostęp do tej operacji.

Aby uzyskać informacje o pojedynczej roli przy użyciu nazwy wyświetlanej, zobacz poprzednie [listy ról](custom-roles-rest.md#list-roles) sekcji.

1. Użyj [definicje ról — lista](/rest/api/authorization/roledefinitions/list) interfejsu API REST można pobrać identyfikatora GUID dla roli. Dla wbudowanych ról, można także uzyskać identyfikator z [wbudowane role](built-in-roles.md).

1. Uruchom następujące żądania:

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

## <a name="create-a-custom-role"></a>Tworzenie niestandardowej roli zabezpieczeń

Aby utworzyć niestandardową rolę, użyj [definicje ról - utworzyć lub zaktualizować](/rest/api/authorization/roledefinitions/createorupdate) interfejsu API REST. Aby wywołać tego interfejsu API, musi mieć dostęp do `Microsoft.Authorization/roleDefinitions/write` operację na wszystkich `assignableScopes`. Wbudowanych ról, tylko [właściciela](built-in-roles.md#owner) i [Administrator dostępu użytkowników](built-in-roles.md#user-access-administrator) udzielany jest dostęp do tej operacji. 

1. Zapoznaj się z listą [operacji dostawcy zasobów](resource-provider-operations.md) dostępnych można utworzyć uprawnienia dla niestandardowej roli zabezpieczeń.

1. Generuj Unikatowy identyfikator, który będzie używany dla identyfikatora niestandardowej roli zabezpieczeń za pomocą narzędzia identyfikatora GUID. Identyfikator ma format: `00000000-0000-0000-0000-000000000000`

1. Uruchom z następującymi żądania i treści:

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

1. W identyfikatorze URI, Zastąp *{zakresu}* przy pierwszym `assignableScopes` niestandardowej roli.

    | Zakres | Typ |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Subskrypcja |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Grupa zasobów |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Zasób |

1. Zastąp *{roleDefinitionId}* o identyfikatorze GUID niestandardowej roli.

1. W treści żądania w `assignableScopes` właściwości, Zastąp *{roleDefinitionId}* z identyfikatorem GUID.

1. Zastąp *{subscriptionId}* z identyfikatorem subskrypcji.

1. W `actions` właściwości, Dodaj rolę umożliwia wykonywanie operacji.

1. W `notActions` właściwości, dodać operacje, które są wykluczone z dozwolonych `actions`.

1. W `roleName` i `description` właściwości, określ nazwę roli unikatowy i opis. Aby uzyskać więcej informacji o właściwościach, zobacz [niestandardowych ról](custom-roles.md).

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

## <a name="update-a-custom-role"></a>Aktualizacja niestandardowej roli zabezpieczeń

Aby zaktualizować niestandardowej roli zabezpieczeń, użyj [definicje ról — Tworzenie lub aktualizowanie](/rest/api/authorization/roledefinitions/createorupdate) interfejsu API REST. Aby wywołać tego interfejsu API, musi mieć dostęp do `Microsoft.Authorization/roleDefinitions/write` operację na wszystkich `assignableScopes`. Wbudowanych ról, tylko [właściciela](built-in-roles.md#owner) i [Administrator dostępu użytkowników](built-in-roles.md#user-access-administrator) udzielany jest dostęp do tej operacji. 

1. Użyj [definicje ról — lista](/rest/api/authorization/roledefinitions/list) lub [uzyskać definicje ról -](/rest/api/authorization/roledefinitions/get) interfejsu API REST, aby uzyskać informacje o niestandardowych rolach. Aby uzyskać więcej informacji, zobacz wcześniej [listy ról](custom-roles-rest.md#list-roles) sekcji.

1. Uruchom następujące żądania:

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. W identyfikatorze URI, Zastąp *{zakresu}* przy pierwszym `assignableScopes` niestandardowej roli.

    | Zakres | Typ |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Subskrypcja |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Grupa zasobów |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Zasób |

1. Zastąp *{roleDefinitionId}* o identyfikatorze GUID niestandardowej roli.

1. Na podstawie informacji o niestandardowych roli, utworzyć treści żądania o następującym formacie:

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

1. Zaktualizować treści żądania zmiany, które mają być do niestandardowej roli.

    Poniżej przedstawiono przykład treści żądania z akcją ustawień diagnostycznych dodany:

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

## <a name="delete-a-custom-role"></a>Usunięcia niestandardowej roli zabezpieczeń

Aby usunąć niestandardową rolę, użyj [Usuń definicje ról -](/rest/api/authorization/roledefinitions/delete) interfejsu API REST. Aby wywołać tego interfejsu API, musi mieć dostęp do `Microsoft.Authorization/roleDefinitions/delete` operację na wszystkich `assignableScopes`. Wbudowanych ról, tylko [właściciela](built-in-roles.md#owner) i [Administrator dostępu użytkowników](built-in-roles.md#user-access-administrator) udzielany jest dostęp do tej operacji. 

1. Użyj [definicje ról — lista](/rest/api/authorization/roledefinitions/list) lub [uzyskać definicje ról -](/rest/api/authorization/roledefinitions/get) interfejsu API REST można pobrać identyfikatora GUID niestandardowej roli. Aby uzyskać więcej informacji, zobacz wcześniej [listy ról](custom-roles-rest.md#list-roles) sekcji.

1. Uruchom następujące żądania:

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. W identyfikatorze URI, Zastąp *{zakresu}* z zakresem, który chcesz usunąć rolę niestandardową.

    | Zakres | Typ |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Subskrypcja |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Grupa zasobów |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Zasób |

1. Zastąp *{roleDefinitionId}* o identyfikatorze GUID niestandardowej roli.

## <a name="next-steps"></a>Kolejne kroki

- [Role niestandardowe na platformie Azure](custom-roles.md)
- [Zarządzanie dostępem przy użyciu RBAC i interfejsu API REST](role-assignments-rest.md)
- [Dokumentacja interfejsu API Azure REST](/rest/api/azure/)