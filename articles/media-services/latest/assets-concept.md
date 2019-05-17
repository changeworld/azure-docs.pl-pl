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
ms.date: 05/11/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 2afcf2066238414cd08e32901ffccf2a44718b6d
ms.sourcegitcommit: f013c433b18de2788bf09b98926c7136b15d36f1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/13/2019
ms.locfileid: "65551761"
---
# <a name="assets"></a>Zasoby

W usłudze Azure Media Services [zasobów](https://docs.microsoft.com/rest/api/media/assets) zawiera informacje o plikach cyfrowych przechowywanych w usłudze Azure Storage (w tym wideo, audio, obrazy, kolekcje miniatur, ścieżki tekstowe i pliki napisów). 

Element zawartości jest mapowany na kontener obiektów blob w [konta usługi Azure Storage](storage-account-concept.md) i pliki w elemencie zawartości są przechowywane jako blokowe obiekty BLOB w kontenerze. Usługa Media Services obsługuje warstwy obiektu Blob, gdy konto korzysta z ogólnego przeznaczenia w wersji 2 (GPv2) magazynu. Konta GPv2, umożliwia przeniesienie plików [chłodna lub archiwum](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers). **Archiwum** magazynu nadaje się do archiwizacji plików źródłowych, gdy nie będą już potrzebne (na przykład po został zakodowany).

**Archiwum** warstwy magazynowania jest zalecane tylko dla plików źródłowych bardzo duże, które już zaszyfrowana i kodowania dane wyjściowe zadania został umieszczony w kontenerze obiektów blob danych wyjściowych. Obiekty BLOB w kontenerze danych wyjściowych, który chcesz skojarzyć z zasobu i użycia, przesłać strumieniowo lub analizować zawartość, musi istnieć w **gorąca** lub **chłodna** warstwy magazynowania.

## <a name="upload-digital-files-into-assets"></a>Przekazać pliki cyfrowe do zasobów

Po pliki cyfrowe są przekazywane do magazynu i skojarzone z elementem zawartości, używać w usłudze Media Services, kodowanie, przesyłanie strumieniowe, analizowanie zawartości przepływów pracy. Jednym z typowych przepływów pracy usługi Media Services jest przekazywanie, kodowanie i przesyłanie strumieniowe plików. W tej sekcji opisano ogólne kroki.

> [!TIP]
> Przed rozpoczęciem tworzenia, przejrzyj [opracowywanie zawartości przy użyciu usługi Media Services v3 API](media-services-apis-overview.md) (w tym informacji na temat uzyskiwania dostępu do interfejsów API, konwencje nazewnictwa, itp.)

1. Użyj interfejsu API usługi Media Services w wersji 3, aby utworzyć nowy element zawartości typu „input”. Ta operacja polega na utworzeniu kontenera na koncie magazynu skojarzonym z kontem usługi Media Services. Interfejs API zwraca nazwę kontenera (na przykład `"container": "asset-b8d8b68a-2d7f-4d8c-81bb-8c7bbbe67ee4"`).
   
    Jeśli masz już kontener obiektów blob, który chcesz skojarzyć z elementem zawartości, możesz podać nazwę kontenera podczas tworzenia elementu zawartości. Usługa Media Services aktualnie obsługuje tylko obiekty blob w katalogu głównym kontenera, a nie w obrębie ścieżek w nazwie pliku. W związku z tym kontener z nazwą pliku „input.mp4” będzie działać. Kontener z nazwą pliku videos/inputs/input.mp4” nie będzie działać.

    Za pomocą wiersza polecenia platformy Azure można przekazywać pliki bezpośrednio do dowolnego konta magazynu i kontenera, do których masz uprawnienia w subskrypcji. <br/>Nazwa kontenera musi być unikatowa i zgodna ze wskazówkami dotyczącymi nazewnictwa magazynu. Nazwa nie musi być zgodna z formatowaniem nazwy kontenera elementów zawartości usługi Media Services (Asset-GUID). 
    
    ```azurecli
    az storage blob upload -f /path/to/file -c MyContainer -n MyBlob
    ```
2. Pobierz adres URL sygnatury dostępu współdzielonego z uprawnieniami odczytu/zapisu, który będzie używany do przekazywania plików cyfrowych do kontenera elementów zawartości. W celu [utworzenia listy adresów URL kontenerów elementów zawartości](https://docs.microsoft.com/rest/api/media/assets/listcontainersas) można użyć interfejsu API usługi Media Services.
3. W celu przekazania plików do kontenera elementów zawartości należy użyć interfejsów API lub zestawów SDK usługi Azure Storage (np. [interfejsu API REST magazynu](../../storage/common/storage-rest-api-auth.md), [zestawu JAVA SDK](../../storage/blobs/storage-quickstart-blobs-java-v10.md) lub [zestawu .NET SDK](../../storage/blobs/storage-quickstart-blobs-dotnet.md)). 
4. W celu utworzenia przekształcenia i zadania przetwarzającego element zawartości „input” należy użyć interfejsów API usługi Media Services w wersji 3. Aby uzyskać więcej informacji, zobacz [Przekształcenia i zadania](transform-concept.md).
5. Stream zawartość z zasobu "Wyjście".

Aby uzyskać pełny przykład .NET, który pokazuje, jak: tworzenia zasobu, Pobierz adres URL zapisu sygnatury dostępu Współdzielonego do zasobu kontenera w magazynie, przekazać plik do kontenera w magazynie przy użyciu adresu URL sygnatury dostępu Współdzielonego, zobacz [utworzyć dane wejściowe zadania z pliku lokalnego](job-input-from-local-file-how-to.md).

### <a name="create-a-new-asset"></a>Utwórz nowy zasób

> [!NOTE]
> Właściwości zasobu typu Data/Godzina, zawsze znajdują się w formacie UTC.

#### <a name="rest"></a>REST

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{amsAccountName}/assets/{assetName}?api-version=2018-07-01
```

Na przykład REST, zobacz [utworzenie elementu zawartości z użyciem usług REST](https://docs.microsoft.com/rest/api/media/assets/createorupdate#examples) przykład.

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

Aby uzyskać pełny przykład, zobacz [utworzyć dane wejściowe zadania z pliku lokalnego](job-input-from-local-file-how-to.md). W wersji 3 usługa Media Services, dane wejściowe zadania można również utworzyć z adresów URL protokołu HTTPS (zobacz [tworzenie dane wejściowe zadania na podstawie adresu URL HTTPS](job-input-from-http-how-to.md)).

## <a name="filtering-ordering-paging"></a>Filtrowania, sortowania, stronicowania

Zobacz [filtrowanie, porządkowanie, stronicowanie jednostek usługi Media Services](entities-overview.md).

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

* [Strumieniowe przesyłanie pliku](stream-files-dotnet-quickstart.md)
* [Korzystanie z funkcji DVR w chmurze](live-event-cloud-dvr.md)
* [Różnice między Media Services v2 i v3](migrate-from-v2-to-v3.md)
