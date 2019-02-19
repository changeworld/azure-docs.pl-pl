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
ms.date: 02/17/2019
ms.author: juliako
ms.openlocfilehash: 21b2d9bdef5e6974126c78b1005bd4786182c881
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/18/2019
ms.locfileid: "56405720"
---
# <a name="dynamic-packaging"></a>Dynamiczne tworzenie pakietów

Microsoft Azure Media Services może służyć do dostarczania wiele nośnika źródłowego formatów plików, przesyłanie strumieniowe formatów multimediów i funkcja content protection formatuje różne technologie klienta (na przykład dla systemu iOS, XBOX, Silverlight, Windows 8). Ci klienci zrozumienie różnych protokołów, na przykład dla systemu iOS wymaga formatu HTTP Live Streaming (HLS) w wersji 4 i Silverlight i konsolach Xbox wymagają Smooth Streaming. Jeśli masz zestaw z adaptacyjną szybkością transmisji bitów (bitów) w formacie MP4 plików (ISO Base nośników 14496-12) lub zestaw Smooth Streaming pliki adaptacyjną szybkością transmisji bitów, które mają służyć do klientów, którzy zrozumieć MPEG DASH, HLS lub Smooth Streaming, należy skorzystać multimediów Dynamiczne tworzenie pakietów usług.

Dynamiczne pakowanie wszystkie, potrzebne jest utworzenie element zawartości zawierający zestaw plików MP4 lub pliki Smooth Streaming adaptacyjną szybkością transmisji bitów. Następnie na podstawie formatu określonego w żądaniu fragmentu ani manifestu przesyłania strumieniowego na żądanie serwer będzie upewnij się, że strumień jest dostarczany za pomocą wybranego protokołu. Dzięki temu wystarczy przechowywać i opłacać pliki w jednym formacie magazynu, a usługa Media Services utworzy oraz udostępni właściwą odpowiedź na podstawie żądań klienta.

Na poniższym diagramie przedstawiono tradycyjne kodowanie i statyczne tworzenie pakietów przepływów pracy.

![Kodowanie statyczne](./media/dynamic-packaging-overview/media-services-static-packaging.png)

Na poniższym diagramie przedstawiono przepływ pracy funkcji dynamicznego tworzenia pakietów.

![Dynamiczne kodowania](./media/dynamic-packaging-overview/media-services-dynamic-packaging.png)

## <a name="common-scenario"></a>Typowy scenariusz

1. Przekaż plik wejściowy (nazywane plik mezzanine). Na przykład H.264, MP4 lub WMV (Aby uzyskać listę obsługiwanych formatów, zobacz [formaty obsługiwane przez Media Encoder Standard](media-encoder-standard-formats.md).
2. Kodowanie pliku mezzanine do H.264 MP4 o adaptacyjnej szybkości transmisji bitów zestawów.
3. Opublikuj element zawartości zawierający adaptacyjną szybkością transmisji bitów, zestawu plików MP4.
4. Twórz adresy URL przesyłania strumieniowego do uzyskania dostępu i przesyłania strumieniowego zawartości.

## <a name="audio-codecs-supported-by-dynamic-packaging"></a>Kodery-dekodery audio obsługiwane przez funkcję dynamicznego tworzenia pakietów

Dynamiczne tworzenie pakietów obsługuje pliki MP4 (lub Smooth Streaming plików), obejmującą audio zakodowane przy użyciu [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) (AAC-LC, HE-AAC v1, v2 HE AAC), [Dolby Digital Plus](https://en.wikipedia.org/wiki/Dolby_Digital_Plus) (Enhanced AC-3 lub E AC3), lub [ Używanie usług DTS](https://en.wikipedia.org/wiki/DTS_%28sound_system%29) (używanie usług DTS Express, DTS LBR, DTS HD, HD DTS bezstratne).

> [!Note]
> Dynamiczne tworzenie pakietów nie obsługuje plików, które zawierają [Dolby Digital](https://en.wikipedia.org/wiki/Dolby_Digital) audio (AC3) (jest to starszy koder-dekoder).

## <a name="next-steps"></a>Kolejne kroki

[Przekazywanie, kodowanie, filmy wideo usługi stream](stream-files-tutorial-with-api.md)
