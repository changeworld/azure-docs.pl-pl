---
title: 'Przykład: Tworzenie niestandardowego umiejętności w potoku kognitywnych wyszukiwania (Azure Search) | Dokumentacja firmy Microsoft'
description: Przedstawiono w niestandardowych umiejętności mapowane na wyszukiwanie kognitywnych indeksowania potoku w usłudze Azure Search przy użyciu interfejsu API tłumaczenie tekstu.
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: 056cff192b25068fa2e895fd46d143a834b7af0b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34641088"
---
# <a name="example-create-a-custom-skill-using-the-text-translate-api"></a>Przykład: Tworzenie niestandardowego umiejętności, przy użyciu interfejsu API tłumaczenie tekstu

W tym przykładzie Dowiedz się, jak utworzyć sieć web umiejętności niestandardowego interfejsu API, akceptuje tekstu w dowolnym języku, który tłumaczy je na język angielski. W przykładzie użyto [funkcji platformy Azure](https://azure.microsoft.com/services/functions/) opakowywać [tłumaczenie tekstu API](https://azure.microsoft.com/services/cognitive-services/translator-text-api/) tak, aby ją implementuje interfejs niestandardowych umiejętności.

## <a name="prerequisites"></a>Wymagania wstępne

+ Przeczytaj informacje o [umiejętności niestandardowy interfejs](cognitive-search-custom-skill-interface.md) artykułu, jeśli nie masz doświadczenia z interfejsem wejścia/wyjścia, który powinien implementować niestandardowe umiejętności.

+ [Załóż API tekst Translator](../cognitive-services/translator/translator-text-how-to-signup.md)i uzyskać klucz interfejsu API, aby pobrać go.

+ Zainstaluj [programu Visual Studio 2017 wersji 15,5 cala](https://www.visualstudio.com/vs/) lub później, w tym obciążenia rozwoju platformy Azure.

## <a name="create-an-azure-function"></a>Tworzenie funkcji platformy Azure

Mimo że w tym przykładzie użyto funkcji Azure do hostowania interfejsu API sieci web, nie jest wymagane.  Tak długo, jak spełniasz [interfejsu wymagania dotyczące kognitywnych umiejętności](cognitive-search-custom-skill-interface.md), podejście, należy wykonać, jest bez znaczenia. Środowisko Azure Functions, jednak ułatwiają tworzenie niestandardowych umiejętności.

### <a name="create-a-function-app"></a>Tworzenie aplikacji funkcji

1. W programie Visual Studio, wybierz **nowy** > **projektu** z menu Plik.

1. W oknie dialogowym Nowy projekt, wybierz **zainstalowana**, rozwiń węzeł **Visual C#** > **chmury**, wybierz pozycję **usługi Azure Functions**, wpisz Nazwa projektu, a następnie wybierz **OK**. Nazwa aplikacji funkcji musi być prawidłową nazwą przestrzeni nazw C#, dlatego nie należy używać znaków podkreślenia, łączników ani znaków innych niż alfanumeryczne.

1. Wybierz typ **wyzwalacza HTTP**

1. Dla konta magazynu, można wybrać **Brak**, ponieważ jeden z magazynów nie są wymagane dla tej funkcji.

1. Wybierz **OK** do funkcji tworzenia projektu i HTTP wyzwalane funkcji.

### <a name="modify-the-code-to-call-the-translate-cognitive-service"></a>Zmodyfikuj kod do wywoływania usługi kognitywnych tłumaczenia

Program Visual Studio tworzy projekt, a w nim klasę zawierającą standardowy kod dla wybranego typu funkcji. Atrybut *FunctionName* metody ustawia nazwę funkcji. Atrybut *HttpTrigger* określa, że funkcja jest wyzwalana przez żądanie HTTP.

Teraz, Zastąp całą zawartość pliku *Function1.cs* następującym kodem:

```csharp
using System.IO;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Azure.WebJobs.Host;
using Newtonsoft.Json;
using System.Net.Http;
using System.Collections.Generic;
using System.Threading.Tasks;
using System.Text;

namespace TranslateFunction
{
    // This function will simply translate messages sent to it.
    public static class Function1
    {
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


        /// <summary>
        /// Note that this function can translate up to 1000 characters. If you expect to need to translate more characters, use 
        /// the paginator skill before calling this custom enricher
        /// </summary>
        [FunctionName("Translate")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Function, "post", Route = null)]HttpRequest req, 
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");

            string recordId = null;
            string originalText = null;
            string originalLanguage = null;
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
            originalLanguage = data?.values?.First?.data?.language?.Value as string;

            if (recordId == null)
            {
                return new BadRequestObjectResult("recordId cannot be null");
            }

            // Only translate records that actually need to be translated. 
            if (!originalLanguage.Contains("en"))
            {
                translatedText = TranslateText(originalText, "en-us").Result;
            }
            else
            {
                // text is already in English.
                translatedText = originalText;
            }

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
        /// Use Cognitive Service to translate text from one language to antoher.
        /// </summary>
        /// <param name="myText">The text to translate</param>
        /// <param name="destinationLanguage">The language you want to translate to.</param>
        /// <returns>Asynchronous task that returns the translated text. </returns>
        async static Task<string> TranslateText(string myText, string destinationLanguage)
        {
            string host = "https://api.microsofttranslator.com";
            string path = "/V2/Http.svc/Translate";

            // NOTE: Replace this example key with a valid subscription key.
            string key = "064d8095730d4a99b49f4bcf16ac67f8";

            HttpClient client = new HttpClient();
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", key);

            List<KeyValuePair<string, string>> list = new List<KeyValuePair<string, string>>() {
                new KeyValuePair<string, string>(myText, "en-us")
            };

            StringBuilder totalResult = new StringBuilder();

            foreach (KeyValuePair<string, string> i in list)
            {
                string uri = host + path + "?to=" + i.Value + "&text=" + System.Net.WebUtility.UrlEncode(i.Key);

                HttpResponseMessage response = await client.GetAsync(uri);

                string result = await response.Content.ReadAsStringAsync();

                // Parse the response XML
                System.Xml.XmlDocument xmlResponse = new System.Xml.XmlDocument();
                xmlResponse.LoadXml(result);
                totalResult.Append(xmlResponse.InnerText); 
            }

            return totalResult.ToString();
        }
    }
}
```

Upewnij się, że wprowadź własne *klucza* wartość w *TranslateText* metoda oparta na kluczu uzyskano podczas rejestracji dla interfejsu API tłumaczenie tekstu.

W tym przykładzie jest proste enricher, który działa tylko na jeden rekord naraz. Ten fakt staje się ważne później, gdy ustawienie rozmiar wsadu dla skillset.

## <a name="test-the-function-from-visual-studio"></a>Testowanie funkcji w programie Visual Studio

Naciśnij klawisz **F5** do uruchomienia programu i testowania zachowania funkcji. Użyj Postman lub Fiddler do wystawiania wywołania podobny do przedstawionego poniżej:

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
               "language": "es"
            }
        }
   ]
}
```
### <a name="response"></a>Odpowiedź
Powinna zostać wyświetlona odpowiedź podobną do poniższego przykładu:

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

Po zakończeniu zachowanie funkcji można go opublikować.

1. W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Opublikuj**. Wybierz **tworzenia nowych** > **publikowania**.

1. Jeśli jeszcze nie zostało to jeszcze połączenia programu Visual Studio do konta platformy Azure, wybierz **Dodaj konto...**

1. Postępuj zgodnie z wyświetlanymi na ekranie. Należy określić konto platformy Azure, grupy zasobów, plan hostingu i konto magazynu, którego chcesz użyć. Jeśli nie masz jeszcze te można utworzyć nową grupę zasobów, nowy plan hostingu i konto magazynu. Po zakończeniu wybierz **Utwórz**

1. Po zakończeniu wdrażania Zanotuj adres URL witryny. Jest to adres funkcji aplikacji na platformie Azure. 

1. W [portalu Azure](https://portal.azure.com), przejdź do grupy zasobów i poszukaj tłumaczenie funkcja, która została opublikowana. W obszarze **Zarządzaj** sekcji, powinny pojawić się za pomocą klucza hosta. Wybierz **kopiowania** ikonę *domyślne* klucz hosta.  


## <a name="test-the-function-in-azure"></a>Testowanie funkcji na platformie Azure

Teraz, po klucz hosta domyślnego przetestować funkcję w następujący sposób:

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
               "language": "es"
            }
        }
   ]
}
```

To powinna dawać wynik podobny do tego, który został wcześniej wyświetlony podczas uruchamiania funkcji w środowisku lokalnym.

## <a name="connect-to-your-pipeline"></a>Połącz do potoku sieci
Teraz, gdy masz nowych umiejętności niestandardowych, można dodać go do Twojego skillset. W poniższym przykładzie przedstawiono sposób wywoływania umiejętności. Ponieważ umiejętności nie obsługuje partie, Dodaj instrukcję maksymalny rozmiar wsadu należy po prostu ```1``` Aby wysyłać dokumenty pojedynczo.

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
            "source": "/document/languageCode"
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

## <a name="next-steps"></a>Następne kroki
Gratulacje! Utworzono Twojego pierwszego enricher niestandardowych. Teraz można wykonać tego samego wzorca w celu dodania własnych funkcji niestandardowych. 

+ [Dodaj niestandardowy umiejętności do potoku kognitywnych wyszukiwania](cognitive-search-custom-skill-interface.md)
+ [Sposób definiowania skillset](cognitive-search-defining-skillset.md)
+ [Utwórz Skillset (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Sposób mapowania pól wzbogaconego](cognitive-search-output-field-mapping.md)