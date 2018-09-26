---
title: Samouczek usługi Bing News wyszukiwania języka C# | Dokumentacja firmy Microsoft
titleSuffix: Microsoft Cognitive Services
description: Wyszukiwanie wiadomości Bing w usłudze Cognitive Services należy nawiązać z aplikacji sieci web platformy ASP.NET Core.
services: cognitive-services
author: ghogen
manager: douge
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: conceptual
ms.date: 03/01/2018
ms.author: ghogen
ms.openlocfilehash: 5cfa82067d28b05f32bd87e0e83d55a03da8d508
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2018
ms.locfileid: "47095433"
---
# <a name="connect-to-bing-news-search-api-by-using-connected-services-in-visual-studio"></a>Łączenie z interfejsu API wyszukiwania wiadomości Bing za pomocą usług połączonych w programie Visual Studio

Korzystając z wyszukiwania wiadomości Bing, aby umożliwić aplikacjom i usługom mocy aparat wyszukiwania bez reklam, o określonym zakresie w sieci Web. Wyszukiwanie wiadomości Bing jest jednym z dostępne usługi wyszukiwania z usługami Cognitive Services.

Ten artykuł zawiera szczegółowe informacje dotyczące korzystania z funkcji usługi Visual Studio połączonych dla wyszukiwania wiadomości Bing. Ta funkcja jest dostępna, w programie Visual Studio 2017 w wersji 15.7 lub nowszej, zainstalowane rozszerzenie usług Cognitive Services.

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure. Jeśli jej nie masz, możesz zarejestrować się w celu [utworzenia bezpłatnego konta](https://azure.microsoft.com/pricing/free-trial/).
- Visual Studio 2017 w wersji 15.7, z zainstalowanym obciążeniem programowania dla sieci Web. [Pobierz teraz](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).

[!INCLUDE [vs-install-cognitive-services-vsix](../../../includes/vs-install-cognitive-services-vsix.md)]

## <a name="add-support-to-your-project-for-bing-news-search-api"></a>Dodanie obsługi do projektu interfejsu API wyszukiwania wiadomości Bing

1. Utwórz nowy projekt sieci web platformy ASP.NET Core o nazwie MyWebApplication. Użyj **aplikacji sieci Web (Model-View-Controller)** szablon projektu, używając ustawień domyślnych. Jest ważne, aby nazwa projektu MyWebApplication, dzięki czemu odpowiada przestrzeni nazw, po skopiowaniu kodu do projektu. 

1. W **Eksploratora rozwiązań**, wybierz **Dodaj** > **podłączoną usługę**.
   Zostanie wyświetlona strona usługi połączonej z usługami, które można dodać do projektu.

   ![Zrzut ekranu z Dodaj podłączoną usługę, element menu](../media/vs-common/Connected-Service-Menu.PNG)

1. W menu dostępnych usług wybierz **przenieść wyszukiwania do Twojej aplikacji inteligentnych**.

   ![Zrzut ekranu przedstawiający listę połączonych usług](./media/vs-bing-news-search-connected-service/Cog-Search-Connected-Service-0.PNG)

   Jeśli po zalogowaniu się do programu Visual Studio i subskrypcji platformy Azure skojarzony z Twoim kontem, zostanie wyświetlona strona, która za pomocą listy rozwijanej z subskrypcjami. Wybierz subskrypcję, której chcesz użyć, a następnie wybierz nazwę interfejsu API wyszukiwania wiadomości Bing. Można również wybrać **Edytuj** do modyfikowania nazwy wygenerowanej automatycznie.

   ![Zrzut ekranu przedstawiający subskrypcji i nazwę pola](media/vs-bing-news-search-connected-service/Cog-Search-Connected-Service-1.PNG)

1. Wybierz grupę zasobów i warstwy cenowej.

   ![Zrzut ekranu przedstawiający grupy zasobów i ceny warstwy pola](media/vs-bing-news-search-connected-service/Cog-Search-Connected-Service-2.PNG)

   Więcej informacji na temat warstw cenowych, zaznacz **Przegląd cennika**.

1. Wybierz **Dodaj** Aby dodać obsługę usługi połączonej.
   Visual Studio modyfikuje projekt, aby dodać pakiety NuGet, wpisy w pliku konfiguracji i inne zmiany, które umożliwiają połączenie interfejsu API wyszukiwania wiadomości Bing. Dane wyjściowe pokazują dziennik, co się dzieje z projektem. Powinien zostać wyświetlony podobny do poniższego:

   ```output
   [5/4/2018 12:41:21.084 PM] Adding Intelligent Search to the project.
   [5/4/2018 12:41:21.271 PM] Creating new Intelligent Search...
   [5/4/2018 12:41:24.128 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Search.ImageSearch' version 1.2.0...
   [5/4/2018 12:41:24.135 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Search.NewsSearch' version 1.2.0...
   [5/4/2018 12:41:24.154 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Search.WebSearch' version 1.2.0...
   [5/4/2018 12:41:24.168 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Search.CustomSearch' version 1.2.0...
   [5/4/2018 12:41:24.187 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Search.VideoSearch' version 1.2.0...
   [5/4/2018 12:42:07.287 PM] Retrieving keys...
   [5/4/2018 12:42:07.741 PM] Updating appsettings.json setting: 'ServiceKey' = 'c271412f3e4c4e1dacc7c4145fa0572a'
   [5/4/2018 12:42:07.745 PM] Updating appsettings.json setting: 'ServiceEndPoint' = 'https://api.cognitive.microsoft.com/bing/v7.0'
   [5/4/2018 12:42:07.749 PM] Updating appsettings.json setting: 'Name' = 'WebApplicationCore-Search_IntelligentSearch'
   [5/4/2018 12:42:10.217 PM] Successfully added Intelligent Search to the project.
   ```

   Plik appsettings.json zawiera teraz następujące nowe ustawienia:

   ```json
   "CognitiveServices": {
     "IntelligentSearch": {
       "ServiceKey": "<your service key>",
       "ServiceEndPoint": "https://api.cognitive.microsoft.com/bing/v7.0",
       "Name": "WebApplicationCore-Search_IntelligentSearch"
     }
   }
   ```
 
## <a name="use-the-bing-news-search-api-to-add-search-functionality-to-a-web-page"></a>Użyj interfejsu API wyszukiwania wiadomości Bing, aby dodać funkcje wyszukiwania do strony sieci web

Teraz, po dodaniu obsługi interfejsu API wyszukiwania wiadomości Bing do projektu, Oto jak dodawanie inteligentnego wyszukiwania do strony sieci web za pomocą interfejsu API.

1.  W *Startup.cs*w `ConfigureServices` metody, dodaj wywołanie do `IServiceCollection.AddSingleton`. To sprawia, że obiekt konfiguracji, który zawiera ustawienia klucza, która jest dostępna dla kodu w projekcie.
 
   ```csharp
        public void ConfigureServices(IServiceCollection services)
        {
            services.AddMvc();
            services.AddSingleton<IConfiguration>(Configuration);
        }
   ```


1. Dodaj nowy plik klasy w obszarze **modeli** folderu o nazwie *BingNewsModel.cs*. Jeśli nazwa projektu w różny sposób korzystania z przestrzeni nazw własny projekt, zamiast MyWebApplication. Zastąp zawartość następującym kodem:
 
    ```csharp
    using Microsoft.Azure.CognitiveServices.Search.NewsSearch.Models;
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Threading.Tasks;
    
    namespace MyWebApplication.Models
    {
        public class BingNewsModel
        {
            public News SearchResult { get; set; } 
            public string SearchText { get; set; }
        }
    }
    ```

   Ten model jest używany do przechowywania wyników wywołanie usługi wyszukiwania wiadomości Bing.
 
1. W **kontrolerów** folderu, Dodaj plik klasy o nazwie *IntelligentSearchController.cs*. Zastąp zawartość następującym kodem:

   ```csharp
    using System.Net.Http;
    using System.Threading.Tasks;
    using MyWebApplication.Models;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Azure.CognitiveServices.Search.NewsSearch;
    using Microsoft.Extensions.Configuration;
    
    namespace MyWebApplication.Controllers
    {
        // A controller to handle News Search requests
        public class IntelligentSearchController : Controller
        {
            private IConfiguration configuration;
  
            // Set up the configuration that contains the keys
            // (from the appsettings.json file)
            // that you will use to access the service  
            public IntelligentSearchController(IConfiguration configuration)
            {
                this.configuration = configuration;
            }

            // Call the Bing News Search API and put the result in the model object.    
            public async Task<IActionResult> BingSearchResult(BingNewsModel model)
            {
                if (!string.IsNullOrWhiteSpace(model.SearchText))
                { 
                    INewsSearchAPI client = this.GetNewsSearchClient(new MyHandler());
                    model.SearchResult = await client.News.SearchAsync(model.SearchText);
                }
                return View(model);
            }
    
            // Forward requests to the Search endpoint to the BingSearchResult method
            [HttpPost("Search")]
            public IActionResult Search(BingNewsModel model)
            {
                return RedirectToAction("BingSearchResult", model);
            }

            // Get the search client object
            private INewsSearchAPI GetNewsSearchClient(DelegatingHandler handler)
            {
                string key =
                   configuration.GetSection("CognitiveServices")["IntelligentSearch:ServiceKey"];
    
                INewsSearchAPI client = new NewsSearchAPI(
                   new ApiKeyServiceClientCredentials(key), handlers: handler);
    
                return client;
            }
        }
    }
   ```

   W tym kodzie Konstruktor konfiguruje obiekt konfiguracji, która zawiera klucze. Metoda `Search` trasa jest po prostu przekierowania do `BingSearchResult` funkcji. Powoduje to wywołanie `GetNewsSearchClient` metodę, aby uzyskać `NewsSearchAPI` obiektu klienta.  `NewsSearchAPI` Zawiera obiekt klienta `SearchAsync` metody, która faktycznie wywołuje usługę i zwraca wyniki w `SearchResult` modelu, który został utworzony. 

1. Dodaj klasę `MyHandler`, którego się odwoływano w poprzednim kodzie. To delegaty asynchroniczne wywołanie usługi wyszukiwania z klasą bazową `DelegatingHandler`.

   ```csharp
    using System.Net.Http;
    using System.Threading.Tasks;
    using System.Threading;

    class MyHandler : DelegatingHandler
    {
        protected async override Task<HttpResponseMessage> SendAsync(
        HttpRequestMessage request, CancellationToken cancellationToken)
        {
            // Call the inner handler.
            var response = await base.SendAsync(request, cancellationToken);
            
            return response;
        }
    }
   ```

1. Aby dodać obsługę przesyłania wyszukiwania i wyświetlania wyników w **widoków** folderu, Utwórz nowy folder o nazwie **IntelligentSearch**. W tym nowym folderze Dodaj widok *BingSearchResult.cshtml*. Skopiuj poniższy kod:

    ```cshtml
    @using System
    @model MyWebApplication.Models.BingNewsModel
    
    @{
        ViewData["Title"] = "BingSearchResult";
    }
    
    <h2>Search News</h2>
    
    <div class="row">
        <section>
            <form asp-controller="IntelligentSearch" asp-action="Search" method="POST"
                  class="form-horizontal" enctype="multipart/form-data">
                <table width ="90%">
                    <tr>
                        <td>
                            <input type="text" name="SearchText" class="form-control" />
                        </td>
                        <td>
                            <button type="submit" class="btn btn-default">Search</button>
                        </td>
                    </tr>
                </table>
            </form>
        </section>
    </div>
    <h2>Search Result</h2=
    <table>
    @if (!string.IsNullOrEmpty(Model.SearchText)) {
        foreach (var item in Model.SearchResult.Value) {
        <tr>
            <td rowspan="2" width="90">
               <img src=@item?.Image?.Thumbnail?.ContentUrl width="80" height="80" />
            </td>
            <td><a href=@item.Url>@item.Name</a></td>
        </tr>   
        <tr>
            <td>@item.Description</td>
        </tr>
        <tr height="10">
            <td/><td/>
         </tr>
        } }
     </table>
    <div>
        <hr />
        <p>
            <a asp-controller="Home" asp-action="Index">Return to Index</a>
        </p>
    </div>
    ```

1. Uruchom lokalnie aplikacji sieci web, wprowadź adres URL strony właśnie utworzony (/ IntelligentSearch/BingSearchResult) i opublikuj żądanie wyszukiwania, korzystając z przycisku wyszukiwania.

   ![Zrzut ekranu z Bing News wyniki](media/vs-bing-news-search-connected-service/Cog-News-Search-Results.PNG)
           
## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy grupa zasobów nie jest już potrzebny, można ją usunąć. Spowoduje to usunięcie usługi cognitive Services i powiązane zasoby. Aby usunąć grupę zasobów za pośrednictwem portalu:

1. Wprowadź nazwę grupy zasobów, w polu wyszukiwania w górnej części portalu. Wybierz grupę zasobów, które chcesz usunąć.
2. Wybierz pozycję **Usuń grupę zasobów**.
3. W **wpisz nazwę grupy zasobów** pole, wprowadź nazwę grupy zasobów i wybierz **Usuń**.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat interfejsu API wyszukiwania wiadomości Bing, zobacz [co to jest wyszukiwanie wiadomości Bing?](index.yml).
