---
title: Zasoby w Azure Media Services | Microsoft Docs
description: Ten artykuł zawiera opis zasoby są i jak są one używane przez usługi Azure Media Services.
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
ms.openlocfilehash: 2f2dea922b7a3ba45ad6493ce94f0c52649dfa68
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/03/2019
ms.locfileid: "70230983"
---
# <a name="assets"></a>Elementy zawartości

W Azure Media Services [zasób](https://docs.microsoft.com/rest/api/media/assets) zawiera informacje o plikach cyfrowych przechowywanych w usłudze Azure Storage (w tym wideo, audio, obrazy, kolekcje miniatur, ścieżki tekstowe i pliki napisów). 

Zasób jest mapowany do kontenera obiektów BLOB na [koncie usługi Azure Storage](storage-account-concept.md) , a pliki w elemencie zawartości są przechowywane jako blokowe obiekty blob w tym kontenerze. Media Services obsługuje warstwy obiektów blob, gdy konto używa magazynu ogólnego przeznaczenia w wersji 2 (GPv2). Za pomocą GPv2 można przenosić pliki do [magazynu chłodnego lub archiwum](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers). Magazyn archiwalny jest odpowiedni do archiwizowania plików źródłowych, gdy nie są już potrzebne (na przykład po ich zakodowaniu).

Warstwa magazynu **archiwum** jest zalecana tylko dla bardzo dużych plików źródłowych, które zostały już zakodowane i dane wyjściowe zadania kodowania zostały umieszczone w wyjściowym kontenerze obiektów BLOB. Obiekty blob w kontenerze danych wyjściowych, które mają zostać skojarzone z zasobem i używają do przesyłania strumieniowego lub analizowania zawartości, muszą znajdować się w warstwie magazynowania gorąca lub **chłodna** .

### <a name="naming-blobs"></a>Nazywanie obiektów BLOB

Nazwy plików/obiektów BLOB w obrębie elementu zawartości muszą spełniać zarówno [wymagania dotyczące nazw obiektów BLOB](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata) , jak i [wymagania dotyczące nazw NTFS](https://docs.microsoft.com/windows/win32/fileio/naming-a-file). Przyczyną tych wymagań jest możliwość skopiowania plików z magazynu obiektów BLOB do lokalnego dysku NTFS w celu przetworzenia.

## <a name="upload-digital-files-into-assets"></a>Przekazywanie plików cyfrowych do zasobów

Po przekazaniu plików cyfrowych do magazynu i skojarzeniu ich z elementem zawartości mogą one być używane w kodowaniu Media Services, przesyłanie strumieniowe, analizowanie przepływów pracy zawartości. Jednym z typowych przepływów pracy Media Services jest przekazywanie, kodowanie i przesyłanie strumieniowe pliku. Ta sekcja zawiera opis ogólnych kroków.

> [!TIP]
> Przed rozpoczęciem opracowywania, zobacz [Tworzenie aplikacji przy użyciu interfejsów api Media Services v3](media-services-apis-overview.md) (w tym informacje na temat uzyskiwania dostępu do interfejsów API, konwencji nazewnictwa itp.).

1. Użyj interfejsu API usługi Media Services w wersji 3, aby utworzyć nowy element zawartości typu „input”. Ta operacja polega na utworzeniu kontenera na koncie magazynu skojarzonym z kontem usługi Media Services. Interfejs API zwraca nazwę kontenera (na przykład `"container": "asset-b8d8b68a-2d7f-4d8c-81bb-8c7bbbe67ee4"`).
   
    Jeśli masz już kontener obiektów blob, który chcesz skojarzyć z elementem zawartości, możesz podać nazwę kontenera podczas tworzenia elementu zawartości. Usługa Media Services aktualnie obsługuje tylko obiekty blob w katalogu głównym kontenera, a nie w obrębie ścieżek w nazwie pliku. W związku z tym kontener z nazwą pliku „input.mp4” będzie działać. Kontener z nazwą pliku videos/inputs/input.mp4” nie będzie działać.

    Za pomocą wiersza polecenia platformy Azure można przekazywać pliki bezpośrednio do dowolnego konta magazynu i kontenera, do których masz uprawnienia w subskrypcji. <br/>Nazwa kontenera musi być unikatowa i zgodna ze wskazówkami dotyczącymi nazewnictwa magazynu. Nazwa nie musi być zgodna z formatowaniem nazwy kontenera elementów zawartości usługi Media Services (Asset-GUID). 
    
    ```azurecli
    az storage blob upload -f /path/to/file -c MyContainer -n MyBlob
    ```
2. Pobierz adres URL sygnatury dostępu współdzielonego z uprawnieniami odczytu/zapisu, który będzie używany do przekazywania plików cyfrowych do kontenera elementów zawartości. W celu [utworzenia listy adresów URL kontenerów elementów zawartości](https://docs.microsoft.com/rest/api/media/assets/listcontainersas) można użyć interfejsu API usługi Media Services.
3. W celu przekazania plików do kontenera elementów zawartości należy użyć interfejsów API lub zestawów SDK usługi Azure Storage (np. [interfejsu API REST magazynu](../../storage/common/storage-rest-api-auth.md), [zestawu JAVA SDK](../../storage/blobs/storage-quickstart-blobs-java-v10.md) lub [zestawu .NET SDK](../../storage/blobs/storage-quickstart-blobs-dotnet.md)). 
4. W celu utworzenia przekształcenia i zadania przetwarzającego element zawartości „input” należy użyć interfejsów API usługi Media Services w wersji 3. Aby uzyskać więcej informacji, zobacz [Przekształcenia i zadania](transform-concept.md).
5. Przesyłaj strumieniowo zawartość z zasobu "output".

Pełny przykład platformy .NET, który pokazuje, jak: utworzyć element zawartości, uzyskać zapisywalny adres URL sygnatury dostępu współdzielonego do kontenera zasobów w magazynie, Przekaż plik do kontenera w magazynie przy użyciu adresu URL sygnatury dostępu współdzielonego, zobacz [Tworzenie danych wejściowych zadania z pliku lokalnego](job-input-from-local-file-how-to.md).

### <a name="create-a-new-asset"></a>Utwórz nowy element zawartości

> [!NOTE]
> Właściwości elementu zawartości typu datetime są zawsze w formacie UTC.

#### <a name="rest"></a>REST

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{amsAccountName}/assets/{assetName}?api-version=2018-07-01
```

Aby zapoznać się z przykładem, zobacz [Tworzenie zasobu z](https://docs.microsoft.com/rest/api/media/assets/createorupdate#examples) przykładem Rest.

W przykładzie pokazano sposób utworzenia **treści żądania**, w której można określić przydatne informacje, takie jak opis, nazwa kontenera, konto magazynu oraz inne informacje.

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
|Identyfikator — (unikatowy) pełna ścieżka Azure Resource Manager, zobacz przykłady w elemencie [zawartości](https://docs.microsoft.com/rest/api/media/assets/createorupdate)||
|Nazwa — (unikatowy) zobacz [konwencje nazewnictwa](media-services-apis-overview.md#naming-conventions) ||
|alternateId|AlternateId|
|assetId|Wartość identyfikatora — (unikatowa) rozpoczyna się `nb:cid:UUID:` od prefiksu.|
|utworzone|Utworzono|
|description|Name|
|lastModified|Ostatnia modyfikacja|
|storageAccountName|StorageAccountName|
|storageEncryptionFormat| Opcje (opcje tworzenia)|
|type||

## <a name="storage-side-encryption"></a>Szyfrowanie po stronie magazynu

Aby chronić Twoje zasoby w spoczynku, zasoby mają zostać zaszyfrowane za pomocą szyfrowania po stronie magazynu. W poniższej tabeli przedstawiono, jak działa szyfrowanie po stronie magazynu w usłudze Media Services:

|Opcja szyfrowania|Opis|Media Services v2|Media Services v3|
|---|---|---|---|
|Szyfrowanie magazynu usługi Media Services|AES-256 szyfrowania kluczy zarządzanych przez usługę Media Services|Obsługiwane<sup>(1)</sup>|Nieobsługiwane<sup>(2)</sup>|
|[Szyfrowanie usługi Storage dla danych magazynowanych](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Szyfrowanie po stronie serwera, oferowane przez usługę Azure Storage, klucz zarządzany przez platformę Azure lub przez klienta|Obsługiwane|Obsługiwane|
|[Szyfrowanie po stronie klienta magazynu](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Szyfrowanie po stronie klienta, oferowane przez usługę Azure storage, klucz zarządzany przez klienta w usłudze Key Vault|Nieobsługiwane|Nieobsługiwane|

<sup>1</sup> a Media Services obsługuje obsługi zawartości, bez zabezpieczeń/bez jakiejkolwiek formy szyfrowania, to nie jest to zalecane.

<sup>2</sup> Media Services v3, szyfrowanie magazynu (szyfrowanie AES-256) jest tylko obsługiwane dla zapewnienia zgodności gdy Twoje zasoby zostały utworzone za pomocą usługi Media Services v2. Co oznacza v3 współpracuje z istniejącym magazynie zaszyfrowane zasoby, ale nie pozwoli na tworzenie nowych.

## <a name="filtering-ordering-paging"></a>Filtrowania, sortowania, stronicowania

Zobacz [filtrowanie, porządkowanie, stronicowanie jednostek Media Services](entities-overview.md).

## <a name="next-steps"></a>Następne kroki

* [Strumieniowe przesyłanie pliku](stream-files-dotnet-quickstart.md)
* [Korzystanie z funkcji DVR w chmurze](live-event-cloud-dvr.md)
* [Różnice między Media Services V2 i V3](migrate-from-v2-to-v3.md)
