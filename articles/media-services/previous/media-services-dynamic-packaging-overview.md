---
title: Omówienie dynamicznego tworzenia pakietów w usłudze Azure Media Services | Dokumentacja firmy Microsoft
description: Zapewnia tematu i przegląd dynamicznego tworzenia pakietów.
author: Juliako
manager: cfowler
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 0d9e4f54-5daa-45c1-bfaa-cf09ca89b812
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: juliako
ms.openlocfilehash: 8f05015da1f66331413086c0e27c25cd5da75f5c
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
ms.locfileid: "33788389"
---
# <a name="dynamic-packaging"></a>Dynamiczne tworzenie pakietów
## <a name="overview"></a>Przegląd
Microsoft Azure Media Services może służyć do dostarczania wiele nośnika źródłowego formatów plików, przesyłania strumieniowego formatów multimediów i formatuje ochrony zawartości do różnych technologii klienta (na przykład iOS, XBOX, Silverlight, Windows 8). Ci klienci zrozumienie różnych protokołów, na przykład iOS wymaga formatu HTTP Live Streaming (HLS) w wersji 4 i Silverlight i Xbox wymagają Smooth Streaming. Jeśli istnieje zestaw o adaptacyjnej szybkości transmisji bitów (różnych szybkościach transmisji bitów) MP4 plików (ISO Base nośników 14496 12) lub zestawu Smooth Streaming plików adaptacyjną szybkością transmisji bitów, które mają służyć do klientów, które rozumieją MPEG DASH, HLS lub Smooth Streaming, należy skorzystać z nośnika Dynamiczne tworzenie pakietów usług.

Dzięki funkcji dynamicznego tworzenia pakietów wystarczy jest utworzenie element zawartości zawierający zestaw plików MP4 lub Smooth Streaming plików. Następnie na podstawie formatu określonego w żądaniu manifestu lub fragmentu przesyłania strumieniowego na żądanie serwera zapewni odbierać strumień w protokole wybrana. Dzięki temu wystarczy przechowywać i opłacać pliki w jednym formacie magazynu, a usługa Media Services utworzy oraz udostępni właściwą odpowiedź na podstawie żądań klienta.

Na poniższym diagramie przedstawiono tradycyjne kodowanie i statyczne tworzenie pakietów przepływów pracy.

![Kodowanie statyczne](./media/media-services-dynamic-packaging-overview/media-services-static-packaging.png)

Na poniższym diagramie przedstawiono przepływ pracy dynamicznego tworzenia pakietów.

![Kodowanie dynamiczne](./media/media-services-dynamic-packaging-overview/media-services-dynamic-packaging.png)


## <a name="common-scenario"></a>Typowy scenariusz
1. Przekaż plik wejściowy (nazywanego plikiem mezzanine). Na przykład, H.264, MP4 lub WMV (Aby uzyskać listę obsługiwanych formatów, zobacz [obsługiwane formaty przez Media Encoder Standard](media-services-media-encoder-standard-formats.md).
2. Kodowanie pliku mezzanine do zestawu o adaptacyjnej szybkości bitowej H.264 MP4.
3. Publikowanie elementu zawartości, który zawiera o adaptacyjnej szybkości bitowej MP4 ustawiony przez utworzenie lokalizatora na żądanie.
4. Tworzyć adresy URL przesyłania strumieniowego dostęp do strumienia zawartości.

## <a name="preparing-assets-for-dynamic-streaming"></a>Przygotowanie zasobów do dynamicznego przesyłania strumieniowego
Aby przygotować zawartości dynamicznej przesyłania strumieniowego masz dwie opcje:

1. [Przekaż plik główny](media-services-dotnet-upload-files.md).
2. [Za pomocą kodera Media Encoder Standard tworzyć zestawów o adaptacyjnej szybkości bitowej H.264 MP4](media-services-dotnet-encode-with-media-encoder-standard.md).
3. [Strumieniowo zawartość](media-services-deliver-content-overview.md).

## <a id="unsupported_formats"></a>Formatów, które nie są obsługiwane przez funkcję dynamicznego tworzenia pakietów
Następujące formaty plików źródła nie są obsługiwane przez funkcję dynamicznego tworzenia pakietów.

* Pliki mp4 cyfrowe Dolby.
* Pliki smooth cyfrowe Dolby.

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

