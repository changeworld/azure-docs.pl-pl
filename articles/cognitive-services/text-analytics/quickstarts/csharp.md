---
title: 'Szybki start: Wywoływanie usługi Analiza tekstu przy użyciu zestawu Azure SDK dla platformy .NET iC#'
titleSuffix: Azure Cognitive Services
description: Informacje oraz przykłady kodu ułatwiające rozpoczęcie pracy przy użyciu usługi Analiza tekstu i C#.
services: cognitive-services
author: raymondl
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 05/28/2019
ms.author: assafi
ms.openlocfilehash: 82297842a56930cec2b4de90998b4ffb904543bb
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446964"
---
# <a name="quickstart-use-the-net-sdk-and-c-to-call-the-text-analytics-service"></a>Szybki start: Korzystanie z zestawu SDK platformy .NET i C# do wywołania usługi analizy tekstu
<a name="HOLTop"></a>

Ten przewodnik Szybki Start pomoże Ci rozpocząć korzystanie z zestawu Azure SDK dla platformy .NET i C# do analizowania języka. Mimo że [analizy tekstu](//go.microsoft.com/fwlink/?LinkID=759711) interfejsu API REST jest zgodny z większość języków programowania, zestaw SDK udostępnia łatwy sposób zintegrować usługę ze swoimi aplikacjami.

> [!NOTE]
> Kod źródłowy tego przykładu jest dostępny w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/samples/TextAnalytics).

Szczegóły techniczne można znaleźć w zestawie SDK dla platformy .NET [odwoływać się do analizy tekstu](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/textanalytics?view=azure-dotnet).

## <a name="prerequisites"></a>Wymagania wstępne

* W każdej wersji programu [visual studio 2017 r. lub nowszej]
* Analiza tekstu [SDK dla platformy .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics)

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

Należy również [punktu końcowego i klucza dostępu](../How-tos/text-analytics-how-to-access-key.md) wygenerowany dla Ciebie podczas tworzenia konta.

## <a name="create-the-visual-studio-solution-and-install-the-sdk"></a>Tworzenie rozwiązań programu Visual Studio i zainstaluj zestaw SDK

