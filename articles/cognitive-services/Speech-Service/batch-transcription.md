---
title: Co to jest transkrypcja wsadowa - Usługa mowy
titleSuffix: Azure Cognitive Services
description: Transkrypcja wsadowa jest idealnym rozwiązaniem, jeśli chcesz transkrypcji dużej ilości dźwięku w magazynie, takich jak obiekty blob platformy Azure. Za pomocą dedykowanego interfejsu API REST można wskazywać pliki audio z identyfikatorem URI podpisu dostępu współdzielonego (SAS) i asynchronicznie odbierać transkrypcje.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/18/2020
ms.author: wolfma
ms.openlocfilehash: fb39f1ec83416ee8ab2a33b514971110db0c0b17
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668833"
---
# <a name="what-is-batch-transcription"></a>Co to jest transkrypcja partii?

Transkrypcja wsadowa to zestaw operacji interfejsu API REST, który umożliwia transkrypcję dużej ilości dźwięku w magazynie. Można wskazać pliki audio z identyfikatorem URI podpisu dostępu współdzielonego (SAS) i asynchronicznie odbierać wyniki transkrypcji.

Asynchroniiowa transkrypcja mowy na tekst to tylko jedna z funkcji. Można użyć interfejsów API REST transkrypcji wsadowej, aby wywołać następujące metody:



|    Operacja transkrypcji partii                                             |    Metoda    |    POŁĄCZENIE INTERFEJSU API REST                                   |
|------------------------------------------------------------------------------|--------------|----------------------------------------------------|
|    Tworzy nową transkrypcję.                                              |    POST      |    api/speechtotext/v2.0/transkrypcje            |
|    Pobiera listę transkrypcji dla uwierzytelnionej subskrypcji.    |    GET       |    api/speechtotext/v2.0/transkrypcje            |
|    Pobiera listę obsługiwanych ustawień regionalnych dla transkrypcji w trybie offline.              |    GET       |    api/speechtotext/v2.0/transkrypcje/ustawienia regionalne    |
|    Aktualizuje modyfikowalne szczegóły transkrypcji zidentyfikowane przez jego identyfikator.    |    Patch     |    api/speechtotext/v2.0/transcriptions/{id}       |
|    Usuwa określone zadanie transkrypcji.                                 |    DELETE    |    api/speechtotext/v2.0/transcriptions/{id}       |
|    Pobiera transkrypcji zidentyfikowane przez dany identyfikator.                        |    GET       |    api/speechtotext/v2.0/transcriptions/{id}       |




Szczegółowy interfejs API, który jest dostępny jako [dokument Swagger,](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A)można przejrzeć i przetestować pod nagłówkiem `Custom Speech transcriptions`.

Zadania transkrypcji partii są planowane na podstawie najlepszych starań. Obecnie nie ma żadnych szacunków, gdy zadanie zmienia się w stan uruchomiony. Przy normalnym obciążeniu systemu powinno się to zdarzyć w ciągu kilku minut. Po uruchomieniu, rzeczywista transkrypcja jest przetwarzana szybciej niż audio w czasie rzeczywistym.

Obok łatwego w użyciu interfejsu API nie trzeba wdrażać niestandardowych punktów końcowych i nie masz żadnych wymagań dotyczących współbieżności do przestrzegania.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="subscription-key"></a>Klucz subskrypcji

