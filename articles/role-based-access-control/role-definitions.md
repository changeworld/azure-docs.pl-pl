---
title: Zrozumienie definicje ról w Azure RBAC | Dokumentacja firmy Microsoft
description: Informacje o definicji roli w kontroli dostępu opartej na rolach (RBAC) oraz sposób definiowania ról niestandardowych dla precyzyjne zarządzanie dostępem zasobów na platformie Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/12/2018
ms.author: rolyon
ms.reviewer: rqureshi
ms.custom: ''
ms.openlocfilehash: 7a9e257d445ff7dadfe27d1c75cde6f58a393397
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
---
# <a name="understand-role-definitions"></a>Zrozumienie definicje ról

Jeśli próbujesz zrozumieć sposób działania roli lub w przypadku tworzenia własnych [niestandardowej roli zabezpieczeń](custom-roles.md), warto poznać sposób definiowania ról. W tym artykule opisano definicji ról oraz niektóre przykłady.

## <a name="role-definition-structure"></a>Struktura definicji roli

A *definicji roli* to kolekcja uprawnień. Jest to czasami właśnie *roli*. Definicja roli wymieniono operacje, które mogą być wykonywane, takich jak odczytu, zapisu i usuwania. Może również wyświetlać listę działań, których nie można wykonać operacji. Definicja roli ma następującą strukturę:

```
assignableScopes []
description
id
name
permissions []
  actions []
  notActions []
roleName
roleType
type
```

Operacje są określane za pomocą ciągów, które mają następujący format:

- `Microsoft.{ProviderName}/{ChildResourceType}/{action}`

`{action}` Część ciągu operacji określa typ operacji, które można wykonywać na typ zasobu. Na przykład, zostanie wyświetlony następujący podciągów w `{action}`:

| Substring akcji    | Opis         |
| ------------------- | ------------------- |
| `*` | Symbol wieloznaczny nieograniczony dostęp do wszystkich operacji, które zgodny z ciągiem. |
| `read` | Umożliwia odczytać operacji (GET). |
| `write` | Włącza zapisu (PUT, POST i poprawek). |
| `delete` | Umożliwia usuwanie operacji (Usuń). |

