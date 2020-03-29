---
title: Lista odmów przydziałów dla zasobów platformy Azure za pomocą interfejsu API REST
description: Dowiedz się, jak wyświetlić listę przypisań odmowy dla użytkowników, grup i aplikacji przy użyciu kontroli dostępu opartej na rolach (RBAC) dla zasobów platformy Azure i interfejsu API REST.
services: active-directory
documentationcenter: na
author: rolyon
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: role-based-access-control
ms.workload: multiple
ms.tgt_pltfrm: rest-api
ms.devlang: na
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 0f648405a3d71bf27c64dacbb3fd78f3e9801137
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063028"
---
# <a name="list-deny-assignments-for-azure-resources-using-the-rest-api"></a>Lista odmów przydziałów dla zasobów platformy Azure przy użyciu interfejsu API REST

[Odmów przydziałów](deny-assignments.md) zablokować użytkownikom wykonywanie określonych akcji zasobów platformy Azure, nawet jeśli przypisanie roli udziela im dostępu. W tym artykule opisano sposób listy przypisań odmowy przy użyciu interfejsu API REST.

> [!NOTE]
> Nie można bezpośrednio tworzyć własnych przypisań odmowy. Aby uzyskać informacje o sposobie tworzenia przypisań odmowy, zobacz [Odrzucanie przypisań](deny-assignments.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby uzyskać informacje o przypisaniu odmowy, musisz mieć:

- `Microsoft.Authorization/denyAssignments/read`uprawnienia, które są zawarte w większości [wbudowanych ról zasobów platformy Azure.](built-in-roles.md)

## <a name="list-a-single-deny-assignment"></a>Wyświetlanie pojedynczego przypisania odmowy

1. Zacznij od następującego żądania:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments/{deny-assignment-id}?api-version=2018-07-01-preview
    ```

1. W identyfikatorze URI zastąp *{scope}* zakresem, dla którego chcesz wyświetlić listę przypisań odmowy.

    > [!div class="mx-tableFixed"]
    > | Zakres | Typ |
    > | --- | --- |
    > | `subscriptions/{subscriptionId}` | Subskrypcja |
    > | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Grupa zasobów |
    > | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | Zasób |

1. Zastąp *{deny-assignment-id}* identyfikatorem przypisania odmowy, który chcesz pobrać.

## <a name="list-multiple-deny-assignments"></a>Wyświetlanie listy wielu przypisań odmowy

1. Zacznij od jednego z następujących żądań:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview
    ```

    Z parametrami opcjonalnymi:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter={filter}
    ```

1. W identyfikatorze URI zastąp *{scope}* zakresem, dla którego chcesz wyświetlić listę przypisań odmowy.

    > [!div class="mx-tableFixed"]
    > | Zakres | Typ |
    > | --- | --- |
    > | `subscriptions/{subscriptionId}` | Subskrypcja |
    > | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Grupa zasobów |
    > | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | Zasób |

1. Zastąp *{filter}* warunkiem, który chcesz zastosować do filtrowania listy przypisania odmowy.

    > [!div class="mx-tableFixed"]
    > | Filtr | Opis |
    > | --- | --- |
    > | (bez filtra) | Wyświetla listę wszystkich przypisań odmowy na, powyżej i poniżej określonego zakresu. |
    > | `$filter=atScope()` | Wyświetla listę odmówić przypisań tylko dla określonego zakresu i powyżej. Nie obejmuje przypisania odmów w podzakresach. |
    > | `$filter=assignedTo('{objectId}')` | Wyświetla listę odmów przypisania dla określonego użytkownika lub jednostki usługi.<br/>Jeśli użytkownik jest członkiem grupy, która ma przypisanie odmowy, to przypisanie odmowy jest również wymienione. Ten filtr jest przechodni dla grup, co oznacza, że jeśli użytkownik jest członkiem grupy, a ta grupa jest członkiem innej grupy, która ma przypisanie odmowy, to przypisanie odmowy jest również wymienione.<br/>Ten filtr akceptuje tylko identyfikator obiektu dla użytkownika lub jednostki usługi. Nie można przekazać identyfikatora obiektu dla grupy. |
    > | `$filter=atScope()+and+assignedTo('{objectId}')` | Wyświetla listę odmów przypisania dla określonego użytkownika lub jednostki usługi i w określonym zakresie. |
    > | `$filter=denyAssignmentName+eq+'{deny-assignment-name}'` | Wyświetla listę odmów przypisania o określonej nazwie. |
    > | `$filter=principalId+eq+'{objectId}'` | Wyświetla listę odmówienia przypisań dla określonego użytkownika, grupy lub jednostki usługi. |

## <a name="list-deny-assignments-at-the-root-scope-"></a>Lista przypisań odmowy w zakresie głównym (/)

1. Podnieś poziom dostępu zgodnie z opisem w obszarze [Zwiększ poziom dostępu administratora globalnego w usłudze Azure Active Directory](elevate-access-global-admin.md).

1. Użyj następującego żądania:

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter={filter}
    ```

1. Zastąp *{filter}* warunkiem, który chcesz zastosować do filtrowania listy przypisania odmowy. Wymagany jest filtr.

    > [!div class="mx-tableFixed"]
    > | Filtr | Opis |
    > | --- | --- |
    > | `$filter=atScope()` | Lista odmów przypisania tylko dla zakresu głównego. Nie obejmuje przypisania odmów w podzakresach. |
    > | `$filter=denyAssignmentName+eq+'{deny-assignment-name}'` | Lista odmów przypisania o określonej nazwie. |

1. Usuń podwyższony poziom dostępu.

## <a name="next-steps"></a>Następne kroki

- [Opis przypisań odmów dla zasobów platformy Azure](deny-assignments.md)
- [Podnoszenie poziomu dostępu administratora globalnego w usłudze Azure Active Directory](elevate-access-global-admin.md)
- [Odwołanie interfejsu API usługi Azure REST](/rest/api/azure/)
