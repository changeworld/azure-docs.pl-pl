---
title: Role niestandardowe na platformie Azure | Dokumentacja firmy Microsoft
description: Poznaj sposoby definiowania ról niestandardowych przy użyciu kontroli dostępu opartej na rolach na platformie Azure (RBAC) dla dostępu do zarządzania zasobami na platformie Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: e4206ea9-52c3-47ee-af29-f6eef7566fa5
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/17/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d7554ef46289600cd15e4675a91f42a2cd735f18
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/18/2018
ms.locfileid: "39112665"
---
# <a name="custom-roles-in-azure"></a>Role niestandardowe na platformie Azure

Jeśli [role wbudowane](built-in-roles.md) nie spełniają specyficznych potrzeb Twojej organizacji, możesz utworzyć własne role niestandardowe. Podobnie jak wbudowane role można przypisać role niestandardowe do użytkowników, grup i nazwy główne usług w subskrypcji, grupy zasobów i zakresy zasobów. Role niestandardowe są przechowywane w dzierżawie usługi Azure Active Directory (Azure AD) i mogą być współużytkowane w różnych subskrypcjach. Każda dzierżawa może mieć maksymalnie 2000 ról niestandardowych. Role niestandardowe można tworzyć przy użyciu programu Azure PowerShell, interfejsu wiersza polecenia platformy Azure lub interfejsu API REST.

## <a name="custom-role-example"></a>Przykład rolę niestandardową

Na poniższym obrazie przedstawiono roli niestandardowej na potrzeby monitorowania i ponowne uruchamianie maszyn wirtualnych, jak wyświetlane przy użyciu programu Azure PowerShell:

```json
{
  "Name":  "Virtual Machine Operator",
  "Id":  "88888888-8888-8888-8888-888888888888",
  "IsCustom":  true,
  "Description":  "Can monitor and restart virtual machines.",
  "Actions":  [
                  "Microsoft.Storage/*/read",
                  "Microsoft.Network/*/read",
                  "Microsoft.Compute/*/read",
                  "Microsoft.Compute/virtualMachines/start/action",
                  "Microsoft.Compute/virtualMachines/restart/action",
                  "Microsoft.Authorization/*/read",
                  "Microsoft.Resources/subscriptions/resourceGroups/read",
                  "Microsoft.Insights/alertRules/*",
                  "Microsoft.Insights/diagnosticSettings/*",
                  "Microsoft.Support/*"
  ],
  "NotActions":  [

                 ],
  "DataActions":  [

                  ],
  "NotDataActions":  [

                     ],
  "AssignableScopes":  [
                           "/subscriptions/{subscriptionId1}",
                           "/subscriptions/{subscriptionId2}",
                           "/subscriptions/{subscriptionId3}"
                       ]
}
```

Po utworzeniu roli niestandardowej, pojawi się w witrynie Azure portal za pomocą ikony pomarańczowy zasobów.

![Ikona roli niestandardowej](./media/custom-roles/roles-custom-role-icon.png)

## <a name="steps-to-create-a-custom-role"></a>Kroki, aby utworzyć rolę niestandardową

