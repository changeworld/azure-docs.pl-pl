---
title: Jak korzystać z transkrypcji usługi Batch — usługa mowy
titleSuffix: Azure Cognitive Services
description: Transkrypcja usługi Batch jest idealnym rozwiązaniem, jeśli chcesz transkrypcja dużą ilość dźwięku w magazynie, na przykład obiekty blob platformy Azure. Za pomocą dedykowanego interfejsu API REST można wskazać pliki audio z identyfikatorem URI sygnatury dostępu współdzielonego (SAS) i asynchronicznie otrzymywać transkrypcje.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: panosper
ms.openlocfilehash: 158a99b1691e59fa58207f3c9291ca9d37a6679c
ms.sourcegitcommit: 36eb583994af0f25a04df29573ee44fbe13bd06e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2019
ms.locfileid: "74538119"
---
# <a name="why-use-batch-transcription"></a>Dlaczego warto używać transkrypcji partii?

Transkrypcja usługi Batch jest idealnym rozwiązaniem, jeśli chcesz transkrypcja dużą ilość dźwięku w magazynie, na przykład obiekty blob platformy Azure. Za pomocą dedykowanego interfejsu API REST można wskazać pliki audio z identyfikatorem URI sygnatury dostępu współdzielonego (SAS) i asynchronicznie otrzymywać transkrypcje.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="subscription-key"></a>Klucz subskrypcji

