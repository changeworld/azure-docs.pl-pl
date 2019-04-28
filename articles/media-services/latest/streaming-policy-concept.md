---
title: Strumieniowe przesyłanie zasad w usłudze Azure Media Services | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera wyjaśnienie, co to są zasady przesyłania strumieniowego i jak są one używane przez usługi Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/03/2019
ms.author: juliako
ms.openlocfilehash: 10600d8f3ff4e08b8d90f28ec15d3cb0c56bcae0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61230900"
---
# <a name="streaming-policies"></a>Zasady przesyłania strumieniowego

W usłudze Azure Media Services v3 [przesyłania strumieniowego zasady](https://docs.microsoft.com/rest/api/media/streamingpolicies) umożliwiają definiowanie protokołów przesyłania strumieniowego i opcje szyfrowania dla Twojego [Lokalizatory przesyłania strumieniowego](streaming-locators-concept.md). Można użyć jednej z wstępnie zdefiniowane zasady przesyłania strumieniowego lub utworzenia niestandardowych zasad. Wstępnie zdefiniowane zasady przesyłania strumieniowego obecnie dostępne są: "Predefined_DownloadOnly", "Predefined_ClearStreamingOnly", "Predefined_DownloadAndClearStreaming", "Predefined_ClearKey", "Predefined_MultiDrmCencStreaming" i "Predefined_MultiDrmStreaming".

> [!IMPORTANT]
> * Właściwości **przesyłania strumieniowego zasady** będące daty/godziny są zawsze w formacie UTC.
> * Należy zaprojektować ograniczony zestaw zasad dla swojego konta usługi multimediów i ponownie ich użyć dla Twojego Lokalizatory przesyłania strumieniowego w każdym przypadku, gdy potrzebne są te same opcje. 

## <a name="examples"></a>Przykłady

### <a name="not-encrypted"></a>Niezaszyfrowane

Jeśli chcesz przesyłać strumieniowo Twojego pliku w Wyczyść (niezaszyfrowane), zestawu wstępnie zdefiniowanych zasad przesyłania strumieniowego wyczyść: do "Predefined_ClearStreamingOnly" (na platformie .NET, możesz użyć PredefinedStreamingPolicy.ClearStreamingOnly).

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

Jeśli zachodzi potrzeba szyfrowanie zawartości przy użyciu szyfrowania koperty i cenc, ustawić zasady "Predefined_MultiDrmCencStreaming". Te zasady wskazują, że chcesz wygenerować dwa klucze zawartości (koperta i CENC) i ustawić je w lokalizatorze. Dlatego stosowane są szyfrowania typu koperta, PlayReady i Widevine (klucz jest dostarczany do klienta odtwarzania w oparciu o skonfigurowane licencje DRM).

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

Jeśli chcesz również zaszyfrować strumienia z CBCS (FairPlay), należy użyć "Predefined_MultiDrmStreaming".

## <a name="filtering-ordering-paging"></a>Filtrowania, sortowania, stronicowania

Zobacz [filtrowanie, porządkowanie, stronicowanie jednostek usługi Media Services](entities-overview.md).

## <a name="next-steps"></a>Kolejne kroki

* [Strumieniowe przesyłanie pliku](stream-files-dotnet-quickstart.md)
* [Używanie dynamicznego szyfrowania AES-128 i usługi dostarczania kluczy](protect-with-aes128.md)
* [Użyj DRM dynamiczne szyfrowanie i licencji usługi dostarczania](protect-with-drm.md)
