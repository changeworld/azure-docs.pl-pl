---
title: Tworzenie ról niestandardowych przy użyciu interfejsu API REST — Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak tworzenie ról niestandardowych dla kontroli dostępu opartej na rolach (RBAC) przy użyciu interfejsu API REST. W tym jak listy, tworzenia, aktualizacji i usuwania ról niestandardowych.
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
ms.date: 06/20/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 8a1bbe8217e2d4a9846f56124e248e19cbe70b19
ms.sourcegitcommit: e0834ad0bad38f4fb007053a472bde918d69f6cb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2018
ms.locfileid: "37436066"
---
# <a name="create-custom-roles-using-the-rest-api"></a>Tworzenie ról niestandardowych przy użyciu interfejsu API REST

Jeśli [role wbudowane](built-in-roles.md) nie spełniają specyficznych potrzeb Twojej organizacji, możesz utworzyć własne role niestandardowe. W tym artykule opisano, jak tworzyć i zarządzać nimi przy użyciu interfejsu API REST ról niestandardowych.

## <a name="list-roles"></a>Tworzenie listy ról

Aby wyświetlić listę wszystkich ról lub uzyskać informacje na temat pojedynczej roli przy użyciu jego nazwę wyświetlaną, użyj [definicje ról — lista](/rest/api/authorization/roledefinitions/list) interfejsu API REST. Aby wywołać ten interfejs API, musisz mieć dostęp do `Microsoft.Authorization/roleDefinitions/read` operacji w zakresie. Kilka [wbudowane role](built-in-roles.md) uzyskują dostęp do tej operacji.

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

1. Zastąp *{filter}* z warunkiem, że chcesz zastosować, aby filtrować listę ról.

    | Filtr | Opis |
    | --- | --- |
    | `$filter=atScopeAndBelow()` | Utwórz listę ról dostępne do przypisania, w określonym zakresie i wszystkie jego podrzędne zakresów. |
    | `$filter=roleName%20eq%20'{roleDisplayName}'` | Formularz zakodowane w adresie URL nazwę wyświetlaną dokładnie roli. Na przykład `$filter=roleName%20eq%20'Virtual%20Machine%20Contributor'` |

### <a name="get-information-about-a-role"></a>Uzyskiwanie informacji o roli

Aby uzyskać informacje na temat roli przy użyciu jego identyfikator definicji roli, należy użyć [uzyskać definicje ról —](/rest/api/authorization/roledefinitions/get) interfejsu API REST. Aby wywołać ten interfejs API, musisz mieć dostęp do `Microsoft.Authorization/roleDefinitions/read` operacji w zakresie. Kilka [wbudowane role](built-in-roles.md) uzyskują dostęp do tej operacji.

Aby uzyskać informacje o pojedynczej roli przy użyciu jego nazwę wyświetlaną, zobacz poprzednie [listy ról](custom-roles-rest.md#list-roles) sekcji.

1. Użyj [definicje ról — lista](/rest/api/authorization/roledefinitions/list) interfejsu API REST, aby uzyskać identyfikator GUID dla roli. Dla wbudowanych ról, można także uzyskać identyfikator z [wbudowane role](built-in-roles.md).

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

Aby utworzyć rolę niestandardową, należy użyć [definicje ról — Utwórz lub zaktualizuj](/rest/api/authorization/roledefinitions/createorupdate) interfejsu API REST. Aby wywołać ten interfejs API, musisz mieć dostęp do `Microsoft.Authorization/roleDefinitions/write` operację na wszystkich `assignableScopes`. Z wbudowanych ról, tylko [właściciela](built-in-roles.md#owner) i [Administrator dostępu użytkowników](built-in-roles.md#user-access-administrator) uzyskują dostęp do tej operacji. 

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

Aby zaktualizować rolę niestandardową, użyj [definicje ról — Tworzenie lub aktualizowanie](/rest/api/authorization/roledefinitions/createorupdate) interfejsu API REST. Aby wywołać ten interfejs API, musisz mieć dostęp do `Microsoft.Authorization/roleDefinitions/write` operację na wszystkich `assignableScopes`. Z wbudowanych ról, tylko [właściciela](built-in-roles.md#owner) i [Administrator dostępu użytkowników](built-in-roles.md#user-access-administrator) uzyskują dostęp do tej operacji. 

1. Użyj [definicje ról — lista](/rest/api/authorization/roledefinitions/list) lub [uzyskać definicje ról —](/rest/api/authorization/roledefinitions/get) interfejsu API REST w celu uzyskania informacji o roli niestandardowej. Aby uzyskać więcej informacji, zobacz wcześniej [listy ról](custom-roles-rest.md#list-roles) sekcji.

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

Aby usunąć niestandardową rolę, użyj [Usuń definicje ról —](/rest/api/authorization/roledefinitions/delete) interfejsu API REST. Aby wywołać ten interfejs API, musisz mieć dostęp do `Microsoft.Authorization/roleDefinitions/delete` operację na wszystkich `assignableScopes`. Z wbudowanych ról, tylko [właściciela](built-in-roles.md#owner) i [Administrator dostępu użytkowników](built-in-roles.md#user-access-administrator) uzyskują dostęp do tej operacji. 

1. Użyj [definicje ról — lista](/rest/api/authorization/roledefinitions/list) lub [uzyskać definicje ról —](/rest/api/authorization/roledefinitions/get) interfejsu API REST, aby uzyskać identyfikator GUID w roli niestandardowej. Aby uzyskać więcej informacji, zobacz wcześniej [listy ról](custom-roles-rest.md#list-roles) sekcji.

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

- [Role niestandardowe na platformie Azure](custom-roles.md)
- [Zarządzanie dostępem przy użyciu RBAC i interfejsu API REST](role-assignments-rest.md)
- [Dokumentacja interfejsu API REST platformy Azure](/rest/api/azure/)