---
title: Zarządzanie dostępem do zasobów platformy Azure przy użyciu RBAC i interfejsu API REST — Azure | Microsoft Docs
description: Dowiedz się, jak zarządzać dostępem do zasobów platformy Azure dla użytkowników, grup i aplikacji przy użyciu kontroli dostępu opartej na rolach (RBAC) i interfejsu API REST. Obejmuje to wyświetlanie dostępu, jego przyznawanie i usuwanie.
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
ms.date: 09/11/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 86ee030e8c97cf3033b9d2d76b8125c64ecf8065
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/14/2019
ms.locfileid: "70996466"
---
# <a name="manage-access-to-azure-resources-using-rbac-and-the-rest-api"></a>Zarządzanie dostępem do zasobów platformy Azure przy użyciu RBAC i interfejsu API REST

[Kontrola dostępu oparta na rolach (RBAC, Role Based Access Control)](overview.md) to sposób zarządzania dostępem do zasobów platformy Azure. W tym artykule opisano sposób zarządzania dostępem użytkowników, grup i aplikacji przy użyciu funkcji RBAC i interfejsu API REST.

## <a name="list-access"></a>Tworzenie listy dostępu

W polu RBAC, aby wyświetlić listę uprawnień dostępu, należy wyświetlić listę przypisań ról. Aby wyświetlić listę przypisań ról, użyj jednego z [przypisań ról —](/rest/api/authorization/roleassignments/list) interfejsów API REST. Aby uściślić wyniki, należy określić zakres i opcjonalny filtr.

1. Rozpocznij od następującego żądania:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter={filter}
    ```

1. W identyfikatorze URI Zastąp wartość *{SCOPE}* zakresem, dla którego chcesz wyświetlić listę przypisań ról.

    | Scope | Type |
    | --- | --- |
    | `providers/Microsoft.Management/managementGroups/{groupId1}` | Grupa zarządzania |
    | `subscriptions/{subscriptionId1}` | Subscription |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resource group |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Resource |

    W poprzednim przykładzie firma Microsoft. Web jest dostawcą zasobów, który odwołuje się do wystąpienia App Service. Analogicznie, można użyć innych dostawców zasobów i określić zakres. Aby uzyskać więcej informacji, zobacz [dostawcy zasobów platformy Azure i typy](../azure-resource-manager/resource-manager-supported-services.md) oraz obsługiwane [Azure Resource Manager operacje dostawcy](resource-provider-operations.md)zasobów.  
     
1. Zamień *filtr {Filter}* na warunek, który ma zostać zastosowany do filtrowania listy przypisywania ról.

    | Filtr | Opis |
    | --- | --- |
    | `$filter=atScope()` | Wyświetla listę przypisań ról tylko dla określonego zakresu, bez uwzględniania przypisań ról w podzakresach. |
    | `$filter=principalId%20eq%20'{objectId}'` | Wyświetla listę przypisań ról dla określonego użytkownika, grupy lub nazwy głównej usługi. |
    | `$filter=assignedTo('{objectId}')` | Wyświetla listę przypisań ról dla określonego użytkownika lub nazwy głównej usługi. Jeśli użytkownik jest członkiem grupy z przypisaniem roli, zostanie ona również wyświetlona. Ten filtr jest przechodni dla grup, co oznacza, że jeśli użytkownik jest członkiem grupy, a grupa jest członkiem innej grupy z przypisaniem roli, to przypisanie roli jest również wymienione. Ten filtr akceptuje tylko identyfikator obiektu dla użytkownika lub nazwy głównej usługi. Nie można przekazać identyfikatora obiektu dla grupy. |

## <a name="grant-access"></a>Przyznaj dostęp

Aby udzielić dostępu za pomocą kontroli dostępu opartej na rolach, tworzy się przypisanie roli. Aby utworzyć przypisanie roli, należy użyć [przypisań ról — tworzenie](/rest/api/authorization/roleassignments/create) interfejsu API REST i określanie podmiotu zabezpieczeń, definicji roli i zakresu. Aby wywołać ten interfejs API, musisz mieć dostęp do `Microsoft.Authorization/roleAssignments/write` tej operacji. W przypadku ról wbudowanych dostęp do tej operacji mają tylko [Administratorzy dostępu](built-in-roles.md#user-access-administrator) [właściciela](built-in-roles.md#owner) i użytkownika.

1. Aby uzyskać identyfikator definicji roli, która ma zostać przypisana, użyj sekcji [definicje ról-list](/rest/api/authorization/roledefinitions/list) API REST lub zobacz [wbudowane role](built-in-roles.md) .

1. Użyj narzędzia GUID, aby wygenerować unikatowy identyfikator, który będzie używany dla identyfikatora przypisania roli. Identyfikator ma format:`00000000-0000-0000-0000-000000000000`

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

    | Scope | Type |
    | --- | --- |
    | `providers/Microsoft.Management/managementGroups/{groupId1}` | Grupa zarządzania |
    | `subscriptions/{subscriptionId1}` | Subscription |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resource group |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/ providers/microsoft.web/sites/mysite1` | Resource |

