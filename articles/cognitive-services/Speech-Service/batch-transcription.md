---
title: Usługa Azure Batch transkrypcji interfejsu API
description: Przykłady
services: cognitive-services
author: PanosPeriorellis
ms.service: cognitive-services
ms.technology: Speech to Text
ms.topic: article
ms.date: 04/26/2018
ms.author: panosper
ms.openlocfilehash: b6fb39ef5941157cfe0d18324deeb9d836d7ab09
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/11/2018
ms.locfileid: "44377625"
---
# <a name="batch-transcription"></a>Transkrypcja wsadowa

Transkrypcja partii jest idealnym rozwiązaniem w przypadku dużych ilości audio. Można wskazać pliki audio i wrócić transkrypcje w trybie asynchronicznym.

## <a name="batch-transcription-api"></a>Batch transkrypcji interfejsu API

Transkrypcji interfejsu API usługi Batch oferuje asynchronicznego zamiana mowy na tekst transkrypcji, oraz dodatkowe funkcje.

> [!NOTE]
> Interfejs API transkrypcji usługi Batch jest idealny dla wywołania roboczych, które zazwyczaj są gromadzone tysiące godzin audio. Interfejs API jest przeprowadzany przez "fire and forget" filozofią, która ułatwia także dużą liczbą nagrania audio.

### <a name="supported-formats"></a>Obsługiwane formaty

Transkrypcji interfejsu API usługi Batch obsługuje następujące formaty:

Name (Nazwa)| Kanał  |
----|----------|
mp3 |   Narzędzie mono   |   
mp3 |  Stereo  | 
WAV |   Narzędzie mono   |
WAV |  Stereo  |

Dla stereo strumieni audio transkrypcji Batch dzieli kanału lewy i prawy podczas transkrypcji. Każdy dwa pliki JSON z wynikiem są tworzone z pojedynczy kanał. Sygnatury czasowe na wypowiedź Włącz dla deweloperów utworzyć uporządkowany końcowego transkrypcji. Poniższy przykładowy kod JSON zawiera dane wyjściowe kanału.

```json
       {
        "recordingsUrl": "https://mystorage.blob.core.windows.net/cris-e2e-datasets/TranscriptionsDataset/small_sentence.wav?st=2018-04-19T15:56:00Z&se=2040-04-21T15:56:00Z&sp=rl&sv=2017-04-17&sr=b&sig=DtvXbMYquDWQ2OkhAenGuyZI%2BYgaa3cyvdQoHKIBGdQ%3D",
        "resultsUrls": {
            "channel_0": "https://mystorage.blob.core.windows.net/bestor-87a0286f-304c-4636-b6bd-b3a96166df28/TranscriptionData/24265e4c-e459-4384-b572-5e3e7795221f?sv=2017-04-17&sr=b&sig=IY2qd%2Fkgtz2PwRe2C88BphH4Hv%2F1VCb1UVJ33xsw%2BEY%3D&se=2018-04-23T14:48:24Z&sp=r"
        },
        "statusMessage": "None.",
        "id": "0bb95356-ff06-469d-acc7-81f9144a269a",
        "createdDateTime": "2018-04-20T14:11:57.167",
        "lastActionDateTime": "2018-04-20T14:12:54.643",
        "status": "Succeeded",
        "locale": "en-US"
    },
```

> [!NOTE]
> Transkrypcji interfejsu API usługi Batch korzysta z usługi REST, do żądania transkrypcje, stanu i skojarzonych wyników. Można użyć interfejsu API z dowolnego języka. W następnej sekcji opisano, jak są używane.

## <a name="authorization-token"></a>Token autoryzacji

