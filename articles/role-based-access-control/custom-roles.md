---
title: Role niestandardowe dla zasobów platformy Azure | Microsoft Docs
description: Dowiedz się, jak tworzyć role niestandardowe z kontrolą dostępu opartą na rolach (RBAC), aby uzyskać szczegółowe zarządzanie dostępem do zasobów platformy Azure.
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
ms.date: 06/07/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fbea0567ec125ce12acd8f757b32df723876fe09
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71338568"
---
# <a name="custom-roles-for-azure-resources"></a>Role niestandardowe dla zasobów platformy Azure

Jeśli [wbudowane role dla zasobów platformy Azure](built-in-roles.md) nie spełniają potrzeb Twojej organizacji, możesz tworzyć własne role niestandardowe. Podobnie jak wbudowane role można przypisywać niestandardowe role do użytkowników, grup i jednostek usługi w ramach subskrypcji, grupy zasobów i zakresów zasobów.

Role niestandardowe są przechowywane w katalogu Azure Active Directory (Azure AD) i mogą być współużytkowane przez subskrypcje. Każdy katalog może mieć maksymalnie **5000** ról niestandardowych. (W przypadku chmur wyspecjalizowanych, takich jak platformy Azure Government, Azure Niemcy i Azure Chiny 21Vianet limit wynosi 2000 ról niestandardowych). Role niestandardowe można tworzyć przy użyciu Azure PowerShell, interfejsu wiersza polecenia platformy Azure lub API REST.

## <a name="custom-role-example"></a>Przykład roli niestandardowej

Poniżej pokazano, jak wygląda rola niestandardowa, jak wyświetlana w formacie JSON. Ta rola niestandardowa może służyć do monitorowania i ponownego uruchamiania maszyn wirtualnych.

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

Gdy tworzysz rolę niestandardową, zostanie ona wyświetlona w Azure Portal z pomarańczowym ikoną zasobu.

![Ikona roli niestandardowej](./media/custom-roles/roles-custom-role-icon.png)

## <a name="steps-to-create-a-custom-role"></a>Procedura tworzenia roli niestandardowej

1. Zdecyduj, w jaki sposób chcesz utworzyć rolę niestandardową

    Role niestandardowe można tworzyć przy użyciu [Azure PowerShell](custom-roles-powershell.md), interfejsu [wiersza polecenia platformy Azure](custom-roles-cli.md)lub [API REST](custom-roles-rest.md).

1. Ustalanie potrzebnych uprawnień

    Podczas tworzenia roli niestandardowej należy znać operacje dostawcy zasobów, które są dostępne do definiowania uprawnień. Aby wyświetlić listę operacji, zapoznaj się z tematem [operacje dostawcy zasobów Azure Resource Manager](resource-provider-operations.md). Operacje zostaną dodane do `Actions` właściwości lub [](role-definitions.md) `NotActions` definicji roli. Jeśli masz operacje na danych, dodasz je do właściwości `DataActions` lub `NotDataActions`.

