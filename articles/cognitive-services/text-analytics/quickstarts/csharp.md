---
title: Przewodnik Szybki Start języka C# dla usługi Azure Cognitive Services, interfejs API analizy tekstu | Dokumentacja firmy Microsoft
description: Pobierz informacje oraz przykłady kodu w celu szybkiego Rozpocznij pracę przy użyciu interfejsu API analizy tekstu usług Microsoft Cognitive Services na platformie Azure.
services: cognitive-services
documentationcenter: ''
author: luiscabrer
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: article
ms.date: 09/20/2017
ms.author: ashmaka
ms.openlocfilehash: 94847adf761652a25fd3e2d594c7169776fefc89
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/18/2018
ms.locfileid: "39125129"
---
# <a name="quickstart-for-text-analytics-api-with-c"></a>Przewodnik Szybki start dotyczący interfejsu API w języku C# analizy tekstu 
<a name="HOLTop"></a>

W tym artykule pokazano, jak wykrywanie języka, analizowanie tonacji i wyodrębnianie kluczowych fraz przy użyciu [interfejsów API analizy tekstu](//go.microsoft.com/fwlink/?LinkID=759711) przy użyciu języka C#. Ten kod został zapisany do pracy nad.Net Core aplikacji, z minimalnym odwołaniami do zewnętrznych bibliotekach, dzięki czemu można ją również uruchomić w systemie Linux lub MacOS.

Zapoznaj się [definicji interfejsu API](//go.microsoft.com/fwlink/?LinkID=759346) dokumentacja techniczna w przypadku interfejsów API.

## <a name="prerequisites"></a>Wymagania wstępne

Konieczne jest posiadanie [konta interfejsu API usług Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) z **interfejsu API analizy tekstu**. Możesz użyć **5000 transakcji miesięcznie w warstwie bezpłatna** aby ukończyć ten przewodnik Szybki Start.

Musisz również posiadać [punktu końcowego i klucza dostępu](../How-tos/text-analytics-how-to-access-key.md) wygenerowany dla Ciebie podczas logowania się. 


## <a name="install-the-nuget-sdk-package"></a>Instalowanie pakietu Nuget zestawu SDK
1. Utwórz nowe rozwiązanie konsoli w programie Visual Studio.
1. Kliknij prawym przyciskiem myszy rozwiązanie i kliknij przycisk **Zarządzaj pakietami NuGet dla rozwiązania**
1. Oznacz **Uwzględnij wydania wstępne** pola wyboru.
1. Wybierz **Przeglądaj** kartę i wyszukaj **Microsoft.Azure.CognitiveServices.Language**
1. Wybierz pakiet Nuget i zainstaluj go.

> [!Tip]
>  Chociaż można wywołać [punktów końcowych HTTP](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6) bezpośrednio z kodu C#, zestaw SDK Microsoft.Azure.CognitiveServices.Language sprawia, że łatwiej do wywołania tej usługi bez konieczności martwienia się o serializację i deserializację formatu JSON.
>
> Kilka przydatne linki:
> - [Stronę pakietu Nuget zestawu SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics)
> - [Kod zestawu SDK ](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/CognitiveServices/dataPlane/Language/TextAnalytics)


## <a name="call-the-text-analytics-api-using-the-sdk"></a>Wywołanie interfejsu API analizy tekstu przy użyciu zestawu SDK
1. Zastąp plik Program.cs kodem przedstawione poniżej. Ten program pokazuje możliwości interfejsu API analizy tekstu w sekcjach 3 (język wyodrębniania, wyodrębnianie kluczowych fraz i analiza opinii).
1. Zastąp `Ocp-Apim-Subscription-Key` wartość nagłówka z prawidłowy klucz dostępu dla Twojej subskrypcji.
1. Zastąp lokalizację w `client.BaseUri` do punktu końcowego, możesz w konkursie. Punkt końcowy znajdziesz w zasobie witryny Azure Portal. Punkt końcowy jest zwykle wygląda jak "https://[region].api.cognitive.microsoft.com/text/analytics/v2.0".
1. Uruchom program.

```csharp
using System;
using Microsoft.Azure.CognitiveServices.Language.TextAnalytics;
using Microsoft.Azure.CognitiveServices.Language.TextAnalytics.Models;
using System.Collections.Generic;
using Microsoft.Rest;
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
            ITextAnalyticsClient client = new TextAnalyticsClient(new ApiKeyServiceClientCredentials());
            client.BaseUri = new Uri("https://westus.api.cognitive.microsoft.com/text/analytics/v2.0");

            Console.OutputEncoding = System.Text.Encoding.UTF8;

            // Extracting language
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

            // Getting key-phrases
            Console.WriteLine("\n\n===== KEY-PHRASE EXTRACTION ======");

            KeyPhraseBatchResult result2 = client.KeyPhrasesAsync(new MultiLanguageBatchInput(
                        new List<MultiLanguageInput>()
                        {
                          new MultiLanguageInput("ja", "1", "猫は幸せ"),
                          new MultiLanguageInput("de", "2", "Fahrt nach Stuttgart und dann zum Hotel zu Fu."),
                          new MultiLanguageInput("en", "3", "My cat is stiff as a rock."),
                          new MultiLanguageInput("es", "4", "A mi me encanta el fútbol!")
                        })).Result;

            // Printing keyphrases
            foreach (var document in result2.Documents)
            {
                Console.WriteLine("Document ID: {0} ", document.Id);

                Console.WriteLine("\t Key phrases:");

                foreach (string keyphrase in document.KeyPhrases)
                {
                    Console.WriteLine("\t\t" + keyphrase);
                }
            }

            // Extracting sentiment
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


            // Printing sentiment results
            foreach (var document in result3.Documents)
            {
                Console.WriteLine("Document ID: {0} , Sentiment Score: {1:0.00}", document.Id, document.Score);
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
 [Często zadawane pytania (FAQ)](../text-analytics-resource-faq.md)

