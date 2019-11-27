---
title: Jak korzystać z transkrypcji usługi Batch — usługa mowy
titleSuffix: Azure Cognitive Services
description: Transkrypcja partii jest idealnym rozwiązaniem, jeśli chcesz także dużej ilości dźwięk w magazynie, takim jak obiektów blob platformy Azure. Za pomocą dedykowanego interfejsu API REST, można wskazać pliki audio, przy użyciu sygnatury dostępu współdzielonego (SAS) identyfikator URI i asynchronicznie otrzymywać transkrypcji.
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
# <a name="why-use-batch-transcription"></a>Dlaczego warto używać usługi Batch transkrypcji?

Transkrypcja partii jest idealnym rozwiązaniem, jeśli chcesz także dużej ilości dźwięk w magazynie, takim jak obiektów blob platformy Azure. Za pomocą dedykowanego interfejsu API REST, można wskazać pliki audio, przy użyciu sygnatury dostępu współdzielonego (SAS) identyfikator URI i asynchronicznie otrzymywać transkrypcji.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="subscription-key"></a>Klucz subskrypcji

Podobnie jak w przypadku wszystkich funkcji usługi Speech, można utworzyć klucz subskrypcji z [Azure Portal](https://portal.azure.com) , postępując zgodnie z [przewodnikiem](get-started.md)wprowadzenie. Jeśli planujesz uzyskiwanie transkrypcje modeli podstawowych w naszym Tworzenie klucza jest wszystko, co należy zrobić.

>[!NOTE]
> Standardowa subskrypcja (S0) dla usług przetwarzania mowy wymagane jest wprowadzenie transkrypcji usługi batch. Bezpłatna subskrypcja kluczy (F0) nie będzie działać. Aby uzyskać dodatkowe informacje, zobacz [Cennik i limity](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

### <a name="custom-models"></a>Modele niestandardowe

Jeśli planujesz dostosowanie modeli akustycznych lub modelowych, postępuj zgodnie z instrukcjami w temacie [Dostosowywanie modeli akustycznych](how-to-customize-acoustic-models.md) i [Dostosowywanie modeli języków](how-to-customize-language-model.md). Aby można było użyć utworzonych modeli w transkrypcji wsadowej, potrzebne są ich identyfikatory modeli. Ten identyfikator nie jest IDENTYFIKATORem punktu końcowego, który znajduje się w widoku szczegółów punktu końcowego, jest IDENTYFIKATORem modelu, który można pobrać po wybraniu szczegółów dla modeli.

## <a name="the-batch-transcription-api"></a>Transkrypcji interfejsu API usługi Batch

Interfejs API transkrypcji usługi Batch oferuje asynchroniczne transkrypcja mowy na tekst, oraz dodatkowe funkcje. To interfejs API REST, który udostępnia metody:

1. Tworzenie żądań przetwarzania wsadowego
1. Stan zapytania
1. Pobieranie transkrypcji

> [!NOTE]
> Interfejs API transkrypcji usługi Batch jest idealny dla wywołania roboczych, które zazwyczaj są gromadzone tysiące godzin audio. Ułatwia to transkrypcja dużych ilości nagrań dźwiękowych.

### <a name="supported-formats"></a>Obsługiwane formaty

Interfejs API transkrypcji usługi Batch obsługuje następujące formaty:

| Format | Koder-dekoder | Szybkość transmisji bitów | Częstotliwość próbkowania |
|--------|-------|---------|-------------|
| WAV | MODUŁU PCM | 16-bitowych | stereo mono, kHz, 8 lub 16 |
| MP3 | MODUŁU PCM | 16-bitowych | stereo mono, kHz, 8 lub 16 |
| OGG | DZIELE | 16-bitowych | stereo mono, kHz, 8 lub 16 |

Dla strumieni audio stereo transkrypcji interfejsu API usługi Batch dzieli kanału lewy i prawy podczas transkrypcji. Każdy dwa pliki JSON z wynikiem są tworzone z pojedynczy kanał. Sygnatury czasowe na wypowiedź Włącz dla deweloperów utworzyć uporządkowany końcowego transkrypcji. To przykładowe żądanie zawiera właściwości dotyczące filtrowania niewulgarności, interpunkcji i znaczników czasu na poziomie wyrazów.

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
> Interfejs API transkrypcji usługi Batch korzysta z usługi REST, do żądania transkrypcje, stanu i skojarzonych wyników. Można użyć interfejsu API z dowolnego języka. W następnej sekcji opisano sposób użycia interfejsu API.

### <a name="configuration-properties"></a>Właściwości konfiguracji

Użyj tych opcjonalnych właściwości, aby skonfigurować transkrypcję:

| Parametr | Opis |
|-----------|-------------|
| `ProfanityFilterMode` | Określa sposób obsługi wulgaryzmów w wyniki rozpoznawania. Akceptowane wartości to `None`, które uniemożliwiają filtrowanie z powodu niedostępności, `masked` które zastępują niewulgarne gwiazdkami, `removed`, które usuwają z wyniku wszystkie niezbyt wulgarne dane, lub `tags`, które dodaje Tagi "wulgarności". Ustawieniem domyślnym jest `masked`. |
| `PunctuationMode` | Określa sposób obsługi znaków interpunkcyjnych w wyniki rozpoznawania. Akceptowane wartości to `None`, które wyłączają interpunkcję, `dictated`, która oznacza jawną interpunkcję, `automatic`, która umożliwia dekoderowi odliczanie z interpunkcją, lub `dictatedandautomatic`, które oznacza podyktowane znaczniki interpunkcyjne lub automatyczne. |
 | `AddWordLevelTimestamps` | Określa, czy sygnatury czasowe poziomu słowa mają być dodawane do danych wyjściowych. Akceptowane wartości to `true`, które umożliwiają wyłączanie sygnatur czasowych na poziomie programu Word i `false` (wartość domyślna). |
 | `AddSentiment` | Należy dodać tonacji do wypowiedź. Akceptowane wartości to `true`, które umożliwiają wyłączenie opcji tonacji na wypowiedź i `false` (wartość domyślna). |
 | `AddDiarization` | Określa, że analiza diarization powinna zostać przeprowadzona na wejściu, który powinien być kanałem mono zawierającym dwa głosy. Akceptowane wartości to `true`, które umożliwiają wyłączenie programu diarization i `false` (wartość domyślna). Wymaga również, aby `AddWordLevelTimestamps` mieć wartość true.|

### <a name="storage"></a>Magazyn

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

Zwróć uwagę na asynchroniczne Instalatora w celu opublikowanie audio i odbieranie stanu transkrypcji. Klient, który tworzysz to klient HTTP platformy .NET. Istnieje `PostTranscriptions` metoda wysyłania szczegółów pliku audio i metody `GetTranscriptions` na potrzeby otrzymywania wyników. `PostTranscriptions` zwraca dojście, a `GetTranscriptions` używa go do utworzenia dojścia w celu uzyskania stanu transkrypcji.

Bieżący kod przykładowy nie określono modelu niestandardowego. Usługa używa modele planu bazowego dla przepisywania pliku lub plików. Aby określić te modele, można przekazać w tej samej metody identyfikatory modelu akustycznego i modelu języka.

> [!NOTE]
> W przypadku transkrypcji bazowej nie trzeba deklarować identyfikatora dla modeli bazowych. Jeśli określisz tylko identyfikator modelu języka (bez identyfikatora modelu akustycznego), zostanie automatycznie wybrany zgodny model akustyczny. Jeśli określisz tylko identyfikator modelu akustycznego, zostanie automatycznie wybrany pasujący model języka.

## <a name="download-the-sample"></a>Pobierz przykład

Przykład można znaleźć w katalogu `samples/batch` w [przykładowym repozytorium GitHub](https://aka.ms/csspeech/samples).

> [!NOTE]
> Zadania transkrypcji usługi Batch są planowane według najlepszego nakładu pracy, ale nie ma oszacowania czasu, gdy zadanie zostanie zmienione w stanie uruchomionym. Po uruchomieniu rzeczywista transkrypcja jest przetwarzana szybciej niż w czasie rzeczywistym.

## <a name="next-steps"></a>Następne kroki

* [Pobierz subskrypcję usługi mowy w wersji próbnej](https://azure.microsoft.com/try/cognitive-services/)
