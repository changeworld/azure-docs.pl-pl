---
title: Przekazywanie i indeksowanie plików wideo za pomocą usługi Video Indexer
titlesuffix: Azure Media Services
description: W tym temacie pokazano, jak przy użyciu interfejsów API przekazywać i indeksować pliki wideo za pomocą usługi Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: 8fffc74075abf6dcc4b5c293819f739a9725646b
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/23/2019
ms.locfileid: "69998172"
---
# <a name="upload-and-index-your-videos"></a>Przekazywanie i indeksowanie plików wideo  

Podczas przekazywania filmów wideo za pomocą interfejsu API Video Indexer dostępne są następujące opcje przekazywania: 

* przekazywanie pliku wideo z adresu URL (opcja preferowana),
* wysyłanie pliku wideo w postaci tablicy bajtów w treści żądania,
* Użyj istniejącego elementu zawartości Azure Media Services, podając [Identyfikator elementu zawartości](https://docs.microsoft.com/azure/media-services/latest/assets-concept) (obsługiwane tylko w przypadku płatnych kont).

W tym artykule pokazano, jak za pomocą interfejsu API [przekazywania pliku wideo](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) przekazywać i indeksować pliki wideo na podstawie adresów URL. Przykładowy kod podany w artykule zawiera oznaczony jako komentarz kod, w którym pokazano, jak przekazać tablicę bajtów. <br/>W artykule omówiono też niektóre parametry, które można ustawić w interfejsie API, aby zmieniać przetwarzanie i dane wyjściowe interfejsu API.

Po przekazaniu wideo Video Indexer opcjonalnie koduje wideo (omówione w artykule). Podczas tworzenia konta w usłudze Video Indexer można wybrać konto bezpłatnej wersji próbnej (w ramach którego otrzymuje się określoną liczbę bezpłatnych minut indeksowania) lub opcję płatną (w przypadku której nie ma ograniczeń przydziału). Usługa Video Indexer w bezpłatnej wersji próbnej udostępnia do 600 minut bezpłatnego indeksowania u użytkowników witryn internetowych oraz do 2400 minut bezpłatnego indeksowania u użytkowników interfejsów API. Opcja with płatna umożliwia utworzenie konta Video Indexer, które jest [połączone z subskrypcją platformy Azure i kontem Azure Media Services](connect-to-azure.md). Naliczane są opłaty za minuty indeksowania, a także opłaty powiązane z kontem usługi Media. 

## <a name="uploading-considerations"></a>Zagadnienia dotyczące przekazywania

- W przypadku przekazywania pliku wideo na podstawie adresu URL (opcja preferowana) punkt końcowy musi być zabezpieczony za pomocą protokołu TLS 1.2 (lub nowszej wersji)
- Rozmiar przekazywania z adresem URL jest ograniczony do 30 GB
- Długość adresu URL żądania jest ograniczona do 2048 znaków
- Rozmiar przekazywania z opcją tablicy bajtowej jest ograniczony do 2 GB
- Opcja tablicy bajtowej jest przekreślania po 30 minutach
- Adres URL podany w parametrze `videoURL` musi być zakodowany
- Indeksowanie Media Services elementów zawartości ma takie samo ograniczenie jak indeksowanie z adresu URL
- Video Indexer ma maksymalny limit czasu trwania wynoszący 4 godziny dla pojedynczego pliku

> [!Tip]
> Zalecane jest korzystanie z platformy .NET w wersji 4.6.2. lub nowszej, ponieważ starsze wersje platformy .NET nie obsługują domyślnie protokołu TLS 1.2.
>
> Jeśli musisz użyć starszej platformy .NET, dodaj jeden wiersz do swojego kodu przed wprowadzeniem wywołania interfejsu API REST:  <br/> System.Net.ServicePointManager.SecurityProtocol = SecurityProtocolType.Tls | SecurityProtocolType.Tls11 | SecurityProtocolType.Tls12;

## <a name="configurations-and-params"></a>Konfiguracje i parametry

W tej sekcji opisano niektóre parametry opcjonalne i wyjaśniono, kiedy należy je ustawić.

### <a name="externalid"></a>externalID 

Ten parametr umożliwia określenie identyfikatora, który zostanie skojarzony z plikiem wideo. Ten identyfikator można zastosować do integracji z zewnętrznym systemem zarządzania zawartością wideo (VCM, Video Content Management). Pliki wideo znajdujące się w portalu usługi Video Indexer można wyszukiwać za pomocą tego określonego identyfikatora zewnętrznego.

### <a name="callbackurl"></a>callbackUrl

Adres URL używany do powiadamiania klienta (za pomocą żądania POST) o następujących zdarzeniach:

- Zmiana stanu indeksowania: 
    - Właściwości:    
    
        |Name|Opis|
        |---|---|
        |id|Identyfikator wideo|
        |state|Stan wideo|  
    - Przykład: https:\//test.com/NotifyMe?projectName=MyProject&ID=1234abcd&State=processed
- Osoba rozpoznana na filmie wideo:
  - Właściwości
    
      |Name|Opis|
      |---|---|
      |id| Identyfikator wideo|
      |faceId|Identyfikator Face ID w indeksie wideo|
      |knownPersonId|Identyfikator osoby, unikatowy w ramach danego modelu twarzy|
      |personName|Imię i nazwisko osoby|
        
    - Przykład: https:\//test.com/NotifyMe?projectName=MyProject&ID=1234abcd&FaceID=12&knownPersonId=CCA84350-89B7-4262-861C-3CAC796542A5&personName=Inigo_Montoya 

#### <a name="notes"></a>Uwagi

- Usługa Video Indexer zwraca wszelkie istniejące parametry podane w oryginalnym adresie URL.
- Podany adres URL musi być zakodowany.

### <a name="indexingpreset"></a>indexingPreset

Tego parametru należy użyć, jeśli nagrania nieprzetworzone lub zewnętrzne zawierają hałas w tle. Parametr ten służy do konfigurowania procesu indeksowania. Można określić następujące wartości:

- `Default` — indeksowanie i wyodrębnianie szczegółowych informacji przy użyciu części zarówno audio, jak i wideo
- `AudioOnly` — indeksowanie i wyodrębnianie szczegółowych informacji przy użyciu tylko części audio (z ignorowaniem części wideo)
- `DefaultWithNoiseReduction` — indeksowanie i wyodrębnianie szczegółowych informacji przy użyciu zarówno audio, jak i wideo przy zastosowaniu algorytmów redukcji szumów w strumieniu audio

Cena zależy od wybranej opcji indeksowania.  

### <a name="priority"></a>priority

Usługa Video Indexer indeksuje filmy wideo zgodnie z ich priorytetem. Użyj parametru **priority**, aby określić priorytet indeksu. Następujące wartości są prawidłowe: **Niska**, **normalna** (domyślna) i **wysoka**.

Parametr **priority** jest obsługiwany tylko w przypadku płatnych kont.

### <a name="streamingpreset"></a>streamingPreset

Po przekazaniu pliku wideo usługa Video Indexer opcjonalnie koduje ten plik. Następnie przechodzi do indeksowania i analizowania pliku wideo. Po zakończeniu analizowania przez usługę Video Indexer otrzymasz powiadomienie z identyfikatorem pliku wideo.  

W przypadku korzystania z interfejsu API [przekazywania pliku wideo](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) lub [ponownego indeksowania pliku wideo](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?) jednym z parametrów opcjonalnych jest `streamingPreset`. W razie ustawienia dla parametru `streamingPreset` wartości `Default`, `SingleBitrate` lub `AdaptiveBitrate` wywoływany jest proces kodowania. Po zakończeniu zadań indeksowania i kodowania plik wideo jest publikowany, aby można go było również przesyłać strumieniowo. Punkt końcowy przesyłania strumieniowego, z którego chcesz strumieniowo przesyłać plik wideo, musi mieć stan **Uruchomiony**.

Aby można było uruchomić zadania indeksowania i kodowania, dla [konta usługi Azure Media Services połączonego z kontem usługi Video Indexer](connect-to-azure.md) wymagane są jednostki zarezerwowane. Aby uzyskać więcej informacji, zobacz [Scaling Media Processing](https://docs.microsoft.com/azure/media-services/previous/media-services-scale-media-processing-overview) (Skalowanie przetwarzania multimediów). Ponieważ te zadania wymagają intensywnego przetwarzania, zdecydowanie zaleca się typ jednostki S3. Liczba jednostek zarezerwowanych określa maksymalną liczbę zadań, które mogą działać równolegle. Zalecenie dotyczące planu bazowego to 10 jednostek zarezerwowanych S3. 

Jeśli chcesz tylko zaindeksować plik wideo bez kodowania go, ustaw dla parametru `streamingPreset` wartość `NoStreaming`.

### <a name="videourl"></a>videoUrl

Adres URL pliku wideo/audio do zaindeksowania. Ten adres URL musi wskazywać plik multimedialny (strony HTML nie są obsługiwane). Plik może być chroniony przez token dostępu podany w ramach identyfikatora URI, a punkt końcowy obsługujący plik musi być zabezpieczony za pomocą protokołu TLS 1.2 lub nowszej wersji. Adres URL musi być zakodowany. 

Jeśli parametr `videoUrl` nie zostanie określony, usługa Video Indexer oczekuje przekazania pliku jako zawartości treści wieloczęściowej/formularza.

## <a name="code-sample"></a>Przykład kodu

W poniższym fragmencie kodu języka C# pokazano używanie wszystkich interfejsów API usługi Video Indexer razem.

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
    //byte[] buffer =new byte[video.Length];
    //video.Read(buffer, 0, buffer.Length);
    //content.Add(new ByteArrayContent(buffer));

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

    // get the video ID from the upload result
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
## <a name="common-errors"></a>Typowe błędy

Kody stanu wymienione w poniższej tabeli mogą być zwracane przez operację przekazywania.

|Kod stanu|ErrorType (w treści odpowiedzi)|Opis|
|---|---|---|
|400|VIDEO_ALREADY_IN_PROGRESS|Ten sam plik wideo jest już w trakcie przetwarzania w ramach danego konta.|
|400|VIDEO_ALREADY_FAILED|Tego samego pliku wideo nie udało się przetworzyć w ramach danego konta mniej niż 2 godziny temu. Klienci interfejsu API powinni poczekać co najmniej 2 godziny przed ponownym przekazaniem pliku wideo.|

## <a name="next-steps"></a>Następne kroki

[Sprawdzanie danych wyjściowych platformy Azure Video Indexer utworzonych przez interfejs API](video-indexer-output-json-v2.md)
