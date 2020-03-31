---
title: Migrowanie z usługi Azure Media Services w wersji 2 do wersji 3
description: W tym artykule opisano zmiany, które zostały wprowadzone w usłudze Azure Media Services w wersji 3 i pokazuje różnice między dwiema wersjami.
services: media-services
documentationcenter: na
author: Juliako
manager: femila
editor: ''
tags: ''
keywords: azure media services, stream, broadcast, live, offline
ms.service: media-services
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 03/09/2020
ms.author: juliako
ms.openlocfilehash: fd094e35ceaa718ec1b258d74106b39744cbd16f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79087829"
---
# <a name="media-services-v2-vs-v3"></a>Usługi multimedialne w wersji 2 a 3

W tym artykule opisano zmiany, które zostały wprowadzone w usłudze Azure Media Services w wersji 3 i pokazuje różnice między dwiema wersjami.

## <a name="general-changes-from-v2"></a>Ogólne zmiany od v2

* W przypadku zasobów utworzonych w wersji 3 usługa Media Services obsługuje tylko [szyfrowanie magazynu po stronie serwera usługi Azure Storage.](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)
    * Interfejsy API w wersji 3 można używać z zasobami utworzonymi za pomocą interfejsów API w wersji 2, które miały [szyfrowanie magazynu](../previous/media-services-rest-storage-encryption.md) (AES 256) dostarczane przez program Media Services.
    * Nie można utworzyć nowych zasobów przy użyciu starszego [szyfrowania magazynu](../previous/media-services-rest-storage-encryption.md) AES 256 przy użyciu interfejsów API w wersji 3.
