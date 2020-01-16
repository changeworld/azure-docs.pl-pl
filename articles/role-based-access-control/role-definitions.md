---
title: Informacje o definicjach ról w ramach RBAC dla zasobów platformy Azure | Microsoft Docs
description: Dowiedz się więcej na temat definicji ról w kontroli dostępu opartej na rolach (RBAC), aby uzyskać szczegółowe zarządzanie dostępem do zasobów platformy Azure.
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
ms.date: 09/11/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: ''
ms.openlocfilehash: 3ff4b2cb6a59a35dc6da4748a7c7fbb4758a4fcf
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75981001"
---
# <a name="understand-role-definitions-for-azure-resources"></a>Opis definicji ról dla zasobów platformy Azure

Jeśli próbujesz zrozumieć, jak działa rola, lub jeśli tworzysz własną [rolę niestandardową dla zasobów platformy Azure](custom-roles.md), warto zrozumieć, w jaki sposób role są definiowane. W tym artykule opisano szczegóły definicji ról i przedstawiono kilka przykładów.

## <a name="role-definition-structure"></a>Struktura definicji roli

*Definicja roli* to zbiór uprawnień. Czasami jest nazywana po prostu *rolą*. Definicja roli określa dozwolone operacje, na przykład odczyt, zapis, czy usuwanie. Może ona również zawierać listę operacji, których nie można wykonywać, lub operacji związanych z danymi źródłowymi. Definicja roli ma następującą strukturę:

```
Name
Id
IsCustom
Description
Actions []
NotActions []
DataActions []
NotDataActions []
AssignableScopes []
```

Operacje są określone za pomocą ciągów, które mają następujący format:

- `{Company}.{ProviderName}/{resourceType}/{action}`

Część `{action}` ciągu operacji określa typ operacji, które można wykonać dla typu zasobu. Na przykład następujące podciągi będą widoczne w `{action}`:

| Podciąg akcji    | Opis         |
| ------------------- | ------------------- |
| `*` | Symbol wieloznaczny daje dostęp do wszystkich operacji, które pasują do ciągu. |
| `read` | Włącza operacje odczytu (GET). |
| `write` | Włącza operacje zapisu (PUT lub PATCH). |
| `action` | Włącza niestandardowe operacje, takie jak ponowne uruchamianie maszyn wirtualnych (POST). |
| `delete` | Włącza operacje usuwania (usuwania). |

