---
title: Zrozumienie Odmów przydziały w RBAC platformy Azure | Dokumentacja firmy Microsoft
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
ms.date: 11/30/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: ''
ms.openlocfilehash: fa1a979c01999bd79c45d24e4c7771edaf346dd8
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/30/2018
ms.locfileid: "52632419"
---
# <a name="understand-deny-assignments"></a>Opis przypisań odmowy

Podobnie jak w przypadku przypisania roli *Odmów przypisania* dołącza zbiór akcje odmowy zawsze do użytkownika, grupy lub jednostki usługi w określonym zakresie na potrzeby odmowa dostępu. Odmowa przypisania Zablokuj użytkownikom możliwość wykonywania określonych czynności, nawet wtedy, gdy przypisanie roli przyznaje im dostępu. Niektórych zasobów, które obejmują teraz dostawców na platformie Azure odmówić przypisania. Obecnie przypisania odmowy są **tylko do odczytu** i mogą zostać ustawione wyłącznie przez platformę Azure.

Pod pewnymi względami Odmów przypisania są inne niż przypisań ról. Odmowa przypisania można wykluczyć podmiotów zabezpieczeń i zapobiegać dziedziczeniu na zakresy podrzędne. Odmów dotyczą również przypisania [klasyczny administrator subskrypcji](rbac-and-directory-admin-roles.md) przypisania.

W tym artykule opisano sposób Odmów przypisania są zdefiniowane.

## <a name="deny-assignment-properties"></a>Odmów przypisania właściwości

 Przypisanie Odmów ma następujące właściwości:

> [!div class="mx-tableFixed"]
> | Właściwość | Wymagane | Typ | Opis |
> | --- | --- | --- | --- |
> | `DenyAssignmentName` | Yes | Ciąg | Nazwa wyświetlana przypisania Odmów. Nazwy muszą być unikatowe dla danego zakresu. |
> | `Description` | Nie | Ciąg | Opis przypisania Odmów. |
> | `Permissions.Actions` | Co najmniej jednej akcji lub jednego elementy DataActions | Ciąg] | Tablica ciągów, które określają operacje zarządzania, do których przypisanie Odmów blokuje dostęp. |
> | `Permissions.NotActions` | Nie | Ciąg] | Tablica ciągów, które określają operacje zarządzania, aby wykluczyć z przypisania Odmów. |
> | `Permissions.DataActions` | Co najmniej jednej akcji lub jednego elementy DataActions | Ciąg] | Tablica ciągów, które określają operacje danych, do których przypisanie Odmów blokuje dostęp. |
> | `Permissions.NotDataActions` | Nie | Ciąg] | Tablica ciągów, które określają operacje na danych, aby wykluczyć z przypisania Odmów. |
> | `Scope` | Nie | Ciąg | Ciąg, który określa zakres, który dotyczy przypisania Odmów. |
> | `DoNotApplyToChildScopes` | Nie | Wartość logiczna | Określa, czy przypisanie odmowy mają zastosowanie do zakresy podrzędne. Wartość domyślna to false. |
> | `Principals[i].Id` | Yes | Ciąg] | Tablica obiektów nazwy głównej usługi Azure AD identyfikatorów (użytkownika, grupy, jednostkę usługi lub tożsamość zarządzana), do których zostanie zastosowana przypisania Odmów. Ustaw na pustym identyfikatorem GUID `00000000-0000-0000-0000-000000000000` do reprezentowania wszystkich podmiotów zabezpieczeń. |
> | `Principals[i].Type` | Nie | Ciąg] | Tablica typów obiektów, reprezentowane przez jednostki [i] .id. Ustaw `SystemDefined` do reprezentowania wszystkich podmiotów zabezpieczeń. |
> | `ExcludePrincipals[i].Id` | Nie | Ciąg] | Tablica obiektów nazwy głównej usługi Azure AD identyfikatorów (użytkownika, grupy, jednostkę usługi lub tożsamość zarządzana), do których przypisanie Odmów nie ma zastosowania. |
> | `ExcludePrincipals[i].Type` | Nie | Ciąg] | Tablica typów obiektów, reprezentowane przez .id ExcludePrincipals [i]. |
> | `IsSystemProtected` | Nie | Wartość logiczna | Określa, czy odmówić przypisania został utworzony przez platformę Azure i nie można edytować ani usunąć. Obecnie wszystkie Odmów przypisania są chronione systemu. |

## <a name="system-defined-principal"></a>Jednostki zdefiniowaną przez system

Do obsługi odmowy przypisania, **System-Defined jednostki** zostały wprowadzone. Ten podmiot zabezpieczeń reprezentuje wszystkich użytkowników, grup, nazw głównych usług i zarządzanych tożsamości w katalogu usługi Azure AD. Jeśli identyfikator podmiotu zabezpieczeń jest zerowy identyfikator GUID `00000000-0000-0000-0000-000000000000` i typ podmiotu zabezpieczeń jest `SystemDefined`, podmiot zabezpieczeń reprezentuje wszystkie podmioty zabezpieczeń. `SystemDefined` może być łączone z `ExcludePrincipals` odrzucanie wszystkich podmiotów zabezpieczeń, z wyjątkiem niektórych użytkowników. `SystemDefined` ma następujące ograniczenia:

- Może być używana tylko w `Principals` i nie może być używany w `ExcludePrincipals`.
- `Principals[i].Type` musi być równa `SystemDefined`.

## <a name="next-steps"></a>Kolejne kroki

* [Lista Odmów przypisania przy użyciu RBAC i interfejsu API REST](deny-assignments-rest.md)
* [Zrozumienie definicje ról](role-definitions.md)
