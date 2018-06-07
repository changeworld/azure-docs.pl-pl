---
title: Kodowanie w chmurze za pomocą usługi Azure Media Services | Dokumentacja firmy Microsoft
description: W tym temacie opisano proces kodowania, korzystając z usługi Azure Media Services
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/21/2018
ms.author: juliako
ms.openlocfilehash: e1c7536c59b110ae3dd753ff5f4b01195f8dadca
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655629"
---
# <a name="encoding-with-azure-media-services"></a>Kodowanie w usłudze Azure Media Services

Usługa Azure Media Services umożliwia kodowanie wysokiej jakości multimedialnych plików do formatów, które można odtworzyć na różnych przeglądarkach i urządzeniach. Na przykład możesz chcieć strumienia, które formatuje zawartość w HLS lub MPEG DASH firmy Apple. Usługa Media Services umożliwia analizowanie zawartości wideo lub audio. W tym temacie umożliwiają wskazówki na temat kodowania zawartości przy użyciu usługi Media Services w wersji 3.

Kodowanie z usługi Media Services w wersji 3, musisz utworzyć transformacji i zadania. Transformacja definiuje przepisu dla kodowania ustawień i danych wyjściowych, a zadanie jest wystąpieniem przepisu. Aby uzyskać więcej informacji, zobacz [przekształca i zadania](transform-concept.md)

Podczas kodowania w usłudze Azure Media Services, musisz predefiniowanych mówić koder przetwarzaniu multimedialnych plików wejściowych. Na przykład można określić rozdzielczości i/lub liczbę kanałów audio, który ma w zakodowanej zawartości. 

Możesz szybkie rozpoczęcie pracy z jednym zalecanych ustawień wbudowanych na podstawie najlepszych rozwiązań w branży lub można utworzyć niestandardowe ustawienia wstępnego pod kątem określonych wymagań scenariusza lub urządzenia. 

Wyszukiwanie szczegółowych informacji o kodera w [specyfikacji OpenAPI](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/preview/2018-03-30-preview). 

## <a name="built-in-presets"></a>Wbudowane ustawienia

Usługa Media Services obsługuje obecnie następujące wbudowane predefiniowanych:  

|**Nazwa ustawienia wstępnego**|**Scenariusz**|**Szczegóły**|
|---|---|---|
|**AudioAnalyzerPreset**|Analizowanie danych audio|Ustawienie ma zastosowanie wstępnie zdefiniowany zestaw operacji na podstawie AI analizy, w tym przekształcania mowy. Ustawienie obsługuje obecnie przetwarzania zawartości z pojedynczą ścieżkę audio.<br/>Można określić języka audio ładunku w danych wejściowych w formacie BCP 47 "region znacznik języka" (na przykład "en US"). Listę obsługiwanych języków są "en US", "pl pl.", "es-ES", "es-MX", "fr-FR", "it-IT", "ja-JP", "pt-BR", "zh-CN".|
|**VideoAnalyzerPreset**|Analizowanie danych audio i wideo|Wyodrębnia szczegółowych informacji (metadanych sformatowanego) z audio i wideo, a następnie generuje plik formatu JSON. Można określić, czy mają zostać wyodrębnione audio insights podczas przetwarzania pliku wideo. Aby uzyskać więcej informacji, zobacz [wideo Analizuj](analyze-videos-tutorial-with-api.md).|
|**BuiltInStandardEncoderPreset**|Przesyłanie strumieniowe|Służy do określania wbudowaną ustawienie wstępne kodowania wejściowy plik wideo z standardowego kodera. <br/>Obecnie obsługiwane są następujące ustawienia:<br/>**EncoderNamedPreset.AdaptiveStreaming** (zalecane). Aby uzyskać więcej informacji, zobacz [automatycznego wygenerowania drabinę szybkości transmisji bitów](autogen-bitrate-ladder.md).<br/>**EncoderNamedPreset.AACGoodQualityAudio** — powoduje utworzenie pojedynczego pliku MP4 zawierający tylko stereo audio zakodowane w 192 kb/s.<br/>**EncoderNamedPreset.H264MultipleBitrate1080p** — powoduje utworzenie zestawu plików MP4 wyrównane GOP 8, od 6000 KB/s do 400 KB/s i stereo AAC audio. Rozdzielczość rozpoczyna się od 1080p i przechodzi do 360 p.<br/>**EncoderNamedPreset.H264MultipleBitrate720p** — powoduje utworzenie zestawu plików MP4 wyrównane GOP 6, od 3400 KB/s do 400 KB/s i stereo AAC audio. Rozdzielczość rozpoczyna się od 720p i przechodzi do 360 p.<br/>**EncoderNamedPreset.H264MultipleBitrateSD** — powoduje utworzenie zestawu plików MP4 wyrównane GOP 5, od 1600 KB/s do 400 KB/s i stereo AAC audio. Rozdzielczość rozpoczyna się od 480p i przechodzi do 360 p.<br/><br/>Aby uzyskać więcej informacji, zobacz [przekazywanie, kodowanie i przesyłanie strumieniowe plików](stream-files-tutorial-with-api.md).|
|**StandardEncoderPreset**|Przesyłanie strumieniowe|Opisuje ustawienia używane podczas kodowania wejściowy plik wideo z standardowego kodera. <br/>Użyj tego ustawienia wstępnego w przypadku dostosowywania ustawienia transformacji. Aby uzyskać więcej informacji, zobacz [jak dostosować ustawienia transformacji](customize-encoder-presets-how-to.md).|

