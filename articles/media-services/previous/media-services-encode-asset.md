---
title: Omówienie koderów multimediów platformy Azure na żądanie | Dokumenty firmy Microsoft
description: Usługa Azure Media Services udostępnia wiele opcji kodowania multimediów w chmurze. Ten artykuł zawiera omówienie koderów multimediów platformy Azure na żądanie.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/25/2019
ms.author: juliako
ms.openlocfilehash: d5d5b8a7328ee82e94d494795617832cb0258667
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251104"
---
# <a name="overview-of-azure-on-demand-media-encoders"></a>Omówienie koderów multimediów platformy Azure na żądanie 

> [!NOTE]
> Do usługi Media Services w wersji 2 nie są już dodawane żadne nowe funkcje. <br/>Sprawdź najnowszą wersję usługi [Media Services w wersji 3](https://docs.microsoft.com/azure/media-services/latest/). Zobacz też [wskazówki dotyczące migracji z wersji 2 do v3](../latest/migrate-from-v2-to-v3.md)

Usługa Azure Media Services udostępnia wiele opcji kodowania multimediów w chmurze.

Podczas rozpoczynania pracy z usługą Media Services, ważne jest, aby zrozumieć różnicę między kodeków i formatów plików.
Kodeki to oprogramowanie, które implementuje algorytmy kompresji/dekompresji, podczas gdy formaty plików są kontenerami, które przechowują skompresowany film.

Usługa Media Services zapewnia dynamiczne opakowanie, które pozwala dostarczać adaptacyjną zawartość kodowaną MP4 lub smooth streaming w formatach strumieniowych obsługiwanych przez Media Services (MPEG DASH, HLS, Smooth Streaming) bez konieczności ponownego pakowania do tych formatów strumieniowych.

Po utworzeniu konta usługi Media Services **domyślny** punkt końcowy przesyłania strumieniowego jest dodawany do konta w stanie **Zatrzymane.** Aby rozpocząć przesyłanie strumieniowe zawartości oraz korzystać z dynamicznego tworzenia pakietów i szyfrowania dynamicznego, punkt końcowy przesyłania strumieniowego, z którego chcesz strumieniowo przesyłać zawartość, musi mieć stan **Uruchomiony**. Rozliczenia za punkty końcowe przesyłania strumieniowego występuje zawsze, gdy punkt końcowy jest w stanie **uruchomiony.**

Usługi Media Services obsługują następujące kodery na żądanie, które są opisane w tym artykule:

* [Usługa Media Encoder Standard](media-services-encode-asset.md#media-encoder-standard)
* [Przepływ pracy usługi Media Encoder w warstwie Premium](media-services-encode-asset.md#media-encoder-premium-workflow)

Ten artykuł zawiera krótki przegląd koderów nośników na żądanie i zawiera łącza do artykułów, które zawierają bardziej szczegółowe informacje. Temat zawiera również porównanie koderów.

Domyślnie każde konto usługi Media Services może mieć jedno aktywne zadanie kodowania naraz. Można zarezerwować jednostki kodowania, które umożliwiają jednoczesne uruchamianie wielu zadań kodowania, po jednym dla każdej zakupionej jednostki zarezerwowanej kodowania. Aby uzyskać więcej informacji, zobacz [Skalowanie jednostek kodujących](media-services-scale-media-processing-overview.md).

## <a name="media-encoder-standard"></a>Usługa Media Encoder Standard

### <a name="how-to-use"></a>Jak stosować
[Jak zakodować za pomocą media encoder standard](media-services-dotnet-encode-with-media-encoder-standard.md)

### <a name="formats"></a>Formaty
[Formaty i kodeki](media-services-media-encoder-standard-formats.md)

### <a name="presets"></a>Ustawień
Media Encoder Standard jest skonfigurowany przy użyciu jednego z predefiniowanych ustawień kodera opisanych [tutaj.](https://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409)

### <a name="input-and-output-metadata"></a>Metadane wejściowe i wyjściowe
Kodery wejściowe metadane są opisane [w tym miejscu](media-services-input-metadata-schema.md).

Kodery wyjściowe metadane są opisane [w tym miejscu](media-services-output-metadata-schema.md).

### <a name="generate-thumbnails"></a>Generowanie miniatur
Aby uzyskać więcej informacji, zobacz [Jak generować miniatury przy użyciu standardu Media Encoder .](media-services-advanced-encoding-with-mes.md#thumbnails)

### <a name="trim-videos-clipping"></a>Przycinanie filmów (przycinanie)
Aby uzyskać więcej informacji, zobacz [Jak przycinać filmy za pomocą media encoder standard](media-services-advanced-encoding-with-mes.md#trim_video).

### <a name="create-overlays"></a>Tworzenie nakładek
Aby uzyskać więcej informacji, zobacz [Jak tworzyć nakładki przy użyciu standardu media encoder](media-services-advanced-encoding-with-mes.md#overlay).

### <a name="see-also"></a>Zobacz też
[Blog usługi media](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/)

## <a name="media-encoder-premium-workflow"></a>Przepływ pracy usługi Media Encoder w warstwie Premium
### <a name="overview"></a>Omówienie
[Wprowadzenie do kodowania premium w usłudze Azure Media Services](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services/)

### <a name="how-to-use"></a>Jak stosować
Przepływ pracy usługi Media Encoder Premium jest konfigurowany przy użyciu złożonych przepływów pracy. Pliki przepływu pracy można tworzyć i aktualizować za pomocą narzędzia [Projektant przepływu pracy.](media-services-workflow-designer.md)

[Jak korzystać z kodowania premium w usłudze Azure Media Services](https://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services/)

### <a name="known-issues"></a>Znane problemy
Jeśli wejściowy film wideo nie zawiera napisów kodowych, wyjściowy zasób nadal będzie zawierał pusty plik TTML.

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Pokrewne artykuły:
* [Wykonywanie zaawansowanych zadań kodowania przez dostosowywanie ustawień predefiniowanych programu Media Encoder Standard](media-services-custom-mes-presets-with-dotnet.md)
* [Przydziały i ograniczenia](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: https://azure.microsoft.com/pricing/details/media-services/
