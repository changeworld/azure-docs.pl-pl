---
title: 'Szybki start: Analiza tekstu Biblioteka kliencka dla platformy .NET | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Skorzystaj z tego przewodnika Szybki Start, aby rozpocząć korzystanie z interfejs API analizy tekstu z usługi Azure Cognitive Services.
services: cognitive-services
author: raymondl
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 08/28/2019
ms.author: assafi
ms.openlocfilehash: 6e6f1d5cfe1f5e745e6f780b5cb9f979520a1f91
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/29/2019
ms.locfileid: "70134931"
---
# <a name="quickstart-text-analytics-client-library-for-net"></a>Szybki start: Biblioteka kliencka analizy tekstu dla platformy .NET
<a name="HOLTop"></a>

Wprowadzenie do biblioteki klienta analiza tekstu dla platformy .NET. Wykonaj następujące kroki, aby zainstalować pakiet i wypróbować przykładowy kod dla podstawowych zadań. 

Użyj biblioteki klienta analiza tekstu dla platformy .NET do wykonania:

* Analiza tonacji
* Wykrywanie języka
* Rozpoznawanie jednostek
* Wyodrębnianie kluczowych fraz

[](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/textanalytics?view=azure-dotnet-preview) | [Przykłady](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples) pakietu | [kodu](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.TextAnalytics)[](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics/)źródłowego w bibliotece dokumentacji referencyjnej (NuGet) | 

> [!NOTE] 
> Kod demonstracyjny w tym artykule używa metod synchronicznych analiza tekstu .NET SDK dla uproszczenia. Jednak w przypadku scenariuszy produkcyjnych zaleca się użycie wsadowych metod asynchronicznych we własnych aplikacjach, aby zapewnić ich skalowalność i elastyczność. Na przykład wywoływanie [SentimentBatchAsync ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.sentimentbatchasync?view=azure-dotnet&viewFallbackFrom=azure-dotnet-preview) zamiast [tonacji ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.sentiment?view=azure-dotnet).

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/)
* Bieżąca wersja [zestaw .NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core).

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-text-analytics-azure-resource"></a>Tworzenie zasobu analiza tekstu platformy Azure

Uzyskaj klucz do uwierzytelniania aplikacji przez usługę Azure Cognitive Services są reprezentowane przez zasoby platformy Azure, które subskrybujesz. Utwórz zasób dla analiza tekstu przy użyciu [Azure Portal](../../cognitive-services-apis-create-account.md) lub [interfejsu wiersza polecenia platformy Azure](../../cognitive-services-apis-create-account-cli.md) na komputerze lokalnym. Możesz również wykonać następujące czynności:

