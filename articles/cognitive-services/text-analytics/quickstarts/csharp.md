---
title: 'Szybki Start: Przy użyciu języka C# do wywołania interfejsu API analizy tekstu | Dokumentacja firmy Microsoft'
titleSuffix: Azure Cognitive Services
description: Uzyskaj informacje i przykłady kodu, które ułatwią Ci szybkie rozpoczęcie pracy przy użyciu interfejsu API analizy tekstu usług Microsoft Cognitive Services na platformie Azure.
services: cognitive-services
documentationcenter: ''
author: luiscabrer
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: article
ms.date: 08/30/2018
ms.author: ashmaka
ms.openlocfilehash: b4d945b7495897caf1f4edd1e909581614798a23
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/10/2018
ms.locfileid: "44303025"
---
# <a name="quickstart-using-c-to-call-the-text-analytics-cognitive-service"></a>Szybki Start: Przy użyciu języka C# do wywoływania usług Cognitive analizy tekstu
<a name="HOLTop"></a>

W tym artykule pokazano, jak wykrywanie języka, analizowanie tonacji i wyodrębnianie kluczowych fraz przy użyciu [interfejsów API analizy tekstu](//go.microsoft.com/fwlink/?LinkID=759711) przy użyciu języka C#. Ten kod został zapisany do pracy w aplikacji .NET Core, z minimalnym odwołaniami do zewnętrznych bibliotekach, aby można było również uruchomić w systemie Linux lub MacOS.

Zapoznaj się [definicji interfejsu API](//go.microsoft.com/fwlink/?LinkID=759346) dokumentacja techniczna w przypadku interfejsów API.

## <a name="prerequisites"></a>Wymagania wstępne

Konieczne jest posiadanie [konta interfejsu API usług Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) przy użyciu interfejsu API analizy tekstu. Możesz użyć *5000 transakcji miesięcznie w warstwie bezpłatna* aby ukończyć ten przewodnik Szybki Start.

Musisz również posiadać [punktu końcowego i klucza dostępu](../How-tos/text-analytics-how-to-access-key.md) które zostały wygenerowane automatycznie podczas tworzenia konta. 


## <a name="install-the-nuget-sdk-package"></a>Instalowanie pakietu NuGet zestawu SDK
1. Utwórz nowe rozwiązanie konsoli w programie Visual Studio.
1. Kliknij prawym przyciskiem myszy rozwiązanie, a następnie wybierz pozycję **Zarządzaj pakietami NuGet dla rozwiązania**.
1. Wybierz **Uwzględnij wydania wstępne** pole wyboru.
1. Wybierz **Przeglądaj** kartę i wyszukaj **Microsoft.Azure.CognitiveServices.Language**.
1. Wybierz **Microsoft.Azure.CognitiveServices.Language.TextAnalytics** NuGet pakiet i zainstaluj go.

> [!Tip]
> Mimo że można wywołać [punktów końcowych HTTP](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6) bezpośrednio z kodu C#, zestaw SDK Microsoft.Azure.CognitiveServices.Language sprawia, że łatwiej do wywołania tej usługi bez konieczności martwienia się o serializację i deserializację formatu JSON.
>
> Oto przydatne łącza:
> - [Stronę pakietu NuGet zestawu SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics)
> - [Kod zestawu SDK](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/CognitiveServices/dataPlane/Language/TextAnalytics)


## <a name="call-the-text-analytics-api-by-using-the-sdk"></a>Wywołanie interfejsu API analizy tekstu, korzystając z zestawu SDK
1. Zastąp plik Program.cs następującym kodem. Ten program pokazuje możliwości interfejsu API analizy tekstu w trzy sekcje (język wyodrębniania, wyodrębnianie kluczowych fraz i analiza opinii).
1. Zastąp `Ocp-Apim-Subscription-Key` wartość nagłówka przy użyciu klucza dostępu, który jest prawidłowy dla Twojej subskrypcji.
1. Zastąp lokalizację w `Endpoint` do punktu końcowego, który możesz w konkursie. Punkt końcowy można znaleźć zasobu portalu platformy Azure. Punkt końcowy zwykle zaczyna się od "https://[region].api.cognitive.microsoft.com." Na przykład tylko nazwa protokołu i hosta.
1. Uruchom program.

```csharp
using System;
using Microsoft.Azure.CognitiveServices.Language.TextAnalytics;
using Microsoft.Azure.CognitiveServices.Language.TextAnalytics.Models;
using System.Collections.Generic;
using Microsoft.Rest;
using System.Linq;
using System.Net.Http;
using System.Threading;
using System.Threading.Tasks;

namespace ConsoleApp1
{
    class Program
    {
        /// <summary>
        /// Container for subscription credentials. Make sure to enter your valid key.
        /// </summary>
        class ApiKeyServiceClientCredentials : ServiceClientCredentials
        {
            public override Task ProcessHttpRequestAsync(HttpRequestMessage request, CancellationToken cancellationToken)
            {
                request.Headers.Add("Ocp-Apim-Subscription-Key", "ENTER KEY HERE");
                return base.ProcessHttpRequestAsync(request, cancellationToken);
            }
        }

        static void Main(string[] args)
        {

            // Create a client.
            ITextAnalyticsClient client = new TextAnalyticsClient(new ApiKeyServiceClientCredentials())
            {
                Endpoint = "https://westus.api.cognitive.microsoft.com"
            };

            Console.OutputEncoding = System.Text.Encoding.UTF8;
```

## <a name="detect-language"></a>Wykrywanie języka

Interfejs API wykrywania języka wykrywa język tekstu dokumentu, za pomocą [metody wykrywania języka](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7).

```csharp
            // Extracting language.
            Console.WriteLine("===== LANGUAGE EXTRACTION ======");

            var result =  client.DetectLanguageAsync(new BatchInput(
                    new List<Input>()
                        {
                          new Input("1", "This is a document written in English."),
                          new Input("2", "Este es un document escrito en Español."),
                          new Input("3", "这是一个用中文写的文件")
                    })).Result;

            // Printing language results.
            foreach (var document in result.Documents)
            {
                Console.WriteLine("Document ID: {0} , Language: {1}", document.Id, document.DetectedLanguages[0].Name);
            }
```

## <a name="extract-key-phrases"></a>Wyodrębnianie kluczowych fraz

Klucz frazy wyodrębniania wyodrębnia kluczowych fraz z tekstu dokumentu, za pomocą [metoda kluczowych fraz](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6).

```csharp
            // Getting key phrases.
            Console.WriteLine("\n\n===== KEY-PHRASE EXTRACTION ======");

            KeyPhraseBatchResult result2 = client.KeyPhrasesAsync(new MultiLanguageBatchInput(
                        new List<MultiLanguageInput>()
                        {
                          new MultiLanguageInput("ja", "1", "猫は幸せ"),
                          new MultiLanguageInput("de", "2", "Fahrt nach Stuttgart und dann zum Hotel zu Fu."),
                          new MultiLanguageInput("en", "3", "My cat is stiff as a rock."),
                          new MultiLanguageInput("es", "4", "A mi me encanta el fútbol!")
                        })).Result;

            // Printing key phrases.
            foreach (var document in result2.Documents)
            {
                Console.WriteLine("Document ID: {0} ", document.Id);

                Console.WriteLine("\t Key phrases:");

                foreach (string keyphrase in document.KeyPhrases)
                {
                    Console.WriteLine("\t\t" + keyphrase);
                }
            }
```

## <a name="analyze-sentiment"></a>Analiza tonacji

Interfejs API analizy tonacji wykrywa tonację zestaw rekordów tekstowych przy użyciu [metoda tonacji](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c9).

```csharp
            // Analyzing sentiment.
            Console.WriteLine("\n\n===== SENTIMENT ANALYSIS ======");

            SentimentBatchResult result3 = client.SentimentAsync(
                    new MultiLanguageBatchInput(
                        new List<MultiLanguageInput>()
                        {
                          new MultiLanguageInput("en", "0", "I had the best day of my life."),
                          new MultiLanguageInput("en", "1", "This was a waste of my time. The speaker put me to sleep."),
                          new MultiLanguageInput("es", "2", "No tengo dinero ni nada que dar..."),
                          new MultiLanguageInput("it", "3", "L'hotel veneziano era meraviglioso. È un bellissimo pezzo di architettura."),
                        })).Result;


            // Printing sentiment results.
            foreach (var document in result3.Documents)
            {
                Console.WriteLine("Document ID: {0} , Sentiment Score: {1:0.00}", document.Id, document.Score);
            }
```

## <a name="identify-linked-entities"></a>Identyfikowanie połączonych jednostek

Interfejs API Entity Linking identyfikuje dobrze znanych jednostek w tekście dokumentu, za pomocą [łączenie podmiotów metoda](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/5ac4251d5b4ccd1554da7634).

```csharp
            // Linking entities
            Console.WriteLine("\n\n===== ENTITY LINKING ======");

            EntitiesBatchResult result4 = client.EntitiesAsync(
                    new MultiLanguageBatchInput(
                        new List<MultiLanguageInput>()
                        {
                            new MultiLanguageInput("en", "0", "I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable."),
                            new MultiLanguageInput("en", "1", "The Seattle Seahawks won the Super Bowl in 2014."),
                        })).Result;

            // Printing entity results.
            foreach (var document in result4.Documents)
            {
                Console.WriteLine("Document ID: {0} , Entities: {1}", document.Id, String.Join(", ", document.Entities.Select(entity => entity.Name)));
            }
        }
    }
}
```

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Analiza tekstu przy użyciu usługi Power BI](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>Zobacz także 

 [Omówienie analizy tekstu](../overview.md)  
 [Często zadawane pytania](../text-analytics-resource-faq.md)
