---
title: 'Przykład: Tworzenie niestandardowych umiejętności cognitive za pomocą API wyszukiwania jednostek Bing — usługa Azure Search'
description: Demonstruje użycie usługi wyszukiwania jednostek Bing w umiejętności przy użyciu niestandardowych mapowane do potoku indeksowania wyszukiwania kognitywnego w usłudze Azure Search.
manager: pablocas
author: luiscabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 7d90f46ada9b9453b4c1516a4a898456dc73b8e7
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672142"
---
# <a name="example-create-a-custom-skill-using-the-bing-entity-search-api"></a>Przykład: Tworzenie niestandardowych umiejętności, przy użyciu interfejsu API wyszukiwania jednostek Bing

W tym przykładzie Dowiedz się, jak utworzyć umiejętności niestandardowego interfejsu API sieci web. To umiejętności Zaakceptuj lokalizacje, osób publicznych i organizacje i zwracać ich opisy. W przykładzie użyto [funkcji platformy Azure](https://azure.microsoft.com/services/functions/) opakowywać [interfejs API wyszukiwania jednostek Bing](https://azure.microsoft.com/services/cognitive-services/bing-entity-search-api/) tak, aby go implementuje interfejs umiejętności niestandardowe.

## <a name="prerequisites"></a>Wymagania wstępne

+ Przeczytaj o [interfejsu umiejętności niestandardowe](cognitive-search-custom-skill-interface.md) artykułu, jeśli nie jesteś zaznajomiony z interfejsem wejścia/wyjścia, która powinna implementować niestandardowe umiejętności.

+ [!INCLUDE [cognitive-services-bing-entity-search-signup-requirements](../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

+ Zainstaluj [Visual Studio 2019](https://www.visualstudio.com/vs/) lub później, np. obciążenie programowanie na platformie Azure.

## <a name="create-an-azure-function"></a>Tworzenie funkcji platformy Azure

Chociaż ten przykład używa funkcji platformy Azure do hostowania interfejsu API sieci web, nie jest to wymagane.  Tak długo, jak spełniasz [interfejsu wymagania dotyczące cognitive umiejętności](cognitive-search-custom-skill-interface.md), podejścia jest bez znaczenia. Usługa Azure Functions, jednak ułatwiają tworzenie umiejętności niestandardowe.

### <a name="create-a-function-app"></a>Tworzenie aplikacji funkcji

1. W programie Visual Studio, wybierz **New** > **projektu** za pomocą menu Plik.

1. W oknie dialogowym Nowy projekt, wybierz **zainstalowane**, rozwiń węzeł **Visual C#**  > **chmury**, wybierz opcję **usługi Azure Functions**, wpisz Nazwa projektu, a następnie wybierz pozycję **OK**. Nazwa aplikacji funkcji musi być prawidłową nazwą C# przestrzeni nazw, dlatego nie należy używać znaków podkreślenia, łączników ani inne znaki inne niż alfanumeryczne.

1. Wybierz **usługi Azure Functions w wersji 2 (.NET Core)** . Można też zrobić to z wersją 1, ale kod napisany poniżej jest oparty na szablonie v2.

1. Wybierz typ jako **wyzwalacz HTTP**

1. Dla konta magazynu można wybrać **Brak**, ponieważ nie ma potrzeby każdy magazyn dla tej funkcji.

1. Wybierz **OK** można utworzyć funkcji w projekcie i HTTP funkcja wyzwalana przez.

### <a name="modify-the-code-to-call-the-bing-entity-search-service"></a>Zmodyfikuj kod do wywołania tej usługi wyszukiwania jednostek Bing

Program Visual Studio tworzy projekt, a w nim klasę zawierającą standardowy kod dla wybranego typu funkcji. Atrybut *FunctionName* metody ustawia nazwę funkcji. Atrybut *HttpTrigger* określa, że funkcja jest wyzwalana przez żądanie HTTP.

Teraz Zastąp całą zawartość pliku *Function1.cs* następującym kodem:

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
    /// cognitive search pipeline.
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

Upewnij się wprowadzić własne *klucz* wartość w `key` stałą na podstawie klucza stało się podczas tworzenia API wyszukiwania jednostek Bing.

W tym przykładzie zawiera wszystkie niezbędne kod w jednym pliku dla wygody. Można znaleźć nieco bardziej ustrukturyzowane wersji tego samego umiejętności w [repozytorium umiejętności power](https://github.com/Azure-Samples/azure-search-power-skills/tree/master/Text/BingEntitySearch).

Oczywiście, może zmienić nazwy pliku z `Function1.cs` do `BingEntitySearch.cs`.

## <a name="test-the-function-from-visual-studio"></a>Testowanie funkcji w programie Visual Studio

Naciśnij klawisz **F5** Aby uruchomić program i testowanie zachowania funkcji. W tym przypadku użyjemy funkcji poniżej do wyszukania dwie jednostki. Użyj narzędzia Postman lub Fiddler do wysyłania wywołania, tak jak pokazano poniżej:

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
Powinny pojawić się odpowiedź podobna do poniższego przykładu:

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

Gdy jesteś zadowolony z zachowanie funkcji, możesz opublikować go.

1. W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Opublikuj**. Wybierz **tworzenia nowych** > **publikowania**.

1. Jeśli nie zostało jeszcze połączone programu Visual Studio do konta platformy Azure, wybierz opcję **Dodaj konto...**

1. Postępuj zgodnie z monitami wyświetlanymi na ekranie. Użytkownik jest proszony o Określ unikatową nazwę dla usługi app service, subskrypcja platformy Azure, grupę zasobów, plan hostingu i konto magazynu, którego chcesz użyć. Jeśli nie masz jeszcze te można utworzyć nową grupę zasobów, nowy plan hostingu i konta magazynu. Po zakończeniu wybierz pozycję **Create**

1. Po zakończeniu wdrożenia należy zauważyć, adres URL witryny. Jest adresem aplikacji funkcji na platformie Azure. 

1. W [witryny Azure portal](https://portal.azure.com), przejdź do grupy zasobów i poszukaj `EntitySearch` funkcja została opublikowana. W obszarze **Zarządzaj** sekcji, powinien zostać wyświetlony klucze hosta. Wybierz **kopiowania** ikonę *domyślne* klucz hosta.  

## <a name="test-the-function-in-azure"></a>Testowanie funkcji na platformie Azure

Teraz, gdy domyślny klucz hosta, testowanie funkcji w następujący sposób:

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

W tym przykładzie należy utworzyć ten sam wynik, które zostały wcześniej użyte podczas działania funkcji w środowisku lokalnym.

## <a name="connect-to-your-pipeline"></a>Nawiązać połączenie z potokiem
Teraz, gdy masz nowych umiejętności niestandardowe, można dodać go do Twojego zestawu umiejętności. W poniższym przykładzie przedstawiono sposób wywoływania umiejętności, aby dodać opis do organizacji w dokumencie (to może zostać rozszerzony do również działają na lokalizacje i osób). Zastąp `[your-entity-search-app-name]` nazwą swojej aplikacji.

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

W tym miejscu jest firma Microsoft zliczanie na wbudowane [umiejętności rozpoznawania jednostek](cognitive-search-skill-entity-recognition.md) znajdować się w zestawu umiejętności oraz mają wzbogacony dokumentu z listy w organizacji. Odwołanie Oto konfiguracji umiejętności wyodrębniania jednostki, które będą wystarczające do generowania danych, której potrzebujesz:

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
Gratulacje! Utworzono Twojego pierwszego enricher niestandardowych. Teraz można wykonać tego samego wzorca, aby dodać własne niestandardowe funkcje. 

+ [Dodaj umiejętności niestandardowe do potoku wyszukiwania kognitywnego](cognitive-search-custom-skill-interface.md)
+ [Jak Definiowanie zestawu umiejętności](cognitive-search-defining-skillset.md)
+ [Tworzenie zestawu umiejętności (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Sposób mapowania pól wzbogacony](cognitive-search-output-field-mapping.md)
