---
title: 'Szybki start: Biblioteka klienta analizy tekstu w wersji 3 dla platformy Node.js | Microsoft Docs'
description: Rozpocznij pracę z biblioteką klienta analizy tekstu w wersji 3 dla platformy Node.js.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 03/06/2020
ms.author: aahi
ms.reviewer: sumeh, assafi
ms.openlocfilehash: 4ce2da3070105f6e098373108164b6f590d423c6
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/07/2020
ms.locfileid: "78925347"
---
<a name="HOLTop"></a>

#### <a name="version-30-preview"></a>[Wersja 3.0-preview](#tab/version-3)

[Dokumentacja referencyjna wersji 3](https://aka.ms/azsdk-js-textanalytics-ref-docs) | [Kod źródłowy biblioteki w wersji 3](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics) | [Pakiet wersji 3 (NPM)](https://www.npmjs.com/package/@azure/ai-text-analytics) | [Przykłady wersji 3](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics/samples)


#### <a name="version-21"></a>[Wersja 2.1](#tab/version-2)

[Dokumentacja referencyjna wersji 2](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics) | [Kod źródłowy biblioteki w wersji 2](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/cognitiveServicesTextAnalytics) | [Pakiet wersji 2 (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-textanalytics) | [Przykłady wersji 2](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/)

---

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [utwórz bezpłatnie](https://azure.microsoft.com/free/)
* Bieżąca wersja platformy [Node.js](https://nodejs.org/).
* Gdy już będziesz mieć subskrypcję platformy Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="Tworzenie zasobu analizy tekstu"  target="_blank">utwórz zasób analizy tekstu <span class="docon docon-navigate-external x-hidden-focus"></span></a> w witrynie Azure Portal, aby uzyskać klucz i punkt końcowy. 
    * Klucz i punkt końcowy z utworzonego zasobu będą potrzebne do połączenia aplikacji z interfejsem API analizy tekstu. Tę czynność wykonasz w dalszej części przewodnika Szybki start.
    * W celu wypróbowania usługi możesz użyć warstwy cenowej Bezpłatna, a później zaktualizować ją do płatnej warstwy na potrzeby środowiska produkcyjnego.

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-new-nodejs-application"></a>Tworzenie nowej aplikacji Node.js

> [!NOTE]
> Tę wersję biblioteki klienta analizy tekstu możesz także uruchomić [w przeglądarce](https://github.com/Azure/azure-sdk-for-js/blob/master/documentation/Bundling.md).

W oknie konsoli (na przykład cmd, PowerShell lub Bash) utwórz nowy katalog dla aplikacji i przejdź do niego. 

```console
mkdir myapp && cd myapp
```

Uruchom polecenie `npm init`, aby utworzyć aplikację Node przy użyciu pliku `package.json`. 

```console
npm init
```
### <a name="install-the-client-library"></a>Instalowanie biblioteki klienta

#### <a name="version-30-preview"></a>[Wersja 3.0-preview](#tab/version-3)

Zainstaluj pakiety NPM `@azure/ai-text-analytics`:

```console
npm install --save @azure/ai-text-analytics
```

#### <a name="version-21"></a>[Wersja 2.1](#tab/version-2)

Zainstaluj pakiety NPM `@azure/cognitiveservices-textanalytics`:

```console
npm install --save @azure/cognitiveservices-textanalytics
```

Plik `package.json` aplikacji zostanie zaktualizowany przy użyciu zależności.

Utwórz plik o nazwie `index.js` i dodaj następujące biblioteki:

---

Plik `package.json` aplikacji zostanie zaktualizowany przy użyciu zależności.
Utwórz plik o nazwie `index.js` i dodaj następujące biblioteki:

#### <a name="version-30-preview"></a>[Wersja 3.0-preview](#tab/version-3)

```javascript
"use strict";

const { TextAnalyticsClient, TextAnalyticsApiKeyCredential } = require("@azure/ai-text-analytics");
```

#### <a name="version-21"></a>[Wersja 2.1](#tab/version-2)

```javascript
"use strict";

const { TextAnalyticsClient, CognitiveServicesCredential } = require("@azure/cognitiveservices-textanalytics");
```
---

Utwórz zmienne dla klucza i punktu końcowego platformy Azure zasobu.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```javascript
const key = '<paste-your-text-analytics-key-here>';
const endpoint = `<paste-your-text-analytics-endpoint-here>`;
```

## <a name="object-model"></a>Model obiektów

Klient analizy tekstu jest obiektem `TextAnalyticsClient` uwierzytelnianym na platformie Azure przy użyciu klucza. Klient udostępnia kilka metod analizowania tekstu, jako pojedynczego ciągu lub zbiorczo.

Tekst jest wysyłany do interfejsu API jako lista elementów `documents`, które są obiektami `dictionary` zawierającymi kombinację atrybutów `id`, `text` i `language` w zależności od używanej metody. Atrybut `text` przechowuje tekst, który ma zostać przeanalizowany, w języku źródłowym (określonym przez atrybut `language`), a atrybut `id` może mieć dowolną wartość. 

Obiekt odpowiedzi to lista zawierająca informacje o analizie dla każdego dokumentu. 

## <a name="code-examples"></a>Przykłady kodu

* [Uwierzytelnianie klienta](#client-authentication)
* [Analiza tonacji](#sentiment-analysis) 
* [Wykrywanie języka](#language-detection)
* [Rozpoznawanie jednostek nazwanych](#named-entity-recognition-ner)
* [Łączenie jednostek](#entity-linking)
* [Wyodrębnianie kluczowych fraz](#key-phrase-extraction)

## <a name="client-authentication"></a>Uwierzytelnianie klienta

#### <a name="version-30-preview"></a>[Wersja 3.0-preview](#tab/version-3)

Utwórz nowy obiekt `TextAnalyticsClient`, podając klucz i punkt końcowy jako parametry.

```javascript
const textAnalyticsClient = new TextAnalyticsClient(endpoint,  new TextAnalyticsApiKeyCredential(key));
```

#### <a name="version-21"></a>[Wersja 2.1](#tab/version-2)

Utwórz nowy obiekt [TextAnalyticsClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient), podając parametry `credentials` i `endpoint`.

[!code-javascript[Authentication and client creation](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=authentication)]

---

## <a name="sentiment-analysis"></a>Analiza tonacji

#### <a name="version-30-preview"></a>[Wersja 3.0-preview](#tab/version-3)

Utwórz tablicę ciągów zawierającą dokument, który chcesz przeanalizować. Wywołaj metodę `analyzeSentiment()` klienta i pobierz zwrócony obiekt `SentimentBatchResult`. Wykonaj iterację po liście wyników i wydrukuj identyfikator każdego dokumentu oraz tonację na poziomie dokumentu wraz ze współczynnikiem ufności. Wynik zawiera tonację na poziomie zdania wraz z przesunięciami, długością i współczynnikami ufności każdego dokumentu.

```javascript
async function sentimentAnalysis(client){

    const sentimentInput = [
        "I had the best day of my life. I wish you were there with me."
    ];
    const sentimentResult = await client.analyzeSentiment(sentimentInput);

    sentimentResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tDocument Sentiment: ${document.sentiment}`);
        console.log(`\tDocument Scores:`);
        console.log(`\t\tPositive: ${document.sentimentScores.positive.toFixed(2)} \tNegative: ${document.sentimentScores.negative.toFixed(2)} \tNeutral: ${document.sentimentScores.neutral.toFixed(2)}`);
        console.log(`\tSentences Sentiment(${document.sentences.length}):`);
        document.sentences.forEach(sentence => {
            console.log(`\t\tSentence sentiment: ${sentence.sentiment}`)
            console.log(`\t\tSentences Scores:`);
            console.log(`\t\tPositive: ${sentence.sentimentScores.positive.toFixed(2)} \tNegative: ${sentence.sentimentScores.negative.toFixed(2)} \tNeutral: ${sentence.sentimentScores.neutral.toFixed(2)}`);
            console.log(`\t\tLength: ${sentence.length}, Offset: ${sentence.offset}`);
        })
    });
}
sentimentAnalysis(textAnalyticsClient)
```

Uruchom kod przy użyciu polecenia `node index.js` w oknie konsoli.

### <a name="output"></a>Dane wyjściowe

```console
ID: 0
        Document Sentiment: positive
        Document Scores:
                Positive: 1.00  Negative: 0.00  Neutral: 0.00
        Sentences Sentiment(2):
                Sentence sentiment: positive
                Sentences Scores:
                Positive: 1.00  Negative: 0.00  Neutral: 0.00
                Length: 30, Offset: 0
                Sentence sentiment: neutral
                Sentences Scores:
                Positive: 0.21  Negative: 0.02  Neutral: 0.77
                Length: 30, Offset: 31
```

#### <a name="version-21"></a>[Wersja 2.1](#tab/version-2)

Utwórz listę obiektów słownika obejmującą dokumenty, które chcesz przeanalizować. Wywołaj metodę [sentiment()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#sentiment-models-textanalyticsclientsentimentoptionalparams-) klienta i pobierz zwrócony obiekt [SentimentBatchResult](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/sentimentbatchresult). Wykonaj iterację po liście wyników i wydrukuj identyfikator każdego dokumentu oraz ocenę tonacji. Ocena bliższa 0 wskazuje negatywną tonację, natomiast ocena bliższa 1 tonację pozytywną.

[!code-javascript[Sentiment analysis](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=sentimentAnalysis)]

Uruchom kod przy użyciu polecenia `node index.js` w oknie konsoli.

### <a name="output"></a>Dane wyjściowe

```console
[ { id: '1', score: 0.87 } ]
[ { id: '2', score: 0.11 } ]
[ { id: '3', score: 0.44 } ]
[ { id: '4', score: 1.00 } ]
```

---

## <a name="language-detection"></a>Wykrywanie języka

#### <a name="version-30-preview"></a>[Wersja 3.0-preview](#tab/version-3)

Utwórz tablicę ciągów zawierającą dokument, który chcesz przeanalizować. Wywołaj metodę `detectLanguage()` klienta i pobierz zwrócony obiekt `DetectLanguageResultCollection`. Następnie wykonaj iterację po wynikach i wydrukuj identyfikator każdego dokumentu wraz z językiem podstawowym.

```javascript
async function languageDetection(client) {

    const languageInputArray = [
        "Ce document est rédigé en Français."
    ];
    const languageResult = await client.detectLanguage(languageInputArray);

    languageResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tPrimary Language ${document.primaryLanguage.name}`)
    });
}
languageDetection(textAnalyticsClient);
```

Uruchom kod przy użyciu polecenia `node index.js` w oknie konsoli.

### <a name="output"></a>Dane wyjściowe

```console
ID: 0
        Primary Language French
```

#### <a name="version-21"></a>[Wersja 2.1](#tab/version-2)

Utwórz listę obiektów słownika obejmującą dokumenty. Wywołaj metodę [detectLanguage()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#detectlanguage-models-textanalyticsclientdetectlanguageoptionalparams-) klienta i pobierz zwrócony obiekt [LanguageBatchResult](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/languagebatchresult). Następnie wykonaj iterację po wynikach i wydrukuj identyfikator każdego dokumentu oraz język.

[!code-javascript[Language detection](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=languageDetection)]

Uruchom kod przy użyciu polecenia `node index.js` w oknie konsoli.

### <a name="output"></a>Dane wyjściowe

```console
Document ID: 1 , Language: English
Document ID: 2 , Language: Spanish
Document ID: 3 , Language: Chinese_Simplified
```

---

## <a name="named-entity-recognition-ner"></a>Rozpoznawanie jednostek nazwanych

#### <a name="version-30-preview"></a>[Wersja 3.0-preview](#tab/version-3)

> [!NOTE]
> W wersji `3.0-preview`:
> * Rozpoznawanie jednostek nazwanych obejmuje oddzielne metody wykrywania danych osobowych. 
> * Łączenie jednostek to żądanie inne niż rozpoznawanie jednostek nazwanych.

Utwórz tablicę ciągów zawierającą dokument, który chcesz przeanalizować. Wywołaj metodę `recognizeEntities()` klienta i pobierz obiekt `RecognizeEntitiesResult`. Wykonaj iterację po liście wyników i wydrukuj nazwę jednostki, jej typ, podtyp, przesunięcie, długość i ocenę.

```javascript
async function entityRecognition(client){

    const entityInputs = [
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800",
        "La sede principal de Microsoft se encuentra en la ciudad de Redmond, a 21 kilómetros de Seattle."
    ];
    const entityResults = await client.recognizeEntities(entityInputs);

    entityResults.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.text} \tCategory: ${entity.category} \tSubcategory: ${entity.subCategory ? entity.subCategory : "N/A"}`);
            console.log(`\tOffset: ${entity.offset}, Length: ${entity.length} \tScore: ${entity.score}`);
        });
    });
}
entityRecognition(textAnalyticsClient);
```

Uruchom kod przy użyciu polecenia `node index.js` w oknie konsoli.

### <a name="output"></a>Dane wyjściowe

```console
Document ID: 0
        Name: Microsoft         Category: Organization  Subcategory: N/A
        Offset: 0, Length: 9    Score: 1
        Name: Bill Gates        Category: Person        Subcategory: N/A
        Offset: 25, Length: 10  Score: 0.67
        Name: Paul Allen        Category: Person        Subcategory: N/A
        Offset: 40, Length: 10  Score: 0.81
        Name: April 4, 1975     Category: DateTime      Subcategory: Date
        Offset: 54, Length: 13  Score: 0.8
        Name: interpreters      Category: PersonType    Subcategory: N/A
        Offset: 95, Length: 12  Score: 0.6
        Name: 8800      Category: Quantity      Subcategory: Number
        Offset: 123, Length: 4  Score: 0.8
Document ID: 1
        Name: Microsoft         Category: Organization  Subcategory: N/A
        Offset: 21, Length: 9   Score: 0.96
        Name: Redmond   Category: Location      Subcategory: GPE
        Offset: 60, Length: 7   Score: 0.09
        Name: 21        Category: Quantity      Subcategory: Number
        Offset: 71, Length: 2   Score: 0.8
        Name: Seattle   Category: Location      Subcategory: GPE
```

## <a name="using-ner-to-detect-personal-information"></a>Wykrywanie danych osobowych za pomocą rozpoznawania jednostek nazwanych

Utwórz tablicę ciągów zawierającą dokument, który chcesz przeanalizować. Wywołaj metodę `recognizePiiEntities()` klienta i pobierz obiekt `EntitiesBatchResult`. Wykonaj iterację po liście wyników i wydrukuj nazwę jednostki, jej typ, podtyp, przesunięcie, długość i ocenę.


```javascript
async function entityPiiRecognition(client){

    const entityPiiInput = [
        "Insurance policy for SSN on file 123-12-1234 is here by approved."
    ];
    const entityPiiResults = await client.recognizePiiEntities(entityPiiInput);

    entityPiiResults.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.text} \tCategory: ${entity.category} \tSubcategory: ${entity.subCategory ? entity.subCategory : "N/A"}`);
            console.log(`\tOffset: ${entity.offset}, Length: ${entity.length} \tScore: ${entity.score}`);
        });
    });
}
entityPiiRecognition(textAnalyticsClient);
```

Uruchom kod przy użyciu polecenia `node index.js` w oknie konsoli.

### <a name="output"></a>Dane wyjściowe

```console
Document ID: 0
        Name: 123-12-1234       Category: U.S. Social Security Number (SSN)     Subcategory: N/A
        Offset: 33, Length: 11  Score: 0.85
```

## <a name="entity-linking"></a>Łączenie jednostek

Utwórz tablicę ciągów zawierającą dokument, który chcesz przeanalizować. Wywołaj metodę `recognizeLinkedEntities()` klienta i pobierz obiekt `RecognizeLinkedEntitiesResult`. Wykonaj iterację po liście wyników i wydrukuj nazwę jednostki, jej identyfikator, źródło danych, adres URL i dopasowania. Każdy obiekt w tablicy `matches` będzie zawierać przesunięcie, długość i ocenę dla tego dopasowania.

```javascript
async function linkedEntityRecognition(client){

    const linkedEntityInput = [
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800. During his career at Microsoft, Gates held the positions of chairman, chief executive officer, president and chief software architect, while also being the largest individual shareholder until May 2014."
    ];
    const entityResults = await client.recognizeLinkedEntities(linkedEntityInput);

    entityResults.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.name} \tID: ${entity.id} \tURL: ${entity.url} \tData Source: ${entity.dataSource}`);
            console.log(`\tMatches:`)
            entity.matches.forEach(match => {
                console.log(`\t\tText: ${match.text}`);
                console.log(`\t\tOffset: ${match.offset}, Length: ${match.length} \tScore: ${match.score.toFixed(3)}`);
            });
        });
    });
}
linkedEntityRecognition(textAnalyticsClient);
```

Uruchom kod przy użyciu polecenia `node index.js` w oknie konsoli.

### <a name="output"></a>Dane wyjściowe

```console
Document ID: 0
        Name: Altair 8800       ID: Altair 8800         URL: https://en.wikipedia.org/wiki/Altair_8800  Data Source: Wikipedia
        Matches:
                Text: Altair 8800
                Offset: 116, Length: 11         Score: 0.777
        Name: Bill Gates        ID: Bill Gates  URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates
                Offset: 25, Length: 10  Score: 0.555
                Text: Gates
                Offset: 161, Length: 5  Score: 0.555
        Name: Paul Allen        ID: Paul Allen  URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen
                Offset: 40, Length: 10  Score: 0.533
        Name: Microsoft         ID: Microsoft   URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft
                Offset: 0, Length: 9    Score: 0.469
                Text: Microsoft
                Offset: 150, Length: 9  Score: 0.469
        Name: April 4   ID: April 4     URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4
                Offset: 54, Length: 7   Score: 0.248
        Name: BASIC     ID: BASIC       URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC
                Offset: 89, Length: 5   Score: 0.281
```

#### <a name="version-21"></a>[Wersja 2.1](#tab/version-2)

> [!NOTE]
> W wersji 2.1 odpowiedź rozpoznawania jednostek nazwanych obejmuje łączenie jednostek.

Utwórz listę obiektów obejmującą dokumenty. Wywołaj metodę [entities()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#entities-models-textanalyticscliententitiesoptionalparams-) klienta i pobierz obiekt [EntitiesBatchResult](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/entitiesbatchresult). Wykonaj iterację po liście wyników i wydrukuj identyfikator każdego dokumentu. Dla każdej wykrytej jednostki wydrukuj jej nazwę w witrynie Wikipedia, typ i podtypy (jeśli istnieją), a także lokalizacje w oryginalnym tekście.

[!code-javascript[Entity recognition](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=entityRecognition)]

Uruchom kod przy użyciu polecenia `node index.js` w oknie konsoli.

### <a name="output"></a>Dane wyjściowe

```console
Document ID: 1
    Name: Microsoft,        Type: Organization,     Sub-Type: N/A
    Offset: 0, Length: 9,   Score: 1.0
    Name: Bill Gates,       Type: Person,   Sub-Type: N/A
    Offset: 25, Length: 10, Score: 0.999847412109375
    Name: Paul Allen,       Type: Person,   Sub-Type: N/A
    Offset: 40, Length: 10, Score: 0.9988409876823425
    Name: April 4,  Type: Other,    Sub-Type: N/A
    Offset: 54, Length: 7,  Score: 0.8
    Name: April 4, 1975,    Type: DateTime, Sub-Type: Date
    Offset: 54, Length: 13, Score: 0.8
    Name: BASIC,    Type: Other,    Sub-Type: N/A
    Offset: 89, Length: 5,  Score: 0.8
    Name: Altair 8800,      Type: Other,    Sub-Type: N/A
    Offset: 116, Length: 11,        Score: 0.8