Oto [współautora](built-in-roles.md#contributor) definicji roli w formacie JSON. Symbol wieloznaczny (`*`) operacji w ramach `actions` wskazuje, że podmiot zabezpieczeń przypisanych do tej roli mogą wykonywać wszystkie akcje lub innymi słowy, jego mogą zarządzać wszystkim. Dotyczy to również akcje zdefiniowane w przyszłości, jak Azure dodaje nowe typy zasobów. Operacje w obszarze `notActions` jest odejmowany od `actions`. W przypadku liczby [współautora](built-in-roles.md#contributor) roli, `notActions` usuwa możliwość ta rola zarządzania dostępem do zasobów, a także przypisać dostęp do zasobów.

```json
[
  {
    "additionalProperties": {},
    "assignableScopes": [
      "/"
    ],
    "description": "Lets you manage everything except access to resources.",
    "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
    "name": "b24988ac-6180-42a0-ab88-20f7382dd24c",
    "permissions": [
      {
        "actions": [
          "*"
        ],
        "additionalProperties": {},
        "notActions": [
          "Microsoft.Authorization/*/Delete",
          "Microsoft.Authorization/*/Write",
          "Microsoft.Authorization/elevateAccess/Action"
        ],
      }
    ],
    "roleName": "Contributor",
    "roleType": "BuiltInRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

## <a name="management-operations"></a>Operacje zarządzania

Kontrola dostępu oparta na rolach dla operacji zarządzania została określona w `actions` i `notActions` sekcje definicji roli. Oto kilka przykładów operacji zarządzania na platformie Azure:

- Zarządzanie dostępem do konta magazynu
- Tworzenie, aktualizowanie lub usuwanie kontenera obiektów blob
- Usuń grupę zasobów i wszystkie jego zasoby

Dostęp do zarządzania nie jest dziedziczona z danymi. Ta separacja uniemożliwia ról z symbolami wieloznacznymi (`*`) z konieczności nieograniczony dostęp do danych. Na przykład, jeśli użytkownik ma [czytnika](built-in-roles.md#reader) roli w przypadku subskrypcji, następnie mogą wyświetlać konta magazynu, ale nie mogą wyświetlać dane.

## <a name="actions"></a>Akcje

`actions` Uprawnień określa operacje zarządzania, do których rola przyznaje dostęp. Jest kolekcją operacji ciągów, które identyfikują zabezpieczanego operacje dostawców zasobów platformy Azure. Oto kilka przykładów operacji zarządzania, które mogą być używane w `actions`.

| Operacja ciągu    | Opis         |
| ------------------- | ------------------- |
| `*/read` | przyznaje dostęp do odczytu dla operacji dla wszystkich typów zasobów wszystkich dostawców zasobów platformy Azure.|
| `Microsoft.Compute/*` | zapewnia dostęp do wszystkich operacji dla wszystkich typów zasobów w dostawcy zasobów Microsoft.Compute.|
| `Microsoft.Network/*/read` | Przyznaje dostęp do odczytu dla operacji dla wszystkich typów zasobów w dostawcę zasobów Microsoft.Network.|
| `Microsoft.Compute/virtualMachines/*` | przyznaje dostęp do wszystkich operacji maszyn wirtualnych i jego podrzędny typów zasobów.|
| `microsoft.web/sites/restart/Action` | Udziela dostępu do ponownego uruchomienia aplikacji sieci web.|

## <a name="notactions"></a>NotActions

`notActions` Uprawnień określa operacje zarządzania, które są wykluczone z dozwolonych `actions`. Użyj `notActions` uprawnienia, jeśli zbiór działań, które chcesz zezwolić łatwiej jest definiowana za pomocą z wyjątkiem operacji ograniczone. Prawa dostępu przyznane przez daną rolę (czynne uprawnienia) jest obliczana przez odjęcie `notActions` operacji `actions` operacji.

> [!NOTE]
> Jeśli użytkownik ma przypisaną rolę, który wyklucza operacji w `notActions`i ma przypisaną rolę drugiego, która udziela dostępu do tej samej operacji, użytkownik może wykonać tej operacji. `notActions` nie jest odmowy reguły — jest po prostu wygodny sposób utworzyć zestaw dozwolonych operacji podczas określonych operacji muszą być wykluczone.
>

## <a name="assignablescopes"></a>AssignableScopes

`assignableScopes` Sekcja określa zakresów (grup zarządzania (obecnie w wersji zapoznawczej), subskrypcji, grupy zasobów lub zasobów), czy rola jest dostępny do przypisania. Można udostępnić rolę do przypisania w tylko subskrypcji lub grupy zasobów, które wymagają, a nie użytkownika bałaganu doświadczenie w pozostałej części subskrypcji lub grupy zasobów. Należy użyć zarządzania co najmniej jedną grupę, subskrypcji, grupy zasobów lub identyfikator zasobu.

Wbudowane role mają `assignableScopes` ustawioną w zakresie głównym (`"/"`). Zakresie głównym wskazuje, że rola jest dostępne do przypisania w wszystkich zakresów. Nie można użyć w zakresie głównym własne niestandardowe role. Jeśli spróbujesz, wystąpi błąd autoryzacji.

Prawidłowe zakresy można przypisać należą:

| Scenariusz | Przykład |
|----------|---------|
| Rola jest dostępne do przypisania w ramach jednej subskrypcji | `"/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"` |
| Rola jest dostępne do przypisania w ramach dwóch subskrypcji. | `"/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e", "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"` |
| Rola jest dostępne do przypisania tylko w grupie zasobów sieciowych | `"/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network"` |
| Rola jest dostępne do przypisania w wszystkich zakresów | `"/"` |

## <a name="assignablescopes-and-custom-roles"></a>assignableScopes i role niestandardowe

`assignableScopes` Sekcji niestandardowej roli zabezpieczeń również formantów, który można utworzyć, usunąć, zmodyfikować lub wyświetlić tworzona rola niestandardowa.

| Zadanie | Operacja | Opis |
| --- | --- | --- |
| Tworzenie/usuwanie niestandardowej roli zabezpieczeń | `Microsoft.Authorization/ roleDefinition/write` | Użytkownicy, którzy otrzymują tej operacji na wszystkich `assignableScopes` niestandardowej roli można utworzyć (lub usunąć) ról niestandardowych do użytku w tych zakresów. Na przykład [właścicieli](built-in-roles.md#owner) i [Administratorzy dostępu użytkownika](built-in-roles.md#user-access-administrator) subskrypcji, grupy zasobów i zasoby. |
| Modyfikowanie niestandardowej roli zabezpieczeń | `Microsoft.Authorization/ roleDefinition/write` | Użytkownicy, którzy otrzymują tej operacji na wszystkich `assignableScopes` niestandardowej roli można zmodyfikować role niestandardowe w tych zakresów. Na przykład [właścicieli](built-in-roles.md#owner) i [Administratorzy dostępu użytkownika](built-in-roles.md#user-access-administrator) subskrypcji, grupy zasobów i zasoby. |
| Wyświetlanie niestandardowej roli zabezpieczeń | `Microsoft.Authorization/ roleDefinition/read` | Użytkownicy, którzy otrzymują tej operacji w zakresie mogą wyświetlać role niestandardowe, które są dostępne do przypisania w tym zakresie. Zezwalaj na wszystkie role wbudowane role niestandardowe, które mają być dostępne do przypisania. |

## <a name="role-definition-examples"></a>Przykłady definicji roli

W poniższym przykładzie przedstawiono [czytnika](built-in-roles.md#reader) definicji roli wyświetlane przy użyciu wiersza polecenia platformy Azure:

```json
[
  {
    "additionalProperties": {},
    "assignableScopes": [
      "/"
    ],
    "description": "Lets you view everything, but not make any changes.",
    "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "name": "acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "permissions": [
      {
        "actions": [
          "*/read"
        ],
        "additionalProperties": {},
        "notActions": [],
      }
    ],
    "roleName": "Reader",
    "roleType": "BuiltInRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

W poniższym przykładzie przedstawiono niestandardowej roli zabezpieczeń dotyczące monitorowania i ponowne uruchomienie maszyn wirtualnych, jak wyświetlane przy użyciu programu Azure PowerShell:

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
  "AssignableScopes":  [
                           "/subscriptions/{subscriptionId1}",
                           "/subscriptions/{subscriptionId2}",
                           "/subscriptions/{subscriptionId3}"
                       ]
}
```

## <a name="see-also"></a>Zobacz także

* [Role wbudowane](built-in-roles.md)
* [Role niestandardowe](custom-roles.md)
* [Operacje dostawcy zasobów Menedżera zasobów platformy Azure](resource-provider-operations.md)
