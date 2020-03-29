---
title: Importowanie wypowiedzi przy użyciu pliku Node.js — USŁUGA LUIS
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak programowo tworzyć aplikację usługi LUIS na podstawie istniejących danych w formacie CSV przy użyciu interfejsu API tworzenia usługi LUIS.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: diberry
ms.openlocfilehash: ef5f6967b7ad9500672d00d93dd8acaca99e5948
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73499465"
---
# <a name="build-a-luis-app-programmatically-using-nodejs"></a>Programowo tworzenie aplikacji usługi LUIS przy użyciu pliku Node.js

Usługa LUIS udostępnia programowy interfejs API, który wykonuje wszystko, co robi witryna sieci Web [usługi LUIS.](luis-reference-regions.md) Może to zaoszczędzić czas, gdy masz wcześniej istniejących danych i byłoby szybsze do tworzenia aplikacji usługi LUIS programowo niż przez wprowadzanie informacji ręcznie. 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Zaloguj się w witrynie [sieci Luis](luis-reference-regions.md) i znajdź [klucz tworzenia](luis-concept-keys.md#authoring-key) w ustawieniach konta. Ten klucz służy do wywoływania interfejsów API tworzenia.
* Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.
* Ten artykuł rozpoczyna się od pliku CSV dla hipotetycznych plików dziennika firmy żądań użytkowników. Pobierz go [tutaj](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/IoT.csv).
* Zainstaluj najnowszy plik Node.js z modułem NPM. Pobierz go [stąd](https://nodejs.org/en/download/).
* **[Zalecane]** Visual Studio Code for IntelliSense and debugging, pobierz go [z tego miejsca](https://code.visualstudio.com/) za darmo.

Cały kod w tym artykule jest dostępny w [repozytorium języka Usługi Azure-Samples Language Understanding GitHub.](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/examples/build-app-programmatically-csv) 

## <a name="map-preexisting-data-to-intents-and-entities"></a>Mapowanie istniejących danych do intencji i jednostek
Nawet jeśli masz system, który nie został utworzony z usługą LUIS na uwadze, jeśli zawiera dane tekstowe, które mapuje do różnych rzeczy, które użytkownicy chcą zrobić, może być w stanie wymyślić mapowanie z istniejących kategorii danych wejściowych użytkownika do intencji w usłudze LUIS. Jeśli można zidentyfikować ważne słowa lub frazy w tym, co użytkownicy powiedzieli, te słowa mogą być mapowane do jednostek.

Otwórz [`IoT.csv`](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/IoT.csv) plik. Zawiera dziennik zapytań użytkowników do hipotetycznej usługi automatyki domowej, w tym jak zostały one skategoryzowane, co powiedział użytkownik, a niektóre kolumny z przydatnymi informacjami wyciągnięte z nich. 

![Plik CSV z istniejącymi danymi](./media/luis-tutorial-node-import-utterances-csv/csv.png) 

Widać, że **RequestType** kolumna może być intencje i **Request** kolumna pokazuje wypowiedź przykład. Inne pola mogą być jednostki, jeśli występują one w wypowiedź. Ponieważ istnieją intencje, jednostki i wypowiedzi przykład, masz wymagania dotyczące prostej, przykładowej aplikacji.

## <a name="steps-to-generate-a-luis-app-from-non-luis-data"></a>Kroki generowania aplikacji usługi LUIS na podstawie danych innych niż usługi LUIS
Aby wygenerować nową aplikację usługi LUIS z pliku CSV:

* Przejaszeń dane z pliku CSV:
    * Konwertuj na format, który można przekazać do usługi LUIS przy użyciu interfejsu API tworzenia. 
    * Na podstawie przeanalizowanych danych zbieraj informacje o zamiarach i jednostkach. 
* Tworzenie wywołań interfejsu API do:
    * Utwórz aplikację.
    * Dodaj intencje i jednostki, które zostały zebrane z analizowanych danych. 
    * Po utworzeniu aplikacji usługi LUIS, można dodać wypowiedzi przykład z analizowanych danych. 

Ten przepływ programu można zobaczyć w `index.js` ostatniej części pliku. Skopiuj lub [pobierz](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/index.js) ten kod i zapisz go w `index.js`pliku .

   [!code-javascript[Node.js code for calling the steps to build a LUIS app](~/samples-luis/examples/build-app-programmatically-csv/index.js)]


## <a name="parse-the-csv"></a>Analizuj CSV

Wpisy kolumn, które zawierają wypowiedzi w pliku CSV muszą być analizowane w formacie JSON, który usługa LUIS może zrozumieć. Ten format JSON `intentName` musi zawierać pole, które identyfikuje intencji wypowiedź. Musi również zawierać `entityLabels` pole, które może być puste, jeśli nie ma żadnych jednostek w wypowiedź. 

Na przykład wpis "Włącz światła" mapuje do tego JSON:

```json
        {
            "text": "Turn on the lights",
            "intentName": "TurnOn",
            "entityLabels": [
                {
                    "entityName": "Operation",
                    "startCharIndex": 5,
                    "endCharIndex": 6
                },
                {
                    "entityName": "Device",
                    "startCharIndex": 12,
                    "endCharIndex": 17
                }
            ]
        }
```

W tym przykładzie `intentName` pochodzi z żądania użytkownika w nagłówku kolumny **Żądanie** w pliku CSV, a `entityName` pochodzi z innych kolumn z informacjami o kluczu. Na przykład jeśli istnieje wpis **operacji** lub **urządzenia**, a ten ciąg występuje również w rzeczywistym żądaniu, a następnie może być oznaczony jako jednostki. Poniższy kod demonstruje ten proces analizowania. Możesz go [download](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_parse.js) skopiować lub pobrać `_parse.js`i zapisać w pliku .

   [!code-javascript[Node.js code for parsing a CSV file to extract intents, entities, and labeled utterances](~/samples-luis/examples/build-app-programmatically-csv/_parse.js)]



## <a name="create-the-luis-app"></a>Tworzenie aplikacji usługi LUIS
Po przeanalizowaniu danych w JSON dodaj je do aplikacji usługi LUIS. Poniższy kod tworzy aplikację usługi LUIS. Skopiuj lub [pobierz](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_create.js) go i zapisz w pliku `_create.js`.

   [!code-javascript[Node.js code for creating a LUIS app](~/samples-luis/examples/build-app-programmatically-csv/_create.js)]


## <a name="add-intents"></a>Dodawanie intencji
Gdy masz aplikację, musisz się do niej przyjemnie. Poniższy kod tworzy aplikację usługi LUIS. Skopiuj lub [pobierz](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_intents.js) go i zapisz w pliku `_intents.js`.

   [!code-javascript[Node.js code for creating a series of intents](~/samples-luis/examples/build-app-programmatically-csv/_intents.js)]


## <a name="add-entities"></a>Dodawanie jednostek
Poniższy kod dodaje jednostki do aplikacji usługi LUIS. Skopiuj lub [pobierz](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_entities.js) go i zapisz w pliku `_entities.js`.

   [!code-javascript[Node.js code for creating entities](~/samples-luis/examples/build-app-programmatically-csv/_entities.js)]
   


## <a name="add-utterances"></a>Dodawanie wypowiedzi
Po zdefiniowaniu jednostek i intencji w aplikacji usługi LUIS można dodać wypowiedzi. Poniższy kod używa [Utterances_AddBatch](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c09) interfejsu API, który umożliwia dodawanie do 100 wypowiedzi naraz.  Skopiuj lub [pobierz](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_upload.js) go i zapisz w pliku `_upload.js`.

   [!code-javascript[Node.js code for adding utterances](~/samples-luis/examples/build-app-programmatically-csv/_upload.js)]


## <a name="run-the-code"></a>Uruchamianie kodu


### <a name="install-nodejs-dependencies"></a>Instalowanie zależności node.js
Zainstaluj zależności Node.js z serwera NPM w wierszu terminala/polecenia.

```console
> npm install
```

### <a name="change-configuration-settings"></a>Zmienianie ustawień konfiguracji
Aby korzystać z tej aplikacji, należy zmienić wartości w pliku index.js do własnego klucza punktu końcowego i podać nazwę, którą ma mieć aplikacja. Można również ustawić kulturę aplikacji lub zmienić numer wersji.

Otwórz plik index.js i zmień te wartości u góry pliku.


```javascript
// Change these values
const LUIS_programmaticKey = "YOUR_AUTHORING_KEY";
const LUIS_appName = "Sample App";
const LUIS_appCulture = "en-us"; 
const LUIS_versionId = "0.1";
```

### <a name="run-the-script"></a>Uruchamianie skryptu
Uruchom skrypt z wiersza terminala/polecenia w pliku Node.js.

```console
> node index.js
```

lub

```console
> npm start
```

### <a name="application-progress"></a>Postęp aplikacji
Gdy aplikacja jest uruchomiona, wiersz polecenia pokazuje postęp. Dane wyjściowe wiersza polecenia obejmują format odpowiedzi z usługi LUIS.

```console
> node index.js
intents: ["TurnOn","TurnOff","Dim","Other"]
entities: ["Operation","Device","Room"]
parse done
JSON file should contain utterances. Next step is to create an app with the intents and entities it found.
Called createApp, created app with ID 314b306c-0033-4e09-92ab-94fe5ed158a2
Called addIntents for intent named TurnOn.
Called addIntents for intent named TurnOff.
Called addIntents for intent named Dim.
Called addIntents for intent named Other.
Results of all calls to addIntent = [{"response":"e7eaf224-8c61-44ed-a6b0-2ab4dc56f1d0"},{"response":"a8a17efd-f01c-488d-ad44-a31a818cf7d7"},{"response":"bc7c32fc-14a0-4b72-bad4-d345d807f965"},{"response":"727a8d73-cd3b-4096-bc8d-d7cfba12eb44"}]
called addEntity for entity named Operation.
called addEntity for entity named Device.
called addEntity for entity named Room.
Results of all calls to addEntity= [{"response":"6a7e914f-911d-4c6c-a5bc-377afdce4390"},{"response":"56c35237-593d-47f6-9d01-2912fa488760"},{"response":"f1dd440c-2ce3-4a20-a817-a57273f169f3"}]
retrying add examples...

Results of add utterances = [{"response":[{"value":{"UtteranceText":"turn on the lights","ExampleId":-67649},"hasError":false},{"value":{"UtteranceText":"turn the heat on","ExampleId":-69067},"hasError":false},{"value":{"UtteranceText":"switch on the kitchen fan","ExampleId":-3395901},"hasError":false},{"value":{"UtteranceText":"turn off bedroom lights","ExampleId":-85402},"hasError":false},{"value":{"UtteranceText":"turn off air conditioning","ExampleId":-8991572},"hasError":false},{"value":{"UtteranceText":"kill the lights","ExampleId":-70124},"hasError":false},{"value":{"UtteranceText":"dim the lights","ExampleId":-174358},"hasError":false},{"value":{"UtteranceText":"hi how are you","ExampleId":-143722},"hasError":false},{"value":{"UtteranceText":"answer the phone","ExampleId":-69939},"hasError":false},{"value":{"UtteranceText":"are you there","ExampleId":-149588},"hasError":false},{"value":{"UtteranceText":"help","ExampleId":-81949},"hasError":false},{"value":{"UtteranceText":"testing the circuit","ExampleId":-11548708},"hasError":false}]}]
upload done
```




## <a name="open-the-luis-app"></a>Otwieranie aplikacji usługi LUIS
Po zakończeniu skryptu można zalogować się do [usługi LUIS](luis-reference-regions.md) i wyświetlić aplikację usługi LUIS utworzoną w obszarze **Moje aplikacje**. Powinieneś być w stanie zobaczyć wypowiedzi dodane w ramach **TurnOn**, **TurnOff**i **Brak** intencji.

![Zamiar TurnOn](./media/luis-tutorial-node-import-utterances-csv/imported-utterances-661.png)


## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Testowanie i szkolenie aplikacji w witrynie sieci LUIS](luis-interactive-test.md)

## <a name="additional-resources"></a>Zasoby dodatkowe

Ta przykładowa aplikacja używa następujących interfejsów API usługi LUIS:
- [tworzenie aplikacji](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36)
- [dodawanie intencji](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0c)
- [dodawanie jednostek](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0e) 
- [dodawanie wypowiedzi](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c09)
