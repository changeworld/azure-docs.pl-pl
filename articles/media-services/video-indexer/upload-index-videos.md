---
title: Przekazywanie i indeksowanie plików wideo za pomocą usługi Video Indexer
titleSuffix: Azure Media Services
description: W tym temacie pokazano, jak przy użyciu interfejsów API przekazywać i indeksować pliki wideo za pomocą usługi Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 02/18/2020
ms.author: juliako
ms.openlocfilehash: 245eabdf4d77682c87062c2581239a554112d748
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77468766"
---
# <a name="upload-and-index-your-videos"></a>Przekazywanie i indeksowanie plików wideo  

Podczas przesyłania filmów za pomocą interfejsu API indeksatora wideo dostępne są następujące opcje przekazywania: 

* przekazywanie pliku wideo z adresu URL (opcja preferowana),
* wysyłanie pliku wideo w postaci tablicy bajtów w treści żądania,
* Użyj istniejącego zasobu usługi Azure Media Services, podając [identyfikator zasobu](https://docs.microsoft.com/azure/media-services/latest/assets-concept) (obsługiwany tylko na płatnych kontach).

Po przesłaniu filmu indeksator wideo (opcjonalnie) zakoduje film (omówiony w artykule). Podczas tworzenia konta w usłudze Video Indexer można wybrać konto bezpłatnej wersji próbnej (w ramach którego otrzymuje się określoną liczbę bezpłatnych minut indeksowania) lub opcję płatną (w przypadku której nie ma ograniczeń przydziału). Usługa Video Indexer w bezpłatnej wersji próbnej udostępnia do 600 minut bezpłatnego indeksowania u użytkowników witryn internetowych oraz do 2400 minut bezpłatnego indeksowania u użytkowników interfejsów API. Za pomocą opcji płatnej można utworzyć konto indeksatora wideo, które jest [połączone z subskrypcją platformy Azure i kontem usługi Azure Media Services.](connect-to-azure.md) Naliczane są opłaty za minuty indeksowania, a także opłaty powiązane z kontem usługi Media. 

W tym artykule pokazano, jak przesyłać i indeksować filmy za pomocą następujących opcji:

* [Witryna internetowa firmy Video Indexer](#website) 
* [Interfejsy API indeksatora wideo](#apis)

## <a name="uploading-considerations-and-limitations"></a>Przekazywanie zagadnień i ograniczeń
 
- Nazwa filmu nie może być większa niż 80 znaków.
- Podczas przesyłania filmu na podstawie adresu URL (preferowany) punkt końcowy musi być zabezpieczony tlsem 1.2 (lub nowszym).
- Rozmiar przesyłania z opcją adresu URL jest ograniczony do 30 GB.
- Długość adresu URL żądania jest ograniczona do 6144 znaków, gdzie długość adresu URL ciągu zapytania jest ograniczona do 4096 znaków .
- Rozmiar przekazywania z opcją tablicy bajtów jest ograniczony do 2 GB.
- Po upływie 30 min opcja tablicy bajtów.
- Adres URL podany `videoURL` w param musi być zakodowany.
- Indeksowanie zasobów usługi Media Services ma takie samo ograniczenie jak indeksowanie z adresu URL.
- Indeksator wideo ma maksymalny limit czasu trwania 4 godzin dla pojedynczego pliku.
- Adres URL musi być dostępny (na przykład publiczny adres URL). 

    Jeśli jest to prywatny adres URL, token dostępu musi być podany w żądaniu.
- Adres URL musi wskazywać prawidłowy plik multimedialny, a nie na `www.youtube.com` stronę sieci Web, na przykład łącze do strony.
- Na płatnym koncie możesz przesłać do 50 filmów na minutę, a na koncie próbnym do 5 filmów na minutę.

> [!Tip]
> Zalecane jest korzystanie z platformy .NET w wersji 4.6.2. lub nowszej, ponieważ starsze wersje platformy .NET nie obsługują domyślnie protokołu TLS 1.2.
>
> Jeśli musisz użyć starszej platformy .NET, dodaj jeden wiersz do swojego kodu przed wprowadzeniem wywołania interfejsu API REST:  <br/> System.Net.ServicePointManager.SecurityProtocol = SecurityProtocolType.Tls | SecurityProtocolType.Tls11 | SecurityProtocolType.Tls12;

## <a name="supported-file-formats-for-video-indexer"></a>Obsługiwane formaty plików dla indeksatora wideo

Zobacz artykuł [o formatach kontenera/pliku wejściowego,](../latest/media-encoder-standard-formats.md#input-containerfile-formats) aby uzyskać listę formatów plików, których można używać za pomocą indeksatora wideo.

## <a name="upload-and-index-a-video-using-the-video-indexer-website"></a><a id="website"/>Przesyłanie i indeksy wideo za pomocą witryny video Indexer

> [!NOTE]
> Nazwa filmu nie może być większa niż 80 znaków.

1. Zaloguj się w witrynie internetowej usługi [Video Indexer](https://www.videoindexer.ai/).
2. Aby przekazać plik wideo, naciśnij przycisk lub link **Upload** (Przekaż).

    ![Upload](./media/video-indexer-get-started/video-indexer-upload.png)

    Po przekazaniu pliku wideo usługa Video Indexer rozpocznie jego indeksowanie i analizowanie.

    ![Przekazano](./media/video-indexer-get-started/video-indexer-uploaded.png) 

    Po zakończeniu analizowania przez usługę Video Indexer otrzymasz powiadomienie z linkiem do tego pliku wideo i krótki opis tego, co znaleziono w tym nagraniu. Na przykład: osoby, tematy i wyniki przetwarzania OCR.

## <a name="upload-and-index-with-api"></a><a id="apis"/>Przesyłanie i indeksy za pomocą interfejsu API

Użyj interfejsu API [przesyłania wideo,](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) aby przesyłać i indeksować filmy na podstawie adresu URL. Przykładowy kod, który następuje zawiera skomentowany kod, który pokazuje, jak przekazać tablicę bajtów. 

### <a name="configurations-and-params"></a>Konfiguracje i parametry

W tej sekcji opisano niektóre parametry opcjonalne i wyjaśniono, kiedy należy je ustawić.

#### <a name="externalid"></a>externalID 

Ten parametr umożliwia określenie identyfikatora, który zostanie skojarzony z plikiem wideo. Ten identyfikator można zastosować do integracji z zewnętrznym systemem zarządzania zawartością wideo (VCM, Video Content Management). Pliki wideo znajdujące się w portalu usługi Video Indexer można wyszukiwać za pomocą tego określonego identyfikatora zewnętrznego.

#### <a name="callbackurl"></a>callbackUrl

Adres URL używany do powiadamiania klienta (za pomocą żądania POST) o następujących zdarzeniach:

- Zmiana stanu indeksowania: 
    - Właściwości:    
    
        |Nazwa|Opis|
        |---|---|
        |id|Identyfikator wideo|
        |state|Stan wideo|  
    - Przykład: https:\//test.com/notifyme?projectName=MyProject&id=1234abcd&state=Przetworzone
- Osoba rozpoznana na filmie wideo:
  - Właściwości
    
      |Nazwa|Opis|
      |---|---|
      |id| Identyfikator wideo|
      |faceId|Identyfikator Face ID w indeksie wideo|
      |knownPersonId|Identyfikator osoby, unikatowy w ramach danego modelu twarzy|
      |personName|Imię i nazwisko osoby|
        
    - Przykład: https:\//test.com/notifyme?projectName=MyProject&id=1234abcd&faceid=12&knownPersonId=CCA84350-89B7-4262-861C-3CAC796542A5&personName=Inigo_Montoya 

##### <a name="notes"></a>Uwagi

- Usługa Video Indexer zwraca wszelkie istniejące parametry podane w oryginalnym adresie URL.
- Podany adres URL musi być zakodowany.

#### <a name="indexingpreset"></a>indexingPreset

Tego parametru należy użyć, jeśli nagrania nieprzetworzone lub zewnętrzne zawierają hałas w tle. Parametr ten służy do konfigurowania procesu indeksowania. Można określić następujące wartości:

- `AudioOnly` — indeksowanie i wyodrębnianie szczegółowych informacji przy użyciu tylko części audio (z ignorowaniem części wideo)
- `VideoOnly`- Indeks i wyodrębnić spostrzeżenia przy użyciu tylko wideo (ignorowanie dźwięku)
- `Default` — indeksowanie i wyodrębnianie szczegółowych informacji przy użyciu części zarówno audio, jak i wideo
- `DefaultWithNoiseReduction` — indeksowanie i wyodrębnianie szczegółowych informacji przy użyciu zarówno audio, jak i wideo przy zastosowaniu algorytmów redukcji szumów w strumieniu audio

> [!NOTE]
> Indeksator wideo obejmuje do dwóch ścieżek audio. Jeśli w pliku znajduje się więcej ścieżek audio, będą one traktowane jako jedna ścieżka.<br/>
Jeśli chcesz indeksować ścieżki oddzielnie, musisz wyodrębnić odpowiedni plik audio `AudioOnly`i zindeksować go jako .

Cena zależy od wybranej opcji indeksowania.  

#### <a name="priority"></a>priority

Usługa Video Indexer indeksuje filmy wideo zgodnie z ich priorytetem. Użyj parametru **priority**, aby określić priorytet indeksu. Prawidłowe są następujące wartości: **Low** (niski), **Normal** (normalny — wartość domyślna), **High** (wysoki).

Parametr **priority** jest obsługiwany tylko w przypadku płatnych kont.

#### <a name="streamingpreset"></a>streamingPreset

Po przekazaniu pliku wideo usługa Video Indexer opcjonalnie koduje ten plik. Następnie przechodzi do indeksowania i analizowania pliku wideo. Po zakończeniu analizowania przez usługę Video Indexer otrzymasz powiadomienie z identyfikatorem pliku wideo.  

W przypadku korzystania z interfejsu API [przekazywania pliku wideo](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) lub [ponownego indeksowania pliku wideo](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?) jednym z parametrów opcjonalnych jest `streamingPreset`. W razie ustawienia dla parametru `streamingPreset` wartości `Default`, `SingleBitrate` lub `AdaptiveBitrate` wywoływany jest proces kodowania. Po zakończeniu zadań indeksowania i kodowania plik wideo jest publikowany, aby można go było również przesyłać strumieniowo. Punkt końcowy przesyłania strumieniowego, z którego chcesz strumieniowo przesyłać plik wideo, musi mieć stan **Uruchomiony**.

Aby można było uruchomić zadania indeksowania i kodowania, dla [konta usługi Azure Media Services połączonego z kontem usługi Video Indexer](connect-to-azure.md) wymagane są jednostki zarezerwowane. Aby uzyskać więcej informacji, zobacz [Scaling Media Processing](https://docs.microsoft.com/azure/media-services/previous/media-services-scale-media-processing-overview) (Skalowanie przetwarzania multimediów). Ponieważ te zadania wymagają intensywnego przetwarzania, zdecydowanie zaleca się typ jednostki S3. Liczba jednostek zarezerwowanych określa maksymalną liczbę zadań, które mogą działać równolegle. Zalecenie dotyczące planu bazowego to 10 jednostek zarezerwowanych S3. 

Jeśli chcesz tylko zaindeksować plik wideo bez kodowania go, ustaw dla parametru `streamingPreset` wartość `NoStreaming`.

#### <a name="videourl"></a>videoUrl

Adres URL pliku wideo/audio do zaindeksowania. Ten adres URL musi wskazywać plik multimedialny (strony HTML nie są obsługiwane). Plik może być chroniony przez token dostępu podany w ramach identyfikatora URI, a punkt końcowy obsługujący plik musi być zabezpieczony za pomocą protokołu TLS 1.2 lub nowszej wersji. Adres URL musi być zakodowany. 

Jeśli parametr `videoUrl` nie zostanie określony, usługa Video Indexer oczekuje przekazania pliku jako zawartości treści wieloczęściowej/formularza.

### <a name="code-sample"></a>Przykład kodu

W poniższym fragmencie kodu języka C# pokazano używanie wszystkich interfejsów API usługi Video Indexer razem.

#### <a name="instructions-for-running-this-code-sample"></a>Instrukcje dotyczące uruchamiania tego przykładu kodu

Po skopiowaniu tego kodu na platformę dewelopera należy podać dwa parametry: klucz uwierzytelniania api Management i adres URL wideo.

* Klucz interfejsu API — klucz interfejsu API jest kluczem subskrypcji zarządzania osobistym interfejsem API, który pozwoli ci uzyskać token dostępu w celu wykonywania operacji na koncie indeksatora wideo. 

    Aby uzyskać klucz interfejsu API, przejdź przez ten przepływ:

    * Przejdź dohttps://api-portal.videoindexer.ai/
    * Logowanie
    * Przejdź **Products** -> do subskrypcji**autoryzacji autoryzacji** -> **Authorization subscription** produktów
    * Kopiowanie **klucza podstawowego**
* Adres URL filmu — adres URL pliku wideo/audio, który ma być indeksowany. Ten adres URL musi wskazywać plik multimedialny (strony HTML nie są obsługiwane). Plik może być chroniony przez token dostępu podany w ramach identyfikatora URI, a punkt końcowy obsługujący plik musi być zabezpieczony za pomocą protokołu TLS 1.2 lub nowszej wersji. Adres URL musi być zakodowany.

Wynik pomyślnego uruchomienia przykładu kodu będzie zawierał adres URL widżetu wglądu i adres URL widżetu gracza, który pozwoli Ci sprawdzić odpowiednio dane i przesłane filmy. 


```csharp
public async Task Sample()
{
    var apiUrl = "https://api.videoindexer.ai";
    var apiKey = "..."; // replace with API key taken from https://aka.ms/viapi

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
    
    // take the relevant account, here we simply take the first, 
    // you can also get the account via accounts.First(account => account.Id == <GUID>);
    var accountInfo = accounts.First();

    // we will use the access token from here on, no need for the apim key
    client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

    // upload a video
    var content = new MultipartFormDataContent();
    Console.WriteLine("Uploading...");
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
    Console.WriteLine("Uploaded");
    Console.WriteLine("Video ID:");
    Console.WriteLine(videoId);

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

        Console.WriteLine("");
        Console.WriteLine("State:");
        Console.WriteLine(processingState);

        // job is finished
        if (processingState != "Uploaded" && processingState != "Processing")
        {
            Console.WriteLine("");
            Console.WriteLine("Full JSON:");
            Console.WriteLine(videoGetIndexResult);
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
    Console.WriteLine("");
    Console.WriteLine("Search:");
    Console.WriteLine(searchResult);

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
    Console.WriteLine("Insights Widget url:");
    Console.WriteLine(insightsWidgetLink);

    // get player widget url
    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", videoAccessToken},
        });
    var playerWidgetRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/PlayerWidget?{queryParams}");
    var playerWidgetLink = playerWidgetRequestResult.Headers.Location;
     Console.WriteLine("");
     Console.WriteLine("Player Widget url:");
     Console.WriteLine(playerWidgetLink);
     Console.WriteLine("\nPress Enter to exit...");
     String line = Console.ReadLine();
     if (line == "enter")
     {
         System.Environment.Exit(0);
     }

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

### <a name="common-errors"></a>Typowe błędy

Kody stanu wymienione w poniższej tabeli mogą być zwracane przez operację przekazywania.

|Kod stanu|ErrorType (w treści odpowiedzi)|Opis|
|---|---|---|
|409|VIDEO_INDEXING_IN_PROGRESS|Ten sam plik wideo jest już w trakcie przetwarzania w ramach danego konta.|
|400|VIDEO_ALREADY_FAILED|Tego samego pliku wideo nie udało się przetworzyć w ramach danego konta mniej niż 2 godziny temu. Klienci interfejsu API powinni poczekać co najmniej 2 godziny przed ponownym przekazaniem pliku wideo.|
|429||Konta próbne są dozwolone 5 przesłanych na minutę. Płatne konta są dozwolone 50 przesłanych na minutę.|

## <a name="next-steps"></a>Następne kroki

[Sprawdzanie danych wyjściowych indeksatora wideo platformy Azure opracowanych przez interfejs API](video-indexer-output-json-v2.md)