Zgodnie z wszystkich funkcji programu Unified Speech Service, Utwórz klucz subskrypcji z [witryny Azure portal](https://portal.azure.com). Ponadto możesz uzyskać klucz interfejsu API z poziomu portalu mowy: 

1. Zaloguj się do [Custom Speech](https://customspeech.ai).

2. Wybierz pozycję **Subskrypcje**.

3. Wybierz **generowanie klucza interfejsu API**.

    ![Zrzut ekranu Custom Speech subskrypcji strony](media/stt/Subscriptions.jpg)

4. Skopiuj i Wklej klucz w kodzie klienta w następującym przykładzie.

> [!NOTE]
> Jeśli planujesz użyć niestandardowego modelu, konieczne będzie identyfikator modelu zbyt. Należy pamiętać, że nie jest to identyfikator wdrożenia lub punkt końcowy, który można znaleźć w widoku Szczegóły punktu końcowego. To identyfikator modelu, który można pobrać po wybraniu szczegółów tego modelu.

## <a name="sample-code"></a>Przykładowy kod

Dostosuj następujący przykładowy kod z kluczem subskrypcji oraz klucza interfejsu API. Dzięki temu można uzyskać tokenu elementu nośnego.

```cs
    public static async Task<CrisClient> CreateApiV1ClientAsync(string username, string key, string hostName, int port)
        {
            var client = new HttpClient();
            client.Timeout = TimeSpan.FromMinutes(25);
            client.BaseAddress = new UriBuilder(Uri.UriSchemeHttps, hostName, port).Uri;

            var tokenProviderPath = "/oauth/ctoken";
            var clientToken = await CreateClientTokenAsync(client, hostName, port, tokenProviderPath, username, key).ConfigureAwait(false);
            client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("bearer", clientToken.AccessToken);

            return new CrisClient(client);
        }
```

Po uzyskaniu tokenu, należy określić identyfikator URI sygnatury dostępu Współdzielonego, wskazujący na plik dźwiękowy wymagające transkrypcji. Pozostała część kodu iteruje przez stan i wyświetla wyniki.

```cs
   static async Task TranscribeAsync()
        { 
            private const string SubscriptionKey = "<your Speech[Preview] subscription key>";
            private const string HostName = "cris.ai";
            private const int Port = 443;
    
            // Creating a Batch transcription API Client
            var client = CrisClient.CreateApiV2Client(SubscriptionKey, HostName, Port);
            
            var transcriptions = await client.GetTranscriptionAsync().ConfigureAwait(false);

            var transcriptionLocation = await client.PostTranscriptionAsync(Name, Description, Locale, new Uri(RecordingsBlobUri), new[] { AdaptedAcousticId, AdaptedLanguageId }).ConfigureAwait(false);

            // get the transcription Id from the location URI
            var createdTranscriptions = new List<Guid>();
            createdTranscriptions.Add(new Guid(transcriptionLocation.ToString().Split('/').LastOrDefault()))

            while (true)
            {
                // get all transcriptions for the user
                transcriptions = await client.GetTranscriptionAsync().ConfigureAwait(false);
                completed = 0; running = 0; notStarted = 0;

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
                                // not creted form here, continue
                                continue;
                            }
                            
                            completed++;
                            
                            // if the transcription was successfull, check the results
                            if (transcription.Status == "Succeeded")
                            {
                                var resultsUri = transcription.ResultsUrls["channel_0"];
                                WebClient webClient = new WebClient();
                                var filename = Path.GetTempFileName();
                                webClient.DownloadFile(resultsUri, filename);
                                var results = File.ReadAllText(filename);
                                Console.WriteLine("Transcription succedded. Results: ");
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

                Console.WriteLine(string.Format("Transcriptions status: {0} completed, {1} running, {2} not started yet", completed, running, notStarted));

                await Task.Delay(TimeSpan.FromSeconds(5)).ConfigureAwait(false);
            }

            Console.WriteLine("Press any key...");
        }
```

> [!NOTE]
> W poprzednim kodzie klucz subskrypcji jest z zasobu Speech(Preview), które tworzysz w witrynie Azure portal. Kluczy uzyskanych z zasobów Custom Speech Service nie będą działać.

Zwróć uwagę, asynchronicznego Instalatora w celu opublikowanie audio i odbieranie stanu transkrypcji. Klient utworzone to klient Http platformy .NET. Brak `PostTranscriptions` metodę szczegóły pliku audio i `GetTranscriptions` metodę, aby otrzymać wyniki. `PostTranscriptions` Zwraca uchwyt, a `GetTranscriptions` używa tego dojścia w celu utworzenia dojścia do uzyskania stanu transkrypcji.

Bieżący kod przykładowy nie określono żadnych niestandardowych modeli. Usługa używa modele planu bazowego dla przepisywania pliku lub plików. Aby określić te modele, w tej samej metody można przekazać identyfikatory modelu akustycznego i modelu języka. 

Jeśli nie chcesz używać punktu odniesienia, należy przekazać identyfikatory modelu dla modeli zarówno akustyczne i językowe.

> [!NOTE]
> Dla linii bazowej transkrypcji nie trzeba deklarować punktów końcowych w modelu odniesienia. Jeśli chcesz korzystać z niestandardowych modeli, podaj ich identyfikatory punktów końcowych jako [przykładowe](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI). Jeśli chcesz planu bazowego akustyczny za pomocą modelu języka podstawowego, masz do deklarowania identyfikatora modelu niestandardowego punktu końcowego. Microsoft wykrywa model linii bazowej partnera (można go akustyczny lub język) i użyty do spełnienia żądania transkrypcji.

### <a name="supported-storage"></a>Obsługiwane

Obecnie tylko magazynu obsługiwane jest usługi Azure Blob storage.

## <a name="downloading-the-sample"></a>Pobieranie przykładu

Przykładowych pokazano, w tym miejscu znajduje się na [GitHub](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI).

> [!NOTE]
> Zazwyczaj transkrypcję audio wymaga przedział czasu równy okresowi plik audio oraz obciążenie 2 – 3 minuty.

## <a name="next-steps"></a>Kolejne kroki

* [Pobierz subskrypcję usługi mowy w wersji próbnej](https://azure.microsoft.com/try/cognitive-services/)
