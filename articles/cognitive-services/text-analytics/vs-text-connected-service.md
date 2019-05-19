---
title: 'Samouczek: Łączenie z usługą analizy tekstu za pomocą usług połączonych w programie Visual Studio'
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak nawiązywać połączenie z analizą tekstu z poziomu aplikacji internetowej platformy ASP.NET Core.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: tutorial
ms.date: 02/13/2019
ms.author: aahi
ms.openlocfilehash: 75228b8c939cb5b8dd04471662ba86b46cfc808c
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2019
ms.locfileid: "65860475"
---
# <a name="tutorial-connect-to-the-text-analytics-service-with-connected-services-in-visual-studio"></a>Samouczek: Łączenie z usługą analizy tekstu za pomocą usług połączonych w programie Visual Studio

Za pomocą usługi Text Analytics możesz wyodrębniać rozbudowane informacje w celu kategoryzowania i przetwarzania danych wizualnych oraz przeprowadzać wspomagane maszynowo moderowanie obrazów, aby ułatwić nadzorowanie usług.

W tym artykule i artykułach towarzyszących podano szczegółowe informacje na temat używania funkcji usługi połączonej programu Visual Studio na potrzeby usługi Text Analytics. Ta funkcja jest dostępna w obu Visual 2019 r Studio lub nowszej, za pomocą zainstalowane rozszerzenie usług Cognitive Services.

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure. Jeśli jej nie masz, możesz zarejestrować się w celu [utworzenia bezpłatnego konta](https://azure.microsoft.com/pricing/free-trial/).
- Dnia Visual Studio 2019 r, z zainstalowanym obciążeniem programowania dla sieci Web. [Pobierz go teraz](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).

[!INCLUDE [vs-install-cognitive-services-vsix](../../../includes/vs-install-cognitive-services-vsix.md)]

## <a name="add-support-to-your-project-for-the-text-analytics-service"></a>Dodawanie obsługi usługi Text Analytics do projektu

1. Utwórz nowy projekt internetowy platformy ASP.NET Core o nazwie TextAnalyticsDemo. Użyj szablonu projektu Aplikacja internetowa (Model-View-Controller) z ustawieniami domyślnymi. Ważne jest, aby nazwać projekt MyWebApplication, dzięki czemu przestrzeń nazw będzie odpowiednia po skopiowaniu kodu do projektu.  Przykład w tym artykule używa modelu MVC, lecz usługi połączonej Text Analytics można użyć z dowolnym typem projektu platformy ASP.NET.

1. W **Eksploratorze rozwiązań** kliknij dwukrotnie element **Usługa połączona**.
   Zostanie wyświetlona strona Usługa połączona zawierająca usługi, które możesz dodać do projektu.

   ![Zrzut ekranu usługi połączonej w Eksploratorze rozwiązań](../media/vs-common/Connected-Services-Solution-Explorer.PNG)

1. W menu dostępnych usług wybierz pozycję **Oceń opinię za pomocą analizy tekstu**.

   ![Zrzut ekranu usług połączonych](./media/vs-text-connected-service/Cog-Text-Connected-Service-0.PNG)

   Jeśli logowanie do programu Visual Studio zostało już wykonane i masz subskrypcję platformy Azure skojarzoną z kontem, zostanie wyświetlona strona z listą rozwijaną zawierającą Twoje subskrypcje.

   ![Zrzut ekranu usługi połączonej Text Analytics](media/vs-text-connected-service/Cog-Text-Connected-Service-1.PNG)

1. Wybierz subskrypcję, której chcesz użyć, i nazwę usługi Text Analytics lub link **Edytuj**, aby zmodyfikować nazwę wygenerowaną automatycznie oraz wybrać grupę zasobów i warstwę cenową.

   ![Zrzut ekranu z polami grupy zasobów i warstwy cenowej](media/vs-text-connected-service/Cog-Text-Connected-Service-2.PNG)

   Skorzystaj z linku, aby wyświetlić szczegóły warstw cenowych.

1. Wybierz przycisk **Dodaj**, aby dodać obsługę usługi połączonej.
   Program Visual Studio zmodyfikuje projekt, dodając pakiety NuGet i wpisy pliku konfiguracji oraz wprowadzając inne zmiany na potrzeby obsługi połączenia z usługą Text Analytics. **Okno danych wyjściowych** pokazuje dziennik działań związanych z projektem. Powinna zostać wyświetlona zawartość podobna do tej:

   ```output
    [6/1/2018 3:04:02.347 PM] Adding Text Analytics to the project.
    [6/1/2018 3:04:02.906 PM] Creating new Text Analytics...
    [6/1/2018 3:04:06.314 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Language' version 1.0.0-preview...
    [6/1/2018 3:04:56.759 PM] Retrieving keys...
    [6/1/2018 3:04:57.822 PM] Updating appsettings.json setting: 'ServiceKey' = '<service key>'
    [6/1/2018 3:04:57.827 PM] Updating appsettings.json setting: 'ServiceEndPoint' = 'https://westus.api.cognitive.microsoft.com/text/analytics/v2.1'
    [6/1/2018 3:04:57.832 PM] Updating appsettings.json setting: 'Name' = 'TextAnalyticsDemo'
    [6/1/2018 3:05:01.840 PM] Successfully added Text Analytics to the project.
    ```
 
## <a name="use-the-text-analytics-service-to-detect-the-language-for-a-text-sample"></a>Użycie usługi Text Analytics do wykrycia języka próbki tekstu

1. Dodaj następujące instrukcje using w pliku Startup.cs.
 
   ```csharp
   using System.IO;
   using System.Net.Http;
   using System.Net.Http.Headers;
   using System.Text;
   using Microsoft.Extensions.Configuration;
   ```
 
1. Dodaj pole configuration oraz konstruktor, który inicjuje to pole, w klasie Startup, aby włączyć konfigurację w programie.

   ```csharp
      private IConfiguration configuration;

      public Startup(IConfiguration configuration)
      {
          this.configuration = configuration;
      }
   ```

1. Dodaj plik klasy o nazwie DemoTextAnalyzeController w folderze Kontrolery i zastąp jego zawartość następującym kodem:

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Net.Http;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Azure.CognitiveServices.Language.TextAnalytics;
    using Microsoft.Azure.CognitiveServices.Language.TextAnalytics.Models;
    using Microsoft.Extensions.Configuration;
    using TextAnalyticsDemo.Models;
    
    namespace TextAnalyticsDemo.Controllers
    {
        public class DemoTextAnalyzeController : Controller
        {
            private IConfiguration configuration;
    
            public DemoTextAnalyzeController(IConfiguration configuration)
            {
                this.configuration = configuration;
            }
    
            public IActionResult TextAnalyzeResult(TextAnalyzeModel model)
            {
    
                if (!string.IsNullOrWhiteSpace(model.TextStr))
                {
                    ITextAnalyticsAPI client = this.GetTextAnalyzeClient(new MyHandler());
                    model.AnalyzeResult = client.DetectLanguage(
                        new BatchInput(
                            new List<Input>()
                            {
                                new Input("id",model.TextStr)
                            }));
                }
                return View(model);
            }
    
            [HttpPost("Analyze")]
            public IActionResult Analyze(TextAnalyzeModel model)
            {
                return RedirectToAction("TextAnalyzeResult", model);
            }
    
            // Using the ServiceKey from the configuration file,
            // get an instance of the Text Analytics client.
            private ITextAnalyticsAPI GetTextAnalyzeClient(DelegatingHandler handler)
            {
                string key = configuration.GetSection("CognitiveServices")["TextAnalytics:ServiceKey"];
    
                ITextAnalyticsAPI client = new TextAnalyticsAPI( handlers: handler);
                client.SubscriptionKey = key;
                client.AzureRegion = AzureRegions.Westus;
    
                return client;
            }
        }
    }
    ```
    
    Kod zawiera funkcję GetTextAnalyzeClient umożliwiającą pobranie obiektu klienta, którego można użyć do wywołania interfejsu API analizy tekstu, oraz procedurę obsługi żądania, która wywołuje funkcję DetectLanguage dla danego tekstu.

1. Dodaj klasę pomocy MyHandler używaną przez powyższy kod.

    ```csharp
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

1. W folderze Modele dodaj klasę modelu.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Threading.Tasks;
    using Microsoft.Azure.CognitiveServices.Language.TextAnalytics.Models;
    
    namespace Demo.Models
    {
        public class TextAnalyzeModel
        {
            public string TextStr { get; set; }
    
            public LanguageBatchResult AnalyzeResult { get; set; }
    
            public SentimentBatchResult AnalyzeResult2 { get; set; }
        }
    }
    ```

1. Dodaj widok na potrzeby wyświetlania analizowanego tekstu, określonego języka i wyniku reprezentującego poziom ufności analizy. Aby to zrobić, kliknij prawym przyciskiem myszy folder **Widoki**, wybierz pozycję **Dodaj**, a następnie **Widok**. W wyświetlonym oknie dialogowym podaj nazwę _TextAnalyzeResult_ i zaakceptuj ustawienia domyślne, aby dodać nowy plik o nazwie _TextAnalyzeResult.cshtml_ w folderze **Widoki**, a następnie skopiuj do niego następującą zawartość:
    
    ```cshtml
    @using System
    @model Demo.Models.TextAnalyzeModel
    
    @{
        ViewData["Title"] = "TextAnalyzeResult";
    }
    
    <h2>Text Language</h2>
    
    <div class="row">
        <section>
            <form asp-controller="DemoTextAnalyze" asp-action="Analyze" method="POST"
                  class="form-horizontal" enctype="multipart/form-data">
                <table width="90%">
                    <tr>
                        <td>
                            <input type="text" name="TextStr" class="form-control" />
                        </td>
                        <td>
                            <button type="submit" class="btn btn-default">Analyze</button>
                        </td>
                    </tr>
                </table>
            </form>
        </section>
    </div>
    
    <h2>Result</h2>
    <div>
        <dl class="dl-horizontal">
            <dt>
                Text :
            </dt>
            <dd>
                @Html.DisplayFor(model => model.TextStr)
            </dd>
            <dt>
                Language Name :
            </dt>
            <dd>
                @Html.DisplayFor(model => model.AnalyzeResult.Documents[0].DetectedLanguages[0].Name)
            </dd>
            <dt>
                Score :
            </dt>
            <dd>
                @Html.DisplayFor(model => model.AnalyzeResult.Documents[0].DetectedLanguages[0].Score)
            </dd>
        </dl>
    </div>
    <div>
        <hr />
        <p>
            <a asp-controller="Home" asp-action="Index">Return to Index</a>
        </p>
    </div>
    
    ```
 
1. Skompiluj i uruchom przykład lokalnie. Podaj jakiś tekst i zobacz, co wykrywa analiza tekstu.
   
## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy grupa zasobów nie jest już potrzebna, usuń ją. Spowoduje to usunięcie usługi poznawczej i powiązanych zasobów. Aby usunąć grupę zasobów za pośrednictwem portalu:

1. Wprowadź nazwę grupy zasobów w polu wyszukiwania w górnej części portalu. Gdy w wynikach wyszukiwania zobaczysz grupę zasobów używaną w tym samouczku, wybierz ją.
2. Wybierz pozycję **Usuń grupę zasobów**.
3. W polu **WPISZ NAZWĘ GRUPY ZASOBÓW:** wpisz nazwę grupy zasobów i wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o usłudze Text Analytics, zapoznając się z [dokumentacją usługi Text Analytics](index.yml).