1. Utwórz nowy projekt aplikacji (.NET Core) konsolę. [Access Visual Studio](https://visualstudio.microsoft.com/vs/).
1. Kliknij prawym przyciskiem myszy rozwiązanie, a następnie wybierz pozycję **Zarządzaj pakietami NuGet dla rozwiązania**.
1. Wybierz kartę **Przeglądaj**. Search for **Microsoft.Azure.CognitiveServices.Language.TextAnalytics**.

## <a name="authenticate-your-credentials"></a>Uwierzytelnienia poświadczeń użytkownika

1. Dodaj następujący kod `using` instrukcje do pliku klasy głównej (czyli Program.cs domyślnie).

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Net.Http;
    using System.Threading;
    using System.Threading.Tasks;

    using Microsoft.Azure.CognitiveServices.Language.TextAnalytics;
    using Microsoft.Azure.CognitiveServices.Language.TextAnalytics.Models;
    using Microsoft.Rest;
    ```

2. Utwórz nową `ApiKeyServiceClientCredentials` klasy do przechowywania poświadczeń, a następnie dodać je dla każdego żądania.

    ```csharp
    /// <summary>
    /// Allows authentication to the API by using a basic apiKey mechanism
    /// </summary>
    class ApiKeyServiceClientCredentials : ServiceClientCredentials
    {
        private readonly string subscriptionKey;

        /// <summary>
        /// Creates a new instance of the ApiKeyServiceClientCredentails class
        /// </summary>
        /// <param name="subscriptionKey">The subscription key to authenticate and authorize as</param>
        public ApiKeyServiceClientCredentials(string subscriptionKey)
        {
            this.subscriptionKey = subscriptionKey;
        }

        /// <summary>
        /// Add the Basic Authentication Header to each outgoing request
        /// </summary>
        /// <param name="request">The outgoing request</param>
        /// <param name="cancellationToken">A token to cancel the operation</param>
        public override Task ProcessHttpRequestAsync(HttpRequestMessage request, CancellationToken cancellationToken)
        {
            if (request == null)
            {
                throw new ArgumentNullException("request");
            }

            request.Headers.Add("Ocp-Apim-Subscription-Key", this.subscriptionKey);
            return base.ProcessHttpRequestAsync(request, cancellationToken);
        }
    }
    ```

3. Aktualizacja `Program` klasy. Dodaj stałe elementy członkowskie związane z kluczem subskrypcji analizy tekstu i inny wpis dla punktu końcowego usługi. Pamiętaj, aby użyć poprawny region platformy Azure dla Twojej subskrypcji analizy tekstu.

    ```csharp
    private const string SubscriptionKey = "enter-your-key-here";

    private const string Endpoint = "enter-your-service-endpoint-here"; // For example: "https://westus.api.cognitive.microsoft.com";
    ```
> [!Tip]
> Aby zwiększyć bezpieczeństwo kluczy tajnych w systemach produkcyjnych, firma Microsoft zaleca, możesz użyć [usługi Azure Key Vault](https://docs.microsoft.com/azure/key-vault/quick-create-net).
>

## <a name="create-a-text-analytics-client"></a>Tworzenie klienta analizy tekstu

W `Main` funkcji projektu, należy wywołać przykładowa metoda, którą chcesz wywołać. Przekaż `Endpoint` i `SubscriptionKey` parametry, które zostały zdefiniowane.

```csharp
    public static void Main(string[] args)
    {
        var credentials = new ApiKeyServiceClientCredentials(SubscriptionKey);
        var client = new TextAnalyticsClient(credentials)
        {
            Endpoint = Endpoint
        };

        // Change the console encoding to display non-ASCII characters.
        Console.OutputEncoding = System.Text.Encoding.UTF8;
        SentimentAnalysisExample(client).Wait();
        // DetectLanguageExample(client).Wait();
        // RecognizeEntitiesExample(client).Wait();
        // KeyPhraseExtractionExample(client).Wait();
        Console.ReadLine();
    }
```

Poniżej opisano sposób wywołania każdej funkcji usługi.

## <a name="perform-sentiment-analysis"></a>Wykonywanie analizy tonacji

1. Utwórz nową funkcję `SentimentAnalysisExample()` przyjmującej klienta, który został utworzony wcześniej.
2. Generowanie listy `MultiLanguageInput` obiektów, które zawiera dokumenty, które mają być analizowane.

    ```csharp
    public static async Task SentimentAnalysisExample(TextAnalyticsClient client)
    {
        // The documents to be analyzed. Add the language of the document. The ID can be any value.
        var inputDocuments = new MultiLanguageBatchInput(
            new List<MultiLanguageInput>
            {
                new MultiLanguageInput("en", "1", "I had the best day of my life."),
                new MultiLanguageInput("en", "2", "This was a waste of my time. The speaker put me to sleep."),
                new MultiLanguageInput("es", "3", "No tengo dinero ni nada que dar..."),
                new MultiLanguageInput("it", "4", "L'hotel veneziano era meraviglioso. È un bellissimo pezzo di architettura."),
            });
        //...
    }
    ```

3. W tej samej funkcji, należy wywołać `client.SentimentAsync()` i uzyskać wynik. Następnie iteracyjnego przeglądania wyników. Wydrukuj wynik każdego dokumentu Identyfikator i wskaźniki nastrojów klientów. Podczas oceny, który jest bliżej 1 oznacza pozytywną tonację, wynik, który znajduje się w pobliżu 0 wskazuje negatywną tonację.

    ```csharp
    var result = await client.SentimentAsync(false, inputDocuments);

    // Printing sentiment results
    foreach (var document in result.Documents)
    {
        Console.WriteLine($"Document ID: {document.Id} , Sentiment Score: {document.Score:0.00}");
    }
    ```

### <a name="output"></a>Output

```console
Document ID: 1 , Sentiment Score: 0.87
Document ID: 2 , Sentiment Score: 0.11
Document ID: 3 , Sentiment Score: 0.44
Document ID: 4 , Sentiment Score: 1.00
```

## <a name="perform-language-detection"></a>Wykrywanie języka

1. Utwórz nową funkcję `DetectLanguageExample()` przyjmującej klienta, który został utworzony wcześniej.
2. Generowanie listy `LanguageInput` obiektów, które zawierają swoje dokumenty.

    ```csharp
    public static async Task DetectLanguageExample(TextAnalyticsClient client)
    {

        // The documents to be submitted for language detection. The ID can be any value.
        var inputDocuments = new LanguageBatchInput(
                new List<LanguageInput>
                    {
                        new LanguageInput(id: "1", text: "This is a document written in English."),
                        new LanguageInput(id: "2", text: "Este es un document escrito en Español."),
                        new LanguageInput(id: "3", text: "这是一个用中文写的文件")
                    });
        //...
    }
    ```

3. W tej samej funkcji, należy wywołać `client.DetectLanguageAsync()` i uzyskać wynik. Następnie iteracyjnego przeglądania wyników. Drukowanie identyfikator każdego dokumentu i zwrócony pierwszy język.

    ```csharp
    var langResults = await client.DetectLanguageAsync(false, inputDocuments);

    // Printing detected languages
    foreach (var document in langResults.Documents)
    {
        Console.WriteLine($"Document ID: {document.Id} , Language: {document.DetectedLanguages[0].Name}");
    }
    ```

### <a name="output"></a>Output

```console
===== LANGUAGE EXTRACTION ======
Document ID: 1 , Language: English
Document ID: 2 , Language: Spanish
Document ID: 3 , Language: Chinese_Simplified
```

## <a name="perform-entity-recognition"></a>Wykonać rozpoznawanie jednostek

1. Utwórz nową funkcję `RecognizeEntitiesExample()` przyjmującej klienta, który został utworzony wcześniej.
2. Generowanie listy `MultiLanguageBatchInput` obiektów, które zawierają swoje dokumenty.

    ```csharp
    public static async Task RecognizeEntitiesExample(TextAnalyticsClient client)
    {

        // The documents to be submitted for entity recognition. The ID can be any value.
        var inputDocuments = new MultiLanguageBatchInput(
            new List<MultiLanguageInput>
            {
                new MultiLanguageInput("en", "1", "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800."),
                new MultiLanguageInput("es", "2", "La sede principal de Microsoft se encuentra en la ciudad de Redmond, a 21 kilómetros de Seattle.")
            });
        //...
    }
    ```

3. W tej samej funkcji, należy wywołać `client.EntitiesAsync()` i uzyskać wynik. Następnie iteracyjnego przeglądania wyników. Drukuj identyfikator każdego dokumentu. Dla każdej jednostki wykryte należy wydrukować nazwy Wikipedia oraz typ i podtypy (jeśli istnieją) oraz lokalizacji, w oryginalny tekst.

    ```csharp
    var entitiesResult = await client.EntitiesAsync(false, inputDocuments);

    // Printing recognized entities
    foreach (var document in entitiesResult.Documents)
    {
        Console.WriteLine($"Document ID: {document.Id} ");

        Console.WriteLine("\t Entities:");
        foreach (var entity in document.Entities)
        {
            Console.WriteLine($"\t\tName: {entity.Name},\tType: {entity.Type ?? "N/A"},\tSub-Type: {entity.SubType ?? "N/A"}");
            foreach (var match in entity.Matches)
            {
                Console.WriteLine($"\t\t\tOffset: {match.Offset},\tLength: {match.Length},\tScore: {match.EntityTypeScore:F3}");
            }
        }
    }
    ```

### <a name="output"></a>Output

```console
Document ID: 1
         Entities:
                Name: Microsoft,        Type: Organization,     Sub-Type: N/A
                        Offset: 0,      Length: 9,      Score: 1.000
                Name: Bill Gates,       Type: Person,   Sub-Type: N/A
                        Offset: 25,     Length: 10,     Score: 1.000
                Name: Paul Allen,       Type: Person,   Sub-Type: N/A
                        Offset: 40,     Length: 10,     Score: 0.999
                Name: April 4,  Type: Other,    Sub-Type: N/A
                        Offset: 54,     Length: 7,      Score: 0.800
                Name: April 4, 1975,    Type: DateTime, Sub-Type: Date
                        Offset: 54,     Length: 13,     Score: 0.800
                Name: BASIC,    Type: Other,    Sub-Type: N/A
                        Offset: 89,     Length: 5,      Score: 0.800
                Name: Altair 8800,      Type: Other,    Sub-Type: N/A
                        Offset: 116,    Length: 11,     Score: 0.800
Document ID: 2
         Entities:
                Name: Microsoft,        Type: Organization,     Sub-Type: N/A
                        Offset: 21,     Length: 9,      Score: 1.000
                Name: Redmond (Washington),     Type: Location, Sub-Type: N/A
                        Offset: 60,     Length: 7,      Score: 0.991
                Name: 21 kilómetros,    Type: Quantity, Sub-Type: Dimension
                        Offset: 71,     Length: 13,     Score: 0.800
                Name: Seattle,  Type: Location, Sub-Type: N/A
                        Offset: 88,     Length: 7,      Score: 1.000
```

## <a name="perform-key-phrase-extraction"></a>Wykonaj wyodrębnianie kluczowych fraz

1. Utwórz nową funkcję `KeyPhraseExtractionExample()` przyjmującej klienta, który został utworzony wcześniej.
2. Generowanie listy `MultiLanguageBatchInput` obiektów, które zawierają swoje dokumenty.

    ```csharp
    public static async Task KeyPhraseExtractionExample(TextAnalyticsClient client)
    {
        var inputDocuments = new MultiLanguageBatchInput(
                    new List<MultiLanguageInput>
                    {
                        new MultiLanguageInput("ja", "1", "猫は幸せ"),
                        new MultiLanguageInput("de", "2", "Fahrt nach Stuttgart und dann zum Hotel zu Fu."),
                        new MultiLanguageInput("en", "3", "My cat might need to see a veterinarian."),
                        new MultiLanguageInput("es", "4", "A mi me encanta el fútbol!")
                    });
        //...
    }
    ```

3. W tej samej funkcji, należy wywołać `client.KeyPhrasesAsync()` i uzyskać wynik. Następnie iteracyjnego przeglądania wyników. Drukowanie identyfikator każdego dokumentu i wszelkie wykryte fraz kluczowych.

    ```csharp
    var kpResults = await client.KeyPhrasesAsync(false, inputDocuments);

    // Printing keyphrases
    foreach (var document in kpResults.Documents)
    {
        Console.WriteLine($"Document ID: {document.Id} ");

        Console.WriteLine("\t Key phrases:");

        foreach (string keyphrase in document.KeyPhrases)
        {
            Console.WriteLine($"\t\t{keyphrase}");
        }
    }
    ```

### <a name="output"></a>Output

```console
Document ID: 1
         Key phrases:
                幸せ
Document ID: 2
         Key phrases:
                Stuttgart
                Hotel
                Fahrt
                Fu
Document ID: 3
         Key phrases:
                cat
                veterinarian
Document ID: 4
         Key phrases:
                fútbol
```

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Analiza tekstu przy użyciu usługi Power BI](../tutorials/tutorial-power-bi-key-phrases.md)


* [Text Analytics overview (Omówienie analizy tekstu)](../overview.md)
* [Często zadawane pytania](../text-analytics-resource-faq.md)