1. Utwórz rolę niestandardową

    Zazwyczaj należy zacząć od istniejącej wbudowanej roli, a następnie zmodyfikować ją na potrzeby Twoich potrzeb. Następnie użyj polecenia [New-AzRoleDefinition](/powershell/module/az.resources/new-azroledefinition) lub [AZ role Definition Create](/cli/azure/role/definition#az-role-definition-create) , aby utworzyć rolę niestandardową. Aby utworzyć rolę niestandardową, musisz mieć uprawnienie `Microsoft.Authorization/roleDefinitions/write` dla wszystkich `AssignableScopes`, takich jak [właściciel](built-in-roles.md#owner) lub [administrator dostępu użytkowników](built-in-roles.md#user-access-administrator).

1. Testowanie roli niestandardowej

    Gdy masz rolę niestandardową, musisz ją przetestować, aby upewnić się, że działa zgodnie z oczekiwaniami. Aby później wprowadzić zmiany, możesz zaktualizować rolę niestandardową.

Aby zapoznać się z samouczkiem krok po kroku na temat tworzenia roli niestandardowej, zobacz [Tutorial: Utwórz rolę niestandardową przy użyciu Azure PowerShell @ no__t-0 lub [Tutorial: Utwórz rolę niestandardową przy użyciu interfejsu wiersza polecenia platformy Azure @ no__t-0.

## <a name="custom-role-properties"></a>Właściwości roli niestandardowej

Rola niestandardowa ma następujące właściwości.

| Właściwość | Wymagane | Typ | Opis |
| --- | --- | --- | --- |
| `Name` | Yes | String | Nazwa wyświetlana roli niestandardowej. Chociaż definicja roli jest zasobem na poziomie subskrypcji, definicja roli może być używana w wielu subskrypcjach, które współużytkują ten sam katalog usługi Azure AD. Ta nazwa wyświetlana musi być unikatowa w zakresie katalogu usługi Azure AD. Może zawierać litery, cyfry, spacje i znaki specjalne. Maksymalna liczba znaków to 128. |
| `Id` | Tak | String | Unikatowy identyfikator roli niestandardowej. W przypadku Azure PowerShell i interfejsu wiersza polecenia platformy Azure ten identyfikator jest generowany automatycznie podczas tworzenia nowej roli. |
| `IsCustom` | Tak | String | Wskazuje, czy jest to rola niestandardowa. Ustaw wartość `true` dla ról niestandardowych. |
| `Description` | Tak | String | Opis roli niestandardowej. Może zawierać litery, cyfry, spacje i znaki specjalne. Maksymalna liczba znaków to 1024. |
| `Actions` | Tak | Ciąg [] | Tablica ciągów, która określa operacje zarządzania, które mogą być wykonywane przez rolę. Aby uzyskać więcej informacji, zobacz [Akcje](role-definitions.md#actions). |
| `NotActions` | Nie | Ciąg [] | Tablica ciągów, która określa operacje zarządzania, które są wykluczone z dozwolonej `Actions`. Aby uzyskać więcej informacji, [](role-definitions.md#notactions)Zobacz nonaruszone. |
| `DataActions` | Nie | Ciąg [] | Tablica ciągów, która określa operacje na danych, które mogą być wykonywane na danych w tym obiekcie. Aby uzyskać więcej informacji, zobacz temat [Dataactions](role-definitions.md#dataactions). |
| `NotDataActions` | Nie | Ciąg [] | Tablica ciągów, która określa operacje na danych, które są wykluczone z dozwolonej `DataActions`. Aby uzyskać więcej informacji, zobacz [NotDataActions](role-definitions.md#notdataactions). |
| `AssignableScopes` | Tak | Ciąg [] | Tablica ciągów, która określa zakresy, które rola niestandardowa jest dostępna do przypisania. W przypadku ról niestandardowych obecnie nie można ustawić `AssignableScopes` do zakresu głównego (`"/"`) ani zakresu grupy zarządzania. Aby uzyskać więcej informacji, zobacz [AssignableScopes](role-definitions.md#assignablescopes) i [organizowanie zasobów przy użyciu grup zarządzania platformy Azure](../governance/management-groups/overview.md#custom-rbac-role-definition-and-assignment). |

## <a name="who-can-create-delete-update-or-view-a-custom-role"></a>Kto może tworzyć, usuwać, aktualizować lub wyświetlać rolę niestandardową

Podobnie jak wbudowane role, właściwość `AssignableScopes` Określa zakresy, które rola jest dostępna do przypisania. Właściwość `AssignableScopes` roli niestandardowej kontroluje również, kto może tworzyć, usuwać, aktualizować lub wyświetlać rolę niestandardową.

| Zadanie | Operacja | Opis |
| --- | --- | --- |
| Utwórz/Usuń rolę niestandardową | `Microsoft.Authorization/ roleDefinitions/write` | Użytkownicy, którym udzielono tej operacji na wszystkich `AssignableScopes` roli niestandardowej, mogą tworzyć (lub usuwać) role niestandardowe do użycia w tych zakresach. Na przykład [właściciele](built-in-roles.md#owner) i [Administratorzy dostępu użytkowników do](built-in-roles.md#user-access-administrator) subskrypcji, grup zasobów i zasobów. |
| Aktualizacja roli niestandardowej | `Microsoft.Authorization/ roleDefinitions/write` | Użytkownicy, którym udzielono tej operacji na wszystkich `AssignableScopes` roli niestandardowej, mogą aktualizować role niestandardowe w tych zakresach. Na przykład [właściciele](built-in-roles.md#owner) i [Administratorzy dostępu użytkowników do](built-in-roles.md#user-access-administrator) subskrypcji, grup zasobów i zasobów. |
| Wyświetlanie roli niestandardowej | `Microsoft.Authorization/ roleDefinitions/read` | Użytkownicy, którym udzielono tej operacji w zakresie, mogą wyświetlać role niestandardowe, które są dostępne do przypisania w tym zakresie. Wszystkie wbudowane role umożliwiają przypisywanie ról niestandardowych. |

## <a name="next-steps"></a>Następne kroki
- [Tworzenie ról niestandardowych dla zasobów platformy Azure przy użyciu programu Azure PowerShell](custom-roles-powershell.md)
- [Tworzenie ról niestandardowych dla zasobów platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure](custom-roles-cli.md)
- [Opis definicji ról dla zasobów platformy Azure](role-definitions.md)
- [Rozwiązywanie problemów z funkcją RBAC dla zasobów platformy Azure](troubleshooting.md)
