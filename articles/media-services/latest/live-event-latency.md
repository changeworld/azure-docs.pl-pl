---
title: Element LiveEvent opóźnienia w usłudze Azure Media Services | Dokumentacja firmy Microsoft
description: W tym temacie ogólny czas oczekiwania na element LiveEvent i przedstawiono sposób ustawiania małymi opóźnieniami.
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
ms.openlocfilehash: 393b87aeed759950b946ccb45a008da9af4b7ebe
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2019
ms.locfileid: "63766841"
---
# <a name="live-event-latency-in-media-services"></a>Czas oczekiwania na zdarzenie na żywo w usłudze Media Services

W tym artykule przedstawiono sposób ustawiania małe opóźnienia na [wydarzenie na żywo](https://docs.microsoft.com/rest/api/media/liveevents). Omawia także typowe wyniki, które podczas przy użyciu ustawień małe opóźnienia w różne odtwarzacze są wyświetlane. Wyniki różnią się zależnie od opóźnienia sieci i usługi CDN.

Aby użyć nowego **LowLatency** funkcji, możesz ustawić **StreamOptionsFlag** do **LowLatency** na **element LiveEvent**. Podczas tworzenia [LiveOutput](https://docs.microsoft.com/rest/api/media/liveoutputs) odtwarzania HLS, ustaw [LiveOutput.Hls.fragmentsPerTsSegment](https://docs.microsoft.com/rest/api/media/liveoutputs/create#hls) 1. Gdy strumień jest uruchomiona, możesz użyć [usługi Azure Media Player](https://ampdemo.azureedge.net/) (AMP pokaz strony) i ustaw opcje odtwarzania, aby używać "Niskie opóźnienie Algorytm heurystyczny profil".

> [!NOTE]
> Obecnie LowLatency HeuristicProfile w usłudze Azure Media Player jest przeznaczona do odtwarzania strumieni w protokole MPEG-DASH w formacie CSF lub CMAF (na przykład `format=mdp-time-csf` lub `format=mdp-time-cmaf`). 

W poniższym przykładzie .NET pokazuje, jak ustawić **LowLatency** na **element LiveEvent**:

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

## <a name="live-events-latency"></a>Czas oczekiwania na zdarzenia na żywo

W poniższej tabeli przedstawiono typowe wyniki opóźnienia (jeśli jest włączona Flaga LowLatency) w usłudze Media Services, zmierzony od momentu kanału informacyjnego wkład osiągnie usługi, gdy przeglądarka widzi odtwarzanie na odtwarzaczu. Aby użyć optymalnie małe opóźnienia, powinien Dostosowywanie ustawień kodera do 1 sekunda "Grupa z obrazów" (GOP) długości. Przy użyciu nowszej długości GOP, możesz zminimalizować użycie przepustowości i zmniejszenie szybkości transmisji bitów w ramach tej samej szybkość klatek. Jest szczególnie korzystne w filmach wideo z mniej ruchu.

### <a name="pass-through"></a>Przekazywanie 

||2S GOP krótki czas oczekiwania, włączone|1s GOP krótki czas oczekiwania, włączone|
|---|---|---|
|KRESKI w AMP|10s|8S|
|HLS na odtwarzaczu natywnych dla systemów iOS|14S|10s|

### <a name="live-encoding"></a>Kodowanie na żywo

||2S GOP krótki czas oczekiwania, włączone|1s GOP krótki czas oczekiwania, włączone|
|---|---|---|
|KRESKI w AMP|14S|10s|
|HLS na odtwarzaczu natywnych dla systemów iOS|18s|13s|

> [!NOTE]
> Opóźnienie end-to-end mogą się różnić w zależności od warunków sieci lokalnej lub wprowadzając warstwy buforowania usługi CDN. Należy przetestować dokładnie konfiguracje.

## <a name="next-steps"></a>Kolejne kroki

- [Omówienie transmisji strumieniowej na żywo](live-streaming-overview.md)
- [Samouczek transmisji strumieniowej na żywo](stream-live-tutorial-with-api.md)

