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
ms.date: 05/10/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 25b3209bed98ea217db9e414caa6f08cee6d8c89
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65761884"
---
# <a name="encoding-with-media-services"></a>Kodowanie za pomocą usługi Media Services

Kodowanie termin w usłudze Media Services dotyczy proces konwersji plików zawierających wideo lub audio z jednego standardowego formatu do innego, mający na celu ułatwienie () zmniejszenie rozmiaru plików i/lub (b) produkujących formatu, który jest zgodny z szerokiej gamy urządzeń i aplikacji. Ten proces jest zwaną także kompresji wideo lub transkodowania. Zobacz [kompresji danych](https://en.wikipedia.org/wiki/Data_compression) i [nowości, kodowanie i Transkodowanie?](https://www.streamingmedia.com/Articles/Editorial/What-Is-/What-Is-Encoding-and-Transcoding-75025.aspx) dalsze omówienie pojęć.

Filmy wideo zwykle są dostarczane do urządzeń i aplikacji przez [pobierania progresywnego](https://en.wikipedia.org/wiki/Progressive_download) lub za pomocą [streaming z adaptacyjną szybkością transmisji bitów](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming). 

* Do świadczenia przez pobierania progresywnego, można użyć usługi Azure Media Services można przekonwertować plik multimedia cyfrowe (plik) do [MP4](https://en.wikipedia.org/wiki/MPEG-4_Part_14) pliku, który zawiera wideo, który został zakodowany za pomocą [H.264](https://en.wikipedia.org/wiki/H.264/MPEG-4_AVC) kodera-dekodera, i dźwięk, który został zakodowany za pomocą [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) kodera-dekodera. Ten plik w formacie MP4, są zapisywane do elementu zawartości w ramach konta magazynu. Możesz użyć interfejsów API usługi Azure Storage lub zestawów SDK (na przykład [interfejsu API REST magazynu](../../storage/common/storage-rest-api-auth.md), [zestawu JAVA SDK](../../storage/blobs/storage-quickstart-blobs-java-v10.md), lub [zestawu .NET SDK](../../storage/blobs/storage-quickstart-blobs-dotnet.md)) do bezpośredniego pobrania plików. Jeśli utworzono dane wyjściowe zasobów o nazwie określonego kontenera w magazynie, na podstawie tej lokalizacji. W przeciwnym razie można użyć usługi Media Services do [listy adresów URL kontenera zasobów](https://docs.microsoft.com/rest/api/media/assets/listcontainersas). 
* Aby przygotować zawartość do dostarczania, streaming z adaptacyjną szybkością transmisji bitów, plik mezzanine musi być zakodowany na wielokrotnych (wysoko lub nisko). Aby zapewnić płynne przejście jakości, zgodnie z szybkości transmisji bitów jest obniżony, więc rozdzielczości wideo. Skutkuje to tak zwane drabiny kodowania — spis rozdzielczości i szybkości transmisji (zobacz [drabiny szybkości transmisji bitów adaptacyjnego automatycznie generowanej](autogen-bitrate-ladder.md)). Usługa Media Services umożliwia kodowanie Twoich plików mezzanine wielokrotnych — w ten sposób, zostanie wyświetlony zestaw plików MP4 i skojarzone przesyłania strumieniowego pliki konfiguracyjne, zapisywane do elementu zawartości w ramach konta magazynu. Następnie można użyć [funkcję dynamicznego tworzenia pakietów](dynamic-packaging-overview.md) zdolności w usłudze Media Services do dostarczania wideo za pośrednictwem przesyłania strumieniowego protokołów, takich jak [MPEG-DASH](https://en.wikipedia.org/wiki/Dynamic_Adaptive_Streaming_over_HTTP) i [HLS](https://en.wikipedia.org/wiki/HTTP_Live_Streaming). To wymaga utworzenia [lokalizatora przesyłania strumieniowego](streaming-locators-concept.md) i tworzenie adresów URL odpowiadający obsługiwanych protokołów, które następnie mogą być przekazywane do urządzenia/aplikacje oparte na ich możliwości przesyłania strumieniowego.

Na poniższym diagramie przedstawiono przepływ pracy na żądanie kodowania za pomocą funkcji dynamicznego tworzenia pakietów.

![Dynamiczne tworzenie pakietów](./media/dynamic-packaging-overview/media-services-dynamic-packaging.png)

Ten temat zawiera wskazówki na temat kodowania zawartości za pomocą usługi Media Services v3.

## <a name="transforms-and-jobs"></a>Transformacje i zadania

Kodowanie za pomocą usługi Media Services v3, musisz utworzyć [Przekształcanie](https://docs.microsoft.com/rest/api/media/transforms) i [zadania](https://docs.microsoft.com/rest/api/media/jobs). Transformacja definiuje przepis na kodowania ustawienia i dane wyjściowe; zadanie jest wystąpieniem przepisu. Aby uzyskać więcej informacji, zobacz [transformacje i zadania](transforms-jobs-concept.md)

Podczas kodowania za pomocą usługi Media Services, umożliwia wstępne Poinformuj kodera w przetwarzaniu multimedialnych plików wejściowych. Na przykład można określić rozdzielczość wideo i/lub liczbę kanałów audio, który ma w zakodowanej zawartości. 

Użytkownik może szybko rozpocząć pracę z jedną z zalecanych wbudowane ustawienia wstępne, oparte na najlepszych branżowych rozwiązań lub można utworzyć niestandardowe ustawienie wstępne pod kątem określonych wymagań scenariusza lub urządzenia. Aby uzyskać więcej informacji, zobacz [kodowanie przy użyciu niestandardowej transformacji](customize-encoder-presets-how-to.md). 

Począwszy od stycznia 2019 r, podczas kodowania za pomocą usługi Media Encoder Standard do tworzenia plików w formacie MP4, nowy plik .mpi jest generowane i dodawane do wyjściowego elementu zawartości. Ten plik MPI ma na celu poprawę wydajności [funkcję dynamicznego tworzenia pakietów](dynamic-packaging-overview.md) i przesyłania strumieniowego scenariuszy.

> [!NOTE]
> Nie należy modyfikować lub usuń plik MPI lub wykonać żadnych zależności usługi o istnieniu (lub nie) z takiego pliku.

## <a name="built-in-presets"></a>Wbudowane ustawienia wstępne

Usługa Media Services obsługuje obecnie następujące wbudowane ustawienia wstępne kodowania:  

### <a name="builtinstandardencoderpreset-preset"></a>Ustawienie wstępne BuiltInStandardEncoderPreset

[BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) służy do ustawiania wbudowaną ustawienie wstępne kodowania wejściowy plik wideo za pomocą kodera w warstwie standardowa. 

Obecnie obsługiwane są następujące ustawienia:

- **EncoderNamedPreset.AACGoodQualityAudio** — tworzy pojedynczego pliku MP4, zawierający tylko dźwięk stereo zakodowane w 192 kb/s.
- **EncoderNamedPreset.AdaptiveStreaming** (zalecane). Aby uzyskać więcej informacji, zobacz [automatyczne generowanie drabiny szybkości transmisji bitów](autogen-bitrate-ladder.md).
- **EncoderNamedPreset.ContentAwareEncodingExperimental** — udostępnia eksperymentalne ustawienie wstępne kodowania zawartości. Podana zawartość danych wejściowych, usługa spróbuje automatycznie określić optymalną liczbę warstw, odpowiednią szybkość transmisji bitów i ustawień rozpoznawania dostarczania przez adaptacyjnego przesyłania strumieniowego. Algorytmy podstawowych będzie ewoluować wraz z upływem czasu. Dane wyjściowe będą zawierać pliki MP4 z przeplotem audio i wideo. Aby uzyskać więcej informacji, zobacz [eksperymentalne ustawienie wstępne kodowania zawartości](cae-experimental.md).
- **EncoderNamedPreset.H264MultipleBitrate1080p** — tworzy zbiór 8 wyrównane GOP pliki MP4 z, od 6000 KB/s do 400 KB/s i stereo AAC audio. Rozdzielczość rozpoczyna się od 1080p i prowadzi w dół do 360 p.
- **EncoderNamedPreset.H264MultipleBitrate720p** — tworzy zbiór 6 wyrównane GOP pliki MP4 z, od 3400 KB/s do 400 KB/s i stereo AAC audio. Rozdzielczość rozpoczyna się od 720p i prowadzi w dół do 360 p.
- **EncoderNamedPreset.H264MultipleBitrateSD** — tworzy zbiór 5 wyrównane GOP pliki MP4 z, od 1600 KB/s do 400 KB/s i stereo AAC audio. Rozdzielczość rozpoczyna się od 480p i prowadzi w dół do 360 p.
- **EncoderNamedPreset.H264SingleBitrate1080p** — tworzy plik w formacie MP4, gdzie wideo jest zakodowane za pomocą H.264 kodera-dekodera 6750 KB/s i wysokość obrazu, 1080 pikseli, a stereo audio jest zaszyfrowana przy użyciu kodera-dekodera AAC-LC, na 64 KB/s.
- **EncoderNamedPreset.H264SingleBitrate720p** — tworzy plik w formacie MP4, gdzie wideo jest zakodowane za pomocą H.264 kodera-dekodera 4500 KB/s i wysokość obrazu, 720 pikseli i stereo audio jest zaszyfrowana przy użyciu kodera-dekodera AAC-LC, na 64 KB/s.
- **EncoderNamedPreset.H264SingleBitrateSD** — tworzy plik w formacie MP4, gdzie wideo jest kodowany za pomocą H.264 kodera-dekodera 2200 KB/s i wysokość obrazu w pikselach 480 i stereo audio jest zaszyfrowana przy użyciu kodera-dekodera AAC-LC, na 64 KB/s.

Aby wyświetlić najbardziej aktualne listy ustawień domyślnych, zobacz [wbudowane ustawienia ma być używany do kodowania wideo](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#encodernamedpreset).

Aby zobaczyć, jak są używane ustawienia wstępne, zapoznaj się z [przekazywanie, kodowanie i przesyłanie strumieniowe plików](stream-files-tutorial-with-api.md).

### <a name="standardencoderpreset-preset"></a>Ustawienie wstępne StandardEncoderPreset

[StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) opisano ustawienia, które będą używane podczas kodowania wejściowy plik wideo za pomocą kodera w warstwie standardowa. Użyj tego ustawienia wstępnego podczas dostosowywania ustawień wstępnych transformacji. 

#### <a name="considerations"></a>Zagadnienia do rozważenia

Podczas tworzenia niestandardowych ustawień wstępnych należy uwzględnić następujące kwestie:

- Wszystkie wartości dla wysokość i szerokość AVC zawartości musi być wielokrotnością liczby 4.
- W usłudze Azure Media Services v3 są wszystkie kodowania szybkości transmisji w bitach na sekundę. To różni się od ustawień wstępnych przy użyciu interfejsów API w wersji 2, które używane kilobitów na sekundę jako jednostka. Na przykład jeśli szybkość transmisji bitów w wersji 2 została określona jako 128 (kilobity/sekundę), w wersji 3 go będzie miał ustawienie 128000 (bity/sekundę).

#### <a name="examples"></a>Przykłady

Usługa Media Services w pełni obsługuje dostosowywania wszystkie wartości w ustawieniach wstępnych w celu spełnienia specyficznych potrzeb kodowania i wymagań dotyczących usługi. Aby uzyskać przykłady pokazujące, jak dostosować ustawienia wstępne kodera Zobacz:

- [Dostosowywanie ustawień wstępnych przy użyciu platformy .NET](customize-encoder-presets-how-to.md)
- [Dostosowywanie ustawień wstępnych przy użyciu interfejsu wiersza polecenia](custom-preset-cli-howto.md)
- [Dostosowywanie ustawień wstępnych z użyciem usług REST](custom-preset-rest-howto.md)

## <a name="preset-schema"></a>Ustawienie wstępne schematu

W Media Services v3 ustawień wstępnych są silnie typizowanych jednostek w interfejsie API, sam. Można znaleźć definicji "schema" dla tych obiektów w [specyfikacji interfejsu Open API (struktury Swagger)](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01). Można również wyświetlić ustawienia wstępnego definicje (takie jak **StandardEncoderPreset**) w [interfejsu API REST](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset), [zestawu .NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.standardencoderpreset?view=azure-dotnet) (lub inne dokumentacja usługi Media Services v3 zestawu SDK).

## <a name="scaling-encoding-in-v3"></a>Skalowanie kodowania w wersji 3

Skalowanie przetwarzania multimediów, zobacz [skalowanie przy użyciu interfejsu wiersza polecenia](media-reserved-units-cli-how-to.md).

## <a name="ask-questions-give-feedback-get-updates"></a>Zadawaj pytania, Prześlij opinię i pobieranie aktualizacji

Zapoznaj się z [społeczności usługi Azure Media Services](media-services-community.md) artykuł, aby wyświetlić różne sposoby zadawaj pytania, Prześlij opinię i pobrać aktualizacje o usłudze Media Services.

## <a name="next-steps"></a>Kolejne kroki

* [Kodowanie z adresu URL HTTPS, przy użyciu wbudowanych ustawień wstępnych](job-input-from-http-how-to.md)
* [Kodowanie pliku lokalnego za pomocą wbudowanych ustawień wstępnych](job-input-from-local-file-how-to.md)
* [Tworzenie niestandardowego ustawienia wstępnego pod kątem określonych wymagań scenariusza lub urządzenia](customize-encoder-presets-how-to.md)
* [Przekazywanie, kodowanie i przesyłanie strumieniowe przy użyciu usługi Media Services](stream-files-tutorial-with-api.md)
