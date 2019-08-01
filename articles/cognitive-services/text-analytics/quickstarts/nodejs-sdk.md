---
title: 'Szybki start: Wywoływanie interfejsu API analizy tekstu przy użyciu środowiska Node.js'
titleSuffix: Azure Cognitive Services
description: Uzyskaj informacje oraz przykłady kodu w celu szybkiego rozpoczęcia korzystania z interfejsu API analizy tekstu.
services: cognitive-services
author: raymondl
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 07/30/2019
ms.author: shthowse
ms.openlocfilehash: 9b8a713d58d5753e04de050e0bc961b5e8388123
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2019
ms.locfileid: "68697481"
---
# <a name="quickstart-using-nodejs-to-call-the-text-analytics-cognitive-service"></a>Szybki start: Wywoływanie analizy tekstu usługi Cognitive Service przy użyciu środowiska Node.js
<a name="HOLTop"></a>

Skorzystaj z tego przewodnika Szybki Start, aby rozpocząć analizowanie języka za pomocą zestawu SDK analiza tekstu dla środowiska Node. js. Chociaż interfejs API REST [Analiza tekstu](//go.microsoft.com/fwlink/?LinkID=759711) jest zgodny z większością języków programowania, zestaw SDK zapewnia łatwy sposób integracji usługi z aplikacjami. Kod źródłowy tego przykładu można znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/textAnalytics.js).

Zapoznaj się z tematem [API definitions (Definicje interfejsu API)](//go.microsoft.com/fwlink/?LinkID=759346), zawierającym dokumentację techniczną interfejsów API.

## <a name="prerequisites"></a>Wymagania wstępne

* [Node.js](https://nodejs.org/)
* Zestaw SDK analiza tekstu [dla środowiska Node. js](https://www.npmjs.com/package/azure-cognitiveservices-textanalytics) można zainstalować przy użyciu programu:

    `npm install azure-cognitiveservices-textanalytics`

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

Musisz również mieć [punkt końcowy i klucz dostępu](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) wygenerowany dla Ciebie podczas tworzenia konta.

## <a name="create-a-nodejs-application-and-install-the-sdk"></a>Tworzenie aplikacji w języku Node. js i Instalowanie zestawu SDK

Po zainstalowaniu środowiska Node. js Utwórz projekt węzła. Utwórz nowy katalog dla aplikacji i przejdź do jego katalogu.

```mkdir myapp && cd myapp```

Uruchom ```npm init``` , aby utworzyć aplikację Node przy użyciu pliku Package. JSON. Zainstaluj pakiety `azure-cognitiveservices-textanalytics`inpm: `ms-rest-azure`

```npm install azure-cognitiveservices-textanalytics ms-rest-azure```

Plik Package. JSON aplikacji zostanie zaktualizowany z zależnościami.

## <a name="authenticate-your-credentials"></a>Uwierzytelnianie poświadczeń

Utwórz nowy plik `index.js` w katalogu głównym projektu i zaimportuj zainstalowane biblioteki

```javascript
const CognitiveServicesCredentials = require("ms-rest-azure").CognitiveServicesCredentials;
const TextAnalyticsAPIClient = require("azure-cognitiveservices-textanalytics");
```

Utwórz zmienną dla klucza subskrypcji analiza tekstu.

```javascript
let credentials = new CognitiveServicesCredentials(
    "enter-your-key-here"
);
```

> [!Tip]
> W celu bezpiecznego wdrożenia wpisów tajnych w systemach produkcyjnych zalecamy używanie [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/quick-create-net).
>

## <a name="create-a-text-analytics-client"></a>Tworzenie klienta analiza tekstu

Utwórz nowy `TextAnalyticsClient` obiekt za pomocą `credentials` jako parametru. Użyj poprawnego regionu świadczenia usługi Azure dla subskrypcji analiza tekstu.

```javascript
//Replace 'westus' with the correct region for your Text Analytics subscription
let client = new TextAnalyticsAPIClient(
    credentials,
    "https://westus.api.cognitive.microsoft.com/"
);
```

## <a name="sentiment-analysis"></a>Analiza tonacji

Utwórz listę obiektów zawierającą dokumenty, które chcesz przeanalizować. Ładunek do `documents`interfejsu API składa się z listy, która `id`zawiera atrybut, `language`i `text` . Atrybut przechowuje tekst do analizy, `language` jest językiem dokumentu i `id` może być dowolną wartością. `text` 

```javascript
const inputDocuments = {documents:[
    {language:"en", id:"1", text:"I had the best day of my life."},
    {language:"en", id:"2", text:"This was a waste of my time. The speaker put me to sleep."},
    {language:"es", id:"3", text:"No tengo dinero ni nada que dar..."},
    {language:"it", id:"4", text:"L'hotel veneziano era meraviglioso. È un bellissimo pezzo di architettura."}
]}
```

Wywołaj `client.sentiment` i Pobierz wynik. Następnie można wykonać iterację w wynikach i wydrukować identyfikator każdego dokumentu oraz tonacji ocenę. Wynik zbliżony do 0 wskazuje negatywną tonacji, natomiast wynik zbliżony do 1 wskazuje pozytywny tonacji.

```javascript
const operation = client.sentiment({multiLanguageBatchInput: inputDocuments})
operation
.then(result => {
    console.log(result.documents);
})
.catch(err => {
    throw err;
});
```

Uruchom swój kod `node index.js` w oknie konsoli.

### <a name="output"></a>Output

```console
[ { id: '1', score: 0.8723785877227783 },
  { id: '2', score: 0.1059873104095459 },
  { id: '3', score: 0.43635445833206177 },
  { id: '4', score: 1 } ]
```

## <a name="language-detection"></a>Wykrywanie języka

Utwórz listę obiektów zawierających dokumenty. Ładunek do interfejsu API składa się z listy `documents`, która `id` zawiera atrybut i `text` . Ten `text` atrybut przechowuje tekst do przeanalizowania, `id` a może być dowolną wartością.

```javascript
// The documents to be submitted for language detection. The ID can be any value.
const inputDocuments = {
    documents: [
        { id: "1", text: "This is a document written in English." },
        { id: "2", text: "Este es un document escrito en Español." },
        { id: "3", text: "这是一个用中文写的文件" }
    ]
    };
```

Wywołaj `client.detectLanguage()` i Pobierz wynik. Następnie można wykonać iterację w wynikach i wydrukować identyfikator każdego dokumentu oraz pierwszy zwracany język.

```javascript
const operation = client.detectLanguage({
    languageBatchInput: inputDocuments
});
operation
    .then(result => {
    result.documents.forEach(document => {
        console.log(`ID: ${document.id}`);
        document.detectedLanguages.forEach(language =>
        console.log(`\tLanguage: ${language.name}`)
        );
    });
    })
    .catch(err => {
    throw err;
    });
```

Uruchom swój kod `node index.js` w oknie konsoli.

### <a name="output"></a>Output

```console
===== LANGUAGE EXTRACTION ======
ID: 1 Language English
ID: 2 Language Spanish
ID: 3 Language Chinese_Simplified
```

## <a name="entity-recognition"></a>Rozpoznawanie jednostek

Utwórz listę obiektów zawierającą dokumenty. Ładunek do `documents`interfejsu API składa się z listy, która `id`zawiera atrybut, `language`i `text` . Atrybut przechowuje tekst do analizy, `language` jest językiem dokumentu i `id` może być dowolną wartością. `text`

```javascript

    const inputDocuments = {documents:[
        {language:"en", id:"1", text:"Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800"},
        {language:"es", id:"2", text:"La sede principal de Microsoft se encuentra en la ciudad de Redmond, a 21 kilómetros de Seattle."},
        ]}

}
```

Wywołaj `client.entities()` i Pobierz wynik. Następnie wykonaj iterację w wynikach i wydrukuj identyfikatory poszczególnych dokumentów. Dla każdej wykrytej jednostki Wydrukuj jej nazwę witryny Wikipedia, typ i podtyp (jeśli istnieje), a także lokalizacje w oryginalnym tekście.

```javascript
const operation = client.entities({
    multiLanguageBatchInput: inputDocuments
});
operation
    .then(result => {
    result.documents.forEach(document => {
        console.log(`Document ID: ${document.id}`)
        document.entities.forEach(e =>{
        console.log(`\tName: ${e.name} Type: ${e.type} Sub Type: ${e.type}`)
        e.matches.forEach(match => (
            console.log(`\t\tOffset: ${match.offset} Length: ${match.length} Score: ${match.entityTypeScore}`)
        ))
        })
    });
    })
    .catch(err => {
    throw err;
    });
```

Uruchom swój kod `node index.js` w oknie konsoli.

### <a name="output"></a>Output

```console
Document ID: 1
    Name: Microsoft Type: Organization Sub Type: Organization
            Offset: 0 Length: 9 Score: 1
    Name: Bill Gates Type: Person Sub Type: Person
            Offset: 25 Length: 10 Score: 0.999786376953125
    Name: Paul Allen Type: Person Sub Type: Person
            Offset: 40 Length: 10 Score: 0.9988105297088623
    Name: April 4 Type: Other Sub Type: Other
            Offset: 54 Length: 7 Score: 0.8
    Name: April 4, 1975 Type: DateTime Sub Type: DateTime
            Offset: 54 Length: 13 Score: 0.8
    Name: BASIC Type: Other Sub Type: Other
            Offset: 89 Length: 5 Score: 0.8
    Name: Altair 8800 Type: Other Sub Type: Other
            Offset: 116 Length: 11 Score: 0.8
Document ID: 2
    Name: Microsoft Type: Organization Sub Type: Organization
            Offset: 21 Length: 9 Score: 0.999755859375
    Name: Redmond (Washington) Type: Location Sub Type: Location
            Offset: 60 Length: 7 Score: 0.9911284446716309
    Name: 21 kilómetros Type: Quantity Sub Type: Quantity
            Offset: 71 Length: 13 Score: 0.8
    Name: Seattle Type: Location Sub Type: Location
            Offset: 88 Length: 7 Score: 0.9998779296875
```

## <a name="key-phrase-extraction"></a>Wyodrębnianie kluczowych fraz

Utwórz listę obiektów zawierającą dokumenty. Ładunek do `documents`interfejsu API składa się z listy, która `id`zawiera atrybut, `language`i `text` . Atrybut przechowuje tekst do analizy, `language` jest językiem dokumentu i `id` może być dowolną wartością. `text`

```javascript
    let inputLanguage = {
    documents: [
        {language:"ja", id:"1", text:"猫は幸せ"},
        {language:"de", id:"2", text:"Fahrt nach Stuttgart und dann zum Hotel zu Fu."},
        {language:"en", id:"3", text:"My cat might need to see a veterinarian."},
        {language:"es", id:"4", text:"A mi me encanta el fútbol!"}
    ]
    };
```

Wywołaj `client.keyPhrases()` i Pobierz wynik. Następnie można wykonać iterację w wynikach i wydrukować identyfikator każdego dokumentu oraz wszystkie wykryte frazy klucza.

```javascript
    let operation = client.keyPhrases({
    multiLanguageBatchInput: inputLanguage
    });
    operation
    .then(result => {
        console.log(result.documents);
    })
    .catch(err => {
        throw err;
    });
```

Uruchom swój kod `node index.js` w oknie konsoli.

### <a name="output"></a>Output

```console
[ 
    { id: '1', keyPhrases: [ '幸せ' ] },
    { id: '2', keyPhrases: [ 'Stuttgart', 'Hotel', 'Fahrt', 'Fu' ] },
    { id: '3', keyPhrases: [ 'cat', 'veterinarian' ] },
    { id: '4', keyPhrases: [ 'fútbol' ] } 
]
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Analiza tekstu przy użyciu usługi Power BI](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>Zobacz także

 [Omówienie analizy tekstu](../overview.md) [Frequently asked questions (FAQ) (Często zadawane pytania (FAQ))](../text-analytics-resource-faq.md)
