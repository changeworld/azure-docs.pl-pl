---
title: Transkrypcja na żywo
titleSuffix: Azure Media Services
description: Dowiedz się więcej o transkrypcji na żywo usługi Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 11/19/2019
ms.author: juliako
ms.openlocfilehash: b364b6e70e3b5723c483bc3435f0c3a152c03aa9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79499875"
---
# <a name="live-transcription-preview"></a>Transkrypcja na żywo (wersja zapoznawcza)

Usługa Azure Media Service dostarcza wideo, audio i tekst w różnych protokołach. Po opublikowaniu transmisji na żywo za pomocą MPEG-DASH lub HLS/CMAF, a następnie wraz z wideo i audio, nasz serwis dostarcza transkrybowany tekst w TTML kompatybilny z IMSC1.1. Dostawa jest pakowany do MPEG-4 Część 30 (ISO/IEC 14496-30) fragmenty. Jeśli używasz dostarczania za pośrednictwem HLS/TS, tekst jest dostarczany jako fragmenty VTT.

W tym artykule opisano, jak włączyć transkrypcję na żywo podczas przesyłania strumieniowego zdarzenia na żywo za pomocą usługi Azure Media Services w wersji 3. Przed kontynuowaniem upewnij się, że znasz korzystanie z interfejsów API REST usługi Media Services w wersji 3 (szczegółowe informacje można znaleźć w [tym samouczku).](stream-files-tutorial-with-rest.md) Należy również zapoznać się z koncepcją [transmisji na żywo.](live-streaming-overview.md) Zaleca się ukończenie samouczka [Strumień na żywo za pomocą usługi Media Services.](stream-live-tutorial-with-api.md)

> [!NOTE]
> Obecnie transkrypcja na żywo jest dostępna tylko jako funkcja podglądu w regionie Zachodnie stany USA 2. Obsługuje transkrypcję mówionych słów w języku angielskim do tekstu. Odwołanie do interfejsu API dla tej funkcji znajduje się poniżej — oto w wersji zapoznawczej szczegóły nie są dostępne w naszych dokumentach REST.

## <a name="creating-the-live-event"></a>Tworzenie wydarzenia na żywo

Aby utworzyć zdarzenie na żywo, należy wysłać put operacji do wersji 2019-05-01-preview, na przykład:

```
PUT https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/liveEvents/:liveEventName?api-version=2019-05-01-preview&autoStart=true 
```

Operacja ma następującą treść (gdzie przechodziowe zdarzenie na żywo jest tworzony z RTMP jako protokół pozyskiwania). Należy zwrócić uwagę na dodanie właściwości transkrypcji. Jedyną dozwoloną wartością języka jest en-US.

```
{ 
  "properties": { 
    "description": "Demonstrate how to enable live transcriptions", 
    "input": { 
      "streamingProtocol": "RTMP", 
      "accessControl": { 
        "ip": { 
          "allow": [ 
            { 
              "name": "Allow All", 
              "address": "0.0.0.0", 
              "subnetPrefixLength": 0 
            } 
          ] 
        } 
      } 
    }, 
    "preview": { 
      "accessControl": { 
        "ip": { 
          "allow": [ 
            { 
              "name": "Allow All", 
              "address": "0.0.0.0", 
              "subnetPrefixLength": 0 
            } 
          ] 
        } 
      } 
    }, 
    "encoding": { 
      "encodingType": "None" 
    }, 
    "transcriptions": [ 
      { 
        "language": "en-US" 
      } 
    ], 
    "vanityUrl": false, 
    "streamOptions": [ 
      "Default" 
    ] 
  }, 
  "location": "West US 2" 
} 
```

Sonduj stan zdarzenia na żywo, aż przejdzie do stanu "Uruchomiony", co oznacza, że możesz teraz wysłać kanał RTMP. Teraz można wykonać te same kroki, jak w tym samouczku, jak sprawdzanie kanału w wersji zapoznawczej i tworzenie wyjść na żywo.

## <a name="transcription-delivery-and-playback"></a>Transkrypcja dostawy i odtwarzania

Zapoznaj się z [omówieniem dynamicznego pakowania,](dynamic-packaging-overview.md#to-prepare-your-source-files-for-delivery) w jaki sposób nasza usługa wykorzystuje dynamiczne opakowania do dostarczania wideo, audio i tekstu w różnych protokołach. Po opublikowaniu transmisji na żywo za pomocą MPEG-DASH lub HLS/CMAF, a następnie wraz z wideo i audio, nasz serwis dostarcza transkrybowany tekst w TTML kompatybilny z IMSC1.1. Ta dostawa jest pakowany do MPEG-4 Część 30 (ISO/IEC 14496-30) fragmenty. Jeśli używasz dostarczania za pośrednictwem HLS/TS, tekst jest dostarczany jako fragmentowany VTT. Do odtwarzania strumienia można użyć odtwarzacza sieci web, takiego jak [Azure Media Player.](use-azure-media-player.md)  

> [!NOTE]
> Jeśli używasz programu Azure Media Player, należy użyć wersji 2.3.3 lub nowszej.

## <a name="known-issues"></a>Znane problemy

W przypadku wersji zapoznawczej znane są następujące problemy z transkrypcją na żywo:

* Ta funkcja jest dostępna tylko w zachodnie stany USA 2.
* Aplikacje muszą używać interfejsów API w wersji zapoznawczej opisanej w [specyfikacji OpenAPI usługi Media Services](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/preview/2019-05-01-preview/streamingservice.json)w wersji 3 .
* Jedynym obsługiwanym językiem jest angielski (en-us).
* W ochronie zawartości obsługiwane jest tylko szyfrowanie kopert AES.

## <a name="next-steps"></a>Następne kroki

* [Omówienie usługi Media Services](media-services-overview.md)
