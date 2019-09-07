---
title: Importowanie wyrażenia długości przy użyciu środowiska Node. js-LUIS
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak utworzyć aplikację usługi LUIS programowo z istniejących danych w formacie CSV przy użyciu interfejsu API tworzenia usługi LUIS.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: diberry
ms.openlocfilehash: 1bee26dc57fd844703e2c9c97b38b9a433227fbf
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/06/2019
ms.locfileid: "70387943"
---
# <a name="build-a-luis-app-programmatically-using-nodejs"></a>Tworzenie aplikacji usługi LUIS programowo przy użyciu środowiska Node.js

Usługa LUIS zapewnia programowego interfejsu API, które ma wszystko, [LUIS](luis-reference-regions.md) jest witryny sieci Web. To zaoszczędzić czas, gdy masz już istniejące dane i będzie szybsze tworzenie aplikacji usługi LUIS programowo niż, wprowadzając informacje ręcznie. 

## <a name="prerequisites"></a>Wymagania wstępne

* Zaloguj się do witryny sieci Web [Luis](luis-reference-regions.md) i Znajdź swój [klucz tworzenia](luis-concept-keys.md#authoring-key) w ustawieniach konta. Ten klucz służy do wywoływania interfejsów API do tworzenia.
* Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Ten artykuł rozpoczyna się od pliku CSV dla hipotetycznych plików dziennika żądań użytkowników. Pobierz go [tutaj](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/IoT.csv).
* Za pomocą pakietu NPM, należy zainstalować najnowsze środowisko Node.js. Pobierz go z [tutaj](https://nodejs.org/en/download/).
* **[Zalecane]**  Programu visual Studio Code dla funkcji IntelliSense i debugowania, pobierz go z [tutaj](https://code.visualstudio.com/) za darmo.

Cały kod w tym artykule jest dostępny w [repozytorium Azure-samples Language Understanding GitHub](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/examples/build-app-programmatically-csv). 

## <a name="map-preexisting-data-to-intents-and-entities"></a>Mapowanie istniejących danych na intencje i podmioty
Nawet jeśli masz system, który nie został utworzony z użyciem usługi LUIS, pamiętając, jeśli zawiera on danych tekstowych, które mapuje do użytkowników różnych rzeczy, o którym chcesz przeprowadzić, może być opracowywane mapowanie z istniejącej kategorii danych wejściowych użytkownika na intencje w usługi LUIS. Jeśli możesz zidentyfikować ważne wyrazy lub frazy w powiedzieć użytkownikom, te wyrazy mogą być mapowane do jednostek.

[`IoT.csv`](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/IoT.csv) Otwórz plik. Zawiera dziennik kwerend użytkowników do usługi hipotetyczny głównego usługi automation, w tym jak zostały przydzielone, nazywany użytkownika i niektóre kolumny przydatnymi informacjami, usunąć z nich. 

![Plik CSV istniejących danych](./media/luis-tutorial-node-import-utterances-csv/csv.png) 

Zobaczysz, że **RequestType** kolumna może być intencji i **żądania** kolumna pokazuje przykład wypowiedź. Inne pola może być jednostki, jeśli występują one w wypowiedź. Ponieważ istnieje intencji, jednostek i wypowiedzi przykład, masz wymagania dotyczące prosty, przykładową aplikację.

## <a name="steps-to-generate-a-luis-app-from-non-luis-data"></a>Kroki, aby wygenerować aplikacją usługi LUIS z danych niż usługi LUIS
Aby wygenerować nową aplikację LUIS z pliku CSV:

* Przeanalizuj dane z pliku CSV:
    * Konwertuj na format, który można przekazać do LUIS przy użyciu interfejsu API tworzenia. 
    * Z danych przeanalizowanych Zbierz informacje o intencjach i jednostkach. 
* Utwórz wywołania interfejsu API tworzenia dla:
    * Utwórz aplikację.
    * Dodawanie intencji i jednostek, które zostały zebrane z przeanalizowanych danych. 
    * Po utworzeniu aplikacji usługi LUIS wypowiedzi przykład można dodać z przeanalizowanych danych. 

Ten przepływ programu można zobaczyć w ostatniej części `index.js` pliku. Kopiowanie lub [Pobierz](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/index.js) ten kod i zapisz go w `index.js`.

   [!code-javascript[Node.js code for calling the steps to build a LUIS app](~/samples-luis/examples/build-app-programmatically-csv/index.js)]


## <a name="parse-the-csv"></a>Analizowanie plików CSV

Wpisy kolumny, które zawierają wypowiedzi w woluminie CSV musi przeanalizować w formacie JSON, który może zrozumieć usługi LUIS. Ten format JSON musi zawierać `intentName` pola, który identyfikuje celem wypowiedź. Musi również zawierać `entityLabels` pola, które może być pusta, jeśli nie ma żadnych podmiotów w wypowiedź. 

Na przykład pozycję "Włącz światła" mapuje dane JSON:

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

W tym przykładzie `intentName` pochodzi z żądania użytkownika w obszarze **żądania** nagłówek kolumny w pliku CSV i `entityName` pochodzi z innych kolumn z informacjami klucza. Na przykład, jeśli istnieje wpis dla **operacji** lub **urządzenia**, a ciąg występuje także w rzeczywistego żądania, a następnie może być opisane jako jednostka. Poniższy przykład demonstruje ten proces analizy. Możesz skopiować lub [Pobierz](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_parse.js) go i zapisać go w celu `_parse.js`.

   [!code-javascript[Node.js code for parsing a CSV file to extract intents, entities, and labeled utterances](~/samples-luis/examples/build-app-programmatically-csv/_parse.js)]



## <a name="create-the-luis-app"></a>Tworzenie aplikacji usługi LUIS
Po przeanalizowaniu danych do postaci JSON, należy go dodać do aplikacji usługi LUIS. Poniższy kod tworzy aplikację usługi LUIS. Kopiowanie lub [Pobierz](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_create.js) i zapisz go w `_create.js`.

   [!code-javascript[Node.js code for creating a LUIS app](~/samples-luis/examples/build-app-programmatically-csv/_create.js)]


## <a name="add-intents"></a>Dodawanie intencji
Po utworzeniu aplikacji, musisz intencji do niego. Poniższy kod tworzy aplikację usługi LUIS. Kopiowanie lub [Pobierz](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_intents.js) i zapisz go w `_intents.js`.

   [!code-javascript[Node.js code for creating a series of intents](~/samples-luis/examples/build-app-programmatically-csv/_intents.js)]


## <a name="add-entities"></a>Dodawanie jednostek
Poniższy kod dodaje jednostki do aplikacji usługi LUIS. Kopiowanie lub [Pobierz](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_entities.js) i zapisz go w `_entities.js`.

   [!code-javascript[Node.js code for creating entities](~/samples-luis/examples/build-app-programmatically-csv/_entities.js)]
   


## <a name="add-utterances"></a>Dodawanie wypowiedzi
Po zdefiniowaniu jednostek i opcjami w aplikacji usługi LUIS można dodać wypowiedzi. Poniższy kod używa [Utterances_AddBatch](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c09) interfejsu API, który umożliwia dodanie maksymalnie 100 wypowiedzi w danym momencie.  Kopiowanie lub [Pobierz](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_upload.js) i zapisz go w `_upload.js`.

   [!code-javascript[Node.js code for adding utterances](~/samples-luis/examples/build-app-programmatically-csv/_upload.js)]


## <a name="run-the-code"></a>Uruchamianie kodu


### <a name="install-nodejs-dependencies"></a>Zainstaluj środowisko Node.js zależności
Zainstaluj zależności środowiska Node.js z poziomu narzędzia NPM, w wierszu polecenia/na terminalu.

```console
> npm install
```

### <a name="change-configuration-settings"></a>Zmienianie ustawień konfiguracji
Aby można było używać tej aplikacji, musisz zmienić wartości w pliku index.js do klucza punktu końcowego, a następnie podaj nazwę, chcesz, aby aplikacja miała. Możesz również ustawić kulturę aplikacji lub zmienić numer wersji.

Otwórz plik index.js i zmień te wartości w górnej części pliku.


```javascript
// Change these values
const LUIS_programmaticKey = "YOUR_AUTHORING_KEY";
const LUIS_appName = "Sample App";
const LUIS_appCulture = "en-us"; 
const LUIS_versionId = "0.1";
```

### <a name="run-the-script"></a>Uruchamianie skryptu
Uruchom skrypt z wiersza polecenia/na terminalu przy użyciu środowiska Node.js.

```console
> node index.js
```

lub

```console
> npm start
```

### <a name="application-progress"></a>Postęp aplikacji
Gdy aplikacja jest uruchomiona, w wierszu polecenia pokazuje postęp. Dane wyjściowe wiersza polecenia obejmuje format odpowiedzi z usługi LUIS.

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




## <a name="open-the-luis-app"></a>Otwórz aplikację usługi LUIS
Po zakończeniu działania skryptu możesz zalogować się do [Luis](luis-reference-regions.md) i zobaczyć aplikację Luis utworzoną w obszarze **Moje aplikacje**. Powinno być możliwe zobaczyć wypowiedzi dodana w obszarze **wlaczac**, **wyłączanie**, i **Brak** intencji.

![Celem wlaczac](./media/luis-tutorial-node-import-utterances-csv/imported-utterances-661.png)


## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Testowanie i uczenie aplikacji w witrynie sieci Web usługi LUIS](luis-interactive-test.md)

## <a name="additional-resources"></a>Zasoby dodatkowe

Ta przykładowa aplikacja korzysta z następujących interfejsów API usługi LUIS:
- [Tworzenie aplikacji](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36)
- [Dodawanie intencji](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0c)
- [Dodaj jednostki](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0e) 
- [Dodawanie wypowiedzi](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c09)
