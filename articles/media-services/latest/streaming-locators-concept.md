---
title: Lokalizatory przesyłania strumieniowego w Azure Media Services | Microsoft Docs
description: Ten artykuł zawiera wyjaśnienie, co to są lokalizatory przesyłania strumieniowego i jak są używane przez Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/26/2019
ms.author: juliako
ms.openlocfilehash: a227c5d0edfb9b49e70452cfcfd08b29ba381857
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74969110"
---
# <a name="streaming-locators"></a>Lokalizatory przesyłania strumieniowego

Aby udostępnić klientom filmy wideo w wyjściowym elemencie zawartości w celu odtwarzania, trzeba utworzyć [lokalizator przesyłania strumieniowego](https://docs.microsoft.com/rest/api/media/streaminglocators) i skompilować adresy URL przesyłania strumieniowego. Aby utworzyć adres URL, należy połączyć nazwę hosta punktu końcowego przesyłania strumieniowego i ścieżkę lokalizatora przesyłania strumieniowego. Aby uzyskać przykład platformy .NET, zobacz [Pobieranie lokalizatora przesyłania strumieniowego](stream-files-tutorial-with-api.md#get-a-streaming-locator).

Proces tworzenia **lokalizatora przesyłania strumieniowego** jest nazywany publikowaniem. Domyślnie **lokalizator przesyłania strumieniowego** jest ważny natychmiast po wykonaniu wywołań interfejsu API i aż do jego usunięcia, chyba że skonfigurujesz opcjonalne czasy rozpoczęcia i zakończenia. 

Podczas tworzenia **lokalizatora przesyłania strumieniowego**należy określić nazwę **zasobu** i nazwę **zasad przesyłania strumieniowego** . Aby uzyskać więcej informacji, zobacz następujące tematy:

* [Elementy zawartości](assets-concept.md)
* [Zasady przesyłania strumieniowego](streaming-policy-concept.md)
* [Zasady kluczy zawartości](content-key-policy-concept.md)

Możesz również określić godzinę rozpoczęcia i zakończenia w usłudze przesyłania strumieniowego, która będzie umożliwiać użytkownikowi odtwarzanie zawartości między tymi godzinami (na przykład od 5/1/2019 do 5/5/2019).  

## <a name="considerations"></a>Zagadnienia do rozważenia

* **Lokalizatory przesyłania strumieniowego** nie są aktualizowalne. 
* Właściwości **lokalizatorów przesyłania strumieniowego** , które są typu DateTime, są zawsze w formacie UTC.
* Należy zapoznać się z ograniczonym zestawem zasad dla konta usługi multimediów i użyć ich ponownie dla lokalizatorów przesyłania strumieniowego, gdy są potrzebne te same opcje. Aby uzyskać więcej informacji, zobacz [przydziały i ograniczenia](limits-quotas-constraints.md).

## <a name="create-streaming-locators"></a>Utwórz lokalizatory przesyłania strumieniowego  

### <a name="not-encrypted"></a>Nieszyfrowane

Jeśli chcesz przesłać strumieniowo plik z niezaszyfrowanym (nieszyfrowanym), ustaw wstępnie zdefiniowane zasady wyznaczania strumienia: na "Predefined_ClearStreamingOnly" (w programie .NET można użyć wyliczenia PredefinedStreamingPolicy. ClearStreamingOnly).

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

### <a name="encrypted"></a>Szyfrowane 

Jeśli zachodzi potrzeba zaszyfrowania zawartości przy użyciu szyfrowania CENC, ustaw zasady na "Predefined_MultiDrmCencStreaming". Szyfrowanie Widevine zostanie zastosowane do strumienia PAUZ i oprogramowania PlayReady do wygładzania. Klucz zostanie dostarczony do klienta odtwarzania na podstawie skonfigurowanych licencji DRM.

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
> Widevine to usługa świadczona przez firmę Google Inc. z zastrzeżeniem warunków użytkowania i zasad zachowania poufności informacji w firmie Google, Inc.

## <a name="associate-filters-with-streaming-locators"></a>Skojarz filtry z lokalizatorami przesyłania strumieniowego

Zobacz [filtry: Skojarz z lokalizatorami przesyłania strumieniowego](filters-concept.md#associating-filters-with-streaming-locator).

## <a name="filter-order-page-streaming-locator-entities"></a>Filtrowanie, kolejność, przesyłanie strumieniowe jednostek lokalizatora stron

Zobacz [filtrowanie, porządkowanie, stronicowanie jednostek Media Services](entities-overview.md).

## <a name="list-streaming-locators-by-asset-name"></a>Wyświetl listę lokalizatorów przesyłania strumieniowego według nazwy zasobu

Aby uzyskać lokalizatory przesyłania strumieniowego na podstawie nazwy skojarzonego elementu zawartości, należy wykonać następujące operacje:

|Język|API|
|---|---|
|REST|[liststreaminglocators](https://docs.microsoft.com/rest/api/media/assets/liststreaminglocators)|
|Interfejs CLI|[AZ AMS Asset list-remisjeers-Locators](https://docs.microsoft.com/cli/azure/ams/asset?view=azure-cli-latest#az-ams-asset-list-streaming-locators)|
|.NET|[ListStreamingLocators](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.assetsoperationsextensions.liststreaminglocators?view=azure-dotnet#Microsoft_Azure_Management_Media_AssetsOperationsExtensions_ListStreamingLocators_Microsoft_Azure_Management_Media_IAssetsOperations_System_String_System_String_System_String_)|
|Java|[AssetStreamingLocator](https://docs.microsoft.com/java/api/com.microsoft.azure.management.mediaservices.v2018_07_01.assetstreaminglocator?view=azure-java-stable)|
|Node.js|[listStreamingLocators](https://docs.microsoft.com/javascript/api/@azure/arm-mediaservices/assets#liststreaminglocators-string--string--string--msrest-requestoptionsbase-)|

## <a name="also-see"></a>Zobacz też

* [Elementy zawartości](assets-concept.md)
* [Zasady przesyłania strumieniowego](streaming-policy-concept.md)
* [Zasady kluczy zawartości](content-key-policy-concept.md)

## <a name="next-steps"></a>Następne kroki

[Samouczek: przekazywanie, kodowanie i przesyłanie strumieniowe filmów wideo przy użyciu platformy .NET](stream-files-tutorial-with-api.md)
