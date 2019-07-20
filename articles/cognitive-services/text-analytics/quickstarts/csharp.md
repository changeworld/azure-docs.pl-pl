---
title: 'Szybki start: Wywołaj usługę analiza tekstu przy użyciu zestawu Azure SDK dla platformy .NET iC#'
titleSuffix: Azure Cognitive Services
description: Informacje i przykłady kodu ułatwiające rozpoczęcie korzystania z usługi analiza tekstu i C#.
services: cognitive-services
author: raymondl
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 07/18/2019
ms.author: assafi
ms.openlocfilehash: 09713528f51675f6e9d7f3073b6c81b095d23631
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/19/2019
ms.locfileid: "68356967"
---
# <a name="quickstart-use-the-net-sdk-and-c-to-call-the-text-analytics-service"></a>Szybki start: Używanie zestawu SDK platformy .NET C# i wywoływanie usługi Analiza tekstu
<a name="HOLTop"></a>

Ten przewodnik Szybki Start ułatwia rozpoczęcie korzystania z zestawu Azure SDK dla C# platformy .NET oraz analizowanie języka. Mimo że interfejs API REST [Analiza tekstu](//go.microsoft.com/fwlink/?LinkID=759711) jest zgodny z większością języków programowania, zestaw SDK zapewnia łatwy sposób integracji usługi z aplikacjami.

> [!NOTE]
> Kod źródłowy tego przykładu jest dostępny w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/samples/TextAnalytics).

Szczegółowe informacje techniczne można znaleźć w dokumentacji dotyczącej zestawu SDK dla programu .NET [Analiza tekstu](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/textanalytics?view=azure-dotnet).

## <a name="prerequisites"></a>Wymagania wstępne

* Wszystkie wersje programu [Visual Studio 2017 lub nowsze]
* Zestaw analiza tekstu [SDK dla platformy .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics)

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

Wymagany jest również [punkt końcowy i klucz dostępu](../How-tos/text-analytics-how-to-access-key.md) , który został wygenerowany podczas rejestracji.

## <a name="create-the-visual-studio-solution-and-install-the-sdk"></a>Utwórz rozwiązanie programu Visual Studio i Zainstaluj zestaw SDK

