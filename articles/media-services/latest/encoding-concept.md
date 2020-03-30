---
title: Kodowanie wideo i audio za pomocą usługi Media Services
titleSuffix: Azure Media Services
description: W tym artykule wyjaśniono kodowanie wideo i audio za pomocą usługi Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 09/10/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 6e1c9aa5c2e049d5fc1ebd8bf745417f56d232ec
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366574"
---
# <a name="encoding-video-and-audio-with-media-services"></a>Kodowanie wideo i audio za pomocą usługi Media Services

Termin kodowanie w umiaźnej umiejsce ma zastosowanie do procesu konwersji plików zawierających cyfrowe wideo i/lub audio z jednego standardowego formatu na inny, w celu (a) zmniejszenia rozmiaru plików i/lub (b) tworzenia formatu zgodnego z szeroką gamą urządzeń i aplikacji. Ten proces jest również określany jako kompresja wideo lub transkodowanie. Zobacz [kompresji danych](https://en.wikipedia.org/wiki/Data_compression) i [Co to jest kodowanie i transkodowanie?](https://www.streamingmedia.com/Articles/Editorial/What-Is-/What-Is-Encoding-and-Transcoding-75025.aspx)

Filmy wideo są zazwyczaj dostarczane do urządzeń i aplikacji przez [progresywne pobieranie](https://en.wikipedia.org/wiki/Progressive_download) lub za pośrednictwem [adaptacyjnego przesyłania strumieniowego szybkości transmisji bitów.](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)

> [!IMPORTANT]
> Usługi Media Services nie rozliczają się za anulowane lub błędy zadań. Na przykład zadanie, które osiągnęło 50% postępu i zostało anulowane, nie jest rozliczane w wysokości 50% minut zlecenia. Opłaty są naliczane tylko za gotowe zadania.

* Aby dostarczać pliki do pobrania progresywnego, można użyć usługi Azure Media Services do konwersji pliku multimediów cyfrowych (antresoli) na plik [MP4,](https://en.wikipedia.org/wiki/MPEG-4_Part_14) który zawiera wideo zakodowane za pomocą kodera-dekodera [H.264,](https://en.wikipedia.org/wiki/H.264/MPEG-4_AVC) oraz dźwięk zakodowany za pomocą kodera kodu [AAC.](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) Ten plik MP4 jest zapisywany na koncie magazynu. Można użyć interfejsów API usługi Azure Storage lub zestawów SDK (na przykład [interfejsu API REST magazynu](../../storage/common/storage-rest-api-auth.md) lub [.NET SDK](../../storage/blobs/storage-quickstart-blobs-dotnet.md)), aby pobrać plik bezpośrednio. Jeśli utworzono zasób wyjściowy o określonej nazwie kontenera w magazynie, użyj tej lokalizacji. W przeciwnym razie można użyć usługi Media Services do [wyświetlenia adresów URL kontenerów zasobów](https://docs.microsoft.com/rest/api/media/assets/listcontainersas). 
* Aby przygotować zawartość do dostarczenia za pomocą adaptacyjnego przesyłania strumieniowego szybkości transmisji bitów, plik antresoli musi być zakodowany z wieloma szybkościami transmisji bitów (od najwyższej do najniższej). Aby zapewnić pełne wdzięku przejście jakości, rozdzielczość wideo jest obniżana wraz z obniżeniem szybkości transmisji bitów. Skutkuje to tak zwaną drabiną kodowania – tabelą rozdzielczości i szybkości transmisji bitów (patrz [automatycznie generowana adaptacyjna drabina szybkości transmisji bitów).](autogen-bitrate-ladder.md) Za pomocą usługi Media Services można zakodować pliki mezzanine z wieloma szybkościami transmisji bitów. W ten sposób otrzymasz zestaw plików MP4 i skojarzonych plików konfiguracyjnych przesyłania strumieniowego zapisanych na koncie magazynu. Następnie można użyć funkcji [dynamicznego pakowania](dynamic-packaging-overview.md) w umiań Media Services, aby dostarczyć wideo za pośrednictwem protokołów przesyłania strumieniowego, takich jak [MPEG-DASH](https://en.wikipedia.org/wiki/Dynamic_Adaptive_Streaming_over_HTTP) i [HLS.](https://en.wikipedia.org/wiki/HTTP_Live_Streaming) Wymaga to utworzenia [lokalizatora przesyłania strumieniowego](streaming-locators-concept.md) i tworzenia adresów URL przesyłania strumieniowego odpowiadających obsługiwanym protokołom, które następnie mogą być przekazywane do urządzeń/aplikacji na podstawie ich możliwości.

Na poniższym diagramie przedstawiono przepływ pracy dotyczący kodowania na żądanie za pomocą dynamicznego pakowania.

![Przepływ pracy do kodowania na żądanie z dynamicznym pakowaniem](./media/dynamic-packaging-overview/media-services-dynamic-packaging.svg)

W tym temacie przedstawiono wskazówki dotyczące kodowania zawartości za pomocą usługi Media Services w wersji 3.

## <a name="transforms-and-jobs"></a>Transformacje i zadania

Aby zakodować za pomocą usługi Media Services w wersji 3, należy utworzyć [transformowanie](https://docs.microsoft.com/rest/api/media/transforms) i [zadanie](https://docs.microsoft.com/rest/api/media/jobs). Transformacja definiuje przepis na ustawienia kodowania i wyjścia; zadanie jest przykładem przepisu. Aby uzyskać więcej informacji, zobacz [Przekształcenia i zadania](transforms-jobs-concept.md).

Podczas kodowania za pomocą usługi Media Services można użyć ustawień wstępnych, aby poinformować kodera, jak należy przetwarzać wejściowe pliki multimedialne. W umiejce w umiaście 3 program Media Services służy do kodowania standardowego kodera. Można na przykład określić rozdzielczość wideo i/lub liczbę kanałów audio w zakodowanej zawartości.

Możesz szybko rozpocząć pracę z jednym z zalecanych wbudowanych ustawień wstępnych opartych na najlepszych praktykach branżowych lub możesz utworzyć niestandardowe ustawienia predefiniowane, aby kierować określone wymagania dotyczące scenariusza lub urządzenia. Aby uzyskać więcej informacji, zobacz [Kodowanie za pomocą niestandardowego przekształcania](customize-encoder-presets-how-to.md).

Począwszy od stycznia 2019 r., podczas kodowania za pomocą standardowego kodera do tworzenia plików MP4, nowy plik .mpi jest generowany i dodawany do wyjściowego zasobu. Ten plik MPI ma na celu zwiększenie wydajności dla [dynamicznego pakowania](dynamic-packaging-overview.md) i przesyłania strumieniowego scenariuszy.

> [!NOTE]
> Nie należy modyfikować ani usuwać pliku MPI ani przyjmować żadnych zależności w usłudze od istnienia (lub nie) takiego pliku.

### <a name="creating-job-input-from-an-https-url"></a>Tworzenie danych wejściowych zadania z adresu URL HTTPS

Gdy przesyłasz zadania do przetwarzania filmów, musisz poinformować media services, gdzie można znaleźć wejściowy film wideo. Jedną z opcji jest określenie adresu URL HTTPS jako danych wejściowych zadania. Obecnie usługi Media Services w wersji 3 nie obsługuje kodowania transferu fragmentaryzowanego za pośrednictwem adresów URL HTTPS.

#### <a name="examples"></a>Przykłady

* [Kodowanie z adresu URL HTTPS za pomocą platformy .NET](stream-files-dotnet-quickstart.md)
* [Kodowanie z adresu URL HTTPS z REST](stream-files-tutorial-with-rest.md)
* [Kodowanie z adresu URL HTTPS za pomocą interfejsu wiersza polecenia](stream-files-cli-quickstart.md)
* [Kodowanie z adresu URL HTTPS za pomocą pliku Node.js](stream-files-nodejs-quickstart.md)

### <a name="creating-job-input-from-a-local-file"></a>Tworzenie danych wejściowych zadania z pliku lokalnego

Wejściowy klip wideo może być przechowywany jako zasób usługi Media Service, w którym to przypadku utworzysz zasób wejściowy na podstawie pliku (przechowywane lokalnie lub w magazynie obiektów Blob platformy Azure).

#### <a name="examples"></a>Przykłady

[Kodowanie pliku lokalnego przy użyciu wbudowanych ustawień predefiniowanych](job-input-from-local-file-how-to.md)

### <a name="creating-job-input-with-subclipping"></a>Tworzenie danych wejściowych zadania za pomocą podlicznika

Podczas kodowania wideo można określić, aby również przyciąć lub przyciąć plik źródłowy i utworzyć dane wyjściowe, które ma tylko żądaną część wejściowego wideo. Ta funkcja działa z dowolnego [przekształcenia,](https://docs.microsoft.com/rest/api/media/transforms) który jest zbudowany przy użyciu [wbudowanych wbudowanychstandardencoderpreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) presetów lub [standardencoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) presetów.

Można określić, aby utworzyć [zadanie](https://docs.microsoft.com/rest/api/media/jobs/create) z pojedynczym klipem wideo na żądanie lub archiwum na żywo (zarejestrowane zdarzenie). Dane wejściowe zadania mogą być adresem URL zasobu lub HTTPS.

> [!TIP]
> Jeśli chcesz przesyłać strumieniowo podpoprzet wideo bez ponownego kodowania wideo, rozważ użycie [manifestów filtrowania wstępnego za pomocą pakietu Dynamic Packager](filters-dynamic-manifest-overview.md).

#### <a name="examples"></a>Przykłady

Zobacz przykłady:

* [Subclip wideo z .NET](subclip-video-dotnet-howto.md)
* [Subclip wideo z REST](subclip-video-rest-howto.md)

## <a name="built-in-presets"></a>Wbudowane ustawienia wstępne

Usługi Media Services obsługują następujące wbudowane ustawienia kodowania:  

### <a name="builtinstandardencoderpreset"></a>WbudowanyStandardEncoderPreset

[BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) służy do ustawiania wbudowanego ustawienia predefiniowanego do kodowania wejściowego wideo za pomocą standardowego kodera.

Następujące ustawienia wstępne są obecnie obsługiwane:

- **EncoderNamedPreset.AACGoodQualityAudio**: produkuje pojedynczy plik MP4 zawierający tylko dźwięk stereo zakodowany przy 192 kbps.
- **EncoderNamedPreset.AdaptiveStreaming** (zalecane): Aby uzyskać więcej informacji, zobacz [automatyczne generowanie drabiny szybkości transmisji bitów](autogen-bitrate-ladder.md).
- **EncoderNamedPreset.ContentAwareEncodingExperimental**: udostępnia eksperymentalne ustawienia predefiniowane dla kodowania z uwzględnieniem zawartości. Biorąc pod uwagę wszelkie zawartości wejściowej, usługa próbuje automatycznie określić optymalną liczbę warstw i odpowiednie ustawienia szybkości transmisji bitów i rozdzielczości dla dostarczania przez adaptacyjne przesyłanie strumieniowe. Podstawowe algorytmy będą nadal ewoluować w czasie. Wyjście będzie zawierać pliki MP4 z wideo i audio z przeplotem. Aby uzyskać więcej informacji, zobacz [Eksperymentalne ustawienia predefiniowane kodowania z uwzględnieniem zawartości](content-aware-encoding.md).
- **EncoderNamedPreset.H264MultipleBitrate1080p**: produkuje zestaw ośmiu plików MP4 wyrównanych do GOP, od 6000 kbps do 400 kbps i stereo audio AAC. Rozdzielczość zaczyna się od 1080p i spada do 360p.
- **EncoderNamedPreset.H264MultipleBitrate720p**: produkuje zestaw sześciu plików MP4 wyrównanych do GOP, od 3400 kb/s do 400 kb/s i stereofoniczny dźwięk AAC. Rozdzielczość zaczyna się od 720p i spada do 360p.
- **EncoderNamedPreset.H264MultipleBitrateSD**: produkuje zestaw pięciu plików MP4 wyrównanych do GOP, od 1600 kbps do 400 kbps i stereofoniczny dźwięk AAC. Rozdzielczość zaczyna się od 480p i spada do 360p.
- **EncoderNamedPreset.H264SingleBitrate1080p**: tworzy plik MP4, w którym wideo jest kodek H.264 na 6750 kbps i wysokość obrazu 1080 pikseli, a dźwięk stereo jest kodowany kodekem AAC-LCC na 64 kbps.
- **EncoderNamedPreset.H264SingleBitrate720p**: tworzy plik MP4, w którym wideo jest kodowany kodekem H.264 przy 4500 kbps i wysokości obrazu 720 pikseli, a dźwięk stereo jest kodowany kodekem AAC-LC z prędkością 64 kbps.
- **EncoderNamedPreset.H264SingleBitrateSD**: tworzy plik MP4, w którym wideo jest kodowany kodekem H.264 przy 2200 kbps i wysokości obrazu 480 pikseli, a dźwięk stereo jest kodowany kodekem AAC-LC z prędkością 64 kbps.

Aby wyświetlić listę najbardziej aktualnych ustawień predefiniowanych, zobacz [wbudowane ustawienia predefiniowane, które mają być używane do kodowania filmów wideo.](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#encodernamedpreset)

Aby zobaczyć, jak używane są ustawienia predefiniowane, zobacz [Przekazywanie, kodowanie i przesyłanie strumieniowe plików](stream-files-tutorial-with-api.md).

### <a name="standardencoderpreset"></a>StandardEncoderPreset

[StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) opisuje ustawienia, które mają być używane podczas kodowania wejściowego wideo za pomocą kodera standardowego. Użyj tego ustawienia predefiniowane podczas dostosowywania ustawień predefiniowanych transformacji.

#### <a name="considerations"></a>Zagadnienia do rozważenia

Podczas tworzenia niestandardowych ustawień predefiniowanych stosuje się następujące zagadnienia:

- Wszystkie wartości wysokości i szerokości zawartości AVC muszą być wielokrotnością czterech.
- W usłudze Azure Media Services w wersji 3 wszystkie kodowanie bitów są w bitach na sekundę. Różni się to od ustawień wstępnych w naszych interfejsach API v2, które używały kilobitów na sekundę jako jednostki. Na przykład jeśli szybkość transmisji bitów w wersji 2 została określona jako 128 (kilobitów/sekundę), w wersji 3 zostanie ustawiona na 128000 (bity/sekundę).

### <a name="customizing-presets"></a>Dostosowywanie ustawień predefiniowanych

Usługi Media Services w pełni obsługuje dostosowywanie wszystkich wartości w ustawieniach predefiniowanych, aby spełnić określone potrzeby i wymagania dotyczące kodowania. Przykłady, które pokazują, jak dostosować ustawienia wstępne kodera, zobacz poniższą listę:

#### <a name="examples"></a>Przykłady

- [Dostosowywanie ustawień wstępnych za pomocą platformy .NET](customize-encoder-presets-how-to.md)
- [Dostosowywanie ustawień wstępnych za pomocą interfejsu wiersza polecenia](custom-preset-cli-howto.md)
- [Dostosowywanie ustawień wstępnych za pomocą restu](custom-preset-rest-howto.md)

## <a name="preset-schema"></a>Wstępnie ustawiony schemat

W usłudze Media Services w wersji 3 ustawienia wstępne są silnie typizowanymi jednostkami w samym interfejsie API. Definicję "schematu" dla tych obiektów można znaleźć w [specyfikacji otwartego interfejsu API (lub Swagger).](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01) Można również wyświetlić wstępnie ustawione definicje (takie jak **StandardEncoderPreset)** w [interfejsie API REST](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset), [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.standardencoderpreset?view=azure-dotnet) (lub innej dokumentacji referencyjnej zestawu SDK usługi Media Services w wersji 3).

## <a name="scaling-encoding-in-v3"></a>Skalowanie kodowania w wersji 3

Aby skalować przetwarzanie multimediów, zobacz [Skalowanie przy obliczu polecenia .](media-reserved-units-cli-how-to.md)

## <a name="billing"></a>Rozliczenia

Usługi Media Services nie rozliczają się za anulowane lub błędy zadań. Na przykład zadanie, które osiągnęło 50% postępu i zostało anulowane, nie jest rozliczane w wysokości 50% minut zlecenia. Opłaty są naliczane tylko za gotowe zadania.

Aby uzyskać więcej informacji, zobacz [cennik](https://azure.microsoft.com/pricing/details/media-services/).

## <a name="ask-questions-give-feedback-get-updates"></a>Zadawaj pytania, przekazuj opinie, otrzyj aktualizacje

Zapoznaj się z artykułem [społeczności usługi Azure Media Services,](media-services-community.md) aby zobaczyć różne sposoby zadawania pytań, przekazywania opinii i otrzymywać aktualizacje dotyczące usługi Media Services.

## <a name="next-steps"></a>Następne kroki

* [Przekazywanie, kodowanie i przesyłanie strumieniowe przy użyciu usługi Media Services](stream-files-tutorial-with-api.md).
* [Kodowanie z adresu URL HTTPS przy użyciu wbudowanych ustawień predefiniowanych](job-input-from-http-how-to.md).
* [Kodowanie pliku lokalnego przy użyciu wbudowanych ustawień predefiniowanych](job-input-from-local-file-how-to.md).
* [Skompiluj niestandardowe ustawienia wstępne, aby kierować reklamy na określony scenariusz lub wymagania urządzenia](customize-encoder-presets-how-to.md).
