---
title: Lista przypisań ról przy użyciu usługi Azure RBAC i interfejsu API REST
description: Dowiedz się, jak określić, jakie zasoby użytkownicy, grupy, jednostki usługi lub tożsamości zarządzane mają dostęp do przy użyciu kontroli dostępu opartej na rolach platformy Azure (RBAC) i interfejsu API REST.
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
ms.openlocfilehash: a494e7fd4c9fb79faa6a1d8cb2c3c871796ccdc5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062154"
---
# <a name="list-role-assignments-using-azure-rbac-and-the-rest-api"></a>Lista przypisań ról przy użyciu usługi Azure RBAC i interfejsu API REST

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)]W tym artykule opisano sposób listy przypisań ról przy użyciu interfejsu API REST.

> [!NOTE]
> Jeśli twoja organizacja zleciła funkcje zarządzania na zewnątrz dostawcy usług, który korzysta z [usługi Azure zarządzanie zasobami delegowanymi,](../lighthouse/concepts/azure-delegated-resource-management.md)przypisania ról autoryzowane przez tego dostawcę usług nie będą wyświetlane w tym miejscu.

## <a name="list-role-assignments"></a>Lista przypisań ról

W RBAC, aby uzyskać dostęp do listy, można wyświetlić listę przypisań ról. Aby wyświetlić listę przypisań ról, użyj jednego z [przypisań ról — lista interfejsów](/rest/api/authorization/roleassignments/list) API REST. Aby zawęzić wyniki, należy określić zakres i filtr opcjonalny.

1. Zacznij od następującego żądania:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter={filter}
    ```

1. W identyfikatorze URI zastąp *{scope}* zakresem, dla którego chcesz wyświetlić listę przypisań ról.

    > [!div class="mx-tableFixed"]
    > | Zakres | Typ |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Grupa zarządzania |
    > | `subscriptions/{subscriptionId1}` | Subskrypcja |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Grupa zasobów |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | Zasób |

    W poprzednim przykładzie microsoft.web jest dostawcą zasobów, który odwołuje się do wystąpienia usługi App Service. Podobnie można użyć innych dostawców zasobów i określić zakres. Aby uzyskać więcej informacji, zobacz [Dostawców i typy zasobów platformy Azure](../azure-resource-manager/management/resource-providers-and-types.md) oraz obsługiwane operacje dostawcy zasobów usługi Azure Resource [Manager](resource-provider-operations.md).  
     
1. Zastąp *{filter}* warunkiem, który chcesz zastosować do filtrowania listy przypisań ról.

    > [!div class="mx-tableFixed"]
    > | Filtr | Opis |
    > | --- | --- |
    > | `$filter=atScope()` | Wyświetla listę przypisań ról tylko dla określonego zakresu, z wyłączeniem przypisań ról w podzakresach. |
    > | `$filter=assignedTo('{objectId}')` | Wyświetla listę przypisań ról dla określonego użytkownika lub jednostki usługi.<br/>Jeśli użytkownik jest członkiem grupy, która ma przypisanie roli, to przypisanie roli jest również wymienione. Ten filtr jest przechodni dla grup, co oznacza, że jeśli użytkownik jest członkiem grupy, a ta grupa jest członkiem innej grupy, która ma przypisanie roli, to przypisanie roli jest również wymienione.<br/>Ten filtr akceptuje tylko identyfikator obiektu dla użytkownika lub jednostki usługi. Nie można przekazać identyfikatora obiektu dla grupy. |
    > | `$filter=atScope()+and+assignedTo('{objectId}')` | Wyświetla listę przypisań ról dla określonego użytkownika lub jednostki usługi i w określonym zakresie. |
    > | `$filter=principalId+eq+'{objectId}'` | Wyświetla listę przypisań ról dla określonego użytkownika, grupy lub jednostki usługi. |

## <a name="next-steps"></a>Następne kroki

- [Dodawanie lub usuwanie przypisań ról przy użyciu usługi Azure RBAC i interfejsu API REST](role-assignments-rest.md)
- [Odwołanie interfejsu API usługi Azure REST](/rest/api/azure/)
