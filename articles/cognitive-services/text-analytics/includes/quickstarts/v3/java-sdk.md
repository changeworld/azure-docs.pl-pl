---
title: 'Szybki Start: analiza tekstu wersja kliencka klienta dla języka Java | Microsoft Docs'
description: Rozpocznij pracę z biblioteką kliencką programu v3 analiza tekstu dla środowiska Java.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 01/15/2020
ms.author: aahi
ms.reviewer: tasharm, assafi
ms.openlocfilehash: a7882f1337e990f828bea15cdc2b9ad72de44f18
ms.sourcegitcommit: d9ec6e731e7508d02850c9e05d98d26c4b6f13e6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/20/2020
ms.locfileid: "76281185"
---
<a name="HOLTop"></a>

[Dokumentacja referencyjna](https://aka.ms/azsdk-java-textanalytics-ref-docs) |  | pakietu [kodu źródłowego biblioteki](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/textanalytics/azure-ai-textanalytics) [(Maven)](https://oss.sonatype.org/#nexus-search;quick~com.azure) [ | ](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics/src/samples/java/com/azure/ai/textanalytics)

> [!NOTE]
> * Ten przewodnik Szybki Start używa wersji `3.0-preview` biblioteki klienckiej analiza tekstu, która obejmuje publiczną wersję zapoznawczą ulepszonego [Analiza tonacji](../../../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) i [nazwanego rozpoznawania jednostek (ner)](../../../how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features).
> * Kod w tym artykule używa metod synchronicznych i niezabezpieczonych magazynów poświadczeń z przyczyn uproszczenia. W przypadku scenariuszy produkcyjnych zaleca się użycie wsadowych metod asynchronicznych w celu zapewnienia wydajności i skalowalności. Na przykład wywoływanie `SentimentBatchAsync()`, a nie `Sentiment()`.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/)
* [Zestaw Java Development Kit](https://www.oracle.com/technetwork/java/javase/downloads/index.html) (JDK) z wersją 8 lub nowszą

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-text-analytics-azure-resource"></a>Tworzenie zasobu analiza tekstu platformy Azure

[!INCLUDE [text-analytics-resource-creation](../resource-creation.md)]

### <a name="create-a-new-maven-project"></a>Utwórz nowy projekt Maven

Dodaj następującą zależność analizy tekstu do projektu. Ta wersja zależności używa `3.0-preview` wersji interfejs API analizy tekstu. 

```xml
<dependencies>
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-ai-textanalytics</artifactId>
        <version>1.0.0-beta.1</version>
    </dependency>
</dependencies>
```

Utwórz nowy plik Java w następującym katalogu: `\src\main\java`.

Otwórz plik Java i Dodaj następujące instrukcje `import`:

```java
import com.azure.ai.textanalytics.models.AnalyzeSentimentResult;
import com.azure.ai.textanalytics.models.DetectLanguageResult;
import com.azure.ai.textanalytics.models.DetectedLanguage;
import com.azure.ai.textanalytics.models.ExtractKeyPhraseResult;
import com.azure.ai.textanalytics.models.LinkedEntity;
import com.azure.ai.textanalytics.models.LinkedEntityMatch;
import com.azure.ai.textanalytics.models.NamedEntity;
import com.azure.ai.textanalytics.models.RecognizeEntitiesResult;
import com.azure.ai.textanalytics.models.RecognizeLinkedEntitiesResult;
import com.azure.ai.textanalytics.models.RecognizePiiEntitiesResult;
import com.azure.ai.textanalytics.models.TextSentiment;
import com.azure.ai.textanalytics.TextAnalyticsClientBuilder;
import com.azure.ai.textanalytics.TextAnalyticsClient;
import java.util.List;
```

W pliku Java Dodaj nową klasę i Dodaj klucz i punkt końcowy zasobu platformy Azure, jak pokazano poniżej.

[!INCLUDE [text-analytics-find-resource-information](../../find-azure-resource-info.md)]

```java
public class TextAnalyticsSample {
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
* [Analiza tonacji](#sentiment-analysis) (publiczna wersja zapoznawcza)
* [Wykrywanie języka](#language-detection)
* [Rozpoznawanie jednostek nazwanych](#named-entity-recognition-public-preview) (publiczna wersja zapoznawcza)
* [Rozpoznawanie jednostek nazwanych — informacje osobiste](#named-entity-recognition---personal-information-public-preview) (publiczna wersja zapoznawcza)
* [Łączenie jednostek](#entity-linking)
* [Wyodrębnianie kluczowych fraz](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

Utwórz metodę tworzenia wystąpienia obiektu `TextAnalyticsClient` przy użyciu `KEY` i `ENDPOINT` utworzonego powyżej.

```java
static TextAnalyticsClient authenticateClient(String subscriptionKey, String endpoint) {
    return new TextAnalyticsClientBuilder()
    .subscriptionKey(subscriptionKey)
    .endpoint(endpoint)
    .buildClient();
}
```

W metodzie `main()` programu Wywołaj metodę uwierzytelniania w celu utworzenia wystąpienia klienta.

## <a name="sentiment-analysis"></a>Analiza opinii

> [!NOTE]
> Poniższy kod dotyczy tonacji Analysis v3, który jest w publicznej wersji zapoznawczej.

Utwórz nową funkcję o nazwie `sentimentAnalysisExample()`, która przybierze wcześniej utworzony klient i wywołaj funkcję `analyzeSentiment()`. Zwrócony obiekt `AnalyzeSentimentResult` będzie zawierał `documentSentiment` i `sentenceSentiments` Jeśli to się powiedzie, lub `errorMessage`, jeśli nie. 

```java
static void sentimentAnalysisExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "I had the best day of my life. I wish you were there with me.";

    AnalyzeSentimentResult sentimentResult = client.analyzeSentiment(text);
    TextSentiment documentSentiment = sentimentResult.getDocumentSentiment();
    System.out.printf(
        "Recognized TextSentiment: %s, Positive Score: %.2f, Neutral Score: %.2f, Negative Score: %.2f.%n",
        documentSentiment.getTextSentimentClass(),
        documentSentiment.getPositiveScore(),
        documentSentiment.getNeutralScore(),
        documentSentiment.getNegativeScore());

    List<TextSentiment> sentiments = sentimentResult.getSentenceSentiments();
    for (TextSentiment textSentiment : sentiments) {
        System.out.printf(
            "Recognized Sentence TextSentiment: %s, Positive Score: %.2f, Neutral Score: %.2f, Negative Score: %.2f.%n",
            textSentiment.getTextSentimentClass(),
            textSentiment.getPositiveScore(),
            textSentiment.getNeutralScore(),
            textSentiment.getNegativeScore());
    }
}
```

### <a name="output"></a>Dane wyjściowe

```console
Recognized TextSentiment: positive, Positive Score: 1.00, Neutral Score: 0.00, Negative Score: 0.00.
Recognized Sentence TextSentiment: positive, Positive Score: 1.00, Neutral Score: 0.00, Negative Score: 0.00.
Recognized Sentence TextSentiment: neutral, Positive Score: 0.21, Neutral Score: 0.77, Negative Score: 0.02.
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

    DetectLanguageResult detectLanguageResult = client.detectLanguage(text, "US");
    DetectedLanguage detectedDocumentLanguage = detectLanguageResult.getPrimaryLanguage();
    System.out.printf("Language: %s, ISO 6391 Name: %s, Score: %s.%n",
        detectedDocumentLanguage.getName(),
        detectedDocumentLanguage.getIso6391Name(),
        detectedDocumentLanguage.getScore());
}
```

### <a name="output"></a>Dane wyjściowe

```console
Language: French, ISO 6391 Name: fr, Score: 1.0.
```
## <a name="named-entity-recognition-public-preview"></a>Rozpoznawanie jednostek nazwanych (publiczna wersja zapoznawcza)

> [!NOTE]
> Poniższy kod dotyczy nazwanego rozpoznawania jednostek v3, które jest w publicznej wersji zapoznawczej.

Utwórz nową funkcję o nazwie `recognizeEntitiesExample()`, która przybierze wcześniej utworzony klient i wywołaj funkcję `recognizeEntities()`. Zwrócony obiekt `RecognizeEntitiesResult` będzie zawierać listę `NamedEntity`, jeśli się powiedzie, lub `errorMessage` Jeśli nie.

```java
static void recognizeEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analysed.
    String text = "I had a wonderful trip to Seattle last week.";
    
    RecognizeEntitiesResult recognizeEntitiesResult = client.recognizeEntities(text);

    for (NamedEntity entity : recognizeEntitiesResult.getNamedEntities()) {
        System.out.printf(
            "Recognized NamedEntity Text: %s, Type: %s, Subtype: %s, Offset: %s, Length: %s, Score: %.3f.%n",
            entity.getText(),
            entity.getType(),
            entity.getSubtype() == null || entity.getSubtype().isEmpty() ? "N/A" : entity.getSubtype(),
            entity.getOffset(),
            entity.getLength(),
            entity.getScore());
    }
}
```

### <a name="output"></a>Dane wyjściowe

```console
Recognized NamedEntity Text: Seattle, Type: Location, Subtype: N/A, Offset: 26, Length: 7, Score: 0.806.
Recognized NamedEntity Text: last week, Type: DateTime, Subtype: DateRange, Offset: 34, Length: 9, Score: 0.800.
```

## <a name="named-entity-recognition---personal-information-public-preview"></a>Rozpoznawanie jednostek nazwanych — informacje osobiste (publiczna wersja zapoznawcza)

> [!NOTE]
> Poniższy kod służy do wykrywania informacji osobistych przy użyciu nazwanego rozpoznawania jednostek v3, który jest w publicznej wersji zapoznawczej.

Utwórz nową funkcję o nazwie `recognizePIIEntitiesExample()`, która przybierze wcześniej utworzony klient i wywołaj funkcję `recognizePiiEntities()`. Zwrócony obiekt `RecognizePiiEntitiesResult` będzie zawierać listę `NamedEntity`, jeśli się powiedzie, lub `errorMessage` Jeśli nie. 

```java
static void recognizePIIEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analysed.
    String text = "Insurance policy for SSN on file 123-12-1234 is here by approved.";
    
    RecognizePiiEntitiesResult recognizePIIEntitiesResult = client.recognizePiiEntities(text);

    for (NamedEntity entity : recognizePIIEntitiesResult.getNamedEntities()) {
        System.out.printf(
            "Personally Identifiable Information Entities Text: %s, Type: %s, Subtype: %s, Offset: %s, Length: %s, Score: %s.%n",
            entity.getText(),
            entity.getType(),
            entity.getSubtype() == null || entity.getSubtype().isEmpty() ? "N/A" : entity.getSubtype(),
            entity.getOffset(),
            entity.getLength(),
            entity.getScore());
    }
}
```

### <a name="output"></a>Dane wyjściowe

```console
Personally Identifiable Information Entities
Text: 123-12-1234, Type: U.S. Social Security Number (SSN), Subtype: N/A, Offset: 33, Length: 11, Score: 0.85.
```

## <a name="entity-linking"></a>Łączenie jednostek

Utwórz nową funkcję o nazwie `recognizeLinkedEntitiesExample()`, która przybierze wcześniej utworzony klient i wywołaj funkcję `recognizeLinkedEntities()`. Zwrócony obiekt `RecognizeLinkedEntitiesResult` będzie zawierać listę `LinkedEntity`, jeśli się powiedzie, lub `errorMessage` Jeśli nie. Ponieważ połączone jednostki są jednoznacznie identyfikowane, wystąpienia tej samej jednostki są pogrupowane pod obiektem `LinkedEntity` jako lista obiektów `LinkedEntityMatch`.

```java
static void recognizeLinkedEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analysed.
    String text = "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, " +
            "to develop and sell BASIC interpreters for the Altair 8800. " +
            "During his career at Microsoft, Gates held the positions of chairman, " +
            "chief executive officer, president and chief software architect, " +
            "while also being the largest individual shareholder until May 2014.";
    
    RecognizeLinkedEntitiesResult recognizeLinkedEntitiesResult = client.recognizeLinkedEntities(text);

    System.out.printf("Linked Entities:%n");
    for (LinkedEntity linkedEntity : recognizeLinkedEntitiesResult.getLinkedEntities()) {
        System.out.printf("Name: %s, ID: %s, URL: %s, Data Source: %s.%n",
            linkedEntity.getName(),
            linkedEntity.getId(),
            linkedEntity.getUrl(),
            linkedEntity.getDataSource());
        System.out.printf("tMatches:%n");
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
tMatches:
Text: Altair 8800, Offset: 11, Length: 116, Score: 0.65.
Name: Bill Gates, ID: Bill Gates, URL: https://en.wikipedia.org/wiki/Bill_Gates, Data Source: Wikipedia.
tMatches:
Text: Bill Gates, Offset: 10, Length: 25, Score: 0.24.
Text: Gates, Offset: 5, Length: 161, Score: 0.24.
Name: Paul Allen, ID: Paul Allen, URL: https://en.wikipedia.org/wiki/Paul_Allen, Data Source: Wikipedia.
tMatches:
Text: Paul Allen, Offset: 10, Length: 40, Score: 0.17.
Name: Microsoft, ID: Microsoft, URL: https://en.wikipedia.org/wiki/Microsoft, Data Source: Wikipedia.
tMatches:
Text: Microsoft, Offset: 9, Length: 0, Score: 0.20.
Text: Microsoft, Offset: 9, Length: 150, Score: 0.20.
Name: April 4, ID: April 4, URL: https://en.wikipedia.org/wiki/April_4, Data Source: Wikipedia.
tMatches:
Text: April 4, Offset: 7, Length: 54, Score: 0.14.
Name: BASIC, ID: BASIC, URL: https://en.wikipedia.org/wiki/BASIC, Data Source: Wikipedia.
tMatches:
Text: BASIC, Offset: 5, Length: 89, Score: 0.05.
```
## <a name="key-phrase-extraction"></a>Wyodrębnianie kluczowych fraz

Utwórz nową funkcję o nazwie `extractKeyPhrasesExample()`, która przybierze wcześniej utworzony klient i wywołaj funkcję `extractKeyPhrases()`. Zwrócony obiekt `ExtractKeyPhraseResult` będzie zawierać listę kluczowych fraz w przypadku powodzenia lub `errorMessage`, jeśli nie.

```java
static void extractKeyPhrasesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "My cat might need to see a veterinarian.";
    
    ExtractKeyPhraseResult keyPhraseResult = client.extractKeyPhrases(text);

    for (String keyPhrase : keyPhraseResult.getKeyPhrases()) {
        System.out.printf("Recognized Phrases: %s.%n", keyPhrase);
    }
}
```

### <a name="output"></a>Dane wyjściowe

```console
Recognized Phrases: cat.
Recognized Phrases: veterinarian.
```
