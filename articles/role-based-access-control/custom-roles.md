---
title: Role niestandardowe dla zasobów platformy Azure | Dokumenty firmy Microsoft
description: Dowiedz się, jak tworzyć role niestandardowe za pomocą kontroli dostępu opartej na rolach (RBAC) do precyzyjnego zarządzania zasobami dostępu platformy Azure.
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
ms.date: 03/19/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9454962e210781559f2fdceb1c36f499c4ae8ff7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062168"
---
# <a name="custom-roles-for-azure-resources"></a>Niestandardowe role dla zasobów platformy Azure

> [!IMPORTANT]
> Dodawanie grupy zarządzania do `AssignableScopes` jest obecnie w wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Jeśli [wbudowane role dla zasobów platformy Azure](built-in-roles.md) nie spełniają potrzeb Twojej organizacji, możesz tworzyć własne role niestandardowe. Podobnie jak wbudowane role, można przypisać role niestandardowe do użytkowników, grup i podmiotów usługi w grupach zarządzania, subskrypcji i zakresów grup zasobów.

Role niestandardowe mogą być współużytkowane między subskrypcjami, które ufają temu samejszydarydu usługi Azure AD. Istnieje limit **5000** ról niestandardowych na katalog. (W przypadku platformy Azure w Niemczech i usłudze Azure China 21Vianet limit wynosi 2000 ról niestandardowych). Role niestandardowe można tworzyć za pomocą witryny Azure portal (Preview), Azure PowerShell, interfejsu wiersza polecenia platformy Azure lub interfejsu API REST.

## <a name="custom-role-example"></a>Przykład roli niestandardowej

