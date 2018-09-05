---
title: Przekaż i Zindeksuj filmów wideo za pomocą indeksatora wideo platformy Azure | Dokumentacja firmy Microsoft
description: W tym temacie pokazano, jak używać interfejsów API, przekazywanie i indeksować wideo za pomocą indeksatora wideo usługi Azure
services: cognitive services
documentationcenter: ''
author: juliako
manager: erikre
ms.service: cognitive-services
ms.topic: article
ms.date: 08/17/2018
ms.author: juliako
ms.openlocfilehash: ac9d3f8fd10a3b65a2af2999b8c7ade7965de912
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/04/2018
ms.locfileid: "43664448"
---
# <a name="upload-and-index-your-videos"></a>Przekazywanie i indeksować wideo  

W tym artykule przedstawiono sposób przekazywania plików wideo za pomocą indeksatora wideo usługi Azure. Interfejsu API indeksatora wideo są dostępne dwie opcje przekazywania: 

* Przekaż wideo z adresu URL (preferowane)
* Wyślij plik wideo w postaci tablicy bajtów w treści żądania.

W tym artykule opisano sposób używania [Przekaż wideo](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) interfejsu API, aby przekazać i indeksować wideo na podstawie adresu URL. Przykładowy kod w artykule zawiera komentarze kod, który pokazuje, jak przekazać tablicę bajtów.  

W artykule omówiono niektóre parametry, można ustawić w interfejsie API, zmienić procesów i danych wyjściowych interfejsu API.

> [!Note]
> Podczas tworzenia konta indeksatora wideo, możesz wybrać bezpłatne konto próbne (skąd określonej liczbie bezpłatnych minut indeksowania) lub opcję płatną (gdzie możesz nie są ograniczone według przydziału). <br/>Za pomocą bezpłatnej wersji próbnej Video Indexer zapewnia do 600 minut indeksować bezpłatne dla użytkowników witryny sieci Web, i maksymalnie 2 400 minut bezpłatne indeksowania dla użytkowników interfejsu API. W przypadku płatnych opcji tworzenia konta Video Indexer, który jest [podłączone do subskrypcji platformy Azure i konto usługi Azure Media Services](connect-to-azure.md). Płacisz indeksowane w ciągu kilku minut, a także konta multimediów powiązane opłaty. 

## <a name="uploading-considerations"></a>Zagadnienia dotyczące przekazywania
    
- Podczas przekazywania filmu wideo na podstawie adresu URL (preferowany) punktu końcowego musi zostać zabezpieczony za pomocą protokołu TLS 1.2 (lub nowszy)
- Opcja tablicy bajtów jest ograniczony do 4GB i upłynie limit czasu po 30 min
- Podany adres URL w `videoURL` param musi być zakodowany

## <a name="configurations-and-params"></a>Konfiguracje i parametry

W tej sekcji opisano niektóre parametry opcjonalne i chcesz je ustawić.

### <a name="externalid"></a>externalID 

Ten parametr umożliwia określenie Identyfikatora, która zostanie skojarzona z wideo. Identyfikator można zastosować do zewnętrznego integracji systemów "wideo zawartości" Zarządzanie (VCM). Filmy wideo, które znajdują się w portalu usługi Video Indexer można przeszukiwać za pomocą określonego identyfikatora zewnętrznego.

### <a name="indexingpreset"></a>indexingPreset

Użyj tego parametru, jeśli nagrania raw lub zewnętrznego zawierają hałas w tle. Ten parametr jest używany do konfigurowania procesu indeksowania. Można określić następujące wartości:

- `Default` — Indeks i wyodrębnić szczegółowe informacje przy użyciu audio i wideo
- `AudioOnly` — Indeks i wyodrębnić szczegółowe informacje przy użyciu tylko audio (Ignorowanie wideo)
- `DefaultWithNoiseReduction` — Indeks i prowadzenie analiz audio i wideo, przy zastosowaniu algorytmów redukcji szumów strumienia audio

Cena zależy od wybranej opcji indeksowania.  

### <a name="callbackurl"></a>callbackUrl

