---
title: Lista Odmów przydziały dla zasobów platformy Azure przy użyciu interfejsu API REST — Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wyświetlić listę Odmów przydziały dla użytkowników, grup i aplikacji przy użyciu kontroli dostępu opartej na rolach (RBAC) dla zasobów platformy Azure oraz interfejsu API REST.
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
ms.date: 06/10/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 0bc49456f5965846a2de542b4a063bab2d1838bf
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67118282"
---
# <a name="list-deny-assignments-for-azure-resources-using-the-rest-api"></a>Lista Odmów przydziały dla zasobów platformy Azure przy użyciu interfejsu API REST

[Odmów przypisania](deny-assignments.md) Zablokuj użytkownikom możliwość wykonywania akcji na określony zasób platformy Azure, nawet wtedy, gdy przypisanie roli przyznaje im dostęp. W tym artykule opisano sposób wyświetlenia listy Odmów przydziałów za pomocą interfejsu API REST.

> [!NOTE]
> Nie można bezpośrednio utworzyć własne Odmów przypisania. Aby uzyskać informacje na temat nie zezwoli na przydziały są tworzone, zobacz [Odmów przypisania](deny-assignments.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby uzyskać informacje o przypisaniu Odmów, musisz mieć:

- `Microsoft.Authorization/denyAssignments/read` uprawnienie, który jest dostępny w większości [wbudowane role zasobów platformy Azure](built-in-roles.md).

## <a name="list-a-single-deny-assignment"></a>Lista jednej Odmów przypisania

1. Uruchom przy użyciu następującego żądania:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments/{deny-assignment-id}?api-version=2018-07-01-preview
    ```

1. W identyfikatorze URI, Zastąp *{zakresu}* z zakresem, dla którego chcesz wyświetlić listę przypisań Odmów.

    | Scope | Typ |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Subskrypcja |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Grupa zasobów |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Resource |

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

    | Scope | Typ |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Subskrypcja |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Grupa zasobów |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Resource |

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
