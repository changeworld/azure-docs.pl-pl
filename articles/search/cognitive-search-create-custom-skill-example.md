---
title: Przykład umiejętności niestandardowych przy użyciu interfejsu API wyszukiwania jednostek Bing
titleSuffix: Azure Cognitive Search
description: Pokazuje, przy użyciu usługi wyszukiwania jednostek Bing w umiejętności niestandardowe mapowane do potoku indeksowania wzbogaconego o sztuczną inteligencję w usłudze Azure Cognitive Search.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 2994c55b39d30ff16a0ca135e93a116784feb201
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113822"
---
# <a name="example-create-a-custom-skill-using-the-bing-entity-search-api"></a>Przykład: Tworzenie umiejętności niestandardowych przy użyciu interfejsu API wyszukiwania jednostek Bing

W tym przykładzie dowiedz się, jak utworzyć niestandardową umiejętność interfejsu API sieci Web. Ta umiejętność będzie akceptować lokalizacje, osoby publiczne i organizacje oraz zwracać im opisy. W przykładzie użyto [funkcji platformy Azure](https://azure.microsoft.com/services/functions/) do zawijania interfejsu API wyszukiwania [jednostek Bing,](https://azure.microsoft.com/services/cognitive-services/bing-entity-search-api/) tak aby implementuje niestandardowy interfejs umiejętności.

## <a name="prerequisites"></a>Wymagania wstępne

+ Przeczytaj o [niestandardowy interfejs umiejętności](cognitive-search-custom-skill-interface.md) artykuł, jeśli nie są zaznajomieni z interfejsem wejścia/wyjścia, który należy zaimplementować umiejętności niestandardowe.

+ [!INCLUDE [cognitive-services-bing-entity-search-signup-requirements](../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

+ Zainstaluj [program Visual Studio 2019](https://www.visualstudio.com/vs/) lub nowszą, w tym obciążenie deweloperskie platformy Azure.

## <a name="create-an-azure-function"></a>Tworzenie funkcji platformy Azure

Chociaż w tym przykładzie używa funkcji platformy Azure do hostowania interfejsu API sieci web, nie jest wymagane.  Tak długo, jak spełniają [wymagania interfejsu dla umiejętności poznawczych,](cognitive-search-custom-skill-interface.md)podejście, które można podjąć jest bez znaczenia. Usługa Azure Functions ułatwia jednak tworzenie umiejętności niestandardowych.

### <a name="create-a-function-app"></a>Tworzenie aplikacji funkcji

1. W programie Visual Studio wybierz polecenie **Nowy** > **projekt** z menu Plik.

1. W oknie dialogowym Nowy projekt wybierz pozycję **Zainstalowano,** rozwiń pozycję **Visual C#** > **Cloud**, wybierz pozycję **Azure Functions**, wpisz nazwę projektu i wybierz przycisk **OK**. Nazwa aplikacji funkcji musi być prawidłowa jako obszar nazw języka C#, więc nie należy używać podkreśleń, łączników ani innych znaków innych niż alfanumeryczne.

1. Wybierz **usługę Azure Functions w wersji 2 (core.NET)**. Można to również zrobić w wersji 1, ale kod napisany poniżej jest oparty na szablonie v2.

1. Wybierz typ **wyzwalacza HTTP**

1. W przypadku konta magazynu można wybrać **opcję Brak,** ponieważ nie będzie potrzebny żaden magazyn dla tej funkcji.

1. Wybierz **przycisk OK,** aby utworzyć projekt funkcji i funkcję wyzwalane HTTP.

### <a name="modify-the-code-to-call-the-bing-entity-search-service"></a>Modyfikowanie kodu w celu wywołania usługi wyszukiwania jednostek Bing

Program Visual Studio tworzy projekt, a w nim klasę zawierającą standardowy kod dla wybranego typu funkcji. Atrybut *FunctionName* metody ustawia nazwę funkcji. Atrybut *HttpTrigger* określa, że funkcja jest wyzwalana przez żądanie HTTP.

Teraz zastąp całą zawartość pliku *Function1.cs* następującym kodem:

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;

namespace SampleSkills
{
    /// <summary>
    /// Sample custom skill that wraps the Bing entity search API to connect it with a 
    /// AI enrichment pipeline.
    /// </summary>
    public static class BingEntitySearch
    {
        #region Credentials
        // IMPORTANT: Make sure to enter your credential and to verify the API endpoint matches yours.
        static readonly string bingApiEndpoint = "https://api.cognitive.microsoft.com/bing/v7.0/entities/";
        static readonly string key = "<enter your api key here>";  
        #endregion

        #region Class used to deserialize the request
        private class InputRecord
        {
            public class InputRecordData
            {
                public string Name { get; set; }
            }

            public string RecordId { get; set; }
            public InputRecordData Data { get; set; }
        }

        private class WebApiRequest
        {
            public List<InputRecord> Values { get; set; }
        }
        #endregion

        #region Classes used to serialize the response

        private class OutputRecord
        {
            public class OutputRecordData
            {
                public string Name { get; set; } = "";
                public string Description { get; set; } = "";
                public string Source { get; set; } = "";
                public string SourceUrl { get; set; } = "";
                public string LicenseAttribution { get; set; } = "";
                public string LicenseUrl { get; set; } = "";
            }

            public class OutputRecordMessage
            {
                public string Message { get; set; }
            }

            public string RecordId { get; set; }
            public OutputRecordData Data { get; set; }
            public List<OutputRecordMessage> Errors { get; set; }
            public List<OutputRecordMessage> Warnings { get; set; }
        }

        private class WebApiResponse
        {
            public List<OutputRecord> Values { get; set; }
        }
        #endregion

        #region Classes used to interact with the Bing API
        private class BingResponse
        {
            public BingEntities Entities { get; set; }
        }
        private class BingEntities
        {
            public BingEntity[] Value { get; set; }
        }

        private class BingEntity
        {
            public class EntityPresentationinfo
            {
                public string[] EntityTypeHints { get; set; }
            }

            public class License
            {
                public string Url { get; set; }
            }

            public class ContractualRule
            {
                public string _type { get; set; }
                public License License { get; set; }
                public string LicenseNotice { get; set; }
                public string Text { get; set; }
                public string Url { get; set; }
            }

            public ContractualRule[] ContractualRules { get; set; }
            public string Description { get; set; }
            public string Name { get; set; }
            public EntityPresentationinfo EntityPresentationInfo { get; set; }
        }
        #endregion

        #region The Azure Function definition

        [FunctionName("EntitySearch")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Function, "post", Route = null)] HttpRequest req,
            ILogger log)
        {
            log.LogInformation("Entity Search function: C# HTTP trigger function processed a request.");

            var response = new WebApiResponse
            {
                Values = new List<OutputRecord>()
            };

            string requestBody = new StreamReader(req.Body).ReadToEnd();
            var data = JsonConvert.DeserializeObject<WebApiRequest>(requestBody);

            // Do some schema validation
            if (data == null)
            {
                return new BadRequestObjectResult("The request schema does not match expected schema.");
            }
            if (data.Values == null)
            {
                return new BadRequestObjectResult("The request schema does not match expected schema. Could not find values array.");
            }

            // Calculate the response for each value.
            foreach (var record in data.Values)
            {
                if (record == null || record.RecordId == null) continue;

                OutputRecord responseRecord = new OutputRecord
                {
                    RecordId = record.RecordId
                };

                try
                {
                    responseRecord.Data = GetEntityMetadata(record.Data.Name).Result;
                }
                catch (Exception e)
                {
                    // Something bad happened, log the issue.
                    var error = new OutputRecord.OutputRecordMessage
                    {
                        Message = e.Message
                    };

                    responseRecord.Errors = new List<OutputRecord.OutputRecordMessage>
                    {
                        error
                    };
                }
                finally
                {
                    response.Values.Add(responseRecord);
                }
            }

            return (ActionResult)new OkObjectResult(response);
        }

        #endregion

        #region Methods to call the Bing API
        /// <summary>
        /// Gets metadata for a particular entity based on its name using Bing Entity Search
        /// </summary>
        /// <param name="entityName">The name of the entity to extract data for.</param>
        /// <returns>Asynchronous task that returns entity data. </returns>
        private async static Task<OutputRecord.OutputRecordData> GetEntityMetadata(string entityName)
        {
            var uri = bingApiEndpoint + "?q=" + entityName + "&mkt=en-us&count=10&offset=0&safesearch=Moderate";
            var result = new OutputRecord.OutputRecordData();

            using (var client = new HttpClient())
            using (var request = new HttpRequestMessage {
                Method = HttpMethod.Get,
                RequestUri = new Uri(uri)
            })
            {
                request.Headers.Add("Ocp-Apim-Subscription-Key", key);

                HttpResponseMessage response = await client.SendAsync(request);
                string responseBody = await response?.Content?.ReadAsStringAsync();

                BingResponse bingResult = JsonConvert.DeserializeObject<BingResponse>(responseBody);
                if (bingResult != null)
                {
                    // In addition to the list of entities that could match the name, for simplicity let's return information
                    // for the top match as additional metadata at the root object.
                    return AddTopEntityMetadata(bingResult.Entities?.Value);
                }
            }

            return result;
        }

        private static OutputRecord.OutputRecordData AddTopEntityMetadata(BingEntity[] entities)
        {
            if (entities != null)
            {
                foreach (BingEntity entity in entities.Where(
                    entity => entity?.EntityPresentationInfo?.EntityTypeHints != null
                        && (entity.EntityPresentationInfo.EntityTypeHints[0] == "Person"
                            || entity.EntityPresentationInfo.EntityTypeHints[0] == "Organization"
                            || entity.EntityPresentationInfo.EntityTypeHints[0] == "Location")
                        && !String.IsNullOrEmpty(entity.Description)))
                {
                    var rootObject = new OutputRecord.OutputRecordData
                    {
                        Description = entity.Description,
                        Name = entity.Name
                    };

                    if (entity.ContractualRules != null)
                    {
                        foreach (var rule in entity.ContractualRules)
                        {
                            switch (rule._type)
                            {
                                case "ContractualRules/LicenseAttribution":
                                    rootObject.LicenseAttribution = rule.LicenseNotice;
                                    rootObject.LicenseUrl = rule.License.Url;
                                    break;
                                case "ContractualRules/LinkAttribution":
                                    rootObject.Source = rule.Text;
                                    rootObject.SourceUrl = rule.Url;
                                    break;
                            }
                        }
                    }

                    return rootObject;
                }
            }

            return new OutputRecord.OutputRecordData();
        }
        #endregion
    }
}
```

Upewnij się, aby wprowadzić własną `key` wartość *klucza* w stałej na podstawie klucza, który masz podczas rejestracji w interfejsie API wyszukiwania jednostek Bing.

Ten przykład zawiera wszystkie niezbędne kodu w jednym pliku dla wygody. W [repozytorium umiejętności mocy](https://github.com/Azure-Samples/azure-search-power-skills/tree/master/Text/BingEntitySearch)można znaleźć nieco bardziej ustrukturyzowana wersja tej samej umiejętności.

Oczywiście możesz zmienić nazwę pliku `Function1.cs` `BingEntitySearch.cs`z do .

## <a name="test-the-function-from-visual-studio"></a>Testowanie funkcji z programu Visual Studio

Naciśnij **klawisz F5,** aby uruchomić program i przetestować zachowania funkcji. W takim przypadku użyjemy poniższej funkcji, aby wyszukać dwie jednostki. Użyj Listonosza lub Skrzypka, aby wystawić połączenie, takie jak pokazane poniżej:

```http
POST https://localhost:7071/api/EntitySearch
```

### <a name="request-body"></a>Treść żądania
```json
{
    "values": [
        {
            "recordId": "e1",
            "data":
            {
                "name":  "Pablo Picasso"
            }
        },
        {
            "recordId": "e2",
            "data":
            {
                "name":  "Microsoft"
            }
        }
    ]
}
```

### <a name="response"></a>Odpowiedź
Powinna zostać wyświetlna odpowiedź podobna do poniższego przykładu:

```json
{
    "values": [
        {
            "recordId": "e1",
            "data": {
                "name": "Pablo Picasso",
                "description": "Pablo Ruiz Picasso was a Spanish painter [...]",
                "source": "Wikipedia",
                "sourceUrl": "http://en.wikipedia.org/wiki/Pablo_Picasso",
                "licenseAttribution": "Text under CC-BY-SA license",
                "licenseUrl": "http://creativecommons.org/licenses/by-sa/3.0/"
            },
            "errors": null,
            "warnings": null
        },
        "..."
    ]
}
```

## <a name="publish-the-function-to-azure"></a>Publikowanie funkcji na platformie Azure

Jeśli jesteś zadowolony z zachowania funkcji, można go opublikować.

1. W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Opublikuj**. Wybierz pozycję **Utwórz nowy** > **publikowanie**.

1. Jeśli program Visual Studio nie został jeszcze połączony z kontem platformy Azure, wybierz pozycję **Dodaj konto....**

1. Postępuj zgodnie z instrukcjami wyświetlanymi na ekranie. Zostaniesz poproszony o określenie unikatowej nazwy usługi aplikacji, subskrypcji platformy Azure, grupy zasobów, planu hostingu i konta magazynu, którego chcesz użyć. Jeśli jeszcze ich nie masz, możesz utworzyć nową grupę zasobów, nowy plan hostingu i konto magazynu. Po zakończeniu wybierz pozycję **Utwórz**

1. Po zakończeniu wdrażania zwróć uwagę na adres URL witryny. Jest to adres aplikacji funkcji na platformie Azure. 

1. W [witrynie Azure portal](https://portal.azure.com)przejdź do grupy `EntitySearch` zasobów i poszukaj opublikowanej funkcji. W sekcji **Zarządzanie** powinny zostać wyświetlona sekcja Klucze hosta. Wybierz ikonę **Kopiuj** *dla domyślnego* klucza hosta.  

## <a name="test-the-function-in-azure"></a>Testowanie funkcji na platformie Azure

Teraz, gdy masz domyślny klucz hosta, przetestuj swoją funkcję w następujący sposób:

```http
POST https://[your-entity-search-app-name].azurewebsites.net/api/EntitySearch?code=[enter default host key here]
```

### <a name="request-body"></a>Treść żądania
```json
{
    "values": [
        {
            "recordId": "e1",
            "data":
            {
                "name":  "Pablo Picasso"
            }
        },
        {
            "recordId": "e2",
            "data":
            {
                "name":  "Microsoft"
            }
        }
    ]
}
```

W tym przykładzie należy uzyskać taki sam wynik, który widziałeś wcześniej podczas uruchamiania funkcji w środowisku lokalnym.

## <a name="connect-to-your-pipeline"></a>Łączenie się z rurociągiem
Teraz, gdy masz nową umiejętność niestandardową, możesz dodać ją do swojego umiejętności. W poniższym przykładzie pokazano, jak wywołać umiejętności, aby dodać opisy do organizacji w dokumencie (można to rozszerzyć, aby również pracować na lokalizacje i osoby). Zamień `[your-entity-search-app-name]` ją na nazwę aplikacji.

```json
{
    "skills": [
      "[... your existing skills remain here]",  
      {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "Our new Bing entity search custom skill",
        "uri": "https://[your-entity-search-app-name].azurewebsites.net/api/EntitySearch?code=[enter default host key here]",
          "context": "/document/merged_content/organizations/*",
          "inputs": [
            {
              "name": "name",
              "source": "/document/merged_content/organizations/*"
            }
          ],
          "outputs": [
            {
              "name": "description",
              "targetName": "description"
            }
          ]
      }
  ]
}
```

W tym miejscu liczymy na wbudowaną [umiejętność rozpoznawania jednostek,](cognitive-search-skill-entity-recognition.md) która będzie obecna w zestawie umiejętności i wzbogaci dokument o listę organizacji. Aby uzyskać odwołanie, oto konfiguracja umiejętności wyodrębniania jednostek, która byłaby wystarczająca do generowania potrzebnych nam danych:

```json
{
    "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
    "name": "#1",
    "description": "Organization name extraction",
    "context": "/document/merged_content",
    "categories": [ "Organization" ],
    "defaultLanguageCode": "en",
    "inputs": [
        {
            "name": "text",
            "source": "/document/merged_content"
        },
        {
            "name": "languageCode",
            "source": "/document/language"
        }
    ],
    "outputs": [
        {
            "name": "organizations",
            "targetName": "organizations"
        }
    ]
},
```

## <a name="next-steps"></a>Następne kroki
Gratulacje! Stworzyłeś swoją pierwszą umiejętność niestandardową. Teraz możesz wykonać ten sam wzorzec, aby dodać własne funkcje niestandardowe. Kliknij poniższe linki, aby dowiedzieć się więcej.

+ [Power Skills: repozytorium umiejętności niestandardowych](https://github.com/Azure-Samples/azure-search-power-skills)
+ [Dodawanie umiejętności niestandardowych do potoku wzbogacania si.](cognitive-search-custom-skill-interface.md)
+ [Jak zdefiniować zestaw umiejętności](cognitive-search-defining-skillset.md)
+ [Tworzenie skillset (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Jak mapować wzbogacone pola](cognitive-search-output-field-mapping.md)
