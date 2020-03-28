---
title: 'Szybki start: biblioteka klientów analizy tekstu dla języka C# | Dokumenty firmy Microsoft'
description: 'Wprowadzenie do biblioteki klienta analizy tekstu dla języka C #'
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 03/17/2020
ms.author: aahi
ms.reviewer: assafi
ms.openlocfilehash: 64eb19e43223c1953a7244f8fd29c48d085f1e96
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80116919"
---
<a name="HOLTop"></a>

#### <a name="version-30-preview"></a>[Wersja 3.0-preview](#tab/version-3)

[v3 Dokumentacja](https://aka.ms/azsdk-net-textanalytics-ref-docs) | [referencyjna v3 Kod](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics) | źródłowy biblioteki[v3 Pakiet (NuGet)](https://www.nuget.org/packages/Azure.AI.TextAnalytics) | [v3 Przykłady](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics/samples)

#### <a name="version-21"></a>[Wersja 2.1](#tab/version-2)

[v2 Dokumentacja](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/textanalytics?view=azure-dotnet-preview) | [referencyjna v2 Kod](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.TextAnalytics) | źródłowy biblioteki[v2 Pakiet (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics/) | [v2 Przykłady](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples)

---

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [utwórz bezpłatną subskrypcję platformy Azure](https://azure.microsoft.com/free/)
* Środowisko [IDE programu Visual Studio](https://visualstudio.microsoft.com/vs/)
* Po utworzeniu subskrypcji <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="platformy Azure utwórz"  target="_blank">zasób <span class="docon docon-navigate-external x-hidden-focus"></span> </a> analizy tekstu w witrynie Azure portal, aby uzyskać klucz i punkt końcowy.  Po wdrożeniu kliknij przycisk **Przejdź do zasobu**.
    * Potrzebny będzie klucz i punkt końcowy z zasobu, który tworzysz, aby połączyć aplikację z interfejsem API analizy tekstu. Wklej klucz i punkt końcowy do kodu poniżej w dalszej części przewodnika Szybki start.
    * Można użyć bezpłatnej warstwy`F0`cenowej ( ), aby wypróbować usługę, a następnie uaktualnić do warstwy płatnej dla produkcji.

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-new-net-core-application"></a>Tworzenie nowej aplikacji .NET Core

Korzystając z środowiska IDE programu Visual Studio, utwórz nową aplikację konsoli .NET Core. Spowoduje to utworzenie projektu "Hello World" z jednym plikiem źródłowym języka C#: *program.cs*.

#### <a name="version-30-preview"></a>[Wersja 3.0-preview](#tab/version-3)

Zainstaluj bibliotekę klienta, klikając prawym przyciskiem myszy rozwiązanie w **Eksploratorze rozwiązań** i wybierając **pozycję Zarządzaj pakietami NuGet**. W Menedżerze pakietów, który otwiera pozycję **Przeglądaj**, `Azure.AI.TextAnalytics`zaznacz pole wyboru Dołącz wydanie **wstępne**i wyszukaj . Wybierz `1.0.0-preview.3`wersję , a następnie **zainstaluj**. Można również użyć [konsoli Menedżera pakietów](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package).

> [!TIP]
> Chcesz wyświetlić cały plik kodu szybkiego startu naraz? Można go znaleźć [na GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/TextAnalytics/program.cs), który zawiera przykłady kodu w tym przewodniku Szybki start. 

#### <a name="version-21"></a>[Wersja 2.1](#tab/version-2)

Zainstaluj bibliotekę klienta, klikając prawym przyciskiem myszy rozwiązanie w **Eksploratorze rozwiązań** i wybierając **pozycję Zarządzaj pakietami NuGet**. W menedżerze pakietów, który się otworzy, wybierz pozycję **Przeglądaj** i wyszukaj `Microsoft.Azure.CognitiveServices.Language.TextAnalytics`. Kliknij na niego, a następnie **zainstaluj**. Można również użyć [konsoli Menedżera pakietów](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package).

> [!TIP]
> Chcesz wyświetlić cały plik kodu szybkiego startu naraz? Można go znaleźć [na GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/samples/TextAnalytics/synchronous/Program.cs), który zawiera przykłady kodu w tym przewodniku Szybki start. 

---

#### <a name="version-30-preview"></a>[Wersja 3.0-preview](#tab/version-3)

Otwórz plik *program.cs* i dodaj `using` następujące dyrektywy:

```csharp
using System;
using System.Globalization;
using Azure.AI.TextAnalytics;
```

W `Program` klasie aplikacji należy utworzyć zmienne dla klucza i punktu końcowego zasobu.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```csharp
private static readonly TextAnalyticsApiKeyCredential credentials = new TextAnalyticsApiKeyCredential("<replace-with-your-text-analytics-key-here>");
private static readonly Uri endpoint = new Uri("<replace-with-your-text-analytics-endpoint-here>");
```

Zastąp `Main` metodę aplikacji. Zdefiniujesz metody wywoływane tutaj później.

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

Otwórz plik *program.cs* i dodaj `using` następujące dyrektywy:

[!code-csharp[Import directives](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=imports)]

W `Program` klasie aplikacji należy utworzyć zmienne dla klucza i punktu końcowego zasobu. 

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```csharp
private static readonly string key = "<replace-with-your-text-analytics-key-here>";
private static readonly string endpoint = "<replace-with-your-text-analytics-endpoint-here>";
```

Zastąp `Main` metodę aplikacji. Zdefiniujesz metody wywoływane tutaj później.

[!code-csharp[main method](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=main)]

---

## <a name="object-model"></a>Model obiektu

Klient analizy tekstu `TextAnalyticsClient` jest obiektem, który uwierzytelnia się na platformie Azure przy użyciu klucza i udostępnia funkcje do akceptowania tekstu jako pojedyncze ciągi lub jako partia. Tekst można wysyłać synchronicznie lub asynchronicznie do interfejsu API. Obiekt odpowiedzi będzie zawierał informacje analityczne dla każdego wysyłanego dokumentu. 

Jeśli używasz wersji, `3.0-preview`możesz użyć `TextAnalyticsClientOptions` opcjonalnego wystąpienia, aby zainicjować klienta z różnymi ustawieniami domyślnymi (na przykład domyślnym językiem lub wskazówką dotyczącą kraju). Można również uwierzytelnić przy użyciu tokenu usługi Azure Active Directory. 

## <a name="code-examples"></a>Przykłady kodu

* [Analiza tonacji](#sentiment-analysis)
* [Wykrywanie języka](#language-detection)
* [Rozpoznawanie nazwanych jednostek](#named-entity-recognition-ner)
* [Wykrywanie danych osobowych](#detect-personal-information)
* [Łączenie jednostek](#entity-linking)
* [Wyodrębnianie fraz kluczowych](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Uwierzytelnij klienta

#### <a name="version-30-preview"></a>[Wersja 3.0-preview](#tab/version-3)

Upewnij się, że metoda główna z wcześniejszych tworzy nowy obiekt klienta z punktem końcowym i poświadczenia.

```csharp
var client = new TextAnalyticsClient(endpoint, credentials);
```

#### <a name="version-21"></a>[Wersja 2.1](#tab/version-2)

Utwórz `ApiKeyServiceClientCredentials` nową klasę do przechowywania poświadczeń i dodaj je do żądań klienta. W nim utwórz zastąpienie, `ProcessHttpRequestAsync()` które dodaje klucz `Ocp-Apim-Subscription-Key` do nagłówka.

[!code-csharp[Client class](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=clientClass)]

Utwórz metodę tworzenia wystąpienia [TextAnalyticsClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-dotnet) obiektu z punktu `ApiKeyServiceClientCredentials` końcowego i obiektu zawierającego klucz.

[!code-csharp[Client authentication](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=authentication)]

---

## <a name="sentiment-analysis"></a>Analiza tonacji

#### <a name="version-30-preview"></a>[Wersja 3.0-preview](#tab/version-3)

Utwórz nową `SentimentAnalysisExample()` funkcję o nazwie, która przyjmuje klienta, który został utworzony wcześniej i wywołać jego `AnalyzeSentiment()` funkcję. Zwrócony `Response<DocumentSentiment>` obiekt będzie zawierał etykietę tonacji i wynik całego dokumentu wejściowego, a także analizę tonacji dla każdego zdania, jeśli zakończy się pomyślnie. Jeśli wystąpił błąd, zostanie wyrzucił . `RequestFailedException`

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

Utwórz nową `SentimentAnalysisExample()` funkcję o nazwie, która przyjmuje klienta, który został utworzony wcześniej, i wywołaj jego [sentiment()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.sentiment?view=azure-dotnet) funkcji. Zwrócony [SentimentResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.sentimentresult?view=azure-dotnet) obiekt będzie `Score` zawierać sentyment, `errorMessage` jeśli zakończy się pomyślnie, a jeśli nie. 

Wynik bliski 0 oznacza negatywne nastroje, podczas gdy wynik bliższy 1 wskazuje na pozytywne nastroje.

[!code-csharp[Sentiment analysis](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=sentiment)]

```console
Sentiment Score: 0.87
```

---

## <a name="language-detection"></a>Wykrywanie języka

#### <a name="version-30-preview"></a>[Wersja 3.0-preview](#tab/version-3)


Utwórz nową `LanguageDetectionExample()` funkcję o nazwie, która przyjmuje klienta, który został utworzony wcześniej i wywołać jego `DetectLanguage()` funkcję. Zwrócony `Response<DetectedLanguage>` obiekt będzie zawierał wykryty język wraz z jego nazwą i kodem ISO-6391. Jeśli wystąpił błąd, zostanie wyrzucił . `RequestFailedException`

> [!Tip]
> W niektórych przypadkach może być trudne do odróżnienia języków na podstawie danych wejściowych. Za pomocą `countryHint` tego parametru można określić 2-literowy kod kraju. Domyślnie interfejs API używa "US" jako domyślnego krajuHint, aby usunąć to zachowanie, możesz `countryHint = ""`zresetować ten parametr, ustawiając tę wartość na pusty ciąg . Aby ustawić inną wartość `TextAnalyticsClientOptions.DefaultCountryHint` domyślną, należy ustawić właściwość i przekazać ją podczas inicjowania klienta.

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

Utwórz nową `languageDetectionExample()` funkcję o nazwie, która przyjmuje klienta, który został utworzony wcześniej, i wywołaj jego [detectlanguage()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.detectlanguage?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_DetectLanguage_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) funkcja. Zwrócony [LanguageResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.languageresult?view=azure-dotnet) obiekt będzie zawierać listę wykrytych języków `DetectedLanguages` w `errorMessage` jeśli zakończy się pomyślnie, a jeśli nie. Wydrukuj pierwszy zwrócony język.

> [!Tip]
> W niektórych przypadkach może być trudne do odróżnienia języków na podstawie danych wejściowych. Za pomocą `countryHint` tego parametru można określić 2-literowy kod kraju. Domyślnie interfejs API używa "US" jako domyślnego krajuHint, aby usunąć to zachowanie, możesz `countryHint = ""` zresetować ten parametr, ustawiając tę wartość na pusty ciąg .

[!code-csharp[Language Detection example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=languageDetection)]

### <a name="output"></a>Dane wyjściowe

```console
Language: English
```

---

## <a name="named-entity-recognition-ner"></a>Rozpoznawanie nazwanych jednostek (NER)

#### <a name="version-30-preview"></a>[Wersja 3.0-preview](#tab/version-3)


> [!NOTE]
> Nowość `3.0-preview`w wersji :
> * Rozpoznawanie jednostek obejmuje teraz możliwość wykrywania informacji osobistych w tekście.
> * Łączenie jednostek jest teraz oddzielone od rozpoznawania jednostek.


Utwórz nową `EntityRecognitionExample()` funkcję o nazwie, która przyjmuje `RecognizeEntities()` klienta, który został utworzony wcześniej, wywołać jego funkcję i iteracji za pośrednictwem wyników. Zwrócony `Response<IReadOnlyCollection<CategorizedEntity>>` obiekt będzie zawierał listę wykrytych jednostek. Jeśli wystąpił błąd, zostanie wyrzucił . `RequestFailedException`

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

Utwórz nową `EntityPIIExample()` funkcję o nazwie, która przyjmuje `RecognizePiiEntities()` klienta, który został utworzony wcześniej, wywołać jego funkcję i iteracji za pośrednictwem wyników. Podobnie jak poprzednia `Response<IReadOnlyCollection<CategorizedEntity>>` funkcja zwrócony obiekt będzie zawierał listę wykrytych jednostek. Jeśli wystąpił błąd, zostanie wyrzucił . `RequestFailedException`

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

Utwórz nową `EntityLinkingExample()` funkcję o nazwie, która przyjmuje `RecognizeLinkedEntities()` klienta, który został utworzony wcześniej, wywołać jego funkcję i iteracji za pośrednictwem wyników. Zwrócony `Response<IReadOnlyCollection<LinkedEntity>>` reprezentuje listę wykrytych jednostek. Jeśli wystąpił błąd, zostanie wyrzucił . `RequestFailedException` Ponieważ połączone jednostki są jednoznacznie identyfikowane, wystąpienia tej `LinkedEntity` samej encji `LinkedEntityMatch` są grupowane pod obiektem jako lista obiektów.

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
> W wersji 2.1 łączenie jednostek jest zawarte w odpowiedzi NER.

Utwórz nową `RecognizeEntitiesExample()` funkcję o nazwie, która przyjmuje klienta, który został utworzony wcześniej, i wywołaj jego [entities()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.entities?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_Entities_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) funkcji. Iterować przez wyniki. Zwrócony [EntitiesResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.entitiesresult?view=azure-dotnet) obiekt będzie zawierać listę wykrytych jednostek w `Entities` if successful, a `errorMessage` jeśli nie. Dla każdej wykrytej jednostki wydrukuj jej typ, podtyp, nazwę Wikipedii (jeśli istnieją), a także lokalizacje w oryginalnym tekście.

[!code-csharp[Entity Recognition example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=entityRecognition)]

--- 


## <a name="key-phrase-extraction"></a>Wyodrębnianie kluczowych fraz

#### <a name="version-30-preview"></a>[Wersja 3.0-preview](#tab/version-3)

Utwórz nową `KeyPhraseExtractionExample()` funkcję o nazwie, która przyjmuje klienta, który został utworzony wcześniej i wywołać jego `ExtractKeyPhrases()` funkcję. Zwrócony `<Response<IReadOnlyCollection<string>>` obiekt będzie zawierał listę wykrytych fraz kluczowych. Jeśli wystąpił błąd, zostanie wyrzucił . `RequestFailedException`

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

Utwórz nową `KeyPhraseExtractionExample()` funkcję o nazwie, która przyjmuje klienta, który został utworzony wcześniej i wywołać jego [KeyPhrases()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.keyphrases?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_KeyPhrases_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) funkcja. Wynik będzie zawierać listę wykrytych fraz `KeyPhrases` kluczowych w if `errorMessage` successful, a jeśli nie. Drukowanie wykrytych fraz kluczowych.

[!code-csharp[Key phrase extraction example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=keyPhraseExtraction)]


### <a name="output"></a>Dane wyjściowe

```console
Key phrases:
    cat
    veterinarian
```

---
