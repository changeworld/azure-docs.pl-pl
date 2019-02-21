---
title: Omówienie dynamicznego tworzenia pakietów usługi Azure Media Services | Dokumentacja firmy Microsoft
description: Temat zawiera omówienie funkcji dynamicznego tworzenia pakietów w usłudze Media Services.
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
ms.date: 02/19/2019
ms.author: juliako
ms.openlocfilehash: d1d07402bca5f01cf63d0b039c085e46bb0f0d62
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/20/2019
ms.locfileid: "56447926"
---
# <a name="dynamic-packaging"></a>Dynamiczne tworzenie pakietów

Usługa content protection formatów różne technologie klienta (na przykład iOS i konsoli XBOX) i Microsoft Azure Media Services może służyć do dostarczania wiele nośnika źródłowego formatów plików, przesyłanie strumieniowe formatów multimediów. Ci klienci zrozumienie różnych protokołów, na przykład dla systemu iOS wymaga formatu HTTP Live Streaming (HLS) i konsolach Xbox wymagają Smooth Streaming. Jeśli masz zestaw z adaptacyjną szybkością transmisji bitów (bitów) w formacie MP4 (ISO Base nośników 14496-12) plików lub zestaw Smooth Streaming pliki adaptacyjną szybkością transmisji bitów, które mają służyć do klientów, którzy zrozumieć HLS, MPEG DASH lub Smooth Streaming, powinna korzystać nośnika Dynamiczne tworzenie pakietów usług.

Za pomocą funkcji dynamicznego tworzenia pakietów wszystko, czego potrzebujesz, jest utworzenie element zawartości zawierający zestaw plików MP4. Następnie na podstawie formatu określonego w żądaniu fragmentu ani manifestu przesyłania strumieniowego na żądanie serwer będzie upewnij się, że strumień jest dostarczany za pomocą wybranego protokołu. Dzięki temu wystarczy przechowywać i opłacać pliki w jednym formacie magazynu, a usługa Media Services utworzy oraz udostępni właściwą odpowiedź na podstawie żądań klienta.

Na poniższym diagramie przedstawiono tradycyjne kodowanie i statyczne tworzenie pakietów przepływów pracy.

![Kodowanie statyczne](./media/dynamic-packaging-overview/media-services-static-packaging.png)

Na poniższym diagramie przedstawiono przepływ pracy funkcji dynamicznego tworzenia pakietów.

![Dynamiczne kodowania](./media/dynamic-packaging-overview/media-services-dynamic-packaging.png)

## <a name="dynamic-packaging-workflow"></a>Przepływ pracy funkcji dynamicznego tworzenia pakietów

1. Przekaż plik wejściowy (nazywane plik mezzanine). Na przykład H.264, MP4 lub WMV (Aby uzyskać listę obsługiwanych formatów, zobacz [formaty obsługiwane przez Media Encoder Standard](media-encoder-standard-formats.md).
2. Kodowanie pliku mezzanine do H.264 MP4 o adaptacyjnej szybkości transmisji bitów zestawów.
3. Opublikuj element zawartości zawierający adaptacyjną szybkością transmisji bitów, zestawu plików MP4.
4. Twórz adresy URL przesyłania strumieniowego do uzyskania dostępu i przesyłania strumieniowego zawartości.

## <a name="audio-codecs-supported-by-dynamic-packaging"></a>Kodery-dekodery audio obsługiwane przez funkcję dynamicznego tworzenia pakietów

Dynamiczne tworzenie pakietów obsługuje pliki w formacie MP4, które zawierają audio zakodowane za pomocą [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) (AAC-LC, HE-AAC v1, v2 HE AAC), [Dolby Digital Plus](https://en.wikipedia.org/wiki/Dolby_Digital_Plus) (Enhanced AC-3 lub E AC3), lub [DTS](https://en.wikipedia.org/wiki/DTS_%28sound_system%29) (DTS Express, DTS LBR, DTS HD, HD DTS bezstratne).

> [!NOTE]
> Dynamiczne tworzenie pakietów nie obsługuje plików, które zawierają [Dolby Digital](https://en.wikipedia.org/wiki/Dolby_Digital) audio (AC3) (jest to starszy koder-dekoder).

## <a name="next-steps"></a>Kolejne kroki

[Przekazywanie, kodowanie, filmy wideo usługi stream](stream-files-tutorial-with-api.md)
