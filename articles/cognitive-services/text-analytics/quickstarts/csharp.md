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
ms.date: 07/30/2019
ms.author: assafi
ms.openlocfilehash: d12f6b400b270c6ef631d9f503980efef1ae8458
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2019
ms.locfileid: "68840385"
---
# <a name="quickstart-use-the-net-sdk-and-c-to-call-the-text-analytics-service"></a>Szybki start: Używanie zestawu SDK platformy .NET C# i wywoływanie usługi Analiza tekstu
<a name="HOLTop"></a>

Ten przewodnik Szybki Start ułatwia rozpoczęcie korzystania z zestawu Azure SDK dla C# platformy .NET oraz analizowanie języka. Mimo że interfejs API REST [Analiza tekstu](//go.microsoft.com/fwlink/?LinkID=759711) jest zgodny z większością języków programowania, zestaw SDK zapewnia łatwy sposób integracji usługi z aplikacjami.

> [!NOTE]
> Kod demonstracyjny w tym artykule używa metod synchronicznych analiza tekstu .NET SDK dla uproszczenia. Jednak w przypadku scenariuszy produkcyjnych zaleca się użycie wsadowych metod asynchronicznych we własnych aplikacjach, aby zapewnić ich skalowalność i elastyczność. Można na przykład użyć `SentimentBatchAsync` `Sentiment`zamiast.
>
> Wsadową asynchroniczną wersję tego przykładu można znaleźć w witrynie [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/samples/TextAnalytics).

Szczegółowe informacje techniczne można znaleźć w dokumentacji dotyczącej zestawu SDK dla programu .NET [Analiza tekstu](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/textanalytics?view=azure-dotnet).

## <a name="prerequisites"></a>Wymagania wstępne

* Dowolna wersja programu Visual Studio 2017 lub nowszego
* Zestaw analiza tekstu [SDK dla platformy .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics)

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

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
    // Enter your Text Analytics (TA) API Key (available in Azure Portal -> your TA resource -> Keys)
    private const string ApiKey = "enter-your-textanalytics-api-key-here";
    // You can get the resource location from Azure Portal -> your TA resource -> Overview
    // There are two acceptable formats for the endpoint, both
    // require that you omit the `/text/analytics/<version>` suffix:
    // 1. A location based endpoint URL -
    //     "https://<your-location>.api.cognitive.microsoft.com";
    // 2. A resource name based endpoint URL -
    //     "https://<your-resource-name>.cognitiveservices.azure.com";
    private const string Endpoint = "enter-your-base-resource-endpoint-here";
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
        SentimentAnalysisExample(client);
        // DetectLanguageExample(client);
        // RecognizeEntitiesExample(client);
        // KeyPhraseExtractionExample(client);
        Console.ReadLine();
    }
```

W poniższych sekcjach opisano sposób wywoływania każdej funkcji usługi.

## <a name="perform-sentiment-analysis"></a>Wykonaj analizę tonacji

1. Utwórz nową funkcję `SentimentAnalysisExample()` , która przybierze wcześniej utworzony klient.
2. W tej samej funkcji Wywołaj `client.Sentiment()` i Pobierz wynik. Wynik będzie zawierać tonacji `Score` , jeśli to `errorMessage` się powiedzie, a jeśli nie. Wynik zbliżony do 0 wskazuje negatywną tonacji, podczas gdy wynik zbliżony do 1 wskazuje pozytywną tonacji.

    ```csharp
    var result = client.Sentiment("I had the best day of my life.", "en");

    // Printing sentiment results
    Console.WriteLine($"Sentiment Score: {result.Score:0.00}");
    ```

### <a name="output"></a>Output

```console
Sentiment Score: 0.87
```

## <a name="perform-language-detection"></a>Przeprowadzanie wykrywania języka

1. Utwórz nową funkcję `DetectLanguageExample()` , która przybierze wcześniej utworzony klient.
2. W tej samej funkcji Wywołaj `client.DetectLanguage()` i Pobierz wynik. Wynik będzie zawierać listę wykrytych języków w `DetectedLanguages` przypadku powodzenia, `errorMessage` a jeśli nie. Następnie wydrukuj pierwszy zwracany język.

    ```csharp
    var result = client.DetectLanguage("This is a document written in English.");

    // Printing detected languages
    Console.WriteLine($"Language: {result.DetectedLanguages[0].Name}");
    ```

> [!Tip]
> W niektórych przypadkach może być trudno odróżnić Języki w oparciu o dane wejściowe. Możesz użyć parametru, `countryHint` aby określić 2-literowy kod kraju. Domyślnie interfejs API używa "US" jako domyślnego countryHint, aby usunąć to zachowanie, można zresetować ten parametr, ustawiając tę wartość na pusty ciąg `countryHint = ""` .

### <a name="output"></a>Output

```console
Language: English
```

## <a name="perform-entity-recognition"></a>Wykonywanie rozpoznawania jednostek

1. Utwórz nową funkcję `RecognizeEntitiesExample()` , która przybierze wcześniej utworzony klient.
2. W tej samej funkcji Wywołaj `client.Entities()` i Pobierz wynik. Następnie wykonaj iterację w wynikach. Wynik będzie zawierać listę wykrytych jednostek w `Entities` przypadku pomyślnego, `errorMessage` a jeśli nie. Dla każdej wykrytej jednostki Wydrukuj jej typ, podtyp, nazwę witryny Wikipedia (jeśli istnieją), a także lokalizacje w oryginalnym tekście.

    ```csharp
    var result = client.Entities("Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800.");

    // Printing recognized entities
    Console.WriteLine("Entities:");
    foreach (var entity in result.Entities)
    {
        Console.WriteLine($"\tName: {entity.Name},\tType: {entity.Type ?? "N/A"},\tSub-Type: {entity.SubType ?? "N/A"}");
        foreach (var match in entity.Matches)
        {
            Console.WriteLine($"\t\tOffset: {match.Offset},\tLength: {match.Length},\tScore: {match.EntityTypeScore:F3}");
        }
    }
    ```

### <a name="output"></a>Output

```console
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
2. W tej samej funkcji Wywołaj `client.KeyPhrases()` i Pobierz wynik. Wynik będzie zawierać listę wykrytych fraz kluczy w `KeyPhrases` przypadku pomyślnego, `errorMessage` a jeśli nie. Następnie wydrukuj wszystkie wykryte frazy kluczy.

    ```csharp
    var result = client.KeyPhrases("My cat might need to see a veterinarian.");

    // Printing key phrases
    Console.WriteLine("Key phrases:");

    foreach (string keyphrase in result.KeyPhrases)
    {
        Console.WriteLine($"\t{keyphrase}");
    }
    ```

### <a name="output"></a>Output

```console
Key phrases:
    cat
    veterinarian
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Analiza tekstu przy użyciu usługi Power BI](../tutorials/tutorial-power-bi-key-phrases.md)


* [Text Analytics overview (Omówienie analizy tekstu)](../overview.md)
* [Często zadawane pytania](../text-analytics-resource-faq.md)
