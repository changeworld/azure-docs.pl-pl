---
title: Azure Media Services transkrypcję dynamiczną | Microsoft Docs
description: W tym artykule opisano, jak Azure Media Services transkrypcja dynamiczna.
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
ms.date: 08/27/2019
ms.author: juliako
ms.openlocfilehash: 98084b9bb6f19d22c7995d3044bb32ceaa947dc5
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74040426"
---
# <a name="live-transcription-preview"></a>Transkrypcja dynamiczna (wersja zapoznawcza)

Usługa Azure Media Service dostarcza wideo, audio i teraz tekst w różnych protokołach. Gdy publikujesz strumień na żywo przy użyciu formatu MPEG-KRESKowego lub HLS/CMAF, a następnie wideo i audio, Nasza usługa dostarczy tekst uzyskanego w IMSC 1.1 zgodne TTML, opakowany do formatu MPEG-4 część 30 (ISO/IEC 14496-30). W przypadku używania dostarczania za pośrednictwem HLS/TS, tekst jest dostarczany jako podzielony VTT. 

W tym artykule opisano sposób włączania transkrypcji na żywo podczas przesyłania strumieniowego zdarzenia na żywo z Azure Media Services v3. Przed kontynuowaniem upewnij się, że wiesz już, jak korzystać z interfejsów API REST usługi Media Services v3 (zobacz [ten samouczek](stream-files-tutorial-with-rest.md) , aby uzyskać szczegółowe informacje). Należy również zapoznać się z koncepcją [przesyłania strumieniowego na żywo](live-streaming-overview.md) . Zaleca się ukończenie [przesyłania strumieniowego na żywo za pomocą](stream-live-tutorial-with-api.md) samouczka Media Services. 

> [!NOTE]
> Obecnie transkrypcja na żywo jest dostępna tylko jako funkcja w wersji zapoznawczej w regionie zachodnie stany USA 2. Obsługuje transkrypcję wyrazów mówionych w języku angielskim do tekstu. Dokumentacja interfejsu API dla tej funkcji znajduje się w tym dokumencie — ponieważ jest w wersji zapoznawczej, szczegóły nie są dostępne w przypadku dokumentów REST. 

## <a name="creating-the-live-event"></a>Tworzenie wydarzenia na żywo 

Aby utworzyć wydarzenie na żywo, należy wysłać operację PUT do wersji 2019-05-01, na przykład: 

```
PUT https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/liveEvents/:liveEventName?api-version=2019-05-01-preview&autoStart=true 
```

Operacja ma następującą treść (w której jest tworzone zdarzenie typu pass-through na żywo z protokołem RTMP jako protokół pozyskiwania). Zwróć uwagę na dodanie właściwości transkrypcji. Jedyną dozwoloną wartością dla języka jest EN-US. 

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

Stan zdarzenia na żywo należy sondować do momentu, gdy zostanie on uruchomiony w stanie "uruchomiona", co oznacza, że można teraz wysyłać źródło danych RTMP. Teraz można wykonać kroki opisane w tym samouczku, takie jak sprawdzanie kanału informacyjnego w wersji zapoznawczej i tworzenie danych wyjściowych na żywo. 

## <a name="delivery-and-playback"></a>Dostarczanie i odtwarzanie 

Zapoznaj się z artykułem [Omówienie pakietów dynamicznych](dynamic-packaging-overview.md#to-prepare-your-source-files-for-delivery) , w jaki sposób usługa używa pakietu dynamicznego do dostarczania wideo, audio i teraz tekstu w różnych protokołach. Gdy publikujesz strumień na żywo przy użyciu formatu MPEG-KRESKowego lub HLS/CMAF, a następnie wideo i audio, Nasza usługa dostarczy tekst uzyskanego w IMSC 1.1 zgodne TTML, opakowany do formatu MPEG-4 część 30 (ISO/IEC 14496-30). W przypadku używania dostarczania za pośrednictwem HLS/TS, tekst jest dostarczany jako podzielony VTT. Aby odtworzyć strumień, można użyć odtwarzacza sieci Web, takiego jak [Azure Media Player](use-azure-media-player.md) .  

> [!NOTE]
>  Jeśli używasz Azure Media Player, użyj wersji 2.3.3 lub nowszej.

## <a name="known-issues"></a>Znane problemy 

W wersji zapoznawczej poniżej przedstawiono znane problemy związane z transkrypcją na żywo 

* Ta funkcja jest dostępna tylko w regionie zachodnie stany USA 2.
* Aplikacje muszą używać interfejsów API w wersji zapoznawczej, zgodnie z opisem w specyfikacji [specyfikacji Media Services v3 openapi](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/preview/2019-05-01-preview/streamingservice.json) .
* Jedynym obsługiwanym językiem jest angielski.
* W odniesieniu do ochrony zawartości obsługiwane jest tylko szyfrowanie kopert AES.

## <a name="next-steps"></a>Następne kroki

[Przegląd Media Services](media-services-overview.md)