1. Zastąp ciąg *{roleAssignmentName}* identyfikatorem GUID przypisania roli.

1. W treści żądania Zastąp wartość *{SCOPE}* zakresem przypisania roli.

    | Scope | Type |
    | --- | --- |
    | `providers/Microsoft.Management/managementGroups/{groupId1}` | Grupa zarządzania |
    | `subscriptions/{subscriptionId1}` | Subscription |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resource group |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/ providers/microsoft.web/sites/mysite1` | Resource |

1. Zastąp ciąg *{zduplikowanych}* identyfikatorem definicji roli.

1. Zastąp ciąg *{principalId}* identyfikatorem obiektu użytkownika, grupy lub jednostki usługi, do której zostanie przypisana rola.

## <a name="remove-access"></a>Usuń dostęp

Aby usunąć dostęp za pomocą kontroli dostępu opartej na rolach, usuwa się przypisanie roli. Aby usunąć przypisanie roli, należy użyć [przypisań ról — Usuń](/rest/api/authorization/roleassignments/delete) interfejs API REST. Aby wywołać ten interfejs API, musisz mieć dostęp do `Microsoft.Authorization/roleAssignments/delete` tej operacji. W przypadku ról wbudowanych dostęp do tej operacji mają tylko [Administratorzy dostępu](built-in-roles.md#user-access-administrator) [właściciela](built-in-roles.md#owner) i użytkownika.

1. Pobierz identyfikator przypisywania ról (GUID). Ten identyfikator jest zwracany podczas pierwszego tworzenia przypisania roli lub można go uzyskać, wyświetlając listę przypisań ról.

1. Rozpocznij od następującego żądania:

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentName}?api-version=2015-07-01
    ```

1. W identyfikatorze URI Zastąp wartość *{SCOPE}* zakresem, aby usunąć przypisanie roli.

    | Scope | Type |
    | --- | --- |
    | `providers/Microsoft.Management/managementGroups/{groupId1}` | Grupa zarządzania |
    | `subscriptions/{subscriptionId1}` | Subscription |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resource group |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/ providers/microsoft.web/sites/mysite1` | Resource |

1. Zastąp ciąg *{roleAssignmentName}* identyfikatorem GUID przypisania roli.

## <a name="next-steps"></a>Następne kroki

- [Deploy resources with Resource Manager templates and Resource Manager REST API (Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i interfejsu API REST usługi Resource Manager)](../azure-resource-manager/resource-group-template-deploy-rest.md)
- [Dokumentacja interfejsu API REST platformy Azure](/rest/api/azure/)
- [Tworzenie ról niestandardowych dla zasobów platformy Azure przy użyciu interfejsu API REST](custom-roles-rest.md)
