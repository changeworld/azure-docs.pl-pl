---
title: Zasady przesyłania strumieniowego w usługach Azure Media Services | Dokumenty firmy Microsoft
description: W tym artykule wyjaśniono, jakie są zasady przesyłania strumieniowego i jak są one używane przez usługę Azure Media Services.
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
ms.openlocfilehash: 9c80056fd62173ff1e5a6ed3979adba71b7706cc
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80582757"
---
# <a name="streaming-policies"></a>Zasady przesyłania strumieniowego

W usłudze Azure Media Services w wersji 3 [zasady przesyłania strumieniowego](https://docs.microsoft.com/rest/api/media/streamingpolicies) umożliwiają definiowanie protokołów przesyłania strumieniowego i opcji szyfrowania dla [lokalizatorów przesyłania strumieniowego](streaming-locators-concept.md). Usługa Media Services w wersji 3 udostępnia niektóre wstępnie zdefiniowane zasady przesyłania strumieniowego, dzięki czemu można ich używać bezpośrednio do wersji próbnej lub produkcji. 

Aktualnie dostępne wstępnie zdefiniowane zasady przesyłania strumieniowego:<br/>
* "Predefined_DownloadOnly"
* "Predefined_ClearStreamingOnly"
* "Predefined_DownloadAndClearStreaming"
* "Predefined_ClearKey"
* "Predefined_MultiDrmCencStreaming" 
* "Predefined_MultiDrmStreaming"

Następujące "Drzewo decyzji" pomaga wybrać wstępnie zdefiniowane zasady przesyłania strumieniowego dla scenariusza.

> [!IMPORTANT]
> * Właściwości **zasad przesyłania strumieniowego,** które są typu Datetime są zawsze w formacie UTC.
> * Należy zaprojektować ograniczony zestaw zasad dla konta usługi Media Service i użyć ich ponownie dla lokalizatorów przesyłania strumieniowego, gdy są potrzebne te same opcje. Aby uzyskać więcej informacji, zobacz [Przydziały i limity](limits-quotas-constraints.md).

## <a name="decision-tree"></a>Drzewo decyzyjne

Kliknij obraz, aby go wyświetlić w pełnym rozmiarze.  

<a href="./media/streaming-policy/large.png" target="_blank"><img src="./media/streaming-policy/large.png"></a> 

W przypadku szyfrowania zawartości należy utworzyć [zasady dotyczące kluczy zawartości](content-key-policy-concept.md), zasady dotyczące **kluczy zawartości** nie są potrzebne do wyczyszczenie przesyłania strumieniowego lub pobierania. 

Jeśli masz specjalne wymagania (na przykład, jeśli chcesz określić różne protokoły, musisz użyć niestandardowej usługi dostarczania kluczy lub musisz użyć wyraźnej ścieżki dźwiękowej), możesz [utworzyć](https://docs.microsoft.com/rest/api/media/streamingpolicies/create) niestandardowe zasady przesyłania strumieniowego. 

## <a name="get-a-streaming-policy-definition"></a>Uzyskaj definicję zasad przesyłania strumieniowego  

Jeśli chcesz zobaczyć definicję zasad przesyłania strumieniowego, użyj [pobierz](https://docs.microsoft.com/rest/api/media/streamingpolicies/get) i określ nazwę zasad. Przykład:

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

## <a name="filtering-ordering-paging"></a>Filtrowanie, zamawianie, stronicowanie

Zobacz [Filtrowanie, zamawianie, stronicowanie jednostek usługi Media Services](entities-overview.md).

## <a name="next-steps"></a>Następne kroki

* [Strumieniowe przesyłanie pliku](stream-files-dotnet-quickstart.md)
* [Używanie dynamicznego szyfrowania AES-128 i usługi dostarczania kluczy](protect-with-aes128.md)
* [Używanie usługi dostarczania licencji i szyfrowania dynamicznego w technologii DRM](protect-with-drm.md)