Poniżej przedstawiono, jak wygląda rola niestandardowa wyświetlana w formacie JSON. Tej roli niestandardowej może służyć do monitorowania i ponownego uruchamiania maszyn wirtualnych.

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
    "/providers/Microsoft.Management/managementGroups/{groupId1}"
  ]
}
```

Podczas tworzenia roli niestandardowej, pojawia się w witrynie Azure portal z pomarańczową ikoną zasobu.

![Ikona roli niestandardowej](./media/custom-roles/roles-custom-role-icon.png)

## <a name="steps-to-create-a-custom-role"></a>Kroki tworzenia roli niestandardowej

1. Zdecyduj, jak chcesz utworzyć rolę niestandardową

    Role niestandardowe można tworzyć za pomocą [witryny Azure Portal](custom-roles-portal.md) (Preview), [Azure PowerShell,](custom-roles-powershell.md) [Azure CLI](custom-roles-cli.md)lub [REST API](custom-roles-rest.md).

1. Określanie potrzebnych uprawnień

    Podczas tworzenia roli niestandardowej, należy znać operacje dostawcy zasobów, które są dostępne do definiowania uprawnień. Aby wyświetlić listę operacji, zobacz [operacje dostawcy zasobów usługi Azure Resource Manager](resource-provider-operations.md). Operacje zostaną dodane do `Actions` `NotActions` lub właściwości [definicji roli](role-definitions.md). Jeśli masz operacje danych, dodasz `DataActions` je `NotDataActions` do właściwości lub.

1. Tworzenie roli niestandardowej

    Zazwyczaj należy rozpocząć od istniejącej wbudowanej roli, a następnie zmodyfikować go do swoich potrzeb. Następnie należy użyć [new-AzRoleDefinition](/powershell/module/az.resources/new-azroledefinition) lub [az definicji roli utworzyć,](/cli/azure/role/definition#az-role-definition-create) aby utworzyć rolę niestandardową. Aby utworzyć rolę niestandardową, `Microsoft.Authorization/roleDefinitions/write` musisz mieć `AssignableScopes`uprawnienia do wszystkich , takich jak [Administrator lub](built-in-roles.md#owner) [Administrator dostępu użytkownika.](built-in-roles.md#user-access-administrator)

1. Testowanie roli niestandardowej

    Gdy masz swoją rolę niestandardową, musisz przetestować ją, aby sprawdzić, czy działa zgodnie z oczekiwaniami. Jeśli konieczne jest późniejsze wprowadzać zmiany, można zaktualizować rolę niestandardową.

Aby uzyskać samouczek krok po kroku na temat tworzenia roli niestandardowej, zobacz [Samouczek: Tworzenie roli niestandardowej przy użyciu programu Azure PowerShell](tutorial-custom-role-powershell.md) lub [samouczka: Tworzenie roli niestandardowej przy użyciu interfejsu wiersza polecenia platformy Azure](tutorial-custom-role-cli.md).

## <a name="custom-role-properties"></a>Właściwości roli niestandardowej

Rola niestandardowa ma następujące właściwości.

| Właściwość | Wymagany | Typ | Opis |
| --- | --- | --- | --- |
| `Name` | Tak | Ciąg | Wyświetlana nazwa roli niestandardowej. Podczas gdy definicja roli jest zasób grupy zarządzania lub subskrypcji, definicja roli może służyć w wielu subskrypcjach, które współużytkuje ten sam katalog usługi Azure AD. Ta nazwa wyświetlana musi być unikatowa w zakresie katalogu usługi Azure AD. Może zawierać litery, cyfry, spacje i znaki specjalne. Maksymalna liczba znaków to 128. |
| `Id` | Tak | Ciąg | Unikatowy identyfikator roli niestandardowej. W przypadku programu Azure PowerShell i interfejsu wiersza polecenia platformy Azure ten identyfikator jest generowany automatycznie podczas tworzenia nowej roli. |
| `IsCustom` | Tak | Ciąg | Wskazuje, czy jest to rola niestandardowa. Ustaw `true` dla ról niestandardowych. |
| `Description` | Tak | Ciąg | Opis roli niestandardowej. Może zawierać litery, cyfry, spacje i znaki specjalne. Maksymalna liczba znaków to 1024. |
| `Actions` | Tak | Ciąg[] | Tablica ciągów, która określa operacje zarządzania, które rola pozwala na wykonanie. Aby uzyskać więcej informacji, zobacz [Akcje](role-definitions.md#actions). |
| `NotActions` | Nie | Ciąg[] | Tablica ciągów określających operacje zarządzania, które są `Actions`wykluczone z dozwolonej . Aby uzyskać więcej informacji, zobacz [NotActions](role-definitions.md#notactions). |
| `DataActions` | Nie | Ciąg[] | Tablica ciągów, która określa operacje danych, które rola pozwala na wykonanie danych w tym obiekcie. Jeśli utworzysz rolę `DataActions`niestandardową za pomocą programu , tej roli nie można przypisać do zakresu grupy zarządzania. Aby uzyskać więcej informacji, zobacz [DataActions](role-definitions.md#dataactions). |
| `NotDataActions` | Nie | Ciąg[] | Tablica ciągów określających operacje danych, które są `DataActions`wykluczone z dozwolonej . Aby uzyskać więcej informacji, zobacz [NotDataActions](role-definitions.md#notdataactions). |
| `AssignableScopes` | Tak | Ciąg[] | Tablica ciągów, która określa zakresy, które rola niestandardowa jest dostępna dla przypisania. Można zdefiniować tylko jedną `AssignableScopes` grupę zarządzania w roli niestandardowej. Dodawanie grupy zarządzania do `AssignableScopes` jest obecnie w wersji zapoznawczej. Aby uzyskać więcej informacji, zobacz [AssignableScopes](role-definitions.md#assignablescopes). |

## <a name="who-can-create-delete-update-or-view-a-custom-role"></a>Kto może tworzyć, usuwać, aktualizować lub wyświetlać rolę niestandardową

Podobnie jak wbudowane role, `AssignableScopes` właściwość określa zakresy, które rola jest dostępna dla przypisania. Właściwość `AssignableScopes` dla roli niestandardowej kontroluje również, kto może tworzyć, usuwać, aktualizować lub wyświetlać rolę niestandardową.

| Zadanie | Operacja | Opis |
| --- | --- | --- |
| Tworzenie/usuwanie roli niestandardowej | `Microsoft.Authorization/ roleDefinitions/write` | Użytkownicy, którym przyznano tę `AssignableScopes` operację na wszystkich rolach niestandardowych można utworzyć (lub usunąć) role niestandardowe do użycia w tych zakresach. Na przykład [administratorzy właścicieli](built-in-roles.md#owner) i [dostępu użytkowników](built-in-roles.md#user-access-administrator) grup zarządzania, subskrypcji i grup zasobów. |
| Aktualizacja roli niestandardowej | `Microsoft.Authorization/ roleDefinitions/write` | Użytkownicy, którym przyznano tę `AssignableScopes` operację we wszystkich rolach niestandardowych, mogą aktualizować role niestandardowe w tych zakresach. Na przykład [administratorzy właścicieli](built-in-roles.md#owner) i [dostępu użytkowników](built-in-roles.md#user-access-administrator) grup zarządzania, subskrypcji i grup zasobów. |
| Wyświetlanie roli niestandardowej | `Microsoft.Authorization/ roleDefinitions/read` | Użytkownicy, którym przyznano tę operację w zakresie, mogą wyświetlać role niestandardowe, które są dostępne do przypisania w tym zakresie. Wszystkie wbudowane role umożliwiają role niestandardowe, które mają być dostępne dla przypisania. |

## <a name="custom-role-limits"></a>Niestandardowe limity ról

Na poniższej liście opisano limity ról niestandardowych.

- Każdy katalog może mieć maksymalnie **5000** ról niestandardowych.
- Usługi Azure Niemcy i Azure China 21Vianet mogą mieć maksymalnie 2000 ról niestandardowych dla każdego katalogu.
- Nie można `AssignableScopes` ustawić zakresu`"/"`głównego ( ).
- Można zdefiniować tylko jedną `AssignableScopes` grupę zarządzania w roli niestandardowej. Dodawanie grupy zarządzania do `AssignableScopes` jest obecnie w wersji zapoznawczej.
- Role niestandardowe z `DataActions` nie można przypisać w zakresie grupy zarządzania.
- Usługa Azure Resource Manager nie sprawdza poprawności istnienia grupy zarządzania w zakresie przypisywalnym definicji roli.

Aby uzyskać więcej informacji na temat ról niestandardowych i grup zarządzania, zobacz [Organizowanie zasobów za pomocą grup zarządzania platformy Azure](../governance/management-groups/overview.md#custom-rbac-role-definition-and-assignment).

## <a name="next-steps"></a>Następne kroki
- [Tworzenie lub aktualizowanie ról niestandardowych platformy Azure przy użyciu witryny Azure Portal (Wersja zapoznawcza)](custom-roles-portal.md)
- [Opis definicji ról dla zasobów platformy Azure](role-definitions.md)
- [Rozwiązywanie problemów z materiałami RBAC dla platformy Azure](troubleshooting.md)
