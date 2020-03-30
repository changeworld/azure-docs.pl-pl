---
title: Zarządzanie zasobami w usłudze Azure Media Services
titleSuffix: Azure Media Services
description: Zasób, w którym są wprowadzane nośniki (na przykład za pośrednictwem przekazywania lub pozyskiwania na żywo), media wyjściowe (z danych wyjściowych zadania) i publikowanie multimediów z (do przesyłania strumieniowego). W tym temacie przedstawiono omówienie sposobu tworzenia nowego zasobu i przekazywania plików.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/26/2020
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: fcdb8af770fa0068e8413d4609a56223a9a20ce2
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80345894"
---
# <a name="manage-assets"></a>Zarządzanie elementami zawartości

W usłudze Azure Media Services [zasób](https://docs.microsoft.com/rest/api/media/assets) to miejsce, w którym 

* przesyłanie plików multimedialnych do zasobu,
* pozyskiwania i archiwizowania strumieni na żywo do zasobu,
* wyniki kodowania zadania analitycznego do zasobu,
* publikować nośniki do przesyłania strumieniowego, 
* pobierać pliki z zasobu.

W tym temacie przedstawiono omówienie sposobu przekazywania plików do zasobu i wykonywania innych typowych operacji. Zawiera również łącza do przykładów kodu i powiązanych tematów.

## <a name="prerequisite"></a>Wymagania wstępne 

Zanim zaczniesz się rozwijać, przejrzyj:

* [Pojęcia](concepts-overview.md)
* [Tworzenie za pomocą interfejsów API usługi Media Services w wersji 3](media-services-apis-overview.md) (zawiera informacje na temat uzyskiwania dostępu do interfejsów API, konwencji nazewnictwa itd.) 

## <a name="upload-media-files-into-an-asset"></a>Przekazywanie plików multimedialnych do zasobu

Po przekazaniu plików cyfrowych do magazynu i skojarzeniu z zasobem mogą być używane w przepływach pracy kodowania, przesyłania strumieniowego i analizowania zawartości usługi Media Services. Jednym z typowych przepływów pracy usługi Media Services jest przekazywanie, kodowanie i przesyłanie strumieniowe pliku. W tej sekcji przedstawiono ogólne kroki.

1. Użyj interfejsu API usługi Media Services w wersji 3, aby utworzyć nowy element zawartości typu „input”. Ta operacja polega na utworzeniu kontenera na koncie magazynu skojarzonym z kontem usługi Media Services. Interfejs API zwraca nazwę kontenera `"container": "asset-b8d8b68a-2d7f-4d8c-81bb-8c7bbbe67ee4"`(na przykład ).

    Jeśli masz już kontener obiektów blob, który chcesz skojarzyć z zasobem, możesz określić nazwę kontenera podczas tworzenia zasobu. Usługa Media Services aktualnie obsługuje tylko obiekty blob w katalogu głównym kontenera, a nie w obrębie ścieżek w nazwie pliku. W związku z tym kontener z nazwą pliku „input.mp4” będzie działać. Jednak kontener o nazwie pliku "videos/inputs/input.mp4" nie będzie działać.

    Za pomocą wiersza polecenia platformy Azure można przekazywać pliki bezpośrednio do dowolnego konta magazynu i kontenera, do których masz uprawnienia w subskrypcji.

    Nazwa kontenera musi być unikatowa i zgodna ze wskazówkami dotyczącymi nazewnictwa magazynu. Nazwa nie musi być zgodna z formatowaniem nazwy kontenera elementów zawartości usługi Media Services (Asset-GUID).

    ```azurecli
    az storage blob upload -f /path/to/file -c MyContainer -n MyBlob
    ```
2. Pobierz adres URL sygnatury dostępu współdzielonego z uprawnieniami odczytu/zapisu, który będzie używany do przekazywania plików cyfrowych do kontenera elementów zawartości.

    W celu [utworzenia listy adresów URL kontenerów elementów zawartości](https://docs.microsoft.com/rest/api/media/assets/listcontainersas) można użyć interfejsu API usługi Media Services.

    **AssetContainerSas.listContainerSas** przyjmuje parametr [ListContainerSasInput,](https://docs.microsoft.com/rest/api/media/assets/listcontainersas#listcontainersasinput) `expiryTime`na którym ustawiono plik . Czas powinien być ustawiony na < 24 godziny.

    [ListContainerSasInput](https://docs.microsoft.com/rest/api/media/assets/listcontainersas#listcontainersasinput) zwraca wiele adresów URL Sygnatury dostępu Współdzielonego, ponieważ dla każdego konta magazynu są dwa klucze konta magazynu. Konto magazynu ma dwa klucze, ponieważ umożliwia bezproblemowe obracanie kluczy konta magazynu (na przykład zmień jeden podczas korzystania z drugiego, a następnie zacznij używać nowego klucza i obróć drugi klucz). Adres URL 1 sygnatury dostępu Współdzielonego reprezentuje klucz magazynu1 i drugi klucz magazynu2.
3. Użyj interfejsów API usługi Azure Storage lub zestawów SDK (na przykład [interfejsu API REST magazynu](../../storage/common/storage-rest-api-auth.md) lub [.NET SDK](../../storage/blobs/storage-quickstart-blobs-dotnet.md)), aby przekazać pliki do kontenera zasobów.
4. W celu utworzenia przekształcenia i zadania przetwarzającego element zawartości „input” należy użyć interfejsów API usługi Media Services w wersji 3. Aby uzyskać więcej informacji, zobacz [Przekształcenia i zadania](transform-concept.md).
5. Przesyłaj strumieniowo zawartość z zasobu "output".

### <a name="create-a-new-asset"></a>Tworzenie nowego elementu zawartości

> [!NOTE]
> Właściwości zasobu typu Datetime są zawsze w formacie UTC.

#### <a name="rest"></a>REST

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{amsAccountName}/assets/{assetName}?api-version=2018-07-01
```

Przykład REST można znaleźć w [przykładzie Tworzenie zasobu z restem.](https://docs.microsoft.com/rest/api/media/assets/createorupdate#examples)

W przykładzie pokazano, jak utworzyć **treść żądania,** gdzie można określić opis, nazwę kontenera, konto magazynu i inne przydatne informacje.

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

### <a name="see-also"></a>Zobacz też

* [Tworzenie danych wejściowych zadania z pliku lokalnego](job-input-from-local-file-how-to.md)
* [Tworzenie danych wejściowych zadania z adresu URL HTTPS](job-input-from-http-how-to.md)

## <a name="ingest-and-archive-live-streams-into-an-asset"></a>Połknąć i archiwizować transmisje na żywo do zasobu

W usłudze Media Services obiekt [live output](https://docs.microsoft.com/rest/api/media/liveoutputs) jest jak cyfrowy rejestrator wideo, który przechwytuje i rejestruje strumień na żywo do zasobu na koncie usługi Media Services. Zarejestrowana zawartość jest utrwalona w kontenerze zdefiniowanym przez [zasób zasobów.](https://docs.microsoft.com/rest/api/media/assets)

Aby uzyskać więcej informacji, zobacz:

* [Korzystanie z funkcji DVR w chmurze](live-event-cloud-dvr.md)
* [Samouczek przesyłania strumieniowego na żywo](stream-live-tutorial-with-api.md)

## <a name="output-the-results-of-a-job-to-an-asset"></a>Wyprowadzanie wyników zadania do zasobu

W umywoniu multimediów podczas przetwarzania filmów (na przykład kodowania lub analizowania) należy utworzyć [zasób](assets-concept.md) wyjściowy do przechowywania wyniku [zadania.](transforms-jobs-concept.md)

Aby uzyskać więcej informacji, zobacz:

* [Kodowanie wideo](encoding-concept.md)
* [Tworzenie danych wejściowych zadania z pliku lokalnego](job-input-from-local-file-how-to.md)

## <a name="publish-an-asset-for-streaming"></a>Publikowanie zasobu do przesyłania strumieniowego

Aby opublikować zasób do przesyłania strumieniowego, należy utworzyć [lokalizator przesyłania strumieniowego](streaming-locators-concept.md). Lokalizator przesyłania strumieniowego musi znać nazwę zasobu, którą chcesz opublikować. 

Aby uzyskać więcej informacji, zobacz:

[Samouczek: Przesyłanie, kodowanie i przesyłanie strumieniowe filmów za pomocą usługi Media Services w wersji 3](stream-files-tutorial-with-api.md)

## <a name="download-results-of-a-job-from-an-output-asset"></a>Pobieranie wyników zadania z zasobu wyjściowego

Następnie można pobrać te wyniki zadania do folderu lokalnego przy użyciu interfejsów API usługi multimediów i magazynu. 

Zobacz przykład [pobierania plików.](download-results-howto.md)

## <a name="filtering-ordering-paging"></a>Filtrowanie, zamawianie, stronicowanie

Zobacz [Filtrowanie, zamawianie, stronicowanie jednostek usługi Media Services](entities-overview.md).

## <a name="next-steps"></a>Następne kroki

Zobacz pełne przykłady kodu, które pokazują, jak przesyłać, kodować, analizować, przesyłać strumieniowo na żywo i na żądanie: 

* [Java](https://docs.microsoft.com/samples/azure-samples/media-services-v3-java/azure-media-services-v3-samples-using-java/), 
* [.NET](https://docs.microsoft.com/samples/azure-samples/media-services-v3-dotnet/azure-media-services-v3-samples-using-net/), 
* [ODPOCZYNEK](https://docs.microsoft.com/samples/azure-samples/media-services-v3-rest-postman/azure-media-services-postman-collection/).