* Właściwości [zasobu](assets-concept.md)w wersji 3 różnią się od wersji 2, zobacz, [jak właściwości są mapowane](#map-v3-asset-properties-to-v2).
* SDK w wersji 3 są teraz oddzielone od SDK magazynu, co daje większą kontrolę nad wersją SDK magazynu, którego chcesz użyć, i unika problemów z przechowywaniem wersji. 
* W interfejsach API w wersji 3 wszystkie szybkości transmisji bitów kodowania są w bitach na sekundę. Jest to inne niż predefiniowane ustawienia cewaru multimediów w wersji 2. Na przykład szybkość transmisji bitów w wersji 2 zostanie określona jako 128 (kbps), ale w wersji 3 będzie to 128000 (bity/sekundę). 
* Jednostki AssetFiles, AccessPolicies i IngestManifests nie istnieją w wersji 3.
* Właściwość IAsset.ParentAssets nie istnieje w wersji 3.
* ContentKeys nie jest już jednostką, jest teraz właściwością lokalizatora przesyłania strumieniowego.
* Obsługa siatki zdarzeń zastępuje NotificationEndpoints.
* Następujące jednostki zostały zmienione
    * Dane wyjściowe zadania zastępuje zadanie i jest teraz częścią zadania.
    * Lokalizator przesyłania strumieniowego zastępuje lokalizator.
    * Wydarzenie na żywo zastępuje kanał.<br/>Rozliczenia za wydarzenia na żywo są oparte na licznikach kanałów na żywo. Aby uzyskać więcej informacji, zobacz [rozliczenia](live-event-states-billing.md) i [ceny](https://azure.microsoft.com/pricing/details/media-services/).
    * Wyjście na żywo zastępuje program.
* Dane wyjściowe na żywo są uruchamiane w momencie utworzenia i zatrzymywane podczas usuwania. Programy działały inaczej w interfejsach API w wersji 2, musiały zostać uruchomione po utworzeniu.
* Aby uzyskać informacje o zadaniu, musisz znać nazwę Przekształcanie, pod którą utworzono zadanie. 
* W wersji 2 pliki [metadanych wejścia](../previous/media-services-input-metadata-schema.md) i [wyjścia](../previous/media-services-output-metadata-schema.md) XML są generowane w wyniku zadania kodowania. W wersji 3 format metadanych zmienił się z XML na JSON. 
* W programie Media Services w wersji 2 można określić wektor inicjowania (IV). W umiań Media Services v3 nie można określić FairPlay IV. Chociaż nie ma to wpływu na klientów korzystających z usługi Media Services zarówno do pakowania, jak i dostarczania licencji, może to być problem podczas korzystania z systemu DRM innej firmy w celu dostarczenia licencji FairPlay (tryb hybrydowy). W takim przypadku ważne jest, aby wiedzieć, że FairPlay IV pochodzi od identyfikatora klucza cbcs i mogą być pobierane przy użyciu tej formuły:

    ```
    string cbcsIV =  Convert.ToBase64String(HexStringToByteArray(cbcsGuid.ToString().Replace("-", string.Empty)));
    ```

    with

    ``` 
    public static byte[] HexStringToByteArray(string hex)
    {
        return Enumerable.Range(0, hex.Length)
            .Where(x => x % 2 == 0)
            .Select(x => Convert.ToByte(hex.Substring(x, 2), 16))
            .ToArray();
    }
    ```

    Aby uzyskać więcej informacji, zobacz [kod usługi Azure Functions C# dla usługi Media Services w wersji 3 w trybie hybrydowym dla operacji na żywo i VOD.](https://github.com/Azure-Samples/media-services-v3-dotnet-core-functions-integration/tree/master/LiveAndVodDRMOperationsV3)
 
> [!NOTE]
> Przejrzyj konwencje nazewnictwa zastosowane do [zasobów usługi Media Services w wersji 3](media-services-apis-overview.md#naming-conventions). Przegląd [nazewnictwa obiektów blob](assets-concept.md#naming).

## <a name="feature-gaps-with-respect-to-v2-apis"></a>Brakujące funkcje w porównaniu z interfejsami API w wersji 2

Interfejs API w wersji 3 ma następujące luki funkcji w odniesieniu do interfejsu API w wersji 2. Likwidacja luk jest w toku.

* Koder [Premium](../previous/media-services-premium-workflow-encoder-formats.md) i starsze [procesory analizy multimediów](../previous/media-services-analytics-overview.md) (Usługa Azure Media Services Indexer 2 Preview, Face Redactor itp.) nie są dostępne za pośrednictwem wersji 3.<br/>Klienci, którzy chcą przeprowadzić migrację z podglądu indeksatora multimediów 1 lub 2, mogą natychmiast użyć ustawień predefiniowanych audioanalyzer w interfejsie API w wersji 3.  To nowe ustawienie wstępne zawiera więcej funkcji niż starszy indeksator multimediów 1 lub 2. 
* Wiele [zaawansowanych funkcji standardu Media Encoder Standard w interfejsach](../previous/media-services-advanced-encoding-with-mes.md) API w wersji 2 nie jest obecnie dostępnych w wersji 3, takich jak:
  
    * Szycie zasobów
    * Nakładki
    * Przycinania
    * Miniatura Sprites
    * Wstawianie cichej ścieżki dźwiękowej, gdy wejście nie ma dźwięku
    * Wstawianie ścieżki wideo, gdy wejście nie ma wideo
* Wydarzenia na żywo z transkodowaniem obecnie nie obsługują wstawiania slate w połowie strumienia i wstawiania znacznika reklamy za pośrednictwem wywołania interfejsu API. 
 
## <a name="asset-specific-changes"></a>Zmiany specyficzne dla zasobów

### <a name="map-v3-asset-properties-to-v2"></a>Mapowanie właściwości zasobu w wersji 3 na v2

W poniższej tabeli przedstawiono, jak właściwości [zasobu](https://docs.microsoft.com/rest/api/media/assets/createorupdate#asset)w wersji 3 są mapowane na właściwości zasobu w wersji 2.

|właściwości v3|właściwości v2|
|---|---|
|`id`- (unikalna) pełna ścieżka usługi Azure Resource Manager, zobacz przykłady w [zasobie](https://docs.microsoft.com/rest/api/media/assets/createorupdate)||
|`name`- (unikalne) patrz [Konwencje nazewnictwa](media-services-apis-overview.md#naming-conventions) ||
|`alternateId`|`AlternateId`|
|`assetId`|`Id`- (unikalna) wartość `nb:cid:UUID:` zaczyna się od prefiksu.|
|`created`|`Created`|
|`description`|`Name`|
|`lastModified`|`LastModified`|
|`storageAccountName`|`StorageAccountName`|
|`storageEncryptionFormat`| `Options`(opcje tworzenia)|
|`type`||

### <a name="storage-side-encryption"></a>Szyfrowanie po stronie magazynu

Aby chronić zasoby w spoczynku, zasoby powinny być szyfrowane przez szyfrowanie po stronie magazynu. W poniższej tabeli przedstawiono, jak działa szyfrowanie po stronie magazynu w umiaźniach Media Services:

|Opcja szyfrowania|Opis|Media Services v2|Media Services v3|
|---|---|---|---|
|Szyfrowanie magazynu usług multimedialnych|Szyfrowanie AES-256, klucz zarządzany przez media services.|Obsługiwane<sup>(1)</sup>|Nie obsługiwane<sup>(2)</sup>|
|[Szyfrowanie usługi przechowywania danych w spoczynku](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Szyfrowanie po stronie serwera oferowane przez usługę Azure Storage, klucz zarządzany przez platformę Azure lub przez klienta.|Obsługiwane|Obsługiwane|
|[Szyfrowanie po stronie klienta magazynu](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Szyfrowanie po stronie klienta oferowane przez usługę Azure Storage, klucz zarządzany przez klienta w usłudze Key Vault.|Nieobsługiwane|Nieobsługiwane|

<sup>1</sup> Chociaż usługi Media Services obsługuje obsługę zawartości w sposób przejrzysty/bez szyfrowania, nie jest to zalecane.

<sup>2</sup> W programie Media Services w wersji 3 szyfrowanie magazynu (szyfrowanie AES-256) jest obsługiwane tylko w celu zapewnienia zgodności z powrotem, gdy zasoby zostały utworzone za pomocą usługi Media Services w wersji 2. Oznacza to, że wersja 3 współpracuje z istniejącymi zaszyfrowanymi zasobami magazynu, ale nie pozwala na tworzenie nowych.

## <a name="code-differences"></a>Różnice w kodzie

W poniższej tabeli przedstawiono różnice w kodzie między wersjami 2 i 3 dla typowych scenariuszy.

|Scenariusz|V2 API|V3 API|
|---|---|---|
|Tworzenie zasobu i przekazywanie pliku |[przykład .NET w wersji 2](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L113)|[przykład .NET w wersji 3](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L169)|
|Przesyłanie zadania|[przykład .NET w wersji 2](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L146)|[przykład .NET w wersji 3](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L298)<br/><br/>Pokazuje, jak najpierw utworzyć transformację, a następnie przesłać zadanie.|
|Publikowanie zasobu za pomocą szyfrowania AES |1. Tworzenie ContentKeyAuthorizationPolicyOption<br/>2. Tworzenie ContentKeyAuthorizationPolicy<br/>3. Tworzenie AssetDeliveryPolicy<br/>4. Tworzenie zasobów i przesyłanie zawartości lub przesyłanie zadania i używanie zasobów wyjściowych<br/>5. Associate AssetDeliveryPolicy z aktywami<br/>6. Tworzenie contentkey<br/>7. Dołącz ContentKey do zasobu<br/>8. Tworzenie AccessPolicy<br/>9. Tworzenie lokalizatora<br/><br/>[przykład .NET w wersji 2](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L64)|1. Tworzenie zasad klucza zawartości<br/>2. Tworzenie zasobu<br/>3. Przesyłanie zawartości lub używanie zasobów jako funkcji JobOutput<br/>4. Tworzenie lokalizatora przesyłania strumieniowego<br/><br/>[przykład .NET w wersji 3](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs#L105)|
|Informacje o pracy i zarządzanie zadaniami |[Zarządzanie zadaniami w wersji 2](../previous/media-services-dotnet-manage-entities.md#get-a-job-reference) |[Zarządzanie zadaniami w wersji 3](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L546)|

> [!NOTE]
> Wpisz ten artykuł w zakładkę i sprawdzaj dostępność aktualizacji.

## <a name="ask-questions-give-feedback-get-updates"></a>Zadawaj pytania, przekazuj opinie, otrzyj aktualizacje

Zapoznaj się z artykułem [społeczności usługi Azure Media Services,](media-services-community.md) aby zobaczyć różne sposoby zadawania pytań, przekazywania opinii i otrzymywać aktualizacje dotyczące usługi Media Services.

## <a name="next-steps"></a>Następne kroki

[Wskazówki dotyczące migracji dotyczące przechodzenia z usługi Media Services w wersji 2 do wersji 3](migrate-from-v2-to-v3.md)
