---
title: Tworzenie aplikacji LUIS programowo przy użyciu środowiska Node.js | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Dowiedz się, jak utworzyć aplikację LUIS programowo z istniejących danych w formacie CSV przy użyciu interfejsu API tworzenia LUIS.
services: cognitive-services
author: DeniseMak
manager: rstand
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 02/21/2018
ms.author: v-geberr
ms.openlocfilehash: 09c9d4da835b7b30fd132770f9d13b33fa80a3f5
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36268330"
---
# <a name="build-a-luis-app-programmatically-using-nodejs"></a>Tworzenie aplikacji LUIS programowo przy użyciu środowiska Node.js

Programowe interfejsu API, który wykonuje wszystkie elementy, które zapewnia LUIS [LUIS] [ LUIS] jest witryny sieci Web. To zaoszczędzić czas, gdy masz już istniejących danych i będzie szybsze tworzenie aplikacji LUIS programowo niż ręcznie wprowadzić informacje. 

## <a name="prerequisites"></a>Wymagania wstępne

* Zaloguj się do [LUIS] [ LUIS] witryny sieci Web i Znajdź Twojej [tworzenia klucza](luis-concept-keys.md#authoring-key) w ustawieniach konta. Ten klucz służy do wywoływania interfejsów API tworzenia.
* Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* W tym samouczku rozpoczyna się od CSV dla plików dziennika hipotetyczny firmy żądań użytkownika. Pobierz go [tutaj](https://github.com/Microsoft/LUIS-Samples/blob/master/examples/build-app-programmatically-csv/IoT.csv).
* Zainstaluj najnowsze Node.js z programu NPM. Pobierz go z [tutaj](https://nodejs.org/en/download/).
* **[Zalecane]**  Visual Studio Code IntelliSense i debugowanie, pobierz go z [tutaj](https://code.visualstudio.com/) bezpłatnie.

## <a name="map-preexisting-data-to-intents-and-entities"></a>Mapowanie istniejących danych do lokalizacji docelowych i jednostek
Nawet jeśli masz system, który nie został utworzony z LUIS pamiętać, jeśli zawiera on danych tekstowych, mapy użytkownikom różne chcesz zrobić, może być opracowywane mapowania z istniejących kategorii danych wejściowych użytkownika do lokalizacji docelowych w LUIS. Po zidentyfikowaniu ważne słów ani fraz w użytkowników powiedział, te słowa, które mogą być mapowane do jednostek.

Otwórz plik `IoT.csv`. Zawiera dziennik kwerend użytkowników do usługi hipotetyczny automatyzacji macierzystego, w tym jak zostały skategoryzowane, użytkownik powiedział i niektóre kolumny z przydatnych informacji na temat pobierane z nich. 

![Plik CSV](./media/luis-tutorial-node-import-utterances-csv/csv.png) 

Zostanie wyświetlony **RequestType** kolumna może być lokalizacji docelowych i **żądania** kolumna zawiera utterance przykład. Pozostałe pola można jednostek, jeśli występują one w utterance. Ponieważ istnieje lokalizacji docelowych, jednostki i przykład zniesławiających, masz wymagań dotyczących prostego, przykładowej aplikacji.

## <a name="steps-to-generate-a-luis-app-from-non-luis-data"></a>Kroki, aby wygenerować aplikacji LUIS na podstawie danych z systemem innym niż LUIS
Aby wygenerować nową aplikację LUIS z pliku źródłowego, najpierw należy przeanalizować dane z pliku CSV i Konwertuj do formatu, który można przekazać do LUIS przy użyciu interfejsu API tworzenia tych danych. Z przeanalizowanych danych można zbierać informacje o jakie intencje i jednostek są dostępne. Następnie wywołań interfejsu API do utworzenia aplikacji i Dodaj intencje i jednostek, które zostały zebrane z przeanalizowanych danych. Po utworzeniu aplikacji LUIS zniesławiających przykład można dodać z przeanalizowanych danych. Ten przepływ w ostatniej części następujący kod jest widoczny. Kopiowanie lub [Pobierz](https://github.com/Microsoft/LUIS-Samples/blob/master/examples/build-app-programmatically-csv/index.js) ten kod i zapisz go w `index.js`.

   [!code-javascript[Node.js code for calling the steps to build a LUIS app](~/samples-luis/examples/build-app-programmatically-csv/index.js)]


## <a name="parse-the-csv"></a>Przeanalizować CSV

Wpisów kolumn, które zawierają zniesławiających w woluminie CSV mają do przeanalizowania do formatu JSON, który można poznać LUIS. Ten format JSON musi zawierać `intentName` pole, które identyfikuje celem utterance. Musi zawierać `entityLabels` pola, które może być pusta, jeśli nie ma żadnych podmiotów w utterance. 

Na przykład pozycję "Włącz kontrolki" mapowana na dane JSON:

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

W tym przykładzie `intentName` pochodzi z żądania użytkownika w obszarze **żądania** nagłówek kolumny w pliku CSV i `entityName` pochodzi z innych kolumn z informacjami klucza. Na przykład, jeśli istnieje wpis dotyczący **operacji** lub **urządzenia**, a ciąg występuje również w rzeczywistego żądania, a następnie mogą zostać oznaczone etykietami jako jednostki. Poniższy kod ilustruje, to podczas analizowania procesu. Możesz skopiować lub [Pobierz](https://github.com/Microsoft/LUIS-Samples/blob/master/examples/build-app-programmatically-csv/_parse.js) go i zapisać go do `_parse.js`.

   [!code-javascript[Node.js code for parsing a CSV file to extract intents, entities, and labeled utterances](~/samples-luis/examples/build-app-programmatically-csv/_parse.js)]



## <a name="create-the-luis-app"></a>Tworzenie aplikacji LUIS
Po przeanalizowaniu danych do postaci JSON, należy go dodać do aplikacji LUIS. Poniższy kod tworzy LUIS aplikacji. Kopiowanie lub [Pobierz](https://github.com/Microsoft/LUIS-Samples/blob/master/examples/build-app-programmatically-csv/_create.js) i zapisz go w `_create.js`.

   [!code-javascript[Node.js code for creating a LUIS app](~/samples-luis/examples/build-app-programmatically-csv/_create.js)]


## <a name="add-intents"></a>Dodawanie intencji
Po utworzeniu aplikacji, musisz intencje do niego. Poniższy kod tworzy LUIS aplikacji. Kopiowanie lub [Pobierz](https://github.com/Microsoft/LUIS-Samples/blob/master/examples/build-app-programmatically-csv/_intents.js) i zapisz go w `_intents.js`.

   [!code-javascript[Node.js code for creating a series of intents](~/samples-luis/examples/build-app-programmatically-csv/_intents.js)]


## <a name="add-entities"></a>Dodawanie jednostek
Poniższy kod dodaje jednostek aplikacji LUIS. Kopiowanie lub [Pobierz](https://github.com/Microsoft/LUIS-Samples/blob/master/examples/build-app-programmatically-csv/_entities.js) i zapisz go w `_entities.js`.

   [!code-javascript[Node.js code for creating entities](~/samples-luis/examples/build-app-programmatically-csv/_entities.js)]
   


## <a name="add-utterances"></a>Dodawanie wypowiedzi
Po zdefiniowaniu jednostki i intencje w aplikacji LUIS można dodać zniesławiających. Poniższy kod używa [Utterances_AddBatch](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c09) interfejsu API, który umożliwia dodanie maksymalnie 100 zniesławiających naraz.  Kopiowanie lub [Pobierz](https://github.com/Microsoft/LUIS-Samples/blob/master/examples/build-app-programmatically-csv/_upload.js) i zapisz go w `_upload.js`.

   [!code-javascript[Node.js code for adding utterances](~/samples-luis/examples/build-app-programmatically-csv/_upload.js)]


## <a name="run-the-code"></a>Uruchamianie kodu


### <a name="install-nodejs-dependencies"></a>Zainstaluj zależności środowiska Node.js
Zainstaluj zależności Node.js z pakietu NPM w wierszu polecenia/terminalu.

````
> npm install
````

### <a name="change-configuration-settings"></a>Zmień ustawienia konfiguracji
Aby można było używać tej aplikacji, musisz Zmień wartości w pliku index.js klucz subskrypcji i podaj nazwę aplikacji na. Można również ustawić kultury aplikacji lub zmienić numer wersji.

Otwórz plik index.js i zmień wartości tych w górnej części pliku.


````JavaScript
// Change these values
const LUIS_programmaticKey = "YOUR_PROGRAMMATIC_KEY";
const LUIS_appName = "Sample App";
const LUIS_appCulture = "en-us"; 
const LUIS_versionId = "0.1";
````
### <a name="run-the-script"></a>Uruchom skrypt
Uruchom skrypt z wiersza polecenia/na terminalu za pomocą języka Node.js.

````
> node index.js
````
lub
````
> npm start
````

### <a name="application-progress"></a>Postęp aplikacji
Gdy aplikacja jest uruchomiona, w wierszu polecenia pokazywania postępu. Dane wyjściowe wiersza polecenia zawiera format odpowiedzi z LUIS.

````
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
````




## <a name="open-the-luis-app"></a>Otwórz aplikację LUIS
Po ukończeniu działania skryptu, należy zalogować się do [LUIS] [ LUIS] i aplikacja LUIS utworzono w obszarze **Moje aplikacje**. Można wyświetlić zniesławiających dodany w obszarze **wlaczac**, **wyłączanie**, i **Brak** lokalizacji docelowych.

![Celem wlaczac](./media/luis-tutorial-node-import-utterances-csv/imported-utterances-661.png)


## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Testowanie i uczenie aplikacji w witrynie sieci Web LUIS](interactive-test.md)

## <a name="additional-resources"></a>Zasoby dodatkowe

Ta przykładowa aplikacja korzysta z poniższych interfejsów API LUIS:
- [Tworzenie aplikacji](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36)
- [Dodaj intencje](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0c)
- [Dodaj jednostki](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0e) 
- [Dodaj zniesławiających](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c09) 

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions

