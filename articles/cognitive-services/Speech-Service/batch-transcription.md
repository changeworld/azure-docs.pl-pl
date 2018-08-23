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
ms.openlocfilehash: 5af829ca076b39758973c28a44d918b9ba5782b1
ms.sourcegitcommit: fab878ff9aaf4efb3eaff6b7656184b0bafba13b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/22/2018
ms.locfileid: "42351254"
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
            // Creating a Batch transcription API Client
            var client = 
                await CrisClient.CreateApiV1ClientAsync(
                    "<your msa>", // MSA email
                    "<your api key>", // API key
                    "stt.speech.microsoft.com",
                    443).ConfigureAwait(false);
            
            var newLocation = 
                await client.PostTranscriptionAsync(
                    "<selected locale i.e. en-us>", // Locale 
                    "<your subscription key>", // Subscription Key
                    new Uri("<SAS URI to your file>")).ConfigureAwait(false);

            var transcription = await client.GetTranscriptionAsync(newLocation).ConfigureAwait(false);

            while (true)
            {
                transcription = await client.GetTranscriptionAsync(transcription.Id).ConfigureAwait(false);

                if (transcription.Status == "Failed" || transcription.Status == "Succeeded")
                {
                    Console.WriteLine("Transcription complete!");

                    if (transcription.Status == "Succeeded")
                    {
                        var resultsUri = transcription.ResultsUrls["channel_0"];

                        WebClient webClient = new WebClient();

                        var filename = Path.GetTempFileName();
                        webClient.DownloadFile(resultsUri, filename);

                        var results = File.ReadAllText(filename);
                        Console.WriteLine(results);
                    }

                    await client.DeleteTranscriptionAsync(transcription.Id).ConfigureAwait(false);

                    break;
                }
                else
                {
                    Console.WriteLine("Transcription status: " + transcription.Status);
                }

                await Task.Delay(TimeSpan.FromSeconds(5)).ConfigureAwait(false);
            }

            Console.ReadLine();
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
