---
title: Omówienie koderów multimedialnych na żądanie na platformie Azure | Microsoft Docs
description: Ten temat zawiera omówienie koderów multimediów na żądanie na platformie Azure.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: e6bfc068-fa46-4d68-b1ce-9092c8f3a3c9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/25/2019
ms.author: juliako
ms.openlocfilehash: 7e8c49815ed5e9294739a840dd0314d1c8c6c174
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2019
ms.locfileid: "69015818"
---
# <a name="overview-of-azure-on-demand-media-encoders"></a>Omówienie koderów multimediów na żądanie na platformie Azure 

## <a name="encoding-overview"></a>Omówienie kodowania

> [!NOTE]
> Do usługi Media Services w wersji 2 nie są już dodawane żadne nowe funkcje. <br/>Zapoznaj się z najnowszą wersją, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Zobacz też [wskazówki dotyczące migracji od wersji 2 do V3](../latest/migrate-from-v2-to-v3.md)

Azure Media Services udostępnia wiele opcji kodowania multimediów w chmurze.

Podczas rozpoczynania pracy z Media Services należy zrozumieć różnicę między koderami-dekodermi i formatami plików.
Kodery-dekoder to oprogramowanie implementujące algorytmy kompresji/dekompresji, natomiast formaty plików są kontenerami, które przechowują skompresowane wideo.

Media Services zapewnia dynamiczne pakowanie, które pozwala na dostarczanie zawartości kodowanej przy użyciu plików MP4 lub Smooth Streaming, w formatach przesyłania strumieniowego obsługiwanych przez Media Services (PAUZy MPEG, HLS, Smooth Streaming) bez konieczności ponownego tworzenia pakietów w tych formaty przesyłania strumieniowego.

Po utworzeniu konta usługi AMS zostanie do niego dodany **domyślny** punkt końcowy przesyłania strumieniowego mający stan **Zatrzymany**. Aby rozpocząć przesyłanie strumieniowe zawartości oraz korzystać z dynamicznego tworzenia pakietów i szyfrowania dynamicznego, punkt końcowy przesyłania strumieniowego, z którego chcesz strumieniowo przesyłać zawartość, musi mieć stan **Uruchomiony**.

> [!Note]
> Rozliczanie punktów końcowych przesyłania strumieniowego odbywa się za każdym razem, gdy punkt końcowy jest w stanie **uruchomienia** .

Media Services obsługuje następujące kodery na żądanie, które zostały opisane w tym artykule:

* [Usługa Media Encoder Standard](media-services-encode-asset.md#media-encoder-standard)
* [Przepływ pracy usługi Media Encoder w warstwie Premium](media-services-encode-asset.md#media-encoder-premium-workflow)

Ten artykuł zawiera krótkie omówienie koderów multimediów na żądanie oraz linki do artykułów, które zawierają bardziej szczegółowe informacje. Temat zawiera również porównanie koderów.

Domyślnie każde konto Media Services może mieć jedno aktywne zadanie kodowania jednocześnie. Można zarezerwować jednostki kodowania, które umożliwiają współbieżne uruchamianie wielu zadań kodowania, jeden dla każdej zakupionej zarezerwowanej jednostki kodowania. Aby uzyskać więcej informacji, zobacz [skalowanie jednostek kodowania](media-services-scale-media-processing-overview.md).

## <a name="media-encoder-standard"></a>Usługa Media Encoder Standard
### <a name="how-to-use"></a>Jak stosować
[Jak kodować przy użyciu Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md)

### <a name="formats"></a>Formatów
[Formaty i kodeki](media-services-media-encoder-standard-formats.md)

### <a name="presets"></a>Ustawienia wstępne
Media Encoder Standard jest skonfigurowany przy użyciu jednego z ustawień predefiniowanych kodera opisanych [poniżej](https://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409).

### <a name="input-and-output-metadata"></a>Metadane wejściowe i wyjściowe
Metadane wejściowe koderów zostały opisane [tutaj](media-services-input-metadata-schema.md).

Metadane wyjściowe koderów zostały opisane [tutaj](media-services-output-metadata-schema.md).

### <a name="generate-thumbnails"></a>Generowanie miniatur
Aby uzyskać więcej informacji, zobacz [jak generować miniatury przy użyciu Media Encoder Standard](media-services-advanced-encoding-with-mes.md#thumbnails).

### <a name="trim-videos-clipping"></a>Przycinanie filmów wideo (wycinków)
Aby uzyskać więcej informacji, zobacz [jak przyciąć wideo przy użyciu Media Encoder Standard](media-services-advanced-encoding-with-mes.md#trim_video).

### <a name="create-overlays"></a>Utwórz nakładki
Aby uzyskać więcej informacji, zobacz [jak utworzyć nakładki przy użyciu Media Encoder Standard](media-services-advanced-encoding-with-mes.md#overlay).

### <a name="see-also"></a>Zobacz także
[Blog Media Services](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/)

## <a name="media-encoder-premium-workflow"></a>Przepływ pracy usługi Media Encoder w warstwie Premium
### <a name="overview"></a>Omówienie
[Wprowadzenie kodowania Premium w Azure Media Services](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services/)

### <a name="how-to-use"></a>Jak stosować
Media Encoder Premium Workflow jest skonfigurowany przy użyciu złożonych przepływów pracy. Pliki przepływu pracy można utworzyć i zaktualizować za pomocą narzędzia [Projektant przepływu pracy](media-services-workflow-designer.md) .

[Jak używać kodowania Premium w Azure Media Services](https://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services/)

### <a name="known-issues"></a>Znane problemy
Jeśli wejściowy film wideo nie zawiera napisów kodowanych, element zawartości wyjściowej nadal będzie zawierał pusty plik TTML.

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Pokrewne artykuły:
* [Wykonywanie zaawansowanych zadań kodowania przez dostosowanie Media Encoder Standard ustawień wstępnych](media-services-custom-mes-presets-with-dotnet.md)
* [Limity przydziału i ograniczenia](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: https://azure.microsoft.com/pricing/details/media-services/
