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
ms.date: 06/18/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: ''
ms.openlocfilehash: 2ec3872b9e11830f7891e98f5fc0182b99e1586d
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/23/2019
ms.locfileid: "69997347"
---
# <a name="understand-role-definitions-for-azure-resources"></a>Opis definicji ról dla zasobów platformy Azure

Jeśli próbujesz zrozumieć, jak działa rola, lub jeśli tworzysz własną rolę niestandardową [dla zasobów platformy Azure](custom-roles.md), warto zrozumieć, w jaki sposób role są definiowane. W tym artykule opisano szczegóły definicji ról i przedstawiono kilka przykładów.

## <a name="role-definition-structure"></a>Struktura definicji roli

*Definicja roli* to zbiór uprawnień. Czasami jest nazywana po prostu *rolą*. Definicja roli określa dozwolone operacje, na przykład odczyt, zapis, czy usuwanie. Może również zawierać listę operacji, które nie mogą być wykonywane lub operacje związane z danymi źródłowymi. Definicja roli ma następującą strukturę:

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

`{action}` Część ciągu operacji określa typ operacji, które można wykonać dla typu zasobu. Na przykład następujące podciągi będą widoczne w `{action}`:

| Podciąg akcji    | Opis         |
| ------------------- | ------------------- |
| `*` | Symbol wieloznaczny daje dostęp do wszystkich operacji, które pasują do ciągu. |
| `read` | Włącza operacje odczytu (GET). |
| `write` | Włącza operacje zapisu (PUT lub PATCH). |
| `action` | Włącza niestandardowe operacje, takie jak ponowne uruchamianie maszyn wirtualnych (POST). |
| `delete` | Włącza operacje usuwania (usuwania). |