Oto definicja roli [współautor](built-in-roles.md#contributor) w formacie JSON. Symbol wieloznaczny (`*`) w obszarze `Actions` oznacza, że podmiot zabezpieczeń przypisany do tej roli może wykonywać wszystkie akcje, czyli może zarządzać wszystkim. Dotyczy to również akcji, które zostaną zdefiniowane, gdy do platformy Azure zostaną dodane nowe typy zasobów. Operacje w obszarze `NotActions` są odejmowane od zestawu operacji w obszarze `Actions`. W przypadku roli [Współautor](built-in-roles.md#contributor) zawartość właściwości `NotActions` uniemożliwia tej roli zarządzanie dostępem do zasobów oraz przypisywanie dostępu do zasobów.

```json
{
  "Name": "Contributor",
  "Id": "b24988ac-6180-42a0-ab88-20f7382dd24c",
  "IsCustom": false,
  "Description": "Lets you manage everything except access to resources.",
  "Actions": [
    "*"
  ],
  "NotActions": [
    "Microsoft.Authorization/*/Delete",
    "Microsoft.Authorization/*/Write",
    "Microsoft.Authorization/elevateAccess/Action"
  ],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/"
  ]
}
```

## <a name="management-and-data-operations"></a>Zarządzanie i operacje na danych

Kontrola dostępu oparta na rolach dla operacji zarządzania jest określona we właściwościach `Actions` i `NotActions` definicji roli. Poniżej przedstawiono kilka przykładów operacji zarządzania na platformie Azure:

- Zarządzanie dostępem do konta magazynu
- Tworzenie, aktualizowanie lub usuwanie kontenera obiektów BLOB
- Usuwanie grupy zasobów i wszystkich jej zasobów

Dostęp do zarządzania nie jest dziedziczony do danych, pod warunkiem, że metoda uwierzytelniania kontenera jest ustawiona na "konto użytkownika usługi Azure AD", a nie "klucz dostępu". Ta separacja zapobiega nieograniczonemu dostępowi do danych w rolach z symbolami wieloznacznymi (`*`). Na przykład jeśli użytkownik ma rolę [czytelnika](built-in-roles.md#reader) w ramach subskrypcji, może wyświetlić konto magazynu, ale domyślnie nie może wyświetlić danych źródłowych.

Wcześniej kontrola dostępu oparta na rolach nie była używana do wykonywania operacji na danych. Autoryzacja dla operacji na danych, które są różne dla różnych dostawców zasobów. Ten sam model autoryzacji kontroli dostępu oparty na rolach używany na potrzeby operacji zarządzania został rozszerzony do operacji na danych.

Aby obsługiwać operacje na danych, nowe właściwości danych zostały dodane do struktury definicji roli. Operacje na danych są definiowane za pomocą właściwości `DataActions` i `NotDataActions`. Po dodaniu tych właściwości danych jest zachowywane rozdzielenie między zarządzaniem i danymi. Zapobiega to nieoczekiwanemu uzyskiwaniu dostępu do danych za pośrednictwem bieżących przypisań ról z symbolami wieloznacznymi (`*`). Poniżej przedstawiono niektóre operacje na danych, określane za pomocą właściwości `DataActions` i `NotDataActions`:

- Odczyt listy obiektów blob w kontenerze
- Zapis obiektu blob magazynu w kontenerze
- Usuwanie komunikatu z kolejki

Oto definicja roli [czytnika danych obiektów blob magazynu](built-in-roles.md#storage-blob-data-reader) , która obejmuje operacje we właściwościach `Actions` i `DataActions`. Ta rola umożliwia odczytywanie kontenera obiektów blob, a także bazowe dane obiektów BLOB.

```json
{
  "Name": "Storage Blob Data Reader",
  "Id": "2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "IsCustom": false,
  "Description": "Allows for read access to Azure Storage blob containers and data",
  "Actions": [
    "Microsoft.Storage/storageAccounts/blobServices/containers/read"
  ],
  "NotActions": [],
  "DataActions": [
    "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read"
  ],
  "NotDataActions": [],
  "AssignableScopes": [
    "/"
  ]
}
```

Do właściwości `DataActions` i `NotDataActions` można dodawać tylko operacje na danych. Dostawcy zasobów identyfikują, które operacje są operacjami danych, ustawiając właściwość `isDataAction` na `true`. Aby wyświetlić listę operacji, w których `isDataAction` jest `true`, zobacz [operacje dostawcy zasobów](resource-provider-operations.md). Role, które nie mają operacji na danych, nie muszą mieć właściwości `DataActions` i `NotDataActions` w ramach definicji roli.

Autoryzacja wszystkich wywołań interfejsu API operacji zarządzania jest obsługiwana przez Azure Resource Manager. Autoryzacja wywołań interfejsu API operacji danych jest obsługiwana przez dostawcę zasobów lub Azure Resource Manager.

### <a name="data-operations-example"></a>Przykład operacji na danych

Aby lepiej zrozumieć, jak działają operacje zarządzania i danych, rozważmy konkretny przykład. Alicja przypisała rolę [właściciela](built-in-roles.md#owner) w zakresie subskrypcji. Robert ma przypisaną rolę [współautor danych obiektów blob magazynu](built-in-roles.md#storage-blob-data-contributor) w zakresie konta magazynu. Na poniższym diagramie przedstawiono ten przykład.

![Kontrola dostępu oparta na rolach została rozszerzona w celu obsługi operacji zarządzania i danych](./media/role-definitions/rbac-management-data.png)

Rola [właściciela](built-in-roles.md#owner) dla Alicja i rola [współautor danych obiektów blob magazynu](built-in-roles.md#storage-blob-data-contributor) dla Roberta mają następujące akcje:

Właściciel

&nbsp;&nbsp;&nbsp;działania &nbsp;<br>
&nbsp;&nbsp;&nbsp;&nbsp;`*`

Współautor danych obiektu blob magazynu

&nbsp;&nbsp;&nbsp;działania &nbsp;<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/delete`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/read`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/write`<br>
&nbsp;&nbsp;&nbsp;&nbsp;akcjami<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write`

Ponieważ Alicja ma akcję z symbolami wieloznacznymi (`*`) w zakresie subskrypcji, ich uprawnienia dziedziczą, aby umożliwić im wykonywanie wszystkich akcji zarządzania. Alicja może odczytywać, zapisywać i usuwać kontenery. Jednak Alicja nie może wykonywać operacji na danych bez podejmowania dodatkowych kroków. Na przykład domyślnie Alicja nie może odczytać obiektów BLOB w kontenerze. Aby odczytać obiekty blob, Alicja musiałaby pobrać klucze dostępu do magazynu i korzystać z nich w celu uzyskania dostępu do obiektów BLOB.

Uprawnienia Roberta są ograniczone tylko do `Actions` i `DataActions` określonych w roli [współautor danych obiektu blob magazynu](built-in-roles.md#storage-blob-data-contributor) . W oparciu o rolę Robert może wykonywać operacje związane z zarządzaniem i danymi. Na przykład Robert może odczytywać, zapisywać i usuwać kontenery na określonym koncie magazynu, a także odczytywać, zapisywać i usuwać obiekty blob.

Aby uzyskać więcej informacji na temat zarządzania i zabezpieczeń płaszczyzny danych dla magazynu, zobacz [Przewodnik po zabezpieczeniach usługi Azure Storage](../storage/blobs/security-recommendations.md).

### <a name="what-tools-support-using-rbac-for-data-operations"></a>Jakie narzędzia obsługują korzystanie z funkcji RBAC dla operacji na danych?

Aby wyświetlać operacje na danych i korzystać z nich, należy dysponować prawidłowymi wersjami narzędzi lub zestawów SDK:

| Narzędzie  | Wersja  |
|---------|---------|
| [Azure PowerShell](/powershell/azure/install-az-ps) | 1.1.0 lub nowszy |
| [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) | 2.0.30 lub nowszy |
| [Platforma Azure dla platformy .NET](/dotnet/azure/) | 2.8.0 — wersja zapoznawcza lub nowsza |
| [Zestaw Azure SDK dla języka Go](/azure/go/azure-sdk-go-install) | 15.0.0 lub nowszy |
| [Platforma Azure dla języka Java](/java/azure/) | 1.9.0 lub nowszy |
| [Platforma Azure dla języka Python](/azure/python/) | 0.40.0 lub nowszy |
| [Zestaw Azure SDK dla środowiska Ruby](https://rubygems.org/gems/azure_sdk) | 0.17.1 lub nowszy |

Aby wyświetlić operacje na danych w interfejsie API REST i korzystać z nich, należy ustawić parametr **API-Version** w następującej wersji lub nowszej:

- 2018-07-01

## <a name="actions"></a>Akcje

Uprawnienie `Actions` określa operacje zarządzania, które mogą być wykonywane przez rolę. Jest to kolekcja ciągów operacji, która identyfikuje zabezpieczone operacje dostawców zasobów platformy Azure. Poniżej przedstawiono kilka przykładów operacji zarządzania, które mogą być używane w `Actions`.

| Ciąg operacji    | Opis         |
| ------------------- | ------------------- |
| `*/read` | Przyznaje dostęp do operacji odczytu dla wszystkich typów zasobów wszystkich dostawców zasobów platformy Azure.|
| `Microsoft.Compute/*` | Przyznaje dostęp do wszystkich operacji dla wszystkich typów zasobów w dostawcy zasobów Microsoft. COMPUTE.|
| `Microsoft.Network/*/read` | Przyznaje dostęp do operacji odczytu dla wszystkich typów zasobów w dostawcy zasobów Microsoft. Network.|
| `Microsoft.Compute/virtualMachines/*` | Przyznaje dostęp do wszystkich operacji maszyn wirtualnych i jego podrzędnych typów zasobów.|
| `microsoft.web/sites/restart/Action` | Przyznaje dostęp do ponownego uruchomienia aplikacji sieci Web.|

## <a name="notactions"></a>NotActions

Uprawnienie `NotActions` określa operacje zarządzania, które są wykluczone z dozwolonej `Actions`. Użyj uprawnienia `NotActions`, jeśli zestaw operacji, do których chcesz zezwolić, jest łatwiej definiowany przez wykluczenie operacji ograniczonej. Dostęp udzielony przez rolę (czynne uprawnienia) jest obliczany przez odjęcie operacji `NotActions` z operacji `Actions`.

> [!NOTE]
> Jeśli użytkownik ma przypisaną rolę, która wyklucza operację w `NotActions`i ma przypisaną drugą rolę, która przyznaje dostęp do tej samej operacji, użytkownik może wykonać tę operację. `NotActions` nie jest regułą odmowy — jest to po prostu wygodny sposób utworzenia zestawu dozwolonych operacji, gdy wymagane jest wykluczenie określonych operacji.
>

## <a name="dataactions"></a>Akcje dataactions

Uprawnienie `DataActions` określa operacje na danych, które mogą być wykonywane na danych w tym obiekcie. Na przykład jeśli użytkownik odczytał dostęp do danych obiektów BLOB do konta magazynu, może odczytać obiekty blob w ramach tego konta magazynu. Poniżej przedstawiono kilka przykładów operacji na danych, które mogą być używane w `DataActions`.

| Ciąg operacji    | Opis         |
| ------------------- | ------------------- |
| `Microsoft.Storage/storageAccounts/ blobServices/containers/blobs/read` | Zwraca obiekt BLOB lub listę obiektów BLOB. |
| `Microsoft.Storage/storageAccounts/ blobServices/containers/blobs/write` | Zwraca wynik zapisania obiektu BLOB. |
| `Microsoft.Storage/storageAccounts/ queueServices/queues/messages/read` | Zwraca komunikat. |
| `Microsoft.Storage/storageAccounts/ queueServices/queues/messages/*` | Zwraca komunikat lub wynik zapisywania lub usuwania wiadomości. |

## <a name="notdataactions"></a>NotDataActions

Uprawnienie `NotDataActions` określa operacje na danych, które są wykluczone z dozwolonych `DataActions`. Dostęp udzielony przez rolę (czynne uprawnienia) jest obliczany przez odjęcie operacji `NotDataActions` z operacji `DataActions`. Każdy dostawca zasobów udostępnia swój odpowiedni zestaw interfejsów API do realizacji operacji na danych.

> [!NOTE]
> Jeśli użytkownik ma przypisaną rolę, która wyklucza operację danych w `NotDataActions`i ma przypisaną drugą rolę, która przyznaje dostęp do tej samej operacji danych, użytkownik może wykonać tę operację na danych. `NotDataActions` nie jest regułą odmowy — jest to po prostu wygodny sposób tworzenia zestawu dozwolonych operacji na danych, gdy wymagane jest wykluczenie określonych operacji na danych.
>

## <a name="assignablescopes"></a>AssignableScopes

Właściwość `AssignableScopes` określa zakresy (grupy zarządzania, subskrypcje, grupy zasobów lub zasoby), dla których ta definicja roli jest dostępna. Rolę można przypisać do przypisania tylko do grup zarządzania, subskrypcji lub grup zasobów, które go wymagają. Należy użyć co najmniej jednej grupy zarządzania, subskrypcji, grupy zasobów lub identyfikatora zasobu.

Wbudowane role mają ustawioną `AssignableScopes` zakresem głównym (`"/"`). Zakres główny wskazuje, że rola jest dostępna do przypisania we wszystkich zakresach. Przykłady prawidłowych zakresów do przypisania to:

| Rola jest dostępna do przypisania | Przykład |
|----------|---------|
| Jedna subskrypcja | `"/subscriptions/{subscriptionId1}"` |
| Dwie subskrypcje | `"/subscriptions/{subscriptionId1}", "/subscriptions/{subscriptionId2}"` |
| Grupa zasobów sieciowych | `"/subscriptions/{subscriptionId1}/resourceGroups/Network"` |
| Jedna grupa zarządzania | `"/providers/Microsoft.Management/managementGroups/{groupId1}"` |
| Grupa zarządzania i subskrypcja | `"/providers/Microsoft.Management/managementGroups/{groupId1}", /subscriptions/{subscriptionId1}",` |
| Wszystkie zakresy (dotyczy tylko ról wbudowanych) | `"/"` |

Aby uzyskać informacje na temat `AssignableScopes` ról niestandardowych, zobacz [role niestandardowe dla zasobów platformy Azure](custom-roles.md).

## <a name="next-steps"></a>Następne kroki

* [Wbudowane role dla zasobów platformy Azure](built-in-roles.md)
* [Niestandardowe role dla zasobów platformy Azure](custom-roles.md)
* [Operacje dostawcy zasobów Azure Resource Manager](resource-provider-operations.md)
