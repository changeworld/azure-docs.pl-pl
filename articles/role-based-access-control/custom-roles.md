---
title: Role niestandardowe na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zdefiniować role niestandardowe przy użyciu kontroli dostępu opartej na rolach na platformie Azure (RBAC) dla precyzyjne zarządzanie dostępem zasobów na platformie Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: e4206ea9-52c3-47ee-af29-f6eef7566fa5
ms.service: role-based-access-control
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/12/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 074c305cb15bc1fb25dfa5cfc52dcce53b661a7e
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/22/2018
ms.locfileid: "36321178"
---
# <a name="custom-roles-in-azure"></a>Role niestandardowe na platformie Azure

Jeśli [wbudowane role](built-in-roles.md) nie spełnienia specyficznych potrzeb Twojej organizacji, możesz utworzyć własne niestandardowe role. Podobnie jak wbudowane role role niestandardowe można przypisać do użytkowników, grup i nazwy główne usług, subskrypcji, grupy zasobów i zakresy zasobów. Role niestandardowe są przechowywane w dzierżawie usługi Azure Active Directory (Azure AD) i mogą być udostępniane między subskrypcjami. Każdy Dzierżawca może mieć maksymalnie 2000 role niestandardowe. Role niestandardowe można tworzyć przy użyciu programu Azure PowerShell, interfejsu wiersza polecenia Azure lub interfejsu API REST.

## <a name="custom-role-example"></a>Przykład niestandardowej roli zabezpieczeń

Poniżej przedstawiono niestandardowej roli zabezpieczeń dotyczące monitorowania i ponowne uruchomienie maszyn wirtualnych, jak wyświetlane przy użyciu programu Azure PowerShell:

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

Po utworzeniu niestandardowej roli zabezpieczeń jest wyświetlana w portalu Azure za pomocą ikony pomarańczowy zasobów.

![Ikona niestandardowej roli zabezpieczeń](./media/custom-roles/roles-custom-role-icon.png)

## <a name="steps-to-create-a-custom-role"></a>Kroki umożliwiające utworzenie niestandardowej roli zabezpieczeń