Oto definicja roli [współautor](built-in-roles.md#contributor) w formacie JSON. Symbol wieloznaczny (`*`) `Actions` w obszarze wskazuje, że podmiot zabezpieczeń przypisany do tej roli może wykonywać wszystkie akcje, lub innymi słowy, może zarządzać wszystko. Obejmuje to akcje zdefiniowane w przyszłości, ponieważ platforma Azure dodaje nowe typy zasobów. Operacje objęte `NotActions` są odejmowane od `Actions`. W przypadku roli [współautor](built-in-roles.md#contributor) program `NotActions` usuwa możliwość zarządzania dostępem do zasobów, a także przydziela dostęp do zasobów.

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

Kontrola dostępu oparta na rolach dla operacji zarządzania jest określona we `Actions` właściwościach i `NotActions` definicji roli. Poniżej przedstawiono kilka przykładów operacji zarządzania na platformie Azure:

- Zarządzanie dostępem do konta magazynu
- Tworzenie, aktualizowanie lub usuwanie kontenera obiektów BLOB
- Usuwanie grupy zasobów i wszystkich jej zasobów

Dostęp do zarządzania nie jest dziedziczony do danych, pod warunkiem, że metoda uwierzytelniania kontenera jest ustawiona na "konto użytkownika usługi Azure AD", a nie "klucz dostępu". Ta separacja zapobiega nieograniczonemu dostępowi do danych w rolach z symbolami wieloznacznymi (`*`). Na przykład jeśli użytkownik ma rolę czytelnika [](built-in-roles.md#reader) w ramach subskrypcji, może wyświetlić konto magazynu, ale domyślnie nie może wyświetlić danych źródłowych.

Wcześniej kontrola dostępu oparta na rolach nie była używana do wykonywania operacji na danych. Autoryzacja dla operacji na danych, które są różne dla różnych dostawców zasobów. Ten sam model autoryzacji kontroli dostępu oparty na rolach używany na potrzeby operacji zarządzania został rozszerzony do operacji na danych.

Aby obsługiwać operacje na danych, nowe właściwości danych zostały dodane do struktury definicji roli. Operacje na danych są określone we `DataActions` właściwościach i `NotDataActions` . Po dodaniu tych właściwości danych jest zachowywane rozdzielenie między zarządzaniem i danymi. Zapobiega to nieoczekiwanemu dostępowi do danych`*`przy użyciu symboli wieloznacznych (). Poniżej przedstawiono niektóre operacje na danych, które można określić `DataActions` w `NotDataActions`i:

- Odczytaj listę obiektów BLOB w kontenerze
- Zapisywanie obiektu blob magazynu w kontenerze
- Usuwanie komunikatu w kolejce

Oto definicja roli [czytnika danych obiektów blob magazynu](built-in-roles.md#storage-blob-data-reader) , która obejmuje operacje we `Actions` właściwościach i. `DataActions` Ta rola umożliwia odczytywanie kontenera obiektów blob, a także bazowe dane obiektów BLOB.

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

Do `DataActions` właściwości i `NotDataActions` można dodawać tylko operacje danych. Dostawcy zasobów identyfikują, które operacje są operacjami danych, `isDataAction` ustawiając właściwość `true`na. Aby wyświetlić listę operacji, gdzie `isDataAction` is `true`, zobacz [operacje dostawcy zasobów](resource-provider-operations.md). Role, które nie mają operacji na danych, nie muszą mieć `DataActions` ani `NotDataActions` właściwości w ramach definicji roli.

Autoryzacja wszystkich wywołań interfejsu API operacji zarządzania jest obsługiwana przez Azure Resource Manager. Autoryzacja wywołań interfejsu API operacji danych jest obsługiwana przez dostawcę zasobów lub Azure Resource Manager.

### <a name="data-operations-example"></a>Przykład operacji na danych

Aby lepiej zrozumieć, jak działają operacje zarządzania i danych, rozważmy konkretny przykład. Alicja przypisała rolę [właściciela](built-in-roles.md#owner) w zakresie subskrypcji. Robert ma przypisaną rolę [współautor danych obiektów blob magazynu](built-in-roles.md#storage-blob-data-contributor) w zakresie konta magazynu. Na poniższym diagramie przedstawiono ten przykład.

![Kontrola dostępu oparta na rolach została rozszerzona w celu obsługi operacji zarządzania i danych](./media/role-definitions/rbac-management-data.png)

Rola [właściciela](built-in-roles.md#owner) dla Alicja i rola [współautor danych obiektów blob magazynu](built-in-roles.md#storage-blob-data-contributor) dla Roberta mają następujące akcje:

Właściciel

&nbsp;&nbsp;&nbsp;&nbsp;Wykonane<br>
&nbsp;&nbsp;&nbsp;&nbsp;`*`

Współautor danych obiektu blob usługi Storage

&nbsp;&nbsp;&nbsp;&nbsp;Wykonane<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/delete`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/read`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/write`<br>
&nbsp;&nbsp;&nbsp;&nbsp;Akcje dataactions<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write`

Ponieważ Alicja ma akcję symbol wieloznaczny (`*`) w zakresie subskrypcji, ich uprawnienia dziedziczą, aby umożliwić im wykonywanie wszystkich akcji zarządzania. Alicja może odczytywać, zapisywać i usuwać kontenery. Jednak Alicja nie może wykonywać operacji na danych bez podejmowania dodatkowych kroków. Na przykład domyślnie Alicja nie może odczytać obiektów BLOB w kontenerze. Aby odczytać obiekty blob, Alicja musiałaby pobrać klucze dostępu do magazynu i korzystać z nich w celu uzyskania dostępu do obiektów BLOB.

Uprawnienia Roberta są ograniczone tylko `Actions` `DataActions` do określonych w roli [współautor danych obiektu blob magazynu](built-in-roles.md#storage-blob-data-contributor) . W oparciu o rolę Robert może wykonywać operacje związane z zarządzaniem i danymi. Na przykład Robert może odczytywać, zapisywać i usuwać kontenery na określonym koncie magazynu, a także odczytywać, zapisywać i usuwać obiekty blob.

Aby uzyskać więcej informacji na temat zarządzania i zabezpieczeń płaszczyzny danych dla magazynu, zobacz [Przewodnik po zabezpieczeniach usługi Azure Storage](../storage/common/storage-security-guide.md).

### <a name="what-tools-support-using-rbac-for-data-operations"></a>Jakie narzędzia obsługują korzystanie z funkcji RBAC dla operacji na danych?

Aby wyświetlać operacje na danych i korzystać z nich, należy dysponować prawidłowymi wersjami narzędzi lub zestawów SDK:

| Tool  | Version  |
|---------|---------|
| [Azure PowerShell](/powershell/azure/install-az-ps) | 1.1.0 lub nowszy |
| [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) | 2.0.30 lub nowszy |
| [Platforma Azure dla platformy .NET](/dotnet/azure/) | 2.8.0 — wersja zapoznawcza lub nowsza |
| [Zestaw Azure SDK dla języka Go](/azure/go/azure-sdk-go-install) | 15.0.0 lub nowszy |
| [Platforma Azure dla języka Java](/java/azure/) | 1.9.0 lub nowszy |
| [Platforma Azure dla języka Python](/python/azure) | 0.40.0 lub nowszy |
| [Zestaw Azure SDK dla środowiska Ruby](https://rubygems.org/gems/azure_sdk) | 0.17.1 lub nowszy |

Aby wyświetlić operacje na danych w interfejsie API REST i korzystać z nich, należy ustawić parametr **API-Version** w następującej wersji lub nowszej:

- 2018-07-01

## <a name="actions"></a>Akcje

`Actions` Uprawnienie określa operacje zarządzania, które mogą być wykonywane przez rolę. Jest to kolekcja ciągów operacji, która identyfikuje zabezpieczone operacje dostawców zasobów platformy Azure. Poniżej przedstawiono kilka przykładów operacji zarządzania, które mogą być używane w `Actions`programie.

| Ciąg operacji    | Opis         |
| ------------------- | ------------------- |
| `*/read` | Przyznaje dostęp do operacji odczytu dla wszystkich typów zasobów wszystkich dostawców zasobów platformy Azure.|
| `Microsoft.Compute/*` | Przyznaje dostęp do wszystkich operacji dla wszystkich typów zasobów w dostawcy zasobów Microsoft. COMPUTE.|
| `Microsoft.Network/*/read` | Przyznaje dostęp do operacji odczytu dla wszystkich typów zasobów w dostawcy zasobów Microsoft. Network.|
| `Microsoft.Compute/virtualMachines/*` | Przyznaje dostęp do wszystkich operacji maszyn wirtualnych i jego podrzędnych typów zasobów.|
| `microsoft.web/sites/restart/Action` | Przyznaje dostęp do ponownego uruchomienia aplikacji sieci Web.|

## <a name="notactions"></a>NotActions

Uprawnienie określa operacje zarządzania, które są wykluczone z dozwolonej `Actions`. `NotActions` Użyj uprawnienia `NotActions` , jeśli zestaw operacji, do których chcesz zezwolić, jest łatwiejszy w użyciu, wykluczając operacje ograniczone. Dostęp udzielony przez rolę (czynne uprawnienia) jest obliczany przez odjęcie `NotActions` operacji `Actions` od operacji.

> [!NOTE]
> Jeśli użytkownik ma przypisaną rolę, która wyklucza operację w programie `NotActions`, i ma przypisaną drugą rolę, która przyznaje dostęp do tej samej operacji, użytkownik może wykonać tę operację. `NotActions`nie jest regułą odmowy — jest to po prostu wygodny sposób tworzenia zestawu dozwolonych operacji, gdy wymagane jest wykluczenie określonych operacji.
>

## <a name="dataactions"></a>DataActions

`DataActions` Uprawnienie określa operacje na danych, które mogą być wykonywane na danych w tym obiekcie. Na przykład jeśli użytkownik odczytał dostęp do danych obiektów BLOB do konta magazynu, może odczytać obiekty blob w ramach tego konta magazynu. Poniżej przedstawiono kilka przykładów operacji na danych, które mogą być używane `DataActions`w programie.

| Ciąg operacji    | Opis         |
| ------------------- | ------------------- |
| `Microsoft.Storage/storageAccounts/ blobServices/containers/blobs/read` | Zwraca obiekt BLOB lub listę obiektów BLOB. |
| `Microsoft.Storage/storageAccounts/ blobServices/containers/blobs/write` | Zwraca wynik zapisania obiektu BLOB. |
| `Microsoft.Storage/storageAccounts/ queueServices/queues/messages/read` | Zwraca komunikat. |
| `Microsoft.Storage/storageAccounts/ queueServices/queues/messages/*` | Zwraca komunikat lub wynik zapisywania lub usuwania wiadomości. |

## <a name="notdataactions"></a>NotDataActions

Uprawnienie określa operacje na danych, które są wykluczone z dozwolonej `DataActions`. `NotDataActions` Dostęp udzielony przez rolę (czynne uprawnienia) jest obliczany przez odjęcie `NotDataActions` operacji `DataActions` od operacji. Każdy dostawca zasobów udostępnia swój odpowiedni zestaw interfejsów API do realizacji operacji na danych.

> [!NOTE]
> Jeśli użytkownik ma przypisaną rolę, która wyklucza operację danych w programie `NotDataActions`, i przypisuje drugą rolę, która przyznaje dostęp do tej samej operacji danych, użytkownik może wykonać tę operację. `NotDataActions`nie jest regułą odmowy — jest to po prostu wygodny sposób tworzenia zestawu dozwolonych operacji na danych, gdy wymagane jest wykluczenie określonych operacji na danych.
>

## <a name="assignablescopes"></a>AssignableScopes

`AssignableScopes` Właściwość określa zakresy (subskrypcje, grupy zasobów lub zasoby), dla których ta definicja roli jest dostępna. Rolę można przypisywać tylko w ramach subskrypcji lub grup zasobów, które ich wymagają, i nie należy w pełni korzystać ze środowiska użytkownika w przypadku pozostałej części subskrypcji lub grup zasobów. Należy użyć co najmniej jednej subskrypcji, grupy zasobów lub identyfikatora zasobu.

Wbudowane role mają `AssignableScopes` ustawiony zakres główny (`"/"`). Zakres główny wskazuje, że rola jest dostępna do przypisania we wszystkich zakresach. Przykłady prawidłowych zakresów do przypisania to:

| Scenariusz | Przykład |
|----------|---------|
| Rola jest dostępna do przypisania w ramach jednej subskrypcji | `"/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"` |
| Rola jest dostępna do przypisania w dwóch subskrypcjach | `"/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e", "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"` |
| Rola jest dostępna do przypisania tylko w sieciowej grupie zasobów | `"/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network"` |
| Rola jest dostępna do przypisywania we wszystkich zakresach (dotyczy tylko ról wbudowanych) | `"/"` |

Informacje o `AssignableScopes` rolach niestandardowych można znaleźć w temacie [role niestandardowe dla zasobów platformy Azure](custom-roles.md).

## <a name="next-steps"></a>Następne kroki

* [Wbudowane role dla zasobów platformy Azure](built-in-roles.md)
* [Niestandardowe role dla zasobów platformy Azure](custom-roles.md)
* [Operacje dostawcy zasobów Azure Resource Manager](resource-provider-operations.md)
