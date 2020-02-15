---
title: 'Szybki Start: analiza tekstu wersja kliencka klienta dla języka Java | Microsoft Docs'
description: Rozpocznij pracę z biblioteką kliencką programu v3 analiza tekstu dla środowiska Java.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 02/11/2020
ms.author: aahi
ms.reviewer: tasharm, assafi
ms.openlocfilehash: eeaef4a9970609c43c03cd784436796a6f8af174
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2020
ms.locfileid: "77211405"
---
<a name="HOLTop"></a>

[Dokumentacja referencyjna](https://aka.ms/azsdk-java-textanalytics-ref-docs) |  | pakietu [kodu źródłowego biblioteki](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/textanalytics/azure-ai-textanalytics) [(Maven)](https://oss.sonatype.org/#nexus-search;quick~com.azure) [ | ](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics/src/samples/java/com/azure/ai/textanalytics)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/)
* [Zestaw Java Development Kit](https://www.oracle.com/technetwork/java/javase/downloads/index.html) (JDK) z wersją 8 lub nowszą


[!INCLUDE [text-analytics-resource-creation](resource-creation.md)]

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-new-maven-project"></a>Utwórz nowy projekt Maven

Dodaj następującą zależność analizy tekstu do projektu. Ta wersja zależności używa `3.0-preview` wersji interfejs API analizy tekstu. 

```xml
<dependencies>
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-ai-textanalytics</artifactId>
        <version>1.0.0-beta.2</version>
    </dependency>
</dependencies>
```

Utwórz nowy plik Java w następującym katalogu: `\src\main\java`.

Otwórz plik Java i Dodaj następujące instrukcje `import`:

```java
import com.azure.ai.textanalytics.models.*;
import com.azure.ai.textanalytics.TextAnalyticsClientBuilder;
import com.azure.ai.textanalytics.TextAnalyticsClient;
```

W pliku Java Dodaj nową klasę i Dodaj klucz i punkt końcowy zasobu platformy Azure, jak pokazano poniżej.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```java
public class TextAnalyticsSamples {
    private static String KEY = "<replace-with-your-text-analytics-key-here>";
    private static String ENDPOINT = "<replace-with-your-text-analytics-endpoint-here>";
}
```

Dodaj następującą metodę główną do klasy. Metody wywoływane tutaj zostaną zdefiniowane później.

```java
public static void main(String[] args) {

    TextAnalyticsClient client = authenticateClient(KEY, ENDPOINT);

    sentimentAnalysisExample(client);
    detectLanguageExample(client);
    recognizeEntitiesExample(client);
    recognizePIIEntitiesExample(client);
    recognizeLinkedEntitiesExample(client);
    extractKeyPhrasesExample(client);
}
```

## <a name="object-model"></a>Model obiektów

Klient analiza tekstu jest obiektem `TextAnalyticsClient`, który jest uwierzytelniany na platformie Azure przy użyciu klucza, i udostępnia funkcje, aby akceptować tekst jako pojedyncze ciągi lub jako partię. Możesz wysyłać tekst do interfejsu API synchronicznie lub asynchronicznie. Obiekt Response będzie zawierać informacje o analizie dla każdego wysyłanego dokumentu. 

## <a name="code-examples"></a>Przykłady kodu

* [Uwierzytelnianie klienta](#authenticate-the-client)
* [analiza tonacji](#sentiment-analysis) 
* [Wykrywanie języka](#language-detection)
* [Rozpoznawanie jednostek nazwanych](#named-entity-recognition-ner) 
* [Łączenie jednostek](#entity-linking)
* [Wyodrębnianie kluczowych fraz](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

Utwórz metodę tworzenia wystąpienia obiektu `TextAnalyticsClient` przy użyciu `KEY` i `ENDPOINT` utworzonego powyżej.

```java
static TextAnalyticsClient authenticateClient(String key, String endpoint) {
    return new TextAnalyticsClientBuilder()
        .apiKey(new TextAnalyticsApiKeyCredential(key))
        .endpoint(endpoint)
        .buildClient();
}
```

W metodzie `main()` programu Wywołaj metodę uwierzytelniania w celu utworzenia wystąpienia klienta.

## <a name="sentiment-analysis"></a>Analiza tonacji

Utwórz nową funkcję o nazwie `sentimentAnalysisExample()`, która przybierze wcześniej utworzony klient i wywołaj funkcję `analyzeSentiment()`. Zwrócony obiekt `AnalyzeSentimentResult` będzie zawierał `documentSentiment` i `sentenceSentiments` Jeśli to się powiedzie, lub `errorMessage`, jeśli nie. 

```java
static void sentimentAnalysisExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "I had the best day of my life. I wish you were there with me.";

    DocumentSentiment documentSentiment = client.analyzeSentiment(text);
        System.out.printf(
            "Recognized document sentiment: %s, positive score: %.2f, neutral score: %.2f, negative score: %.2f.%n",
            documentSentiment.getSentiment(),
            documentSentiment.getSentimentScores().getPositive(),
            documentSentiment.getSentimentScores().getNeutral(),
            documentSentiment.getSentimentScores().getNegative());

        for (SentenceSentiment sentenceSentiment : documentSentiment.getSentences()) {
            System.out.printf(
                "Recognized sentence sentiment: %s, positive score: %.2f, neutral score: %.2f, negative score: %.2f.%n",
                sentenceSentiment.getSentiment(),
                sentenceSentiment.getSentimentScores().getPositive(),
                sentenceSentiment.getSentimentScores().getNeutral(),
                sentenceSentiment.getSentimentScores().getNegative());
        }
}
```

### <a name="output"></a>Dane wyjściowe

```console
Recognized document sentiment: positive, Positive Score: 1.00, Neutral Score: 0.00, Negative Score: 0.00.
Recognized sentence sentiment: positive, positive score: 1.00, neutral score: 0.00, negative score: 0.00.
Recognized sentence sentiment: neutral, positive score: 0.21, neutral score: 0.77, negative score: 0.02.
```
## <a name="language-detection"></a>Wykrywanie języka

Utwórz nową funkcję o nazwie `detectLanguageExample()`, która przybierze wcześniej utworzony klient i wywołaj funkcję `detectLanguage()`. Zwrócony obiekt `DetectLanguageResult` będzie zawierał wykryty język podstawowy, listę innych języków wykrytych w przypadku powodzenia lub `errorMessage`, jeśli nie.

> [!Tip]
> W niektórych przypadkach może być trudno odróżnić Języki w oparciu o dane wejściowe. Można użyć parametru `countryHint`, aby określić 2-literowy kod kraju. Domyślnie interfejs API używa "US" jako domyślnego countryHint, aby usunąć to zachowanie, można zresetować ten parametr, ustawiając tę wartość na pusty ciąg `countryHint = ""`. Aby ustawić inną wartość domyślną, należy ustawić właściwość `TextAnalyticsClientOptions.DefaultCountryHint` i przekazać ją podczas inicjowania klienta.

```java
static void detectLanguageExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "Ce document est rédigé en Français.";

    DetectedLanguage detectedLanguage = client.detectLanguage(text);
    System.out.printf("Detected primary language: %s, ISO 6391 name: %s, score: %.2f.%n",
        detectedLanguage.getName(),
        detectedLanguage.getIso6391Name(),
        detectedLanguage.getScore());
}
```

### <a name="output"></a>Dane wyjściowe

```console
Detected primary language: French, ISO 6391 name: fr, score: 1.00.
```
## <a name="named-entity-recognition-ner"></a>Rozpoznawanie jednostek nazwanych (NER)

> [!NOTE]
> W wersji `3.0-preview`:
> * NER obejmuje oddzielne metody wykrywania informacji osobistych. 
> * Łączenie jednostek to oddzielne żądanie niż NER.

Utwórz nową funkcję o nazwie `recognizeEntitiesExample()`, która przybierze wcześniej utworzony klient i wywołaj funkcję `recognizeEntities()`. Zwrócony obiekt `RecognizeEntitiesResult` będzie zawierać listę `NamedEntity`, jeśli się powiedzie, lub `errorMessage` Jeśli nie.

```java
static void recognizeEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "I had a wonderful trip to Seattle last week.";

    for (CategorizedEntity entity : client.recognizeEntities(text)) {
        System.out.printf(
            "Recognized entity: %s, entity category: %s, entity sub-category: %s, offset: %s, length: %s, score: %.2f.%n",
            entity.getText(),
            entity.getCategory(),
            entity.getSubCategory() == null || entity.getSubCategory().isEmpty() ? "N/A" : entity.getSubCategory(),
            entity.getOffset(),
            entity.getLength(),
            entity.getScore());
    }
}
```

### <a name="output"></a>Dane wyjściowe

```console
Recognized entity: Seattle, entity category: Location, entity sub-category: GPE, offset: 26, length: 7, score: 0.92.
Recognized entity: last week, entity category: DateTime, entity sub-category: DateRange, offset: 34, length: 9, score: 0.80.
```

## <a name="using-ner-to-recognize-personal-information"></a>Rozpoznawanie informacji osobistych za pomocą NER

Utwórz nową funkcję o nazwie `recognizePIIEntitiesExample()`, która przybierze wcześniej utworzony klient i wywołaj funkcję `recognizePiiEntities()`. Zwrócony obiekt `RecognizePiiEntitiesResult` będzie zawierać listę `NamedEntity`, jeśli się powiedzie, lub `errorMessage` Jeśli nie. 

```java
static void recognizePIIEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "Insurance policy for SSN on file 123-12-1234 is here by approved.";

    for (PiiEntity entity : client.recognizePiiEntities(text)) {
        System.out.printf(
            "Recognized personal identifiable information entity: %s, entity category: %s, entity sub-category: %s, offset: %s, length: %s, score: %.2f.%n",
            entity.getText(),
            entity.getCategory(),
            entity.getSubCategory() == null || entity.getSubCategory().isEmpty() ? "N/A" : entity.getSubCategory(),
            entity.getOffset(),
            entity.getLength(),
            entity.getScore());
    }
}
```

### <a name="output"></a>Dane wyjściowe

```console
Recognized personal identifiable information entity: 123-12-1234, entity category: U.S. Social Security Number (SSN), entity sub-category: N/A, offset: 33, length: 11, score: 0.85.
```

## <a name="entity-linking"></a>Łączenie jednostek

Utwórz nową funkcję o nazwie `recognizeLinkedEntitiesExample()`, która przybierze wcześniej utworzony klient i wywołaj funkcję `recognizeLinkedEntities()`. Zwrócony obiekt `RecognizeLinkedEntitiesResult` będzie zawierać listę `LinkedEntity`, jeśli się powiedzie, lub `errorMessage` Jeśli nie. Ponieważ połączone jednostki są jednoznacznie identyfikowane, wystąpienia tej samej jednostki są pogrupowane pod obiektem `LinkedEntity` jako lista obiektów `LinkedEntityMatch`.

```java
static void recognizeLinkedEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, " +
            "to develop and sell BASIC interpreters for the Altair 8800. " +
            "During his career at Microsoft, Gates held the positions of chairman, " +
            "chief executive officer, president and chief software architect, " +
            "while also being the largest individual shareholder until May 2014.";

    System.out.printf("Linked Entities:%n");
    for (LinkedEntity linkedEntity : client.recognizeLinkedEntities(text)) {
        System.out.printf("Name: %s, ID: %s, URL: %s, Data Source: %s.%n",
                linkedEntity.getName(),
                linkedEntity.getId(),
                linkedEntity.getUrl(),
                linkedEntity.getDataSource());
        System.out.printf("Matches:%n");
        for (LinkedEntityMatch linkedEntityMatch : linkedEntity.getLinkedEntityMatches()) {
            System.out.printf("Text: %s, Offset: %s, Length: %s, Score: %.2f.%n",
                    linkedEntityMatch.getText(),
                    linkedEntityMatch.getOffset(),
                    linkedEntityMatch.getLength(),
                    linkedEntityMatch.getScore());
        }
    }
}
```

### <a name="output"></a>Dane wyjściowe

```console
Linked Entities:
Name: Altair 8800, ID: Altair 8800, URL: https://en.wikipedia.org/wiki/Altair_8800, Data Source: Wikipedia.
Matches:
Text: Altair 8800, Offset: 11, Length: 116, Score: 0.78.
Name: Bill Gates, ID: Bill Gates, URL: https://en.wikipedia.org/wiki/Bill_Gates, Data Source: Wikipedia.
Matches:
Text: Bill Gates, Offset: 10, Length: 25, Score: 0.55.
Text: Gates, Offset: 5, Length: 161, Score: 0.55.
Name: Paul Allen, ID: Paul Allen, URL: https://en.wikipedia.org/wiki/Paul_Allen, Data Source: Wikipedia.
Matches:
Text: Paul Allen, Offset: 10, Length: 40, Score: 0.53.
Name: Microsoft, ID: Microsoft, URL: https://en.wikipedia.org/wiki/Microsoft, Data Source: Wikipedia.
Matches:
Text: Microsoft, Offset: 9, Length: 0, Score: 0.47.
Text: Microsoft, Offset: 9, Length: 150, Score: 0.47.
Name: April 4, ID: April 4, URL: https://en.wikipedia.org/wiki/April_4, Data Source: Wikipedia.
Matches:
Text: April 4, Offset: 7, Length: 54, Score: 0.25.
Name: BASIC, ID: BASIC, URL: https://en.wikipedia.org/wiki/BASIC, Data Source: Wikipedia.
Matches:
Text: BASIC, Offset: 5, Length: 89, Score: 0.28.
```
## <a name="key-phrase-extraction"></a>Wyodrębnianie kluczowych fraz

Utwórz nową funkcję o nazwie `extractKeyPhrasesExample()`, która przybierze wcześniej utworzony klient i wywołaj funkcję `extractKeyPhrases()`. Zwrócony obiekt `ExtractKeyPhraseResult` będzie zawierać listę kluczowych fraz w przypadku powodzenia lub `errorMessage`, jeśli nie.

```java
static void extractKeyPhrasesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "My cat might need to see a veterinarian.";

    System.out.printf("Recognized phrases: %n");
    for (String keyPhrase : client.extractKeyPhrases(text)) {
        System.out.printf("%s%n", keyPhrase);
    }
}
```

### <a name="output"></a>Dane wyjściowe

```console
Recognized phrases: 
cat
veterinarian
```
