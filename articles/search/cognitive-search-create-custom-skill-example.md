---
title: 'Przykład: Tworzenie umiejętności niestandardowe w usłudze wyszukiwania poznawczego potok — usługa Azure Search'
description: Demonstruje użycie interfejsu API tłumaczenia tekstu w umiejętności niestandardowych mapowane na usługa cognitive search indeksowanie potoku w usłudze Azure Search.
manager: pablocas
author: luiscabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: f3b4e6cd18a362775443bb296560a076aaa1497d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61344163"
---
# <a name="example-create-a-custom-skill-using-the-text-translate-api"></a>Przykład: Tworzenie niestandardowych umiejętności, przy użyciu interfejsu API tłumaczenia tekstu

W tym przykładzie Dowiedz się, jak utworzyć sieć web umiejętności niestandardowego interfejsu API, która akceptuje tekstu w dowolnym języku i przekształca je do języka angielskiego. W przykładzie użyto [funkcji platformy Azure](https://azure.microsoft.com/services/functions/) opakowywać [tłumaczenia interfejsu API tłumaczenia tekstu](https://azure.microsoft.com/services/cognitive-services/translator-text-api/) tak, aby go implementuje interfejs umiejętności niestandardowe.

## <a name="prerequisites"></a>Wymagania wstępne

+ Przeczytaj o [interfejsu umiejętności niestandardowe](cognitive-search-custom-skill-interface.md) artykułu, jeśli użytkownik nie jest zaznajomiony z interfejsem wejścia/wyjścia, która powinna implementować niestandardowe umiejętności.

+ [Załóż konto interfejsu API tłumaczenia tekstu](../cognitive-services/translator/translator-text-how-to-signup.md)i Uzyskaj klucz interfejsu API można pobrać go.

+ Zainstaluj [programu Visual Studio 2017 w wersji 15.5](https://www.visualstudio.com/vs/) lub później, np. obciążenie programowanie na platformie Azure.

## <a name="create-an-azure-function"></a>Tworzenie funkcji platformy Azure

Chociaż ten przykład używa funkcji platformy Azure do hostowania interfejsu API sieci web, nie jest wymagany.  Tak długo, jak spełniasz [interfejsu wymagania dotyczące cognitive umiejętności](cognitive-search-custom-skill-interface.md), podejścia jest bez znaczenia. Usługa Azure Functions, jednak ułatwiają tworzenie umiejętności niestandardowe.

### <a name="create-a-function-app"></a>Tworzenie aplikacji funkcji

1. W programie Visual Studio, wybierz **New** > **projektu** za pomocą menu Plik.

1. W oknie dialogowym Nowy projekt, wybierz **zainstalowane**, rozwiń węzeł **Visual C#** > **chmury**, wybierz opcję **usługi Azure Functions**, wpisz Nazwa projektu, a następnie wybierz pozycję **OK**. Nazwa aplikacji funkcji musi być prawidłową nazwą przestrzeni nazw C#, dlatego nie należy używać znaków podkreślenia, łączników ani znaków innych niż alfanumeryczne.

1. Wybierz **usługi Azure Functions w wersji 2 (.NET Core)**. Można też zrobić to z wersją 1, ale kod napisany poniżej jest oparty na szablonie v2.

1. Wybierz typ jako **wyzwalacz HTTP**

1. Dla konta magazynu można wybrać **Brak**, ponieważ nie ma potrzeby każdy magazyn dla tej funkcji.

1. Wybierz **OK** można utworzyć funkcji w projekcie i HTTP funkcja wyzwalana przez.

### <a name="modify-the-code-to-call-the-translate-cognitive-service"></a>Zmodyfikuj kod do wywoływania usługi Cognitive tłumaczenie

Program Visual Studio tworzy projekt, a w nim klasę zawierającą standardowy kod dla wybranego typu funkcji. Atrybut *FunctionName* metody ustawia nazwę funkcji. Atrybut *HttpTrigger* określa, że funkcja jest wyzwalana przez żądanie HTTP.

Teraz Zastąp całą zawartość pliku *Function1.cs* następującym kodem:

```csharp
using System;
using System.Net.Http;
using System.Collections.Generic;
using System.Threading.Tasks;
using System.IO;
using System.Text;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Azure.WebJobs.Host;
using Newtonsoft.Json;

namespace TranslateFunction
{
    // This function will simply translate messages sent to it.
    public static class Function1
    {
        static string path = "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0";

        // NOTE: Replace this example key with a valid subscription key.
        static string key = "<enter your api key here>";

        #region classes used to serialize the response
        private class WebApiResponseError
        {
            public string message { get; set; }
        }

        private class WebApiResponseWarning
        {
            public string message { get; set; }
        }

        private class WebApiResponseRecord
        {
            public string recordId { get; set; }
            public Dictionary<string, object> data { get; set; }
            public List<WebApiResponseError> errors { get; set; }
            public List<WebApiResponseWarning> warnings { get; set; }
        }

        private class WebApiEnricherResponse
        {
            public List<WebApiResponseRecord> values { get; set; }
        }
        #endregion

        [FunctionName("Translate")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Function, "post", Route = null)]HttpRequest req,
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");

            string recordId = null;
            string originalText = null;
            string toLanguage = null;
            string translatedText = null;

            string requestBody = new StreamReader(req.Body).ReadToEnd();
            dynamic data = JsonConvert.DeserializeObject(requestBody);

            // Validation
            if (data?.values == null)
            {
                return new BadRequestObjectResult(" Could not find values array");
            }
            if (data?.values.HasValues == false || data?.values.First.HasValues == false)
            {
                // It could not find a record, then return empty values array.
                return new BadRequestObjectResult(" Could not find valid records in values array");
            }

            recordId = data?.values?.First?.recordId?.Value as string;
            originalText = data?.values?.First?.data?.text?.Value as string;
            toLanguage = data?.values?.First?.data?.language?.Value as string;

            if (recordId == null)
            {
                return new BadRequestObjectResult("recordId cannot be null");
            }

            translatedText = TranslateText(originalText, toLanguage).Result;
        
            // Put together response.
            WebApiResponseRecord responseRecord = new WebApiResponseRecord();
            responseRecord.data = new Dictionary<string, object>();
            responseRecord.recordId = recordId;
            responseRecord.data.Add("text", translatedText);

            WebApiEnricherResponse response = new WebApiEnricherResponse();
            response.values = new List<WebApiResponseRecord>();
            response.values.Add(responseRecord);

            return (ActionResult)new OkObjectResult(response);
        }


        /// <summary>
        /// Use Cognitive Service to translate text from one language to another.
        /// </summary>
        /// <param name="originalText">The text to translate.</param>
        /// <param name="toLanguage">The language you want to translate to.</param>
        /// <returns>Asynchronous task that returns the translated text. </returns>
        async static Task<string> TranslateText(string originalText, string toLanguage)
        {
            System.Object[] body = new System.Object[] { new { Text = originalText } };
            var requestBody = JsonConvert.SerializeObject(body);

            var uri = $"{path}&to={toLanguage}";

            string result = "";

            using (var client = new HttpClient())
            using (var request = new HttpRequestMessage())
            {
                request.Method = HttpMethod.Post;
                request.RequestUri = new Uri(uri);
                request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");
                request.Headers.Add("Ocp-Apim-Subscription-Key", key);

                var response = await client.SendAsync(request);
                var responseBody = await response.Content.ReadAsStringAsync();

                dynamic data = JsonConvert.DeserializeObject(responseBody);
                result = data?.First?.translations?.First?.text?.Value as string;

            }
            return result;
        }
    }
}
```

Upewnij się wprowadzić własne *klucz* wartość w *TranslateText* metody na podstawie klucza stało się podczas tworzenia interfejsu API tłumaczenia tekstu.

W tym przykładzie jest proste enricher, który działa tylko na jeden rekord jednocześnie. Fakt ten staje się ważne później, podczas ustawiania rozmiaru partii dla zestawu umiejętności.

## <a name="test-the-function-from-visual-studio"></a>Testowanie funkcji w programie Visual Studio

Naciśnij klawisz **F5** Aby uruchomić program i testowanie zachowania funkcji. W tym przypadku użyjemy funkcji poniżej umożliwia tłumaczenie tekstu w języku hiszpańskim na język angielski. Użyj narzędzia Postman lub Fiddler do wysyłania wywołania, tak jak pokazano poniżej:

```http
POST https://localhost:7071/api/Translate
```
### <a name="request-body"></a>Treść żądania
```json
{
   "values": [
        {
            "recordId": "a1",
            "data":
            {
               "text":  "Este es un contrato en Inglés",
               "language": "en"
            }
        }
   ]
}
```
### <a name="response"></a>Odpowiedź
Powinny pojawić się odpowiedź podobna do poniższego przykładu:

```json
{
    "values": [
        {
            "recordId": "a1",
            "data": {
                "text": "This is a contract in English"
            },
            "errors": null,
            "warnings": null
        }
    ]
}
```

## <a name="publish-the-function-to-azure"></a>Publikowanie funkcji na platformie Azure

Gdy jesteś zadowolony z zachowaniem funkcji, możesz opublikować go.

1. W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Opublikuj**. Wybierz **tworzenia nowych** > **publikowania**.

1. Jeśli nie zostało jeszcze połączone programu Visual Studio do konta platformy Azure, wybierz opcję **Dodaj konto...**

1. Postępuj zgodnie z monitami wyświetlanymi na ekranie. Należy określić konto platformy Azure, grupę zasobów, plan hostingu i konto magazynu, którego chcesz użyć. Jeśli nie masz jeszcze te można utworzyć nową grupę zasobów, nowy plan hostingu i konta magazynu. Po zakończeniu wybierz pozycję **Create**

1. Po zakończeniu wdrożenia należy pamiętać, adres URL witryny. Jest adresem aplikacji funkcji na platformie Azure. 

1. W [witryny Azure portal](https://portal.azure.com), przejdź do grupy zasobów i poszukaj tłumaczenie funkcja, która została opublikowana. W obszarze **Zarządzaj** sekcji, powinien zostać wyświetlony klucze hosta. Wybierz **kopiowania** ikonę *domyślne* klucz hosta.  

## <a name="test-the-function-in-azure"></a>Testowanie funkcji na platformie Azure

Teraz, gdy domyślny klucz hosta, testowanie funkcji w następujący sposób:

```http
POST https://translatecogsrch.azurewebsites.net/api/Translate?code=[enter default host key here]
```
### <a name="request-body"></a>Treść żądania
```json
{
   "values": [
        {
            "recordId": "a1",
            "data":
            {
               "text":  "Este es un contrato en Inglés",
               "language": "en"
            }
        }
   ]
}
```

To powinno to dawać wynik podobny do tego, które zostały wcześniej użyte podczas działania funkcji w środowisku lokalnym.

## <a name="connect-to-your-pipeline"></a>Nawiązać połączenie z potokiem
Teraz, gdy masz nowych umiejętności niestandardowe, można dodać go do Twojego zestawu umiejętności. W poniższym przykładzie przedstawiono sposób wywoływania umiejętności. Ponieważ umiejętności nie obsługuje partii, Dodaj instrukcję maksymalny rozmiar partii to po prostu ```1``` wysyłać dokumenty pojedynczo.

```json
{
    "skills": [
      ...,  
      {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "Our new translator custom skill",
        "uri": "http://translatecogsrch.azurewebsites.net/api/Translate?code=[enter default host key here]",
        "batchSize":1,
        "context": "/document",
        "inputs": [
          {
            "name": "text",
            "source": "/document/content"
          },
          {
            "name": "language",
            "source": "/document/destinationLanguage"
          }
        ],
        "outputs": [
          {
            "name": "text",
            "targetName": "translatedText"
          }
        ]
      }
  ]
}
```

## <a name="next-steps"></a>Kolejne kroki
Gratulacje! Utworzono Twojego pierwszego enricher niestandardowych. Teraz można wykonać tego samego wzorca, aby dodać własne niestandardowe funkcje. 

+ [Dodaj umiejętności niestandardowe do potoku wyszukiwania kognitywnego](cognitive-search-custom-skill-interface.md)
+ [Jak Definiowanie zestawu umiejętności](cognitive-search-defining-skillset.md)
+ [Tworzenie zestawu umiejętności (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Sposób mapowania pól wzbogacony](cognitive-search-output-field-mapping.md)
