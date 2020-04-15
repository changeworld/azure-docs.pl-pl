---
title: Korzystanie z interfejsu API usługi Video Indexer
titleSuffix: Azure Media Services
description: W tym artykule opisano, jak rozpocząć pracę z interfejsem API indeksatora wideo usługi Media Services.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 04/13/2020
ms.author: juliako
ms.openlocfilehash: 82bdb177cf4d9c400d1b13ba7178658089950557
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81314330"
---
# <a name="tutorial-use-the-video-indexer-api"></a>Samouczek: używanie interfejsu API usługi Video Indexer

Video Indexer konsoliduje różne technologie sztucznej inteligencji audio i wideo (AI) oferowane przez firmę Microsoft w jedną zintegrowaną usługę, ułatwiając rozwój. Interfejsy API zostały zaprojektowane tak, aby umożliwić deweloperom skupienie się na korzystaniu z technologii AI multimediów bez obawy o skalę, globalny zasięg, dostępność i niezawodność platform w chmurze. Za pomocą interfejsu API możesz przesyłać pliki, ukazywać szczegółowe informacje wideo, uzyskać adresy URL osadzonych informacji i widżetów graczy i nie tylko.

Podczas tworzenia konta indeksatora wideo możesz wybrać bezpłatne konto próbne (gdzie otrzymujesz określoną liczbę bezpłatnych minut indeksowania) lub opcję płatną (w której nie jesteś ograniczony przez przydział). Dzięki bezpłatnej wersji próbnej indeksator wideo zapewnia użytkownikom witryny do 600 minut bezpłatnego indeksowania i do 2400 minut bezpłatnego indeksowania dla użytkowników interfejsu API. Za pomocą opcji płatnej można utworzyć konto indeksatora wideo [połączone z subskrypcją platformy Azure i kontem usługi Azure Media Services.](connect-to-azure.md) Naliczane są opłaty za minuty indeksowania, a także opłaty powiązane z kontem usługi Azure Media Services.

