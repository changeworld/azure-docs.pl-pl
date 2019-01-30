---
title: Jak używać transkrypcji usługi Batch — usługi mowy
titlesuffix: Azure Cognitive Services
description: Transkrypcja partii jest idealnym rozwiązaniem, jeśli chcesz także dużej ilości dźwięk w magazynie, takim jak obiektów blob platformy Azure. Za pomocą dedykowanego interfejsu API REST, można wskazać pliki audio, przy użyciu sygnatury dostępu współdzielonego (SAS) identyfikator URI i asynchronicznie otrzymywać transkrypcji.
services: cognitive-services
author: PanosPeriorellis
manager: cgronlun
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: bf89180ea98473d2da3495286396a12c6f25288f
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55228665"
---
# <a name="why-use-batch-transcription"></a>Dlaczego warto używać usługi Batch transkrypcji?

Transkrypcja partii jest idealnym rozwiązaniem, jeśli chcesz także dużej ilości dźwięk w magazynie, takim jak obiektów blob platformy Azure. Za pomocą dedykowanego interfejsu API REST, można wskazać pliki audio, przy użyciu sygnatury dostępu współdzielonego (SAS) identyfikator URI i asynchronicznie otrzymywać transkrypcji.

>[!NOTE]
> Standardowa subskrypcja (S0) dla usług przetwarzania mowy wymagane jest wprowadzenie transkrypcji usługi batch. Bezpłatna subskrypcja kluczy (F0) nie będzie działać. Aby uzyskać więcej informacji, zobacz [ceny i limity](https://azure.microsoft.com/en-us/pricing/details/cognitive-services/speech-services/).

## <a name="the-batch-transcription-api"></a>Transkrypcji interfejsu API usługi Batch

Interfejs API transkrypcji usługi Batch oferuje asynchroniczne transkrypcja mowy na tekst, oraz dodatkowe funkcje. To interfejs API REST, który udostępnia metody:

1. Tworzenie żądań przetwarzania wsadowego
1. Stan zapytania
1. Pobieranie transkrypcji

> [!NOTE]
> Interfejs API transkrypcji usługi Batch jest idealny dla wywołania roboczych, które zazwyczaj są gromadzone tysiące godzin audio. Interfejs API jest przeprowadzany przez "fire and forget" filozofią, która ułatwia także bardzo dużych ilości nagrania audio.

### <a name="supported-formats"></a>Obsługiwane formaty

Interfejs API transkrypcji usługi Batch obsługuje następujące formaty:

| Format | Koder-dekoder | Szybkość transmisji bitów | Częstotliwość próbkowania |
|--------|-------|---------|-------------|
| WAV | MODUŁU PCM | 16-bitowych | stereo mono, kHz, 8 lub 16 |
| MP3 | MODUŁU PCM | 16-bitowych | stereo mono, kHz, 8 lub 16 |
| OGG | DZIELE | 16-bitowych | stereo mono, kHz, 8 lub 16 |

> [!NOTE]
> Interfejs API usługi Batch transkrypcji wymaga klucza S0 (płacenia warstwy). Nie działa z kluczem bezpłatna (f0).

Dla strumieni audio stereo transkrypcji interfejsu API usługi Batch dzieli kanału lewy i prawy podczas transkrypcji. Każdy dwa pliki JSON z wynikiem są tworzone z pojedynczy kanał. Sygnatury czasowe na wypowiedź Włącz dla deweloperów utworzyć uporządkowany końcowego transkrypcji. Poniższy przykładowy kod JSON zawiera dane wyjściowe kanału, właściwości includuing do filtra wulgaryzmów i modelu znaki interpunkcyjne.

```json
{
  "recordingsUrl": "https://contoso.com/mystoragelocation",
  "models": [],
  "locale": "en-US",
  "name": "Transcription using locale en-US",
  "description": "An optional description of the transcription.",
  "properties": {
    "ProfanityFilterMode": "Masked",
    "PunctuationMode": "DictatedAndAutomatic"
  },
```

> [!NOTE]
> Interfejs API transkrypcji usługi Batch korzysta z usługi REST, do żądania transkrypcje, stanu i skojarzonych wyników. Można użyć interfejsu API z dowolnego języka. W następnej sekcji opisano sposób użycia interfejsu API.

### <a name="query-parameters"></a>Parametry zapytania

Te parametry mogą być zawarte w ciągu zapytania żądania REST.

| Parametr | Opis | Wymagane / opcjonalne |
|-----------|-------------|---------------------|
| `ProfanityFilterMode` | Określa sposób obsługi wulgaryzmów w wyniki rozpoznawania. Akceptowane wartości to `none` która wyłącza filtrowanie wulgaryzmów `masked` gwiazdek, która zastępuje wulgaryzmów `removed` z wyników, które powoduje usunięcie wszystkich wulgaryzmów lub `tags` dodaje tagi "wulgaryzmów". Ustawieniem domyślnym jest `masked`. | Optional (Opcjonalność) |
| `PunctuationMode` | Określa sposób obsługi znaków interpunkcyjnych w wyniki rozpoznawania. Akceptowane wartości to `none` która wyłącza znak interpunkcyjny, `dictated` co oznacza jawne znak interpunkcyjny, `automatic` umożliwiającą dekodera przeciwdziałania znak interpunkcyjny, lub `dictatedandautomatic` co oznacza definiowane znaków interpunkcyjnych lub automatyczny. | Optional (Opcjonalność) |


## <a name="authorization-token"></a>Token autoryzacji

Jak z wszystkich funkcji usługi rozpoznawania mowy, tworzenie klucz subskrypcji z [witryny Azure portal](https://portal.azure.com) postępując zgodnie z naszym [Wprowadzenie — przewodnik](get-started.md). Jeśli planujesz uzyskiwanie transkrypcje modeli podstawowych w naszym Tworzenie klucza jest wszystko, co należy zrobić.

Jeśli zamierzasz dostosować i użyć modelu niestandardowego, Dodaj klucz subskrypcji do portal usługi custom speech, wykonując następujące czynności:

1. Zaloguj się do [Custom Speech](https://customspeech.ai).

2. W prawym górnym rogu, wybierz **subskrypcje**.

3. Wybierz **połączyć z istniejącą subskrypcją**.

4. W oknie podręcznym Dodaj klucz subskrypcji i alias.

    ![W oknie Dodaj subskrypcję](media/stt/Subscriptions.jpg)

5. Skopiuj i Wklej klucz w kodzie klienta w następującym przykładzie.

> [!NOTE]
> Jeśli planujesz użyć niestandardowego modelu, konieczne będzie identyfikator modelu zbyt. Ten identyfikator nie jest identyfikator punktu końcowego, który można znaleźć w widoku Szczegóły punktu końcowego. To identyfikator modelu, który można pobrać po wybraniu szczegółów tego modelu.

## <a name="sample-code"></a>Przykładowy kod

Dostosuj następujący przykładowy kod z kluczem subskrypcji oraz klucza interfejsu API. Ta akcja umożliwia pobieranie tokenu elementu nośnego.

```cs
     public static CrisClient CreateApiV2Client(string key, string hostName, int port)

        {
            var client = new HttpClient();
            client.Timeout = TimeSpan.FromMinutes(25);
            client.BaseAddress = new UriBuilder(Uri.UriSchemeHttps, hostName, port).Uri;
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", key);

            return new CrisClient(client);
        }
```

Po uzyskaniu tokenu, należy określić identyfikator URI sygnatury dostępu Współdzielonego, który wskazuje plik audio, który wymaga transkrypcji. Pozostała część kodu iteruje przez stan i wyświetla wyniki. Na początku możesz skonfigurować klucz, region, modele użycia w celu skojarzenia zabezpieczeń, jak pokazano w poniższym fragmencie kodu. Następnie tworzy klienta i żądania POST.

```cs
            private const string SubscriptionKey = "<your Speech subscription key>";
            private const string HostName = "westus.cris.ai";
            private const int Port = 443;

            // SAS URI
            private const string RecordingsBlobUri = "SAS URI pointing to the file in Azure Blob Storage";

            // adapted model Ids
            private static Guid AdaptedAcousticId = new Guid("guid of the acoustic adaptation model");
            private static Guid AdaptedLanguageId = new Guid("guid of the language model");

            // Creating a Batch Transcription API Client
            var client = CrisClient.CreateApiV2Client(SubscriptionKey, HostName, Port);

            var transcriptionLocation = await client.PostTranscriptionAsync(Name, Description, Locale, new Uri(RecordingsBlobUri), new[] { AdaptedAcousticId, AdaptedLanguageId }).ConfigureAwait(false);
```

Teraz, gdy wprowadzono żądanie można wykonywać zapytania i Pobierz wyniki transkrypcji, jak pokazano w poniższym fragmencie kodu:

```cs

            // get all transcriptions for the user
            transcriptions = await client.GetTranscriptionAsync().ConfigureAwait(false);

            // for each transcription in the list we check the status
            foreach (var transcription in transcriptions)
            {
                switch(transcription.Status)
                {
                    case "Failed":
                    case "Succeeded":

                            // we check to see if it was one of the transcriptions we created from this client.
                        if (!createdTranscriptions.Contains(transcription.Id))
                        {
                            // not created from here, continue
                            continue;
                        }

                        completed++;

                        // if the transcription was successful, check the results
                        if (transcription.Status == "Succeeded")
                        {
                            var resultsUri = transcription.ResultsUrls["channel_0"];
                            WebClient webClient = new WebClient();
                            var filename = Path.GetTempFileName();
                            webClient.DownloadFile(resultsUri, filename);
                            var results = File.ReadAllText(filename);
                            Console.WriteLine("Transcription succeeded. Results: ");
                            Console.WriteLine(results);
                        }

                    break;
                    case "Running":
                    running++;
                     break;
                    case "NotStarted":
                    notStarted++;
                    break;

                    }
                }
            }
        }
```

Aby uzyskać szczegółowe informacje dotyczące poprzedniego wywołania, zobacz nasze [dokument struktury swagger](https://westus.cris.ai/swagger/ui/index). Aby uzyskać pełny przykład pokazano poniżej, przejdź do [GitHub](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI).

> [!NOTE]
> W poprzednim kodzie klucz subskrypcji jest z zasobu mowy, utworzonej w witrynie Azure portal. Klucze, które można uzyskać z zasobu Custom Speech Service nie będą działać.

Zwróć uwagę na asynchroniczne Instalatora w celu opublikowanie audio i odbieranie stanu transkrypcji. Klient, który tworzysz to klient HTTP platformy .NET. Brak `PostTranscriptions` Metoda przesyłania plików audio i `GetTranscriptions` metody do odbierania wyników. `PostTranscriptions` Zwraca uchwyt, a `GetTranscriptions` używa jej do utworzenia dojście można pobrać stanu transkrypcji.

Bieżący kod przykładowy nie określono modelu niestandardowego. Usługa używa modele planu bazowego dla przepisywania pliku lub plików. Aby określić te modele, można przekazać w tej samej metody identyfikatory modelu akustycznego i modelu języka.

Jeśli nie chcesz używać punktu odniesienia, należy przekazać identyfikatory modelu dla modeli zarówno akustyczne i językowe.

> [!NOTE]
> Dla linii bazowej transkrypcje nie trzeba deklarować punktów końcowych w modelu odniesienia. Jeśli chcesz korzystać z niestandardowych modeli, podaj ich identyfikatory punktów końcowych jako [przykładowe](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI). Chcąc planu bazowego akustyczny za pomocą modelu języka podstawowego, należy zadeklarować tylko identyfikator punktu końcowego modelu niestandardowego. Microsoft wykrywa model linii bazowej partnera&mdash;czy akustyczne lub języka&mdash;i używa ich do spełnienia żądania transkrypcji.

### <a name="supported-storage"></a>Obsługiwane

Obecnie tylko magazynu obsługiwane jest usługi Azure Blob storage.

## <a name="download-the-sample"></a>Pobierz przykład

Przykład można znaleźć w tym artykule, na [GitHub](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI).

> [!NOTE]
> Transkrypcję audio zazwyczaj wymaga przedział czasu równy okresowi plik audio oraz obciążenie dwu - na trzy minuty.

## <a name="next-steps"></a>Kolejne kroki

* [Pobierz subskrypcję usługi mowy w wersji próbnej](https://azure.microsoft.com/try/cognitive-services/)
