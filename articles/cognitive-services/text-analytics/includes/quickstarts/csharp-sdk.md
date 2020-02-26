---
title: 'Szybki Start: analiza tekstu bibliotekę kliencką dla C# | Microsoft Docs'
description: Wprowadzenie do biblioteki klienta analiza tekstu dla programuC#
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 01/15/2020
ms.author: aahi
ms.reviewer: assafi
ms.openlocfilehash: b95f7fd14239b258fe1b501fdf71cf0dfe2beacf
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77590820"
---
<a name="HOLTop"></a>

#### <a name="version-30-preview"></a>[Wersja 3,0-Preview](#tab/version-3)

[dokumentacja referencyjna v3](https://aka.ms/azsdk-net-textanalytics-ref-docs) | [kod źródłowy biblioteki v3](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics) | [v3 (NuGet)](https://www.nuget.org/packages/Azure.AI.TextAnalytics) | [v3](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics/samples)

#### <a name="version-21"></a>[Wersja 2,1](#tab/version-2)

[wersja 2 dokumentacja referencyjna](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/textanalytics?view=azure-dotnet-preview) | [wersja 2 kod źródłowy](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.TextAnalytics) | [v2 pakiet (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics/) | [v2](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples)

---

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/)
* [Środowisko IDE programu Visual Studio](https://visualstudio.microsoft.com/vs/)

[!INCLUDE [text-analytics-resource-creation](resource-creation.md)]

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-new-net-core-application"></a>Tworzenie nowej aplikacji platformy .NET Core

Za pomocą środowiska IDE programu Visual Studio Utwórz nową aplikację konsolową platformy .NET Core. Spowoduje to utworzenie projektu "Hello world" z pojedynczym C# plikiem źródłowym: *program.cs*.

#### <a name="version-30-preview"></a>[Wersja 3,0-Preview](#tab/version-3)

Zainstaluj bibliotekę kliencką, klikając prawym przyciskiem myszy rozwiązanie w **Eksplorator rozwiązań** i wybierając pozycję **Zarządzaj pakietami NuGet**. W Menedżerze pakietów, który otwiera Wybierz pozycję **Przeglądaj**, zaznacz pozycję **Uwzględnij wersję wstępną**i Wyszukaj `Azure.AI.TextAnalytics`. Wybierz pozycję wersja `1.0.0-preview.2`a następnie **Zainstaluj**program. Można również użyć [konsoli Menedżera pakietów](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package).

Otwórz plik *program.cs* i Dodaj następujące dyrektywy `using`:

```csharp
using System;
using Azure.AI.TextAnalytics;
```

W klasie `Program` aplikacji Utwórz zmienne dla klucza i punktu końcowego zasobu.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```csharp
private static readonly TextAnalyticsApiKeyCredential credentials = new TextAnalyticsApiKeyCredential("<replace-with-your-text-analytics-key-here>");
private static readonly Uri endpoint = new Uri("<replace-with-your-text-analytics-endpoint-here>");
```

Zastąp metodę `Main` aplikacji. Metody wywoływane tutaj zostaną zdefiniowane później.

```csharp
static void Main(string[] args)
{
    var client = new TextAnalyticsClient(endpoint, credentials);
    // You will implement these methods later in the quickstart.
    SentimentAnalysisExample(client);
    LanguageDetectionExample(client);
    EntityRecognitionExample(client);
    EntityPIIExample(client);
    EntityLinkingExample(client);
    KeyPhraseExtractionExample(client);

    Console.Write("Press any key to exit.");
    Console.ReadKey();
}
```

#### <a name="version-21"></a>[Wersja 2,1](#tab/version-2)

Zainstaluj bibliotekę kliencką, klikając prawym przyciskiem myszy rozwiązanie w **Eksplorator rozwiązań** i wybierając pozycję **Zarządzaj pakietami NuGet**. W Menedżerze pakietów, który zostanie otwarty, wybierz pozycję **Przeglądaj** i Wyszukaj `Microsoft.Azure.CognitiveServices.Language.TextAnalytics`. Kliknij go, a następnie **Zainstaluj**. Można również użyć [konsoli Menedżera pakietów](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package).

Otwórz plik *program.cs* i Dodaj następujące dyrektywy `using`:

[!code-csharp[Import directives](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=imports)]

W klasie `Program` aplikacji Utwórz zmienne dla klucza i punktu końcowego zasobu. 

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```csharp
private static readonly string key = "<replace-with-your-text-analytics-key-here>";
private static readonly string endpoint = "<replace-with-your-text-analytics-endpoint-here>";
```

Zastąp metodę `Main` aplikacji. Metody wywoływane tutaj zostaną zdefiniowane później.

[!code-csharp[main method](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=main)]

---

## <a name="object-model"></a>Model obiektów

Klient analiza tekstu jest obiektem `TextAnalyticsClient`, który jest uwierzytelniany na platformie Azure przy użyciu klucza, i udostępnia funkcje, aby akceptować tekst jako pojedyncze ciągi lub jako partię. Możesz wysyłać tekst do interfejsu API synchronicznie lub asynchronicznie. Obiekt Response będzie zawierać informacje o analizie dla każdego wysyłanego dokumentu. 

Jeśli używasz wersji `3.0-preview`, możesz użyć opcjonalnego wystąpienia `TextAnalyticsClientOptions`, aby zainicjować klienta z różnymi ustawieniami domyślnymi (na przykład językiem domyślnym lub wskazówką kraju). Uwierzytelnianie można także przeprowadzić przy użyciu tokenu Azure Active Directory. 

## <a name="code-examples"></a>Przykłady kodu

* [Analiza tonacji](#sentiment-analysis)
* [Wykrywanie języka](#language-detection)
* [Rozpoznawanie jednostek nazwanych](#named-entity-recognition-ner)
* [Wykrywanie informacji osobistych](#detect-personal-information)
* [Łączenie jednostek](#entity-linking)
* [Wyodrębnianie kluczowych fraz](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

#### <a name="version-30-preview"></a>[Wersja 3,0-Preview](#tab/version-3)

Upewnij się, że główna Metoda z wcześniej tworzy nowy obiekt klienta z punktem końcowym i poświadczeniami.

```csharp
var client = new TextAnalyticsClient(endpoint, credentials);
```

#### <a name="version-21"></a>[Wersja 2,1](#tab/version-2)

Utwórz nową klasę `ApiKeyServiceClientCredentials` do przechowywania poświadczeń i Dodaj je do żądań klienta. W tym celu należy utworzyć przesłonięcie dla `ProcessHttpRequestAsync()`, które dodaje klucz do nagłówka `Ocp-Apim-Subscription-Key`.

[!code-csharp[Client class](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=clientClass)]

Utwórz metodę, aby utworzyć wystąpienie obiektu [TextAnalyticsClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-dotnet) z punktem końcowym i obiektem `ApiKeyServiceClientCredentials` zawierającym klucz.

[!code-csharp[Client authentication](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=authentication)]

---

## <a name="sentiment-analysis"></a>Analiza tonacji

#### <a name="version-30-preview"></a>[Wersja 3,0-Preview](#tab/version-3)

Utwórz nową funkcję o nazwie `SentimentAnalysisExample()`, która przybierze wcześniej utworzony klient i wywołaj funkcję `AnalyzeSentiment()`. Zwrócony obiekt `Response<DocumentSentiment>` będzie zawierać etykietę tonacji i ocenę całego dokumentu wejściowego, a także analizę tonacji dla każdego zdania, jeśli to się powiedzie. Jeśli wystąpił błąd, spowoduje to zgłoszenie `RequestFailedException`.

```csharp
static void SentimentAnalysisExample(TextAnalyticsClient client)
{
    string inputText = "I had the best day of my life. I wish you were there with me.";
    DocumentSentiment documentSentiment = client.AnalyzeSentiment(inputText);
    Console.WriteLine($"Document sentiment: {documentSentiment.Sentiment}\n");

    var si = new StringInfo(inputText);
    foreach (var sentence in documentSentiment.Sentences)
    {
        Console.WriteLine($"\tSentence [offset {sentence.Offset}, length {sentence.Length}]");
        Console.WriteLine($"\tText: \"{si.SubstringByTextElements(sentence.Offset, sentence.Length)}\"");
        Console.WriteLine($"\tSentence sentiment: {sentence.Sentiment}");
        Console.WriteLine($"\tPositive score: {sentence.SentimentScores.Positive:0.00}");
        Console.WriteLine($"\tNegative score: {sentence.SentimentScores.Negative:0.00}");
        Console.WriteLine($"\tNeutral score: {sentence.SentimentScores.Neutral:0.00}\n");
    }
}
```

### <a name="output"></a>Dane wyjściowe

```console
Document sentiment: Positive

        Sentence [offset 0, length 30]
        Text: "I had the best day of my life."
        Sentence sentiment: Positive
        Positive score: 1.00
        Negative score: 0.00
        Neutral score: 0.00

        Sentence [offset 31, length 30]
        Text: "I wish you were there with me."
        Sentence sentiment: Neutral
        Positive score: 0.21
        Negative score: 0.02
        Neutral score: 0.77
```

#### <a name="version-21"></a>[Wersja 2,1](#tab/version-2)

Utwórz nową funkcję o nazwie `SentimentAnalysisExample()`, która przybierze wcześniej utworzony klient i wywołaj funkcję [tonacji ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.sentiment?view=azure-dotnet) . Zwrócony obiekt [SentimentResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.sentimentresult?view=azure-dotnet) będzie zawierać tonacji `Score` w przypadku powodzenia i `errorMessage`, jeśli nie. 

Wynik zbliżony do 0 wskazuje negatywną tonacji, podczas gdy wynik zbliżony do 1 wskazuje pozytywną tonacji.

[!code-csharp[Sentiment analysis](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=sentiment)]

```console
Sentiment Score: 0.87
```

---

## <a name="language-detection"></a>Wykrywanie języka

#### <a name="version-30-preview"></a>[Wersja 3,0-Preview](#tab/version-3)


Utwórz nową funkcję o nazwie `LanguageDetectionExample()`, która przybierze wcześniej utworzony klient i wywołaj funkcję `DetectLanguage()`. Zwrócony obiekt `Response<DetectedLanguage>` będzie zawierać wykryty język wraz z jego nazwą i kodem ISO-6391. Jeśli wystąpił błąd, spowoduje to zgłoszenie `RequestFailedException`.

> [!Tip]
> W niektórych przypadkach może być trudno odróżnić Języki w oparciu o dane wejściowe. Można użyć parametru `countryHint`, aby określić 2-literowy kod kraju. Domyślnie interfejs API używa "US" jako domyślnego countryHint, aby usunąć to zachowanie, można zresetować ten parametr, ustawiając tę wartość na pusty ciąg `countryHint = ""`. Aby ustawić inną wartość domyślną, należy ustawić właściwość `TextAnalyticsClientOptions.DefaultCountryHint` i przekazać ją podczas inicjowania klienta.

```csharp
static void LanguageDetectionExample(TextAnalyticsClient client)
{
    DetectedLanguage detectedLanguage = client.DetectLanguage("Ce document est rédigé en Français.");
    Console.WriteLine("Language:");
    Console.WriteLine($"\t{detectedLanguage.Name},\tISO-6391: {detectedLanguage.Iso6391Name}\n");
}
```

### <a name="output"></a>Dane wyjściowe

```console
Language:
        French, ISO-6391: fr
```

#### <a name="version-21"></a>[Wersja 2,1](#tab/version-2)

Utwórz nową funkcję o nazwie `languageDetectionExample()`, która przybierze wcześniej utworzony klient i wywołaj funkcję [DetectLanguage ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.detectlanguage?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_DetectLanguage_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) . Zwrócony obiekt [LanguageResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.languageresult?view=azure-dotnet) będzie zawierać listę wykrytych języków w `DetectedLanguages`, jeśli zakończy się pomyślnie, a `errorMessage`, jeśli nie. Drukowanie pierwszego zwracanego języka.

> [!Tip]
> W niektórych przypadkach może być trudno odróżnić Języki w oparciu o dane wejściowe. Można użyć parametru `countryHint`, aby określić 2-literowy kod kraju. Domyślnie interfejs API używa "US" jako domyślnego countryHint, aby usunąć to zachowanie, można zresetować ten parametr, ustawiając tę wartość na pusty ciąg `countryHint = ""`.

[!code-csharp[Language Detection example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=languageDetection)]

### <a name="output"></a>Dane wyjściowe

```console
Language: English
```

---

## <a name="named-entity-recognition-ner"></a>Rozpoznawanie jednostek nazwanych (NER)

#### <a name="version-30-preview"></a>[Wersja 3,0-Preview](#tab/version-3)


> [!NOTE]
> Nowość w wersji `3.0-preview`:
> * Funkcja rozpoznawania jednostek obejmuje teraz możliwość wykrywania informacji osobistych w tekście.
> * Łączenie jednostek jest teraz oddzielone od rozpoznawania jednostek.


Utwórz nową funkcję o nazwie `EntityRecognitionExample()`, która przyjmuje wcześniej utworzony klient, wywołaj jego funkcję `RecognizeEntities()` i wykonaj iterację w wynikach. Zwrócony obiekt `Response<IReadOnlyCollection<CategorizedEntity>>` będzie zawierać listę wykrytych jednostek. Jeśli wystąpił błąd, spowoduje to zgłoszenie `RequestFailedException`.

```csharp
static void EntityRecognitionExample(TextAnalyticsClient client)
{
    var response = client.RecognizeEntities("I had a wonderful trip to Seattle last week.");
    Console.WriteLine("Named Entities:");
    foreach(var entity in response.Value)
    {
        Console.WriteLine($"\tText: {entity.Text},\tCategory: {entity.Category},\tSub-Category: {entity.SubCategory}");
        Console.WriteLine($"\t\tOffset: {entity.Offset},\tLength: {entity.Length},\tScore: {entity.Score:F3}\n");
    }
}
```

### <a name="output"></a>Dane wyjściowe

```console
Named Entities:
        Text: Seattle,  Category: Location,     Sub-Category: GPE
                Offset: 26,     Length: 7,      Score: 0.920

        Text: last week,        Category: DateTime,     Sub-Category: DateRange
                Offset: 34,     Length: 9,      Score: 0.800
```

## <a name="detect-personal-information"></a>Wykrywanie informacji osobistych

Utwórz nową funkcję o nazwie `EntityPIIExample()`, która przyjmuje wcześniej utworzony klient, wywołaj jego funkcję `RecognizePiiEntities()` i wykonaj iterację w wynikach. Podobnie jak w poprzedniej funkcji, zwracany obiekt `Response<IReadOnlyCollection<CategorizedEntity>>` będzie zawierać listę wykrytych jednostek. Jeśli wystąpił błąd, spowoduje to zgłoszenie `RequestFailedException`.

```csharp
static void EntityPIIExample(TextAnalyticsClient client)
{
    string inputText = "Insurance policy for SSN on file 123-12-1234 is here by approved.";
    var response = client.RecognizePiiEntities(inputText);
    Console.WriteLine("Personally Identifiable Information Entities:");
    foreach(var entity in response.Value)
    {
        Console.WriteLine($"\tText: {entity.Text},\tCategory: {entity.Category},\tSub-Category: {entity.SubCategory}");
        Console.WriteLine($"\t\tOffset: {entity.Offset},\tLength: {entity.Length},\tScore: {entity.Score:F3}\n");
    }
}
```

### <a name="output"></a>Dane wyjściowe

```console
Personally Identifiable Information Entities:
        Text: 123-12-1234,      Category: U.S. Social Security Number (SSN),    Sub-Category: None
                Offset: 33,     Length: 11,     Score: 0.850
```


## <a name="entity-linking"></a>Łączenie jednostek

Utwórz nową funkcję o nazwie `EntityLinkingExample()`, która przyjmuje wcześniej utworzony klient, wywołaj jego funkcję `RecognizeLinkedEntities()` i wykonaj iterację w wynikach. Zwrócony `Response<IReadOnlyCollection<LinkedEntity>>` reprezentuje listę wykrytych jednostek. Jeśli wystąpił błąd, spowoduje to zgłoszenie `RequestFailedException`. Ponieważ połączone jednostki są jednoznacznie identyfikowane, wystąpienia tej samej jednostki są pogrupowane pod obiektem `LinkedEntity` jako lista obiektów `LinkedEntityMatch`.

```csharp
static void EntityLinkingExample(TextAnalyticsClient client)
{
    var response = client.RecognizeLinkedEntities(
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, " +
        "to develop and sell BASIC interpreters for the Altair 8800. " +
        "During his career at Microsoft, Gates held the positions of chairman, " +
        "chief executive officer, president and chief software architect, " +
        "while also being the largest individual shareholder until May 2014.");
    Console.WriteLine("Linked Entities:");
    foreach (var entity in response.Value)
    {
        Console.WriteLine($"\tName: {entity.Name},\tID: {entity.Id},\tURL: {entity.Url}\tData Source: {entity.DataSource}");
        Console.WriteLine("\tMatches:");
        foreach (var match in entity.Matches)
        {
            Console.WriteLine($"\t\tText: {match.Text}");
            Console.WriteLine($"\t\tOffset: {match.Offset},\tLength: {match.Length},\tScore: {match.Score:F3}\n");
        }
    }
}
```

### <a name="output"></a>Dane wyjściowe

```console
Linked Entities:
        Name: Altair 8800,      ID: Altair 8800,        URL: https://en.wikipedia.org/wiki/Altair_8800  Data Source: Wikipedia
        Matches:
                Text: Altair 8800
                Offset: 116,    Length: 11,     Score: 0.777

        Name: Bill Gates,       ID: Bill Gates, URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates
                Offset: 25,     Length: 10,     Score: 0.555

                Text: Gates
                Offset: 161,    Length: 5,      Score: 0.555

        Name: Paul Allen,       ID: Paul Allen, URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen
                Offset: 40,     Length: 10,     Score: 0.533

        Name: Microsoft,        ID: Microsoft,  URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft
                Offset: 0,      Length: 9,      Score: 0.469

                Text: Microsoft
                Offset: 150,    Length: 9,      Score: 0.469

        Name: April 4,  ID: April 4,    URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4
                Offset: 54,     Length: 7,      Score: 0.248

        Name: BASIC,    ID: BASIC,      URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC
                Offset: 89,     Length: 5,      Score: 0.281
```

#### <a name="version-21"></a>[Wersja 2,1](#tab/version-2)

> [!NOTE]
> W wersji 2,1, konsolidacja jednostki jest uwzględniona w odpowiedzi NER.

Utwórz nową funkcję o nazwie `RecognizeEntitiesExample()`, która przybierze wcześniej utworzony klient i wywołaj funkcję [Entities ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.entities?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_Entities_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) . Wykonaj iterację w wynikach. Zwrócony obiekt [EntitiesResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.entitiesresult?view=azure-dotnet) będzie zawierać listę wykrytych jednostek w `Entities`, jeśli zakończy się pomyślnie, a `errorMessage`, jeśli nie. Dla każdej wykrytej jednostki Wydrukuj jej typ, podtyp, nazwę witryny Wikipedia (jeśli istnieją), a także lokalizacje w oryginalnym tekście.

[!code-csharp[Entity Recognition example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=entityRecognition)]

--- 


## <a name="key-phrase-extraction"></a>Wyodrębnianie kluczowych fraz

#### <a name="version-30-preview"></a>[Wersja 3,0-Preview](#tab/version-3)

Utwórz nową funkcję o nazwie `KeyPhraseExtractionExample()`, która przybierze wcześniej utworzony klient i wywołaj funkcję `ExtractKeyPhrases()`. Zwrócony obiekt `<Response<IReadOnlyCollection<string>>` będzie zawierać listę wykrytych fraz kluczy. Jeśli wystąpił błąd, spowoduje to zgłoszenie `RequestFailedException`.

```csharp
static void KeyPhraseExtractionExample(TextAnalyticsClient client)
{
    var response = client.ExtractKeyPhrases("My cat might need to see a veterinarian.");

    // Printing key phrases
    Console.WriteLine("Key phrases:");

    foreach (string keyphrase in response.Value)
    {
        Console.WriteLine($"\t{keyphrase}");
    }
}
```

### <a name="output"></a>Dane wyjściowe

```console
Key phrases:
    cat
    veterinarian
```

#### <a name="version-21"></a>[Wersja 2,1](#tab/version-2)

Utwórz nową funkcję o nazwie `KeyPhraseExtractionExample()`, która powoduje, że klient został utworzony wcześniej i wywołaj funkcję [frazy kluczowej ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.keyphrases?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_KeyPhrases_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) . Wynik będzie zawierać listę wykrytych fraz kluczy w `KeyPhrases`, jeśli się powiedzie, i `errorMessage`, jeśli nie. Drukuj wszystkie wykryte frazy kluczy.

[!code-csharp[Key phrase extraction example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=keyPhraseExtraction)]


### <a name="output"></a>Dane wyjściowe

```console
Key phrases:
    cat
    veterinarian
```

---
