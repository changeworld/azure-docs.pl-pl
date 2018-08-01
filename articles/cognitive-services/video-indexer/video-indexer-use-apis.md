---
title: Użyj interfejsu API indeksatora wideo platformy Azure | Dokumentacja firmy Microsoft
description: W tym artykule pokazano, jak rozpocząć pracę, przy użyciu interfejsu API indeksatora wideo.
services: cognitive services
documentationcenter: ''
author: juliako
manager: erikre
ms.service: cognitive-services
ms.topic: article
ms.date: 07/25/2018
ms.author: juliako
ms.openlocfilehash: 82416c7c653438fcd8b8f4a4ead7591bad0ac022
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2018
ms.locfileid: "39391519"
---
# <a name="use-azure-video-indexer-api"></a>Użyj interfejsu API indeksatora wideo platformy Azure

Usługa Video Indexer konsoliduje różne technologie audio i wideo sztucznej inteligencji (AI), oferowane przez firmę Microsoft w jednej usłudze zintegrowane, co upraszcza programowanie. Interfejsy API są przeznaczone do umożliwienia deweloperom skupić się na korzystanie z technologii AI multimediów bez konieczności martwienia się o skalowanie globalne dotrzeć do, dostępność i niezawodność platformy w chmurze. Interfejs API umożliwia przekazywanie plików, uzyskiwanie szczegółowych informacji szczegółowych wideo i uzyskiwanie adresów URL szczegółowe informacje, jak i odtwarzacz widżetów w celu osadzania ich w aplikacji i innych zadań.

Podczas tworzenia konta indeksatora wideo, możesz wybrać bezpłatne konto próbne (skąd określonej liczbie bezpłatnych minut indeksowania) lub opcję płatną (gdzie możesz nie są ograniczone według przydziału). Za pomocą bezpłatnej wersji próbnej Video Indexer zapewnia do 600 minut indeksować bezpłatne dla użytkowników witryny sieci Web, i maksymalnie 2 400 minut bezpłatne indeksowania dla użytkowników interfejsu API. W przypadku płatnych opcji tworzenia konta Video Indexer, który jest [podłączone do subskrypcji platformy Azure i konto usługi Azure Media Services](connect-to-azure.md). Płacisz indeksowane w ciągu kilku minut, a także konta multimediów powiązane opłaty. 

