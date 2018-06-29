---
title: Partia zadań Azure przekształcania interfejsu API | Dokumentacja firmy Microsoft Azure
description: Przykłady
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.technology: Speech to Text
ms.topic: article
ms.date: 04/26/2018
ms.author: panosper
ms.openlocfilehash: cf58f676be52aa16ce6de59c3566613c7ee9276d
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2018
ms.locfileid: "37084086"
---
# <a name="batch-transcription"></a>Przekształcenia partii

Przekształcenia partii jest idealny dla przypadków użycia z dużą ilością audio. Umożliwia deweloperowi wskaż plików audio i wrócić transcriptions w trybie asynchronicznym.

## <a name="batch-transcription-api"></a>Przekształcenia partii interfejsu API

Przekształcenia partii interfejsu API umożliwia powyższym scenariuszu. Zapewnia ona asynchroniczne mowy do przekształcania tekstu oraz dodatkowe funkcje.

> [!NOTE]
> Przekształcenia interfejsu API partii jest idealny dla biurem obsługi zwykle się tysiące godziny audio. Zasady Fire & zapomnij klas interfejsu API można łatwo wykonać transkrypcji dużą liczbę nagrania audio.

### <a name="supported-formats"></a>Obsługiwane formaty

Przekształcenia interfejsu API partii ma de-facto we wszystkich scenariuszach związanych z Centrum wywołanie w trybie offline i oferują obsługę wszystkich powiązanych formatów. Obecnie obsługiwane formaty:

Name (Nazwa)| Kanał  |
----|----------|
mp3 |   Mono   |   
mp3 |  Stereo  | 
WAV |   Mono   |
WAV |  Stereo  |

Stereo strumieni audio przekształcania partii podzieli lewy i prawy kanał podczas zapisu. Każdy dwa pliki JSON z wynikiem są tworzone z jednego kanału. Sygnatury czasowe na utterance umożliwiają deweloperom tworzenie uporządkowanego wykaz końcowego. W poniższym przykładzie JSON zawierają dane wyjściowe kanału.

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
> Przekształcenia interfejsu API partii jest przy użyciu usługi REST żądanych transcriptions, stanu i skojarzone wyników. Jest on oparty na platformie .NET i nie ma żadnych zależności zewnętrzne. W następnej sekcji opisano, jak będą wykorzystywane.

## <a name="authorization-token"></a>Token autoryzacji

Zgodnie z wszystkimi funkcjami usługi mowy Unified, użytkownik musi utworzyć klucz subskrypcji z [portalu Azure](https://portal.azure.com). Ponadto klucz interfejsu API musi zostać pobrany z portalu mowy. Kroki, aby wygenerować klucz interfejsu API:

1. Zaloguj się do https://customspeech.ai.

2. Kliknij pozycję subskrypcje.

3. Kliknij opcję `Generate API Key`.

    ![Widok przekazywania](media/stt/Subscriptions.jpg)

4. Skopiuj i Wklej klucz w kodzie klienta w poniższym przykładzie.

> [!NOTE]
> Jeśli planujesz użyć niestandardowego modelu wymagana będzie identyfikator modelu zbyt. Należy pamiętać, że nie jest wdrożenia lub identyfikator punktu końcowego, które znajdują się w widoku szczegółów punktu końcowego, ale identyfikator modelu, który można pobrać po kliknięciu szczegóły tego modelu

## <a name="sample-code"></a>Przykładowy kod

Użyj interfejsu API jest dość proste do przodu. Poniższy przykładowy kod musi można dostosować za pomocą klucza subskrypcji i klucz interfejsu API, który włącza umożliwia deweloperowi do uzyskania tokenu elementu nośnego jako kod następujący kod fragment kodu przedstawia:

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

Po uzyskaniu tokenu deweloper musi zmiennoprzecinkową identyfikatora Uri połączenia SAS wskazuje plik dźwiękowy wymagających zapisu. Pozostałe kod po prostu iteruje stan i wyświetla wyniki.

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
> Klucz subskrypcji wspomnianego powyżej fragmentu kodu jest na podstawie Speech(Preview) zasobu utworzonego w portalu Azure. Klucze uzyskiwane ze źródła niestandardowej usługi rozpoznawania mowy nie będą działać.


Zwróć uwagę, ustawień asynchroniczne przesyłanie audio i odbieranie stanu przekształcania. Klient NET Http jest klienta utworzone. Brak `PostTranscriptions` metody wysyłania szczegółów pliku audio i `GetTranscriptions` metodę, aby otrzymywać wyniki. `PostTranscriptions` Zwraca uchwyt i `GetTranscriptions` metody jest tworzenie przy użyciu tego uchwytu dojścia do uzyskania stanu przekształcania.

Bieżący kod przykładowy nie określa żadnych niestandardowych modeli. Usługa będzie używać modeli linii bazowej przetwarzanie plików. Jeśli użytkownik chce Określ modele, co można przekazać na tej samej metody modelIDs akustycznego i model języka. 

Jeśli jeden nie chce używać linii bazowej, jeden musi przejść pomyślnie identyfikatory modelu dla modeli zarówno akustycznego i języka.

> [!NOTE]
> Dla linii bazowej przekształcania użytkownik nie ma zadeklarować punkty końcowe modeli linii bazowej. Jeśli użytkownik chce używać niestandardowych modeli on będzie musiał podać ich identyfikatorów punkty końcowe jako [próbki](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI). Jeśli użytkownik chce używać akustycznego linii bazowej z modelem języka linii bazowej następnie on tylko musi zadeklarować identyfikatora modelu niestandardowe punktu końcowego. Wewnętrznie naszym systemie zorientować się modelu linii bazowej partnera (można go akustycznego lub język) i używać, aby wykonać żądanie zapisu.

### <a name="supported-storage"></a>Obsługiwanego magazynu

Obecnie tylko magazyn obsługiwane jest obiektów blob platformy Azure.

## <a name="downloading-the-sample"></a>Pobieranie próbki

Przykładowe wyświetlane w tym miejscu znajduje się na [GitHub](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI).

> [!NOTE]
> Audio przekształcania wymaga zwykle od zakresu czasu równy okresowi plik dźwiękowy oraz koszty 2 – 3 minuty.

## <a name="next-steps"></a>Kolejne kroki

* [Pobierz wersję próbną subskrypcji mowy](https://azure.microsoft.com/try/cognitive-services/)
