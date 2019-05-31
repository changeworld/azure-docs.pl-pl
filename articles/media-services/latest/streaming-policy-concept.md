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
ms.date: 05/28/2019
ms.author: juliako
ms.openlocfilehash: a813c77e81e51bfe13e75ed6c8d0e24b4d0fa645
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/30/2019
ms.locfileid: "66392924"
---
# <a name="streaming-policies"></a>Zasady przesyłania strumieniowego

W usłudze Azure Media Services v3 [przesyłania strumieniowego zasady](https://docs.microsoft.com/rest/api/media/streamingpolicies) umożliwiają definiowanie protokołów przesyłania strumieniowego i opcje szyfrowania dla Twojego [Lokalizatory przesyłania strumieniowego](streaming-locators-concept.md). Usługa Media Services v3 zawiera kilka wstępnie zdefiniowanych zasad przesyłania strumieniowego, aby można je bezpośrednio do wersji próbnej lub produkcji. 

Obecnie dostępne wstępnie zdefiniowane zasady przesyłania strumieniowego:<br/>
* 'Predefined_DownloadOnly'
* 'Predefined_ClearStreamingOnly'
* "Predefined_DownloadAndClearStreaming"
* 'Predefined_ClearKey'
* 'Predefined_MultiDrmCencStreaming' 
* "Predefined_MultiDrmStreaming"

Następujące "drzewa decyzyjnego" pomaga w wyborze wstępnie zdefiniowanych zasad przesyłania strumieniowego dla danego scenariusza.

> [!IMPORTANT]
> * Właściwości **przesyłania strumieniowego zasady** będące daty/godziny są zawsze w formacie UTC.
> * Należy zaprojektować ograniczony zestaw zasad dla swojego konta usługi multimediów i ponownie ich użyć dla Twojego Lokalizatory przesyłania strumieniowego w każdym przypadku, gdy potrzebne są te same opcje. Aby uzyskać więcej informacji, zobacz [przydziały i ograniczenia](limits-quotas-constraints.md).

## <a name="decision-tree"></a>Drzewo decyzyjne

Kliknij obraz, aby go wyświetlić w pełnym rozmiarze.  

<a href="./media/streaming-policy/large.png" target="_blank"><img src="./media/streaming-policy/large.png"></a> 

Jeśli szyfrowanie zawartości, należy utworzyć [zasad klucza zawartości](content-key-policy-concept.md), **zasad klucza zawartości** nie jest potrzebna do zwykłego przesyłania strumieniowego lub pobierania. 

Jeśli masz specjalnych wymagań (na przykład, jeśli chcesz określić różnych protokołów, należy użyć usługi dostarczania kluczy niestandardowych lub należy użyć ścieżki wyczyść audio), możesz to zrobić [tworzenie](https://docs.microsoft.com/rest/api/media/streamingpolicies/create) niestandardowe zasady przesyłania strumieniowego. 

## <a name="get-a-streaming-policy-definition"></a>Pobierz definicję zasad do przesyłania strumieniowego  

Jeśli chcesz zobaczyć definicję zasad z przesyłania strumieniowego, użyj [uzyskać](https://docs.microsoft.com/rest/api/media/streamingpolicies/get) i określ nazwę zasad. Na przykład:

### <a name="rest"></a>REST

Żądanie:

```
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Media/mediaServices/contosomedia/streamingPolicies/clearStreamingPolicy?api-version=2018-07-01
```

Odpowiedź:

```
{
  "name": "clearStreamingPolicy",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Media/mediaservices/contosomedia/streamingPolicies/clearStreamingPolicy",
  "type": "Microsoft.Media/mediaservices/streamingPolicies",
  "properties": {
    "created": "2018-08-08T18:29:30.8501486Z",
    "noEncryption": {
      "enabledProtocols": {
        "download": true,
        "dash": true,
        "hls": true,
        "smoothStreaming": true
      }
    }
  }
}
```

## <a name="filtering-ordering-paging"></a>Filtrowania, sortowania, stronicowania

Zobacz [filtrowanie, porządkowanie, stronicowanie jednostek usługi Media Services](entities-overview.md).

## <a name="next-steps"></a>Kolejne kroki

* [Strumieniowe przesyłanie pliku](stream-files-dotnet-quickstart.md)
* [Dynamiczne szyfrowanie AES-128 i usługę dostarczania kluczy](protect-with-aes128.md)
* [Użyj DRM dynamiczne szyfrowanie i licencji usługi dostarczania](protect-with-drm.md)
