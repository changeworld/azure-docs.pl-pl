---
title: Role niestandardowe dla zasobów platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak tworzenie ról niestandardowych przy użyciu kontroli dostępu opartej na rolach (RBAC) dla dostępu do zarządzania zasobami platformy Azure.
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
ms.date: 02/22/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f795571de275453738d23e80885f4d9006ca3a20
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60195035"
---
# <a name="custom-roles-for-azure-resources"></a>Role niestandardowe dla zasobów platformy Azure

Jeśli [wbudowane role dla zasobów platformy Azure](built-in-roles.md) nie spełniają potrzeb Twojej organizacji, możesz tworzyć własne role niestandardowe. Podobnie jak wbudowane role można przypisać role niestandardowe do użytkowników, grup i nazwy główne usług w subskrypcji, grupy zasobów i zakresy zasobów. Role niestandardowe są przechowywane w katalogu usługi Azure Active Directory (Azure AD) i mogą być współużytkowane w różnych subskrypcjach. Każdy katalog może mieć maksymalnie 2000 ról niestandardowych. Role niestandardowe można tworzyć przy użyciu programu Azure PowerShell, interfejsu wiersza polecenia platformy Azure lub interfejsu API REST.

## <a name="custom-role-example"></a>Przykład rolę niestandardową

Poniżej przedstawiono, jak rolę niestandardową wygląda jak wyświetlane w formacie JSON. Tę rolę niestandardową, może służyć do monitorowania i ponowne uruchamianie maszyn wirtualnych.

```json
{
  "Name": "Virtual Machine Operator",
  "Id": "88888888-8888-8888-8888-888888888888",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.ResourceHealth/availabilityStatuses/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Insights/diagnosticSettings/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/subscriptions/{subscriptionId1}",
    "/subscriptions/{subscriptionId2}",
    "/subscriptions/{subscriptionId3}"
  ]
}
```

Podczas tworzenia roli niestandardowej pojawia się w witrynie Azure portal za pomocą ikony pomarańczowy zasobów.

![Ikona roli niestandardowej](./media/custom-roles/roles-custom-role-icon.png)

## <a name="steps-to-create-a-custom-role"></a>Kroki, aby utworzyć rolę niestandardową

1. Zdecyduj, jak chcesz utworzyć rolę niestandardową

    Można utworzyć za pomocą ról niestandardowych [programu Azure PowerShell](custom-roles-powershell.md), [wiersza polecenia platformy Azure](custom-roles-cli.md), lub [interfejsu API REST](custom-roles-rest.md).

1. Określ uprawnienia, których potrzebujesz

    Podczas tworzenia roli niestandardowej, musisz wiedzieć zasobu operacji dostawcy, które są dostępne do definiowania uprawnień. Aby wyświetlić listę operacji, zobacz [operacji dostawcy zasobów usługi Azure Resource Manager](resource-provider-operations.md). Należy dodać operacje do `Actions` lub `NotActions` właściwości [definicji roli](role-definitions.md). W przypadku operacji na danych spowoduje dodanie tych, które mają `DataActions` lub `NotDataActions` właściwości.