1. Określają uprawnienia, które są potrzebne

    Tworząc niestandardową rolę, musisz wiedzieć zasobu operacji dostawcy, które są dostępne określić swoje uprawnienia. Aby wyświetlić listę działań, można użyć [Get-AzureRMProviderOperation](/powershell/module/azurerm.resources/get-azurermprovideroperation) lub [listy operacji dostawcy az](/cli/azure/provider/operation#az-provider-operation-list) poleceń.
    Aby określić uprawnienia dla niestandardowej roli zabezpieczeń, należy dodać operacje na `actions` lub `notActions` właściwości [definicji roli](role-definitions.md). Jeśli masz operacje na danych, możesz dodać je na `dataActions` lub `notDataActions` właściwości.

2. Tworzenie niestandardowych ról

    Azure PowerShell lub interfejsu wiersza polecenia Azure umożliwia tworzenie niestandardowych ról. Zazwyczaj rozpoczynać się od istniejącej roli wbudowane, a następnie zmodyfikować go do potrzeb. Użyć [AzureRmRoleDefinition nowy](/powershell/module/azurerm.resources/new-azurermroledefinition) lub [utworzenia definicji roli az](/cli/azure/role/definition#az-role-definition-create) poleceń, aby utworzyć niestandardowe roli. Aby utworzyć niestandardową rolę, musisz mieć `Microsoft.Authorization/roleDefinitions/write` uprawnienia we wszystkich `assignableScopes`, takich jak [właściciela](built-in-roles.md#owner) lub [Administrator dostępu użytkowników](built-in-roles.md#user-access-administrator).

3. Testowanie tworzona rola niestandardowa

    Po utworzeniu niestandardowej roli zabezpieczeń, należy go przetestować, aby sprawdzić, czy działa zgodnie z oczekiwaniami. Dostosowania należy wykonać, po zaktualizowaniu tworzona rola niestandardowa.

## <a name="custom-role-properties"></a>Właściwości niestandardowej roli zabezpieczeń

Rola niestandardowa ma następujące właściwości.

| Właściwość | Wymagane | Typ | Opis |
| --- | --- | --- | --- |
| `Name` | Yes | Ciąg | Nazwa wyświetlana rola niestandardowa. Musi być unikatowa dla Twojej dzierżawy. Może zawierać litery, cyfry, spacje i znaki specjalne. Maksymalna liczba znaków to 128. |
| `Id` | Yes | Ciąg | Unikatowy identyfikator roli niestandardowych. Dla programu Azure PowerShell i interfejsu wiersza polecenia Azure ten identyfikator jest generowany automatycznie podczas tworzenia nowej roli. |
| `IsCustom` | Yes | Ciąg | Wskazuje, czy jest niestandardowej roli zabezpieczeń. Ustaw `true` dla ról niestandardowych. |
| `Description` | Yes | Ciąg | Opis roli niestandardowej. Może zawierać litery, cyfry, spacje i znaki specjalne. Maksymalna liczba znaków wynosi 1024. |
| `Actions` | Yes | Ciąg] | Tablica ciągów określa operacji zarządzania, które pozwala rola do wykonania. Aby uzyskać więcej informacji, zobacz [akcje](role-definitions.md#actions). |
| `NotActions` | Nie | Ciąg] | Tablica ciągów określa operacje zarządzania, które są wykluczone z dozwolonych `actions`. Aby uzyskać więcej informacji, zobacz [notActions](role-definitions.md#notactions). |
| `DataActions` | Nie | Ciąg] | Tablica ciągów określa operacje danych, które pozwala rola wykonywanych do danych w tym obiekcie. Aby uzyskać więcej informacji, zobacz [dataActions (wersja zapoznawcza)](role-definitions.md#dataactions-preview). |
| `NotDataActions` | Nie | Ciąg] | Tablica ciągów określa operacje danych, które są wykluczone z dozwolonych `dataActions`. Aby uzyskać więcej informacji, zobacz [notDataActions (wersja zapoznawcza)](role-definitions.md#notdataactions-preview). |
| `AssignableScopes` | Yes | Ciąg] | Tablica ciągów Określa zakresy, że rola niestandardowa jest dostępne do przypisania. Nie można ustawić zakresu głównego (`"/"`). Aby uzyskać więcej informacji, zobacz [assignableScopes](role-definitions.md#assignablescopes). |

## <a name="assignablescopes-for-custom-roles"></a>assignableScopes dla ról niestandardowych

Podobnie jak wbudowane role `assignableScopes` właściwość określa zakresy, czy rola jest dostępny do przypisania. Jednak nie można używać w zakresie głównym (`"/"`) w niestandardowych ról. Jeśli spróbujesz, wystąpi błąd autoryzacji. `assignableScopes` Właściwości niestandardowej roli zabezpieczeń również określać, kto tworzenie, usuwanie, modyfikowanie lub wyświetlić tworzona rola niestandardowa.

| Zadanie | Operacja | Opis |
| --- | --- | --- |
| Tworzenie/usuwanie niestandardowej roli zabezpieczeń | `Microsoft.Authorization/ roleDefinition/write` | Użytkownicy, którzy otrzymują tej operacji na wszystkich `assignableScopes` niestandardowej roli można utworzyć (lub usunąć) ról niestandardowych do użytku w tych zakresów. Na przykład [właścicieli](built-in-roles.md#owner) i [Administratorzy dostępu użytkownika](built-in-roles.md#user-access-administrator) subskrypcji, grupy zasobów i zasoby. |
| Modyfikowanie niestandardowej roli zabezpieczeń | `Microsoft.Authorization/ roleDefinition/write` | Użytkownicy, którzy otrzymują tej operacji na wszystkich `assignableScopes` niestandardowej roli można zmodyfikować role niestandardowe w tych zakresów. Na przykład [właścicieli](built-in-roles.md#owner) i [Administratorzy dostępu użytkownika](built-in-roles.md#user-access-administrator) subskrypcji, grupy zasobów i zasoby. |
| Wyświetlanie niestandardowej roli zabezpieczeń | `Microsoft.Authorization/ roleDefinition/read` | Użytkownicy, którzy otrzymują tej operacji w zakresie mogą wyświetlać role niestandardowe, które są dostępne do przypisania w tym zakresie. Zezwalaj na wszystkie role wbudowane role niestandardowe, które mają być dostępne do przypisania. |

## <a name="next-steps"></a>Kolejne kroki
- [Tworzenie niestandardowych ról przy użyciu programu Azure PowerShell](custom-roles-powershell.md)
- [Tworzenie niestandardowych ról przy użyciu wiersza polecenia platformy Azure](custom-roles-cli.md)
- [Zrozumienie definicje ról](role-definitions.md)
