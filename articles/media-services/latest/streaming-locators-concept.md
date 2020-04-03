---
title: Lokalizatory przesyłania strumieniowego w usłudze Azure Media Services | Dokumenty firmy Microsoft
description: W tym artykule wyjaśniono, czym są lokalizatory przesyłania strumieniowego i jak są one używane przez usługę Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/04/2020
ms.author: juliako
ms.openlocfilehash: 41b2d0ad1e072fb2bf5860ae80f8f25f886b37f7
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80582673"
---
# <a name="streaming-locators"></a>Lokalizatory przesyłania strumieniowego

Aby udostępnić klientom filmy wideo w wyjściowym elemencie zawartości w celu odtwarzania, trzeba utworzyć [lokalizator przesyłania strumieniowego](https://docs.microsoft.com/rest/api/media/streaminglocators) i skompilować adresy URL przesyłania strumieniowego. Aby utworzyć adres URL, musisz połączyć nazwę hosta punktu końcowego przesyłania strumieniowego i ścieżkę lokalizatora przesyłania strumieniowego. Aby uzyskać przykład platformy .NET, zobacz [Pobieranie lokalizatora przesyłania strumieniowego](stream-files-tutorial-with-api.md#get-a-streaming-locator).

Proces tworzenia **lokalizatora przesyłania strumieniowego** jest nazywany publikowaniem. Domyślnie **lokalizator przesyłania strumieniowego** jest ważny natychmiast po wykonaniu wywołań interfejsu API i aż do jego usunięcia, chyba że skonfigurujesz opcjonalne czasy rozpoczęcia i zakończenia. 

Podczas tworzenia **lokalizatora przesyłania strumieniowego**należy określić nazwę **zasobu** i nazwę **zasad przesyłania strumieniowego.** Aby uzyskać więcej informacji, zobacz następujące tematy:

* [Elementy zawartości](assets-concept.md)
* [Zasady przesyłania strumieniowego](streaming-policy-concept.md)
* [Zasady klucza zawartości](content-key-policy-concept.md)

Możesz również określić godzinę rozpoczęcia i zakończenia lokalizatora przesyłania strumieniowego, która pozwoli użytkownikowi odtwarzać zawartość tylko między tymi czasami (na przykład między 5/1/2019 a 5/5/2019).  

## <a name="considerations"></a>Zagadnienia do rozważenia

* **Lokalizatory przesyłania strumieniowego** nie można aktualizować. 
* Właściwości **lokalizatorów przesyłania strumieniowego,** które są typu Datetime są zawsze w formacie UTC.
* Należy zaprojektować ograniczony zestaw zasad dla konta usługi Media Service i użyć ich ponownie dla lokalizatorów przesyłania strumieniowego, gdy są potrzebne te same opcje. Aby uzyskać więcej informacji, zobacz [Przydziały i limity](limits-quotas-constraints.md).

## <a name="create-streaming-locators"></a>Tworzenie lokalizatorów przesyłania strumieniowego  

### <a name="not-encrypted"></a>Nie zaszyfrowane

Jeśli chcesz przesyłać strumieniowo plik w postaci przezroczystej (niezaszyfrowanej), ustaw wstępnie zdefiniowaną, przejrzystą zasadę przesyłania strumieniowego: na "Predefined_ClearStreamingOnly" (w .NET możesz użyć wyliczenia PredefinedStreamingPolicy.ClearStreamingOnly).

```csharp
StreamingLocator locator = await client.StreamingLocators.CreateAsync(
    resourceGroup,
    accountName,
    locatorName,
    new StreamingLocator
    {
        AssetName = assetName,
        StreamingPolicyName = PredefinedStreamingPolicy.ClearStreamingOnly
    });
```

### <a name="encrypted"></a>Zaszyfrowane 

Jeśli chcesz zaszyfrować zawartość za pomocą szyfrowania CENC, ustaw swoje zasady na "Predefined_MultiDrmCencStreaming". Szyfrowanie Widevine zostanie zastosowane do strumienia DASH i PlayReady do Smooth. Klucz zostanie dostarczony do klienta odtwarzania na podstawie skonfigurowanych licencji DRM.

```csharp
StreamingLocator locator = await client.StreamingLocators.CreateAsync(
    resourceGroup,
    accountName,
    locatorName,
    new StreamingLocator
    {
        AssetName = assetName,
        StreamingPolicyName = "Predefined_MultiDrmCencStreaming",
        DefaultContentKeyPolicyName = contentPolicyName
    });
```

Jeśli chcesz również zaszyfrować strumień HLS za pomocą CBCS (FairPlay), użyj "Predefined_MultiDrmStreaming".

> [!NOTE]
> Widevine jest usługą świadczoną przez Google Inc. i podlega warunkom korzystania z usługi oraz Polityce prywatności Firmy Google, Inc.

## <a name="associate-filters-with-streaming-locators"></a>Kojarzenie filtrów z lokalizatorami przesyłania strumieniowego

Zobacz [Filtry: skojarzyć z lokalizatorów przesyłania strumieniowego](filters-concept.md#associating-filters-with-streaming-locator).

## <a name="filter-order-page-streaming-locator-entities"></a>Filtrowanie, kolejność, strona Streaming Locator jednostek

Zobacz [Filtrowanie, zamawianie, stronicowanie jednostek usługi Media Services](entities-overview.md).

## <a name="list-streaming-locators-by-asset-name"></a>Lista lokalizatorów przesyłania strumieniowego według nazwy zasobu

Aby uzyskać lokalizatory przesyłania strumieniowego na podstawie skojarzonej nazwy zasobu, należy użyć następujących operacji:

|Język|interfejs API|
|---|---|
|REST|[lokalizatory liststreamingu](https://docs.microsoft.com/rest/api/media/assets/liststreaminglocators)|
|Interfejs wiersza polecenia|[az ams asset list-streaming-lokalizatory](https://docs.microsoft.com/cli/azure/ams/asset?view=azure-cli-latest#az-ams-asset-list-streaming-locators)|
|.NET|[Lokalizatory liststreamingu](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.assetsoperationsextensions.liststreaminglocators?view=azure-dotnet#Microsoft_Azure_Management_Media_AssetsOperationsExtensions_ListStreamingLocators_Microsoft_Azure_Management_Media_IAssetsOperations_System_String_System_String_System_String_)|
|Java|[Lokalizator zasobów](https://docs.microsoft.com/rest/api/media/assets/liststreaminglocators#assetstreaminglocator)|
|Node.js|[listSlodatory lokalizatorów](https://docs.microsoft.com/javascript/api/@azure/arm-mediaservices/assets#liststreaminglocators-string--string--string--msrest-requestoptionsbase-)|

## <a name="see-also"></a>Zobacz też

* [Elementy zawartości](assets-concept.md)
* [Zasady przesyłania strumieniowego](streaming-policy-concept.md)
* [Zasady klucza zawartości](content-key-policy-concept.md)
* [Samouczek: Przesyłanie, kodowanie i przesyłanie strumieniowe filmów za pomocą platformy .NET](stream-files-tutorial-with-api.md)

## <a name="next-steps"></a>Następne kroki

[Jak utworzyć lokalizator przesyłania strumieniowego i utworzyć adresy URL](create-streaming-locator-build-url.md)
