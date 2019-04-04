---
title: Za pomocą interfejsu API Video Indexer — platformy Azure
titlesuffix: Azure Media Services
description: W tym artykule pokazano, jak zacząć korzystać z interfejsu API usługi Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 03/25/2019
ms.author: juliako
ms.openlocfilehash: d915058e13f3c5287bdb037a1600e7f0a09da436
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58895668"
---
# <a name="tutorial-use-the-video-indexer-api"></a>Samouczek: Korzystanie z interfejsu API usługi Video Indexer

Usługa Video Indexer to pojedyncza usługa zintegrowana, w której skonsolidowano różne technologie sztucznej inteligencji firmy Microsoft związane z audio i wideo, co upraszcza programowanie. Interfejsy API zaprojektowano w celu umożliwienia deweloperom skupienia się na korzystaniu z technologii sztucznej inteligencji do obsługi multimediów bez konieczności martwienia się o skalę, zasięg globalny, dostępność i niezawodność platformy w chmurze. Za ich pomocą można przekazywać pliki, uzyskiwać szczegółowe informacje o wideo, pobierać adresy URL widżetów szczegółowych informacji i odtwarzacza w celu osadzania ich w aplikacji, a także wykonywać inne zadania.

Podczas tworzenia konta w usłudze Video Indexer można wybrać konto bezpłatnej wersji próbnej (w ramach którego otrzymuje się określoną liczbę bezpłatnych minut indeksowania) lub opcję płatną (w przypadku której nie ma ograniczeń przydziału). Usługa Video Indexer w bezpłatnej wersji próbnej udostępnia do 600 minut bezpłatnego indeksowania u użytkowników witryn internetowych oraz do 2400 minut bezpłatnego indeksowania u użytkowników interfejsów API. W przypadku płatnych opcji tworzenia konta Video Indexer, który jest [połączony z subskrypcją platformy Azure oraz konta usługi Azure Media Services](connect-to-azure.md). Naliczane są opłaty za minuty indeksowania, a także opłaty powiązane z kontem usługi Azure Media Services. 

