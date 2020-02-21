---
title: Elementy zawartości
titleSuffix: Azure Media Services
description: Dowiedz się więcej o zasobach i sposobach ich użycia przez Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 08/29/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 3860823787b860f2504d6fb13b9479d1feec9d28
ms.sourcegitcommit: 934776a860e4944f1a0e5e24763bfe3855bc6b60
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/20/2020
ms.locfileid: "77505817"
---
# <a name="assets-in-azure-media-services"></a>Zasoby w Azure Media Services

W Azure Media Services [zasób](https://docs.microsoft.com/rest/api/media/assets) zawiera informacje o plikach cyfrowych przechowywanych w usłudze Azure Storage (w tym wideo, audio, obrazy, kolekcje miniatur, ścieżki tekstowe i pliki napisów).

Zasób jest mapowany do kontenera obiektów BLOB na [koncie usługi Azure Storage](storage-account-concept.md) , a pliki w elemencie zawartości są przechowywane jako blokowe obiekty blob w tym kontenerze. Media Services obsługuje warstwy obiektów blob, gdy konto używa magazynu ogólnego przeznaczenia w wersji 2 (GPv2). Za pomocą GPv2 można przenosić pliki do [magazynu chłodnego lub archiwum](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers). Magazyn **archiwalny** jest odpowiedni do archiwizowania plików źródłowych, gdy nie są już potrzebne (na przykład po ich zakodowaniu).

Warstwa magazynu **archiwum** jest zalecana tylko dla bardzo dużych plików źródłowych, które zostały już zakodowane i dane wyjściowe zadania kodowania zostały umieszczone w wyjściowym kontenerze obiektów BLOB. Obiekty blob w kontenerze danych wyjściowych, które mają zostać skojarzone z elementem zawartości i używają do przesyłania strumieniowego lub analizowania zawartości, muszą znajdować się w warstwie magazynowania **gorąca** lub **chłodna** .

### <a name="naming"></a>Nadawanie nazw 

#### <a name="assets"></a>Elementy zawartości

Nazwy zasobów muszą być unikatowe. Nazwy zasobów Media Services v3 (na przykład zasoby, zadania, przekształcenia) podlegają ograniczeniom nazw Azure Resource Manager. Aby uzyskać więcej informacji, zobacz [konwencje nazewnictwa](media-services-apis-overview.md#naming-conventions).

#### <a name="blobs"></a>Obiekty blob

Nazwy plików/obiektów BLOB w obrębie elementu zawartości muszą spełniać zarówno [wymagania dotyczące nazw obiektów BLOB](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata) , jak i [wymagania dotyczące nazw NTFS](https://docs.microsoft.com/windows/win32/fileio/naming-a-file). Przyczyną tych wymagań jest możliwość skopiowania plików z magazynu obiektów BLOB do lokalnego dysku NTFS w celu przetworzenia.

## <a name="upload-digital-files-into-assets"></a>Przekazywanie plików cyfrowych do zasobów

Po przekazaniu plików cyfrowych do magazynu i skojarzeniu ich z elementem zawartości można używać ich w Media Services kodowania, przesyłania strumieniowego i analizowania przepływów danych. Jednym z typowych przepływów pracy Media Services jest przekazywanie, kodowanie i przesyłanie strumieniowe pliku. Ta sekcja zawiera opis ogólnych kroków.

> [!TIP]
> Przed rozpoczęciem opracowywania, przejrzyj temat [Programowanie za pomocą interfejsów api Media Services v3](media-services-apis-overview.md) (w tym informacje na temat uzyskiwania dostępu do interfejsów API, konwencji nazewnictwa itd.).

1. Użyj interfejsu API usługi Media Services w wersji 3, aby utworzyć nowy element zawartości typu „input”. Ta operacja polega na utworzeniu kontenera na koncie magazynu skojarzonym z kontem usługi Media Services. Interfejs API zwraca nazwę kontenera (na przykład `"container": "asset-b8d8b68a-2d7f-4d8c-81bb-8c7bbbe67ee4"`).

    Jeśli masz już kontener obiektów blob, który chcesz skojarzyć z elementem zawartości, możesz określić nazwę kontenera podczas tworzenia elementu zawartości. Usługa Media Services aktualnie obsługuje tylko obiekty blob w katalogu głównym kontenera, a nie w obrębie ścieżek w nazwie pliku. W związku z tym kontener z nazwą pliku „input.mp4” będzie działać. Jednak kontener z nazwą pliku "wideo/dane wejściowe/Input. mp4" nie będzie działał.

    Za pomocą wiersza polecenia platformy Azure można przekazywać pliki bezpośrednio do dowolnego konta magazynu i kontenera, do których masz uprawnienia w subskrypcji.

    Nazwa kontenera musi być unikatowa i zgodna ze wskazówkami dotyczącymi nazewnictwa magazynu. Nazwa nie musi być zgodna z formatowaniem nazwy kontenera elementów zawartości usługi Media Services (Asset-GUID).

    ```azurecli
    az storage blob upload -f /path/to/file -c MyContainer -n MyBlob
    ```
2. Pobierz adres URL sygnatury dostępu współdzielonego z uprawnieniami odczytu/zapisu, który będzie używany do przekazywania plików cyfrowych do kontenera elementów zawartości. W celu [utworzenia listy adresów URL kontenerów elementów zawartości](https://docs.microsoft.com/rest/api/media/assets/listcontainersas) można użyć interfejsu API usługi Media Services.
3. Użyj interfejsów API usługi Azure Storage lub zestawów SDK (na przykład [interfejsu API REST magazynu](../../storage/common/storage-rest-api-auth.md) lub [zestawu SDK platformy .NET](../../storage/blobs/storage-quickstart-blobs-dotnet.md)) do przekazywania plików do kontenera zasobów.
4. W celu utworzenia przekształcenia i zadania przetwarzającego element zawartości „input” należy użyć interfejsów API usługi Media Services w wersji 3. Aby uzyskać więcej informacji, zobacz [Przekształcenia i zadania](transform-concept.md).
5. Przesyłaj strumieniowo zawartość z zasobu "output".

Pełny przykład platformy .NET, który pokazuje, jak utworzyć element zawartości, uzyskać zapisywalny adres URL sygnatury dostępu współdzielonego do kontenera zasobów w magazynie i przekazać go do kontenera w magazynie przy użyciu adresu URL sygnatury dostępu współdzielonego, można znaleźć w temacie [Tworzenie danych wejściowych zadania z pliku lokalnego](job-input-from-local-file-how-to.md).

### <a name="create-a-new-asset"></a>Utwórz nowy element zawartości

> [!NOTE]
> Właściwości elementu zawartości typu datetime są zawsze w formacie UTC.

#### <a name="rest"></a>REST

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{amsAccountName}/assets/{assetName}?api-version=2018-07-01
```

Aby zapoznać się z przykładem, zobacz [Tworzenie zasobu z](https://docs.microsoft.com/rest/api/media/assets/createorupdate#examples) przykładem Rest.

W przykładzie pokazano, jak utworzyć **treść żądania** , gdzie można określić opis, nazwę kontenera, konto magazynu i inne przydatne informacje.

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

Pełny przykład można znaleźć w temacie [Tworzenie danych wejściowych zadania z pliku lokalnego](job-input-from-local-file-how-to.md). W Media Services v3 dane wejściowe zadania mogą być również tworzone na podstawie adresów URL HTTPS (zobacz [Tworzenie danych wejściowych zadania na podstawie adresu URL https](job-input-from-http-how-to.md)).

## <a name="map-v3-asset-properties-to-v2"></a>Mapuj właściwości zasobu v3 do wersji 2

W poniższej tabeli przedstawiono sposób, w jaki właściwości [zasobu](https://docs.microsoft.com/rest/api/media/assets/createorupdate#asset)w wersji v3 mapują się na właściwości zasobu w 2.

|Właściwości v3|Właściwości v2|
|---|---|
|`id` — (unikatowy) pełna ścieżka Azure Resource Manager, zobacz przykłady w elemencie [zawartości](https://docs.microsoft.com/rest/api/media/assets/createorupdate)||
|`name` — (unikatowy) zobacz [konwencje nazewnictwa](media-services-apis-overview.md#naming-conventions) ||
|`alternateId`|`AlternateId`|
|`assetId`|wartość `Id`-(Unique) rozpoczyna się od prefiksu `nb:cid:UUID:`.|
|`created`|`Created`|
|`description`|`Name`|
|`lastModified`|`LastModified`|
|`storageAccountName`|`StorageAccountName`|
|`storageEncryptionFormat`| `Options` (opcje tworzenia)|
|`type`||

## <a name="storage-side-encryption"></a>Szyfrowanie po stronie magazynu

Aby chronić Twoje zasoby w spoczynku, zasoby mają zostać zaszyfrowane za pomocą szyfrowania po stronie magazynu. W poniższej tabeli przedstawiono, jak działa szyfrowanie po stronie magazynu w usłudze Media Services:

|Opcja szyfrowania|Opis|Media Services v2|Media Services v3|
|---|---|---|---|
|Szyfrowanie magazynu usługi Media Services|Szyfrowanie AES-256, klucz zarządzany przez Media Services.|Obsługiwane<sup>(1)</sup>|Nieobsługiwane<sup>(2)</sup>|
|[szyfrowanie usługi Storage danych magazynowanych](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Szyfrowanie po stronie serwera oferowane przez usługę Azure Storage, klucz zarządzany przez platformę Azure lub przez klienta.|Obsługiwane|Obsługiwane|
|[Szyfrowanie po stronie klienta magazynu](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Szyfrowanie po stronie klienta oferowane przez usługę Azure Storage, klucz zarządzany przez klienta w Key Vault.|Nieobsługiwane|Nieobsługiwane|

<sup>1</sup> , gdy Media Services obsługuje obsługę zawartości w trybie Wyczyść/bez żadnej formy szyfrowania, nie jest to zalecane.

<sup>2</sup> w Media Services v3, szyfrowanie magazynu (szyfrowanie AES-256) jest obsługiwane tylko w przypadku zgodności z poprzednimi wersjami, gdy zasoby zostały utworzone przy użyciu Media Services V2. Oznacza to, że wersja v3 współpracuje z istniejącymi zasobami zaszyfrowanymi magazynu, ale nie umożliwia tworzenia nowych.

## <a name="filtering-ordering-paging"></a>Filtrowania, sortowania, stronicowania

Zobacz [filtrowanie, porządkowanie, stronicowanie jednostek Media Services](entities-overview.md).

## <a name="next-steps"></a>Następne kroki

* [Strumieniowe przesyłanie pliku](stream-files-dotnet-quickstart.md)
* [Korzystanie z funkcji DVR w chmurze](live-event-cloud-dvr.md)
* [Różnice między Media Services V2 i V3](migrate-from-v2-to-v3.md)
