---
title: Zasoby w usłudze Media Services — Azure | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera opis zasoby są i jak są one używane przez usługi Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 01/01/2018
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 8507d51f0d4d49d89fc24b38ed73df7488261daa
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/02/2019
ms.locfileid: "53969579"
---
# <a name="assets"></a>Elementy zawartości

W usłudze Azure Media Services [zasobów](https://docs.microsoft.com/rest/api/media/assets) zawiera pliki cyfrowe (w tym wideo, audio, obrazy, kolekcje miniatur, ścieżki tekstowe i pliki napisów) oraz metadane dotyczące tych plików. Przekazane pliki cyfrowe do elementu zawartości używać w usłudze Media Services, kodowanie, przesyłanie strumieniowe, analizowanie zawartości przepływów pracy. Aby uzyskać więcej informacji, zobacz [przekazać pliki cyfrowe do zasobów](#upload-digital-files-into-assets) poniższej sekcji.

Element zawartości jest mapowany na kontener obiektów blob w [konta usługi Azure Storage](storage-account-concept.md) i pliki w elemencie zawartości są przechowywane jako blokowe obiekty BLOB w kontenerze. Usługa Media Services obsługuje warstwy obiektu Blob, gdy konto korzysta z ogólnego przeznaczenia w wersji 2 (GPv2) magazynu. Konta GPv2, umożliwia przeniesienie plików [chłodna lub archiwum](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers). **Archiwum** magazynu nadaje się do archiwizacji plików źródłowych, gdy nie będą już potrzebne (na przykład po został zakodowany).

**Archiwum** warstwy magazynowania jest zalecane tylko dla plików źródłowych bardzo duże, które już zaszyfrowana i kodowania dane wyjściowe zadania został umieszczony w kontenerze obiektów blob danych wyjściowych. Obiekty BLOB w kontenerze danych wyjściowych, który chcesz skojarzyć z zasobu i użycia, przesłać strumieniowo lub analizować zawartość, musi istnieć w **gorąca** lub **chłodna** warstwy magazynowania.

## <a name="asset-definition"></a>Definicja zasobu

W poniższej tabeli przedstawiono właściwości zasobów oraz zapewnia ich definicje.

|Name (Nazwa)|Opis|
|---|---|
|id|W pełni kwalifikowanego Identyfikatora zasobu dla zasobu.|
|name|Nazwa zasobu.|
|properties.alternateId |Alternatywny identyfikator elementu zawartości.|
|properties.assetId |Identyfikator zasobu.|
|Properties.container |Nazwa kontenera obiektów blob zasobów.|
|Properties.created |Data utworzenia zasobu.<br/> Data i godzina jest zawsze w formacie UTC.|
|Properties.Description|Opis elementu zawartości.|
|properties.lastModified |Ostatniej modyfikacji zasobu. <br/> Data i godzina jest zawsze w formacie UTC.|
|properties.storageAccountName |Nazwa konta magazynu.|
|properties.storageEncryptionFormat |Format szyfrowania elementu zawartości. Jeden z Brak lub MediaStorageEncryption.|
|type|Typ zasobu.|

Aby uzyskać pełną definicję, zobacz [zasoby](https://docs.microsoft.com/rest/api/media/assets).

## <a name="upload-digital-files-into-assets"></a>Przekazać pliki cyfrowe do zasobów

Jednym z typowych przepływów pracy usługi Media Services jest przekazywanie, kodowanie i przesyłanie strumieniowe plików. W tej sekcji opisano ogólne kroki.

1. Użyj interfejsu API usługi Media Services v3, aby utworzyć nowy zasób "danych wejściowych". Ta operacja tworzy kontener w ramach konta magazynu skojarzone z kontem usługi Media Services. Interfejs API zwraca nazwę kontenera (na przykład `"container": "asset-b8d8b68a-2d7f-4d8c-81bb-8c7bbbe67ee4"`).
   
    Jeśli masz już kontener obiektów blob, który chcesz skojarzyć z zasobem, należy określić nazwę kontenera, podczas tworzenia elementu zawartości. Usługa Media Services aktualnie obsługuje tylko obiekty BLOB w katalogu głównym kontenera, a nie przy użyciu ścieżki w nazwie pliku. W związku z tym kontener o nazwie "input.mp4" będą działać. Kontener o nazwie "videos/inputs/input.mp4", nie będzie działać.

    Za pomocą wiersza polecenia platformy Azure można przekazać bezpośrednio do dowolnego konta magazynu i kontener, w którym masz uprawnienia do subskrypcji. <br/>Nazwa kontenera musi być unikatowa i postępuj zgodnie z magazynu wskazówki dotyczące nazewnictwa. Nazwa nie musi postępuj zgodnie z elementu zawartości multimediów usługi nazwa kontenera (identyfikator GUID zasobu), formatowanie. 
    
    ```azurecli
    az storage blob upload -f /path/to/file -c MyContainer -n MyBlob
    ```
2. Pobierz adres URL sygnatury dostępu Współdzielonego z uprawnieniami odczytu / zapisu, używane można przekazać pliki cyfrowe do kontenera zasobów. Można użyć interfejsu API Media Services do [listy adresów URL kontenera zasobów](https://docs.microsoft.com/rest/api/media/assets/listcontainersas).
3. Za pomocą interfejsów API usługi Azure Storage lub zestawów SDK (na przykład [interfejsu API REST magazynu](../../storage/common/storage-rest-api-auth.md), [zestawu JAVA SDK](../../storage/blobs/storage-quickstart-blobs-java-v10.md), lub [zestawu .NET SDK](../../storage/blobs/storage-quickstart-blobs-dotnet.md)) przekazywanie plików do kontenera zasobów. 
4. Użyj interfejsów API w wersji 3 usługa Media Services, aby utworzyć przekształcenie i zadania przetwarzania "danych wejściowych" element zawartości. Aby uzyskać więcej informacji, zobacz [transformacje i zadania](transform-concept.md).
5. Stream zawartość z zasobu "Wyjście".

> [!TIP]
> Aby uzyskać pełny przykład .NET, który pokazuje, jak: tworzenia zasobu, Pobierz adres URL zapisu sygnatury dostępu Współdzielonego do zasobu kontenera w magazynie, przekazać plik do kontenera w magazynie przy użyciu adresu URL sygnatury dostępu Współdzielonego, zobacz [utworzyć dane wejściowe zadania z pliku lokalnego](job-input-from-local-file-how-to.md).

### <a name="create-a-new-asset"></a>Utwórz nowy zasób

#### <a name="rest"></a>REST

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{amsAccountName}/assets/{assetName}?api-version=2018-07-01
```

Na przykład REST, zobacz [utworzenie elementu zawartości z użyciem usług REST](https://docs.microsoft.com/rest/api/media/assets/createorupdate#examples) przykład.

W przykładzie pokazano sposób tworzenia **treść żądania** której można określić przydatne informacje, takie jak opis, nazwa kontenera, konto magazynu oraz inne informacje.

#### <a name="curl"></a>cURL

```cURL
curl -X PUT \
  'https://management.azure.com/subscriptions/00000000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Media/mediaServices/amsAccountName/assets/myOutputAsset?api-version=2018-07-01' \
  -H 'Accept: application/json' \
  -H 'Content-Type: application/json' \
  -d '{
  "properties": {
    "description": "",
  }
}'
```

#### <a name="net"></a>.NET

```csharp
 Asset asset = await client.Assets.CreateOrUpdateAsync(resourceGroupName, accountName, assetName, new Asset());
```

Aby uzyskać pełny przykład, zobacz [utworzyć dane wejściowe zadania z pliku lokalnego](job-input-from-local-file-how-to.md). W wersji 3 usługa Media Services, dane wejściowe zadania można również utworzyć z adresów URL protokołu HTTPS (zobacz [tworzenie dane wejściowe zadania na podstawie adresu URL HTTPS](job-input-from-http-how-to.md)).

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
|name|Obsługuje: Eq, Gt, Lt|Obsługuje: Rosnącej na malejącą lub odwrotnie|
|properties.alternateId |Obsługuje: EQ||
|properties.assetId |Obsługuje: EQ||
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

Jeśli zasoby są tworzone lub usuwane podczas stronicować kolekcji, zmiany są odzwierciedlane w zwróconych wyników (Jeśli te zmiany w części w kolekcji, która nie została pobrana). 

#### <a name="c-example"></a>Przykład w języku C#

W poniższym przykładzie C# przedstawiono sposób wyliczyć wszystkie zasoby w ramach konta.

```csharp
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.Assets.ListNextAsync(currentPage.NextPageLink);
}
```

#### <a name="rest-example"></a>Przykład REST

Rozważmy następujący przykład, dla których jest używana funkcja $skiptoken. Upewnij się, Zastąp *amstestaccount* nazwą swojego konta i zestaw *parametru api-version* wartość do najnowszej wersji.

Jeśli żądanie Lista zasobów w następujący sposób:

```
GET  https://management.azure.com/subscriptions/00000000-3761-485c-81bb-c50b291ce214/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01 HTTP/1.1
x-ms-client-request-id: dd57fe5d-f3be-4724-8553-4ceb1dbe5aab
Content-Type: application/json; charset=utf-8
```

Możesz zwróci odpowiedź podobny do następującego:

```
HTTP/1.1 200 OK
 
{
"value":[
{
"name":"Asset 0","id":"/subscriptions/00000000-3761-485c-81bb-c50b291ce214/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/amstestaccount/assets/Asset 0","type":"Microsoft.Media/mediaservices/assets","properties":{
"assetId":"00000000-5a4f-470a-9d81-6037d7c23eff","created":"2018-12-11T22:12:44.98Z","lastModified":"2018-12-11T22:15:48.003Z","container":"asset-98d07299-5a4f-470a-9d81-6037d7c23eff","storageAccountName":"amsdevc1stoaccount11","storageEncryptionFormat":"None"
}
},
// lots more assets
{
"name":"Asset 517","id":"/subscriptions/00000000-3761-485c-81bb-c50b291ce214/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/amstestaccount/assets/Asset 517","type":"Microsoft.Media/mediaservices/assets","properties":{
"assetId":"00000000-912e-447b-a1ed-0f723913b20d","created":"2018-12-11T22:14:08.473Z","lastModified":"2018-12-11T22:19:29.657Z","container":"asset-fd05a503-912e-447b-a1ed-0f723913b20d","storageAccountName":"amsdevc1stoaccount11","storageEncryptionFormat":"None"
}
}
],"@odata.nextLink":"https:// management.azure.com/subscriptions/00000000-3761-485c-81bb-c50b291ce214/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$skiptoken=Asset+517"
}
```

Następnej strony będzie następnie zażądać, wysyłając żądanie get:

```
https://management.azure.com/subscriptions/00000000-3761-485c-81bb-c50b291ce214/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$skiptoken=Asset+517
```

Aby uzyskać więcej przykładów REST, zobacz [zasobów — lista](https://docs.microsoft.com/rest/api/media/assets/list)

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

[Różnice między Media Services v2 i v3](migrate-from-v2-to-v3.md)