Podobnie jak w odniesieniu do wszystkich funkcji usługi Mowy, klucz subskrypcji można utworzyć z [witryny Azure portal,](https://portal.azure.com) postępujących zgodnie z naszym [przewodnikiem Wprowadzenie.](get-started.md)

>[!NOTE]
> Do użycia transkrypcji wsadowej wymagana jest subskrypcja standardowa (S0) dla usługi mowy. Bezpłatne klucze subskrypcji (F0) nie działają. Aby uzyskać więcej informacji, zobacz [ceny i limity](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

### <a name="custom-models"></a>Modele niestandardowe

Jeśli planujesz dostosować modele akustyczne lub językowe, wykonaj kroki opisane w [obszarze Dostosowywanie modeli akustycznych](how-to-customize-acoustic-models.md) i [Projektowanie modeli językowych dostosowywania](how-to-customize-language-model.md). Aby użyć utworzonych modeli w transkrypcji partii, trzeba ich identyfikatory modelu. Identyfikator modelu można pobrać podczas sprawdzania szczegółów modelu. Wdrożony niestandardowy punkt końcowy nie jest potrzebny dla usługi transkrypcji partii.

## <a name="the-batch-transcription-api"></a>Interfejs API transkrypcji partii

### <a name="supported-formats"></a>Obsługiwane formaty

Interfejs API transkrypcji partii obsługuje następujące formaty:

| Format | Kodek | Bitrate | Częstotliwość próbkowania                     |
|--------|-------|---------|---------------------------------|
| WAV    | PCM   | 16-bitowy  | 8 kHz lub 16 kHz, mono lub stereo |
| Mp3    | PCM   | 16-bitowy  | 8 kHz lub 16 kHz, mono lub stereo |
| Ogg    | Opus  | 16-bitowy  | 8 kHz lub 16 kHz, mono lub stereo |

W przypadku strumieni audio stereo, lewy i prawy kanał są dzielone podczas transkrypcji. Dla każdego kanału tworzony jest plik wyników JSON. Sygnatury czasowe generowane na wypowiedź umożliwiają deweloperowi utworzenie uporządkowanego końcowego transkrypcji.

### <a name="configuration"></a>Konfigurowanie

Parametry konfiguracji są dostarczane jako JSON:

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
    "TranscriptionResultsContainerUrl" : "<service SAS URI to Azure container to store results into (write permission required)>"
  }
}
```

### <a name="configuration-properties"></a>Właściwości konfiguracji

Użyj tych właściwości opcjonalnych, aby skonfigurować transkrypcję:

:::row:::
   :::column span="1":::
      **Parametr**
   :::column-end:::
   :::column span="2":::
      **Opis**
:::row-end:::
:::row:::
   :::column span="1":::
      `ProfanityFilterMode`
   :::column-end:::
   :::column span="2":::
      Określa sposób obsługi wulgaryzmów w wynikach rozpoznawania. Akceptowane wartości `None` to wyłączenie filtrowania wulgaryzmów, `Masked` zastąpienie wulgaryzmów gwiazdkami, `Removed` usunięcie wszystkich `Tags` wulgaryzmów z wyniku lub dodanie tagów "wulgaryzmów". Ustawienie domyślne to `Masked`.
:::row-end:::
:::row:::
   :::column span="1":::
      `PunctuationMode`
   :::column-end:::
   :::column span="2":::
      Określa sposób obsługi interpunkcji w wynikach rozpoznawania. Akceptowane wartości `None` to wyłączenie znaków interpunkcyjnych, `Dictated` sugerowanie jawnych `Automatic` (mówionych) znaków interpunkcyjnych, `DictatedAndAutomatic` umożliwienie dekoderowi radzenia sobie ze interpunkcją lub użycie podyktowanych i automatycznych znaków interpunkcyjnych. Ustawienie domyślne to `DictatedAndAutomatic`.
:::row-end:::
:::row:::
   :::column span="1":::
      `AddWordLevelTimestamps`
   :::column-end:::
   :::column span="2":::
      Określa, czy do danych wyjściowych mają być dodawane sygnatury czasowe poziomu wyrazu. Zaakceptowane wartości `true` mają umożliwić sygnatury czasowe poziomu wyrazu i `false` (wartość domyślna), aby je wyłączyć.
:::row-end:::
:::row:::
   :::column span="1":::
      `AddSentiment`
   :::column-end:::
   :::column span="2":::
      Określa, czy analiza tonacji ma być stosowana do wypowiedź. Akceptowane wartości `true` mają włączyć `false` i (wartość domyślna) ją wyłączyć.
:::row-end:::
:::row:::
   :::column span="1":::
      `AddDiarization`
   :::column-end:::
   :::column span="2":::
      Określa, że analiza diaryzacji powinna być przeprowadzana na wejściu, które ma być kanałem mono zawierającym dwa głosy. Zaakceptowane wartości `true` umożliwiają diarization `false` i (wartość domyślna), aby ją wyłączyć. Wymaga `AddWordLevelTimestamps` to również, aby być ustawione na true.
:::row-end:::
:::row:::
   :::column span="1":::
      `TranscriptionResultsContainerUrl`
   :::column-end:::
   :::column span="2":::
      Opcjonalny adres URL z [sygnaturą dostępu Współdzielonego usługi](../../storage/common/storage-sas-overview.md) do kontenera zapisywalny na platformie Azure. Wynik jest przechowywany w tym kontenerze.
:::row-end:::

### <a name="storage"></a>Magazyn

Transkrypcja wsadowa obsługuje [magazyn obiektów Blob platformy Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) do odczytywania audio i zapisywania transkrypcji do magazynu.

## <a name="the-batch-transcription-result"></a>Wynik transkrypcji partii

W przypadku audio wejściowego mono tworzony jest jeden plik wyników transkrypcji. W przypadku dźwięku wejściowego stereo tworzone są dwa pliki wyników transkrypcji. Każdy ma tę strukturę:

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
          "Offset": number                                  'time in ticks (1 tick is 100 nanosec)'
          "Duration": number                                'time in ticks (1 tick is 100 nanosec)'
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
                  "Offset": number                          'time in ticks (1 tick is 100 nanosec)'
                  "Duration": number                        'time in ticks (1 tick is 100 nanosec)'
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

| Formularz        | Zawartość                                                                                                                                                  |
|-------------|----------------------------------------------------------------------------------------------------------------------------------------------------------|
| `Lexical`   | Rzeczywiste słowa rozpoznane.                                                                                                                             |
| `ITN`       | Znormalizowana forma rozpoznanego tekstu zwersycją zwerycjonowaną odwrotnością tekstu. Stosuje się skróty ("doktor smith" do "dr smith"), numery telefonów i inne przekształcenia. |
| `MaskedITN` | Formularz ITN z zastosowanym maskowaniem wulgaryzmów.                                                                                                             |
| `Display`   | Forma wyświetlania rozpoznanego tekstu. Dodano znaki interpunkcyjne i wielkie litery są uwzględniane.                                                             |

## <a name="speaker-separation-diarization"></a>Separacja głośników (diarization)

Diarization to proces oddzielania głośników kawałkiem dźwięku. Nasz rurociąg Batch obsługuje diarization i jest w stanie rozpoznać dwa głośniki na nagraniach jednokanałowych. Funkcja ta nie jest dostępna na nagraniach stereofonicznych.

Wszystkie dane wyjściowe `SpeakerId`transkrypcji zawiera . Jeśli diarization nie jest `"SpeakerId": null` używany, pokazuje w danych wyjściowych JSON. Do diaryzacji obsługujemy dwa głosy, więc `"1"` głośniki są identyfikowane jako lub `"2"`.

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

Sygnatury czasowe na poziomie worda również musiałyby być "włączone", jak wskazują parametry w powyższym żądaniu.

## <a name="sentiment-analysis"></a>Analiza tonacji

Funkcja tonacji szacuje tonację wyrażoną w dźwięku. Sentyment jest wyrażany przez wartość między 0 `Negative` `Neutral`i `Positive` 1 dla , i sentymentu. Na przykład analizy tonacji można używać w scenariuszach centrum obsługi:

- Uzyskaj wgląd w zadowolenie klientów
- Uzyskaj wgląd w wydajność agentów (zespół odbierając połączenia)
- Znajdź dokładny moment w czasie, gdy połączenie miało skręt w kierunku ujemnym
- Co poszło dobrze, gdy zamieniając negatywne wezwanie w pozytywny kierunek
- Określ, co klienci lubią i czego nie lubią w produkcie lub usłudze

Tonację jest oceniana na segment audio na podstawie postaci leksykalne. Cały tekst w tym segmencie audio jest używany do obliczania tonacji. Nie zagregowane nastroje są obliczane dla całej transkrypcji. Obecnie analiza nastrojów jest dostępna tylko dla języka angielskiego.

Przykład wyjściowy JSON wygląda poniżej:

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

Usługa transkrypcji może obsługiwać dużą liczbę przesłanych transkrypcji. Można zbadać stan transkrypcji za `GET` pomocą [metody transkrypcji](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A/GetTranscriptions). Zachowaj informacje zwrócone do rozsądnego `take` rozmiaru, określając parametr (kilkaset). [Po pobraniu](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A/DeleteTranscription) wyników regularnie usuwaj transkrypcje z usługi. Gwarantuje to szybkie odpowiedzi z wywołań zarządzania transkrypcją.

## <a name="sample-code"></a>Przykładowy kod

Kompletne przykłady są dostępne w [repozytorium próbek usługi GitHub](https://aka.ms/csspeech/samples) wewnątrz podkatalogu. `samples/batch`

Należy dostosować przykładowy kod z informacjami o subskrypcji, region usługi, identyfikator URI sygnatury dostępu Współdzielonego wskazujący plik audio do transkrypcji i identyfikatory modelu w przypadku, gdy chcesz użyć niestandardowego modelu akustycznego lub językowego.

[!code-csharp[Configuration variables for batch transcription](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchdefinition)]

Przykładowy kod konfiguruje klienta i przesyła żądanie transkrypcji. Następnie sonduje informacje o stanie i drukowanie szczegółów dotyczących postępu transkrypcji.

[!code-csharp[Code to check batch transcription status](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchstatus)]

Aby uzyskać szczegółowe informacje na temat poprzednich połączeń, zobacz nasz [dokument Swagger](https://westus.cris.ai/swagger/ui/index). Aby uzyskać pełny przykład pokazany tutaj, przejdź `samples/batch` do [GitHub](https://aka.ms/csspeech/samples) w podkatalogu.

Zanotuj konfigurację asynchroniią do publikowania dźwięku i odbierania stanu transkrypcji. Utworzony klient jest klientem HTTP platformy .NET. Istnieje `PostTranscriptions` metoda wysyłania szczegółów pliku audio `GetTranscriptions` i metoda odbierania wyników. `PostTranscriptions`zwraca dojście `GetTranscriptions` i używa go do utworzenia dojścia, aby uzyskać stan transkrypcji.

Bieżący przykładowy kod nie określa modelu niestandardowego. Usługa używa modeli linii bazowej do transkrypcji pliku lub plików. Aby określić modele, można przekazać tę samą metodę co identyfikatory modelu dla modelu akustycznego i językowego.

> [!NOTE]
> W przypadku transkrypcji linii bazowej nie trzeba deklarować identyfikatora dla modeli linii bazowych. Jeśli określisz tylko identyfikator modelu języka (i nie ma identyfikatora modelu akustycznego), automatycznie wybierany jest pasujący model akustyczny. Jeśli określisz tylko identyfikator modelu akustycznego, automatycznie wybierany jest pasujący model języka.

## <a name="download-the-sample"></a>Pobierz przykład

Przykład można znaleźć w `samples/batch` katalogu w [przykładowym repozytorium GitHub](https://aka.ms/csspeech/samples).

## <a name="next-steps"></a>Następne kroki

- [Pobierz subskrypcję usługi mowy w wersji próbnej](https://azure.microsoft.com/try/cognitive-services/)
