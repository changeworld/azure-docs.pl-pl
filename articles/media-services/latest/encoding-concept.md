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
ms.date: 04/21/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: ed798995807f4037f0127b08e25e04bdd0340d42
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62103608"
---
# <a name="encoding-with-media-services"></a>Kodowanie za pomocą usługi Media Services

Usługa Azure Media Services umożliwia kodowanie do plików MP4 wysokiej jakości multimedialnych plików, więc zawartość może być odtwarzany na podstawie różnych przeglądarek i urządzeń. Pomyślne zadania kodowania usługi Media Services tworzy dane wyjściowe zasobów ze zbiorem każdego pliku MP4 z adaptacyjną szybkością transmisji bitów i przesyłanie strumieniowe plików konfiguracyjnych. Pliki konfiguracji zawierają ISM, .ismc, .mpi i innych plików, których nie należy modyfikować. Po zakończeniu zadania kodowania, możesz korzystać z zalet [funkcję dynamicznego tworzenia pakietów](dynamic-packaging-overview.md) i rozpocząć przesyłanie strumieniowe.

Aby wprowadzić filmów wideo w danych wyjściowych dostępne dla klientów do odtwarzania elementu zawartości, należy utworzyć **lokalizatora przesyłania strumieniowego** i tworzenie adresów URL przesyłania strumieniowego. Następnie na podstawie określonego formatu w manifeście, klientom strumień jest dostarczany za protokół, które wybrali.

Na poniższym diagramie przedstawiono przesyłania strumieniowego na żądanie za pomocą funkcji dynamicznego tworzenia pakietów przepływu pracy.

![Dynamiczne tworzenie pakietów](./media/dynamic-packaging-overview/media-services-dynamic-packaging.svg)

Ten temat zawiera wskazówki na temat kodowania zawartości za pomocą usługi Media Services v3.

## <a name="transforms-and-jobs"></a>Transformacje i zadania

Kodowanie za pomocą usługi Media Services v3, musisz utworzyć [Przekształcanie](https://docs.microsoft.com/rest/api/media/transforms) i [zadania](https://docs.microsoft.com/rest/api/media/jobs). Przekształcenie definiuje przepis na kodowania ustawień i danych wyjściowych, a zadanie jest wystąpieniem przepisu. Aby uzyskać więcej informacji, zobacz [transformacje i zadania](transforms-jobs-concept.md)

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

## <a name="scaling-encoding-in-v3"></a>Skalowanie kodowania w wersji 3

Skalowanie przetwarzania multimediów, zobacz [skalowanie przy użyciu interfejsu wiersza polecenia](media-reserved-units-cli-how-to.md).

## <a name="provide-feedback"></a>Przekazywanie opinii

Zapoznaj się z [społeczności usługi Azure Media Services](media-services-community.md) artykuł, aby wyświetlić różne sposoby zadawaj pytania, Prześlij opinię i pobrać aktualizacje o usłudze Media Services.

## <a name="next-steps"></a>Kolejne kroki

* [Kodowanie z adresu URL HTTPS, przy użyciu wbudowanych ustawień wstępnych](job-input-from-http-how-to.md)
* [Kodowanie pliku lokalnego za pomocą wbudowanych ustawień wstępnych](job-input-from-local-file-how-to.md)
* [Tworzenie niestandardowego ustawienia wstępnego pod kątem określonych wymagań scenariusza lub urządzenia](customize-encoder-presets-how-to.md)
* [Przekazywanie, kodowanie i przesyłanie strumieniowe przy użyciu usługi Media Services](stream-files-tutorial-with-api.md)