W tym artykule pokazano, jak deweloperzy mogą korzystać z [interfejsu API indeksatora wideo](https://api-portal.videoindexer.ai/). Aby zapoznać się z bardziej szczegółowym omówieniem usługi Video Indexer, zobacz [Przegląd](video-indexer-overview.md) artykułu.

## <a name="subscribe-to-the-api"></a>Subskrybowanie do interfejsu API

1. Rejestrowanie.

    Aby rozpocząć tworzenie aplikacji za pomocą indeksatora wideo, musisz pierwszego logowania do [Video Indexer](https://api-portal.videoindexer.ai/) portalu. 
    
    ![Rejestrowanie](./media/video-indexer-use-apis/video-indexer-api01.png)

    > [!Important]
    > * Należy użyć tego samego dostawcy, którego użyto podczas rejestracji w usłudze Video Indexer.
    > * Osobiste Google i Microsoft (outlook/live) konta należy używać tylko dla konta wersji próbnej. Konta połączone z platformy Azure wymaga usługi Azure AD.
    > * Może istnieć tylko jedno aktywne konto na wiadomości E-Mail. Jeśli użytkownik próbuje zalogować się za pomocą user@gmail.com za pomocą konta LinkedIn, a następnie za pomocą user@gmail.com do nowszego Google zostanie wyświetlona strona błędu, informujący o tym użytkownika już istnieje.


2. Subskrypcja.

    Wybierz [produktów](https://api-portal.videoindexer.ai/products) kartę. Następnie wybierz autoryzacji i subskrypcji. 
    
    ![Rejestrowanie](./media/video-indexer-use-apis/video-indexer-api02.png)

    > [!NOTE]
    > Nowi użytkownicy automatycznie mają subskrypcję autoryzacji.
    
    Po zasubskrybowaniu będzie można zobaczyć Twoja subskrypcja i Twoje klucze podstawowe i pomocnicze. Klucze powinny być chronione. Klucze można stosować tylko w kodzie serwera. Nie powinien być dostępny po stronie klienta (js, HTML itp.).

    ![Rejestrowanie](./media/video-indexer-use-apis/video-indexer-api03.png)

## <a name="obtain-access-token-using-the-authorization-api"></a>Uzyskiwanie tokenu dostępu przy użyciu interfejsu API autoryzacji

Gdy subskrybujesz interfejs API autoryzacji będzie umożliwiające uzyskanie tokenów dostępu. Te tokeny dostępu są używane do uwierzytelniania przy użyciu operacji interfejsu API. 

Każde wywołanie interfejsu API operacji powinna być skojarzona z tokenem dostępu dopasowywania zakresu autoryzacji wywołania.

- Na poziomie użytkownika — tokeny dostępu na poziomie użytkownika umożliwiają wykonywanie operacji na **użytkownika** poziom. Na przykład uzyskać skojarzonych kont.
- Poziom konta — tokenów dostępu na poziomie konta pozwalają na wykonywanie operacji na **konta** poziom lub **wideo** poziom. Na przykład Przekaż wideo, listę wszystkich filmów wideo, skorzystaj z informacji szczegółowych wideo, itp.
- Poziom wideo — tokeny dostępu na poziomie wideo umożliwiają wykonywanie operacji na określonym **wideo**. Na przykład analiz wideo, Pobierz podpisy, skorzystaj z elementów widget, itp. 

Można kontrolować, czy tokeny te są tylko do odczytu czy umożliwiają one, edytowanie, określając **allowEdit = PRAWDA/FAŁSZ**.

W przypadku większości scenariuszy serwer serwer, którego prawdopodobnie będziesz używasz takie same **konta** tokenu, ponieważ obejmuje ona zarówno **konta** operacji i **wideo** operacji. Jednakże jeśli planowane jest zapewnienie po stronie klienta wywołania do indeksatora wideo (na przykład z języka javascript), może okazać się użycie **wideo** tokenu dostępu, aby uniemożliwić klientom uzyskiwanie dostępu do całego konta. To jest również powód, że w przypadku osadzania kodu klienta VideoIndexer w swoim kliencie (na przykład za pomocą **widżet uzyskiwanie szczegółowych informacji** lub **uzyskać Widgetu odtwarzania**) należy podać **wideo**token dostępu.

Aby je ułatwić, można użyć **autoryzacji** interfejs API > **GetAccounts** można uzyskać kont bez uzyskania użytkownika tokenu najpierw. Można również zadawać można pobrać konta z prawidłową tokenów, dzięki któremu można pominąć dodatkowe wywołanie w celu uzyskania tokenu konta.

Tokeny dostępu wygasają po upływie 1 godzinę. Upewnij się, że Twój token dostępu jest prawidłowy, przed rozpoczęciem korzystania z interfejsu API operacji. Jeśli wygasną, Wywołaj interfejs API autoryzacji ponownie, aby uzyskać nowy token dostępu.
 
Jesteś gotowy do uruchomienia, integracja z interfejsem API. Znajdź [szczegółowy opis każdego interfejsu REST API indeksatora wideo](http://api-portal.videoindexer.ai/).

## <a name="location"></a>Lokalizacja

Operacja wszystkie interfejsy API wymagają parametru lokalizacja wskazuje region, do którego powinny być kierowane wywołania i w której zostało utworzone konto.

Stosowanie wartości opisanych w poniższej tabeli. **Param value** jest wartość, gdy przekazujesz przy użyciu interfejsu API.

|**Nazwa**|**Wartości parametrów**|**Opis**|
|---|---|---|
|Wersja próbna|wersja próbna|Używany dla konta wersji próbnej.|
|Zachodnie stany USA|westus2|Używane w regionie platformy Azure zachodnie stany USA 2.|
|Europa Północna |northeurope|Używane w regionie Europa Północna platformy Azure.|
|Azja Wschodnia|eastasia|Używane dla regionu Azja Wschodnia platformy Azure.|

## <a name="account-id"></a>Identyfikator konta 

Parametr Accountid jest wymagany we wszystkich wywołaniach interfejsu API operacyjnej. Identyfikator konta jest identyfikatorem GUID, który można uzyskać w jednym z następujących sposobów:

* Użyj portalu usługi Video Indexer, aby pobrać identyfikator konta:

    1. Zaloguj się do [videoindexer](https://www.videoindexer.ai/).
    2. Przejdź do **ustawienia** strony.
    3. Skopiuj identyfikator konta.

        ![Identyfikator konta](./media/video-indexer-use-apis/account-id.png)

* Za pomocą interfejsu API można programowo pobrać identyfikatora konta.

    Użyj [uzyskać kont](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Accounts?) interfejsu API.
    
    > [!TIP]
    > Możesz wygenerować tokeny dostępu dla konta, definiując `generateAccessTokens=true`.
    
* Pobierz identyfikator konta z adresu URL strony graczy na Twoim koncie.

    Podczas oglądania wideo identyfikator pojawia się po `accounts` sekcji i przed `videos` sekcji.

    ```
    https://www.videoindexer.ai/accounts/00000000-f324-4385-b142-f77dacb0a368/videos/d45bf160b5/
    ```

## <a name="recommendations"></a>Zalecenia

W tej sekcji przedstawiono kilka zaleceń, korzystając z interfejsu API indeksatora wideo.

- Jeśli planowane jest przekazywania plików wideo, zaleca się umieścić plik w lokalizacji sieci publicznej (na przykład OneDrive). Uzyskiwanie linku do filmu wideo, a następnie podaj adres URL jako parametr przekazywania pliku. 

    Podany adres URL do indeksatora wideo musi wskazywać plik multimedialny (audio lub wideo). Niektóre z łączy generowanych przez usługi OneDrive to dla strony HTML, który zawiera plik. Łatwe weryfikacji dla adresu URL będzie Aby wkleić ją w przeglądarce — Jeśli plik zostanie uruchomiony, pobieranie, prawdopodobnie dobrej adresu URL. Jeśli przeglądarka renderowania kilka wizualizacji, prawdopodobnie nie jest link do pliku, ale na stronie HTML.
    
- Po wywołaniu interfejsu API, który pobiera informacjom o filmie wideo dla filmu wideo w określonym uzyskasz szczegółowe dane wyjściowe JSON jako treść odpowiedzi. [Zobacz szczegółowe informacje o zwrócone dane JSON, w tym temacie](video-indexer-output-json.md).

## <a name="code-sample"></a>Przykład kodu

Poniższy fragment kodu języka C# pokazuje użycie wszystkich interfejsów API indeksatora wideo ze sobą.

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

[Sprawdź szczegóły w danych wyjściowych JSON](video-indexer-output-json.md).

## <a name="see-also"></a>Zobacz także

[Omówienie indeksatora wideo](video-indexer-overview.md)
