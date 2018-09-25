---
title: Lista Odmów przypisania przy użyciu RBAC i interfejsu API REST — Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wyświetlić listę Odmów przydziały dla użytkowników, grup i aplikacji, przy użyciu kontroli dostępu opartej na rolach (RBAC) oraz interfejsu API REST.
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
ms.date: 09/24/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 44c1d3b18bb9bdc63247379fe3f277cb6542f2da
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46975590"
---
# <a name="list-deny-assignments-using-rbac-and-the-rest-api"></a>Lista Odmów przypisania przy użyciu RBAC i interfejsu API REST

Obecnie Odmów przypisania **tylko do odczytu** i może zostać ustawiona tylko przez platformę Azure. Mimo że nie można utworzyć własne Odmów przypisania, można wyświetlić listę Odmów przypisania, ponieważ może mieć wpływ na Twoje czynnych uprawnień. W tym artykule opisano, jak na liście odmowy przypisania przy użyciu RBAC i interfejsu API REST.

## <a name="list-a-single-deny-assignment"></a>Lista jednej Odmów przypisania

1. Uruchom przy użyciu następującego żądania:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments/{deny-assignment-id}?api-version=2018-07-01-preview
    ```

1. W identyfikatorze URI, Zastąp *{zakresu}* z zakresem, dla którego chcesz wyświetlić listę przypisań Odmów.

    | Zakres | Typ |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Subskrypcja |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Grupa zasobów |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Zasób |

1. Zastąp *{identyfikator Odmów przypisania}* o identyfikatorze przypisania Odmów chcesz pobrać.

## <a name="list-multiple-deny-assignments"></a>Lista wielu Odmów przypisania

1. Rozpocznij od jednego z następujących żądań:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview
    ```

    Z opcjonalnymi parametrami:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter={filter}
    ```

1. W identyfikatorze URI, Zastąp *{zakresu}* z zakresem, dla którego chcesz wyświetlić listę przypisań Odmów.

    | Zakres | Typ |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Subskrypcja |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Grupa zasobów |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Zasób |

1. Zastąp *{filter}* z warunek, który chcesz zastosować, aby filtrować listę przypisania Odmów.

    | Filtr | Opis |
    | --- | --- |
    | (bez filtrowania) | Lista wszystkich odmówić przypisania w powyżej i poniżej określonego zakresu. |
    | `$filter=atScope()` | Lista odmówić przypisań dla określonego zakresu lub nowszej. Nie ma przypisań Odmów w subscopes. |
    | `$filter=denyAssignmentName%20eq%20'{deny-assignment-name}'` | Lista odmówić przypisania o określonej nazwie. |

## <a name="list-deny-assignments-at-the-root-scope-"></a>Lista Odmów przypisania w zakresie głównym (/)

1. Podniesienie poziomu dostępu, zgodnie z opisem w [podniesienie poziomu dostępu administratora globalnego usługi Azure Active Directory](elevate-access-global-admin.md).

1. Użyj następującego żądania:

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter={filter}
    ```

1. Zastąp *{filter}* z warunek, który chcesz zastosować, aby filtrować listę przypisania Odmów. Filtr jest wymagany.

    | Filtr | Opis |
    | --- | --- |
    | `$filter=atScope()` | Lista odmówić przypisania tylko zakres głównego. Nie ma przypisań Odmów w subscopes. |
    | `$filter=denyAssignmentName%20eq%20'{deny-assignment-name}'` | Lista odmówić przypisania o określonej nazwie. |

1. Usuń podwyższonego poziomu dostępu.

## <a name="next-steps"></a>Kolejne kroki

- [Zrozumienie Odmów przypisania](deny-assignments.md)
- [Podniesienie poziomu dostępu administratora globalnego usługi Azure Active Directory](elevate-access-global-admin.md)
- [Dokumentacja interfejsu API REST platformy Azure](/rest/api/azure/)