1. Określ uprawnienia, których potrzebujesz

    Podczas tworzenia roli niestandardowej, musisz wiedzieć zasobu operacji dostawcy, które są dostępne do definiowania uprawnień. Aby wyświetlić listę operacji, można użyć [polecenia Get](/powershell/module/azurerm.resources/get-azurermprovideroperation) lub [lista operacji dostawcy az](/cli/azure/provider/operation#az-provider-operation-list) poleceń.
    Aby określić uprawnienia dla swojej roli niestandardowej, należy dodać operacje do `Actions` lub `NotActions` właściwości [definicji roli](role-definitions.md). Jeśli masz operacje na danych, należy dodać tych, które mają `DataActions` lub `NotDataActions` właściwości.

2. Tworzenie roli niestandardowej

    Aby utworzyć rolę niestandardową, można użyć programu Azure PowerShell lub wiersza polecenia platformy Azure. Zazwyczaj rozpoczynać istniejącą rolę wbudowaną, a następnie zmodyfikuj ją do własnych potrzeb. Następnie możesz użyć [New-AzureRmRoleDefinition](/powershell/module/azurerm.resources/new-azurermroledefinition) lub [utworzenia definicji roli az](/cli/azure/role/definition#az-role-definition-create) polecenia, aby utworzyć rolę niestandardową. Aby utworzyć rolę niestandardową, konieczne jest posiadanie `Microsoft.Authorization/roleDefinitions/write` uprawnienia na wszystkich `AssignableScopes`, takich jak [właściciela](built-in-roles.md#owner) lub [Administrator dostępu użytkowników](built-in-roles.md#user-access-administrator).

3. Testowanie roli niestandardowej

    Po utworzeniu roli użytkownika niestandardowego, należy go przetestować, aby sprawdzić, czy działa zgodnie z oczekiwaniami. Dostosowania należy podjąć, po zaktualizowaniu roli niestandardowej.

## <a name="custom-role-properties"></a>Właściwości roli niestandardowej

Rola niestandardowa ma następujące właściwości.

| Właściwość | Wymagane | Typ | Opis |
| --- | --- | --- | --- |
| `Name` | Yes | Ciąg | Wyświetlana nazwa roli niestandardowej. Musi być unikatowa dla dzierżawy. Może zawierać litery, cyfry, spacje i znaki specjalne. Maksymalna liczba znaków to 128. |
| `Id` | Yes | Ciąg | Unikatowy identyfikator roli niestandardowej. Dla programu Azure PowerShell i wiersza polecenia platformy Azure ten identyfikator jest generowany automatycznie po utworzeniu nowej roli. |
| `IsCustom` | Yes | Ciąg | Wskazuje, czy jest to rola niestandardowa. Ustaw `true` dla ról niestandardowych. |
| `Description` | Yes | Ciąg | Opis roli niestandardowej. Może zawierać litery, cyfry, spacje i znaki specjalne. Maksymalna liczba znaków wynosi 1024. |
| `Actions` | Yes | Ciąg] | Tablica ciągów określa operacje zarządzania, dozwolone przez rolę do wykonania. Aby uzyskać więcej informacji, zobacz [akcje](role-definitions.md#actions). |
| `NotActions` | Nie | Ciąg] | Tablica ciągów, który określa operacje zarządzania, które są wykluczone z dozwolonych `Actions`. Aby uzyskać więcej informacji, zobacz [NotActions](role-definitions.md#notactions). |
| `DataActions` | Nie | Ciąg] | Tablica ciągów określa operacje danych, dozwolone przez rolę do wykonania na danych w obrębie tego obiektu. Aby uzyskać więcej informacji, zobacz [elementy DataActions (wersja zapoznawcza)](role-definitions.md#dataactions-preview). |
| `NotDataActions` | Nie | Ciąg] | Tablica ciągów określa operacje danych, które są wykluczone z dozwolonych `DataActions`. Aby uzyskać więcej informacji, zobacz [NotDataActions (wersja zapoznawcza)](role-definitions.md#notdataactions-preview). |
| `AssignableScopes` | Yes | Ciąg] | Tablica ciągów określa zakresów, że rola niestandardowa jest dostępne do przypisania. Nie można ustawić na zakres głównego (`"/"`). Aby uzyskać więcej informacji, zobacz [AssignableScopes](role-definitions.md#assignablescopes). |

## <a name="assignablescopes-for-custom-roles"></a>assignableScopes dla ról niestandardowych

Podobnie jak w przypadku wbudowanych ról `AssignableScopes` właściwość określa zakresów, że rola jest dostępne do przypisania. Jednak nie można używać zakres głównego (`"/"`) proces niestandardowej. Jeśli spróbujesz, otrzymasz błąd autoryzacji. `AssignableScopes` Właściwość niestandardową rolę określa również, kto może tworzyć, usuwać, zmodyfikować lub wyświetlić rolę niestandardową.

| Zadanie | Operacja | Opis |
| --- | --- | --- |
| Tworzenie/usuwanie roli niestandardowej | `Microsoft.Authorization/ roleDefinition/write` | Użytkownicy, którzy otrzymują tę operację na wszystkich `AssignableScopes` roli niestandardowej można utworzyć (lub usunąć) ról niestandardowych do użytku w tych zakresach. Na przykład [właścicieli](built-in-roles.md#owner) i [Administratorzy dostępu użytkownika](built-in-roles.md#user-access-administrator) subskrypcji, grupy zasobów i zasobów. |
| Modyfikuj rolę niestandardową | `Microsoft.Authorization/ roleDefinition/write` | Użytkownicy, którzy otrzymują tę operację na wszystkich `AssignableScopes` rolę niestandardową można zmodyfikować niestandardowe role w tych zakresach. Na przykład [właścicieli](built-in-roles.md#owner) i [Administratorzy dostępu użytkownika](built-in-roles.md#user-access-administrator) subskrypcji, grupy zasobów i zasobów. |
| Wyświetl rolę niestandardową | `Microsoft.Authorization/ roleDefinition/read` | Użytkownicy, którzy otrzymują tej operacji w zakresie można wyświetlić role niestandardowe, które są dostępne do przypisania w tym zakresie. Wszystkie wbudowane role umożliwiają ról niestandardowych, które mają być dostępne do przypisania. |

## <a name="next-steps"></a>Kolejne kroki
- [Tworzenie ról niestandardowych przy użyciu programu Azure PowerShell](custom-roles-powershell.md)
- [Tworzenie ról niestandardowych przy użyciu wiersza polecenia platformy Azure](custom-roles-cli.md)
- [Zrozumienie definicje ról](role-definitions.md)
