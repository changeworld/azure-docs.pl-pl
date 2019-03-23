---
title: 'Szybki start: wywoływanie interfejsu API analizy tekstu przy użyciu języka C#'
titleSuffix: Azure Cognitive Services
description: Uzyskaj informacje oraz przykłady kodu w celu szybkiego rozpoczęcia korzystania z interfejsu API analizy tekstu.
services: cognitive-services
author: ashmaka
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 01/02/2019
ms.author: assafi
ms.openlocfilehash: e960f662fda4272bbc9763eb04fdb739c4776af8
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2019
ms.locfileid: "58371336"
---
# <a name="quickstart-using-c-to-call-the-text-analytics-cognitive-service"></a>Szybki start: wywoływanie analizy tekstu usługi Cognitive Service przy użyciu języka C#
<a name="HOLTop"></a>

W tym artykule opisano, jak wykrywać język, analizować tonację i wyodrębniać kluczowe frazy przy użyciu [interfejsów API analizy tekstu](//go.microsoft.com/fwlink/?LinkID=759711) i języka C#. Ten kod został zapisany do pracy w aplikacji .NET Core, z minimalnym odwołaniami do zewnętrznych bibliotekach, dzięki czemu można ją również uruchomić w systemie Linux lub MacOS.

Zapoznaj się z tematem [API definitions (Definicje interfejsu API)](//go.microsoft.com/fwlink/?LinkID=759346), zawierającym dokumentację techniczną interfejsów API.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

Musisz również mieć [punkt końcowy i klucz dostępu](../How-tos/text-analytics-how-to-access-key.md) wygenerowany dla Ciebie podczas tworzenia konta.

## <a name="install-the-nuget-sdk-package"></a>Instalowanie pakietu zestawu SDK NuGet
1. Utwórz nowe rozwiązanie konsolowe w programie Visual Studio.
1. Kliknij rozwiązanie prawym przyciskiem myszy, a następnie kliknij pozycję **Zarządzaj pakietami NuGet dla rozwiązania**.
1. Zaznacz pole wyboru **Uwzględnij wersję wstępną**.
1. Wybierz kartę **Przeglądaj** i wyszukaj ciąg **Microsoft.Azure.CognitiveServices.Language.TextAnalytics**
1. Wybierz pakiet NuGet i zainstaluj go. Może być konieczne zmiany na starszą v2.8.0 teraz (ponieważ są z 3-18-2019 r) do momentu przykładowy kod jest aktualizowana 3.0.0.

> [!Tip]
>  [Punkty końcowe HTTP](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6) można wywołać bezpośrednio z języka C#, ale zestaw SDK Microsoft.Azure.CognitiveServices.Language znacznie ułatwia wywołanie usługi bez martwienia się o serializację i deserializację danych JSON.
>
> Kilka przydatnych linków:
> - [Strona zestawu SDK Nuget](<https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics>)
> - [Kod zestawu SDK](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/CognitiveServices/dataPlane/Language/TextAnalytics)

## <a name="call-the-text-analytics-api-using-the-sdk"></a>Wywoływanie interfejsu API analizy tekstu przy użyciu zestawu SDK

1. Zastąp plik Program.cs kodem przedstawionym poniżej. Ten program pokazuje możliwości interfejsu API analizy tekstu w trzech sekcjach (wyodrębnianie języka, wyodrębnianie kluczowych fraz i analiza tonacji).
1. Zastąp wartość nagłówka `Ocp-Apim-Subscription-Key` kluczem dostępu właściwym dla Twojej subskrypcji.
1. Zastąp regionu w `Endpoint`. Punkt końcowy usługi można znaleźć w sekcji Przegląd zasobu analizy tekstu w [witryny Azure portal](<https://ms.portal.azure.com>). Uwzględnić tylko ta część punktu końcowego usługi: "https://[region].api.cognitive.microsoft.com".
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
        private const string SubscriptionKey = ""; //Insert your Text Anaytics subscription key

        /// </summary>
        class ApiKeyServiceClientCredentials : ServiceClientCredentials
        {
            public override Task ProcessHttpRequestAsync(HttpRequestMessage request, CancellationToken cancellationToken)
            {
                request.Headers.Add("Ocp-Apim-Subscription-Key", SubscriptionKey);
                return base.ProcessHttpRequestAsync(request, cancellationToken);
            }
        }

        static void Main(string[] args)
        {

            // Create a client.
            ITextAnalyticsClient client = new TextAnalyticsClient(new ApiKeyServiceClientCredentials())
            {
                Endpoint = "https://westus.api.cognitive.microsoft.com"
            }; //Replace 'westus' with the correct region for your Text Analytics subscription

            Console.OutputEncoding = System.Text.Encoding.UTF8;

            // Extracting language
            Console.WriteLine("===== LANGUAGE EXTRACTION ======");

            var result = client.DetectLanguageAsync(new BatchInput(
                    new List<Input>()
                        {
                          new Input("1", "This is a document written in English."),
                          new Input("2", "Este es un document escrito en Español."),
                          new Input("3", "这是一个用中文写的文件")
                    })).Result;

            // Printing language results.
            foreach (var document in result.Documents)
            {
                Console.WriteLine($"Document ID: {document.Id} , Language: {document.DetectedLanguages[0].Name}");
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
                Console.WriteLine($"Document ID: {document.Id} ");

                Console.WriteLine("\t Key phrases:");

                foreach (string keyphrase in document.KeyPhrases)
                {
                    Console.WriteLine($"\t\t{keyphrase}");
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
                Console.WriteLine($"Document ID: {document.Id} , Sentiment Score: {document.Score:0.00}");
            }


            // Identify entities
            Console.WriteLine("\n\n===== ENTITIES ======");

            EntitiesBatchResultV2dot1 result4 = client.EntitiesAsync(
                    new MultiLanguageBatchInput(
                        new List<MultiLanguageInput>()
                        {
                          new MultiLanguageInput("en", "0", "The Great Depression began in 1929. By 1933, the GDP in America fell by 25%.")
                        })).Result;

            // Printing entities results
            foreach (var document in result4.Documents)
            {
                Console.WriteLine($"Document ID: {document.Id} ");

                Console.WriteLine("\t Entities:");

                foreach (EntityRecordV2dot1 entity in document.Entities)
                {
                    Console.WriteLine($"\t\t{entity.Name}\t\t{entity.WikipediaUrl}\t\t{entity.Type}\t\t{entity.SubType}");
                }
            }

            Console.ReadLine();
        }
    }
}
```

## <a name="application-output"></a>Dane wyjściowe aplikacji

Aplikacja wyświetla następujące informacje:

```console
===== LANGUAGE EXTRACTION ======
Document ID: 1 , Language: English
Document ID: 2 , Language: Spanish
Document ID: 3 , Language: Chinese_Simplified


===== KEY-PHRASE EXTRACTION ======
Document ID: 1
         Key phrases:
                幸せ
Document ID: 2
         Key phrases:
                Stuttgart
                Hotel
Document ID: 3
         Key phrases:
                cat
                rock
Document ID: 4
         Key phrases:
                fútbol

===== SENTIMENT ANALYSIS ======
Document ID: 0 , Sentiment Score: 0.87
Document ID: 1 , Sentiment Score: 0.11
Document ID: 2 , Sentiment Score: 0.44
Document ID: 3 , Sentiment Score: 1.00
```

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Analiza tekstu przy użyciu usługi Power BI](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>Zobacz także

 [Omówienie analizy tekstu](../overview.md) [Frequently asked questions (FAQ) (Często zadawane pytania (FAQ))](../text-analytics-resource-faq.md)

