---
title: Zrozumienie definicji ról w funkcji RBAC dla zasobów platformy Azure | Dokumentacja firmy Microsoft
description: Informacje na temat definicji ról kontroli dostępu opartej na rolach (RBAC) dla dostępu do zarządzania zasobami platformy Azure.
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
ms.date: 02/09/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: ''
ms.openlocfilehash: 7855c2bd45ba35ecb0ede5c60268e6446f37ed5a
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "58804534"
---
# <a name="understand-role-definitions-for-azure-resources"></a>Zrozumienie definicje ról na potrzeby zasobów platformy Azure

Jeśli chcesz zrozumieć, jak działa rola lub jeśli tworzysz własny [roli niestandardowej na potrzeby zasobów platformy Azure](custom-roles.md), warto zrozumieć sposób definiowania ról. W tym artykule opisano szczegóły definicje ról oraz przedstawiono kilka przykładów.

## <a name="role-definition-structure"></a>Struktura definicji roli

*Definicja roli* to zbiór uprawnień. Czasami jest nazywana po prostu *rolą*. Definicja roli określa dozwolone operacje, na przykład odczyt, zapis, czy usuwanie. Można również podać, operacje, które nie mogą być wykonywane lub operacje związane z danych źródłowych. Definicja roli ma następującą strukturę:

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

Operacje są określane za pomocą ciągów, które mają następujący format:

- `{Company}.{ProviderName}/{resourceType}/{action}`

`{action}` Część ciągu operacji określa rodzaj operacji można wykonywać na typ zasobu. Na przykład, zostanie wyświetlony następujący podciągów w `{action}`:

| Podciąg akcji    | Opis         |
| ------------------- | ------------------- |
| `*` | Symbol wieloznaczny udziela dostępu do wszystkich operacji, które pasuje do ciągu. |
| `read` | Włącza odczytu (GET). |
| `write` | Umożliwia pisanie operacji (PUT, POST i PATCH). |
| `delete` | Umożliwia usuwanie operacji (Usuń). |