W tym artykule pokazano, jak deweloperzy mogą korzystać z [interfejsu API usługi Video Indexer](https://api-portal.videoindexer.ai/).

## <a name="subscribe-to-the-api"></a>Subskrybowanie interfejsu API

1. Zaloguj się w [portalu dla deweloperów usługi Video Indexer](https://api-portal.videoindexer.ai/).
    
    ![Logowanie](./media/video-indexer-use-apis/video-indexer-api01.png)

   > [!Important]
   > * Musisz użyć tego samego dostawcy, który został użyty podczas tworzenia konta w usłudze Video Indexer.
   > * Osobistych kont Google i Microsoft (outlook/live) można używać tylko w przypadku kont wersji próbnej. Konta połączone z platformą Azure wymagają usługi Azure AD.
   > * Dla każdego adresu e-mail może istnieć tylko jedno aktywne konto. Jeśli użytkownik spróbuje zalogować się za pomocą adresu user@gmail.com na koncie związanym z serwisem LinkedIn, a następnie za pomocą adresu user@gmail.com na koncie Google, przy drugiej próbie zostanie wyświetlona strona błędu z informacją, że użytkownik już istnieje.

2. Subskrybuj.

    Wybierz kartę [Products](https://api-portal.videoindexer.ai/products) (Produkty). Następnie wybierz pozycję Authorization (Autoryzacja) i subskrybuj ten element. 
    
    ![Rejestrowanie](./media/video-indexer-use-apis/video-indexer-api02.png)

    > [!NOTE]
    > Nowi użytkownicy automatycznie subskrybują autoryzację.
    
    Po subskrybowaniu będzie można wyświetlić subskrypcję, a także klucze podstawowy i pomocniczy. Te klucze należy chronić. Powinno się ich używać tylko w kodzie serwera. Nie powinny być dostępne po stronie klienta (w plikach .js, .html itp.).

    ![Rejestrowanie](./media/video-indexer-use-apis/video-indexer-api03.png)

> [!TIP]
> Usługa Video Indexer może używać klucza pojedynczej subskrypcji do łączenia się z wieloma kontami usługi Video Indexer. Te konta usługi Video Indexer można następnie połączyć z różnymi kontami usługi Media Services.

## <a name="obtain-access-token-using-the-authorization-api"></a>Uzyskiwanie tokenu dostępu przy użyciu interfejsu API autoryzacji

Po subskrybowaniu interfejsu API autoryzacji można uzyskiwać tokeny dostępu. Te tokeny dostępu są używane do uwierzytelniania w interfejsie API operacji. 

Każde wywołanie interfejsu API operacji powinno być skojarzone z tokenem dostępu zgodnym z zakresem autoryzacji wywołania.

- Poziom użytkownika — tokeny dostępu poziomu użytkownika umożliwiają wykonywanie operacji na poziomie **użytkownika**. Na przykład uzyskiwanie informacji o skojarzonych kontach.
- Poziom konta — tokeny dostępu poziomu konta umożliwiają wykonywanie operacji na poziomie **konta** lub **pliku wideo**. Na przykład przekazanie pliku wideo, zwrócenie listy wszystkich plików wideo, pobranie szczegółowych informacji o wideo itp.
- Poziom wideo — tokeny dostępu poziomu wideo umożliwiają wykonywanie operacji na określonym **pliku wideo**. Na przykład pobranie szczegółowych informacji o wideo, pobranie napisów, pobranie widżetów itp. 

Możesz sterować tym, czy te tokeny są tylko do odczytu, czy umożliwiają edytowanie, określając parametr **allowEdit=true/false**.

W przypadku większości scenariuszy serwer-serwer prawdopodobnie będziesz używać tego samego tokenu **konta**, ponieważ pozwala on na operacje dotyczące zarówno **konta**, jak i **pliku wideo**. Jeśli jednak planujesz stosowanie wywołań usługi Video Indexer po stronie klienta (na przykład za pomocą języka JavaScript), dobrym rozwiązaniem jest użycie tokenu dostępu poziomu **pliku wideo**, aby uniemożliwić klientom uzyskiwanie dostępu do całego konta. To jest również powód tego, że w przypadku osadzania kodu klienta usługi Video Indexer w kliencie (na przykład za pomocą funkcji **pobierania widżetu szczegółowych informacji** lub **pobierania widżetu odtwarzacza**) należy podać token dostępu **pliku wideo**.

Aby ułatwić sprawę, można uzyskać informacje o kontach za pomocą pozycji **Authorization** API (Interfejs API autoryzacji) > **GetAccounts** (Pobierz konta) bez wcześniejszego pobierania tokenu użytkownika. Można również zażądać informacji o kontach z ważnymi tokenami, co umożliwia pominięcie dodatkowego wywołania w celu pobrania tokenu konta.

Tokeny dostępu wygasają po godzinie. Przed skorzystaniem z interfejsu API operacji upewnij się, że token dostępu jest ważny. Jeśli wygaśnie, ponownie wywołaj interfejs API autoryzacji, aby uzyskać nowy token dostępu.
 
Możesz już rozpocząć integrowanie z interfejsem API. Zapoznaj się ze [szczegółowymi opisami poszczególnych interfejsów API REST usługi Video Indexer](https://api-portal.videoindexer.ai/).

## <a name="account-id"></a>Identyfikator konta 

Identyfikator konta to parametr wymagany we wszystkich wywołaniach interfejsu API operacji. Identyfikator konta jest identyfikatorem GUID, który można uzyskać w jeden z następujących sposobów:

* Uzyskiwanie identyfikatora konta za pomocą **witryny internetowej usługi Video Indexer**:

    1. Przejdź do witryny internetowej [Video Indexer](https://www.videoindexer.ai/) i zaloguj się.
    2. Przejdź do strony **Settings** (Ustawienia).
    3. Skopiuj identyfikator konta.

        ![Identyfikator konta](./media/video-indexer-use-apis/account-id.png)

* Uzyskiwanie identyfikatora konta za pomocą **portalu dla deweloperów usługi Video Indexer**.

    Skorzystaj z interfejsu API [uzyskiwania kont](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Accounts?).
    
    > [!TIP]
    > Tokeny dostępu dla kont możesz wygenerować, definiując atrybut`generateAccessTokens=true`.
    
* Pobierz identyfikator konta z adresu URL strony odtwarzacza na swoim koncie.

    Podczas odtwarzania pliku wideo identyfikator jest podany po sekcji `accounts` i przed sekcją `videos`.

    ```
    https://www.videoindexer.ai/accounts/00000000-f324-4385-b142-f77dacb0a368/videos/d45bf160b5/
    ```

## <a name="recommendations"></a>Zalecenia

W tej sekcji przedstawiono kilka zaleceń dotyczących korzystania z interfejsu API usługi Video Indexer.

- Jeśli planujesz przekazanie pliku wideo, zalecamy umieszczenie pliku w publicznej lokalizacji sieciowej (na przykład w usłudze OneDrive). Uzyskaj link do pliku wideo, a następnie podaj ten adres URL jako parametr przekazywania pliku. 

    Adres URL przekazywany do usługi Video Indexer musi wskazywać plik multimedialny (audio lub wideo). Niektóre linki generowane przez usługę OneDrive prowadzą do zawierających te pliki stron HTML. Adres URL można łatwo zweryfikować, wklejając go w przeglądarce — jeśli rozpocznie się pobieranie pliku, prawdopodobnie jest to właściwy adres URL. Jeśli w przeglądarce renderowana jest jakaś wizualizacja, prawdopodobnie nie jest to link do pliku, ale do strony HTML.
    
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

## <a name="see-also"></a>Zobacz także

- [Omówienie indeksatora wideo](video-indexer-overview.md)
- [Regiony](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)

## <a name="next-steps"></a>Kolejne kroki

[Sprawdzanie szczegółów danych wyjściowych JSON](video-indexer-output-json-v2.md).

