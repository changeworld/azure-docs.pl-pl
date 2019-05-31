---
title: Przesyłanie strumieniowe lokalizatorów w usłudze Azure Media Services | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera opis Lokalizatory przesyłania strumieniowego są i jak są one używane przez usługi Azure Media Services.
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
ms.openlocfilehash: 5897b7df2460257784c40eb974c473573ec4003d
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/29/2019
ms.locfileid: "66299163"
---
# <a name="streaming-locators"></a>Lokalizatory przesyłania strumieniowego

Aby udostępnić klientom filmy wideo w wyjściowym elemencie zawartości w celu odtwarzania, trzeba utworzyć [lokalizator przesyłania strumieniowego](https://docs.microsoft.com/rest/api/media/streaminglocators) i skompilować adresy URL przesyłania strumieniowego. Aby utworzyć adres URL, musisz złączyć ścieżkę lokalizatora przesyłania strumieniowego i nazwę hosta punktu końcowego przesyłania strumieniowego. Aby uzyskać przykład platformy .NET, zobacz [Pobieranie lokalizatora przesyłania strumieniowego](stream-files-tutorial-with-api.md#get-a-streaming-locator).

Proces tworzenia **lokalizatora przesyłania strumieniowego** jest nazywany publikowaniem. Domyślnie **lokalizator przesyłania strumieniowego** jest ważny natychmiast po wykonaniu wywołań interfejsu API i aż do jego usunięcia, chyba że skonfigurujesz opcjonalne czasy rozpoczęcia i zakończenia. 

Podczas tworzenia **lokalizatora przesyłania strumieniowego**, należy określić **zasobów** nazwy i **przesyłania strumieniowego zasad** nazwy. Więcej informacji znajduje się w następujących tematach:

* [Zasoby](assets-concept.md)
* [Zasady przesyłania strumieniowego](streaming-policy-concept.md)
* [Zasady kluczy zawartości](content-key-policy-concept.md)

Można również określić godziny rozpoczęcia i zakończenia w usługi lokalizatora przesyłania strumieniowego, który tylko umożliwi użytkownikowi odtwarzanie zawartości między tymi godzinami (na przykład między 5/1/2019 r do 2019-5/5).  

## <a name="considerations"></a>Zagadnienia do rozważenia

* **Lokalizatory przesyłania strumieniowego** nie są aktualizowalne. 
* Właściwości **Lokalizatory przesyłania strumieniowego** będące daty/godziny są zawsze w formacie UTC.
* Należy zaprojektować ograniczony zestaw zasad dla swojego konta usługi multimediów i ponownie ich użyć dla Twojego Lokalizatory przesyłania strumieniowego w każdym przypadku, gdy potrzebne są te same opcje. Aby uzyskać więcej informacji, zobacz [przydziały i ograniczenia](limits-quotas-constraints.md).

## <a name="create-streaming-locators"></a>Utworzenia lokalizatorów przesyłania strumieniowego  

### <a name="not-encrypted"></a>Niezaszyfrowane

Jeśli chcesz przesyłać strumieniowo Twojego pliku w Wyczyść (niezaszyfrowane), zestawu wstępnie zdefiniowanych zasad przesyłania strumieniowego wyczyść: do "Predefined_ClearStreamingOnly" (na platformie .NET, możesz użyć wyliczenia PredefinedStreamingPolicy.ClearStreamingOnly).

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

Jeśli zachodzi potrzeba szyfrowanie zawartości przy użyciu szyfrowania CENC, ustawić zasady "Predefined_MultiDrmCencStreaming". Szyfrowanie Widevine, będą stosowane do strumienia DASH i technologii PlayReady do sprawnego. Klucz zostaną dostarczone do klienta odtwarzania, oparte na skonfigurowanym licencji DRM.

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

Jeśli chcesz również zaszyfrować strumienia HLS z CBCS (FairPlay), należy użyć "Predefined_MultiDrmStreaming".

## <a name="associate-filters-with-streaming-locators"></a>Kojarzenie filtrów z Lokalizatory przesyłania strumieniowego

Zobacz [filtry: skojarzyć z Lokalizatory przesyłania strumieniowego](filters-concept.md#associating-filters-with-streaming-locator).

## <a name="filter-order-page-streaming-locator-entities"></a>Filtr, zamówienia, strona lokalizatora przesyłania strumieniowego jednostek

Zobacz [filtrowanie, porządkowanie, stronicowanie jednostek usługi Media Services](entities-overview.md).

## <a name="list-streaming-locators-by-asset-name"></a>Lokalizatory przesyłania strumieniowego listę według nazwy zasobu

Aby uzyskać Lokalizatory przesyłania strumieniowego, na podstawie skojarzone nazwy zasobów, użyj następujących operacji:

|Język|Interfejs API|
|---|---|
|REST|[liststreaminglocators](https://docs.microsoft.com/rest/api/media/assets/liststreaminglocators)|
|Interfejs wiersza polecenia|[az ams asset list-streaming-locators](https://docs.microsoft.com/cli/azure/ams/asset?view=azure-cli-latest#az-ams-asset-list-streaming-locators)|
|.NET|[ListStreamingLocators](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.assetsoperationsextensions.liststreaminglocators?view=azure-dotnet#Microsoft_Azure_Management_Media_AssetsOperationsExtensions_ListStreamingLocators_Microsoft_Azure_Management_Media_IAssetsOperations_System_String_System_String_System_String_)|
|Java|[AssetStreamingLocator](https://docs.microsoft.com/java/api/com.microsoft.azure.management.mediaservices.v2018_07_01.assetstreaminglocator?view=azure-java-stable)|
|Node.js|[listStreamingLocators](https://docs.microsoft.com/javascript/api/azure-arm-mediaservices/assets?view=azure-node-latest#liststreaminglocators-string--string--string--object-)|

## <a name="also-see"></a>Zobacz też

* [Zasoby](assets-concept.md)
* [Zasady przesyłania strumieniowego](streaming-policy-concept.md)
* [Zasady kluczy zawartości](content-key-policy-concept.md)

## <a name="next-steps"></a>Kolejne kroki

[Samouczek: Przekazywanie, kodowanie i przesyłanie strumieniowe wideo za pomocą platformy .NET](stream-files-tutorial-with-api.md)
