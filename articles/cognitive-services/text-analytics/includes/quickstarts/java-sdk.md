---
title: 'Szybki start: Biblioteka klienta analizy tekstu w wersji 3 dla języka Java | Microsoft Docs'
description: Rozpocznij pracę z biblioteką klienta analizy tekstu w wersji 3 dla języka Java.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 03/12/2020
ms.author: aahi
ms.reviewer: tasharm, assafi, sumeh
ms.openlocfilehash: 11092b74c0256d298dece0f909d8d7dd241e7b13
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/14/2020
ms.locfileid: "79371335"
---
<a name="HOLTop"></a>

[Dokumentacja referencyjna](https://aka.ms/azsdk-java-textanalytics-ref-docs) | [Kod źródłowy biblioteki](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/textanalytics/azure-ai-textanalytics) | [Pakiet](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/1.0.0-beta.3) | [Przykłady](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics/src/samples/java/com/azure/ai/textanalytics)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [utwórz bezpłatnie](https://azure.microsoft.com/free/)
* Zestaw [Java Development Kit](https://www.oracle.com/technetwork/java/javase/downloads/index.html) (JDK) w wersji 8 lub nowszej
* Gdy już będziesz mieć subskrypcję platformy Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="Tworzenie zasobu analizy tekstu"  target="_blank">utwórz zasób analizy tekstu <span class="docon docon-navigate-external x-hidden-focus"></span></a> w witrynie Azure Portal, aby uzyskać klucz i punkt końcowy. 
    * Klucz i punkt końcowy z utworzonego zasobu będą potrzebne do połączenia aplikacji z interfejsem API analizy tekstu. Tę czynność wykonasz w dalszej części przewodnika Szybki start.
    * W celu wypróbowania usługi możesz użyć warstwy cenowej Bezpłatna, a później zaktualizować ją do płatnej warstwy na potrzeby środowiska produkcyjnego.

## <a name="setting-up"></a>Konfigurowanie

### <a name="add-the-client-library"></a>Dodawanie biblioteki klienta

Utwórz projekt Maven w preferowanym środowisku IDE lub w środowisku deweloperskim. Następnie dodaj następującą zależność do pliku  *pom.xml* projektu. Składnię implementacji [dla innych narzędzi kompilacji](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/1.0.0-beta.3) można znaleźć w Internecie.

```xml
<dependencies>
     <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-ai-textanalytics</artifactId>
        <version>1.0.0-beta.3</version>
    </dependency>
</dependencies>
```

> [!TIP]
> Czy chcesz wyświetlić cały plik z kodem przewodnika Szybki start od razu? Można go znaleźć [w witrynie GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/TextAnalytics/TextAnalyticsSamples.java), która zawiera przykłady kodu z tego przewodnika Szybki start. 

Utwórz plik Java o nazwie `TextAnalyticsSamples.java`. Otwórz ten plik i dodaj następujące instrukcje `import`:

```java
import com.azure.ai.textanalytics.models.*;
import com.azure.ai.textanalytics.TextAnalyticsClientBuilder;
import com.azure.ai.textanalytics.TextAnalyticsClient;
```

W pliku Java dodaj nową klasę oraz klucz i punkt końcowy zasobu platformy Azure, jak pokazano poniżej.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```java
public class TextAnalyticsSamples {
    private static String KEY = "<replace-with-your-text-analytics-key-here>";
    private static String ENDPOINT = "<replace-with-your-text-analytics-endpoint-here>";
}
```

Do klasy dodaj następującą metodę main. Metody wywoływane w tym miejscu zdefiniujesz później.

```java
public static void main(String[] args) {
    //You will create these methods later in the quickstart.
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

Klient analizy tekstu jest obiektem `TextAnalyticsClient`, który jest uwierzytelniany na platformie Azure przy użyciu klucza, i udostępnia funkcje umożliwiające akceptowanie tekstu jako pojedynczych ciągów lub jako partii. Tekst może być wysyłany do interfejsu API synchronicznie lub asynchronicznie. Obiekt odpowiedzi będzie zawierać informacje o analizie dla każdego wysyłanego dokumentu. 

## <a name="code-examples"></a>Przykłady kodu

* [Uwierzytelnianie klienta](#authenticate-the-client)
* [Analiza tonacji](#sentiment-analysis) 
* [Wykrywanie języka](#language-detection)
* [Rozpoznawanie jednostek nazwanych](#named-entity-recognition-ner) 
* [Łączenie jednostek](#entity-linking)
* [Wyodrębnianie kluczowych fraz](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

Utwórz metodę umożliwiającą utworzenie wystąpienia obiektu `TextAnalyticsClient` przy użyciu klucza i punktu końcowego zasobu analizy tekstu.

```java
static TextAnalyticsClient authenticateClient(String key, String endpoint) {
    return new TextAnalyticsClientBuilder()
        .apiKey(new TextAnalyticsApiKeyCredential(key))
        .endpoint(endpoint)
        .buildClient();
}
```

W metodzie `main()` programu wywołaj metodę uwierzytelniania w celu utworzenia wystąpienia klienta.

## <a name="sentiment-analysis"></a>Analiza tonacji

Utwórz nową funkcję o nazwie `sentimentAnalysisExample()`, która wywołuje funkcję `analyzeSentiment()` klienta utworzonego wcześniej. Zwrócony obiekt `AnalyzeSentimentResult` będzie zawierał obiekty `documentSentiment` i `sentenceSentiments`, jeśli wywołanie się powiedzie, lub obiekt `errorMessage`, jeśli wywołanie się nie powiedzie. 

```java
static void sentimentAnalysisExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "I had the best day of my life. I wish you were there with me.";

    DocumentSentiment documentSentiment = client.analyzeSentiment(text);
    System.out.printf(
        "Recognized document sentiment: %s, positive score: %s, neutral score: %s, negative score: %s.%n",
        documentSentiment.getSentiment(),
        documentSentiment.getConfidenceScores().getPositive(),
        documentSentiment.getConfidenceScores().getNeutral(),
        documentSentiment.getConfidenceScores().getNegative());

    for (SentenceSentiment sentenceSentiment : documentSentiment.getSentences()) {
        System.out.printf(
            "Recognized sentence sentiment: %s, positive score: %s, neutral score: %s, negative score: %s.%n",
            sentenceSentiment.getSentiment(),
            sentenceSentiment.getConfidenceScores().getPositive(),
            sentenceSentiment.getConfidenceScores().getNeutral(),
            sentenceSentiment.getConfidenceScores().getNegative());
    }
}
```

### <a name="output"></a>Dane wyjściowe

```console
Recognized document sentiment: positive, positive score: 1.0, neutral score: 0.0, negative score: 0.0.
Recognized sentence sentiment: positive, positive score: 1.0, neutral score: 0.0, negative score: 0.0.
Recognized sentence sentiment: neutral, positive score: 0.21, neutral score: 0.77, negative score: 0.02.
```

## <a name="language-detection"></a>Wykrywanie języka

Utwórz nową funkcję o nazwie `detectLanguageExample()`, która wywołuje funkcję `detectLanguage()` klienta utworzonego wcześniej. W przypadku powodzenia zwrócony obiekt `DetectLanguageResult` będzie zawierał wykryty język podstawowy i listę innych wykrytych języków. W przeciwnym razie będzie zawierać obiekt `errorMessage`.

> [!Tip]
> W niektórych przypadkach może być trudno odróżnić języki w oparciu o dane wejściowe. Za pomocą parametru `countryHint` możesz podać 2-literowy kod kraju. Domyślnie interfejs API używa wartości „US” jako domyślnego ustawienia countryHint. Aby usunąć to zachowanie, możesz zresetować ten parametr, ustawiając wartość na pusty ciąg `countryHint = ""`. Aby ustawić inną wartość domyślną, ustaw właściwość `TextAnalyticsClientOptions.DefaultCountryHint` i przekaż ją podczas inicjowania klienta.

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
## <a name="named-entity-recognition-ner"></a>Rozpoznawanie jednostek nazwanych

> [!NOTE]
> W wersji `3.0-preview`:
> * Rozpoznawanie jednostek nazwanych obejmuje oddzielne metody wykrywania danych osobowych. 
> * Łączenie jednostek to żądanie inne niż rozpoznawanie jednostek nazwanych.

Utwórz nową funkcję o nazwie `recognizeEntitiesExample()`, która wywołuje funkcję `recognizeEntities()` klienta utworzonego wcześniej. W przypadku powodzenia zwrócony obiekt `RecognizeEntitiesResult` będzie zawierał listę obiektów `NamedEntity`. W przeciwnym razie będzie zawierać obiekt `errorMessage`.

```java
static void recognizeEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "I had a wonderful trip to Seattle last week.";

    for (CategorizedEntity entity : client.recognizeEntities(text)) {
        System.out.printf(
            "Recognized entity: %s, entity category: %s, entity sub-category: %s, score: %s.%n",
            entity.getText(),
            entity.getCategory(),
            entity.getSubCategory(),
            entity.getConfidenceScore());
    }
}
```

### <a name="output"></a>Dane wyjściowe

```console
Recognized entity: Seattle, entity category: Location, entity sub-category: GPE, score: 0.92.
Recognized entity: last week, entity category: DateTime, entity sub-category: DateRange, score: 0.8.
```

## <a name="using-ner-to-recognize-personal-information"></a>Wykrywanie danych osobowych za pomocą rozpoznawania jednostek nazwanych

Utwórz nową funkcję o nazwie `recognizePIIEntitiesExample()`, która wywołuje funkcję `recognizePiiEntities()` klienta utworzonego wcześniej. W przypadku powodzenia zwrócony obiekt `RecognizePiiEntitiesResult` będzie zawierał listę obiektów `NamedEntity`. W przeciwnym razie będzie zawierać obiekt `errorMessage`. 

```java
static void recognizePIIEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "Insurance policy for SSN on file 123-12-1234 is here by approved.";

    for (PiiEntity entity : client.recognizePiiEntities(text)) {
        System.out.printf(
            "Recognized personal identifiable information entity: %s, entity category: %s, %nentity sub-category: %s, score: %s.%n",
            entity.getText(),
            entity.getCategory(),
            entity.getSubCategory(),
            entity.getConfidenceScore());
    }
}
```

### <a name="output"></a>Dane wyjściowe

```console
Recognized personal identifiable information entity: 123-12-1234, entity category: U.S. Social Security Number (SSN), 
entity sub-category: null, score: 0.85.
```

## <a name="entity-linking"></a>Łączenie jednostek

Utwórz nową funkcję o nazwie `recognizeLinkedEntitiesExample()`, która wywołuje funkcję `recognizeLinkedEntities()` klienta utworzonego wcześniej. W przypadku powodzenia zwrócony obiekt `RecognizeLinkedEntitiesResult` będzie zawierał listę obiektów `LinkedEntity`. W przeciwnym razie będzie zawierać obiekt `errorMessage`. Ponieważ połączone jednostki są jednoznacznie identyfikowane, wystąpienia tej samej jednostki są grupowane w ramach obiektu `LinkedEntity` jako lista obiektów `LinkedEntityMatch`.

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
                linkedEntity.getDataSourceEntityId(),
                linkedEntity.getUrl(),
                linkedEntity.getDataSource());
        System.out.printf("Matches:%n");
        for (LinkedEntityMatch linkedEntityMatch : linkedEntity.getLinkedEntityMatches()) {
            System.out.printf("Text: %s, Score: %.2f%n",
                    linkedEntityMatch.getText(),
                    linkedEntityMatch.getConfidenceScore());
        }
    }
}
```

