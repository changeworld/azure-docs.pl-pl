---
title: Ustawienia małych opóźnień liveEvent w usłudze Azure Media Services | Dokumenty firmy Microsoft
description: W tym temacie przedstawiono omówienie ustawień małych opóźnień LiveEvent i pokazano, jak ustawić małe opóźnienia.
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
ms.date: 04/22/2019
ms.author: juliako
ms.openlocfilehash: a82a0644fac099b568ab86ea213b98cd8e7d5c22
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78199652"
---
# <a name="live-event-low-latency-settings"></a>Ustawienia małych opóźnień zdarzenia na żywo

W tym artykule pokazano, jak ustawić małe opóźnienia w [wydarzeniu](https://docs.microsoft.com/rest/api/media/liveevents)na żywo . Omówiono również typowe wyniki, które można zobaczyć podczas korzystania z ustawień małych opóźnień w różnych odtwarzaczach. Wyniki różnią się w zależności od sieci CDN i opóźnienia sieci.

Aby użyć nowej funkcji **LowLatency,** należy ustawić **StreamOptionsFlag** na **LowLatency** na **LiveEvent**. Podczas tworzenia [liveoutput](https://docs.microsoft.com/rest/api/media/liveoutputs) dla odtwarzania HLS, ustaw [LiveOutput.Hls.fragmentsPerTsSegment](https://docs.microsoft.com/rest/api/media/liveoutputs/create#hls) do 1. Po uruchomieniu strumienia można użyć [usługi Azure Media Player](https://ampdemo.azureedge.net/) (strona demonstracyjna AMP) i ustawić opcje odtwarzania, aby używać "profilu heurystyki o niskim opóźnieniu".

> [!NOTE]
> Obecnie LowLatency HeuristicProfile w usłudze Azure Media Player jest przeznaczony do odtwarzania strumieni w protokole MPEG-DASH, w formacie CSF lub CMAF (na przykład `format=mdp-time-csf` lub `format=mdp-time-cmaf`). 

W poniższym przykładzie .NET pokazano, jak ustawić **LowLatency** na **LiveEvent:**

```csharp
LiveEvent liveEvent = new LiveEvent(
            location: mediaService.Location, 
            description: "Sample LiveEvent for testing",
            vanityUrl: false,
            encoding: new LiveEventEncoding(
                        // Set this to Standard to enable a transcoding LiveEvent, and None to enable a pass-through LiveEvent
                        encodingType:LiveEventEncodingType.None, 
                        presetName:null
                    ),
            input: new LiveEventInput(LiveEventInputProtocol.RTMP,liveEventInputAccess), 
            preview: liveEventPreview,
            streamOptions: new List<StreamOptionsFlag?>()
            {
                // Set this to Default or Low Latency
                // To use low latency optimally, you should tune your encoder settings down to 1 second "Group Of Pictures" (GOP) length instead of 2 seconds.
                StreamOptionsFlag.LowLatency
            }
        );
```                

Zobacz pełny przykład: [MediaV3LiveApp](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs#L126).

## <a name="live-events-latency"></a>Opóźnienie wydarzeń na żywo

W poniższych tabelach przedstawiono typowe wyniki opóźnienia (gdy flaga LowLatency jest włączona) w usłudze Media Services, mierzona od momentu, gdy kanał wkładu osiągnie usługę do momentu, gdy widz zobaczy odtwarzanie na odtwarzaczu. Aby optymalnie korzystać z małych opóźnień, należy dostroić ustawienia kodera do 1 sekundy długości "Grupy obrazów" (GOP). Korzystając z wyższej długości GOP, można zminimalizować zużycie przepustowości i zmniejszyć szybkość transmisji bitów w tej samej szybkości klatek. Jest to szczególnie korzystne w filmach o mniejszym ruchu.

### <a name="pass-through"></a>Przekazywanie 

||2s GOP niskie opóźnienie włączone|1s GOP niskie opóźnienie włączone|
|---|---|---|
|DASH w AMP|10s|8s|
|HLS na natywnym odtwarzaczu iOS|14 s|10s|

### <a name="live-encoding"></a>Kodowanie na żywo

||2s GOP niskie opóźnienie włączone|1s GOP niskie opóźnienie włączone|
|---|---|---|
|DASH w AMP|14 s|10s|
|HLS na natywnym odtwarzaczu iOS|18s|13s|

> [!NOTE]
> Opóźnienie end-to-end może się różnić w zależności od warunków sieci lokalnej lub wprowadzenie warstwy buforowania sieci CDN. Należy przetestować dokładne konfiguracje.

## <a name="next-steps"></a>Następne kroki

- [Omówienie transmisji na żywo](live-streaming-overview.md)
- [Samouczek przesyłania strumieniowego na żywo](stream-live-tutorial-with-api.md)

