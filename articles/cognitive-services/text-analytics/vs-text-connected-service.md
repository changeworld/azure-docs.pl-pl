---
title: Samouczek analiz C# | Dokumentacja firmy Microsoft
titleSuffix: Microsoft Cognitive Services
description: Nawiąż połączenie analizy tekstu z aplikacji sieci web programu ASP.NET Core.
services: cognitive-services
author: ghogen
manager: douge
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: conceptual
ms.date: 06/01/2018
ms.author: ghogen
ms.openlocfilehash: c97f75e0a41a4bf314963dc26c6424a0b773822b
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38665234"
---
# <a name="connect-to-the-text-analytics-service-by-using-connected-services-in-visual-studio"></a>Połączenia z usługą Analiza tekstu przy użyciu usług połączonych programu Visual Studio

Za pomocą usługi analizy tekstu, możesz wyodrębniaj rozbudowane informacje w celu kategoryzowania i przetwarzania danych wizualnych oraz przeprowadzać wspomaganego maszynowo moderowania obrazów, aby ułatwić nadzorowanie swoich usług.

W tym artykule i umieszczanych tam artykułach pomocnika należy podać szczegóły dotyczące korzystania z funkcji usługi Visual Studio połączone usługi Text Analytics. Ta funkcja jest dostępna w obu 15.7 2017 usługi Visual Studio lub nowszej, za pomocą zainstalowane rozszerzenie usług Cognitive Services.

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure. Jeśli jej nie masz, możesz zarejestrować się w celu [utworzenia bezpłatnego konta](https://azure.microsoft.com/pricing/free-trial/).
- Visual Studio 2017 w wersji 15.7, z zainstalowanym obciążeniem programowania dla sieci Web. [Pobierz teraz](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).

[!INCLUDE [vs-install-cognitive-services-vsix](../../../includes/vs-install-cognitive-services-vsix.md)]

## <a name="add-support-to-your-project-for-the-text-analytics-service"></a>Dodanie obsługi do projektu usługi Text Analytics

1. Utwórz nowy projekt sieci web platformy ASP.NET Core, o nazwie TextAnalyticsDemo. Użyj szablonu projektu aplikacji sieci Web (Model-View-Controller) z ustawieniami domyślnymi. Jest ważne, aby nazwa projektu MyWebApplication, dzięki czemu odpowiada przestrzeni nazw, po skopiowaniu kodu do projektu.  W przykładzie w tym artykule użyto MVC, ale usługa połączona analizy tekstu można użyć z dowolnym typem projektu programu ASP.NET.

1. W **Eksploratora rozwiązań**, kliknij dwukrotnie **podłączoną usługę** elementu.
   Zostanie wyświetlona strona usługi połączonej z usługami, które można dodać do projektu.

   ![Zrzut ekranu przedstawiający usługi połączonej w Eksploratorze rozwiązań](../media/vs-common/Connected-Services-Solution-Explorer.PNG)

1. W menu dostępnych usług wybierz **ocenę tonacji z analizy tekstu**.

   ![Zrzut ekranu z połączonych usług ekranu](./media/vs-text-connected-service/Cog-Text-Connected-Service-0.PNG)

   Jeśli po zalogowaniu się do programu Visual Studio i subskrypcji platformy Azure skojarzony z Twoim kontem, zostanie wyświetlona strona, która za pomocą listy rozwijanej z subskrypcjami.

   ![Zrzut ekranu z tekstu analizy podłączoną usługę ekranu](media/vs-text-connected-service/Cog-Text-Connected-Service-1.PNG)

1. Wybierz subskrypcję, którą chcesz użyć, a następnie wybierz nazwę usługi Text Analytics lub wybierz **Edytuj** link, aby zmodyfikować automatycznie wygenerowaną nazwę, wybierz grupę zasobów i warstwy cenowej.

   ![Zrzut ekranu przedstawiający grupy zasobów i ceny warstwy pola](media/vs-text-connected-service/Cog-Text-Connected-Service-2.PNG)

   Kliknij link, aby szczegóły na warstw cenowych.

1. Wybierz **Dodaj** Aby dodać obsługę usługi połączonej.
   Visual Studio modyfikuje projekt, aby dodać pakiety NuGet, wpisy w pliku konfiguracji i inne zmiany do obsługi połączeń z usługą analiza. **Okno danych wyjściowych** pokazuje dziennika, co się dzieje z projektem. Powinien zostać wyświetlony podobny do poniższego:

   ```output
    [6/1/2018 3:04:02.347 PM] Adding Text Analytics to the project.
    [6/1/2018 3:04:02.906 PM] Creating new Text Analytics...
    [6/1/2018 3:04:06.314 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Language' version 1.0.0-preview...
    [6/1/2018 3:04:56.759 PM] Retrieving keys...
    [6/1/2018 3:04:57.822 PM] Updating appsettings.json setting: 'ServiceKey' = '<service key>'
    [6/1/2018 3:04:57.827 PM] Updating appsettings.json setting: 'ServiceEndPoint' = 'https://westus.api.cognitive.microsoft.com/text/analytics/v2.0'
    [6/1/2018 3:04:57.832 PM] Updating appsettings.json setting: 'Name' = 'TextAnalyticsDemo'
    [6/1/2018 3:05:01.840 PM] Successfully added Text Analytics to the project.
    ```
 
## <a name="use-the-text-analytics-service-to-detect-the-language-for-a-text-sample"></a>Usługa analizy tekstu do wykrywania języka dla próbki tekstu.

1. Dodaj następujące instrukcje using w pliku Startup.cs.
 
   ```csharp
   using System.IO;
   using System.Net.Http;
   using System.Net.Http.Headers;
   using System.Text;
   using Microsoft.Extensions.Configuration;
   ```
 
1. Dodaj pole konfiguracji, a następnie dodaj konstruktor, który inicjuje w polu configuration w klasie uruchamiania, aby włączyć konfigurację w programie.

   ```csharp
      private IConfiguration configuration;

      public Startup(IConfiguration configuration)
      {
          this.configuration = configuration;
      }
   ```

1. Dodaj plik klasy w folderze kontrolerów, o nazwie DemoTextAnalyzeController i zastąp jego zawartość następującym kodem:

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
    
    Kod zawiera GetTextAnalyzeClient można pobrać klienta, obiekt, którego można użyć do wywołania interfejsu API analizy tekstu, a program obsługi żądania, który wywołuje DetectLanguage dla danego tekstu.

1. Dodaj klasę pomocnika MyHandler, który jest używany przez poprzedniego kodu.

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

1. W folderze modeli Dodaj klasę modelu.

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

1. Dodaj widok, który przedstawia analizowany tekst, języka, określane i wynik, który reprezentuje poziom ufności do analizy.
    
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
 
1. Skompiluj i uruchom przykład lokalnie. Wprowadź jakiś tekst i zobacz język wykrywa analizy tekstu.
   
## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie są już potrzebne, Usuń grupę zasobów. Spowoduje to usunięcie usługi cognitive Services i powiązane zasoby. Aby usunąć grupę zasobów za pośrednictwem portalu:

1. Wprowadź nazwę grupy zasobów w polu wyszukiwania w górnej części portalu. Gdy pojawi się grupę zasobów używaną w ramach tego samouczka w wynikach wyszukiwania, wybierz ją.
2. Wybierz pozycję **Usuń grupę zasobów**.
3. W polu **WPISZ NAZWĘ GRUPY ZASOBÓW:** wpisz nazwę grupy zasobów i wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o usłudze analizy tekstu, zapoznając się [dokumentacja usługi Text Analytics usługi](index.yml).