### <a name="output"></a>Dane wyjściowe

```console
Linked Entities:
Name: Altair 8800, ID: Altair 8800, URL: https://en.wikipedia.org/wiki/Altair_8800, Data Source: Wikipedia.
Matches:
Text: Altair 8800, Score: 0.78
Name: Bill Gates, ID: Bill Gates, URL: https://en.wikipedia.org/wiki/Bill_Gates, Data Source: Wikipedia.
Matches:
Text: Bill Gates, Score: 0.55
Text: Gates, Score: 0.55
Name: Paul Allen, ID: Paul Allen, URL: https://en.wikipedia.org/wiki/Paul_Allen, Data Source: Wikipedia.
Matches:
Text: Paul Allen, Score: 0.53
Name: Microsoft, ID: Microsoft, URL: https://en.wikipedia.org/wiki/Microsoft, Data Source: Wikipedia.
Matches:
Text: Microsoft, Score: 0.47
Text: Microsoft, Score: 0.47
Name: April 4, ID: April 4, URL: https://en.wikipedia.org/wiki/April_4, Data Source: Wikipedia.
Matches:
Text: April 4, Score: 0.25
Name: BASIC, ID: BASIC, URL: https://en.wikipedia.org/wiki/BASIC, Data Source: Wikipedia.
Matches:
Text: BASIC, Score: 0.28
```
## <a name="key-phrase-extraction"></a>Wyodrębnianie kluczowych fraz

Utwórz nową funkcję o nazwie `extractKeyPhrasesExample()`, która wywołuje funkcję `extractKeyPhrases()` klienta utworzonego wcześniej. W przypadku powodzenia zwrócony obiekt `ExtractKeyPhraseResult` będzie zawierał listę fraz kluczowych. W przeciwnym razie będzie zawierać obiekt `errorMessage`.

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
