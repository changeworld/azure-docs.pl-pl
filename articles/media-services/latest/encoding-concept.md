---
title: Kodowanie w chmurze za pomocą usługi Media Services — Azure | Dokumentacja firmy Microsoft
description: W tym temacie opisano proces kodowania, korzystając z usługi Azure Media Services
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 12/08/2018
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 7c16fc61a8654fa6b7236b9c5252ed4874787d50
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2018
ms.locfileid: "53141751"
---
# <a name="encoding-with-media-services"></a>Kodowanie za pomocą usługi Media Services

Usługa Azure Media Services umożliwia kodowanie plików multimediów cyfrowych wysokiej jakości do formatów, które można odtwarzać na różnych przeglądarek i urządzeń. Na przykład może zaistnieć potrzeba strumieniowego odtwarzania treści w formatach HLS lub MPEG DASH firmy Apple. Ten temat zawiera wskazówki na temat kodowania zawartości za pomocą usługi Media Services v3.

Kodowanie za pomocą usługi Media Services v3, musisz utworzyć transformację i zadania. Przekształcenie definiuje przepis na kodowania ustawień i danych wyjściowych, a zadanie jest wystąpieniem przepisu. Aby uzyskać więcej informacji, zobacz [transformacje i zadania](transform-concept.md)

Podczas kodowania za pomocą usługi Media Services, umożliwia wstępne Poinformuj kodera w przetwarzaniu multimedialnych plików wejściowych. Na przykład można określić rozdzielczość wideo i/lub liczbę kanałów audio, który ma w zakodowanej zawartości. 

Użytkownik może szybko rozpocząć pracę z jedną z zalecanych wbudowane ustawienia wstępne, oparte na najlepszych branżowych rozwiązań lub można utworzyć niestandardowe ustawienie wstępne pod kątem określonych wymagań scenariusza lub urządzenia. Aby uzyskać więcej informacji, zobacz [kodowanie przy użyciu niestandardowej transformacji](customize-encoder-presets-how-to.md). 

## <a name="built-in-presets"></a>Wbudowane ustawienia wstępne

Usługa Media Services obsługuje obecnie następujące wbudowane ustawienia wstępne kodowania:  

|**Nazwa ustawienia wstępnego**|**Scenariusz**|**Szczegóły**|
|---|---|---|
|**BuiltInStandardEncoderPreset**|Przesyłanie strumieniowe|Używane do definiowania wbudowaną ustawienie wstępne kodowania wejściowy plik wideo za pomocą kodera w warstwie standardowa. <br/>Obecnie obsługiwane są następujące ustawienia:<br/>**EncoderNamedPreset.AdaptiveStreaming** (zalecane). Aby uzyskać więcej informacji, zobacz [automatyczne generowanie drabiny szybkości transmisji bitów](autogen-bitrate-ladder.md).<br/>**EncoderNamedPreset.AACGoodQualityAudio** — tworzy pojedynczego pliku MP4, zawierający tylko dźwięk stereo zakodowane w 192 kb/s.<br/>**EncoderNamedPreset.H264MultipleBitrate1080p** — tworzy zbiór 8 wyrównane GOP pliki MP4 z, od 6000 KB/s do 400 KB/s i stereo AAC audio. Rozdzielczość rozpoczyna się od 1080p i prowadzi w dół do 360 p.<br/>**EncoderNamedPreset.H264MultipleBitrate720p** — tworzy zbiór 6 wyrównane GOP pliki MP4 z, od 3400 KB/s do 400 KB/s i stereo AAC audio. Rozdzielczość rozpoczyna się od 720p i prowadzi w dół do 360 p.<br/>**EncoderNamedPreset.H264MultipleBitrateSD** — tworzy zbiór 5 wyrównane GOP pliki MP4 z, od 1600 KB/s do 400 KB/s i stereo AAC audio. Rozdzielczość rozpoczyna się od 480p i prowadzi w dół do 360 p.<br/><br/>Aby uzyskać więcej informacji, zobacz [przekazywanie, kodowanie i przesyłanie strumieniowe plików](stream-files-tutorial-with-api.md).|
|**StandardEncoderPreset**|Przesyłanie strumieniowe|W tym artykule opisano ustawienia, które mają być używane podczas kodowania wejściowy plik wideo za pomocą kodera w warstwie standardowa. <br/>Użyj tego ustawienia wstępnego podczas dostosowywania ustawień wstępnych transformacji. Aby uzyskać więcej informacji, zobacz [Dostosowywanie ustawień wstępnych przekształcenie](customize-encoder-presets-how-to.md).|

## <a name="custom-presets"></a>Ustawienia niestandardowe

Usługa Media Services w pełni obsługuje dostosowywania wszystkie wartości w ustawieniach wstępnych w celu spełnienia specyficznych potrzeb kodowania i wymagań dotyczących usługi. Możesz użyć **StandardEncoderPreset** wstępnie ustawione podczas dostosowywania ustawień wstępnych transformacji. Aby uzyskać szczegółowe wyjaśnienie i przykład zobacz [Dostosowywanie ustawień wstępnych kodera](customize-encoder-presets-how-to.md).

## <a name="scaling-encoding-in-v3"></a>Skalowanie kodowania w wersji 3

Obecnie klienci mają używać witryny Azure portal lub interfejsów API w wersji 2 usługi Media Services do zestawu jednostek ru (zgodnie z opisem w [skalowanie przetwarzania multimediów](../previous/media-services-scale-media-processing-overview.md). 

## <a name="next-steps"></a>Kolejne kroki

### <a name="tutorials"></a>Samouczki

Następujący samouczek pokazuje, jak w celu zakodowania zawartości przy użyciu usługi Media Services:

* [Przekazywanie, kodowanie i przesyłanie strumieniowe przy użyciu usługi Media Services](stream-files-tutorial-with-api.md)

### <a name="code-samples"></a>Przykłady kodu

Poniższe przykłady kodu zawiera kod, który pokazuje, jak kodowanie za pomocą usługi Media Services:

* [.NET Core](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore)
* [Interfejs wiersza polecenia platformy Azure](https://github.com/Azure/azure-docs-cli-python-samples/tree/master/media-services)

### <a name="sdks"></a>Zestawy SDK

Można użyć dowolnego z następujących obsługiwanych usług Media Services v3 zestawów SDK w celu zakodowania zawartości.

* [Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
* [REST](https://docs.microsoft.com/rest/api/media/transforms)
* [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/mediaservices/management?view=azure-dotnet)
* [Java](https://docs.microsoft.com/java/api/overview/azure/mediaservices)
* [Python](https://docs.microsoft.com/python/api/overview/azure/media-services?view=azure-python)

