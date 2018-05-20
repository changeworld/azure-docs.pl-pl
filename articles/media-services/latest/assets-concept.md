---
title: Zasoby w usłudze Azure Media Services | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera opis zasoby są i jak są używane przez usługi Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: 76ed74f2df62d478b83e109a492977ec2d580198
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2018
---
# <a name="assets"></a>Elementy zawartości

**Zasobów** zawiera (w tym wideo, audio, obrazy, kolekcje miniatur, ścieżek tekstu i pliki napisów) pliki cyfrowe i metadane dotyczące tych plików. Po pliki cyfrowe są przekazywane do elementu zawartości, mogą być używane w Media Services, kodowania i przesyłania strumieniowego przepływów pracy.

Zasób jest zamapowana do kontenera obiektów blob w [konta magazynu Azure](storage-account-concept.md) i pliki w elementach zawartości są przechowywane jako blokowych obiektów blob w tym kontenerze. Możesz użyć plików zasobów w kontenerach przy użyciu zestawu SDK usługi Magazyn klientów.

Usługa Azure Media Services obsługuje warstw obiektów Blob, gdy konto używa ogólnego przeznaczenia v2 (GPv2) magazynu. Z GPv2 można przenieść plików do cool lub chłodni. Cold storage nadaje się do archiwizacji plików źródłowych, gdy nie są już potrzebne (na przykład po ich ma został zakodowany.)

W wersji 3 usługi Media Services dane wejściowe zadania mogą być tworzone z zasobów lub adresy URL HTTP (s). Aby utworzyć zasób, który może służyć jako dane wejściowe dla zadania, zobacz [utworzyć wprowadzania zadania z pliku lokalnego](job-input-from-local-file-how-to.md).

Ponadto należy przeczytać o [kont magazynu w usłudze Media Services](storage-account-concept.md) i [transformacji i zadania](transform-concept.md).

## <a name="asset-definition"></a>Definicja zasobów

W poniższej tabeli przedstawiono właściwości zasobów i umożliwia ich definicje.

|Name (Nazwa)|Typ|Opis|
|---|---|---|
|Identyfikator|ciąg|Identyfikator FQDN zasobu dla zasobu.|
|name|ciąg|Nazwa zasobu.|
|properties.alternateId |ciąg|Alternatywny identyfikator zasobu.|
|properties.assetId |ciąg|Identyfikator zasobu.|
|Properties.container |ciąg|Nazwa kontenera obiektów blob zasobów.|
|Properties.created |ciąg|Data utworzenia zasobu.|
|Properties.Description |ciąg|Opis zasobów.|
|properties.lastModified |ciąg|Data trwałego jego ostatniej modyfikacji.|
|properties.storageAccountName |ciąg|Nazwa konta magazynu.|
|properties.storageEncryptionFormat |AssetStorageEncryptionFormat |Format szyfrowania zasobów. Jeden z Brak lub MediaStorageEncryption.|
|type|ciąg|Typ zasobu.|

Dla pełnej definicji [zasoby](https://docs.microsoft.com/rest/api/media/assets).

## <a name="filtering-ordering-and-paging-support"></a>Filtrowanie, kolejność i Obsługa stronicowania

Usługa Media Services obsługuje następujące opcje zapytania OData dla zasobów: 

* $filter 
* $orderby 
* $top 
* $skiptoken 

### <a name="filteringordering"></a>Filtrowanie kolejności

W poniższej tabeli przedstawiono, jak te opcje można stosować do właściwości zasobów: 

|Name (Nazwa)|Filtr|Kolejność|
|---|---|---|
|Identyfikator|Obsługuje:<br/>Równa się<br/>Więcej niż<br/>Mniej niż|Obsługuje:<br/>Rosnąco<br/>Malejąco|
|name|||
|properties.alternateId |Obsługuje:<br/>Równa się||
|properties.assetId |Obsługuje:<br/>Równa się||
|Properties.container |||
|Properties.created|Obsługuje:<br/>Równa się<br/>Więcej niż<br/>Mniej niż|Obsługuje:<br/>Rosnąco<br/>Malejąco|
|Properties.Description |||
|properties.lastModified |||
|properties.storageAccountName |||
|properties.storageEncryptionFormat | ||
|type|||

W poniższym przykładzie C# filtry Data utworzenia:

```csharp
var odataQuery = new ODataQuery<Asset>("properties/created lt 2018-05-11T17:39:08.387Z");
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName, odataQuery);
```

### <a name="pagination"></a>Paginacja

Podział na strony jest obsługiwana dla każdego z czterech włączone sortowania. 

Jeśli odpowiedź na zapytanie zawiera wiele (obecnie ponad 1000) elementów, usługa zwraca "@odata.nextLink" właściwości do pobrania następnej strony wyników. Może to służyć do strony za pomocą cały zestaw wyników. Rozmiar strony nie jest konfigurowane przez użytkownika. 

Jeśli zasoby są tworzone lub usuwane podczas stronicowania za pomocą kolekcji, zmiany są uwzględniane w zwracanych wyników (Jeśli te zmiany w części w kolekcji, która nie została pobrana.) 

Poniższy przykład C# przedstawia wyliczyć wszystkie zasoby w ramach konta.

```csharp
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.Assets.ListNextAsync(currentPage.NextPageLink);
}
```

Przykłady REST, zobacz [zasoby — listy](https://docs.microsoft.com/rest/api/media/assets/list)

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Strumieniowe przesyłanie pliku](stream-files-dotnet-quickstart.md)