Adres URL wpisu na potrzeby powiadomień podczas indeksowania zostanie ukończona. Usługa Video Indexer dodaje dwa parametry ciągu do niej zapytania: identyfikator i stan. Na przykład, jeśli jest adres url wywołania zwrotnego "https://test.com/notifyme?projectName=MyProject", powiadomienia będą wysyłane z dodatkowe parametry"https://test.com/notifyme?projectName=MyProject&id=1234abcd&state=Processed".

Można również dodać więcej parametrów do adresu URL, przed opublikowaniem wywołań do usługi Video Indexer i te parametry zostaną uwzględnione podczas wywołania zwrotnego. Później w kodzie, można przeanalizować ciągu zapytania i uzyskać kopii wszystkich określonych parametrów ciągu zapytania (dane, który pierwotnie miały możesz dołączyć do adresu URL, a także informacje Video Indexer dostarczone.) Adres URL musi być zakodowany.

### <a name="streamingpreset"></a>streamingPreset

Po przekazaniu filmu wideo usługa Video Indexer opcjonalnie koduje wideo. Następnie przechodzi do indeksowania i analizowanie filmu wideo. Po zakończeniu Video Indexer analizowanie, otrzymasz powiadomienie z identyfikatorem wideo.  

Korzystając z [Przekaż wideo](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) lub [ponownie Poindeksuj wideo](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?) interfejsu API jest jeden z parametrów opcjonalnych `streamingPreset`. Jeśli ustawisz `streamingPreset` do `Default`, `SingleBitrate`, lub `AdaptiveBitrate`, zostanie wywołany procesu kodowania. Po operacji indeksowania i zadań kodowania wideo został opublikowany, więc również strumieniowego przesyłania wideo. Punkt końcowy przesyłania strumieniowego, z którego chcesz strumieniowo przesyłać wideo musi znajdować się w **systemem** stanu.

