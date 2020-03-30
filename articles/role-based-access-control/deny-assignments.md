---
title: Opis przypisań odmów dla zasobów platformy Azure
description: Dowiedz się więcej o odmowie przydziałów w kontroli dostępu opartej na rolach (RBAC) dla zasobów platformy Azure.
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
ms.date: 03/26/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: ''
ms.openlocfilehash: db249ccde1026cd468a1c30942891119482697ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80372483"
---
# <a name="understand-deny-assignments-for-azure-resources"></a>Opis przypisań odmów dla zasobów platformy Azure

Podobnie jak przypisanie roli, *przypisanie odmowy* dołącza zestaw akcji odmowy do użytkownika, grupy lub jednostki usługi w określonym zakresie w celu odmowy dostępu. Odmów przydziałów zablokować użytkownikom wykonywanie określonych akcji zasobów platformy Azure, nawet jeśli przypisanie roli udziela im dostępu.

W tym artykule opisano sposób definiowania przypisań odmowy.

## <a name="how-deny-assignments-are-created"></a>Jak tworzone są przypisania odmów

Zadania odmowy są tworzone i zarządzane przez platformę Azure w celu ochrony zasobów. Plany platformy Azure i aplikacje zarządzane platformy Azure używają przydyskłań odmów w celu ochrony zasobów zarządzanych przez system. Plany platformy Azure i aplikacje zarządzane platformy Azure są jedynym sposobem, w jaki można utworzyć przypisania odmowy. Nie można bezpośrednio tworzyć własnych przypisań odmowy. Aby uzyskać więcej informacji na temat sposobu używania przez plany zleceń odmowy do blokowania zasobów, zobacz [Opis blokowania zasobów w planach platformy Azure.](../governance/blueprints/concepts/resource-locking.md)

> [!NOTE]
> Nie można bezpośrednio tworzyć własnych przypisań odmowy.

## <a name="compare-role-assignments-and-deny-assignments"></a>Porównywanie przypisań ról i odrzucanie przypisań

Odmów przypisania wykonaj podobny wzorzec jak przypisania ról, ale mają również pewne różnice.

| Możliwości | Przypisanie roli | Odmów przypisania |
| --- | --- | --- |
| Udzielanie dostępu | :heavy_check_mark: |  |
| Odmowa dostępu |  | :heavy_check_mark: |
| Możliwość bezpośredniego tworzenia | :heavy_check_mark: |  |
| Stosowanie w zakresie | :heavy_check_mark: | :heavy_check_mark: |
| Wyklucz zleceniodawców |  | :heavy_check_mark: |
| Zapobieganie dziedziczeniu zakresów podrzędnych |  | :heavy_check_mark: |
| Stosowanie do [klasycznych przypisań administratora subskrypcji](rbac-and-directory-admin-roles.md) |  | :heavy_check_mark: |

## <a name="deny-assignment-properties"></a>Odmowa właściwości przydziału

 Przypisanie odmowy ma następujące właściwości:

> [!div class="mx-tableFixed"]
> | Właściwość | Wymagany | Typ | Opis |
> | --- | --- | --- | --- |
> | `DenyAssignmentName` | Tak | Ciąg | Wyświetlana nazwa przypisania odmowy. Nazwy muszą być unikatowe dla danego zakresu. |
> | `Description` | Nie | Ciąg | Opis przypisania odmowy. |
> | `Permissions.Actions` | Co najmniej jedna akcja lub jedna akcja DataActions | Ciąg[] | Tablica ciągów, które określają operacje zarządzania, do których przypisanie odmowy blokuje dostęp. |
> | `Permissions.NotActions` | Nie | Ciąg[] | Tablica ciągów, które określają operacje zarządzania do wykluczenia z przypisania odmowy. |
> | `Permissions.DataActions` | Co najmniej jedna akcja lub jedna akcja DataActions | Ciąg[] | Tablica ciągów, które określają operacje danych, do których przypisanie odmowy blokuje dostęp. |
> | `Permissions.NotDataActions` | Nie | Ciąg[] | Tablica ciągów, które określają operacje danych do wykluczenia z przypisania odmowy. |
> | `Scope` | Nie | Ciąg | Ciąg, który określa zakres, który stosuje się do przypisania odmowy. |
> | `DoNotApplyToChildScopes` | Nie | Wartość logiczna | Określa, czy przypisanie odmowy ma zastosowanie do zakresów podrzędnych. Wartość domyślna jest false. |
> | `Principals[i].Id` | Tak | Ciąg[] | Tablica identyfikatorów głównych obiektów usługi Azure AD (użytkownika, grupy, jednostki usługi lub tożsamości zarządzanej), do której ma zastosowanie przypisanie odmowy. Ustaw pusty identyfikator `00000000-0000-0000-0000-000000000000` GUID do reprezentowania wszystkich podmiotów. |
> | `Principals[i].Type` | Nie | Ciąg[] | Tablica typów obiektów reprezentowanych przez principals[i].Id. Ustaw do `SystemDefined` reprezentowania wszystkich podmiotów. |
> | `ExcludePrincipals[i].Id` | Nie | Ciąg[] | Tablica identyfikatorów głównych obiektów usługi Azure AD (użytkownika, grupy, jednostki usługi lub tożsamości zarządzanej), do których przypisanie odmowy nie ma zastosowania. |
> | `ExcludePrincipals[i].Type` | Nie | Ciąg[] | Tablica typów obiektów reprezentowanych przez ExcludePrincipals[i].Id. |
> | `IsSystemProtected` | Nie | Wartość logiczna | Określa, czy to przypisanie odmowy zostało utworzone przez platformę Azure i nie może być edytowane ani usuwane. Obecnie wszystkie przypisania odmów są chronione systemowo. |

## <a name="the-all-principals-principal"></a>Główny zleceniodawca wszystkich

Aby obsługiwać przypisania odmów, wprowadzono podmiot zdefiniowany przez system o nazwie *Wszystkie podmioty.* Ten podmiot zabezpieczeń reprezentuje wszystkich użytkowników, grupy, jednostki usługi i tożsamości zarządzane w katalogu usługi Azure AD. Jeśli identyfikator główny jest zerowy `00000000-0000-0000-0000-000000000000` identyfikator GUID `SystemDefined`i typ główny jest, główny reprezentuje wszystkie podmioty. W danych wyjściowych programu Azure PowerShell wszystkie podmioty wygląda następująco:

```azurepowershell
Principals              : {
                          DisplayName:  All Principals
                          ObjectType:   SystemDefined
                          ObjectId:     00000000-0000-0000-0000-000000000000
                          }
```

Wszystkie podmioty mogą być `ExcludePrincipals` łączone z odmówić wszystkich podmiotów z wyjątkiem niektórych użytkowników. Wszystkie podmioty mają następujące ograniczenia:

- Może być używany `Principals` tylko w `ExcludePrincipals`pliku .
- `Principals[i].Type`musi być `SystemDefined`ustawiona na .

## <a name="next-steps"></a>Następne kroki

* [Samouczek: Ochrona nowych zasobów za pomocą blokad zasobów usługi Azure Blueprints](../governance/blueprints/tutorials/protect-new-resources.md)
* [Lista odmów przydziałów dla zasobów platformy Azure przy użyciu witryny Azure portal](deny-assignments-portal.md)
