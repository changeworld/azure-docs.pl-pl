---
title: Dodawanie lub usuwanie przypisań ról za pomocą interfejsu RBAC i interfejsu API REST
description: Dowiedz się, jak udzielić dostępu do zasobów platformy Azure dla użytkowników, grup, podmiotów korzystających z usług lub tożsamości zarządzanych przy użyciu kontroli dostępu opartej na rolach platformy Azure (RBAC) i interfejsu API REST.
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
ms.openlocfilehash: 9beda6589c03f1b14fc9756af86a9ce0711894c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063007"
---
# <a name="add-or-remove-role-assignments-using-azure-rbac-and-the-rest-api"></a>Dodawanie lub usuwanie przypisań ról przy użyciu usługi Azure RBAC i interfejsu API REST

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)]W tym artykule opisano sposób przypisywania ról przy użyciu interfejsu API REST.

## <a name="prerequisites"></a>Wymagania wstępne

Aby dodać lub usunąć przypisania ról, musisz mieć:

- `Microsoft.Authorization/roleAssignments/write`i `Microsoft.Authorization/roleAssignments/delete` uprawnień, takich jak [Administrator dostępu do użytkownika](built-in-roles.md#user-access-administrator) lub [Właściciel](built-in-roles.md#owner)

## <a name="add-a-role-assignment"></a>Dodaj przypisanie roli

W RBAC, aby udzielić dostępu, należy dodać przypisanie roli. Aby dodać przypisanie roli, użyj [przypisania ról — Tworzenie](/rest/api/authorization/roleassignments/create) interfejsu API REST i określ podmiot zabezpieczeń, definicję roli i zakres. Aby wywołać ten interfejs API, `Microsoft.Authorization/roleAssignments/write` musisz mieć dostęp do operacji. Z wbudowanych ról tylko [administrator dostępu właściciela](built-in-roles.md#owner) i [użytkownika](built-in-roles.md#user-access-administrator) mają dostęp do tej operacji.

1. Użyj interfejsu API [definicje ról — lista](/rest/api/authorization/roledefinitions/list) REST lub zobacz [Wbudowane role,](built-in-roles.md) aby uzyskać identyfikator definicji roli, którą chcesz przypisać.

1. Narzędzie GUID służy do generowania unikatowego identyfikatora, który będzie używany dla identyfikatora przypisania roli. Identyfikator ma format:`00000000-0000-0000-0000-000000000000`

1. Zacznij od następującego żądania i treści:

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

1. W ramach identyfikatora URI zastąp *{scope}* zakresem przypisania roli.

    > [!div class="mx-tableFixed"]
    > | Zakres | Typ |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Grupa zarządzania |
    > | `subscriptions/{subscriptionId1}` | Subskrypcja |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Grupa zasobów |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/microsoft.web/sites/mysite1` | Zasób |

    W poprzednim przykładzie microsoft.web jest dostawcą zasobów, który odwołuje się do wystąpienia usługi App Service. Podobnie można użyć innych dostawców zasobów i określić zakres. Aby uzyskać więcej informacji, zobacz [Dostawców i typy zasobów platformy Azure](../azure-resource-manager/management/resource-providers-and-types.md) oraz obsługiwane operacje dostawcy zasobów usługi Azure Resource [Manager](resource-provider-operations.md).  

1. Zastąp *{roleAssignmentName}* identyfikatorem GUID przypisania roli.

1. W treści żądania zastąp *{scope}* zakresem przypisania roli.

    > [!div class="mx-tableFixed"]
    > | Zakres | Typ |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Grupa zarządzania |
    > | `subscriptions/{subscriptionId1}` | Subskrypcja |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Grupa zasobów |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/microsoft.web/sites/mysite1` | Zasób |

1. Zastąp *{roleDefinitionId}* identyfikatorem definicji roli.

1. Zastąp *{principalId}* identyfikatorem obiektu użytkownika, grupy lub jednostki usługi, który zostanie przypisany do roli.

## <a name="remove-a-role-assignment"></a>Usuwanie przypisania roli

Aby usunąć dostęp za pomocą kontroli dostępu opartej na rolach, usuwa się przypisanie roli. Aby usunąć przypisanie roli, użyj interfejsu API [Przypisania ról — Usuwanie](/rest/api/authorization/roleassignments/delete) interfejsu API REST. Aby wywołać ten interfejs API, `Microsoft.Authorization/roleAssignments/delete` musisz mieć dostęp do operacji. Z wbudowanych ról tylko [administrator dostępu właściciela](built-in-roles.md#owner) i [użytkownika](built-in-roles.md#user-access-administrator) mają dostęp do tej operacji.

1. Pobierz identyfikator przypisania roli (GUID). Ten identyfikator jest zwracany podczas pierwszego tworzenia przypisania roli lub można go uzyskać, wymieniając przypisania roli.

1. Zacznij od następującego żądania:

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentName}?api-version=2015-07-01
    ```

1. W ramach identyfikatora URI zastąp *{scope}* zakresem usuwania przypisania roli.

    > [!div class="mx-tableFixed"]
    > | Zakres | Typ |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Grupa zarządzania |
    > | `subscriptions/{subscriptionId1}` | Subskrypcja |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Grupa zasobów |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/microsoft.web/sites/mysite1` | Zasób |

1. Zastąp *{roleAssignmentName}* identyfikatorem GUID przypisania roli.

## <a name="next-steps"></a>Następne kroki

- [Lista przypisań ról przy użyciu usługi Azure RBAC i interfejsu API REST](role-assignments-list-rest.md)
- [Deploy resources with Resource Manager templates and Resource Manager REST API (Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i interfejsu API REST usługi Resource Manager)](../azure-resource-manager/templates/deploy-rest.md)
- [Odwołanie interfejsu API usługi Azure REST](/rest/api/azure/)
- [Tworzenie ról niestandardowych dla zasobów platformy Azure przy użyciu interfejsu API REST](custom-roles-rest.md)