W tym artykule pokazano, jak deweloperzy mogą korzystać z [interfejsu API usługi Video Indexer](https://api-portal.videoindexer.ai/).

## <a name="subscribe-to-the-api"></a>Subskrybowanie interfejsu API

1. Zaloguj się w [portalu dla deweloperów usługi Video Indexer](https://api-portal.videoindexer.ai/).
    
    ![Zaloguj się do portalu dewelopera indeksatora wideo](./media/video-indexer-use-apis/video-indexer-api01.png)

   > [!Important]
   > * Musisz użyć tego samego dostawcy, który został użyty podczas tworzenia konta w usłudze Video Indexer.
   > * Osobiste konta Google i Microsoft (Outlook/Live) mogą być używane tylko dla kont próbne. Konta połączone z platformą Azure wymagają usługi Azure AD.
   > * Na wiadomość e-mail może istnieć tylko jedno aktywne konto. Jeśli użytkownik spróbuje zalogować się za user@gmail.com user@gmail.com pomocą LinkedIn, a później za pomocą Google, ten ostatni wyświetli stronę błędu, mówiąc, że użytkownik już istnieje.

2. Subskrybuj.

    Wybierz kartę [Produkty.](https://api-portal.videoindexer.ai/products) Następnie wybierz pozycję Autoryzacja i subskrybuj.
    
    ![Karta Produkty w portalu dla deweloperów indeksatora wideo](./media/video-indexer-use-apis/video-indexer-api02.png)

    > [!NOTE]
    > Nowi użytkownicy automatycznie subskrybują autoryzację.
    
    Po zasubskrybowaniu możesz zobaczyć subskrypcję oraz klucze podstawowe i pomocnicze. Te klucze należy chronić. Powinno się ich używać tylko w kodzie serwera. Nie powinny być dostępne po stronie klienta (.js, .html i tak dalej).

    ![Subskrypcja i klucze w portalu deweloperskim indeksatora wideo](./media/video-indexer-use-apis/video-indexer-api03.png)

> [!TIP]
> Usługa Video Indexer może używać klucza pojedynczej subskrypcji do łączenia się z wieloma kontami usługi Video Indexer. Te konta usługi Video Indexer można następnie połączyć z różnymi kontami usługi Media Services.

## <a name="obtain-access-token-using-the-authorization-api"></a>Uzyskiwanie tokenu dostępu przy użyciu interfejsu API autoryzacji

Po zasubskrybowaniu interfejsu API autoryzacji można uzyskać tokeny dostępu. Te tokeny dostępu są używane do uwierzytelniania w interfejsie API operacji.

Każde wywołanie interfejsu API operacji powinno być skojarzone z tokenem dostępu zgodnym z zakresem autoryzacji wywołania.

- Poziom użytkownika: tokeny dostępu na poziomie użytkownika umożliwiają wykonywanie operacji na poziomie **użytkownika.** Na przykład uzyskiwanie informacji o skojarzonych kontach.
- Poziom konta: tokeny dostępu na poziomie konta umożliwiają wykonywanie operacji na poziomie **konta** lub na poziomie **wideo.** Na przykład prześlij film, wystaw wszystkie filmy, uzyskaj wgląd w film i tak dalej.
- Poziom wideo: tokeny dostępu na poziomie wideo umożliwiają wykonywanie operacji na określonym **filmie.** Na przykład możesz uzyskać wgląd w wideo, pobrać podpisy, pobrać widżety i tak dalej.

Można kontrolować, czy te tokeny są tylko do odczytu lub jeśli pozwalają na edycję, określając **allowEdit=true/false**.

W przypadku większości scenariuszy między serwerami prawdopodobnie użyjesz tego samego **tokenu konta,** ponieważ obejmuje on zarówno operacje **na koncie,** jak i operacje **wideo.** Jeśli jednak planujesz nawiązywać połączenia po stronie klienta do indeksatora wideo (na przykład z języka JavaScript), chcesz użyć tokenu dostępu do **wideo,** aby uniemożliwić klientom uzyskanie dostępu do całego konta. Jest to również powód, dla którego podczas osadzania kodu klienta indeksatora wideo w kliencie (na przykład przy użyciu **widżetu Pobierz statystyki** lub **widżetu Pobierz odtwarzacz)** należy podać token dostępu do **wideo.**

Aby ułatwić sprawę, można uzyskać informacje o kontach za pomocą pozycji **Authorization** API (Interfejs API autoryzacji) > **GetAccounts** (Pobierz konta) bez wcześniejszego pobierania tokenu użytkownika. Można również zażądać informacji o kontach z ważnymi tokenami, co umożliwia pominięcie dodatkowego wywołania w celu pobrania tokenu konta.

Tokeny dostępu wygasają po godzinie. Przed skorzystaniem z interfejsu API operacji upewnij się, że token dostępu jest ważny. Jeśli wygaśnie, ponownie zadzwoń do interfejsu API autoryzacji, aby uzyskać nowy token dostępu.

Możesz rozpocząć integrację z interfejsem API. Zapoznaj się ze [szczegółowymi opisami poszczególnych interfejsów API REST usługi Video Indexer](https://api-portal.videoindexer.ai/).

## <a name="account-id"></a>Identyfikator konta

Identyfikator konta to parametr wymagany we wszystkich wywołaniach interfejsu API operacji. Identyfikator konta jest identyfikatorem GUID, który można uzyskać w jeden z następujących sposobów:

* Uzyskiwanie identyfikatora konta za pomocą **witryny internetowej usługi Video Indexer**:

    1. Przejdź do witryny internetowej [Video Indexer](https://www.videoindexer.ai/) i zaloguj się.
    2. Przejdź do strony **Settings** (Ustawienia).
    3. Skopiuj identyfikator konta.

        ![Ustawienia indeksatora wideo i identyfikator konta](./media/video-indexer-use-apis/account-id.png)

* Uzyskiwanie identyfikatora konta za pomocą **portalu dla deweloperów usługi Video Indexer**.

    Użyj interfejsu [API pobierz konto.](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Account?)

    > [!TIP]
    > Tokeny dostępu dla kont możesz wygenerować, definiując atrybut`generateAccessTokens=true`.

* Pobierz identyfikator konta z adresu URL strony odtwarzacza na swoim koncie.

    Podczas odtwarzania pliku wideo identyfikator jest podany po sekcji `accounts` i przed sekcją `videos`.

    ```
    https://www.videoindexer.ai/accounts/00000000-f324-4385-b142-f77dacb0a368/videos/d45bf160b5/
    ```

## <a name="recommendations"></a>Zalecenia

W tej sekcji przedstawiono kilka zaleceń dotyczących korzystania z interfejsu API usługi Video Indexer.

- Jeśli planujesz przekazać film, zaleca się umieszczenie pliku w jakiejś publicznej lokalizacji sieciowej (na przykład w usłudze OneDrive). Uzyskaj link do pliku wideo, a następnie podaj ten adres URL jako parametr przekazywania pliku.

    Adres URL przekazywany do usługi Video Indexer musi wskazywać plik multimedialny (audio lub wideo). Niektóre linki generowane przez usługę OneDrive prowadzą do zawierających te pliki stron HTML. Łatwa weryfikacja adresu URL polega na wklejeniu go do przeglądarki — jeśli plik rozpocznie pobieranie, prawdopodobnie będzie to dobry adres URL. Jeśli przeglądarka renderuje wizualizację, prawdopodobnie nie jest to łącze do pliku, ale do strony HTML.

- Wywołanie dla konkretnego pliku wideo interfejsu API, który pobiera szczegółowe informacje o wideo, powoduje pobranie w zawartości odpowiedzi szczegółowych danych wyjściowych JSON. [W tym temacie omówiono szczegółowo zwracane informacje JSON](video-indexer-output-json-v2.md).

## <a name="code-sample"></a>Przykład kodu

W poniższym fragmencie kodu języka C# pokazano używanie wszystkich interfejsów API usługi Video Indexer razem.

```csharp
var apiUrl = "https://api.videoindexer.ai";
var accountId = "..."; 
var location = "westus2";
var apiKey = "..."; 

System.Net.ServicePointManager.SecurityProtocol = System.Net.ServicePointManager.SecurityProtocol | System.Net.SecurityProtocolType.Tls12;

// create the http client
var handler = new HttpClientHandler(); 
handler.AllowAutoRedirect = false; 
var client = new HttpClient(handler);
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey); 

// obtain account access token
var accountAccessTokenRequestResult = client.GetAsync($"{apiUrl}/auth/{location}/Accounts/{accountId}/AccessToken?allowEdit=true").Result;
var accountAccessToken = accountAccessTokenRequestResult.Content.ReadAsStringAsync().Result.Replace("\"", "");

client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

// upload a video
var content = new MultipartFormDataContent();
Debug.WriteLine("Uploading...");
// get the video from URL
var videoUrl = "VIDEO_URL"; // replace with the video URL

// as an alternative to specifying video URL, you can upload a file.
// remove the videoUrl parameter from the query string below and add the following lines:
  //FileStream video =File.OpenRead(Globals.VIDEOFILE_PATH);
  //byte[] buffer = new byte[video.Length];
  //video.Read(buffer, 0, buffer.Length);
  //content.Add(new ByteArrayContent(buffer));

var uploadRequestResult = client.PostAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos?accessToken={accountAccessToken}&name=some_name&description=some_description&privacy=private&partition=some_partition&videoUrl={videoUrl}", content).Result;
var uploadResult = uploadRequestResult.Content.ReadAsStringAsync().Result;

// get the video id from the upload result
var videoId = JsonConvert.DeserializeObject<dynamic>(uploadResult)["id"];
Debug.WriteLine("Uploaded");
Debug.WriteLine("Video ID: " + videoId);           

// obtain video access token
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);
var videoTokenRequestResult = client.GetAsync($"{apiUrl}/auth/{location}/Accounts/{accountId}/Videos/{videoId}/AccessToken?allowEdit=true").Result;
var videoAccessToken = videoTokenRequestResult.Content.ReadAsStringAsync().Result.Replace("\"", "");

client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

// wait for the video index to finish
while (true)
{
  Thread.Sleep(10000);

  var videoGetIndexRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/{videoId}/Index?accessToken={videoAccessToken}&language=English").Result;
  var videoGetIndexResult = videoGetIndexRequestResult.Content.ReadAsStringAsync().Result;

  var processingState = JsonConvert.DeserializeObject<dynamic>(videoGetIndexResult)["state"];

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
var searchRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/Search?accessToken={accountAccessToken}&id={videoId}").Result;
var searchResult = searchRequestResult.Content.ReadAsStringAsync().Result;
Debug.WriteLine("");
Debug.WriteLine("Search:");
Debug.WriteLine(searchResult);

// get insights widget url
var insightsWidgetRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/{videoId}/InsightsWidget?accessToken={videoAccessToken}&widgetType=Keywords&allowEdit=true").Result;
var insightsWidgetLink = insightsWidgetRequestResult.Headers.Location;
Debug.WriteLine("Insights Widget url:");
Debug.WriteLine(insightsWidgetLink);

// get player widget url
var playerWidgetRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/{videoId}/PlayerWidget?accessToken={videoAccessToken}").Result;
var playerWidgetLink = playerWidgetRequestResult.Headers.Location;
Debug.WriteLine("");
Debug.WriteLine("Player Widget url:");
Debug.WriteLine(playerWidgetLink);

```

## <a name="see-also"></a>Zobacz też

- [Omówienie usługi Video Indexer](video-indexer-overview.md)
- [Regiony](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)

## <a name="next-steps"></a>Następne kroki

- [Sprawdź szczegóły wyjścia JSON](video-indexer-output-json-v2.md)
- Zapoznaj się z [przykładowym kodem,](https://github.com/Azure-Samples/media-services-video-indexer) który pokazuje ważny aspekt przekazywania i indeksowania filmu. Po wil kodu daje dobry pomysł, jak korzystać z naszego interfejsu API dla podstawowych funkcji. Zapoznaj się z komentarzami i zapoznaj się z naszymi poradami w zakresie najlepszych praktyk.

