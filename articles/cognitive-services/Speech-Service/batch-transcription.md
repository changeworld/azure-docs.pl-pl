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
ms.date: 12/17/2019
ms.author: panosper
ms.openlocfilehash: 5732df2551eafa74b81f9a918a1cb7cf5ac1395c
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2020
ms.locfileid: "76768043"
---
# <a name="how-to-use-batch-transcription"></a>Jak korzystać z transkrypcji partii

Transkrypcja usługi Batch jest idealnym rozwiązaniem w przypadku jego przepisywania dużej ilości danych audio w magazynie. Korzystając z dedykowanego interfejsu API REST, można wskazać pliki audio przy użyciu identyfikatora URI sygnatury dostępu współdzielonego (SAS) i asynchronicznie otrzymywać wyniki transkrypcji.

Interfejs API oferuje asynchroniczne transkrypcje zamiany mowy na tekst i inne funkcje. Interfejsu API REST można użyć do udostępnienia metod:

- Tworzenie żądań przetwarzania wsadowego
- Zbadaj stan
- Pobierz wyniki transkrypcji
- Usuwanie informacji o transkrypcji z usługi

Szczegółowy interfejs API jest dostępny jako [dokument struktury Swagger](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A), pod nagłówkiem `Custom Speech transcriptions`.

Zadania transkrypcji partii są planowane na podstawie najlepszego nakładu pracy. Obecnie nie ma oszacowania, kiedy zadanie zostanie zmienione w stanie uruchomienia. W przypadku normalnego ładowania systemu powinno to nastąpić w ciągu kilku minut. Po uruchomieniu rzeczywista transkrypcja jest przetwarzana szybciej niż w czasie rzeczywistym dźwięku.

Obok łatwego w użyciu interfejsu API nie musisz wdrażać niestandardowych punktów końcowych i nie masz żadnych wymagań współbieżności do obserwowania.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="subscription-key"></a>Klucz subskrypcji

