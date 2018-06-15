---
title: Użyj Azure indeksatora wideo interfejsu API | Dokumentacja firmy Microsoft
description: W tym artykule pokazano, jak rozpocząć korzystanie z interfejsu API indeksatora wideo.
services: cognitive services
documentationcenter: ''
author: juliako
manager: erikre
ms.service: cognitive-services
ms.topic: article
ms.date: 06/04/2018
ms.author: juliako
ms.openlocfilehash: d378934a0c085910475c366f4bdb538f09efc12b
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35356476"
---
# <a name="use-azure-video-indexer-api"></a>Użyj Azure indeksatora wideo interfejsu API

Indeksator wideo konsoliduje różnych technologii audio i wideo sztucznego analizy (AI) oferowanych przez firmę Microsoft w jednej zintegrowana usługa wprowadzania programowanie prostsze. Interfejsy API są przeznaczone do umożliwienia osiągnąć deweloperom skoncentrować się na korzystanie z technologii AI nośnika bez obaw o skali globalnej, dostępność i niezawodność platformy w chmurze. Aby przekazać pliki, uzyskiwanie szczegółowych wideo szczegółowych informacji, uzyskiwanie adresów URL widżetów szczegółowe dane i player w celu osadzania ich w aplikacji i innych zadań, można użyć interfejsu API.

> [!Note]
> Bezpłatna wersja próbna ma dzienny limit przekazywania 100 plików. Komunikat o błędzie zwrócony, aby uzyskać szczegółowe informacje, można sprawdzić. Należy pamiętać, że dzienny limit mogą ulec zmianie.

