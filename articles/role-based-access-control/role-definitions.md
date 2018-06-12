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
ms.date: 05/18/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: ''
ms.openlocfilehash: 9bb7808f2b483fe9cd7d22c6df3fe80d4a98f1f4
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35266860"
---
# <a name="understand-role-definitions"></a>Omówienie definicji ról

Jeśli próbujesz zrozumieć sposób działania roli lub w przypadku tworzenia własnych [niestandardowej roli zabezpieczeń](custom-roles.md), warto poznać sposób definiowania ról. W tym artykule opisano definicji ról oraz niektóre przykłady.

## <a name="role-definition-structure"></a>Struktura definicji roli

A *definicji roli* to kolekcja uprawnień. Jest to czasami właśnie *roli*. Definicja roli wymieniono operacje, które mogą być wykonywane, takich jak odczytu, zapisu i usuwania. Można również podać, operacje, których nie można wykonać lub operacje związane z danych. Definicja roli ma następującą strukturę:

```
assignableScopes []
description
id
name
permissions []
  actions []
  dataActions []
  notActions []
  notDataActions []
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
        "dataActions": [],
        "notActions": [
          "Microsoft.Authorization/*/Delete",
          "Microsoft.Authorization/*/Write",
          "Microsoft.Authorization/elevateAccess/Action"
        ],
        "notDataActions": []
      }
    ],
    "roleName": "Contributor",
    "roleType": "BuiltInRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

## <a name="management-and-data-operations-preview"></a>Operacje zarządzania oraz danych (wersja zapoznawcza)

Kontrola dostępu oparta na rolach dla operacji zarządzania została określona w `actions` i `notActions` sekcje definicji roli. Oto kilka przykładów operacji zarządzania na platformie Azure:

- Zarządzanie dostępem do konta magazynu
- Tworzenie, aktualizowanie lub usuwanie kontenera obiektów blob
- Usuń grupę zasobów i wszystkie jego zasoby

Dostęp do zarządzania nie jest dziedziczona z danymi. Ta separacja uniemożliwia ról z symbolami wieloznacznymi (`*`) z konieczności nieograniczony dostęp do danych. Na przykład, jeśli użytkownik ma [czytnika](built-in-roles.md#reader) roli w przypadku subskrypcji, następnie mogą wyświetlać konta magazynu, ale domyślnie nie można wyświetlić danych.

Wcześniej kontroli dostępu opartej na rolach nie był używany podczas operacji danych. Autoryzacji danych operacje różnić między dostawców zasobów. Operacje na danych (obecnie w wersji zapoznawczej) wzbogacono modelu autoryzacji tego samego kontroli dostępu opartej na rolach używane dla operacji zarządzania.

Do obsługi operacji danych, struktura definicji roli dodano nowe sekcje danych. Operacje na danych są określone w `dataActions` i `notDataActions` sekcje. Dodając poniższe sekcje danych rozdzielenie zarządzania oraz danych jest obsługiwana. Zapobiega to bieżące przypisania roli z symbolami wieloznacznymi (`*`) z nagle o uzyskanie dostępu do danych. Poniżej przedstawiono niektóre operacje danych, które można określić w `dataActions` i `notDataActions`:

- Odczytaj listę obiektów blob w kontenerze
- Zapis w kontenerze obiektu blob magazynu
- Usuwanie wiadomości w kolejce

Oto [czytnik danych magazynu obiektów Blob (wersja zapoznawcza)](built-in-roles.md#storage-blob-data-reader-preview) definicji roli, w tym operacje w obu `actions` i `dataActions` sekcje. Ta rola umożliwia odczytanie kontenera obiektów blob, a także dane obiektów blob.

```json
[
  {
    "additionalProperties": {},
    "assignableScopes": [
      "/"
    ],
    "description": "Allows for read access to Azure Storage blob containers and data.",
    "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
    "name": "2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
    "permissions": [
      {
        "actions": [
          "Microsoft.Storage/storageAccounts/blobServices/containers/read"
        ],
        "additionalProperties": {},
        "dataActions": [
          "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read"
        ],
        "notActions": [],
        "notDataActions": []
      }
    ],
    "roleName": "Storage Blob Data Reader (Preview)",
    "roleType": "BuiltInRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

Tylko operacje na danych mogą być dodawane do `dataActions` i `notDataActions` sekcje. Dostawcy zasobów określenie, jakie operacje są operacje na danych, ustawiając `isDataAction` właściwości `true`. Aby wyświetlić listę operacji gdzie `isDataAction` jest `true`, zobacz [operacji dostawcy zasobów](resource-provider-operations.md). Role, które nie mają danych operacji nie muszą mieć `dataActions` i `notDataActions` sekcje w ramach definicji roli.

Autoryzacji dla wszystkich wywołań interfejsu API operacji zarządzania jest obsługiwany przez Menedżera zasobów Azure. Autoryzacji do wywołania interfejsu API operacji danych jest obsługiwane przez dostawcę zasobów lub usługi Azure Resource Manager.

### <a name="data-operations-example"></a>Przykład operacje danych

Aby lepiej zrozumieć, jak działają operacje zarządzania oraz danych, zastanówmy się z konkretnym przykładzie. Alicja zostanie przypisana [właściciela](built-in-roles.md#owner) rola w zakresie subskrypcji. Przypisano Bob [magazynu obiektów Blob danych Współautor (wersja zapoznawcza)](built-in-roles.md#storage-blob-data-contributor-preview) roli w zakresie konta magazynu. Na poniższym diagramie przedstawiono w tym przykładzie.

![Kontrola dostępu oparta na rolach został rozszerzony do obsługi zarządzania i operacje na danych](./media/role-definitions/rbac-management-data.png)

[Właściciela](built-in-roles.md#owner) roli dla Alicji i [magazynu obiektów Blob danych Współautor (wersja zapoznawcza)](built-in-roles.md#storage-blob-data-contributor-preview) roli dla Roberta ma następujące akcje:

Właściciel

&nbsp;&nbsp;&nbsp;&nbsp;Akcje<br>
&nbsp;&nbsp;&nbsp;&nbsp;`*`

Współautor danych obiektu blob magazynu (wersja zapoznawcza)

&nbsp;&nbsp;&nbsp;&nbsp;Akcje<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/delete`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/read`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/write`<br>
&nbsp;&nbsp;&nbsp;&nbsp;DataActions<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write`

Ponieważ Alicja ma symbolu wieloznacznego (`*`) działania w zakresie subskrypcji, jej uprawnienia dziedziczyć w dół umożliwia jej wykonywać wszystkie akcje zarządzania. Jednak Alicja nie mogą wykonywać operacje na danych. Na przykład domyślnie Alicja nie może odczytać obiektów blob w kontenerze, ale użytkownik może odczytu, zapisu i usuwania kontenerów.

Roberta uprawnienia są ograniczone do właśnie `actions` i `dataActions` określony w [magazynu obiektów Blob danych Współautor (wersja zapoznawcza)](built-in-roles.md#storage-blob-data-contributor-preview) roli. W zależności od roli, Roberta, który można wykonać zarówno zarządzanie i operacje na danych. Na przykład Robert może odczytu, zapisu i usuwania kontenerów w podanego konta magazynu i on można również do odczytu, zapisu i usuwania obiektów blob.

### <a name="what-tools-support-using-rbac-for-data-operations"></a>Jakie narzędzia obsługi przy użyciu funkcji RBAC dla operacji danych?

Aby wyświetlić i pracować z operacji danych, musi mieć poprawne wersje narzędzia i zestawy SDK:

| Narzędzie  | Wersja  |
|---------|---------|
| [Azure PowerShell](/powershell/azure/install-azurerm-ps) | 5.6.0 lub nowszy |
| [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) | 2.0.30 lub nowszy |
| [Azure dla platformy .NET](/dotnet/azure/) | 2.8.0-Preview lub nowszy |
| [Zestaw Azure SDK dla przejdź](/go/azure/azure-sdk-go-install) | 15.0.0 lub nowszy |
| [Azure dla języka Java](/java/azure/) | 1.9.0 lub nowszy |
| [Azure dla języka Python](/python/azure) | 0.40.0 lub nowszy |
| [Zestaw Azure SDK dla środowiska Ruby](https://rubygems.org/gems/azure_sdk) | 0.17.1 lub nowszy |

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

## <a name="dataactions-preview"></a>dataActions (wersja zapoznawcza)

`dataActions` Uprawnień określa operacje danych, do których rola przyznaje dostęp do danych w tym obiekcie. Na przykład jeśli użytkownik ma obiektu blob danych dostęp do odczytu konta magazynu, następnie będzie mogła ją odczytać w ramach tego konta magazynu obiektów blob. Oto kilka przykładów operacje na danych, które mogą być używane w `dataActions`.

| Operacja ciągu    | Opis         |
| ------------------- | ------------------- |
| `Microsoft.Storage/storageAccounts/ blobServices/containers/blobs/read` | Zwraca obiekt blob lub listę obiektów blob. |
| `Microsoft.Storage/storageAccounts/ blobServices/containers/blobs/write` | Zwraca wynik zapisu obiektu blob. |
| `Microsoft.Storage/storageAccounts/ queueServices/queues/messages/read` | Zwraca komunikat. |
| `Microsoft.Storage/storageAccounts/ queueServices/queues/messages/*` | Zwraca wynik zapisywanie lub usuwanie wiadomości lub wiadomości. |

## <a name="notdataactions-preview"></a>notDataActions (wersja zapoznawcza)

`notDataActions` Uprawnień określa operacje danych, które są wykluczone z dozwolonych `dataActions`. Prawa dostępu przyznane przez daną rolę (czynne uprawnienia) jest obliczana przez odjęcie `notDataActions` operacji `dataActions` operacji. Każdy dostawca zasobów udostępnia jego odpowiednich zestaw interfejsów API do wykonania operacji danych.

> [!NOTE]
> Jeśli użytkownik ma przypisaną rolę, który wyklucza operacji danych w `notDataActions`i ma przypisaną rolę drugiego, która udziela dostępu do tej samej operacji danych, użytkownik może wykonać tej operacji danych. `notDataActions` nie jest odmowy reguły — jest po prostu wygodny sposób utworzyć zestaw danych dozwolonych operacji podczas operacji określonych danych należy do wykluczenia.
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
        "dataActions": [],
        "notActions": [],
        "notDataActions": []
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

## <a name="see-also"></a>Zobacz także

* [Role wbudowane](built-in-roles.md)
* [Role niestandardowe](custom-roles.md)
* [Operacje dostawcy zasobów Menedżera zasobów platformy Azure](resource-provider-operations.md)
