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
ms.date: 09/24/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: ''
ms.openlocfilehash: 8ef3a2ec44c5eff80d3a50a6c56805667e164ba8
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46980172"
---
# <a name="understand-deny-assignments"></a>Zrozumienie Odmów przypisania

Podobnie jak w przypadku przypisania roli *Odmów przypisania* powiązań zbiór akcje odmowy zawsze do użytkownika, grupy lub jednostki usługi w określonym zakresie na potrzeby odmowa dostępu. Przypisanie Odmów można wykluczyć podmiotów zabezpieczeń i zapobiec dziedziczenia zakresy podrzędne, który jest inny niż przypisań ról. Obecnie Odmów przypisania **tylko do odczytu** i może zostać ustawiona tylko przez platformę Azure. W tym artykule opisano sposób Odmów przypisania są zdefiniowane.

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
> | `Principals[i].Id` | Yes | Ciąg] | Tablica obiektów nazwy głównej usługi Azure AD identyfikatorów (użytkownika, grupy lub jednostki usługi), do których zostanie zastosowana przypisania Odmów. Ustaw na pustym identyfikatorem GUID `00000000-0000-0000-0000-000000000000` do reprezentowania wszystkich użytkowników. |
> | `Principals[i].Type` | Nie | Ciąg] | Tablica typów obiektów, reprezentowane przez jednostki [i] .id. Ustaw `Everyone` do reprezentowania wszystkich użytkowników. |
> | `ExcludePrincipals[i].Id` | Nie | Ciąg] | Tablica obiektów nazwy głównej usługi Azure AD identyfikatorów (użytkownika, grupy lub jednostki usługi), do których przypisanie Odmów nie ma zastosowania. |
> | `ExcludePrincipals[i].Type` | Nie | Ciąg] | Tablica typów obiektów, reprezentowane przez .id ExcludePrincipals [i]. |
> | `IsSystemProtected` | Nie | Wartość logiczna | Określa, czy odmówić przypisania został utworzony przez platformę Azure i nie można edytować ani usunąć. Obecnie wszystkie Odmów przypisania są chronione systemu. |

## <a name="everyone-principal"></a>Wszyscy podmiotu zabezpieczeń

Do obsługi odmowy przypisania, jednostki wszyscy wprowadzono. Reprezentuje główną wszystkim użytkownikom, grupom i nazwy główne usług w katalogu usługi Azure AD. Jeśli identyfikator podmiotu zabezpieczeń jest zerowy identyfikator GUID `00000000-0000-0000-0000-000000000000` i typ podmiotu zabezpieczeń jest `Everyone`, podmiot zabezpieczeń reprezentuje wszystkich użytkowników. Każdy podmiot zabezpieczeń może być łączone z `ExcludePrincipals` odrzucanie wszystkich użytkowników, z wyjątkiem niektórych użytkowników. Wszyscy podmiotu zabezpieczeń ma następujące ograniczenia:

- Może być używana tylko w `Principals` i nie może być używany w `ExcludePrincipals`.
- `Principals[i].Type` musi być równa `Everyone`.

## <a name="next-steps"></a>Kolejne kroki

* [Lista Odmów przypisania przy użyciu RBAC i interfejsu API REST](deny-assignments-rest.md)
* [Zrozumienie definicje ról](role-definitions.md)
