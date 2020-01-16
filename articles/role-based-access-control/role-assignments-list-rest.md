---
title: Wyświetlanie listy przypisań ról przy użyciu funkcji RBAC platformy Azure i interfejsu API REST
description: Dowiedz się, jak określić zasoby, których użytkownicy, grupy, nazwy główne usług lub tożsamości zarządzane są dostępne do korzystania z kontroli dostępu opartej na rolach (RBAC) na platformie Azure i interfejsu API REST.
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
ms.date: 01/10/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 0db3e1b222aad7d2a5aa9fc20663fc6e17ea4f8c
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75981072"
---
# <a name="list-role-assignments-using-azure-rbac-and-the-rest-api"></a>Wyświetlanie listy przypisań ról przy użyciu funkcji RBAC platformy Azure i interfejsu API REST

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)] w tym artykule opisano sposób wyświetlania przypisań ról przy użyciu interfejsu API REST.

> [!NOTE]
> Jeśli organizacja ma funkcje zarządzania, które są używane przez usługę zarządzania [zasobami delegowanymi przez platformę Azure](../lighthouse/concepts/azure-delegated-resource-management.md), w tym miejscu nie będą wyświetlane przypisania ról autoryzowane przez tego dostawcę usług.

## <a name="list-role-assignments"></a>Lista przypisań ról

W polu RBAC, aby wyświetlić listę uprawnień dostępu, należy wyświetlić listę przypisań ról. Aby wyświetlić listę przypisań ról, użyj jednego z [przypisań ról —](/rest/api/authorization/roleassignments/list) interfejsów API REST. Aby uściślić wyniki, należy określić zakres i opcjonalny filtr.

1. Rozpocznij od następującego żądania:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter={filter}
    ```

1. W identyfikatorze URI Zastąp wartość *{SCOPE}* zakresem, dla którego chcesz wyświetlić listę przypisań ról.

    | Zakres | Typ |
    | --- | --- |
    | `providers/Microsoft.Management/managementGroups/{groupId1}` | Grupa zarządzania |
    | `subscriptions/{subscriptionId1}` | Subskrypcja |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Grupa zasobów |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Zasób |

    W poprzednim przykładzie firma Microsoft. Web jest dostawcą zasobów, który odwołuje się do wystąpienia App Service. Analogicznie, można użyć innych dostawców zasobów i określić zakres. Aby uzyskać więcej informacji, zobacz [dostawcy zasobów platformy Azure i typy](../azure-resource-manager/management/resource-providers-and-types.md) oraz obsługiwane [Azure Resource Manager operacje dostawcy](resource-provider-operations.md)zasobów.  
     
1. Zamień *filtr {Filter}* na warunek, który ma zostać zastosowany do filtrowania listy przypisywania ról.

    | Filtr | Opis |
    | --- | --- |
    | `$filter=atScope()` | Wyświetla listę przypisań ról tylko dla określonego zakresu, bez uwzględniania przypisań ról w podzakresach. |
    | `$filter=principalId%20eq%20'{objectId}'` | Wyświetla listę przypisań ról dla określonego użytkownika, grupy lub nazwy głównej usługi. |
    | `$filter=assignedTo('{objectId}')` | Wyświetla listę przypisań ról dla określonego użytkownika lub nazwy głównej usługi. Jeśli użytkownik jest członkiem grupy z przypisaniem roli, zostanie ona również wyświetlona. Ten filtr jest przechodni dla grup, co oznacza, że jeśli użytkownik jest członkiem grupy, a grupa jest członkiem innej grupy z przypisaniem roli, to przypisanie roli jest również wymienione. Ten filtr akceptuje tylko identyfikator obiektu dla użytkownika lub nazwy głównej usługi. Nie można przekazać identyfikatora obiektu dla grupy. |

## <a name="next-steps"></a>Następne kroki

- [Dodawanie lub usuwanie przypisań ról przy użyciu funkcji RBAC platformy Azure i interfejsu API REST](role-assignments-rest.md)
- [Dokumentacja interfejsu API REST platformy Azure](/rest/api/azure/)