* Uzyskaj [klucz wersji próbnej](https://azure.microsoft.com/try/cognitive-services/#decision) ważny przez 7 dni bezpłatnie. Po utworzeniu rejestracji będzie on dostępny w [witrynie sieci Web systemu Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Wyświetl zasób na [Azure Portal](https://portal.azure.com/)

Po otrzymaniu klucza z subskrypcji próbnej lub zasobu [Utwórz zmienną środowiskową](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) dla klucza o nazwie `TEXT_ANALYTICS_SUBSCRIPTION_KEY`.

### <a name="create-a-new-c-application"></a>Utwórz nową C# aplikację

Utwórz nową aplikację platformy .NET Core w preferowanym edytorze lub środowisku IDE. 

W oknie konsoli (na przykład cmd, PowerShell lub bash) Użyj `dotnet new` polecenia, aby utworzyć nową aplikację konsolową o nazwie. `text-analytics quickstart` To polecenie tworzy prosty projekt "Hello world" C# z pojedynczym plikiem źródłowym: *program.cs*. 

```console
dotnet new console -n text-analytics-quickstart
```

Zmień katalog na nowo utworzony folder aplikacji. Aplikację można skompilować przy użyciu:

```console
dotnet build
```

Dane wyjściowe kompilacji nie powinny zawierać ostrzeżeń ani błędów. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

W katalogu projektu Otwórz plik *program.cs* w preferowanym edytorze lub w środowisku IDE. Dodaj następujące `using` dyrektywy:

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

W `Program` klasie aplikacji Utwórz zmienne dla punktu końcowego platformy Azure i klucza subskrypcji zasobu. W konstruktorze statycznym Uzyskaj te wartości ze zmiennych `TEXT_ANALYTICS_SUBSCRIPTION_KEY` środowiskowych i. `TEXT_ANALYTICS_ENDPOINT` Jeśli te zmienne środowiskowe zostały utworzone po rozpoczęciu edytowania aplikacji, należy zamknąć i ponownie otworzyć Edytor, środowisko IDE lub powłokę używaną w celu uzyskania dostępu do zmiennych.

```csharp
private const string key_var = "TEXT_ANALYTICS_SUBSCRIPTION_KEY";
private static readonly string subscriptionKey = Environment.GetEnvironmentVariable(key_var);

private const string endpoint_var = "TEXT_ANALYTICS_ENDPOINT";
private static readonly string endpoint = Environment.GetEnvironmentVariable(endpoint_var);

static Program()
{
    if (null == subscriptionKey)
    {
        throw new Exception("Please set/export the environment variable: " + key_var);
    }
    if (null == endpoint)
    {
        throw new Exception("Please set/export the environment variable: " + endpoint_var);
    }
}
```

W `Main` metodzie aplikacji Utwórz poświadczenia, aby uzyskać dostęp do punktu końcowego analiza tekstu.  Metody wywoływane przez `Main` metodę zostaną zdefiniowane później.

[!INCLUDE [text-analytics-find-resource-information](../includes/find-azure-resource-info.md)]

```csharp
static void Main(string[] args)
{
    var credentials = new ApiKeyServiceClientCredentials(subscriptionKey);
    TextAnalyticsClient client = new TextAnalyticsClient(credentials)
    {
        Endpoint = endpoint
    };

    Console.OutputEncoding = System.Text.Encoding.UTF8;
    SentimentAnalysisExample(client);
    // languageDetectionExample(client);
    // entityRecognitionExample(client);
    // KeyPhraseExtractionExample(client);
    
    Console.Write("Press any key to exit.");
    Console.ReadKey();
}
```

### <a name="install-the-client-library"></a>Zainstaluj bibliotekę kliencką

W katalogu aplikacji zainstaluj analiza tekstuą bibliotekę kliencką dla platformy .NET przy użyciu następującego polecenia:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Language.TextAnalytics --version 4.0.0
```

Jeśli używasz środowiska IDE programu Visual Studio, Biblioteka kliencka jest dostępna jako pakiet NuGet do pobrania.

## <a name="object-model"></a>Model obiektów

Klient analiza tekstu jest obiektem [TextAnalyticsClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-dotnet) , który jest uwierzytelniany na platformie Azure przy użyciu klucza, i udostępnia funkcje do akceptowania tekstu jako pojedyncze ciągi lub jako partia. Możesz wysyłać tekst do interfejsu API synchronicznie lub asynchronicznie. Obiekt Response będzie zawierać informacje o analizie dla każdego wysyłanego dokumentu. 


## <a name="code-examples"></a>Przykłady kodu

* [Uwierzytelnianie klienta](#authenticate-the-client)
* [Analiza tonacji](#sentiment-analysis)
* [Wykrywanie języka](#language-detection)
* [Rozpoznawanie jednostek](#entity-recognition)
* [Wyodrębnianie kluczowych fraz](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

Utwórz nową `ApiKeyServiceClientCredentials` klasę do przechowywania poświadczeń i Dodaj je do żądań klienta. W tym celu `ProcessHttpRequestAsync()` należy utworzyć przesłonięcie, które dodaje klucz `Ocp-Apim-Subscription-Key` do nagłówka.

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

`main()` W metodzie Utwórz wystąpienie klienta przy użyciu klucza i punktu końcowego.

```csharp
var credentials = new ApiKeyServiceClientCredentials(key);
TextAnalyticsClient client = new TextAnalyticsClient(credentials)
{
    Endpoint = endpoint
};
```

## <a name="sentiment-analysis"></a>Analiza tonacji

Utwórz nową funkcję o nazwie `SentimentAnalysisExample()` , która przybiera wcześniej utworzoną klienta i wywołaj funkcję [tonacji ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.sentiment?view=azure-dotnet) . Zwrócony obiekt [SentimentResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.sentimentresult?view=azure-dotnet) będzie zawierać tonacji `Score` w przypadku `errorMessage` powodzenia, a jeśli nie. 

Wynik zbliżony do 0 wskazuje negatywną tonacji, podczas gdy wynik zbliżony do 1 wskazuje pozytywną tonacji.

```csharp
static void SentimentAnalysisExample(TextAnalyticsClient client){
    var result = client.Sentiment("I had the best day of my life.", "en");
    Console.WriteLine($"Sentiment Score: {result.Score:0.00}");
}
```

### <a name="output"></a>Output

```console
Sentiment Score: 0.87
```

## <a name="language-detection"></a>Wykrywanie języka

Utwórz nową funkcję o nazwie `languageDetectionExample()` , która przybiera wcześniej utworzoną klienta i wywołaj funkcję [DetectLanguage ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.detectlanguage?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_DetectLanguage_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) . Zwrócony obiekt [LanguageResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.languageresult?view=azure-dotnet) będzie zawierać listę wykrytych języków w `DetectedLanguages` przypadku `errorMessage` powodzenia, a jeśli nie.  Drukowanie pierwszego zwracanego języka.

> [!Tip]
> W niektórych przypadkach może być trudno odróżnić Języki w oparciu o dane wejściowe. Możesz użyć parametru, `countryHint` aby określić 2-literowy kod kraju. Domyślnie interfejs API używa "US" jako domyślnego countryHint, aby usunąć to zachowanie, można zresetować ten parametr, ustawiając tę wartość na pusty ciąg `countryHint = ""` .

```csharp
static void languageDetectionExample(TextAnalyticsClient client){
    var result = client.DetectLanguage("This is a document written in English.");
    Console.WriteLine($"Language: {result.DetectedLanguages[0].Name}");
}
```
<!--
[!code-csharp[Language Detection example](~/cognitive-services-dotnet-sdk-samples/samples/language/Program.cs?name=language-detection)]
-->

### <a name="output"></a>Output

```console
Language: English
```

## <a name="entity-recognition"></a>Rozpoznawanie jednostek

Utwórz nową funkcję o nazwie `RecognizeEntitiesExample()` , która przybiera wcześniej utworzoną klienta i wywołaj funkcję [Entities ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.entities?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_Entities_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) . Wykonaj iterację w wynikach. Zwrócony obiekt [EntitiesResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.entitiesresult?view=azure-dotnet) będzie zawierać listę wykrytych jednostek w `Entities` przypadku `errorMessage` powodzenia, a jeśli nie. Dla każdej wykrytej jednostki Wydrukuj jej typ, podtyp, nazwę witryny Wikipedia (jeśli istnieją), a także lokalizacje w oryginalnym tekście.

```csharp
static void entityRecognitionExample(TextAnalyticsClient client){

    var result = client.Entities("Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800.");
    Console.WriteLine("Entities:");
    foreach (var entity in result.Entities){
        Console.WriteLine($"\tName: {entity.Name},\tType: {entity.Type ?? "N/A"},\tSub-Type: {entity.SubType ?? "N/A"}");
        foreach (var match in entity.Matches){
            Console.WriteLine($"\t\tOffset: {match.Offset},\tLength: {match.Length},\tScore: {match.EntityTypeScore:F3}");
        }
    }
}
```
<!--
[!code-csharp[Entity Recognition example](~/cognitive-services-dotnet-sdk-samples/samples/language/Program.cs?name=language-detection)]
-->

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

## <a name="key-phrase-extraction"></a>Wyodrębnianie kluczowych fraz

Utwórz nową funkcję o nazwie `KeyPhraseExtractionExample()` , która powoduje, że klient został utworzony wcześniej i wywołaj funkcję [frazy kluczowej ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.keyphrases?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_KeyPhrases_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) . Wynik będzie zawierać listę wykrytych fraz kluczy w `KeyPhrases` przypadku pomyślnego, `errorMessage` a jeśli nie. Drukuj wszystkie wykryte frazy kluczy.

```csharp
static void KeyPhraseExtractionExample(TextAnalyticsClient client)
{
    var result = client.KeyPhrases("My cat might need to see a veterinarian.");

    // Printing key phrases
    Console.WriteLine("Key phrases:");

    foreach (string keyphrase in result.KeyPhrases)
    {
        Console.WriteLine($"\t{keyphrase}");
    }
}
```

### <a name="output"></a>Output

```console
Key phrases:
    cat
    veterinarian
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli chcesz wyczyścić i usunąć subskrypcję Cognitive Services, możesz usunąć zasób lub grupę zasobów. Usunięcie grupy zasobów spowoduje również usunięcie wszystkich innych zasobów skojarzonych z tą grupą zasobów.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfejs wiersza polecenia platformy Azure](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Analiza tekstu przy użyciu usługi Power BI](../tutorials/tutorial-power-bi-key-phrases.md)


* [Text Analytics overview (Omówienie analizy tekstu)](../overview.md)
* [Analiza tonacji](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Rozpoznawanie jednostek](../how-tos/text-analytics-how-to-entity-linking.md)
* [Wykryj język](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [Rozpoznawanie języka](../how-tos/text-analytics-how-to-language-detection.md)
