---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 02/14/2020
ms.topic: include
ms.custom: include file
ms.author: diberry
ms.openlocfilehash: 070de0f65e890c38acd5075286b349e95cd19f3b
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77371937"
---
Użyj biblioteki klienta tworzenia języka (LUIS) dla pliku Node.js, aby:

* Utwórz aplikację.
* Dodaj intencje, jednostki i wypowiedzi przykład.
* Dodawanie funkcji, takich jak lista fraz.
* Trenuj i publikuj aplikację.
* Usuń aplikację

[Dokumentacja dokumentacja](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/?view=azure-node-latest) | [dokumentacja Kod źródłowy kodu](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-luis-authoring) | [źródłowego (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-luis-authoring),[Przykłady](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/LUIS/luis_authoring_quickstart.js) pakietu środowiska wykonawczego [(NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-luis-runtime) | 

## <a name="prerequisites"></a>Wymagania wstępne

* Zasób tworzenia opisu języka: [utwórz go w witrynie Azure portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)
* [Node.js](https://nodejs.org)

## <a name="setting-up"></a>Konfigurowanie

### <a name="get-your-language-understanding-luis-starter-key"></a>Pobierz klucz startowy rozumienia języka (LUIS)

Pobierz [klucz startowy,](../luis-how-to-azure-subscription.md#starter-key) tworząc zasób tworzenia usługi LUIS. Zachowaj klucz i punkt końcowy klucza dla następnego kroku.

### <a name="create-an-environment-variable"></a>Tworzenie zmiennej środowiskowej

Korzystając z klucza i regionu klucza, utwórz dwie zmienne środowiskowe do uwierzytelniania:

* `LUIS_AUTHORING_KEY`- Klucz zasobu do uwierzytelniania żądań.
* `LUIS_AUTHORING_ENDPOINT`- Punkt końcowy skojarzony z kluczem.

Użyj instrukcji dla systemu operacyjnego.

#### <a name="windows"></a>[Windows](#tab/windows)

```console
setx LUIS_AUTHORING_KEY <replace-with-your-luis-authoring-key
setx LUIS_AUTHORING_ENDPOINT <replace-with-your-luis-authoring-endpoint>
```

Po dodaniu zmiennej środowiskowej uruchom ponownie okno konsoli.

#### <a name="linux"></a>[Linux](#tab/linux)

```bash
export LUIS_AUTHORING_KEY=<replace-with-your-luis-authoring-key>
export LUIS_AUTHORING_ENDPOINT=<replace-with-your-luis-authoring-endpoint>
```

Po dodaniu zmiennej środowiskowej uruchom polecenie `source ~/.bashrc` z okna konsoli, aby zmiany zostały uwzględnione.

#### <a name="macos"></a>[Macos](#tab/unix)

Edytuj `.bash_profile`swój program i dodaj zmienną środowiskową:

```bash
export LUIS_AUTHORING_KEY=<replace-with-your-luis-authoring-key>
export LUIS_AUTHORING_ENDPOINT=<replace-with-your-luis-authoring-endpoint>
```

Po dodaniu zmiennej środowiskowej uruchom polecenie `source .bash_profile` z okna konsoli, aby zmiany zostały uwzględnione.
***

### <a name="install-the-npm-library-for-luis-authoring"></a>Instalowanie biblioteki NPM do tworzenia usługi LUIS

W katalogu aplikacji zainstaluj zależności za pomocą następującego polecenia:

```console
npm install @azure/cognitiveservices-luis-authoring @azure/ms-rest-js
```

## <a name="object-model"></a>Model obiektu

Klient tworzenia języka (LUIS) jest obiektem [LUISAuthoringClient,](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/luisauthoringclient?view=azure-node-latest) który uwierzytelnia się na platformie Azure, który zawiera klucz autora.

Po utworzeniu klienta użyj tego klienta, aby uzyskać dostęp do funkcji, w tym:

* Aplikacje - [dodawanie,](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#add-applicationcreateobject--msrest-requestoptionsbase-) [usuwanie,](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#deletemethod-string--models-appsdeletemethodoptionalparams-) [publikowanie](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#publish-string--applicationpublishobject--msrest-requestoptionsbase-)
* Przykładowe wypowiedzi — [dodawanie przez partię](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/examples?view=azure-node-latest#batch-string--string--examplelabelobject----msrest-requestoptionsbase-), [usuwanie według identyfikatora](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/examples?view=azure-node-latest#deletemethod-string--string--number--msrest-requestoptionsbase-)
* Funkcje — zarządzanie [listami fraz](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/features?view=azure-node-latest#addphraselist-string--string--phraselistcreateobject--msrest-requestoptionsbase-)
* Model — zarządzanie [intencjami](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/model?view=azure-node-latest#addintent-string--string--modelcreateobject--msrest-requestoptionsbase-) i encjami
* Wzorzec - zarządzanie [wzorami](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/pattern?view=azure-node-latest#addpattern-string--string--patternrulecreateobject--msrest-requestoptionsbase-)
* Pociąg - [trenuj](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/train?view=azure-node-latest#trainversion-string--string--msrest-requestoptionsbase-) aplikację i ankietę na [temat statusu szkolenia](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/train?view=azure-node-latest#getstatus-string--string--msrest-requestoptionsbase-)
* [Wersje](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/versions?view=azure-node-latest) — zarządzanie za pomocą klonowania, eksportowania i usuwania


## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu pokazują, jak wykonać następujące czynności za pomocą biblioteki klienta tworzenia języka (LUIS) dla pliku Node.js:

* [Tworzenie aplikacji](#create-a-luis-app)
* [Dodawanie jednostek](#create-entities-for-the-app)
* [Dodawanie intencji](#create-intent-for-the-app)
* [Dodawanie przykładowych wypowiedzi](#add-example-utterance-to-intent)
* [Trenuj aplikację](#train-the-app)
* [Publikowanie aplikacji](#publish-a-language-understanding-app)
* [Usuwanie aplikacji](#delete-a-language-understanding-app)
* [Lista aplikacji](#list-language-understanding-apps)

## <a name="create-a-new-nodejs-application"></a>Tworzenie nowej aplikacji Node.js

Utwórz nowy plik tekstowy w `luis_authoring_quickstart.js`preferowanym edytorze lub IDE o nazwie . Następnie dodaj następujące zależności.

[!code-javascript[Create a new application in your preferred editor or IDE.](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=Dependencies)]

Tworzenie zmiennych dla punktu końcowego i klucza platformy Azure zasobu. Jeśli po uruchomieniu aplikacji utworzono zmienną środowiskową, należy zamknąć i ponownie otworzyć edytor, IDE lub powłokę uruchamianą ją, aby uzyskać dostęp do zmiennej.

[!code-javascript[Create variables for your resource's Azure endpoint and key.](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=Variables)]

## <a name="authenticate-the-client"></a>Uwierzytelnij klienta

Utwórz [obiekt CognitiveServicesCredentials](https://docs.microsoft.com/javascript/api/@azure/ms-rest-js/apikeycredentials?view=azure-node-latest) za pomocą klucza i użyj go z punktem końcowym do utworzenia obiektu [LUISAuthoringClient.](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/luisauthoringclient?view=azure-node-latest)

[!code-javascript[Create LUIS client object](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringCreateClient)]

## <a name="create-a-luis-app"></a>Tworzenie aplikacji usługi LUIS

1. Utwórz aplikację usługi LUIS, która będzie zawierała model przetwarzania języka naturalnego (NLP), zawierający intencje, jednostki i wypowiedzi przykładowe.

1. Utwórz metodę [dodawania](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest) obiektu [AppsOperation](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest) w celu utworzenia aplikacji. Nazwa i kultura języka są wymagane właściwości.

    [!code-javascript[Create LUIS client app](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringCreateApplication&highlight=9-11)]


## <a name="create-intent-for-the-app"></a>Tworzenie intencji dla aplikacji
Obiekt podstawowy w modelu aplikacji usługi LUIS jest intencją. Intencji jest wyrównane z grupowania _intencji_wypowiedzi użytkownika . Użytkownik może zadać pytanie lub złożyć oświadczenie w poszukiwaniu konkretnej _zamierzonej_ odpowiedzi od bota (lub innej aplikacji klienckiej). Przykładami intencji są rezerwacja lotu, pytanie o pogodę w mieście docelowym i pytanie o informacje kontaktowe dotyczące obsługi klienta.

Użyj [metody model.add_intent](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/model?view=azure-node-latest#addintent-string--string--modelcreateobject--msrest-requestoptionsbase-) o nazwie unikatowej intencji, a następnie przekaż identyfikator aplikacji, identyfikator wersji i nową nazwę intencji.

[!code-javascript[Create intent](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringAddIntents&highlight=2-6)]

## <a name="create-entities-for-the-app"></a>Tworzenie encji dla aplikacji

Chociaż jednostki nie są wymagane, znajdują się one w większości aplikacji. Jednostka wyodrębnia informacje z wypowiedzi użytkownika, niezbędne do fullfil intencji użytkownika. Istnieje kilka typów [wstępnie utworzonych](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/model?view=azure-node-latest#addcustomprebuiltentity-string--string--prebuiltdomainmodelcreateobject--msrest-requestoptionsbase-) i niestandardowych jednostek, z których każdy ma własne modele obiektu transformacji danych (DTO).  Typowe wstępnie utworzone encje do dodania do aplikacji obejmują [numer](../luis-reference-prebuilt-number.md), [datetimeV2](../luis-reference-prebuilt-datetimev2.md), [geographyV2](../luis-reference-prebuilt-geographyv2.md), [liczba porządkowa](../luis-reference-prebuilt-ordinal.md).

Ta **metoda add_entities** stworzyła `Location` prostą jednostkę `Class` z dwiema `Flight` rolami, prostą jednostką, jednostką złożoną i dodaje kilka wstępnie utworzonych jednostek.

Ważne jest, aby wiedzieć, że jednostki nie są oznaczone z zamiarem. Mogą i zwykle mają zastosowanie do wielu intencji. Tylko przykład wypowiedzi użytkownika są oznaczone dla określonego, pojedynczego zamiaru.

Metody tworzenia dla jednostek są częścią [Model](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/model?view=azure-node-latest) klasy. Każdy typ jednostki ma swój własny model obiektu przekształcania danych (DTO).

[!code-javascript[Create entities for the app](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringAddEntities&highlight=2-6)]

## <a name="add-example-utterance-to-intent"></a>Dodawanie przykładu wypowiedź do intencji

W celu określenia intencji wypowiedź i wyodrębnić jednostki, aplikacja potrzebuje przykładów wypowiedzi. Przykłady należy kierować określonego, pojedynczy zamiar i należy oznaczyć wszystkie encje niestandardowe. Wstępnie utworzone jednostki nie muszą być oznaczane.

Dodaj wypowiedzi przykład, tworząc listę [ExampleLabelObject](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/examplelabelobject?view=azure-node-latest) obiektów, jeden obiekt dla każdego wypowiedź przykład. Każdy przykład należy oznaczyć wszystkie jednostki słownikiem pary nazwy/wartości nazwy jednostki i wartości jednostki. Wartość jednostki powinna być dokładnie tak, jak pojawia się w tekście wypowiedź przykład.

Wywołanie [examples.batch](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/examples?view=azure-node-latest#batch-string--string--examplelabelobject----msrest-requestoptionsbase-) z identyfikatorem aplikacji, identyfikatorem wersji i listą przykładów. Połączenie odpowiada z listą wyników. Należy sprawdzić wynik każdego przykładu, aby upewnić się, że został pomyślnie dodany do modelu.

[!code-javascript[Add example utterance to intent](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringBatchAddUtterancesForIntent&highlight=52-56)]

## <a name="train-the-app"></a>Uczenie aplikacji

Po utworzeniu modelu aplikacja usługi LUIS musi zostać przeszkolona dla tej wersji modelu. Przeszkolony model może być używany w [kontenerze](../luis-container-howto.md)lub [opublikowany](../luis-how-to-publish-app.md) w szczelinach przejściowych lub produktowych.

[Train.trainVersion](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/train?view=azure-node-latest#trainversion-string--string--msrest-requestoptionsbase-) Metoda wymaga identyfikatora aplikacji i identyfikator wersji.

Bardzo mały model, taki jak ten szybki start, będzie trenował bardzo szybko. W przypadku aplikacji na poziomie produkcji szkolenie aplikacji powinno zawierać wywołanie sondowania do [metody get_status,](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/train?view=azure-node-latest#getstatus-string--string--msrest-requestoptionsbase-) aby określić, kiedy lub jeśli szkolenie zakończyło się pomyślnie. Odpowiedź jest lista [ModelTrainingInfo](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/modeltraininginfo?view=azure-node-latest) obiektów o osobnym stanie dla każdego obiektu. Wszystkie obiekty muszą być skuteczne, aby szkolenie było uważane za kompletne.

[!code-javascript[Train LUIS client app](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringTrainVersion&highlight=2-5)]

Szkolenie wszystkich modeli wymaga czasu. Użyj operacjiResult, aby sprawdzić stan szkolenia.

[!code-javascript[Get training status](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringWaitForOperation&highlight=11-14)]

## <a name="publish-a-language-understanding-app"></a>Publikowanie aplikacji do rozumienia języka

Opublikuj aplikację usługi LUIS przy użyciu metody [app.publish.](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#publish-string--applicationpublishobject--msrest-requestoptionsbase-) Spowoduje to opublikowanie bieżącej uczonej wersji do określonego gniazda w punkcie końcowym. Aplikacja kliencka używa tego punktu końcowego do wysyłania wypowiedzi użytkownika do przewidywania intencji i wyodrębniania jednostek.

[!code-javascript[Publish LUIS client app](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringPublishVersion&highlight=2-5)]

## <a name="delete-a-language-understanding-app"></a>Usuwanie aplikacji do rozumienia języka

Usuń aplikację usługi LUIS przy użyciu [metody app.deleteMethod.](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#deletemethod-string--models-appsdeletemethodoptionalparams-) Spowoduje to usunięcie bieżącej aplikacji.

[!code-javascript[Publish LUIS client app](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringDeleteApp&highlight=2)]

## <a name="list-language-understanding-apps"></a>Wyświetlanie aplikacji opisu języka

Pobieranie listy aplikacji skojarzonych z kluczem Opis języka

[!code-javascript[Publish LUIS client app](~/cognitive-services-quickstart-code/javascript/LUIS/luis_authoring_quickstart.js?name=AuthoringListApps)]

## <a name="run-the-application"></a>Uruchamianie aplikacji

Uruchom aplikację za `node luis_authoring_quickstart.js` pomocą polecenia w pliku szybkiego startu.

```console
node luis_authoring_quickstart.js
```

Dane wyjściowe wiersza polecenia aplikacji to:

```console
Created LUIS app with ID e137a439-b3e0-4e16-a7a8-a9746e0715f7
Entity Destination created.
Entity Class created.
Entity Flight created.
Intent FindFlights added.
Created 3 entity labels.
Created 3 entity labels.
Created 3 entity labels.
Example utterances added.
Waiting for train operation to finish...
Current model status: ["Queued"]
Current model status: ["InProgress"]
Current model status: ["InProgress"]
Current model status: ["InProgress"]
Current model status: ["Success"]
Application published. Endpoint URL: https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/e137a439-b3e0-4e16-a7a8-a9746e0715f7
Application with ID e137a439-b3e0-4e16-a7a8-a9746e0715f7 deleted. Operation result: Operation Successful
```
