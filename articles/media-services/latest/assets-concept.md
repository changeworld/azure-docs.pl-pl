---
title: Zasoby w usłudze Azure Media Services | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera opis zasoby są i jak są one używane przez usługi Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 10/24/2018
ms.author: juliako
ms.openlocfilehash: e7abdb568b11870fb467ee6d3759881ca337d3cc
ms.sourcegitcommit: 5de9de61a6ba33236caabb7d61bee69d57799142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/25/2018
ms.locfileid: "50085815"
---
# <a name="assets"></a>Elementy zawartości

**Zasobów** zawiera pliki cyfrowe (w tym wideo, audio, obrazy, kolekcje miniatur, ścieżki tekstowe i pliki napisów) oraz metadane dotyczące tych plików. Przekazane pliki cyfrowe do elementu zawartości mogą one używane w usłudze Media Services, kodowanie i przesyłanie strumieniowe przepływów pracy.

Element zawartości jest mapowany na kontener obiektów blob w [konta usługi Azure Storage](storage-account-concept.md) i pliki w elemencie zawartości są przechowywane jako blokowe obiekty BLOB w kontenerze. Możesz porozmawiać z plików zasobów w kontenerach przy użyciu klientów zestawu SDK usługi Storage.

Usługa Azure Media Services obsługuje warstwy obiektu Blob, gdy konto korzysta z ogólnego przeznaczenia w wersji 2 (GPv2) magazynu. Konta GPv2 umożliwia przeniesienie plików do chłodnego lub zimnego magazynu. Zimnego magazynu nadaje się do archiwizacji plików źródłowych, gdy nie będą już potrzebne (na przykład po ich ma został zakodowany.)

W wersji 3 usługa Media Services dane wejściowe zadania mogą być tworzone z zasobów lub w przypadku adresów URL HTTP (s). Aby utworzyć element zawartości, który może służyć jako dane wejściowe dla zadania, zobacz [utworzyć dane wejściowe zadania z pliku lokalnego](job-input-from-local-file-how-to.md).

Ponadto, przeczytaj temat [kont magazynu w usłudze Media Services](storage-account-concept.md) i [transformacje i zadania](transform-concept.md).

## <a name="asset-definition"></a>Definicja zasobu

W poniższej tabeli przedstawiono właściwości zasobów oraz zapewnia ich definicje.

|Name (Nazwa)|Opis|
|---|---|
|id|W pełni kwalifikowanego Identyfikatora zasobu dla zasobu.|
|name|Nazwa zasobu.|
|properties.alternateId |Alternatywny identyfikator elementu zawartości.|
|properties.assetId |Identyfikator zasobu.|
|Properties.container |Nazwa kontenera obiektów blob zasobów.|
|Properties.created |Data utworzenia zasobu.|
|Properties.Description|Opis elementu zawartości.|
|properties.lastModified |Ostatniej modyfikacji zasobu.|
|properties.storageAccountName |Nazwa konta magazynu.|
|properties.storageEncryptionFormat |Format szyfrowania elementu zawartości. Jeden z Brak lub MediaStorageEncryption.|
|type|Typ zasobu.|

Pełna definicja można zobaczyć [zasoby](https://docs.microsoft.com/rest/api/media/assets).

## <a name="filtering-ordering-paging"></a>Filtrowania, sortowania, stronicowania

Usługa Media Services obsługuje następujące opcje zapytania OData dla zasobów: 

* $filter 
* $orderby 
* $top 
* $skiptoken 

Opis operatora:

* EQ = równa
* Ne = nie jest równa
* GE = większa niż lub równe
* Le = mniejsze niż lub równe
* Gt = większa niż
* Lt = mniej niż

### <a name="filteringordering"></a>Filtrowanie porządkowanie

W poniższej tabeli przedstawiono, jak te opcje można stosować do właściwości zasobów: 

|Name (Nazwa)|Filtr|Zamówienie|
|---|---|---|
|id|||
|name|Obsługuje: Eq, Gt, Lt|Obsługuje: rosnącej na malejącą lub odwrotnie|
|properties.alternateId |Obsługuje: Eq||
|properties.assetId |Obsługuje: Eq||
|Properties.container |||
|Properties.created|Obsługuje: Eq, Gt, Lt| Obsługuje: Rosnącej na malejącą lub odwrotnie|
|Properties.Description |||
|properties.lastModified |||
|properties.storageAccountName |||
|properties.storageEncryptionFormat | ||
|type|||

W poniższym przykładzie C# filtr Data utworzenia:

```csharp
var odataQuery = new ODataQuery<Asset>("properties/created lt 2018-05-11T17:39:08.387Z");
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName, odataQuery);
```

### <a name="pagination"></a>Paginacja

Podział na strony jest obsługiwana dla każdego z czterech włączone sortowania. Obecnie rozmiar strony wynosi 1000.

> [!TIP]
> Łącze do następnej zawsze należy używać wyliczania kolekcji i nie są zależne od wielkości określonej strony.

Jeśli odpowiedzi na zapytanie zawiera wiele elementów, usługa zwraca "\@odata.nextLink" właściwości do pobrania następnej strony wyników. Może to służyć do strony za pomocą cały zestaw wyników. Nie można skonfigurować rozmiaru strony. 

Jeśli zasoby są tworzone lub usuwane podczas stronicować kolekcji, zmiany są uwzględniane w zwróconych wyników (Jeśli te zmiany w części w kolekcji, która nie została pobrana.) 

W poniższym przykładzie C# przedstawiono sposób wyliczyć wszystkie zasoby w ramach konta.

```csharp
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.Assets.ListNextAsync(currentPage.NextPageLink);
}
```

POZOSTAŁE przykłady można znaleźć [zasobów — lista](https://docs.microsoft.com/rest/api/media/assets/list)

## <a name="storage-side-encryption"></a>Szyfrowanie po stronie magazynu

Aby chronić Twoje zasoby w spoczynku, zasoby mają zostać zaszyfrowane za pomocą szyfrowania po stronie magazynu. W poniższej tabeli przedstawiono, jak działa szyfrowanie po stronie magazynu w usłudze Media Services:

|Opcja szyfrowania|Opis|Media Services v2|Media Services v3|
|---|---|---|---|
|Szyfrowanie magazynu usługi Media Services|AES-256 szyfrowania kluczy zarządzanych przez usługę Media Services|Obsługiwane<sup>(1)</sup>|Nieobsługiwane<sup>(2)</sup>|
|[Szyfrowanie usługi Storage dla danych magazynowanych](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Szyfrowanie po stronie serwera, oferowane przez usługę Azure Storage, klucz zarządzany przez platformę Azure lub przez klienta|Obsługiwane|Obsługiwane|
|[Szyfrowanie po stronie klienta magazynu](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Szyfrowanie po stronie klienta, oferowane przez usługę Azure storage, klucz zarządzany przez klienta w usłudze Key Vault|Nieobsługiwane|Nieobsługiwane|

<sup>1</sup> a Media Services obsługuje obsługi zawartości, bez zabezpieczeń/bez jakiejkolwiek formy szyfrowania, to nie jest to zalecane.

<sup>2</sup> Media Services v3, szyfrowanie magazynu (szyfrowanie AES-256) jest tylko obsługiwane dla zapewnienia zgodności gdy Twoje zasoby zostały utworzone za pomocą usługi Media Services v2. Co oznacza v3 współpracuje z istniejącym magazynie zaszyfrowane zasoby, ale nie pozwoli na tworzenie nowych.

## <a name="next-steps"></a>Kolejne kroki

[Strumieniowe przesyłanie pliku](stream-files-dotnet-quickstart.md)
