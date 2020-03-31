---
title: Kody błędów zdarzeń na żywo usługi Azure Media Services | Dokumenty firmy Microsoft
description: W tym artykule wymieniono kody błędów zdarzeń na żywo.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/25/2020
ms.author: juliako
ms.openlocfilehash: f9067562f67190b8bc04392f33078d4d3262f986
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77654583"
---
# <a name="media-services-live-event-error-codes"></a>Kody błędów zdarzeń na żywo usług Media Services

Tabele w tej sekcji wyświetlają kody błędów [zdarzenia na żywo.](live-events-outputs-concept.md)

## <a name="liveeventconnectionrejected"></a>LiveEventConnectionWysunięty

Podczas subskrybowania zdarzeń [w usztywnieniu](https://docs.microsoft.com/azure/event-grid/) dla zdarzenia na żywo, może zostać wyświetlony jeden z następujących błędów ze zdarzenia [LiveEventConnectionWysuczone.](media-services-event-schemas.md#liveeventconnectionrejected)

| Kod wyniku | Opis |
| ----------- | ----------- |
| MPE_RTMP_APPID_AUTH_FAILURE | Nieprawidłowy adres URL pozyskiwania |
| MPE_INGEST_ENCODER_CONNECTION_DENIED | Koder IP nie jest obecny na liście dozwolonych ip skonfigurowany |
| MPE_INGEST_RTMP_SETDATAFRAME_NOT_RECEIVED | Koder RTMP nie wysłał polecenia setDataFrame. |
| MPE_INGEST_CODEC_NOT_SUPPORTED | Określony kodek nie jest obsługiwany. |
| MPE_INGEST_DESCRIPTION_INFO_NOT_RECEIVED |Informacje o opisie mediów nie zostały odebrane przed dostarczeniem rzeczywistych danych multimedialnych.|
| MPE_INGEST_MEDIA_QUALITIES_EXCEEDED |Liczba jakości dla typu audio lub wideo przekroczyła maksymalny dozwolony limit.|
| MPE_INGEST_BITRATE_AGGREGATED_EXCEEDED |Całkowita przychodząca szybkość transmisji bitów w usłudze wydarzenia lub kanału na żywo przekroczyła maksymalny dozwolony limit.|
| MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID | Sygnatura czasowa dla flvtagu audio lub audio jest nieprawidłowa od kodera RTMP. |
| MPE_INGEST_FRAMERATE_EXCEEDED | Przychodzących koder połkniętych strumieni ze szybkością klatek na sekundę przekroczyła maksymalną dozwoloną 30 klatek na sekundę do kodowania wydarzeń na żywo / kanałów.|
| MPE_INGEST_VIDEO_RESOLUTION_NOT_SUPPORTED | Przychodzących cekoder pozyskanych strumieni przekroczył następujące dozwolone rozdzielczości: 1920x1088 do kodowania wydarzeń na żywo /kanałów i 4096 x 2160 dla pass-through wydarzeń na żywo / kanałów.|

## <a name="liveeventencoderdisconnected"></a>LiveEventEncoderRozłączne

Może pojawić się jeden z następujących błędów ze zdarzenia [LiveEventEncoderDisconnected.](media-services-event-schemas.md#liveeventencoderdisconnected)

|Kod wyniku|Opis|
|---|---|
|MPE_RTMP_SESSION_IDLE_TIMEOUT|Limit czasu sesji RTMP został przekroczony po bezczynności dla dozwolonego limitu czasu.|
|MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID|Sygnatura czasowa dla flvtagu audio lub audio jest nieprawidłowa od kodera RTMP.|
|MPE_CAPACITY_LIMIT_REACHED|Koder wysyła dane zbyt szybko.|
|Nieznane kody błędów|Te kody błędów mogą wahać się od błędu pamięci do zduplikowanych wpisów na mapie mieszania.|


## <a name="see-also"></a>Zobacz też

[Kody błędów punktu końcowego przesyłania strumieniowego (Origin)](streaming-endpoint-error-codes.md)

## <a name="next-steps"></a>Następne kroki

[Samouczek: Przesyłanie strumieniowe na żywo za pomocą usług multimedialnych](stream-live-tutorial-with-api.md)