Jak z wszystkich funkcji usługi rozpoznawania mowy, tworzenie klucz subskrypcji z [witryny Azure portal](https://portal.azure.com) postępując zgodnie z naszym [Wprowadzenie — przewodnik](get-started.md).

>[!NOTE]
> Aby można było użyć transkrypcji partii, wymagana jest Standardowa subskrypcja (S0) dla usługi rozpoznawania mowy. Bezpłatna subskrypcja kluczy (F0) nie będzie działać. Aby uzyskać więcej informacji, zobacz [Cennik i limity](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

### <a name="custom-models"></a>Modele niestandardowe

Jeśli planujesz dostosowanie modeli akustycznych lub modelowych, postępuj zgodnie z instrukcjami w temacie [Dostosowywanie modeli akustycznych](how-to-customize-acoustic-models.md) i [modeli języków dostosowania projektu](how-to-customize-language-model.md). Aby można było używać utworzonych modeli w transkrypcji partii, potrzebne są ich identyfikatory modeli. Identyfikator modelu można pobrać podczas inspekcji szczegółów modelu. Wdrożony niestandardowy punkt końcowy nie jest wymagany w przypadku usługi transkrypcji partii.

## <a name="the-batch-transcription-api"></a>Transkrypcji interfejsu API usługi Batch

### <a name="supported-formats"></a>Obsługiwane formaty

Interfejs API transkrypcji usługi Batch obsługuje następujące formaty:

| Format | Koder-dekoder | Szybkość transmisji bitów | Częstotliwość próbkowania |
|--------|-------|---------|-------------|
| WAV | MODUŁU PCM | 16-bitowych | 8 kHz lub 16 kHz, mono lub stereo |
| MP3 | MODUŁU PCM | 16-bitowych | 8 kHz lub 16 kHz, mono lub stereo |
| OGG | DZIELE | 16-bitowych | 8 kHz lub 16 kHz, mono lub stereo |

W przypadku strumieni stereofonicznych audio lewy i prawy są dzielone podczas transkrypcji. Dla każdego kanału tworzony jest plik wynikowy JSON. Sygnatury czasowe wygenerowane na wypowiedź umożliwiają deweloperowi utworzenie uporządkowanej końcowej transkrypcji.

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
    "ProfanityFilterMode": "None | Removed | Tags | Masked",
    "PunctuationMode": "None | Dictated | Automatic | DictatedAndAutomatic",
    "AddWordLevelTimestamps" : "True | False",
    "AddSentiment" : "True | False",
    "AddDiarization" : "True | False",
    "TranscriptionResultsContainerUrl" : "<SAS to Azure container to store results into (write permission required)>"
  }
}
```

### <a name="configuration-properties"></a>Właściwości konfiguracji

Użyj tych opcjonalnych właściwości, aby skonfigurować transkrypcję:

| Parametr | Opis |
|-----------|-------------|
| `ProfanityFilterMode` | Określa sposób obsługi wulgaryzmów w wyniki rozpoznawania. Akceptowane wartości to `None` która wyłącza filtrowanie wulgaryzmów `Masked` gwiazdek, która zastępuje wulgaryzmów `Removed` z wyników, które powoduje usunięcie wszystkich wulgaryzmów lub `Tags` dodaje tagi "wulgaryzmów". Ustawieniem domyślnym jest `Masked`. |
| `PunctuationMode` | Określa sposób obsługi znaków interpunkcyjnych w wyniki rozpoznawania. Akceptowane wartości to `None` która wyłącza znak interpunkcyjny, `Dictated` co oznacza jawne znak interpunkcyjny, `Automatic` umożliwiającą dekodera przeciwdziałania znak interpunkcyjny, lub `DictatedAndAutomatic` co oznacza definiowane znaków interpunkcyjnych lub automatyczny. |
| `AddWordLevelTimestamps` | Określa, czy sygnatury czasowe poziomu słowa mają być dodawane do danych wyjściowych. Akceptowane wartości to `true`, które umożliwiają wyłączanie sygnatur czasowych na poziomie programu Word i `false` (wartość domyślna). |
| `AddSentiment` | Należy dodać tonacji do wypowiedź. Akceptowane wartości to `true`, które umożliwiają wyłączenie opcji tonacji na wypowiedź i `false` (wartość domyślna). |
| `AddDiarization` | Określa, że analiza diarization powinna zostać przeprowadzona na wejściu, który powinien być kanałem mono zawierającym dwa głosy. Akceptowane wartości to `true`, które umożliwiają wyłączenie programu diarization i `false` (wartość domyślna). Wymaga również, aby `AddWordLevelTimestamps` mieć wartość true.|
|`TranscriptionResultsContainerUrl`|Opcjonalny token sygnatury dostępu współdzielonego do zapisywalnego kontenera na platformie Azure. Wynik zostanie zapisany w tym kontenerze.

### <a name="storage"></a>Usługa Storage

Transkrypcja usługi Batch obsługuje [usługę Azure Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) do odczytu i zapisywania transkrypcji w magazynie.

## <a name="the-batch-transcription-result"></a>Wynik transkrypcji partii

W przypadku wejścia audio mono jest tworzony jeden plik wynikowy transkrypcji. W przypadku dźwięku wejściowego stereo, tworzone są dwa pliki wyników transkrypcji. Każda z nich ma następującą strukturę:

```json
{
  "AudioFileResults":[ 
    {
      "AudioFileName": "Channel.0.wav | Channel.1.wav"      'maximum of 2 channels supported'
      "AudioFileUrl": null                                  'always null'
      "AudioLengthInSeconds": number                        'Real number. Two decimal places'
      "CombinedResults": [
        {
          "ChannelNumber": null                             'always null'
          "Lexical": string
          "ITN": string
          "MaskedITN": string
          "Display": string
        }
      ]
      SegmentResults:[                                      'for each individual segment'
        {
          "RecognitionStatus": "Success | Failure"
          "ChannelNumber": null
          "SpeakerId": null | "1 | 2"                       'null if no diarization
                                                             or stereo input file, the
                                                             speakerId as a string if
                                                             diarization requested for
                                                             mono audio file'
          "Offset": number                                  'time in milliseconds'
          "Duration": number                                'time in milliseconds'
          "OffsetInSeconds" : number                        'Real number. Two decimal places'
          "DurationInSeconds" : number                      'Real number. Two decimal places'
          "NBest": [
            {
              "Confidence": number                          'between 0 and 1'
              "Lexical": string
              "ITN": string
              "MaskedITN": string
              "Display": string
              "Sentiment":
                {                                           'this is omitted if sentiment is
                                                             not requested'
                  "Negative": number                        'between 0 and 1'
                  "Neutral": number                         'between 0 and 1'
                  "Positive": number                        'between 0 and 1'
                }
              "Words": [
                {
                  "Word": string
                  "Offset": number                          'time in milliseconds'
                  "Duration": number                        'time in milliseconds'
                  "OffsetInSeconds": number                 'Real number. Two decimal places'
                  "DurationInSeconds": number               'Real number. Two decimal places'
                  "Confidence": number                      'between 0 and 1'
                }
              ]
            }
          ]
        }
      ]
    }
  ]
}
```

Wynik zawiera następujące formularze:

|Seryjn|Zawartość|
|-|-|
|`Lexical`|Rzeczywiste wyrazy zostały rozpoznane.
|`ITN`|Odwrócony tekst — znormalizowana forma rozpoznanego tekstu. Skróty ("Lekarz Kowalski" do "Dr Kowalski"), numery telefonów i inne przekształcenia są stosowane.
|`MaskedITN`|Formularz ITN z zastosowanym maską wulgarności.
|`Display`|Formularz wyświetlania rozpoznanego tekstu. Obejmuje to dodawanie interpunkcji i wielkich liter.

## <a name="speaker-separation-diarization"></a>Separacja głośników (Diarization)

Diarization to proces oddzielania głośników w części audio. Nasz potok wsadowy obsługuje diarization i jest w stanie rozpoznawać dwa głośniki w nagraniach kanału mono. Funkcja nie jest dostępna w nagraniach stereo.

Wszystkie dane wyjściowe transkrypcji zawierają `SpeakerId`. Jeśli diarization nie jest używany, zostanie wyświetlony `"SpeakerId": null` w danych wyjściowych JSON. W przypadku diarization Obsługujemy dwa głosy, więc głośniki będą identyfikowane jako `"1"` lub `"2"`.

Aby zażądać diarization, wystarczy dodać odpowiedni parametr w żądaniu HTTP, jak pokazano poniżej.

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

Znaczniki czasu na poziomie słowa również muszą mieć wartość "włączone", ponieważ parametry w powyższym żądaniu wskazują.

## <a name="sentiment-analysis"></a>Analiza opinii

Funkcja tonacji szacuje tonacji wyrażoną w dźwięku. Tonacji jest wyrażona wartością z zakresu od 0 do 1 dla `Negative`, `Neutral`i `Positive` tonacji. Na przykład analiza tonacji może być używana w scenariuszach centrum połączeń:

- Uzyskaj wgląd w szczegółowe informacje na temat zadowolenia klientów
- Uzyskaj wgląd w wydajność agentów (zespół przejmowania wywołań)
- Znajdowanie dokładnego punktu w czasie, gdy wywołanie zajęło negatywny kierunek
- Co się stało, gdy ujemne wywołanie jest wynikiem pozytywnym
- Określ, co Ci klienci i co nie lubię o produkcie lub usłudze

Tonacji jest oceniane na segment audio w oparciu o postać leksykalną. Cały tekst w tym segmencie audio służy do obliczania tonacji. Nie jest obliczany zagregowany tonacji dla całego transkrypcji.

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

## <a name="best-practices"></a>Najlepsze rozwiązania

Usługa transkrypcji może obsłużyć dużą liczbę przesłanych transkrypcji. Można badać stan transkrypcji za pomocą `GET` w [metodzie transkrypcji](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A/GetTranscriptions). Zachowaj informacje zwracane do rozsądnego rozmiaru, określając parametr `take` (kilka setek). [Usuwanie transkrypcji](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A/DeleteTranscription) regularnie z usługi po pobraniu wyników. Pozwoli to zagwarantować szybkie odpowiedzi od wywołań zarządzania transkrypcji.

## <a name="sample-code"></a>Przykładowy kod

Kompletne przykłady są dostępne w [przykładowym repozytorium GitHub](https://aka.ms/csspeech/samples) w ramach podkatalogu `samples/batch`.

Musisz dostosować przykładowy kod przy użyciu informacji o subskrypcji, regionu usługi, identyfikatora URI sygnatury dostępu współdzielonego, wskazującego plik audio do Transkrypcja, oraz identyfikatorów modeli w przypadku, gdy chcesz użyć niestandardowego modelu akustycznego lub języka.

[!code-csharp[Configuration variables for batch transcription](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchdefinition)]

Przykładowy kod skonfiguruje klienta i prześle żądanie transkrypcji. Następnie będzie sondował informacje o stanie i wydrukować szczegółowe informacje o postępie transkrypcji.

[!code-csharp[Code to check batch transcription status](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchstatus)]

Aby uzyskać szczegółowe informacje o poprzednich wywołaniach, zobacz [dokument struktury Swagger](https://westus.cris.ai/swagger/ui/index). Aby wyświetlić pełny przykład, przejdź do witryny [GitHub](https://aka.ms/csspeech/samples) w podkatalogu `samples/batch`.

Zwróć uwagę na asynchroniczne Instalatora w celu opublikowanie audio i odbieranie stanu transkrypcji. Klient, który tworzysz to klient HTTP platformy .NET. Brak `PostTranscriptions` Metoda przesyłania plików audio i `GetTranscriptions` metody do odbierania wyników. `PostTranscriptions` Zwraca uchwyt, a `GetTranscriptions` używa jej do utworzenia dojście można pobrać stanu transkrypcji.

Bieżący kod przykładowy nie określono modelu niestandardowego. Usługa używa modele planu bazowego dla przepisywania pliku lub plików. Aby określić te modele, można przekazać w tej samej metody identyfikatory modelu akustycznego i modelu języka.

> [!NOTE]
> W przypadku transkrypcji bazowej nie trzeba deklarować identyfikatora dla modeli bazowych. Jeśli określisz tylko identyfikator modelu języka (bez identyfikatora modelu akustycznego), zostanie automatycznie wybrany zgodny model akustyczny. Jeśli określisz tylko identyfikator modelu akustycznego, zostanie automatycznie wybrany pasujący model języka.

## <a name="download-the-sample"></a>Pobierz przykład

Przykład można znaleźć w katalogu `samples/batch` w [przykładowym repozytorium GitHub](https://aka.ms/csspeech/samples).

## <a name="next-steps"></a>Następne kroki

* [Pobierz subskrypcję usługi mowy w wersji próbnej](https://azure.microsoft.com/try/cognitive-services/)
