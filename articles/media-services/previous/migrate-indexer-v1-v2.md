---
title: Migrowanie z indeksatora w wersji 1 i 2 do indeksatora wideo usługi Azure Media Services | Dokumenty firmy Microsoft
description: W tym temacie omówiono sposób migracji z usługi Azure Media Indexer w wersji 1 i 2 do indeksatora wideo usługi Azure Media Services.
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
ms.date: 09/20/2019
ms.author: juliako
ms.openlocfilehash: 2268c074480f99ca23117ca2ffd2c87c1dbb10a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76513239"
---
# <a name="migrate-from-media-indexer-and-media-indexer-2-to-video-indexer"></a>Migrowanie z indeksatora multimediów i indeksatora multimediów 2 do indeksatora wideo

Procesor multimediów [indeksu mediów Azure](media-services-index-content.md) i procesory multimediów [Azure Media Indexer 2](media-services-process-content-with-indexer2.md) Preview są wycofywane. Aby uzyskać daty wycofania, zobacz ten temat [starszych składników.](legacy-components.md) [Indeksator wideo usługi Azure Media Services](https://docs.microsoft.com/azure/media-services/video-indexer/) zastępuje te starsze procesory multimediów.

Indeksator wideo usługi Azure Media Services jest zbudowany na podstawie usługi Azure Media Analytics, Azure Cognitive Search, Cognitive Services (takich jak interfejs API twarzy, usługa Microsoft Translator, interfejs API przetwarzania obrazów komputerowych i niestandardowa usługa mowy). Umożliwia wyodrębnianie szczegółowych informacji z plików wideo przy użyciu modeli wideo i audio usługi Video Indexer. Aby zobaczyć, w jakich scenariuszach można używać indeksatora wideo, jakie funkcje oferuje i jak rozpocząć, zobacz [Modele wideo i audio indeksatora wideo.](../video-indexer/video-indexer-overview.md) 

Można wyodrębnić szczegółowe informacje z plików wideo i audio przy użyciu [ustawień predefiniowanych analizatora usługi Azure Media Services w wersji 3](../latest/analyzing-video-audio-files-concept.md) lub bezpośrednio przy użyciu [interfejsów API indeksatora wideo.](https://api-portal.videoindexer.ai/) Obecnie istnieje nakładanie się funkcji oferowanych przez interfejsy API indeksatora wideo i interfejsów API usługi Media Services w wersji 3.

> [!NOTE]
> Aby zrozumieć, kiedy chcesz użyć video indexer vs Media Services analizatora presetów, sprawdź [dokument porównawczy](../video-indexer/compare-video-indexer-with-media-services-presets.md). 

W tym artykule omówiono kroki migracji z indeksatora multimediów Azure i indeksatora multimediów Azure 2 do indeksatora wideo usługi Azure Media Services.  

## <a name="migration-options"></a>Opcje migracji 

|Jeśli potrzebujesz  |to |
|---|---|
|rozwiązanie, które zapewnia transkrypcję mowy na tekst dla dowolnego formatu pliku multimedialnego w formatach plików z podpisami kodowymi: VTT, SRT lub TTML<br/>jak również dodatkowe informacje audio, takie jak: słowa kluczowe, wnioskowanie tematów, zdarzenia akustyczne, diarization głośników, wyodrębnianie jednostek i tłumaczenie| aktualizowanie aplikacji w celu korzystania z możliwości indeksatora wideo platformy Azure za pośrednictwem interfejsu API REST indeksatora wideo w wersji 2 lub predefiniowanych ustawień analizatora audio usługi Azure Media Services w wersji 3.|
|możliwości zamiany mowy na tekst| bezpośrednio korzystać z interfejsu API mowy usług Cognitive Services.|  

## <a name="getting-started-with-video-indexer"></a>Wprowadzenie do indeksatora wideo

W poniższej sekcji przedstawiono odpowiednie linki: [Jak mogę rozpocząć pracę z video indexerem?](https://docs.microsoft.com/azure/media-services/video-indexer/video-indexer-overview#how-can-i-get-started-with-video-indexer) 

## <a name="getting-started-with-media-services-v3-apis"></a>Wprowadzenie do interfejsów API usługi Media Services w wersji 3

Interfejs API usługi Azure Media Services w wersji 3 umożliwia wyodrębnianie szczegółowych informacji z plików wideo i audio za pośrednictwem [predefiniowanych ustawień analizatora usługi Azure Media Services w wersji 3.](../latest/analyzing-video-audio-files-concept.md) 

**AudioAnalyzerPreset** umożliwia wyodrębnienie wielu informacji audio z pliku audio lub wideo. Dane wyjściowe zawierają plik VTT lub TTML dla transkrypcji audio i plik JSON (ze wszystkimi dodatkowymi spostrzeżeniami audio). Statystyki audio obejmują słowa kluczowe, indeksowanie prelegentów i analizę tonacji mowy. AudioAnalyzerPreset obsługuje również wykrywanie języka dla określonych języków. Aby uzyskać szczegółowe informacje, zobacz [Przekształcenia](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#audioanalyzerpreset).

### <a name="get-started"></a>Wprowadzenie

Aby rozpocząć, zobacz:

* [Samouczek](../latest/analyze-videos-tutorial-with-api.md)
* Próbki audioanalyzerpreset: [zestaw Java SDK](https://github.com/Azure-Samples/media-services-v3-java/tree/master/AudioAnalytics/AudioAnalyzer) lub [zestaw SDK .NET](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/AudioAnalytics/AudioAnalyzer)
* VideoAnalyzerPreset przykłady: [Java SDK](https://github.com/Azure-Samples/media-services-v3-java/tree/master/VideoAnalytics/VideoAnalyzer) lub [.NET SDK](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/VideoAnalytics/VideoAnalyzer)

## <a name="getting-started-with-cognitive-services-speech-services"></a>Wprowadzenie do usług cognitive services mowy

[Usługa Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/) udostępnia usługę zamiany mowy na tekst, która transkrybuje strumienie audio do tekstu w czasie rzeczywistym, które mogą korzystać z aplikacji, narzędzi lub urządzeń. Za pomocą zamiany mowy na tekst można [dostosować własny model akustyczny, model języka lub model wymowy.](../../cognitive-services/speech-service/how-to-custom-speech-train-model.md) Aby uzyskać więcej informacji, zobacz [Cognitive Services speech-to-text](../../cognitive-services/speech-service/speech-to-text.md). 

> [!NOTE] 
> Usługa zamiany mowy na tekst nie przyjmuje formatów plików wideo i przyjmuje tylko [określone formaty audio.](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-speech-to-text#audio-formats) 

Aby uzyskać więcej informacji na temat usługi zamiany tekstu na mowę i jak rozpocząć pracę, zobacz [Co to jest zamiana mowy na tekst?](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-to-text)

## <a name="known-differences-from-deprecated-services"></a>Znane różnice w stosunku do przestarzałych usług 

Przekonasz się, że indeksator wideo, usługi Azure Media Services w wersji 3 AudioAnalyzerPreset i usługi rozpoznawania mowy usług Cognitive Services są bardziej niezawodne i zapewniają lepszą jakość danych wyjściowych niż wycofane procesory Azure Media Indexer 1 i Azure Media Indexer 2.  

Niektóre znane różnice obejmują: 

* Usługi mowy usług Cognitive Services nie obsługują wyodrębniania słów kluczowych. Jednak video Indexer i Media Services v3 AudioAnalyzerPreset zarówno oferują bardziej niezawodny zestaw słów kluczowych w formacie JSON pliku. 

## <a name="need-help"></a>Potrzebujesz pomocy?

Możesz otworzyć bilet pomocy technicznej, przechodząc do [nowego żądania pomocy technicznej](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

## <a name="next-steps"></a>Następne kroki

* [Starsze składniki](legacy-components.md)
* [Strona cennika](https://azure.microsoft.com/pricing/details/media-services/#encoding)


