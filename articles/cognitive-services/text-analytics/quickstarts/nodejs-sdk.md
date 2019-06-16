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
ms.date: 06/11/2019
ms.author: shthowse
ms.openlocfilehash: 7e43d53c0916cf7fdc684c9e044e632015662c3b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67081519"
---
# <a name="quickstart-using-nodejs-to-call-the-text-analytics-cognitive-service"></a>Szybki start: Wywoływanie analizy tekstu usługi Cognitive Service przy użyciu środowiska Node.js
<a name="HOLTop"></a>

Użyj tego przewodnika Szybki Start, aby rozpocząć analizowanie języka przy użyciu zestawu SDK analizy tekstu dla środowiska Node.js. Gdy [analizy tekstu](//go.microsoft.com/fwlink/?LinkID=759711) interfejsu API REST jest zgodny z większość języków programowania, zestaw SDK udostępnia łatwy sposób zintegrować usługę ze swoimi aplikacjami. Kod źródłowy tego przykładu można znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/textAnalytics.js).

Zapoznaj się z tematem [API definitions (Definicje interfejsu API)](//go.microsoft.com/fwlink/?LinkID=759346), zawierającym dokumentację techniczną interfejsów API.

## <a name="prerequisites"></a>Wymagania wstępne

* [Node.js](https://nodejs.org/)
* Analiza tekstu [zestaw SDK for Node.js](https://www.npmjs.com/package/azure-cognitiveservices-textanalytics) można zainstalować zestawu SDK za pomocą:

    `npm install azure-cognitiveservices-textanalytics`

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

Musisz również mieć [punkt końcowy i klucz dostępu](../How-tos/text-analytics-how-to-access-key.md) wygenerowany dla Ciebie podczas tworzenia konta.

## <a name="create-a-nodejs-application-and-install-the-sdk"></a>Tworzenie aplikacji Node.js i zainstaluj zestaw SDK

Po zainstalowaniu środowiska Node.js, Utwórz projekt węzła. Utwórz nowy katalog dla aplikacji, a następnie przejdź do swojego katalogu.

```mkdir myapp && cd myapp```

Uruchom ```npm init``` do tworzenia aplikacji węzła przy użyciu pliku package.json. Zainstaluj `ms-rest-azure` i `azure-cognitiveservices-textanalytics` pakietów NPM:

```npm install azure-cognitiveservices-textanalytics ms-rest-azure```

Plik package.json aplikacji zostaną zaktualizowane z zależnościami.

## <a name="authenticate-your-credentials"></a>Uwierzytelnienia poświadczeń użytkownika

Utwórz nowy plik `index.js` w projekcie główny i Importuj biblioteki zainstalowane

```javascript
const CognitiveServicesCredentials = require("ms-rest-azure").CognitiveServicesCredentials;
const TextAnalyticsAPIClient = require("azure-cognitiveservices-textanalytics");
```

Utwórz zmienną dla swój klucz subskrypcji analizy tekstu.

```javascript
let credentials = new CognitiveServicesCredentials(
    "enter-your-key-here"
);
```

> [!Tip]
> W bezpieczne wdrażanie kluczy tajnych w systemach produkcyjnych zalecamy używanie [usługi Azure Key Vault](https://docs.microsoft.com/azure/key-vault/quick-create-net).
>

## <a name="create-a-text-analytics-client"></a>Tworzenie klienta analizy tekstu

Utwórz nową `TextAnalyticsClient` obiekt z `credentials` jako parametr. Użyj poprawny region platformy Azure dla Twojej subskrypcji analizy tekstu.

```javascript
//Replace 'westus' with the correct region for your Text Analytics subscription
let client = new TextAnalyticsAPIClient(
    credentials,
    "https://westus.api.cognitive.microsoft.com/"
);
```

## <a name="sentiment-analysis"></a>Analiza tonacji

Utwórz listę obiektów, zawierające dokumentów, które mają być analizowane. Ładunek do interfejsu API składa się z listy `documents`, które zawierają `id`, `language`, i `text` atrybutu. `text` Atrybut przechowuje tekst, który ma być analizowane `language` jest język dokumentu, a `id` może być dowolna wartość. 

```javascript
const inputDocuments = {documents:[
    {language:"en", id:"1", text:"I had the best day of my life."},
    {language:"en", id:"2", text:"This was a waste of my time. The speaker put me to sleep."},
    {language:"es", id:"3", text:"No tengo dinero ni nada que dar..."},
    {language:"it", id:"4", text:"L'hotel veneziano era meraviglioso. È un bellissimo pezzo di architettura."}
]}
```

Wywołaj `client.sentiment` i uzyskać wynik. Następnie iteracyjnego przeglądania wyników i Drukuj identyfikator każdego dokumentu i ocenę tonacji. Podczas oceny bliższa 1 oznacza pozytywną tonację, wyniku przybliżania 0 wskazuje negatywną tonację.

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

Uruchom kod za pomocą `node index.js` w oknie konsoli.

### <a name="output"></a>Dane wyjściowe

```console
[ { id: '1', score: 0.8723785877227783 },
  { id: '2', score: 0.1059873104095459 },
  { id: '3', score: 0.43635445833206177 },
  { id: '4', score: 1 } ]
```

## <a name="language-detection"></a>Wykrywanie języka

Utwórz listę obiektów zawierających dokumentów. Ładunek do interfejsu API składa się z listy `documents`, które zawierają `id` i `text` atrybutu. `text` Atrybut przechowuje tekst, który ma być analizowane i `id` może być dowolna wartość.

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

Wywołaj `client.detectLanguage()` i uzyskać wynik. Następnie iteracyjnego przeglądania wyników i Drukuj identyfikator każdego dokumentu i zwrócony pierwszy język.

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

Uruchom kod za pomocą `node index.js` w oknie konsoli.

### <a name="output"></a>Dane wyjściowe

```console
===== LANGUAGE EXTRACTION ======
ID: 1 Language English
ID: 2 Language Spanish
ID: 3 Language Chinese_Simplified
```

## <a name="entity-recognition"></a>Rozpoznawanie jednostek

Utwórz listę obiektów, zawierające dokumentów. Ładunek do interfejsu API składa się z listy `documents`, które zawierają `id`, `language`, i `text` atrybutu. `text` Atrybut przechowuje tekst, który ma być analizowane `language` jest język dokumentu, a `id` może być dowolna wartość.

```javascript

    const inputDocuments = {documents:[
        {language:"en", id:"1", text:"Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800"},
        {language:"es", id:"2", text:"La sede principal de Microsoft se encuentra en la ciudad de Redmond, a 21 kilómetros de Seattle."},
        ]}

}
```

Wywołaj `client.entities()` i uzyskać wynik. Następnie iteracyjnego przeglądania wyników i drukowanie identyfikator każdego dokumentu. Dla każdej wykrytej jednostki, wydrukować jej nazwę wikipedia, typ i podtypy (jeśli istnieje) oraz lokalizacji, w oryginalny tekst.

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

Uruchom kod za pomocą `node index.js` w oknie konsoli.

### <a name="output"></a>Dane wyjściowe

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

Utwórz listę obiektów, zawierające dokumentów. Ładunek do interfejsu API składa się z listy `documents`, które zawierają `id`, `language`, i `text` atrybutu. `text` Atrybut przechowuje tekst, który ma być analizowane `language` jest język dokumentu, a `id` może być dowolna wartość.

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

Wywołaj `client.keyPhrases()` i uzyskać wynik. Następnie iteracyjnego przeglądania wyników i Drukuj identyfikator każdego dokumentu i wszelkie wykryte fraz kluczowych.

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

Uruchom kod za pomocą `node index.js` w oknie konsoli.

### <a name="output"></a>Dane wyjściowe

```console
[ 
    { id: '1', keyPhrases: [ '幸せ' ] },
    { id: '2', keyPhrases: [ 'Stuttgart', 'Hotel', 'Fahrt', 'Fu' ] },
    { id: '3', keyPhrases: [ 'cat', 'veterinarian' ] },
    { id: '4', keyPhrases: [ 'fútbol' ] } 
]
```

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Analiza tekstu przy użyciu usługi Power BI](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>Zobacz także

 [Omówienie analizy tekstu](../overview.md) [Frequently asked questions (FAQ) (Często zadawane pytania (FAQ))](../text-analytics-resource-faq.md)