1. Tworzenie roli niestandardowej

    Zazwyczaj rozpoczynać istniejącą rolę wbudowaną, a następnie zmodyfikuj ją do własnych potrzeb. Następnie możesz użyć [New AzRoleDefinition](/powershell/module/az.resources/new-azroledefinition) lub [utworzenia definicji roli az](/cli/azure/role/definition#az-role-definition-create) polecenia, aby utworzyć rolę niestandardową. Aby utworzyć rolę niestandardową, konieczne jest posiadanie `Microsoft.Authorization/roleDefinitions/write` uprawnienia na wszystkich `AssignableScopes`, takich jak [właściciela](built-in-roles.md#owner) lub [Administrator dostępu użytkowników](built-in-roles.md#user-access-administrator).

1. Testowanie roli niestandardowej

    Po utworzeniu roli użytkownika niestandardowego, należy go przetestować, aby sprawdzić, czy działa zgodnie z oczekiwaniami. Jeśli musisz wprowadzić korekty później, należy zaktualizować roli niestandardowej.

Aby uzyskać samouczek krok po kroku dotyczące sposobu tworzenia roli niestandardowej, zobacz [samouczka: Utworzyć rolę niestandardową przy użyciu programu Azure PowerShell](tutorial-custom-role-powershell.md) lub [samouczka: Utworzyć rolę niestandardową przy użyciu wiersza polecenia platformy Azure](tutorial-custom-role-cli.md).

## <a name="custom-role-properties"></a>Właściwości roli niestandardowej

Rola niestandardowa ma następujące właściwości.

| Właściwość | Wymagane | Typ | Opis |
| --- | --- | --- | --- |
| `Name` | Yes | String | Wyświetlana nazwa roli niestandardowej. Definicja roli jest zasobem poziom subskrypcji, definicja roli może służyć w wielu subskrypcjach, które współużytkują ten sam katalog usługi Azure AD. Ta nazwa wyświetlana musi być unikatowa w zakresie katalogu usługi Azure AD. Może zawierać litery, cyfry, spacje i znaki specjalne. Maksymalna liczba znaków to 128. |
| `Id` | Yes | String | Unikatowy identyfikator roli niestandardowej. Dla programu Azure PowerShell i wiersza polecenia platformy Azure ten identyfikator jest generowany automatycznie po utworzeniu nowej roli. |
| `IsCustom` | Yes | String | Wskazuje, czy jest to rola niestandardowa. Ustaw `true` dla ról niestandardowych. |
| `Description` | Yes | String | Opis roli niestandardowej. Może zawierać litery, cyfry, spacje i znaki specjalne. Maksymalna liczba znaków wynosi 1024. |
| `Actions` | Yes | Ciąg] | Tablica ciągów określa operacje zarządzania, dozwolone przez rolę do wykonania. Aby uzyskać więcej informacji, zobacz [akcje](role-definitions.md#actions). |
| `NotActions` | Nie | Ciąg] | Tablica ciągów, który określa operacje zarządzania, które są wykluczone z dozwolonych `Actions`. Aby uzyskać więcej informacji, zobacz [NotActions](role-definitions.md#notactions). |
| `DataActions` | Nie | Ciąg] | Tablica ciągów określa operacje danych, dozwolone przez rolę do wykonania na danych w obrębie tego obiektu. Aby uzyskać więcej informacji, zobacz [elementy DataActions (wersja zapoznawcza)](role-definitions.md#dataactions-preview). |
| `NotDataActions` | Nie | Ciąg] | Tablica ciągów określa operacje danych, które są wykluczone z dozwolonych `DataActions`. Aby uzyskać więcej informacji, zobacz [NotDataActions (wersja zapoznawcza)](role-definitions.md#notdataactions-preview). |
| `AssignableScopes` | Yes | Ciąg] | Tablica ciągów określa zakresów, że rola niestandardowa jest dostępne do przypisania. Obecnie nie można ustawić na zakres głównego (`"/"`) lub zakres grupy zarządzania. Aby uzyskać więcej informacji, zobacz [AssignableScopes](role-definitions.md#assignablescopes) i [organizowanie zasobów przy użyciu grup zarządzania platformy Azure](../governance/management-groups/index.md#custom-rbac-role-definition-and-assignment). |

## <a name="who-can-create-delete-update-or-view-a-custom-role"></a>Kto może tworzyć, usunąć, zaktualizować lub wyświetlić rolę niestandardową

Podobnie jak w przypadku wbudowanych ról `AssignableScopes` właściwość określa zakresów, że rola jest dostępne do przypisania. `AssignableScopes` Właściwość niestandardową rolę kontroluje również, kto może tworzyć, usunąć, zaktualizować lub wyświetlić roli niestandardowej.

| Zadanie | Operacja | Opis |
| --- | --- | --- |
| Tworzenie/usuwanie roli niestandardowej | `Microsoft.Authorization/ roleDefinitions/write` | Użytkownicy, którzy otrzymują tę operację na wszystkich `AssignableScopes` roli niestandardowej można utworzyć (lub usunąć) ról niestandardowych do użytku w tych zakresach. Na przykład [właścicieli](built-in-roles.md#owner) i [Administratorzy dostępu użytkownika](built-in-roles.md#user-access-administrator) subskrypcji, grupy zasobów i zasobów. |
| Aktualizacja roli niestandardowej | `Microsoft.Authorization/ roleDefinitions/write` | Użytkownicy, którzy otrzymują tę operację na wszystkich `AssignableScopes` rolę niestandardową można zaktualizować ról niestandardowych w tych zakresach. Na przykład [właścicieli](built-in-roles.md#owner) i [Administratorzy dostępu użytkownika](built-in-roles.md#user-access-administrator) subskrypcji, grupy zasobów i zasobów. |
| Wyświetl rolę niestandardową | `Microsoft.Authorization/ roleDefinitions/read` | Użytkownicy, którzy otrzymują tej operacji w zakresie można wyświetlić role niestandardowe, które są dostępne do przypisania w tym zakresie. Wszystkie wbudowane role umożliwiają ról niestandardowych, które mają być dostępne do przypisania. |

## <a name="next-steps"></a>Kolejne kroki
- [Tworzenie ról niestandardowych dla zasobów platformy Azure przy użyciu programu Azure PowerShell](custom-roles-powershell.md)
- [Tworzenie ról niestandardowych dla zasobów platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure](custom-roles-cli.md)
- [Zrozumienie definicje ról na potrzeby zasobów platformy Azure](role-definitions.md)
- [Rozwiązywanie problemów z funkcji RBAC dla zasobów platformy Azure](troubleshooting.md)
