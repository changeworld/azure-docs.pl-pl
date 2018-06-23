---
title: Zarządzanie dostępem przy użyciu RBAC i interfejsu API REST - Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zarządzać dostępem dla użytkowników, grup i aplikacji, przy użyciu kontroli dostępu opartej na rolach (RBAC) i interfejsu API REST. W tym jak lista dostępu, udzielić dostępu i spowodować usunięcie dostępu.
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
ms.openlocfilehash: cfcb87fdff8105b25d4f7e63b775aaf9243d2a90
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/22/2018
ms.locfileid: "36317011"
---
# <a name="manage-access-using-rbac-and-the-rest-api"></a>Zarządzanie dostępem przy użyciu RBAC i interfejsu API REST

[Kontrola dostępu oparta na rolach (RBAC)](overview.md) jest sposób zarządzania dostępem do zasobów na platformie Azure. W tym artykule opisano, jak zarządzać dostępu dla użytkowników, grup i aplikacji przy użyciu RBAC i interfejsu API REST.

## <a name="list-access"></a>Dostęp do listy

W RBAC na liście dostępu, możesz listę przypisań ról. Aby wyświetlić listę przypisań ról, użyj jednej z [przypisań ról — lista](/rest/api/authorization/roleassignments/list) interfejsów API REST. Aby uściślić wyniki, należy określić zakres i opcjonalny filtr. Aby wywołać interfejs API, musi mieć dostęp do `Microsoft.Authorization/roleAssignments/read` operacji w podanym zakresie. Kilka [wbudowane role](built-in-roles.md) udzielany jest dostęp do tej operacji.

1. Uruchom następujące żądania:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter={filter}
    ```

1. W identyfikatorze URI, Zastąp *{zakresu}* z zakresem, dla którego chcesz wyświetlić listę przypisań ról.

    | Zakres | Typ |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Subskrypcja |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Grupa zasobów |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Zasób |

1. Zastąp *{filtru}* warunek, który chcesz zastosować, aby filtrować listę przypisania roli.

    | Filtr | Opis |
    | --- | --- |
    | `$filter=atScope()` | Wyświetl listę przypisań ról tylko określony zakres, z wyłączeniem przypisania ról na subscopes. |
    | `$filter=principalId%20eq%20'{objectId}'` | Wyświetl listę przypisań ról dla określonego użytkownika, grupy lub nazwy głównej usługi. |
    | `$filter=assignedTo('{objectId}')` | Wyświetl listę przypisań ról dla określonego użytkownika, między innymi dziedziczone z grupy. |

## <a name="grant-access"></a>Udzielanie dostępu

W RBAC Aby udzielić dostępu, należy utworzyć przypisania roli. Aby utworzyć przypisanie roli, należy użyć [tworzenia przypisań ról -](/rest/api/authorization/roleassignments/create) interfejsu API REST i określ podmiotu zabezpieczeń, definicji roli i zakres. Aby wywołać tego interfejsu API, musi mieć dostęp do `Microsoft.Authorization/roleAssignments/write` operacji. Wbudowanych ról, tylko [właściciela](built-in-roles.md#owner) i [Administrator dostępu użytkowników](built-in-roles.md#user-access-administrator) udzielany jest dostęp do tej operacji.

1. Użyj [definicje ról — lista](/rest/api/authorization/roledefinitions/list) interfejsu API REST lub zobacz [wbudowane role](built-in-roles.md) uzyskać identyfikator definicji roli, do którego chcesz przypisać.

1. Generuj Unikatowy identyfikator, który będzie używany dla identyfikatora przypisania roli za pomocą narzędzia identyfikatora GUID. Identyfikator ma format: `00000000-0000-0000-0000-000000000000`

1. Uruchom z następującymi żądania i treści:

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentName}?api-version=2015-07-01
    ```

    ```json
    {
      "properties": {
        "roleDefinitionId": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}",
        "principalId": "{principalId}"
      }
    }
    ```
    
1. W identyfikatorze URI, Zastąp *{zakresu}* z zakresem przypisania roli.

    | Zakres | Typ |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Subskrypcja |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Grupa zasobów |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Zasób |

1. Zastąp *{roleAssignmentName}* o identyfikatorze GUID przypisania roli.

1. W treści żądania, Zastąp *{subscriptionId}* z identyfikatorem subskrypcji.

1. Zastąp *{roleDefinitionId}* z identyfikator definicji roli.

1. Zastąp *{principalId}* z identyfikatorem obiektu użytkownika, grupy lub nazwy głównej usługi, który zostanie przypisany do roli.

## <a name="remove-access"></a>Usuń dostęp

W RBAC aby spowodować usunięcie dostępu, należy usunąć przypisanie roli. Aby usunąć przypisanie roli, należy użyć [usunąć przypisania roli -](/rest/api/authorization/roleassignments/delete) interfejsu API REST. Aby wywołać tego interfejsu API, musi mieć dostęp do `Microsoft.Authorization/roleAssignments/delete` operacji. Wbudowanych ról, tylko [właściciela](built-in-roles.md#owner) i [Administrator dostępu użytkowników](built-in-roles.md#user-access-administrator) udzielany jest dostęp do tej operacji.

1. Pobierz identyfikator przypisania roli (GUID). Ten identyfikator jest zwracany, należy najpierw utworzyć przypisania roli lub pobrać go przez listę przypisań ról.

1. Uruchom następujące żądania:

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentName}?api-version=2015-07-01
    ```

1. W identyfikatorze URI, Zastąp *{zakresu}* w zakresie usuwania przypisania roli.

    | Zakres | Typ |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Subskrypcja |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Grupa zasobów |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Zasób |

1. Zastąp *{roleAssignmentName}* o identyfikatorze GUID przypisania roli.

## <a name="next-steps"></a>Kolejne kroki

- [Deploy resources with Resource Manager templates and Resource Manager REST API (Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i interfejsu API REST usługi Resource Manager)](../azure-resource-manager/resource-group-template-deploy-rest.md)
- [Dokumentacja interfejsu API Azure REST](/rest/api/azure/)
- [Tworzenie niestandardowych ról przy użyciu interfejsu API REST](custom-roles-rest.md)