---
title: 'Szybki start: Biblioteka klienta analizy tekstu dla języka C# | Microsoft Docs'
description: Rozpocznij pracę z biblioteką klienta analizy tekstu dla języka C#
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 03/12/2020
ms.author: aahi
ms.reviewer: assafi
ms.openlocfilehash: 6adce0ed6b5b5768bd9a489fced25ce439a33e0a
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79203426"
---
<a name="HOLTop"></a>

#### <a name="version-30-preview"></a>[Wersja 3.0-preview](#tab/version-3)

[Dokumentacja referencyjna wersji 3](https://aka.ms/azsdk-net-textanalytics-ref-docs) | [Kod źródłowy biblioteki w wersji 3](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics) | [Pakiet wersji 3 (NuGet)](https://www.nuget.org/packages/Azure.AI.TextAnalytics) | [Przykłady wersji 3](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics/samples)

#### <a name="version-21"></a>[Wersja 2.1](#tab/version-2)

[Dokumentacja referencyjna wersji 2](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/textanalytics?view=azure-dotnet-preview) | [Kod źródłowy biblioteki w wersji 2](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.TextAnalytics) | [Pakiet wersji 2 (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics/) | [Przykłady wersji 2](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples)

---

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [utwórz bezpłatnie](https://azure.microsoft.com/free/)
* [Środowisko IDE programu Visual Studio](https://visualstudio.microsoft.com/vs/)
* Gdy już będziesz mieć subskrypcję platformy Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="Tworzenie zasobu analizy tekstu"  target="_blank">utwórz zasób analizy tekstu <span class="docon docon-navigate-external x-hidden-focus"></span></a> w witrynie Azure Portal, aby uzyskać klucz i punkt końcowy. 
    * Klucz i punkt końcowy z utworzonego zasobu będą potrzebne do połączenia aplikacji z interfejsem API analizy tekstu. Tę czynność wykonasz w dalszej części przewodnika Szybki start.
    * W celu wypróbowania usługi możesz użyć warstwy cenowej Bezpłatna, a później zaktualizować ją do płatnej warstwy na potrzeby środowiska produkcyjnego.

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-new-net-core-application"></a>Tworzenie nowej aplikacji .NET Core

Za pomocą środowiska IDE programu Visual Studio utwórz nową aplikację konsolową platformy .NET Core. Spowoduje to utworzenie projektu „Hello world” z pojedynczym plikiem źródłowym C#: *program.cs*.

#### <a name="version-30-preview"></a>[Wersja 3.0-preview](#tab/version-3)

Zainstaluj bibliotekę klienta, klikając prawym przyciskiem myszy rozwiązanie w **Eksploratorze rozwiązań** i wybierając polecenie **Zarządzaj pakietami NuGet**. W otwartym menedżerze pakietów wybierz pozycję **Przeglądaj**, zaznacz opcję **Uwzględnij wersję wstępną** i wyszukaj ciąg `Azure.AI.TextAnalytics`. Wybierz wersję `1.0.0-preview.2`, a następnie pozycję **Zainstaluj**. Możesz również użyć [konsoli menedżera pakietów](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package).

> [!TIP]
> Czy chcesz wyświetlić cały plik z kodem przewodnika Szybki start od razu? Można go znaleźć [w witrynie GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/TextAnalytics/program.cs), która zawiera przykłady kodu z tego przewodnika Szybki start. 

#### <a name="version-21"></a>[Wersja 2.1](#tab/version-2)

Zainstaluj bibliotekę klienta, klikając prawym przyciskiem myszy rozwiązanie w **Eksploratorze rozwiązań** i wybierając polecenie **Zarządzaj pakietami NuGet**. W otwartym menedżerze pakietów wybierz pozycję **Przeglądaj** i wyszukaj ciąg `Microsoft.Azure.CognitiveServices.Language.TextAnalytics`. Kliknij pakiet, a następnie pozycję **Zainstaluj**. Możesz również użyć [konsoli menedżera pakietów](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package).

> [!TIP]
> Czy chcesz wyświetlić cały plik z kodem przewodnika Szybki start od razu? Można go znaleźć [w witrynie GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/samples/TextAnalytics/synchronous/Program.cs), która zawiera przykłady kodu z tego przewodnika Szybki start. 

---

#### <a name="version-30-preview"></a>[Wersja 3.0-preview](#tab/version-3)

Otwórz plik *program.cs* i dodaj następujące dyrektywy `using`:

```csharp
using System;
using System.Globalization;
using Azure.AI.TextAnalytics;
```

W klasie `Program` aplikacji utwórz zmienne dla punktu końcowego i klucza zasobu.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```csharp
private static readonly TextAnalyticsApiKeyCredential credentials = new TextAnalyticsApiKeyCredential("<replace-with-your-text-analytics-key-here>");
private static readonly Uri endpoint = new Uri("<replace-with-your-text-analytics-endpoint-here>");
```

Zastąp metodę `Main` aplikacji. Metody wywoływane w tym miejscu zdefiniujesz później.

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

#### <a name="version-21"></a>[Wersja 2.1](#tab/version-2)

Otwórz plik *program.cs* i dodaj następujące dyrektywy `using`:

[!code-csharp[Import directives](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=imports)]

W klasie `Program` aplikacji utwórz zmienne dla punktu końcowego i klucza zasobu. 

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```csharp
private static readonly string key = "<replace-with-your-text-analytics-key-here>";
private static readonly string endpoint = "<replace-with-your-text-analytics-endpoint-here>";
```

Zastąp metodę `Main` aplikacji. Metody wywoływane w tym miejscu zdefiniujesz później.

[!code-csharp[main method](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=main)]

---

## <a name="object-model"></a>Model obiektów

Klient analizy tekstu jest obiektem `TextAnalyticsClient`, który uwierzytelnia się na platformie Azure przy użyciu Twojego klucza i udostępnia funkcje akceptowania tekstu jako pojedynczych ciągów lub partii. Tekst do interfejsu API możesz wysyłać synchronicznie lub asynchronicznie. Obiekt odpowiedzi będzie zawierać informacje o analizie dla każdego wysłanego dokumentu. 

Jeśli używasz wersji `3.0-preview`, możesz użyć opcjonalnego wystąpienia `TextAnalyticsClientOptions`, aby zainicjować klienta z różnymi ustawieniami domyślnymi (na przykład domyślnym językiem lub wskazówką dotyczącą kraju). Uwierzytelnianie można także przeprowadzić przy użyciu tokenu usługi Azure Active Directory. 

## <a name="code-examples"></a>Przykłady kodu

* [Analiza tonacji](#sentiment-analysis)
* [Wykrywanie języka](#language-detection)
* [Rozpoznawanie jednostek nazwanych](#named-entity-recognition-ner)
* [Wykrywanie danych osobowych](#detect-personal-information)
* [Łączenie jednostek](#entity-linking)
* [Wyodrębnianie kluczowych fraz](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

#### <a name="version-30-preview"></a>[Wersja 3.0-preview](#tab/version-3)

Upewnij się, że wcześniejsza metoda main tworzy nowy obiekt klienta przy użyciu punktu końcowego i poświadczeń.

```csharp
var client = new TextAnalyticsClient(endpoint, credentials);
```

#### <a name="version-21"></a>[Wersja 2.1](#tab/version-2)

Utwórz nową klasę `ApiKeyServiceClientCredentials` na potrzeby przechowywania poświadczeń i dodawania ich do żądań klienta. Wewnątrz niej utwórz przesłonięcie dla metody `ProcessHttpRequestAsync()`, które dodaje klucz do nagłówka `Ocp-Apim-Subscription-Key`.

[!code-csharp[Client class](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=clientClass)]

Utwórz metodę służącą do tworzenia wystąpienia obiektu [TextAnalyticsClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-dotnet) przy użyciu punktu końcowego i obiektu `ApiKeyServiceClientCredentials` zawierającego klucz.

[!code-csharp[Client authentication](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=authentication)]

---

## <a name="sentiment-analysis"></a>Analiza tonacji

#### <a name="version-30-preview"></a>[Wersja 3.0-preview](#tab/version-3)

Utwórz nową funkcję o nazwie `SentimentAnalysisExample()`, która wywołuje funkcję `AnalyzeSentiment()` klienta utworzonego wcześniej. W przypadku powodzenia zwrócony obiekt `Response<DocumentSentiment>` będzie zawierać ocenę i etykietę tonacji dla całego dokumentu wejściowego, a także analizę tonacji dla każdego zdania. Jeśli wystąpił błąd, zostanie zgłoszony wyjątek `RequestFailedException`.

```csharp
static void SentimentAnalysisExample(TextAnalyticsClient client)
{
    string inputText = "I had the best day of my life. I wish you were there with me.";
    DocumentSentiment documentSentiment = client.AnalyzeSentiment(inputText);
    Console.WriteLine($"Document sentiment: {documentSentiment.Sentiment}\n");

    var si = new StringInfo(inputText);
    foreach (var sentence in documentSentiment.Sentences)
    {
        Console.WriteLine($"\tSentence [length {sentence.GraphemeLength}]");
        Console.WriteLine($"\tText: \"{si.SubstringByTextElements(sentence.GraphemeOffset, sentence.GraphemeLength)}\"");
        Console.WriteLine($"\tSentence sentiment: {sentence.Sentiment}");
        Console.WriteLine($"\tPositive score: {sentence.ConfidenceScores.Positive:0.00}");
        Console.WriteLine($"\tNegative score: {sentence.ConfidenceScores.Negative:0.00}");
        Console.WriteLine($"\tNeutral score: {sentence.ConfidenceScores.Neutral:0.00}\n");
    }
}
```

### <a name="output"></a>Dane wyjściowe

```console
Document sentiment: Positive

        Sentence [length 30]
        Text: "I had the best day of my life."
        Sentence sentiment: Positive
        Positive score: 1.00
        Negative score: 0.00
        Neutral score: 0.00

        Sentence [length 30]
        Text: "I wish you were there with me."
        Sentence sentiment: Neutral
        Positive score: 0.21
        Negative score: 0.02
        Neutral score: 0.77
```

#### <a name="version-21"></a>[Wersja 2.1](#tab/version-2)

Utwórz nową funkcję o nazwie `SentimentAnalysisExample()`, która wywołuje funkcję [Sentiment()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.sentiment?view=azure-dotnet) klienta utworzonego wcześniej. Zwrócony obiekt [SentimentResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.sentimentresult?view=azure-dotnet) będzie zawierać wartość `Score` tonacji w przypadku powodzenia lub komunikat `errorMessage` w przypadku niepowodzenia. 

Ocena bliska 0 wskazuje negatywną tonację, natomiast ocena bliższa 1 tonację pozytywną.

[!code-csharp[Sentiment analysis](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=sentiment)]

```console
Sentiment Score: 0.87
```

---

## <a name="language-detection"></a>Wykrywanie języka

#### <a name="version-30-preview"></a>[Wersja 3.0-preview](#tab/version-3)


Utwórz nową funkcję o nazwie `LanguageDetectionExample()`, która wywołuje funkcję `DetectLanguage()` klienta utworzonego wcześniej. Zwrócony obiekt `Response<DetectedLanguage>` będzie zawierać wykryty język wraz z jego nazwą i kodem ISO-6391. Jeśli wystąpił błąd, zostanie zgłoszony wyjątek `RequestFailedException`.

> [!Tip]
> W niektórych przypadkach może być trudno odróżnić języki w oparciu o dane wejściowe. Za pomocą parametru `countryHint` możesz podać 2-literowy kod kraju. Domyślnie interfejs API używa wartości „US” jako domyślnego ustawienia countryHint. Aby usunąć to zachowanie, możesz zresetować ten parametr, ustawiając wartość na pusty ciąg `countryHint = ""`. Aby ustawić inną wartość domyślną, ustaw właściwość `TextAnalyticsClientOptions.DefaultCountryHint` i przekaż ją podczas inicjowania klienta.

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

#### <a name="version-21"></a>[Wersja 2.1](#tab/version-2)

Utwórz nową funkcję o nazwie `languageDetectionExample()`, która wywołuje funkcję [DetectLanguage()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.detectlanguage?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_DetectLanguage_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) klienta utworzonego wcześniej. Zwrócony obiekt [LanguageResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.languageresult?view=azure-dotnet) będzie zawierać listę wykrytych języków w elemencie `DetectedLanguages` w przypadku powodzenia lub komunikat `errorMessage` w przypadku niepowodzenia. Wydrukuj pierwszy zwrócony język.

> [!Tip]
> W niektórych przypadkach może być trudno odróżnić języki w oparciu o dane wejściowe. Za pomocą parametru `countryHint` możesz podać 2-literowy kod kraju. Domyślnie interfejs API używa wartości „US” jako domyślnego ustawienia countryHint. Aby usunąć to zachowanie, możesz zresetować ten parametr, ustawiając wartość na pusty ciąg `countryHint = ""`.

[!code-csharp[Language Detection example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=languageDetection)]

### <a name="output"></a>Dane wyjściowe

```console
Language: English
```

---

## <a name="named-entity-recognition-ner"></a>Rozpoznawanie jednostek nazwanych

#### <a name="version-30-preview"></a>[Wersja 3.0-preview](#tab/version-3)


> [!NOTE]
> Nowości w wersji `3.0-preview`:
> * Funkcja rozpoznawania jednostek obejmuje teraz możliwość wykrywania danych osobowych w tekście.
> * Łączenie jednostek zostało oddzielone od rozpoznawania jednostek.


Utwórz nową funkcję o nazwie `EntityRecognitionExample()`, która wywołuje funkcję `RecognizeEntities()` klienta utworzonego wcześniej, a następnie wykonuje iterację po wynikach. Zwrócony obiekt `Response<IReadOnlyCollection<CategorizedEntity>>` będzie zawierać listę wykrytych jednostek. Jeśli wystąpił błąd, zostanie zgłoszony wyjątek `RequestFailedException`.

```csharp
static void EntityRecognitionExample(TextAnalyticsClient client)
{
    var response = client.RecognizeEntities("I had a wonderful trip to Seattle last week.");
    Console.WriteLine("Named Entities:");
    foreach (var entity in response.Value)
    {
        Console.WriteLine($"\tText: {entity.Text},\tCategory: {entity.Category},\tSub-Category: {entity.SubCategory}");
        Console.WriteLine($"\t\tLength: {entity.GraphemeLength},\tScore: {entity.ConfidenceScore:F2}\n");
    }
}
```

### <a name="output"></a>Dane wyjściowe

```console
Named Entities:
        Text: Seattle,  Category: Location,     Sub-Category: GPE
                Length: 7,      Score: 0.92

        Text: last week,        Category: DateTime,     Sub-Category: DateRange
                Length: 9,      Score: 0.80
```

## <a name="detect-personal-information"></a>Wykrywanie danych osobowych

Utwórz nową funkcję o nazwie `EntityPIIExample()`, która wywołuje funkcję `RecognizePiiEntities()` klienta utworzonego wcześniej, a następnie wykonuje iterację po wynikach. Podobnie jak w poprzedniej funkcji zwrócony obiekt `Response<IReadOnlyCollection<CategorizedEntity>>` będzie zawierać listę wykrytych jednostek. Jeśli wystąpił błąd, zostanie zgłoszony wyjątek `RequestFailedException`.

```csharp
static void EntityPIIExample(TextAnalyticsClient client)
{
    string inputText = "Insurance policy for SSN on file 123-12-1234 is here by approved.";
    var response = client.RecognizePiiEntities(inputText);
    Console.WriteLine("Personally Identifiable Information Entities:");
    foreach (var entity in response.Value)
    {
        Console.WriteLine($"\tText: {entity.Text},\tCategory: {entity.Category},\tSub-Category: {entity.SubCategory}");
        Console.WriteLine($"\t\tLength: {entity.GraphemeLength},\tScore: {entity.ConfidenceScore:F2}\n");
    }
}
```

### <a name="output"></a>Dane wyjściowe

```console
Personally Identifiable Information Entities:
        Text: 123-12-1234,      Category: U.S. Social Security Number (SSN),    Sub-Category:
                Length: 11,     Score: 0.85
```


## <a name="entity-linking"></a>Łączenie jednostek

Utwórz nową funkcję o nazwie `EntityLinkingExample()`, która wywołuje funkcję `RecognizeLinkedEntities()` klienta utworzonego wcześniej, a następnie wykonuje iterację po wynikach. Zwrócony obiekt `Response<IReadOnlyCollection<LinkedEntity>>` reprezentuje listę wykrytych jednostek. Jeśli wystąpił błąd, zostanie zgłoszony wyjątek `RequestFailedException`. Ponieważ połączone jednostki są jednoznacznie identyfikowane, wystąpienia tej samej jednostki są grupowane w ramach obiektu `LinkedEntity` jako lista obiektów `LinkedEntityMatch`.

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
        Console.WriteLine($"\tName: {entity.Name},\tID: {entity.DataSourceEntityId},\tURL: {entity.Url}\tData Source: {entity.DataSource}");
        Console.WriteLine("\tMatches:");
        foreach (var match in entity.Matches)
        {
            Console.WriteLine($"\t\tText: {match.Text}");
            Console.WriteLine($"\t\tLength: {match.GraphemeLength},\tScore: {match.ConfidenceScore:F2}\n");
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
                Length: 11,     Score: 0.78

        Name: Bill Gates,       ID: Bill Gates, URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates
                Length: 10,     Score: 0.55

                Text: Gates
                Length: 5,      Score: 0.55

        Name: Paul Allen,       ID: Paul Allen, URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen
                Length: 10,     Score: 0.53

        Name: Microsoft,        ID: Microsoft,  URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft
                Length: 9,      Score: 0.47

                Text: Microsoft
                Length: 9,      Score: 0.47

        Name: April 4,  ID: April 4,    URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4
                Length: 7,      Score: 0.25

        Name: BASIC,    ID: BASIC,      URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC
                Length: 5,      Score: 0.28
```

#### <a name="version-21"></a>[Wersja 2.1](#tab/version-2)

> [!NOTE]
> W wersji 2.1 odpowiedź rozpoznawania jednostek nazwanych obejmuje łączenie jednostek.

Utwórz nową funkcję o nazwie `RecognizeEntitiesExample()`, która wywołuje funkcję [Entities()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.entities?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_Entities_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) klienta utworzonego wcześniej. Wykonaj iterację po wynikach. Zwrócony obiekt [EntitiesResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.entitiesresult?view=azure-dotnet) będzie zawierać listę wykrytych jednostek w elemencie `Entities` w przypadku powodzenia lub komunikat `errorMessage` w przypadku niepowodzenia. Dla każdej wykrytej jednostki wydrukuj jej typ, podtypy i nazwę w witrynie Wikipedia (jeśli istnieją), a także lokalizacje w oryginalnym tekście.

[!code-csharp[Entity Recognition example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=entityRecognition)]

--- 


## <a name="key-phrase-extraction"></a>Wyodrębnianie kluczowych fraz

#### <a name="version-30-preview"></a>[Wersja 3.0-preview](#tab/version-3)

Utwórz nową funkcję o nazwie `KeyPhraseExtractionExample()`, która wywołuje funkcję `ExtractKeyPhrases()` klienta utworzonego wcześniej. Zwrócony obiekt `<Response<IReadOnlyCollection<string>>` będzie zawierać listę wykrytych fraz kluczowych. Jeśli wystąpił błąd, zostanie zgłoszony wyjątek `RequestFailedException`.

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

#### <a name="version-21"></a>[Wersja 2.1](#tab/version-2)

Utwórz nową funkcję o nazwie `KeyPhraseExtractionExample()`, która wywołuje funkcję [KeyPhrases()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.keyphrases?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_KeyPhrases_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) klienta utworzonego wcześniej. Wynik będzie zawierać listę wykrytych fraz kluczowych w elemencie `KeyPhrases` w przypadku powodzenia lub komunikat `errorMessage` w przypadku niepowodzenia. Wydrukuj wszystkie wykryte frazy kluczowe.

[!code-csharp[Key phrase extraction example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=keyPhraseExtraction)]


### <a name="output"></a>Dane wyjściowe

```console
Key phrases:
    cat
    veterinarian
```

---
