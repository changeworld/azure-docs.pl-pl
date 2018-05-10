---
title: Omówienie i porównanie Azure na żądanie nośnika koderów | Dokumentacja firmy Microsoft
description: Ten temat zawiera omówienie i porównanie Azure na żądanie koderów nośnika.
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.assetid: e6bfc068-fa46-4d68-b1ce-9092c8f3a3c9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2017
ms.author: juliako
ms.openlocfilehash: 6e44cff930f943f5cf30fb5335ad6a7fd1d08ab5
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="overview-and-comparison-of-azure-on-demand-media-encoders"></a>Omówienie i porównanie Azure na żądanie nośnika koderów
## <a name="encoding-overview"></a>Omówienie kodowania
Azure Media Services udostępnia wiele opcji kodowania nośnika w chmurze.

Podczas uruchamiania z programem Media Services, należy zrozumieć różnicę między formatami koderów-dekoderów i plików.
Kodery-dekodery to oprogramowanie, które implementuje algorytmy kompresji i dekompresji formatów plików są kontenery zawierających skompresowane wideo.

Usługa Media Services udostępnia funkcję dynamicznego tworzenia pakietów, co pozwala na dostarczanie zawartości o adaptacyjnej szybkości bitowej MP4 lub Smooth Streaming zakodowane w formatach transmisji strumieniowej obsługiwanych przez usługę Media Services (MPEG DASH, HLS, Smooth Streaming) bez konieczności ponownego tworzenia pakietów w tych formatach.

>[!NOTE]
>Po utworzeniu konta usługi AMS zostanie do niego dodany **domyślny** punkt końcowy przesyłania strumieniowego mający stan **Zatrzymany**. Aby rozpocząć przesyłanie strumieniowe zawartości oraz korzystać z dynamicznego tworzenia pakietów i szyfrowania dynamicznego, punkt końcowy przesyłania strumieniowego, z którego chcesz strumieniowo przesyłać zawartość, musi mieć stan **Uruchomiony**. Aby móc korzystać z [dynamicznego tworzenia pakietów](media-services-dynamic-packaging-overview.md), należy wykonać następujące czynności:
>
>Ponadto kodowanie pliku źródłowego do zestawu plików MP4 z adaptacyjną szybkością transmisji bitów lub pliki Smooth Streaming adaptacyjną szybkością transmisji bitów (kroki kodowania przedstawiono w dalszej części tego samouczka).

Usługa Media Services obsługuje następujące na koderów żądanie, które zostały opisane w tym artykule:

* [Usługa Media Encoder Standard](media-services-encode-asset.md#media-encoder-standard)
* [Przepływ pracy usługi Media Encoder w warstwie Premium](media-services-encode-asset.md#media-encoder-premium-workflow)

W tym artykule zawiera krótki przegląd na żądanie koderów nośnika i łącza do artykułów, które zapewniają bardziej szczegółowe informacje. Temat zawiera również porównania koderów.

>[!NOTE]
>Domyślnie wszystkie konta usługi Media Services może mieć aktywne zadania kodowania w czasie. Istnieje możliwość rezerwowania jednostki kodowania, które umożliwiają wielu zadań kodowania uruchomione jednocześnie, po jednej dla każdego kodowania jednostka zarezerwowanego zakupu. Aby uzyskać informacje, zobacz [skalowania jednostki kodowania](media-services-scale-media-processing-overview.md).

## <a name="media-encoder-standard"></a>Usługa Media Encoder Standard
### <a name="how-to-use"></a>Jak stosować
[Sposób kodowania przy użyciu Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md)

### <a name="formats"></a>Formaty
[Koderów-dekoderów i formaty](media-services-media-encoder-standard-formats.md)

### <a name="presets"></a>Ustawienia
Media Encoder Standard została skonfigurowana przy użyciu jednego ustawień kodera opisane [tutaj](http://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409).

### <a name="input-and-output-metadata"></a>Wejście i wyjście metadanych
Opisano metadanych wejściowych koderów [tutaj](media-services-input-metadata-schema.md).

Opisano metadanych dane wyjściowe koderów [tutaj](media-services-output-metadata-schema.md).

### <a name="generate-thumbnails"></a>Generowanie miniatur
Aby uzyskać informacje, zobacz [sposób generowania miniatur przy użyciu standardu Media Encoder Standard](media-services-advanced-encoding-with-mes.md#thumbnails).

### <a name="trim-videos-clipping"></a>TRIM wideo (wycinka)
Aby uzyskać informacje, zobacz [jak przyciąć wideo przy użyciu standardu Media Encoder Standard](media-services-advanced-encoding-with-mes.md#trim_video).

### <a name="create-overlays"></a>Utwórz nakładki
Aby uzyskać informacje, zobacz [tworzenie nakładki przy użyciu standardu Media Encoder Standard](media-services-advanced-encoding-with-mes.md#overlay).

### <a name="see-also"></a>Zobacz także
[Blog usługi Media Services](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/)

## <a name="media-encoder-premium-workflow"></a>Przepływ pracy usługi Media Encoder w warstwie Premium
### <a name="overview"></a>Przegląd
[Wprowadzenie do kodowania w Azure Media Services w wersji Premium](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services/)

### <a name="how-to-use"></a>Jak stosować
Media Encoder Premium w przepływie pracy jest skonfigurowana przy użyciu złożonych przepływów pracy. Pliki przepływu pracy może zostać utworzony i zaktualizować przy użyciu [projektanta przepływów pracy](media-services-workflow-designer.md) narzędzia.

[Jak używać kodowania w Azure Media Services w wersji Premium](https://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services/)

### <a name="known-issues"></a>Znane problemy
Jeśli wejściowy plik wideo nie zawiera kodowane się, dane wyjściowe zasobów będzie nadal zawierać pusty plik TTML.


## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Pokrewne artykuły:
* [Wykonywania zaawansowanych zadań kodowania dostosowując ustawienia standardu Media Encoder Standard](media-services-custom-mes-presets-with-dotnet.md)
* [Przydziały i ograniczenia](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: http://azure.microsoft.com/pricing/details/media-services/