1. Utwórz nowy projekt aplikacja konsoli (.NET Core). [Uzyskaj dostęp do programu Visual Studio](https://visualstudio.microsoft.com/vs/).
1. Kliknij prawym przyciskiem myszy rozwiązanie i wybierz pozycję **Zarządzaj pakietami NuGet dla rozwiązania**.
1. Wybierz kartę **Przeglądaj**. Wyszukaj ciąg **Microsoft. Azure. CognitiveServices. Language. textanalytics**.

## <a name="authenticate-your-credentials"></a>Uwierzytelnianie poświadczeń

1. Dodaj następujące `using` instrukcje do pliku klasy głównej (który domyślnie jest program.cs).

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

2. Utwórz nową `ApiKeyServiceClientCredentials` klasę do przechowywania poświadczeń i Dodaj je do każdego żądania.

    ```csharp
    class ApiKeyServiceClientCredentials : ServiceClientCredentials
    {
        private readonly string apiKey;

        public ApiKeyServiceClientCredentials(string apiKey)
        {
            this.apiKey = apiKey;
        }

        public override Task ProcessHttpRequestAsync(HttpRequestMessage request, CancellationToken cancellationToken)
        {
            if (request == null)
            {
                throw new ArgumentNullException("request");
            }
            request.Headers.Add("Ocp-Apim-Subscription-Key", this.apiKey);
            return base.ProcessHttpRequestAsync(request, cancellationToken);
        }
    }
    ```

3. `Program` Zaktualizuj klasę. Dodaj stały element członkowski dla klucza interfejs API analizy tekstu i drugi dla punktu końcowego usługi. Pamiętaj, aby użyć poprawnej lokalizacji platformy Azure dla zasobu analiza tekstu.

    ```csharp
    //Enter your Text Analytics (TA) API Key (available in Azure Portal -> your TA resource -> Keys)
    private const string ApiKey = "enter-your-textanalytics-api-key-here";
    //You can get the resource location from Azure Portal -> your TA resource -> Overview
    private const string Endpoint = "enter-your-service-endpoint-here"; // For example: "https://<your-location>.api.cognitive.microsoft.com";
    ```
> [!Tip]
> W celu zwiększenia bezpieczeństwa wpisów tajnych w systemach produkcyjnych zalecamy korzystanie z [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/quick-create-net).
>

## <a name="create-a-text-analytics-client"></a>Tworzenie klienta analiza tekstu

`Main` W funkcji projektu Wywołaj przykładową metodę, którą chcesz wywołać. Przekaż zdefiniowane przez `ApiKey` siebie parametry i.`Endpoint`

```csharp
    public static void Main(string[] args)
    {
        var credentials = new ApiKeyServiceClientCredentials(ApiKey);
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

W poniższych sekcjach opisano sposób wywoływania każdej funkcji usługi.

## <a name="perform-sentiment-analysis"></a>Wykonaj analizę tonacji

1. Utwórz nową funkcję `SentimentAnalysisExample()` , która przybierze wcześniej utworzony klient.
2. Wygeneruj listę `MultiLanguageInput` obiektów, które zawierają dokumenty, które chcesz przeanalizować.

    ```csharp
    public static async Task SentimentAnalysisExample(TextAnalyticsClient client)
    {
        // The documents to be analyzed. Add the language of the document. The ID can be any value.
        var inputDocuments = new MultiLanguageBatchInput(
            new List<MultiLanguageInput>
            {
                new MultiLanguageInput("en", "1", "I had the best day of my life.")
            });
        //...
    }
    ```

3. W tej samej funkcji Wywołaj `client.SentimentAsync()` i Pobierz wynik. Następnie wykonaj iterację w wynikach. Drukuj identyfikator każdego dokumentu i ocenę tonacji. Wynik zbliżony do 0 wskazuje negatywną tonacji, podczas gdy wynik zbliżony do 1 wskazuje pozytywną tonacji.

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
```

## <a name="perform-language-detection"></a>Przeprowadzanie wykrywania języka

1. Utwórz nową funkcję `DetectLanguageExample()` , która przybierze wcześniej utworzony klient.
2. Wygeneruj listę `LanguageInput` obiektów, które zawierają dokumenty.

    ```csharp
    public static async Task DetectLanguageExample(TextAnalyticsClient client)
    {

        // The documents to be submitted for language detection. The ID can be any value.
        var inputDocuments = new LanguageBatchInput(
                new List<LanguageInput>
                    {
                        new LanguageInput(id: "1", text: "This is a document written in English.")
                    });
        //...
    }
    ```

3. W tej samej funkcji Wywołaj `client.DetectLanguageAsync()` i Pobierz wynik. Następnie wykonaj iterację w wynikach. Drukuj identyfikator każdego dokumentu i pierwszy zwrócony język.

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
```

## <a name="perform-entity-recognition"></a>Wykonywanie rozpoznawania jednostek

1. Utwórz nową funkcję `RecognizeEntitiesExample()` , która przybierze wcześniej utworzony klient.
2. Wygeneruj listę `MultiLanguageBatchInput` obiektów, które zawierają dokumenty.

    ```csharp
    public static async Task RecognizeEntitiesExample(TextAnalyticsClient client)
    {
        // The documents to be submitted for entity recognition. The ID can be any value.
        var inputDocuments = new MultiLanguageBatchInput(
            new List<MultiLanguageInput>
            {
                new MultiLanguageInput("en", "1", "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800.")
            });
        //...
    }
    ```

3. W tej samej funkcji Wywołaj `client.EntitiesAsync()` i Pobierz wynik. Następnie wykonaj iterację w wynikach. Drukuj identyfikator każdego dokumentu. Dla każdej wykrytej jednostki Wydrukuj jej nazwę witryny Wikipedia oraz typ i podtypy (jeśli istnieją), a także lokalizacje w oryginalnym tekście.

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
```

## <a name="perform-key-phrase-extraction"></a>Wykonaj wyodrębnianie kluczowych fraz

1. Utwórz nową funkcję `KeyPhraseExtractionExample()` , która przybierze wcześniej utworzony klient.
2. Wygeneruj listę `MultiLanguageBatchInput` obiektów, które zawierają dokumenty.

    ```csharp
    public static async Task KeyPhraseExtractionExample(TextAnalyticsClient client)
    {
        var inputDocuments = new MultiLanguageBatchInput(
                    new List<MultiLanguageInput>
                    {
                        new MultiLanguageInput("en", "1", "My cat might need to see a veterinarian.")
                    });
        //...
    }
    ```

3. W tej samej funkcji Wywołaj `client.KeyPhrasesAsync()` i Pobierz wynik. Następnie wykonaj iterację w wynikach. Drukuj identyfikator każdego dokumentu i wszystkie wykryte frazy kluczy.

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
                cat
                veterinarian
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Analiza tekstu przy użyciu usługi Power BI](../tutorials/tutorial-power-bi-key-phrases.md)


* [Text Analytics overview (Omówienie analizy tekstu)](../overview.md)
* [Często zadawane pytania](../text-analytics-resource-faq.md)