Aby można było uruchomić indeksowanie i zadań kodowania oraz [konta usługi Azure Media Services połączyć swoje konto usługi Video Indexer](connect-to-azure.md), wymaga jednostek zarezerwowanych. Aby uzyskać więcej informacji, zobacz [skalowanie przetwarzania multimediów](https://docs.microsoft.com/azure/media-services/previous/media-services-scale-media-processing-overview). Ponieważ te zadania intensywnie korzystających z obliczeń, zdecydowanie zaleca się typ jednostki S3. Liczbę jednostek zarezerwowanych określa maksymalną liczbę zadań, które mogą działać równolegle. Zalecenie dotyczące planu bazowego jest 10 jednostki zarezerwowane S3. 

Jeśli chcesz tylko indeksować wideo, ale nie Koduj go ustawić `streamingPreset`do `NoStreaming`.

### <a name="videourl"></a>videoUrl

Adres URL pliku wideo i audio mają być indeksowane. Adres URL musi wskazywać na plik multimedialny (stron HTML, które nie są obsługiwane). Plik może być chroniony przez token dostępu w ramach identyfikatora URI, a punkt końcowy obsługujący plik musi być zabezpieczone za pomocą protokołu TLS 1.2 lub nowszej. Adres URL musi być zakodowany. 

Jeśli `videoUrl` nie zostanie określony, indeksatora wideo oczekuje przekazania pliku jako treść multipart/form.

## <a name="code-sample"></a>Przykład kodu

Poniższy fragment kodu języka C# pokazuje użycie wszystkich interfejsów API indeksatora wideo ze sobą.

```csharp
public async Task Sample()
{
    var apiUrl = "https://api.videoindexer.ai";
    var location = "westus2";
    var apiKey = "...";

    System.Net.ServicePointManager.SecurityProtocol =
        System.Net.ServicePointManager.SecurityProtocol | System.Net.SecurityProtocolType.Tls12;

    // create the http client
    var handler = new HttpClientHandler();
    handler.AllowAutoRedirect = false;
    var client = new HttpClient(handler);
    client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);

    // obtain account information and access token
    string queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"generateAccessTokens", "true"},
            {"allowEdit", "true"},
        });
    HttpResponseMessage result = await client.GetAsync($"{apiUrl}/auth/trial/Accounts?{queryParams}");
    var json = await result.Content.ReadAsStringAsync();
    var accounts = JsonConvert.DeserializeObject<AccountContractSlim[]>(json);
    // take the relevant account, here we simply take the first
    var accountInfo = accounts.First();

    // we will use the access token from here on, no need for the apim key
    client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

    // upload a video
    var content = new MultipartFormDataContent();
    Debug.WriteLine("Uploading...");
    // get the video from URL
    var videoUrl = "VIDEO_URL"; // replace with the video URL

    // as an alternative to specifying video URL, you can upload a file.
    // remove the videoUrl parameter from the query params below and add the following lines:
    //FileStream video =File.OpenRead(Globals.VIDEOFILE_PATH);
    //byte[] buffer =newbyte[video.Length];
    //video.Read(buffer, 0, buffer.Length);
    //content.Add(newByteArrayContent(buffer));

    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", accountInfo.AccessToken},
            {"name", "video_name"},
            {"description", "video_description"},
            {"privacy", "private"},
            {"partition", "partition"},
            {"videoUrl", videoUrl},
        });
    var uploadRequestResult = await client.PostAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos?{queryParams}", content);
    var uploadResult = await uploadRequestResult.Content.ReadAsStringAsync();

    // get the video id from the upload result
    string videoId = JsonConvert.DeserializeObject<dynamic>(uploadResult)["id"];
    Debug.WriteLine("Uploaded");
    Debug.WriteLine("Video ID:");
    Debug.WriteLine(videoId);

    // wait for the video index to finish
    while (true)
    {
        await Task.Delay(10000);

        queryParams = CreateQueryString(
            new Dictionary<string, string>()
            {
                {"accessToken", accountInfo.AccessToken},
                {"language", "English"},
            });

        var videoGetIndexRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/Index?{queryParams}");
        var videoGetIndexResult = await videoGetIndexRequestResult.Content.ReadAsStringAsync();

        string processingState = JsonConvert.DeserializeObject<dynamic>(videoGetIndexResult)["state"];

        Debug.WriteLine("");
        Debug.WriteLine("State:");
        Debug.WriteLine(processingState);

        // job is finished
        if (processingState != "Uploaded" && processingState != "Processing")
        {
            Debug.WriteLine("");
            Debug.WriteLine("Full JSON:");
            Debug.WriteLine(videoGetIndexResult);
            break;
        }
    }

    // search for the video
    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", accountInfo.AccessToken},
            {"id", videoId},
        });

    var searchRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/Search?{queryParams}");
    var searchResult = await searchRequestResult.Content.ReadAsStringAsync();
    Debug.WriteLine("");
    Debug.WriteLine("Search:");
    Debug.WriteLine(searchResult);

    // Generate video access token (used for get widget calls)
    client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);
    var videoTokenRequestResult = await client.GetAsync($"{apiUrl}/auth/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/AccessToken?allowEdit=true");
    var videoAccessToken = (await videoTokenRequestResult.Content.ReadAsStringAsync()).Replace("\"", "");
    client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

    // get insights widget url
    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", videoAccessToken},
            {"widgetType", "Keywords"},
            {"allowEdit", "true"},
        });
    var insightsWidgetRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/InsightsWidget?{queryParams}");
    var insightsWidgetLink = insightsWidgetRequestResult.Headers.Location;
    Debug.WriteLine("Insights Widget url:");
    Debug.WriteLine(insightsWidgetLink);

    // get player widget url
    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", videoAccessToken},
        });
    var playerWidgetRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/PlayerWidget?{queryParams}");
    var playerWidgetLink = playerWidgetRequestResult.Headers.Location;
    Debug.WriteLine("");
    Debug.WriteLine("Player Widget url:");
    Debug.WriteLine(playerWidgetLink);
}

private string CreateQueryString(IDictionary<string, string> parameters)
{
    var queryParameters = HttpUtility.ParseQueryString(string.Empty);
    foreach (var parameter in parameters)
    {
        queryParameters[parameter.Key] = parameter.Value;
    }

    return queryParameters.ToString();
}

public class AccountContractSlim
{
    public Guid Id { get; set; }
    public string Name { get; set; }
    public string Location { get; set; }
    public string AccountType { get; set; }
    public string Url { get; set; }
    public string AccessToken { get; set; }
}
```



## <a name="next-steps"></a>Kolejne kroki

[Sprawdź dane wyjściowe Azure Video Indexer produkowane przez interfejsy API wersji 2](video-indexer-output-json-v2.md)