Podobnie jak w przypadku wszystkich funkcji usługi Speech, można utworzyć klucz subskrypcji z [Azure Portal](https://portal.azure.com) , postępując zgodnie z [przewodnikiem](get-started.md)wprowadzenie. Jeśli planujesz pobieranie transkrypcji z modeli bazowych, należy utworzyć klucz, aby to zrobić.

>[!NOTE]
> Do korzystania z transkrypcji wsadowej jest wymagane użycie standardowej subskrypcji (S0) dla usług mowy. Bezpłatne klucze subskrypcji (F0) nie będą działały. Aby uzyskać dodatkowe informacje, zobacz [Cennik i limity](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

### <a name="custom-models"></a>Modele niestandardowe

Jeśli planujesz dostosowanie modeli akustycznych lub modelowych, postępuj zgodnie z instrukcjami w temacie [Dostosowywanie modeli akustycznych](how-to-customize-acoustic-models.md) i [Dostosowywanie modeli języków](how-to-customize-language-model.md). Aby można było użyć utworzonych modeli w transkrypcji wsadowej, potrzebne są ich identyfikatory modeli. Ten identyfikator nie jest IDENTYFIKATORem punktu końcowego, który znajduje się w widoku szczegółów punktu końcowego, jest IDENTYFIKATORem modelu, który można pobrać po wybraniu szczegółów dla modeli.

## <a name="the-batch-transcription-api"></a>Interfejs API transkrypcji usługi Batch

Interfejs API transkrypcji usługi Batch oferuje asynchroniczne transkrypcje zamiany mowy na tekst wraz z dodatkowymi funkcjami. Jest to interfejs API REST, który udostępnia metody dla:

1. Tworzenie żądań przetwarzania wsadowego
1. Stan zapytania
1. Pobieranie transkrypcji

> [!NOTE]
> Interfejs API transkrypcji usługi Batch jest idealnym rozwiązaniem w przypadku centrów wywołań, które zwykle łączą tysiące godzin audio. Ułatwia to transkrypcja dużych ilości nagrań dźwiękowych.

### <a name="supported-formats"></a>Obsługiwane formaty

Interfejs API transkrypcji usługi Batch obsługuje następujące formaty:

| Format | Wymaga | Multimedia | Częstotliwość próbkowania |
|--------|-------|---------|-------------|
| PLIKI | MODULE PCM | 16-bitowa | 8 lub 16 kHz, mono, stereo |
| FORMATU | MODULE PCM | 16-bitowa | 8 lub 16 kHz, mono, stereo |
| OGG | OPUS | 16-bitowa | 8 lub 16 kHz, mono, stereo |

W przypadku strumieni stereofonicznych audio interfejs API transkrypcji partii dzieli lewy i prawy kanał podczas transkrypcji. Dwa pliki JSON z wynikiem są tworzone na podstawie jednego kanału. Sygnatury czasowe na wypowiedź umożliwiają deweloperowi utworzenie uporządkowanej końcowej transkrypcji. To przykładowe żądanie zawiera właściwości dotyczące filtrowania niewulgarności, interpunkcji i znaczników czasu na poziomie wyrazów.

### <a name="configuration"></a>Konfigurowanie

Parametry konfiguracji są podane jako dane JSON:

```json
{
  "recordingsUrl": "<URL to the Azure blob to transcribe>",
  "models": [{"Id":"<optional acoustic model ID>"},{"Id":"<optional language model ID>"}],
  "locale": "<locale to use, for example en-US>",
  "name": "<user defined name of the transcription batch>",
  "description": "<optional description of the transcription>",
  "properties": {
    "ProfanityFilterMode": "Masked",
    "PunctuationMode": "DictatedAndAutomatic",
    "AddWordLevelTimestamps" : "True",
    "AddSentiment" : "True"
  }
}
```

> [!NOTE]
> Interfejs API transkrypcji usługi Batch używa usługi REST do żądania transkrypcji, stanu i skojarzonych wyników. Możesz użyć interfejsu API z dowolnego języka. W następnej sekcji opisano sposób użycia interfejsu API.

### <a name="configuration-properties"></a>Właściwości konfiguracji

Użyj tych opcjonalnych właściwości, aby skonfigurować transkrypcję:

| Parametr | Opis |
|-----------|-------------|
| `ProfanityFilterMode` | Określa sposób obsługi niezbyt wulgarności w wynikach rozpoznawania. Akceptowane wartości to `None`, które uniemożliwiają filtrowanie z powodu niedostępności, `masked` które zastępują niewulgarne gwiazdkami, `removed`, które usuwają z wyniku wszystkie niezbyt wulgarne dane, lub `tags`, które dodaje Tagi "wulgarności". Ustawieniem domyślnym jest `masked`. |
| `PunctuationMode` | Określa, jak obsłużyć interpunkcję w wynikach rozpoznawania. Akceptowane wartości to `None`, które wyłączają interpunkcję, `dictated`, która oznacza jawną interpunkcję, `automatic`, która umożliwia dekoderowi odliczanie z interpunkcją, lub `dictatedandautomatic`, które oznacza podyktowane znaczniki interpunkcyjne lub automatyczne. |
 | `AddWordLevelTimestamps` | Określa, czy sygnatury czasowe poziomu słowa mają być dodawane do danych wyjściowych. Akceptowane wartości to `true`, które umożliwiają wyłączanie sygnatur czasowych na poziomie programu Word i `false` (wartość domyślna). |
 | `AddSentiment` | Należy dodać tonacji do wypowiedź. Akceptowane wartości to `true`, które umożliwiają wyłączenie opcji tonacji na wypowiedź i `false` (wartość domyślna). |
 | `AddDiarization` | Określa, że analiza diarization powinna zostać przeprowadzona na wejściu, który powinien być kanałem mono zawierającym dwa głosy. Akceptowane wartości to `true`, które umożliwiają wyłączenie programu diarization i `false` (wartość domyślna). Wymaga również, aby `AddWordLevelTimestamps` mieć wartość true.|

### <a name="storage"></a>Usługa Storage

Transkrypcja usługi Batch obsługuje [usługę Azure Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) do odczytu i zapisywania transkrypcji w magazynie.

## <a name="speaker-separation-diarization"></a>Separacja głośników (Diarization)

Diarization to proces oddzielania głośników w części audio. Nasz potok wsadowy obsługuje Diarization i jest w stanie rozpoznawać dwa głośniki w nagraniach kanału mono.

Aby zażądać przetworzenia żądania transkrypcji audio dla diarization, wystarczy dodać odpowiedni parametr w żądaniu HTTP, jak pokazano poniżej.

 ```json
{
  "recordingsUrl": "<URL to the Azure blob to transcribe>",
  "models": [{"Id":"<optional acoustic model ID>"},{"Id":"<optional language model ID>"}],
  "locale": "<locale to us, for example en-US>",
  "name": "<user defined name of the transcription batch>",
  "description": "<optional description of the transcription>",
  "properties": {
    "AddWordLevelTimestamps" : "True",
    "AddDiarization" : "True"
  }
}
```

Sygnatury czasowe poziomu słów również muszą mieć wartość "włączone", ponieważ parametry w powyższym żądaniu wskazują. 

Odpowiedni dźwięk będzie zawierać głośniki identyfikowane przez liczbę (obecnie obsługujemy tylko dwa głosy, więc głośniki będą identyfikowane jako "prelegent 1" i "prelegent 2"), a następnie dane wyjściowe transkrypcji.

Należy również zauważyć, że Diarization nie jest dostępny w nagraniach stereo. Ponadto wszystkie dane wyjściowe JSON będą zawierać tag głośników. Jeśli diarization nie jest używany, w danych wyjściowych JSON zostanie wyświetlony element "Prelegent: null".

> [!NOTE]
> Diarization jest dostępny we wszystkich regionach i dla wszystkich ustawień regionalnych.

## <a name="sentiment"></a>Opinia

Tonacji to nowa funkcja w interfejsie API transkrypcji usługi Batch i jest ważną funkcją w domenie usługi Call Center. Klienci mogą używać parametrów `AddSentiment` do ich żądań, aby

1.  Uzyskaj wgląd w szczegółowe informacje na temat zadowolenia klientów
2.  Uzyskaj wgląd w wydajność agentów (zespół przejmowania wywołań)
3.  Lokalizowanie dokładnego punktu w czasie, gdy wywołanie zajęło negatywny kierunek
4.  Określ, co poszło dobrze, gdy wyłączają ujemne wywołania dodatnie
5.  Określ, co Ci klienci i co nie lubię o produkcie lub usłudze

Tonacji jest oceniane dla segmentu audio, gdzie segment audio jest zdefiniowany, gdy czas przepada między początkiem wypowiedź (offset) i wykryciem ostatniego strumienia bajtów. Cały tekst w tym segmencie jest używany do obliczania tonacji. NIE obliczamy żadnych zagregowanych wartości tonacji dla całego wywołania ani całej mowy każdego kanału. Te agregacje pozostały do właściciela domeny w celu dalszej zastosowania.

Tonacji jest zastosowany do formy leksykalnej.

Przykład danych wyjściowych JSON wygląda następująco:

```json
{
  "AudioFileResults": [
    {
      "AudioFileName": "Channel.0.wav",
      "AudioFileUrl": null,
      "SegmentResults": [
        {
          "RecognitionStatus": "Success",
          "ChannelNumber": null,
          "Offset": 400000,
          "Duration": 13300000,
          "NBest": [
            {
              "Confidence": 0.976174,
              "Lexical": "what's the weather like",
              "ITN": "what's the weather like",
              "MaskedITN": "what's the weather like",
              "Display": "What's the weather like?",
              "Words": null,
              "Sentiment": {
                "Negative": 0.206194,
                "Neutral": 0.793785,
                "Positive": 0.0
              }
            }
          ]
        }
      ]
    }
  ]
}
```
Funkcja używa modelu tonacji, który jest obecnie w wersji beta.

## <a name="sample-code"></a>Przykładowy kod

Kompletne przykłady są dostępne w [przykładowym repozytorium GitHub](https://aka.ms/csspeech/samples) w ramach podkatalogu `samples/batch`.

Musisz dostosować przykładowy kod przy użyciu informacji o subskrypcji, regionu usługi, identyfikatora URI sygnatury dostępu współdzielonego, wskazującego plik audio do Transkrypcja, oraz identyfikatorów modeli w przypadku, gdy chcesz użyć niestandardowego modelu akustycznego lub języka.

[!code-csharp[Configuration variables for batch transcription](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchdefinition)]

Przykładowy kod skonfiguruje klienta i prześle żądanie transkrypcji. Następnie będzie sondował informacje o stanie i wydrukować szczegółowe informacje o postępie transkrypcji.

[!code-csharp[Code to check batch transcription status](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchstatus)]

Aby uzyskać szczegółowe informacje o poprzednich wywołaniach, zobacz [dokument struktury Swagger](https://westus.cris.ai/swagger/ui/index). Aby wyświetlić pełny przykład, przejdź do witryny [GitHub](https://aka.ms/csspeech/samples) w podkatalogu `samples/batch`.

Zanotuj konfigurację asynchroniczną dotyczącą ogłaszania audio i uzyskiwania stanu transkrypcji. Tworzony klient jest klientem HTTP platformy .NET. Istnieje `PostTranscriptions` metoda wysyłania szczegółów pliku audio i metody `GetTranscriptions` na potrzeby otrzymywania wyników. `PostTranscriptions` zwraca dojście, a `GetTranscriptions` używa go do utworzenia dojścia w celu uzyskania stanu transkrypcji.

Bieżący przykładowy kod nie określa modelu niestandardowego. Usługa używa modeli bazowych do jego przepisywania pliku lub plików. Aby określić modele, można przekazać tę samą metodę co identyfikatory modelu dla akustycznego i modelu języka.

> [!NOTE]
> W przypadku transkrypcji bazowej nie trzeba deklarować identyfikatora dla modeli bazowych. Jeśli określisz tylko identyfikator modelu języka (bez identyfikatora modelu akustycznego), zostanie automatycznie wybrany zgodny model akustyczny. Jeśli określisz tylko identyfikator modelu akustycznego, zostanie automatycznie wybrany pasujący model języka.

## <a name="download-the-sample"></a>Pobierz przykład

Przykład można znaleźć w katalogu `samples/batch` w [przykładowym repozytorium GitHub](https://aka.ms/csspeech/samples).

> [!NOTE]
> Zadania transkrypcji usługi Batch są planowane według najlepszego nakładu pracy, ale nie ma oszacowania czasu, gdy zadanie zostanie zmienione w stanie uruchomionym. Po uruchomieniu rzeczywista transkrypcja jest przetwarzana szybciej niż w czasie rzeczywistym.

## <a name="next-steps"></a>Następne kroki

* [Pobierz subskrypcję usługi mowy w wersji próbnej](https://azure.microsoft.com/try/cognitive-services/)