W tym artykule opisano, jak Programiści mogą wykorzystać [wideo indeksatora API](https://api-portal.videoindexer.ai/). Aby uzyskać bardziej szczegółowe omówienie usługi indeksatora wideo, zobacz [omówienie](video-indexer-overview.md) artykułu.

## <a name="subscribe-to-the-api"></a>Subskrypcja do interfejsu API

1. Rejestrowanie.

    Aby uruchomić programowania z użyciem indeksatora wideo, musi pierwszego logowania do [indeksatora wideo](https://api-portal.videoindexer.ai/) portalu. 
    
    ![Rejestrowanie](./media/video-indexer-use-apis/video-indexer-api01.png)

    > [!Important]
    > 1. Należy użyć tego samego dostawcy użytą podczas rejestracji dla indeksatora wideo.
    > 2. Zanim można zarejestrować użytkowników usługi Azure AD z domeny, administrator domeny usługi AAD należy włączyć rejestrację tej domeny [tutaj](https://api-portal.videoindexer.ai/aadadminconsent).
    > 3. Osobiste Google i Microsoft (outlook/live) konta mogą służyć tylko dla konta wersji próbnej. Konta połączenia na platformie Azure wymagają usługi AAD.

2. Subskrypcja.

    Wybierz [produktów](https://api-portal.videoindexer.ai/products) kartę. Następnie wybierz autoryzacji i subskrypcji. 
    
    ![Rejestrowanie](./media/video-indexer-use-apis/video-indexer-api02.png)
    
    Po dokonaniu subskrypcji można zobaczyć subskrypcji i Twoje klucze podstawowe i pomocnicze. Klucze powinny być chronione. Klucze powinna być używana tylko przez kod serwera. Nie powinien być możliwy po stronie klienta (js, HTML itp.).

    ![Rejestrowanie](./media/video-indexer-use-apis/video-indexer-api03.png)

## <a name="obtain-access-token-using-the-authorization-api"></a>Uzyskaj token dostępu przy użyciu interfejsu API autoryzacji

Gdy subskrybujesz API autoryzacji można uzyskać tokeny dostępu. Te tokeny dostępu są używane do uwierzytelniania interfejsu API operacji. 

Każde wywołanie interfejsu API operacji powinna być skojarzona z tokenem dostępu dopasowania zakresu autoryzacji wywołania.

- Na poziomie użytkownika - tokeny dostępu na poziomie użytkownika umożliwiają wykonywanie operacji na **użytkownika** poziom. Na przykład get skojarzonych kont.
- Poziom konta — tokenów dostępu na poziomie konta pozwalają na wykonywanie operacji na **konta** poziom lub **wideo** poziom. Na przykład Przekaż wideo, wyświetlić listę wszystkich plików wideo, get insights wideo, np.
- Wideo poziom — tokenów dostępu na poziomie wideo umożliwiają wykonywanie operacji na określonym **wideo**. Na przykład uzyskiwanie szczegółowych informacji wideo, Pobierz podpisów, Pobierz elementy widget, itd. 

Można kontrolować te tokeny są tylko do odczytu lub ich Zezwól na edytowanie przez określenie **allowEdit = PRAWDA/FAŁSZ**.

W większości przypadków serwer serwer, prawdopodobnie skorzystasz takie same **konta** tokenu, ponieważ obejmuje ona zarówno **konta** operacji i **wideo** operacji. Jednak jeśli jest planowane po stronie klienta wywołania indeksatora wideo (np. z javascript), czy chcesz użyć **wideo** tokenu dostępu, aby uniemożliwić klientom dostęp do całego konta. Jest to przyczyna że w przypadku osadzanie kodu klienta VideoIndexer w kliencie (na przykład za pomocą **Widget uzyskać szczegółowe informacje** lub **uzyskać elementu Widget Player**) należy podać **wideo**tokenu dostępu.

Aby ułatwić, można użyć **autoryzacji** interfejsu API > **GetAccounts** można pobrać kont bez uzyskania użytkownik tokenu najpierw. Możesz również poprosić można uzyskać konta z prawidłowych tokenów, dzięki któremu można pominąć dodatkowe wywołanie dotyczące pobrania tokenu konta.

Tokeny dostępu wygaśnięcia po godzinie. Upewnij się, że token dostępu jest prawidłowy, przed rozpoczęciem korzystania z interfejsu API operacji. Jeśli wygaśnie, wywołania interfejsu API autoryzacji ponownie, aby uzyskać nowy token dostępu.
 
Wszystko jest gotowe rozpocząć integrowanie z interfejsu API. Znajdź [szczegółowy opis każdego interfejsu API REST indeksatora wideo](http://api-portal.videoindexer.ai/).

## <a name="location"></a>Lokalizacja

Operacja wszystkie interfejsy API wymagać parametr lokalizacji i wskazujący regionu, do którego powinny być kierowane wywołanie i w której utworzono konto.

Zastosowanie wartości opisane w poniższej tabeli. **Wartość Param** wartość, gdy przekazujesz używa interfejsu API.

|**Nazwa**|**Wartości parametrów**|**Opis**|
|---|---|---|
|Wersja próbna|wersja próbna|Używana dla konta wersji próbnej.|
|Zachodnie stany USA|westus2|Używane w regionie Azure zachodnie nam 2.|
|Europa Północna |northeurope|Używane dla regionu Azure, Europa Północna.|
|Azja Wschodnia|eastasia|Używane dla regionu Azja Wschodnia Azure.|

## <a name="account-id"></a>Identyfikator konta 

Parametr Identyfikatora konta jest wymagany w operacyjnej wywołania interfejsu API. Identyfikator konta jest identyfikatorem GUID, który można uzyskać w jednym z następujących sposobów:

* Użyj portalu indeksatora wideo, aby uzyskać identyfikator konta:

    1. Zaloguj się do [videoindexer](https://www.videoindexer.ai/).
    2. Przejdź do **ustawienia** strony.
    3. Skopiuj identyfikator konta.

        ![Identyfikator konta](./media/video-indexer-use-apis/account-id.png)

* Za pomocą interfejsu API można programowo pobrać nazwę konta.

    Użyj [uzyskać kont](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Accounts?) interfejsu API.
    
    > [!TIP]
    > Można generować tokeny dostępu dla konta, definiując `generateAccessTokens=true`.
    
* Pobierz identyfikator konta z adresu URL strony player w ramach Twojego konta.

    Podczas odtwarzania wideo identyfikator pojawia się po `accounts` sekcji i przed `videos` sekcji.

    ```
    https://www.videoindexer.ai/accounts/00000000-f324-4385-b142-f77dacb0a368/videos/d45bf160b5/
    ```

## <a name="recommendations"></a>Zalecenia

Ta sekcja zawiera kilka zaleceń, korzystając z interfejsu API indeksatora wideo.

- Jeśli planujesz przekazywanie pliku wideo, zalecane jest umieścić plik w niektórych lokalizacją w sieci publicznej (na przykład OneDrive). Uzyskiwanie linku do wideo i podaj adres URL jako param pliku przekazywania. 

    Adres URL dostarczony do indeksatora wideo musi wskazywać plik nośnika (audio i wideo). Niektóre linki generowanych przez usługi OneDrive są dla strony HTML, który zawiera plik. Łatwe weryfikacji dla adresu URL byłoby wklej go w przeglądarce — Jeśli plik zostanie uruchomiony, pobieranie, prawdopodobnie jest dobrym adres URL. Jeśli przeglądarka renderowania niektórych wizualizacji, prawdopodobnie nie jest łącze do pliku, ale strona HTML.
    
- Po wywołaniu interfejsu API, który pobiera insights wideo dla filmu określonego otrzymasz szczegółowe dane wyjściowe JSON jako treść odpowiedzi. [Szczegółowe informacje o zwrócony JSON w tym temacie](video-indexer-output-json.md).

## <a name="code-sample"></a>Przykład kodu

Poniższy fragment kodu C# przedstawiono użycie wszystkich interfejsów API indeksatora wideo razem.

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
  //byte[] buffer =newbyte[video.Length];
  //video.Read(buffer, 0, buffer.Length);
  //content.Add(newByteArrayContent(buffer));

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

## <a name="next-steps"></a>Kolejne kroki

[Przejrzyj szczegóły danych wyjściowych JSON](video-indexer-output-json.md).

## <a name="see-also"></a>Zobacz także

[Film poglądowy dotyczący indeksatora](video-indexer-overview.md)
