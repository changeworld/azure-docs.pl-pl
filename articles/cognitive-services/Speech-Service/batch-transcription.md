---
title: Usługa Azure Batch transkrypcji interfejsu API | Dokumentacja firmy Microsoft Azure
description: Przykłady
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.technology: Speech to Text
ms.topic: article
ms.date: 04/26/2018
ms.author: panosper
ms.openlocfilehash: 9dd7479ae95f74123d9b762e42ec95e8dbf25818
ms.sourcegitcommit: 756f866be058a8223332d91c86139eb7edea80cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2018
ms.locfileid: "37346448"
---
# <a name="batch-transcription"></a>Transkrypcja wsadowa

Transkrypcja partii jest idealny dla przypadków użycia z dużą ilością audio. Dzięki temu deweloper wskaż plików audio i uzyskanie transkrypcje w trybie asynchronicznym.

## <a name="batch-transcription-api"></a>Batch transkrypcji interfejsu API

Transkrypcji interfejsu API usługi Batch pozwala powyższym scenariuszu. Oferuje ona asynchroniczne zamiana mowy na tekst transkrypcji oraz dodatkowe funkcje.

> [!NOTE]
> Transkrypcji interfejsu API usługi Batch jest idealny dla biurem obsługi zwykle się tysiące godzin audio. Ogień & zapomnij Filozofia interfejsu API ułatwia także dużą liczbą nagrania audio.

### <a name="supported-formats"></a>Obsługiwane formaty

Transkrypcji interfejsu API usługi Batch ma na celu stają się de facto we wszystkich scenariuszach związanych z Centrum wywołań w trybie offline i oferują pomoc techniczną dla wszystkich powiązanych formatów. Obecnie obsługiwane formaty:

Name (Nazwa)| Kanał  |
----|----------|
mp3 |   Narzędzie mono   |   
mp3 |  Stereo  | 
WAV |   Narzędzie mono   |
WAV |  Stereo  |

Dla stereo strumieni audio transkrypcji Batch zostanie podzielona kanału lewy i prawy podczas transkrypcji. Każdy dwa pliki JSON z wynikiem są tworzone z pojedynczy kanał. Sygnatury czasowe na wypowiedź Włącz dla deweloperów utworzyć uporządkowany końcowego transkrypcji. Poniższy przykładowy kod JSON zawiera dane wyjściowe kanału.

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
> Transkrypcji interfejsu API usługi Batch korzysta z usługi REST, do żądania transkrypcje, stanu i skojarzonych wyników. Interfejs API można używać z dowolnego języka. W następnej sekcji opisano, jak są używane.

## <a name="authorization-token"></a>Token autoryzacji

Zgodnie z wszystkich funkcji programu Unified Speech Service, użytkownik musi utworzyć klucz subskrypcji z [witryny Azure portal](https://portal.azure.com). Ponadto klucza interfejsu API musi zostać uzyskane z portalu mowy. Kroki, aby wygenerować klucz interfejsu API:

1. Zaloguj się do https://customspeech.ai.

2. Kliknij pozycję subskrypcje.

3. Kliknij opcję `Generate API Key`.

    ![Wyświetl przekazywania](media/stt/Subscriptions.jpg)

4. Skopiuj i Wklej klucz w kodzie klienta w poniższym przykładzie.

> [!NOTE]
> Jeśli planujesz użyć niestandardowego modelu należy identyfikator modelu zbyt. Należy pamiętać, że to nie wdrożenia lub identyfikator punktu końcowego, który można znaleźć w widoku Szczegóły punktu końcowego, ale identyfikator modelu, który można pobrać po kliknięciu szczegółowe informacje o modelu

## <a name="sample-code"></a>Przykładowy kod

Korzystanie z interfejsu API jest już bardzo proste. Przykładowy kod poniżej musi można dostosować za pomocą klucz subskrypcji i klucz interfejsu API, który z kolei umożliwia deweloperom uzyskanie tokenu elementu nośnego, jak poniższy fragment kodu przedstawia:

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

Po uzyskaniu tokenu deweloper musi Określ identyfikator Uri sygnatury dostępu Współdzielonego, wskazujący na plik dźwiękowy wymagające transkrypcji. Pozostała część kodu po prostu wykonuje iterację przez stan i wyświetla wyniki.

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
> Klucz subskrypcji wspomniano w powyższym fragmencie kodu jest kluczem z zasobu Speech(Preview), które tworzysz w witrynie Azure portal. Kluczy uzyskanych z zasobów Custom Speech Service nie będzie działać.


Zwróć uwagę, asynchronicznego Instalatora w celu opublikowanie audio i odbieranie stanu transkrypcji. Klient utworzone to klient Http platformy .NET. Brak `PostTranscriptions` metodę szczegóły pliku audio i `GetTranscriptions` metodę, aby otrzymać wyniki. `PostTranscriptions` Zwraca uchwyt, a `GetTranscriptions` metody jest tworzenie przy użyciu tego uchwytu dojścia do uzyskania stanu transkrypcji.

Bieżący kod przykładowy nie określono żadnych niestandardowych modeli. Modele planu bazowego dla przepisywania plików będzie używane przez usługę. Gdy użytkownik chce określić te modele, jeden może przekazać w tej samej metody modelIDs akustyczne i modelu języka. 

Jeśli jeden nie chcesz używać punktu odniesienia, jeden musi pomyślnie przejść identyfikatory modelu dla modeli zarówno akustyczne i językowe.

> [!NOTE]
> Dla linii bazowej transkrypcji użytkownik nie ma do deklarowania punktów końcowych w modelu odniesienia. Jeśli użytkownik chce, aby korzystać z niestandardowych modeli miałby on zapewnienie ich identyfikatory punktów końcowych jako [przykładowe](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI). Jeśli użytkownik chce planu bazowego akustyczny za pomocą modelu języka podstawowego następnie on tylko musi zadeklarować identyfikatora modelu niestandardowego punktu końcowego. Wewnętrznie nasz system ustalenie modelu odniesienia partnera (można go akustyczny lub język) i używać go do spełnienia żądania transkrypcji.

### <a name="supported-storage"></a>Obsługiwane

Obecnie tylko magazynu obsługiwane jest obiektów blob platformy Azure.

## <a name="downloading-the-sample"></a>Pobieranie przykładu

Przykładowe wyświetlane w tym miejscu znajduje się na [GitHub](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI).

> [!NOTE]
> Zazwyczaj transkrypcję audio wymaga przedział czasu równy okresowi plik audio oraz obciążenie 2 – 3 minuty.

## <a name="next-steps"></a>Kolejne kroki

* [Pobierz wersję próbną subskrypcji mowy](https://azure.microsoft.com/try/cognitive-services/)
