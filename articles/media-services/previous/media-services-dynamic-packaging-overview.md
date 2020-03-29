---
title: Omówienie dynamicznego pakowania usługi Azure Media Services | Dokumenty firmy Microsoft
description: W tym artykułach przedstawiono omówienie dynamicznego pakowania usług Microsoft Azure Media Services.
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
ms.date: 03/21/2019
ms.author: juliako
ms.openlocfilehash: 079094965775c140c0343da98e40fd008995d45a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74901186"
---
# <a name="dynamic-packaging"></a>Dynamiczne tworzenie pakietów

> [!div class="op_single_selector" title1="Wybierz używana wersja usługi Media Services:"]
> * [Wersja 3](../latest/dynamic-packaging-overview.md)
> * [Wersja 2](media-services-dynamic-packaging-overview.md)

> [!NOTE]
> Do usługi Media Services w wersji 2 nie są już dodawane żadne nowe funkcje. <br/>Sprawdź najnowszą wersję usługi [Media Services w wersji 3](https://docs.microsoft.com/azure/media-services/latest/). Zobacz też [wskazówki dotyczące migracji z wersji 2 do v3](../latest/migrate-from-v2-to-v3.md)

Usługi Microsoft Azure Media Services mogą służyć do dostarczania wielu formatów plików źródłowych multimediów, formatów przesyłania strumieniowego multimediów i formatów ochrony zawartości do różnych technologii klienckich (na przykład iOS, XBOX, Silverlight, Windows 8). Ci klienci rozumieją różne protokoły, na przykład iOS wymaga formatu HTTP Live Streaming (HLS) V4, a silverlight i xbox wymagają płynnego przesyłania strumieniowego. Jeśli masz zestaw adaptacyjnych plików MP4 (ISO Base Media 14496-12) lub zestaw adaptacyjnych plików płynnego przesyłania strumieniowego o szybkości transmisji bitów, które chcesz obsługiwać klientom, którzy rozumieją MPEG DASH, HLS lub Smooth Streaming, powinieneś skorzystać z multimediów Usługi dynamiczne opakowania.

Dzięki dynamicznemu pakowaniu wystarczy utworzyć zasób zawierający zestaw adaptacyjnych plików MP4 o szybkości transmisji bitów lub adaptacyjnych plików Smooth Streaming o szybkości transmisji bitów. Następnie, na podstawie określonego formatu w żądaniu manifestu lub fragmentu, serwer przesyłania strumieniowego na żądanie zapewni, że otrzymasz strumień w wybranym protokole. Dzięki temu wystarczy przechowywać i opłacać pliki w jednym formacie magazynu, a usługa Media Services utworzy oraz udostępni właściwą odpowiedź na podstawie żądań klienta.

Na poniższym diagramie przedstawiono tradycyjny kodowanie i statyczny przepływ pracy pakowania.

![Kodowanie statyczne](./media/media-services-dynamic-packaging-overview/media-services-static-packaging.png)

Na poniższym diagramie przedstawiono dynamiczny przepływ pracy pakowania.

![Kodowanie dynamiczne](./media/media-services-dynamic-packaging-overview/media-services-dynamic-packaging.png)

## <a name="common-scenario"></a>Typowy scenariusz

1. Przekaż plik wejściowy (nazywany plikiem antresoli). Na przykład H.264, MP4 lub WMV (lista obsługiwanych formatów znajduje się [w formatach obsługiwanych przez standard kodera multimediów](media-services-media-encoder-standard-formats.md).
2. Zakoduj plik mezzanine do adaptacyjnych zestawów bitrate H.264 MP4.
3. Opublikuj zasób zawierający adaptacyjny zestaw mp4 szybkości transmisji bitów, tworząc lokalizator na żądanie.
4. Twórz strumieniowe adresy URL, aby uzyskiwać dostęp do zawartości i przesyłać strumieniowo je strumieniowo.

## <a name="preparing-assets-for-dynamic-streaming"></a>Przygotowanie zasobów do dynamicznego przesyłania strumieniowego

Aby przygotować zasób do dynamicznego przesyłania strumieniowego, dostępne są następujące opcje:

- [Prześlij plik główny](media-services-dotnet-upload-files.md).
- [Koder Media Encoder Standard służy do tworzenia adaptacyjnych zestawów bitrate H.264 MP4.](media-services-dotnet-encode-with-media-encoder-standard.md)
- [Przesyłaj strumieniowo zawartość](media-services-deliver-content-overview.md).

## <a name="audio-codecs-supported-by-dynamic-packaging"></a>Kodeki audio obsługiwane przez dynamiczne opakowania

Dynamic Packaging obsługuje pliki MP4, które zawierają dźwięk zakodowany z [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) (AAC-LC, HE-AAC v1, HE-AAC v2), [Dolby Digital Plus](https://en.wikipedia.org/wiki/Dolby_Digital_Plus)(Enhanced AC-3 lub E-AC3), Dolby Atmos lub [DTS](https://en.wikipedia.org/wiki/DTS_%28sound_system%29) (DTS Express, DTS LBR, DTS HD, DTS HD Lossless). Przesyłanie strumieniowe zawartości Dolby Atmos jest obsługiwane dla standardów, takich jak protokół MPEG-DASH z interfejsem MP4 common streaming format (CSF) lub common media application format (CMAF) oraz za pośrednictwem protokołu HTTP Live Streaming (HLS) z CMAF.

> [!NOTE]
> Funkcja Dynamic Packaging nie obsługuje plików zawierających dźwięk [Dolby Digital](https://en.wikipedia.org/wiki/Dolby_Digital) (AC3) (jest to starszy kodek).

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

