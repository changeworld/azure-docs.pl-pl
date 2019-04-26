---
title: Zarządzanie dostępem do zasobów platformy Azure przy użyciu RBAC i interfejsu API REST — Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zarządzać dostępem do zasobów platformy Azure dla użytkowników, grup i aplikacji przy użyciu kontroli dostępu opartej na rolach (RBAC) oraz interfejsu API REST. Obejmuje to wyświetlanie dostępu, jego przyznawanie i usuwanie.
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
ms.openlocfilehash: 0e0c83d411242be38992dd763dea72eda70ffbf4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60531844"
---
# <a name="manage-access-to-azure-resources-using-rbac-and-the-rest-api"></a>Zarządzanie dostępem do zasobów platformy Azure przy użyciu RBAC i interfejsu API REST

[Kontrola dostępu oparta na rolach (RBAC, Role Based Access Control)](overview.md) to sposób zarządzania dostępem do zasobów platformy Azure. W tym artykule opisano, jak zarządzać dostępu dla użytkowników, grup i aplikacji przy użyciu RBAC i interfejsu API REST.

## <a name="list-access"></a>Tworzenie listy dostępu

RBAC dostęp do listy, możesz liście przypisań ról. Aby wyświetlić listę przypisań ról, użyj jednej z [przypisania ról — lista](/rest/api/authorization/roleassignments/list) interfejsów API REST. Aby uzyskać dokładniejsze wyniki, należy określić zakres i opcjonalny filtr.

1. Uruchom przy użyciu następującego żądania:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter={filter}
    ```

1. W identyfikatorze URI, Zastąp *{zakresu}* z zakresem, dla którego chcesz wyświetlić listę przypisań ról.

    | Zakres | Type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Subskrypcja |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Grupa zasobów |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Resource |

1. Zastąp *{filter}* z warunkiem, że chcesz zastosować, aby filtrować listę przypisania roli.

    | Filtr | Opis |
    | --- | --- |
    | `$filter=atScope()` | Lista przypisań ról tylko określonym zakresie, nie wliczając przypisań ról w subscopes. |
    | `$filter=principalId%20eq%20'{objectId}'` | Lista przypisań ról dla określonego użytkownika, grupy lub jednostki usługi. |
    | `$filter=assignedTo('{objectId}')` | Lista przypisań ról dla określonego użytkownika, a także dziedziczone z grupy. |

## <a name="grant-access"></a>Udzielanie dostępu

Aby udzielić dostępu za pomocą kontroli dostępu opartej na rolach, tworzy się przypisanie roli. Aby utworzyć przypisanie roli, należy użyć [Utwórz przypisania ról —](/rest/api/authorization/roleassignments/create) interfejsu API REST, a następnie określ podmiotu zabezpieczeń, definicja roli i zakresu. Aby wywołać ten interfejs API, musisz mieć dostęp do `Microsoft.Authorization/roleAssignments/write` operacji. Z wbudowanych ról, tylko [właściciela](built-in-roles.md#owner) i [Administrator dostępu użytkowników](built-in-roles.md#user-access-administrator) uzyskują dostęp do tej operacji.

1. Użyj [definicje ról — lista](/rest/api/authorization/roledefinitions/list) interfejsu API REST lub zobacz [wbudowane role](built-in-roles.md) można uzyskać identyfikator definicji roli, którą chcesz przypisać.

1. Narzędzie identyfikator GUID można wygenerować unikatowy identyfikator, który będzie używany jako identyfikator przypisania roli. Identyfikator ma następujący format: `00000000-0000-0000-0000-000000000000`

1. Uruchom za pomocą następujących żądania i treści:

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

    | Zakres | Type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Subskrypcja |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Grupa zasobów |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Resource |

1. Zastąp *{roleAssignmentName}* o identyfikatorze GUID przypisania roli.

1. W treści żądania, Zastąp *{subscriptionId}* z identyfikatorem Twojej subskrypcji.

1. Zastąp *{roleDefinitionId}* o identyfikatorze definicji roli.

1. Zastąp *{principalId}* o identyfikatorze obiektu użytkownika, grupy lub jednostki usługi, która zostanie przypisana rola.

## <a name="remove-access"></a>Usuwanie dostępu

Aby usunąć dostęp za pomocą kontroli dostępu opartej na rolach, usuwa się przypisanie roli. Aby usunąć przypisania roli, należy użyć [Usuń przypisania ról —](/rest/api/authorization/roleassignments/delete) interfejsu API REST. Aby wywołać ten interfejs API, musisz mieć dostęp do `Microsoft.Authorization/roleAssignments/delete` operacji. Z wbudowanych ról, tylko [właściciela](built-in-roles.md#owner) i [Administrator dostępu użytkowników](built-in-roles.md#user-access-administrator) uzyskują dostęp do tej operacji.

1. Uzyskaj identyfikator przypisania roli (GUID). Ten identyfikator jest zwracana, gdy należy najpierw utworzyć przypisania roli lub możesz ją uzyskać, wyświetlając listę przypisań ról.

1. Uruchom przy użyciu następującego żądania:

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentName}?api-version=2015-07-01
    ```

1. W identyfikatorze URI, Zastąp *{zakresu}* w zakresie usuwania przypisania roli.

    | Zakres | Type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Subskrypcja |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Grupa zasobów |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Resource |

1. Zastąp *{roleAssignmentName}* o identyfikatorze GUID przypisania roli.

## <a name="next-steps"></a>Kolejne kroki

- [Deploy resources with Resource Manager templates and Resource Manager REST API (Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i interfejsu API REST usługi Resource Manager)](../azure-resource-manager/resource-group-template-deploy-rest.md)
- [Dokumentacja interfejsu API REST platformy Azure](/rest/api/azure/)
- [Tworzenie ról niestandardowych dla zasobów platformy Azure przy użyciu interfejsu API REST](custom-roles-rest.md)