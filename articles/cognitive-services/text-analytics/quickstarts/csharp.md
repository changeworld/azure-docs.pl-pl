---
title: C# szybkiego startu dla usług Azure kognitywnych, analiza tekstu interfejsu API | Dokumentacja firmy Microsoft
description: Pobierz informacje i przykładowy kod w celu szybkiego Rozpoczynanie pracy przy użyciu interfejsu API z analizy tekstu w kognitywnych usług Microsoft Azure.
services: cognitive-services
documentationcenter: ''
author: luiscabrer
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: article
ms.date: 09/20/2017
ms.author: ashmaka
ms.openlocfilehash: d9c61a83450844461f621ff16354881a029f7ad6
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36266298"
---
# <a name="quickstart-for-text-analytics-api-with-c"></a>Szybki Start dla interfejsu API w języku C#. Analiza tekstu 
<a name="HOLTop"></a>

W tym artykule przedstawiono sposób wykrywania języka, analizowanie wskaźniki nastrojów klientów, a następnie Wyodrębnij fraz klucza przy użyciu [interfejsów API Analytics tekst](//go.microsoft.com/fwlink/?LinkID=759711) języku C#. Ten kod został zapisany do pracy w .net Core aplikacji, z minimalnym odwołania do bibliotek zewnętrznych, więc można go również uruchomić w systemie Linux lub MacOS.

Zapoznaj się [definicji interfejsu API](//go.microsoft.com/fwlink/?LinkID=759346) dokumentacja techniczna dla interfejsów API.

## <a name="prerequisites"></a>Wymagania wstępne

Musi mieć [kognitywnych interfejsu API usług konta](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) z **interfejsu API z analizy tekstu**. Można użyć **warstwę bezpłatna do 5000 miesięcznie transakcji** do ukończenia tego przewodnika Szybki Start.

Musi mieć również [punktu końcowego i klucz dostępu](../How-tos/text-analytics-how-to-access-key.md) wygenerowany automatycznie podczas logowania się. 


## <a name="install-the-nuget-sdk-package"></a>Zainstaluj pakiet Nuget SDK
1. Utwórz nowe rozwiązanie konsoli w programie Visual Studio.
1. Kliknij prawym przyciskiem myszy rozwiązanie i kliknij przycisk **Zarządzaj pakietami NuGet dla rozwiązania**
1. Oznacz **Uwzględnij wydanie wstępne** wyboru.
1. Wybierz **Przeglądaj** , a następnie wyszukaj **Microsoft.Azure.CognitiveServices.Language**
1. Wybierz pakiet Nuget, a następnie zainstaluj go.

> [!Tip]
>  Podczas gdy można wywołać [punktów końcowych HTTP](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6) bezpośrednio w języku C#, zestaw SDK Microsoft.Azure.CognitiveServices.Language ułatwia do wywołania tej usługi, nie martwiąc się o serializację i deserializację formatu JSON.
>
> Kilka przydatne łącza:
> - [Strona Nuget zestawu SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics)
> - [Kod zestawu SDK ](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/CognitiveServices/dataPlane/Language/TextAnalytics)


## <a name="call-the-text-analytics-api-using-the-sdk"></a>Wywołanie interfejsu API z analizy tekstu, przy użyciu zestawu SDK
1. Zastąp plik Program.cs kodem poniżej. Ten program pokazuje możliwości interfejsu API analizy tekstu w sekcjach 3 (wyodrębniania języka, wyodrębniania frazy klucza i wskaźniki nastrojów klientów analizy).
1. Zastąp `Ocp-Apim-Subscription-Key` wartość nagłówka z kluczem dostępu prawidłową dla Twojej subskrypcji.
1. Zmienić lokalizację w `client.AzureRegion` (obecnie `AzureRegions.Westus`) w regionie zalogowano się.
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
            ITextAnalyticsAPI client = new TextAnalyticsAPI(new ApiKeyServiceClientCredentials());
            client.AzureRegion = AzureRegions.Westus;

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

 [Omówienie Analiza tekstu](../overview.md)  
 [Często zadawane pytania (FAQ)](../text-analytics-resource-faq.md)

