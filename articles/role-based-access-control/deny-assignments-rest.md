---
title: Wyświetlanie listy Odmów przypisań zasobów platformy Azure za pomocą interfejsu API REST
description: Dowiedz się, jak lista odrzuconych przypisań dla użytkowników, grup i aplikacji przy użyciu kontroli dostępu opartej na rolach (RBAC) dla zasobów platformy Azure i interfejsu API REST.
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
ms.openlocfilehash: 9e6214b3cb2cdca2d80ebae43771b206e3396d8b
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/11/2020
ms.locfileid: "77137318"
---
# <a name="list-deny-assignments-for-azure-resources-using-the-rest-api"></a>Wyświetlanie listy Odmów przypisań zasobów platformy Azure przy użyciu interfejsu API REST

[Odmowa przypisania](deny-assignments.md) uniemożliwia użytkownikom wykonywanie określonych akcji zasobów platformy Azure nawet wtedy, gdy przypisanie roli przyznaje im dostęp. W tym artykule opisano sposób wyświetlania listy zablokowanych przypisań przy użyciu interfejsu API REST.

> [!NOTE]
> Nie można bezpośrednio tworzyć własnych przypisań Odmów. Aby uzyskać informacje na temat sposobu tworzenia przypisań Odmów, zobacz [odmowa przypisań](deny-assignments.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby uzyskać informacje o przypisaniu odmowy, należy dysponować:

- uprawnienie `Microsoft.Authorization/denyAssignments/read`, które jest zawarte w większości [wbudowanych ról dla zasobów platformy Azure](built-in-roles.md).

## <a name="list-a-single-deny-assignment"></a>Utwórz listę jednego przypisania odmowy

1. Rozpocznij od następującego żądania:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments/{deny-assignment-id}?api-version=2018-07-01-preview
    ```

1. W identyfikatorze URI Zastąp wartość *{SCOPE}* zakresem, dla którego chcesz wyświetlić listę przypisań Odmów.

    | Zakres | Typ |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Subskrypcja |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Grupa zasobów |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Zasób |

1. Zastąp *ciąg {Deny-przypisania-ID}* identyfikatorem przypisania Odmów, który chcesz pobrać.

## <a name="list-multiple-deny-assignments"></a>Wyświetl wiele przypisań Odmów

1. Zacznij od jednego z następujących żądań:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview
    ```

    Z opcjonalnymi parametrami:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter={filter}
    ```

1. W identyfikatorze URI Zastąp wartość *{SCOPE}* zakresem, dla którego chcesz wyświetlić listę przypisań Odmów.

    | Zakres | Typ |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Subskrypcja |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Grupa zasobów |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Zasób |

1. Zastąp wartość *{Filter}* warunkiem, którą chcesz zastosować, aby odfiltrować listę przypisań Odmów.

    | Filtr | Opis |
    | --- | --- |
    | (brak filtru) | Wyświetl listę wszystkich przypisań Odmów w, powyżej i poniżej określonego zakresu. |
    | `$filter=atScope()` | Wyświetl listę odrzuconych przypisań tylko dla określonego zakresu i powyżej. Nie obejmuje przypisań Odmów w podzakresach. |
    | `$filter=denyAssignmentName%20eq%20'{deny-assignment-name}'` | Wyświetl listę odrzuconych przypisań o określonej nazwie. |

## <a name="list-deny-assignments-at-the-root-scope-"></a>Wyświetl listę odrzuconych przypisań w zakresie głównym (/)

1. Podnieś poziom dostępu zgodnie z opisem w temacie [Podnieś poziom dostępu dla administratora globalnego w Azure Active Directory](elevate-access-global-admin.md).

1. Użyj następującego żądania:

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter={filter}
    ```

1. Zastąp wartość *{Filter}* warunkiem, którą chcesz zastosować, aby odfiltrować listę przypisań Odmów. Wymagany jest filtr.

    | Filtr | Opis |
    | --- | --- |
    | `$filter=atScope()` | Wyświetl listę odrzuconych przypisań tylko dla zakresu głównego. Nie obejmuje przypisań Odmów w podzakresach. |
    | `$filter=denyAssignmentName%20eq%20'{deny-assignment-name}'` | Wyświetl listę odrzuconych przypisań o określonej nazwie. |

1. Usuń dostęp z podwyższonym poziomem uprawnień.

## <a name="next-steps"></a>Następne kroki

- [Informacje o odmowie przypisań zasobów platformy Azure](deny-assignments.md)
- [Podnoszenie poziomu dostępu administratora globalnego w usłudze Azure Active Directory](elevate-access-global-admin.md)
- [Dokumentacja interfejsu API REST platformy Azure](/rest/api/azure/)
