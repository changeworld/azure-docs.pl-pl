---
title: 'Szybki start: Analiza tekstu Biblioteka kliencka dla środowiska Node. js | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Uzyskaj informacje oraz przykłady kodu w celu szybkiego rozpoczęcia korzystania z interfejsu API analizy tekstu.
services: cognitive-services
author: raymondl
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 08/28/2019
ms.author: shthowse
ms.openlocfilehash: c3cd64d0a683a60132808bca6a7ceb4aa84db4f1
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195193"
---
# <a name="quickstart-text-analytics-client-library-for-nodejs"></a>Szybki start: Biblioteka kliencka analizy tekstu dla środowiska Node. js
<a name="HOLTop"></a>

Rozpocznij pracę z analiza tekstuną biblioteką klienta dla programu. Node. js. Wykonaj następujące kroki, aby zainstalować pakiet i wypróbować przykładowy kod dla podstawowych zadań. 

Za pomocą analiza tekstuj biblioteki klienckiej środowiska Node. js wykonaj następujące:

* Analiza tonacji
* Wykrywanie języka
* Rozpoznawanie jednostek
* Wyodrębnianie kluczowych fraz

[](https://docs.microsoft.com/javascript/api/overview/azure/cognitiveservices/textanalytics?view=azure-node-latest) | [Przykłady](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/) pakietu | [kodu](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/cognitiveServicesTextAnalytics)[](https://www.npmjs.com/package/azure-cognitiveservices-textanalytics)źródłowego biblioteki dokumentacji referencyjnej (npm) | 

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/)
* Bieżąca wersja środowiska [Node. js](https://nodejs.org/).

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-text-analytics-azure-resource"></a>Tworzenie zasobu analiza tekstu platformy Azure

Usługa Azure Cognitive Services jest reprezentowana przez zasoby platformy Azure, które subskrybujesz. Utwórz zasób do analizy tekstu przy użyciu [Azure Portal](../../cognitive-services-apis-create-account.md) lub [interfejsu wiersza polecenia platformy Azure](../../cognitive-services-apis-create-account-cli.md) na komputerze lokalnym. Możesz również wykonać następujące czynności:

* Uzyskaj [klucz wersji próbnej](https://azure.microsoft.com/try/cognitive-services/#decision) ważny przez 7 dni bezpłatnie. Po utworzeniu rejestracji będzie on dostępny w [witrynie sieci Web systemu Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Wyświetl zasób na [Azure Portal](https://portal.azure.com/)

Po otrzymaniu klucza z subskrypcji próbnej lub zasobu [Utwórz zmienną środowiskową](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) dla klucza o nazwie `TEXTANALYTICS_SUBSCRIPTION_KEY`.

### <a name="create-a-new-nodejs-application"></a>Tworzenie nowej aplikacji Node.js

W oknie konsoli (na przykład cmd, PowerShell lub bash) Utwórz nowy katalog dla aplikacji i przejdź do niego. 

```console
mkdir myapp && cd myapp
```

Uruchom polecenie, aby utworzyć aplikację Node `package.json` z plikiem. `npm init` 

```console
npm init
```

Utwórz plik o nazwie `index.js` i zaimportuj następujące biblioteki:

```javascript
const CognitiveServicesCredentials = require("@azure/ms-rest-js");
const TextAnalyticsAPIClient = require("@azure/cognitiveservices-textanalytics");
```

Utwórz zmienne dla punktu końcowego platformy Azure i klucza subskrypcji zasobu. Uzyskaj te wartości ze zmiennych środowiskowych TEXT_ANALYTICS_SUBSCRIPTION_KEY i TEXT_ANALYTICS_ENDPOINT. Jeśli te zmienne środowiskowe zostały utworzone po rozpoczęciu edytowania aplikacji, należy zamknąć i ponownie otworzyć Edytor, środowisko IDE lub powłokę używaną w celu uzyskania dostępu do zmiennych.

[!INCLUDE [text-analytics-find-resource-information](../includes/find-azure-resource-info.md)]

```javascript
const key_var = 'TEXT_ANALYTICS_SUBSCRIPTION_KEY';
if (!process.env[key_var]) {
    throw new Error('please set/export the following environment variable: ' + key_var);
}
const subscription_key = process.env[key_var];

const endpoint_var = 'TEXT_ANALYTICS_ENDPOINT';
if (!process.env[endpoint_var]) {
    throw new Error('please set/export the following environment variable: ' + endpoint_var);
}
const endpoint = process.env[endpoint_var];
```

### <a name="install-the-client-library"></a>Zainstaluj bibliotekę kliencką

Zainstaluj pakiety `@azure/cognitiveservices-textanalytics`inpm: `@azure/ms-rest-js`

```console
npm install @azure/cognitiveservices-textanalytics @azure/ms-rest-js
```

`package.json` Plik aplikacji zostanie zaktualizowany z zależnościami.

## <a name="object-model"></a>Model obiektów

Klient analiza tekstu jest obiektem [TextAnalyticsClient](https://docs.microsoft.com/javascript/api/azure-cognitiveservices-textanalytics/textanalyticsclient?view=azure-node-latest) , który jest uwierzytelniany na platformie Azure przy użyciu klucza. Klient udostępnia kilka metod analizowania tekstu, w postaci pojedynczego ciągu lub partii.

Tekst jest wysyłany do interfejsu API jako lista `documents` `id`obiektów, które są `dictionary` obiektami zawierającymi kombinację atrybutów `text`, i `language` , w zależności od używanej metody. Ten `text` atrybut zawiera tekst, który ma być analizowany w pochodzeniu `language`i `id` może być dowolną wartością. 

Obiekt Response jest listą zawierającą informacje o analizie dla każdego dokumentu. 

## <a name="code-examples"></a>Przykłady kodu

* [Uwierzytelnianie klienta](#authenticate-the-client)
* [Analiza tonacji](#sentiment-analysis)
* [Wykrywanie języka](#language-detection)
* [Rozpoznawanie jednostek](#entity-recognition)
* [Wyodrębnianie kluczowych fraz](#key-phrase-extraction)


## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

Utwórz nowy obiekt [TextAnalyticsClient](https://docs.microsoft.com/javascript/api/azure-cognitiveservices-textanalytics/textanalyticsclient?view=azure-node-latest) z `credentials` i `endpoint` jako parametr.

```javascript
const creds = new CognitiveServicesCredentials.ApiKeyCredentials({ inHeader: { 'Ocp-Apim-Subscription-Key': subscription_key } });
const client = new TextAnalyticsAPIClient.TextAnalyticsClient(creds, endpoint);
```

## <a name="sentiment-analysis"></a>Analiza tonacji

Utwórz listę obiektów zawierającą dokumenty, które chcesz przeanalizować.

```javascript
const inputDocuments = {documents:[
    {language:"en", id:"1", text:"I had the best day of my life."}
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
[ { id: '1', score: 0.8723785877227783 } ]
```

## <a name="language-detection"></a>Wykrywanie języka

Utwórz listę obiektów zawierających dokumenty.

```javascript
// The documents to be submitted for language detection. The ID can be any value.
const inputDocuments = {
    documents: [
        { id: "1", text: "This is a document written in English." }
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
```

## <a name="entity-recognition"></a>Rozpoznawanie jednostek

Utwórz listę obiektów zawierającą dokumenty.

```javascript
const inputDocuments = {
    documents: [
        { language: "en", id: "1", text: "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800" }
    ]
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
```

## <a name="key-phrase-extraction"></a>Wyodrębnianie kluczowych fraz

Utwórz listę obiektów zawierającą dokumenty.

```javascript
    let inputLanguage = {
    documents: [
        {language:"en", id:"1", text:"My cat might need to see a veterinarian."}
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
    { id: '1', keyPhrases: [ 'cat', 'veterinarian' ] }
]
```

## <a name="run-the-application"></a>Uruchamianie aplikacji

Uruchom aplikację za pomocą `node` polecenia w pliku szybkiego startu.

```console
node index.js
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli chcesz wyczyścić i usunąć subskrypcję Cognitive Services, możesz usunąć zasób lub grupę zasobów. Usunięcie grupy zasobów spowoduje również usunięcie wszystkich skojarzonych z nią zasobów.

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
* Kod źródłowy tego przykładu można znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/textAnalytics.js).