Document ID: 2
    Name: Microsoft,        Type: Organization,     Sub-Type: N/A
    Offset: 21, Length: 9,  Score: 0.999755859375
    Name: Redmond (Washington),     Type: Location, Sub-Type: N/A
    Offset: 60, Length: 7,  Score: 0.9911284446716309
    Name: 21 kilómetros,    Type: Quantity, Sub-Type: Dimension
    Offset: 71, Length: 13, Score: 0.8
    Name: Seattle,  Type: Location, Sub-Type: N/A
    Offset: 88, Length: 7,  Score: 0.9998779296875
```

---

## <a name="key-phrase-extraction"></a>Wyodrębnianie kluczowych fraz

#### <a name="version-30-preview"></a>[Wersja 3.0-preview](#tab/version-3)

Utwórz tablicę ciągów zawierającą dokument, który chcesz przeanalizować. Wywołaj metodę `extractKeyPhrases()` klienta i pobierz zwrócony obiekt `ExtractKeyPhrasesResult`. Wykonaj iterację po wynikach i wydrukuj identyfikator każdego dokumentu oraz wszystkie wykryte kluczowe frazy.

```javascript
async function keyPhraseExtraction(client){

    const keyPhrasesInput = [
        "My cat might need to see a veterinarian.",
    ];
    const keyPhraseResult = await client.extractKeyPhrases(keyPhrasesInput);
    
    keyPhraseResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tDocument Key Phrases: ${document.keyPhrases}`);
    });
}
keyPhraseExtraction(textAnalyticsClient);
```

Uruchom kod przy użyciu polecenia `node index.js` w oknie konsoli.

### <a name="output"></a>Dane wyjściowe

```console
ID: 0
        Document Key Phrases: cat,veterinarian
```

#### <a name="version-21"></a>[Wersja 2.1](#tab/version-2)

Utwórz listę obiektów obejmującą dokumenty. Wywołaj metodę [keyPhrases()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#keyphrases-models-textanalyticsclientkeyphrasesoptionalparams-) klienta i pobierz zwrócony obiekt [KeyPhraseBatchResult](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/keyphrasebatchresult). Wykonaj iterację po wynikach i wydrukuj identyfikator każdego dokumentu oraz wszystkie wykryte kluczowe frazy.

[!code-javascript[Key phrase extraction](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=keyPhraseExtraction)]

Uruchom kod przy użyciu polecenia `node index.js` w oknie konsoli.

### <a name="output"></a>Dane wyjściowe

```console
[
    { id: '1', keyPhrases: [ '幸せ' ] }
    { id: '2', keyPhrases: [ 'Stuttgart', "hotel", "Fahrt", "Fu" ] }
    { id: '3', keyPhrases: [ 'cat', 'veterinarian' ] }
    { id: '3', keyPhrases: [ 'fútbol' ] }
]
```

---

## <a name="run-the-application"></a>Uruchamianie aplikacji

Uruchom aplikację, wykonując polecenie `node` dla pliku szybkiego startu.

```console
node index.js
```
