---
title: Zrozumienie Odmów przydziały dla zasobów platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o Odmów przypisania kontroli dostępu opartej na rolach (RBAC) dla zasobów platformy Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/13/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: ''
ms.openlocfilehash: f15d6fd81337aa4a859539e86f37a516848c9370
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165978"
---
# <a name="understand-deny-assignments-for-azure-resources"></a>Zrozumienie Odmów przydziały dla zasobów platformy Azure

Podobnie jak w przypadku przypisania roli *Odmów przypisania* dołącza zbiór akcje odmowy zawsze do użytkownika, grupy lub jednostki usługi w określonym zakresie na potrzeby odmowa dostępu. Odmowa przypisania Zablokuj użytkownikom możliwość wykonywania akcji na określony zasób platformy Azure, nawet wtedy, gdy przypisanie roli przyznaje im dostęp.

W tym artykule opisano sposób Odmów przypisania są zdefiniowane.

## <a name="how-deny-assignments-are-created"></a>Jak uniemożliwić przypisania są tworzone

Odmowa przypisania są tworzone i zarządzane przez platformę Azure, aby chronić zasoby. Na przykład Azure schematy i na platformie Azure aplikacje zarządzane przez użycie Odmów przydziały, aby chronić zasoby zarządzane przez system. Aby uzyskać więcej informacji, zobacz [chronić nowe zasoby za pomocą blokad zasobów platformy Azure, plany](../governance/blueprints/tutorials/protect-new-resources.md).

## <a name="compare-role-assignments-and-deny-assignments"></a>Porównaj przypisań ról i odmawiać go przypisania

Odmowa przypisania wykonaj podobny wzorzec jako przypisania roli, ale także mieć pewne różnice.

| Możliwości | Przypisanie roli | Zezwalaj na przypisanie |
| --- | --- | --- |
| Udzielanie dostępu | :heavy_check_mark: |  |
| Odmowa dostępu |  | :heavy_check_mark: |
| Można bezpośrednio utworzyć | :heavy_check_mark: |  |
| Stosowanie w zakresie | :heavy_check_mark: | :heavy_check_mark: |
| Wykluczanie jednostek |  | :heavy_check_mark: |
| Zapobiegaj dziedziczenia zakresy podrzędne |  | :heavy_check_mark: |
| Dotyczą [klasyczny administrator subskrypcji](rbac-and-directory-admin-roles.md) przypisania |  | :heavy_check_mark: |

## <a name="deny-assignment-properties"></a>Odmów przypisania właściwości

 Przypisanie Odmów ma następujące właściwości:

> [!div class="mx-tableFixed"]
> | Właściwość | Wymagane | Typ | Opis |
> | --- | --- | --- | --- |
> | `DenyAssignmentName` | Yes | String | Nazwa wyświetlana przypisania Odmów. Nazwy muszą być unikatowe dla danego zakresu. |
> | `Description` | Nie | String | Opis przypisania Odmów. |
> | `Permissions.Actions` | Co najmniej jednej akcji lub jednego elementy DataActions | Ciąg] | Tablica ciągów, które określają operacje zarządzania, do których przypisanie Odmów blokuje dostęp. |
> | `Permissions.NotActions` | Nie | Ciąg] | Tablica ciągów, które określają operacje zarządzania, aby wykluczyć z przypisania Odmów. |
> | `Permissions.DataActions` | Co najmniej jednej akcji lub jednego elementy DataActions | Ciąg] | Tablica ciągów, które określają operacje danych, do których przypisanie Odmów blokuje dostęp. |
> | `Permissions.NotDataActions` | Nie | Ciąg] | Tablica ciągów, które określają operacje na danych, aby wykluczyć z przypisania Odmów. |
> | `Scope` | Nie | String | Ciąg, który określa zakres, który dotyczy przypisania Odmów. |
> | `DoNotApplyToChildScopes` | Nie | Boolean | Określa, czy przypisanie odmowy mają zastosowanie do zakresy podrzędne. Wartość domyślna to false. |
> | `Principals[i].Id` | Yes | Ciąg] | Tablica obiektów nazwy głównej usługi Azure AD identyfikatorów (użytkownika, grupy, jednostkę usługi lub tożsamość zarządzana), do których zostanie zastosowana przypisania Odmów. Ustaw na pustym identyfikatorem GUID `00000000-0000-0000-0000-000000000000` do reprezentowania wszystkich podmiotów zabezpieczeń. |
> | `Principals[i].Type` | Nie | Ciąg] | Tablica typów obiektów, reprezentowane przez jednostki [i] .id. Ustaw `SystemDefined` do reprezentowania wszystkich podmiotów zabezpieczeń. |
> | `ExcludePrincipals[i].Id` | Nie | Ciąg] | Tablica obiektów nazwy głównej usługi Azure AD identyfikatorów (użytkownika, grupy, jednostkę usługi lub tożsamość zarządzana), do których przypisanie Odmów nie ma zastosowania. |
> | `ExcludePrincipals[i].Type` | Nie | Ciąg] | Tablica typów obiektów, reprezentowane przez .id ExcludePrincipals [i]. |
> | `IsSystemProtected` | Nie | Boolean | Określa, czy odmówić przypisania został utworzony przez platformę Azure i nie można edytować ani usunąć. Obecnie wszystkie Odmów przypisania są chronione systemu. |

## <a name="the-all-principals-principal"></a>Podmiot zabezpieczeń wszystkich podmiotów zabezpieczeń

Do obsługi odmowy przypisania, podmiot zabezpieczeń zdefiniowaną przez system o nazwie *wszystkich podmiotów zabezpieczeń* zostały wprowadzone. Ten podmiot zabezpieczeń reprezentuje wszystkich użytkowników, grup, nazw głównych usług i zarządzanych tożsamości w katalogu usługi Azure AD. Jeśli identyfikator podmiotu zabezpieczeń jest zerowy identyfikator GUID `00000000-0000-0000-0000-000000000000` i typ podmiotu zabezpieczeń jest `SystemDefined`, podmiot zabezpieczeń reprezentuje wszystkie podmioty zabezpieczeń. W danych wyjściowych programu Azure PowerShell wszystkie podmioty zabezpieczeń wygląda następująco:

```azurepowershell
Principals              : {
                          DisplayName:  All Principals
                          ObjectType:   SystemDefined
                          ObjectId:     00000000-0000-0000-0000-000000000000
                          }
```

Wszystkie podmioty zabezpieczeń może być łączone z `ExcludePrincipals` odrzucanie wszystkich podmiotów zabezpieczeń, z wyjątkiem niektórych użytkowników. Wszystkie podmioty zabezpieczeń ma następujące ograniczenia:

- Może być używana tylko w `Principals` i nie może być używany w `ExcludePrincipals`.
- `Principals[i].Type` musi być równa `SystemDefined`.

## <a name="next-steps"></a>Kolejne kroki

* [Lista Odmów przydziały dla zasobów platformy Azure przy użyciu witryny Azure portal](deny-assignments-portal.md)
* [Zrozumienie definicje ról na potrzeby zasobów platformy Azure](role-definitions.md)
