---
title: Nawiązywanie połączenia z interfejsem API wyszukiwania wiadomości Bing za pomocą usług połączonych w programie Visual Studio i języka C#
titleSuffix: Azure Cognitive Services
description: Nawiąż połączenie z interfejsem API wyszukiwania wiadomości Bing z poziomu aplikacji internetowej platformy ASP.NET Core.
services: cognitive-services
author: ghogen
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: tutorial
ms.date: 03/01/2018
ms.author: ghogen
ms.openlocfilehash: 2925ca5a303876a68b6d605c7312d43af102b6e0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60680308"
---
# <a name="tutorial-connect-to-bing-news-search-api-with-connected-services-in-visual-studio-and-c"></a>Samouczek: Nawiązywanie połączenia z interfejsem API wyszukiwania wiadomości Bing za pomocą usług połączonych w programie Visual Studio i języka C#

Użycie interfejsu API wyszukiwania wiadomości Bing umożliwia aplikacjom i usługom korzystanie z możliwości działającej w skali internetowej wyszukiwarki bez reklam. Wyszukiwanie wiadomości Bing to jedna z usług wyszukiwania dostępnych w ramach usług Cognitive Services.

W tym artykule przedstawiono szczegółowe instrukcje dotyczące korzystania z interfejsu API wyszukiwania wiadomości Bing za pomocą funkcji usługi połączonej programu Visual Studio. Ta funkcja jest dostępna w programie Visual Studio 2017 15.7 lub nowszym z zainstalowanym rozszerzeniem usług Cognitive Services.

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure. Jeśli jej nie masz, możesz zarejestrować się w celu [utworzenia bezpłatnego konta](https://azure.microsoft.com/pricing/free-trial/).
- Program Visual Studio 2017 w wersji 15.7 z zainstalowanym pakietem roboczym Programowanie aplikacji internetowych. [Pobierz go teraz](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).

[!INCLUDE [vs-install-cognitive-services-vsix](../../../includes/vs-install-cognitive-services-vsix.md)]

## <a name="add-support-to-your-project-for-bing-news-search-api"></a>Dodawanie do projektu obsługi interfejsu API wyszukiwania wiadomości Bing

1. Utwórz nowy projekt internetowy ASP.NET Core o nazwie MyWebApplication. Użyj szablonu projektu **Aplikacja internetowa (Model-View-Controller)** z ustawieniami domyślnymi. Ważne jest, aby nazwać projekt MyWebApplication, dzięki czemu przestrzeń nazw będzie odpowiednia po skopiowaniu kodu do projektu. 

1. W **Eksploratorze rozwiązań** wybierz pozycję **Dodaj** > **Usługa połączona**.
   Zostanie wyświetlona strona Usługa połączona zawierająca usługi, które możesz dodać do projektu.

   ![Zrzut ekranu przedstawiający element menu Dodaj > Usługa połączona](../media/vs-common/Connected-Service-Menu.PNG)

1. Z menu dostępnych usług wybierz pozycję **Dołącz inteligentne wyszukiwanie do swoich aplikacji**.

   ![Zrzut ekranu przedstawiający listę usług połączonych](./media/vs-bing-news-search-connected-service/Cog-Search-Connected-Service-0.PNG)

   Jeśli logowanie do programu Visual Studio zostało już wykonane i masz subskrypcję platformy Azure skojarzoną z kontem, zostanie wyświetlona strona z listą rozwijaną zawierającą Twoje subskrypcje. Wybierz subskrypcję, której chcesz użyć, a następnie wybierz nazwę interfejsu API wyszukiwania wiadomości Bing. Możesz również wybrać pozycję **Edytuj**, aby zmodyfikować automatycznie wygenerowaną nazwę.

   ![Zrzut ekranu przedstawiający pola Subskrypcja i Nazwa](media/vs-bing-news-search-connected-service/Cog-Search-Connected-Service-1.PNG)

1. Wybierz grupę zasobów i warstwę cenową.

   ![Zrzut ekranu przedstawiający pola Grupa zasobów i Warstwa cenowa](media/vs-bing-news-search-connected-service/Cog-Search-Connected-Service-2.PNG)

   Aby uzyskać więcej informacji na temat warstw cenowych, wybierz pozycję **Przejrzyj cennik**.

1. Wybierz przycisk **Dodaj**, aby dodać obsługę usługi połączonej.
   Program Visual Studio zmodyfikuje projekt, dodając pakiety NuGet i wpisy pliku konfiguracji oraz wprowadzając inne zmiany na potrzeby obsługi połączenia z interfejsem API wyszukiwania wiadomości Bing. Dane wyjściowe pokazują zapis działań związanych z projektem. Powinna zostać wyświetlona zawartość podobna do tej:

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
 
## <a name="use-the-bing-news-search-api-to-add-search-functionality-to-a-web-page"></a>Dodawanie funkcji wyszukiwania do strony internetowej za pomocą interfejsu API wyszukiwania wiadomości Bing

Po dodaniu obsługi interfejsu API wyszukiwania wiadomości Bing do projektu możesz użyć tego interfejsu API, aby dodać funkcję inteligentnego wyszukiwania do strony internetowej, wykonując opisane poniżej czynności.

1. W pliku *Startup.cs* w ramach metody `ConfigureServices` dodaj wywołanie `IServiceCollection.AddSingleton`. W ten sposób udostępnisz obiekt konfiguracji zawierający kluczowe ustawienia na potrzeby kodu w projekcie.
 
   ```csharp
       public void ConfigureServices(IServiceCollection services)
       {
           services.AddMvc();
           services.AddSingleton<IConfiguration>(Configuration);
       }
   ```


1. W folderze **Models** dodaj nowy plik klasy o nazwie *BingNewsModel.cs*. Jeśli projekt nosi inną nazwę niż MyWebApplication, użyj tej nazwy do określenia przestrzeni nazw projektu. Zastąp zawartość pliku następującym kodem:
 
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

   Ten model jest używany do przechowywania wyników wywołania usługi wyszukiwania wiadomości Bing.
 
1. W folderze **Controllers** dodaj nowy plik klasy o nazwie *IntelligentSearchController.cs*. Zastąp zawartość pliku następującym kodem:

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

   W ramach tego kodu konstruktor konfiguruje obiekt konfiguracji zawierający klucze. Metoda dla trasy `Search` to po prostu przekierowanie do funkcji `BingSearchResult`. Powoduje ono wywołanie metody `GetNewsSearchClient` w celu uzyskania obiektu klienta `NewsSearchAPI`.  Obiekt klienta `NewsSearchAPI` zawiera metodę `SearchAsync`, która odpowiada za faktyczne wywołanie usługi i zwrócenie wyników w utworzonym poprzednio modelu `SearchResult`. 

1. Dodaj klasę `MyHandler`, do której odwołuje się wcześniejszy kod. Deleguje ona asynchroniczne wywołanie usługi wyszukiwania do klasy bazowej: `DelegatingHandler`.

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

1. Aby dodać obsługę przesyłania zapytań i wyświetlania wyników, w folderze **Views** utwórz nowy folder o nazwie **IntelligentSearch**. W tym nowym folderze dodaj widok *BingSearchResult.cshtml*. Skopiuj do niego poniższy kod:

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

1. Uruchom aplikację internetową lokalnie, wprowadź adres URL utworzonej właśnie strony (/IntelligentSearch/BingSearchResult), a następnie wyślij żądanie wyszukiwania za pomocą przycisku Search (Wyszukaj).

   ![Zrzut ekranu przedstawiający wyniki wyszukiwania wiadomości Bing](media/vs-bing-news-search-connected-service/Cog-News-Search-Results.PNG)
           
## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy grupa zasobów nie jest już potrzebna, możesz ją usunąć. Spowoduje to usunięcie usługi poznawczej i powiązanych zasobów. Aby usunąć grupę zasobów za pośrednictwem portalu:

1. Wprowadź nazwę grupy zasobów w polu wyszukiwania w górnej części portalu. Wybierz grupę zasobów, którą chcesz usunąć.
2. Wybierz pozycję **Usuń grupę zasobów**.
3. W polu **Wpisz nazwę grupy zasobów** wpisz nazwę grupy zasobów i wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat interfejsu API wyszukiwania wiadomości Bing, zobacz [Co to jest wyszukiwanie wiadomości Bing?](index.yml).
