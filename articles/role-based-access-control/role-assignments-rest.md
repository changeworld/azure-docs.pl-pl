---
title: Dodawanie lub usuwanie przypisań ról przy użyciu RBAC i interfejsu API REST
description: Dowiedz się, jak udzielić dostępu do zasobów platformy Azure dla użytkowników, grup, nazw głównych usług lub tożsamości zarządzanych przy użyciu kontroli dostępu opartej na rolach (RBAC) na platformie Azure oraz interfejsu API REST.
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
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: ae6fce5ab962637fe477ade75cf81b6ac237bdd2
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/11/2020
ms.locfileid: "77138308"
---
# <a name="add-or-remove-role-assignments-using-azure-rbac-and-the-rest-api"></a>Dodawanie lub usuwanie przypisań ról przy użyciu funkcji RBAC platformy Azure i interfejsu API REST

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)] w tym artykule opisano sposób przypisywania ról przy użyciu interfejsu API REST.

## <a name="prerequisites"></a>Wymagania wstępne

Aby dodać lub usunąć przypisania ról, musisz mieć:

- uprawnienia `Microsoft.Authorization/roleAssignments/write` i `Microsoft.Authorization/roleAssignments/delete`, takie jak [administrator dostępu użytkowników](built-in-roles.md#user-access-administrator) lub [właściciel](built-in-roles.md#owner)

## <a name="add-a-role-assignment"></a>Dodawanie przypisania roli

W celu udzielenia dostępu w ramach RBAC należy dodać przypisanie roli. Aby dodać przypisanie roli, użyj [przypisań ról — tworzenie](/rest/api/authorization/roleassignments/create) interfejsu API REST i określanie podmiotu zabezpieczeń, definicji roli i zakresu. Aby wywołać ten interfejs API, musisz mieć dostęp do `Microsoft.Authorization/roleAssignments/write` operacji. W przypadku ról wbudowanych dostęp do tej operacji mają tylko [Administratorzy dostępu](built-in-roles.md#user-access-administrator) [właściciela](built-in-roles.md#owner) i użytkownika.

1. Aby uzyskać identyfikator definicji roli, która ma zostać przypisana, użyj sekcji [definicje ról-list](/rest/api/authorization/roledefinitions/list) API REST lub zobacz [wbudowane role](built-in-roles.md) .

1. Użyj narzędzia GUID, aby wygenerować unikatowy identyfikator, który będzie używany dla identyfikatora przypisania roli. Identyfikator ma format: `00000000-0000-0000-0000-000000000000`

1. Rozpocznij od następującego żądania i treści:

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentName}?api-version=2015-07-01
    ```

    ```json
    {
      "properties": {
        "roleDefinitionId": "/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}",
        "principalId": "{principalId}"
      }
    }
    ```

1. W identyfikatorze URI Zastąp wartość *{SCOPE}* zakresem przypisania roli.

    | Zakres | Typ |
    | --- | --- |
    | `providers/Microsoft.Management/managementGroups/{groupId1}` | Grupa zarządzania |
    | `subscriptions/{subscriptionId1}` | Subskrypcja |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Grupa zasobów |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/ providers/microsoft.web/sites/mysite1` | Zasób |

1. Zastąp ciąg *{roleAssignmentName}* identyfikatorem GUID przypisania roli.

1. W treści żądania Zastąp wartość *{SCOPE}* zakresem przypisania roli.

    | Zakres | Typ |
    | --- | --- |
    | `providers/Microsoft.Management/managementGroups/{groupId1}` | Grupa zarządzania |
    | `subscriptions/{subscriptionId1}` | Subskrypcja |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Grupa zasobów |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/ providers/microsoft.web/sites/mysite1` | Zasób |

1. Zastąp ciąg *{zduplikowanych}* identyfikatorem definicji roli.

1. Zastąp ciąg *{principalId}* identyfikatorem obiektu użytkownika, grupy lub jednostki usługi, do której zostanie przypisana rola.

## <a name="remove-a-role-assignment"></a>Usuwanie przypisania roli

Aby usunąć dostęp za pomocą kontroli dostępu opartej na rolach, usuwa się przypisanie roli. Aby usunąć przypisanie roli, należy użyć [przypisań ról — Usuń](/rest/api/authorization/roleassignments/delete) interfejs API REST. Aby wywołać ten interfejs API, musisz mieć dostęp do `Microsoft.Authorization/roleAssignments/delete` operacji. W przypadku ról wbudowanych dostęp do tej operacji mają tylko [Administratorzy dostępu](built-in-roles.md#user-access-administrator) [właściciela](built-in-roles.md#owner) i użytkownika.

1. Pobierz identyfikator przypisywania ról (GUID). Ten identyfikator jest zwracany podczas pierwszego tworzenia przypisania roli lub można go uzyskać, wyświetlając listę przypisań ról.

1. Rozpocznij od następującego żądania:

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentName}?api-version=2015-07-01
    ```

1. W identyfikatorze URI Zastąp wartość *{SCOPE}* zakresem, aby usunąć przypisanie roli.

    | Zakres | Typ |
    | --- | --- |
    | `providers/Microsoft.Management/managementGroups/{groupId1}` | Grupa zarządzania |
    | `subscriptions/{subscriptionId1}` | Subskrypcja |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Grupa zasobów |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/ providers/microsoft.web/sites/mysite1` | Zasób |

1. Zastąp ciąg *{roleAssignmentName}* identyfikatorem GUID przypisania roli.

## <a name="next-steps"></a>Następne kroki

- [Wyświetlanie listy przypisań ról przy użyciu funkcji RBAC platformy Azure i interfejsu API REST](role-assignments-list-rest.md)
- [Deploy resources with Resource Manager templates and Resource Manager REST API (Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i interfejsu API REST usługi Resource Manager)](../azure-resource-manager/templates/deploy-rest.md)
- [Dokumentacja interfejsu API REST platformy Azure](/rest/api/azure/)
- [Tworzenie ról niestandardowych dla zasobów platformy Azure przy użyciu interfejsu API REST](custom-roles-rest.md)
