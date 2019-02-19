---
title: Lista Odmów przydziały dla zasobów platformy Azure przy użyciu interfejsu API REST — Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wyświetlić listę Odmów przydziały dla użytkowników, grup i aplikacji, za pomocą kontroli dostępu opartej na rolach (RBAC) dla zasobów platformy Azure oraz interfejsu API REST.
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
ms.openlocfilehash: 29b8e0953109238b724cc8df9f456706f71a041e
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/18/2019
ms.locfileid: "56341627"
---
# <a name="list-deny-assignments-for-azure-resources-using-the-rest-api"></a>Lista Odmów przydziały dla zasobów platformy Azure przy użyciu interfejsu API REST

Obecnie przypisania odmowy są **tylko do odczytu** i mogą zostać ustawione wyłącznie przez platformę Azure. Nawet jeśli nie możesz utworzyć własnych przypisań odmowy, możesz wyświetlić listę przypisań odmowy, ponieważ mogą one mieć wpływ na Twoje uprawnienia. W tym artykule opisano, jak na liście odmowy przypisania przy użyciu RBAC i interfejsu API REST.

## <a name="list-a-single-deny-assignment"></a>Lista jednej Odmów przypisania

1. Uruchom przy użyciu następującego żądania:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments/{deny-assignment-id}?api-version=2018-07-01-preview
    ```

1. W identyfikatorze URI, Zastąp *{zakresu}* z zakresem, dla którego chcesz wyświetlić listę przypisań Odmów.

    | Zakres | Type |
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

    | Zakres | Type |
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

- [Zrozumienie Odmów przydziały dla zasobów platformy Azure](deny-assignments.md)
- [Podnoszenie poziomu dostępu administratora globalnego w usłudze Azure Active Directory](elevate-access-global-admin.md)
- [Dokumentacja interfejsu API REST platformy Azure](/rest/api/azure/)