## <a name="custom-presets"></a>Ustawienia niestandardowe

Usługa Media Services obsługuje pełni Dostosowywanie wszystkich wartości ustawienia, aby spełnić wymagania dotyczące i wymagań kodowania. Możesz użyć **StandardEncoderPreset** ustawienie wstępne w przypadku dostosowywania ustawienia transformacji. Aby uzyskać szczegółowe wyjaśnienia i przykładowe, zobacz [jak dostosować ustawienia kodera](customize-encoder-presets-how-to.md).

## <a name="scaling-encoding-in-v3"></a>Skalowanie kodowania w wersji 3

Obecnie klienci mają do portalu Azure lub interfejsów API usług AMS v2 skonfiguruj RUs (zgodnie z opisem w [skalowanie przetwarzania multimediów](../previous/media-services-scale-media-processing-overview.md). 

## <a name="next-steps"></a>Kolejne kroki

### <a name="tutorials"></a>Samouczki

Następujące tutorals przedstawiają sposób kodowania zawartości przy użyciu usługi Media Services:

* [Przekazywanie, kodowania i przesyłania strumieniowego przy użyciu usługi Azure Media Services](stream-files-tutorial-with-api.md)
* [Analizowanie plików wideo za pomocą usługi Azure Media Services](analyze-videos-tutorial-with-api.md)

### <a name="code-samples"></a>Przykłady kodu

Poniższe przykłady kodu zawiera kod, który przedstawia sposób kodowania w usłudze Media Services:

* [.NET Core](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore)
* [Interfejs wiersza polecenia 2.0](https://github.com/Azure/azure-docs-cli-python-samples/tree/master/media-services)

### <a name="sdks"></a>Zestawy SDK

Można użyć dowolnej z następujących obsługiwanych SDK w wersji 3 usługi Media Services do zakodowania zawartości.

* [Interfejs wiersza polecenia platformy Azure 2.0](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
* [REST](https://docs.microsoft.com/rest/api/media/transforms)
* [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/mediaservices/management?view=azure-dotnet)
* [Java](https://docs.microsoft.com/java/api/overview/azure/mediaservices)
* [Python](https://docs.microsoft.com/python/api/overview/azure/media-services?view=azure-python)