Oto [Współautor](built-in-roles.md#contributor) definicji roli w formacie JSON. Symbol wieloznaczny (`*`) działanie `Actions` oznacza, że jednostka przypisani do tej roli mogą wykonywać wszystkie akcje lub innymi słowy, jego może zarządzać wszystkim. Obejmuje to akcje zdefiniowane w przyszłości, jak platforma Azure dodaje nowe typy zasobów. Operacje w obszarze `NotActions` są odejmowane od `Actions`. W przypadku właściwości [Współautor](built-in-roles.md#contributor) roli `NotActions` usuwa ta rola umożliwia zarządzanie dostępem do zasobów, a także przypisać dostęp do zasobów.

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

## <a name="management-and-data-operations-preview"></a>Operacje zarządzania i dane (wersja zapoznawcza)

Kontrola dostępu oparta na rolach dla operacji zarządzania została określona w `Actions` i `NotActions` właściwości definicji roli. Poniżej przedstawiono kilka przykładów operacje zarządzania na platformie Azure:

- Zarządzanie dostępem do konta magazynu
- Tworzenie, aktualizowanie lub usuwanie kontenera obiektów blob
- Usuń grupę zasobów i wszystkie jej zasoby

Dostęp do funkcji zarządzania nie jest dziedziczony z danymi. Ta separacja zapobiega ról z symbolami wieloznacznymi (`*`) z konieczności nieograniczony dostęp do danych. Na przykład, jeśli użytkownik ma [czytnika](built-in-roles.md#reader) roli w ramach subskrypcji, następnie mogą oni wyświetlać konta magazynu, ale domyślnie nie można wyświetlić danych bazowych.

Wcześniej kontrola dostępu oparta na rolach nie był używany dla operacji na danych. Autoryzacja dla operacji na danych zróżnicowane dla dostawców zasobów. Modelu autoryzacji tych samych kontroli dostępu opartej na rolach używany do wykonywania operacji zarządzania została rozszerzona na operacje na danych (obecnie w wersji zapoznawczej).

Aby obsługiwać operacje na danych, struktura definicji roli dodano nowe właściwości danych. Operacje na danych są określone w `DataActions` i `NotDataActions` właściwości. Dodanie właściwości tych danych, rozdzielenia danych i zarządzania jest zachowywana. Zapobiega to bieżące przypisania roli z symbolami wieloznacznymi (`*`) nagle uzyskanie dostępu do danych. Poniżej przedstawiono niektóre operacje na danych, które mogą być określone w `DataActions` i `NotDataActions`:

- Odczytaj listę obiektów blob w kontenerze
- Zapisywanie magazynu obiektów blob w kontenerze
- Usuń komunikat z kolejki

Oto [czytnik danych obiektu Blob magazynu](built-in-roles.md#storage-blob-data-reader) definicji roli, który obejmuje operacje w obu `Actions` i `DataActions` właściwości. Ta rola umożliwia odczytywanie kontenera obiektów blob, a także podstawowych danych obiektów blob.

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

Tylko operacje na danych mogą być dodawane do `DataActions` i `NotDataActions` właściwości. Dostawcy zasobów Zidentyfikuj, jakie operacje są operacje na danych, ustawiając `isDataAction` właściwość `true`. Aby wyświetlić listę operacji, gdzie `isDataAction` jest `true`, zobacz [operacji dostawcy zasobów](resource-provider-operations.md). Role, które nie mają operacje na danych nie musi mieć `DataActions` i `NotDataActions` właściwości w ramach definicji roli.

Autoryzacji dla wszystkich wywołań interfejsu API operacji zarządzania odbywa się przez usługę Azure Resource Manager. Autoryzacji dla wywołań interfejsu API operacji danych odbywa się przez dostawcę zasobów lub usługi Azure Resource Manager.

### <a name="data-operations-example"></a>Przykład operacje na danych

Aby lepiej zrozumieć, jak działają operacje zarządzania i dane, rozważmy konkretnemu przykładowi. Przypisano Alicja [właściciela](built-in-roles.md#owner) rolę w zakresie subskrypcji. Przypisano Bob [Współautor danych obiektu Blob magazynu](built-in-roles.md#storage-blob-data-contributor) rolę w zakresie konta magazynu. Na poniższym diagramie przedstawiono w tym przykładzie.

![Kontrola dostępu oparta na rolach został rozszerzony do obsługi zarządzania i operacje na danych](./media/role-definitions/rbac-management-data.png)

[Właściciela](built-in-roles.md#owner) roli dla Alicji i [Współautor danych obiektu Blob magazynu](built-in-roles.md#storage-blob-data-contributor) rola dla Roberta ma następujące akcje:

Właściciel

&nbsp;&nbsp;&nbsp;&nbsp;Akcje<br>
&nbsp;&nbsp;&nbsp;&nbsp;`*`

Współautor danych obiektu blob usługi Storage

&nbsp;&nbsp;&nbsp;&nbsp;Akcje<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/delete`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/read`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/write`<br>
&nbsp;&nbsp;&nbsp;&nbsp;Elementy DataActions<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write`

Ponieważ Alicja zawiera symbol wieloznaczny (`*`) działania w zakresie subskrypcji, jej uprawnienia dziedziczyć dół umożliwiające jej wykonywać wszystkie akcje zarządzania. Alicja może odczytu, zapisu i usuwania kontenerów. Jednak Alicja nie mogą wykonywać operacje na danych bez wykonywania dodatkowych czynności. Na przykład domyślnie Alicja nie może odczytać obiektów blob w kontenerze. Do odczytywania obiektów blob, Alicja musi pobrać klucze dostępu do magazynu i używać ich do uzyskania dostępu do obiektów blob.

Uprawnienia przez Boba są ograniczone do właśnie `Actions` i `DataActions` określonych w [Współautor danych obiektu Blob magazynu](built-in-roles.md#storage-blob-data-contributor) roli. W zależności od roli Bob można wykonywać operacje na danych i zarządzania. Na przykład Bob może odczytu, zapisu i usuwania kontenerów na koncie magazynu określonym i użytkownik może również odczytu, zapisu i usuwania obiektów blob.

Aby uzyskać więcej informacji na temat zarządzania i bezpieczeństwo płaszczyzny danych dla magazynu, zobacz [Przewodnik po zabezpieczeniach usługi Azure Storage](../storage/common/storage-security-guide.md).

### <a name="what-tools-support-using-rbac-for-data-operations"></a>Jakie narzędzia obsługują, przy użyciu funkcji RBAC dla operacji na danych?

Aby przeglądać i pracować z operacji na danych, konieczne jest posiadanie poprawne wersje narzędzia i zestawy SDK:

| Tool  | Wersja  |
|---------|---------|
| [Azure PowerShell](/powershell/azure/install-az-ps) | 1.1.0 lub nowszy |
| [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) | 2.0.30 lub nowszej |
| [Azure for .NET](/dotnet/azure/) | 2.8.0-Preview lub nowszej |
| [Zestaw Azure SDK dla języka Go](/go/azure/azure-sdk-go-install) | 15.0.0 lub nowszy |
| [Platforma Azure dla języka Java](/java/azure/) | 1.9.0 lub nowszy |
| [Platforma Azure dla języka Python](/python/azure) | 0.40.0 lub nowszy |
| [Zestaw Azure SDK dla środowiska Ruby](https://rubygems.org/gems/azure_sdk) | 0.17.1 lub nowszy |

Wyświetlanie i używanie operacji na danych w interfejsie API REST, należy ustawić **parametru api-version** parametru następującą wersję lub nowszej wersji:

- 2018-01-01-preview

Witryna Azure portal umożliwia także użytkownikom przeglądanie i zarządzanie nimi zawartość, kolejek i obiektów Blob kontenerów za pomocą usługi Azure AD w wersji zapoznawczej środowiska. Aby wyświetlić i zarządzania zawartością kolejki lub obiektu Blob kliknij kontener **Eksplorowanie danych za pomocą usługi Azure AD w wersji zapoznawczej** na koncie magazynu — omówienie.

![Zapoznaj się z kolejek i obiektów Blob kontenerów za pomocą usługi Azure AD (wersja zapoznawcza)](./media/role-definitions/rbac-dataactions-browsing.png)

## <a name="actions"></a>Akcje

`Actions` Uprawnień określa operacje zarządzania, dozwolone przez rolę do wykonania. Jest to kolekcja operacji ciągów, które identyfikują zabezpieczanych operacje dostawców zasobów platformy Azure. Poniżej przedstawiono kilka przykładów operacji zarządzania, które mogą być używane w `Actions`.

| Parametry operacji    | Opis         |
| ------------------- | ------------------- |
| `*/read` | Przyznaje dostęp do odczytu dla wszystkich typów zasobów dla wszystkich dostawców zasobów platformy Azure.|
| `Microsoft.Compute/*` | Zapewnia dostęp do wszystkich operacji dotyczących wszystkich typów zasobów dostawcy zasobów Microsoft.Compute.|
| `Microsoft.Network/*/read` | Przyznaje dostęp do odczytu dla wszystkich typów zasobów dostawcy zasobów Microsoft.Network.|
| `Microsoft.Compute/virtualMachines/*` | Przyznaje dostęp do wszystkich operacji maszyn wirtualnych i jego podrzędnych typów zasobów.|
| `microsoft.web/sites/restart/Action` | Przyznaje dostęp do ponownego uruchomienia aplikacji sieci web.|

## <a name="notactions"></a>NotActions

`NotActions` Uprawnień określa operacje zarządzania, które są wykluczone z dozwolonych `Actions`. Użyj `NotActions` uprawnienia, jeśli zestaw operacji, które chcesz zezwolić na łatwiejsze jest definiowany przez ograniczone operacje z wyjątkiem. Dostęp udzielany przez rolę (czynne uprawnienia) jest obliczana przez odjęcie ilości `NotActions` operacje z `Actions` operacji.

> [!NOTE]
> Jeśli użytkownik ma przypisaną rolę, bez operacji w `NotActions`i ma przypisany druga rola, która udziela dostępu do tej samej operacji, użytkownik może wykonać tej operacji. `NotActions` nie jest odmowy reguły — jest po prostu wygodny sposób utworzyć zestaw dozwolonych operacji podczas określonych operacji, które powinny zostać wykluczone.
>

## <a name="dataactions-preview"></a>elementy dataActions (wersja zapoznawcza)

`DataActions` Uprawnień określa operacje danych, dozwolone przez rolę do wykonania na danych w obrębie tego obiektu. Na przykład jeśli użytkownik ma dostęp do danych obiektów blob na koncie magazynu do odczytu, następnie będzie mogła ją odczytać obiekty BLOB w ramach tego konta magazynu. Poniżej przedstawiono kilka przykładów operacje na danych, które mogą być używane w `DataActions`.

| Parametry operacji    | Opis         |
| ------------------- | ------------------- |
| `Microsoft.Storage/storageAccounts/ blobServices/containers/blobs/read` | Zwraca obiekt blob lub listę obiektów blob. |
| `Microsoft.Storage/storageAccounts/ blobServices/containers/blobs/write` | Zwraca wynik zapisania obiektu blob. |
| `Microsoft.Storage/storageAccounts/ queueServices/queues/messages/read` | Zwraca komunikat. |
| `Microsoft.Storage/storageAccounts/ queueServices/queues/messages/*` | Zwraca komunikat lub wynik zapisu i usuwania komunikatu. |

## <a name="notdataactions-preview"></a>notDataActions (wersja zapoznawcza)

`NotDataActions` Uprawnień określa operacje danych, które są wykluczone z dozwolonych `DataActions`. Dostęp udzielany przez rolę (czynne uprawnienia) jest obliczana przez odjęcie ilości `NotDataActions` operacje z `DataActions` operacji. Każdy dostawca zasobów udostępnia jego odpowiedniego zestawu interfejsów API, aby wykonać operacje na danych.

> [!NOTE]
> Jeśli użytkownik ma przypisaną rolę, który wyklucza operacja na danych w `NotDataActions`i ma przypisany druga rola, która udziela dostępu do tej samej operacji danych, użytkownik może wykonać tej operacji danych. `NotDataActions` nie jest odmowy reguły — jest po prostu wygodny sposób utworzyć zestaw danych dozwolonych operacji podczas określonymi operacjami, które powinny zostać wykluczone.
>

## <a name="assignablescopes"></a>AssignableScopes

`AssignableScopes` Właściwość określa zakresy (subskrypcji, grupy zasobów lub zasobów), że rola jest dostępne do przypisania. Możesz udostępnić rolę do przypisania tylko do subskrypcji lub grupy zasobów, które wymagają, a nie użytkownika zaśmiecania środowisko dla pozostałych grup zasobów lub subskrypcji. Należy użyć co najmniej jednej subskrypcji, grupy zasobów lub identyfikator zasobu.

Role wbudowane mają `AssignableScopes` Ustaw zakres głównego (`"/"`). Zakres głównego wskazuje, że rola jest dostępne do przypisania we wszystkich zakresach. Przykłady prawidłowe zakresy możliwe do przypisania:

| Scenariusz | Przykład |
|----------|---------|
| Rola jest dostępne do przypisania w ramach jednej subskrypcji | `"/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"` |
| Rola jest dostępne do przypisania w dwóch subskrypcji | `"/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e", "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"` |
| Rola jest dostępne do przypisania tylko w grupie zasobów sieciowych | `"/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network"` |
| Rola jest dostępne do przypisania we wszystkich zakresach | `"/"` |

Aby uzyskać informacje o `AssignableScopes` dla ról niestandardowych, zobacz [niestandardowych ról dla zasobów platformy Azure](custom-roles.md).

## <a name="next-steps"></a>Kolejne kroki

* [Wbudowane role dla zasobów platformy Azure](built-in-roles.md)
* [Niestandardowe role dla zasobów platformy Azure](custom-roles.md)
* [Operacje dostawcy zasobów w usłudze Azure Resource Manager](resource-provider-operations.md)
