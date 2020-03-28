---
title: 'Samouczek: Rozumienie języka Bot Node.js v4'
description: Za pomocą Node.js, skompiluj bota czatu zintegrowanego ze zrozumieniem języka (LUIS) w tym samouczku. Ten czatbot używa aplikacji Human Resources, aby szybko wdrożyć rozwiązanie bota. Bot jest tworzony za pomocą platformy Bot Framework w wersji 4 i bota aplikacji internetowej platformy Azure.
ms.topic: tutorial
ms.date: 02/03/2020
ms.openlocfilehash: 3ce12176957412a5599ced8b043f553969194efb
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76987848"
---
# <a name="tutorial-use-a-web-app-bot-enabled-with-language-understanding-in-nodejs"></a>Samouczek: Korzystanie z bota aplikacji sieci Web włączone z opisem języka w node.js

Użyj Node.js do tworzenia czatu bot zintegrowany ze zrozumieniem języka (LUIS). Bot jest zbudowany z zasobów [bot aplikacji](https://docs.microsoft.com/azure/bot-service/) sieci Web azure i Bot [Framework wersji](https://github.com/Microsoft/botbuilder-dotnet) V4.

**Ten samouczek zawiera informacje na temat wykonywania następujących czynności:**

> [!div class="checklist"]
> * Tworzenie bota aplikacji internetowej. Ten proces tworzy nową aplikację usługi LUIS.
> * Pobierz projekt bota utworzony przez usługę bota sieci Web
> * Uruchamianie bota i emulatora lokalnie na komputerze
> * Wyświetlanie wyników wypowiedzi w bocie

## <a name="prerequisites"></a>Wymagania wstępne

* [Emulator bota](https://aka.ms/abs/build/emulatordownload)
* [Kod programu Visual Studio](https://code.visualstudio.com/Download)


## <a name="create-a-web-app-bot-resource"></a>Tworzenie zasobu bota aplikacji sieci Web

1. W witrynie [Azure Portal](https://portal.azure.com) wybierz polecenie **Utwórz nowy zasób**.

1. W polu wyszukiwania wyszukaj i wybierz pozycję **Web App Bot** (Bot aplikacji internetowej). Wybierz **pozycję Utwórz**.

1. W polu **Bot Service** (Usługa bota) podaj wymagane informacje:

    |Ustawienie|Przeznaczenie|Zalecane ustawienia|
    |--|--|--|
    |Nazwa bota|Nazwa zasobu|`luis-nodejs-bot-` + `<your-name>`, na przykład `luis-nodejs-bot-johnsmith`|
    |Subskrypcja|Subskrypcja miejsca utworzenia bota.|Subskrypcja podstawowa.
    |Grupa zasobów|Logiczna grupa zasobów platformy Azure|Utwórz nową grupę do przechowywania wszystkich zasobów używanych z tym botem, nazwij grupę `luis-nodejs-bot-resource-group`.|
    |Lokalizacja|Region platformy Azure — nie musi być taki sam jak region tworzenia lub publikowania usługi LUIS.|`westus`|
    |Warstwa cenowa|Służy do określania limitów żądań usługi i rozliczeń.|`F0` to warstwa bezpłatna.
    |Nazwa aplikacji|Nazwa jest używana jako domena podrzędna, gdy bot jest wdrażany w chmurze (na przykład humanresourcesbot.azurewebsites.net).|`luis-nodejs-bot-` + `<your-name>`, na przykład `luis-nodejs-bot-johnsmith`|
    |Szablon bota|Ustawienia struktury bota — zobacz następną tabelę|
    |Lokalizacja aplikacji usługi LUIS|Musi być taka sama jak region zasobu usługi LUIS|`westus`|
    |Plan usługi aplikacji/lokalizacja|Nie należy zmieniać wartości domyślnej podanej wartości.|
    |Application Insights|Nie należy zmieniać wartości domyślnej podanej wartości.|
    |Identyfikator aplikacji i hasło firmy Microsoft|Nie należy zmieniać wartości domyślnej podanej wartości.|

1. W **szablonie Bot**wybierz następujące opcje, a następnie wybierz przycisk **Wybierz** w tych ustawieniach:

    |Ustawienie|Przeznaczenie|Wybór|
    |--|--|--|
    |Wersja zestawu SDK|Wersja struktury bota|**Zestaw SDK w wersji 4**|
    |Język zestawu SDK|Język programowania bota|**Node.js**|
    |Bot|Typ bota|**Bot podstawowy**|

1. Wybierz **pozycję Utwórz**. To powoduje utworzenie i wdrożenie usługi bota na platformie Azure. W ramach tego procesu jest tworzona nowa aplikacja usługi LUIS o nazwie `luis-nodejs-bot-XXXX`. Ta nazwa jest oparta na nazwie aplikacji /Azure Bot Service.

    > [!div class="mx-imgBorder"]
    > [![Tworzenie bota aplikacji sieci Web](./media/bfv4-nodejs/create-web-app-service.png)](./media/bfv4-nodejs/create-web-app-service.png#lightbox)

    Poczekaj, aż usługa bot zostanie utworzona przed kontynuowaniem.

## <a name="the-bot-has-a-language-understanding-model"></a>Bot ma model rozumienia języka

Proces tworzenia usługi bot tworzy również nową aplikację usługi LUIS z intencji i wypowiedzi przykład. Bot zapewnia mapowanie intencji do nowej aplikacji LUIS dla następujących intencji:

|Intencje usługi LUIS bota podstawowego|przykładowa wypowiedź|
|--|--|
|Zarezerwuj lot|`Travel to Paris`|
|Cancel|`bye`|
|GetWeather ( GetWeather )|`what's the weather like?`|
|Brak|Cokolwiek spoza domeny aplikacji.|

## <a name="test-the-bot-in-web-chat"></a>Testowanie bota w czacie internetowym

1. Będąc jeszcze w witrynie Azure portal dla nowego bota, wybierz pozycję **Testuj w czacie internetowym**.
1. W polach tekstowych **Wpisz** wiadomość `Book a flight from Seattle to Berlin tomorrow`wprowadź tekst . Bot odpowiada weryfikacją, czy chcesz zarezerwować lot.

    ![Zrzut ekranu przedstawiający witrynę Azure portal, wprowadź tekst "hello".](./media/bfv4-nodejs/ask-bot-question-in-portal-test-in-web-chat.png)

    Za pomocą funkcji testu można szybko przetestować bota. Aby uzyskać bardziej kompletne testowanie, w tym debugowanie, pobierz kod bota i użyj programu Visual Studio.

## <a name="download-the-web-app-bot-source-code"></a>Pobierz kod źródłowy bota aplikacji internetowej
Aby tworzyć kod bota aplikacji internetowej, pobierz kod i użyj go na komputerze lokalnym.

1. W witrynie Azure Portal wybierz pozycję **Build** (Kompilacja) z sekcji **Bot management** (Zarządzanie botem).

1. Wybierz przycisk **Download Bot source code** (Pobierz kod źródłowy bota).

    [![Pobierz kod źródłowy bota aplikacji internetowej dla podstawowego bota](../../../includes/media/cognitive-services-luis/bfv4/download-code.png)](../../../includes/media/cognitive-services-luis/bfv4/download-code.png#lightbox)

1. Gdy w oknie podręcznym pojawi się pytanie **Uwzględnij ustawienia aplikacji w pobranym pliku zip?**, **wybierz**tak . Zapewnia to ustawienia usługi LUIS.

1. Po spakowaniu kodu źródłowego w komunikacie zostanie podany hiperlink umożliwiający pobranie kodu. Wybierz hiperlink.

1. Zapisz plik zip na komputerze lokalnym i wyodrębnij pliki. Otwórz projekt za pomocą programu Visual Studio.

## <a name="review-code-to-send-utterance-to-luis-and-get-response"></a>Przejrzyj kod, aby wysłać wypowiedź do usługi LUIS i uzyskać odpowiedź

1. Aby wysłać wypowiedź użytkownika do punktu końcowego przewidywania usługi LUIS, otwórz **okna dialogowe -> flightBookingRecognizer.js** pliku. Jest to miejsce, gdzie wypowiedź użytkownika wprowadzana do bota jest wysyłania do usługi LUIS. Odpowiedź z usługi LUIS jest zwracana z **executeLuisQuery** metody.

    ````javascript
    class FlightBookingRecognizer {

        ...

        /**
         * Returns an object with preformatted LUIS results for the bot's dialogs to consume.
         * @param {TurnContext} context
         */
        async executeLuisQuery(context) {
            return await this.recognizer.recognize(context);
        }

        ...

    }
    ````

1. **Okna dialogowe -> mainDialog** przechwytuje wypowiedź i wysyła go do executeLuisQuery w actStep metody.


    ````javascript
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.

    const { LuisRecognizer } = require('botbuilder-ai');

    class FlightBookingRecognizer {
        constructor(config) {
            const luisIsConfigured = config && config.applicationId && config.endpointKey && config.endpoint;
            if (luisIsConfigured) {
                this.recognizer = new LuisRecognizer(config, {}, true);
            }
        }

        get isConfigured() {
            return (this.recognizer !== undefined);
        }

        /**
         * Returns an object with preformatted LUIS results for the bot's dialogs to consume.
         * @param {TurnContext} context
         */
        async executeLuisQuery(context) {
            return await this.recognizer.recognize(context);
        }

        getFromEntities(result) {
            let fromValue, fromAirportValue;
            if (result.entities.$instance.From) {
                fromValue = result.entities.$instance.From[0].text;
            }
            if (fromValue && result.entities.From[0].Airport) {
                fromAirportValue = result.entities.From[0].Airport[0][0];
            }

            return { from: fromValue, airport: fromAirportValue };
        }

        getToEntities(result) {
            let toValue, toAirportValue;
            if (result.entities.$instance.To) {
                toValue = result.entities.$instance.To[0].text;
            }
            if (toValue && result.entities.To[0].Airport) {
                toAirportValue = result.entities.To[0].Airport[0][0];
            }

            return { to: toValue, airport: toAirportValue };
        }

        /**
         * This value will be a TIMEX. And we are only interested in a Date so grab the first result and drop the Time part.
         * TIMEX is a format that represents DateTime expressions that include some ambiguity. e.g. missing a Year.
         */
        getTravelDate(result) {
            const datetimeEntity = result.entities.datetime;
            if (!datetimeEntity || !datetimeEntity[0]) return undefined;

            const timex = datetimeEntity[0].timex;
            if (!timex || !timex[0]) return undefined;

            const datetime = timex[0].split('T')[0];
            return datetime;
        }
    }

    module.exports.FlightBookingRecognizer = FlightBookingRecognizer;
    ````
<a name="ask-bot-a-question-for-the-book-flight-intent"></a>

## <a name="use-the-bot-emulator-to-test-the-bot"></a>Użyj emulatora bota, aby przetestować bota

Zadaj botowi pytanie dotyczące intencji lotu książki.

1. Rozpocznij emulator botów i wybierz pozycję **Otwórz bota**.
1. W oknie **podręcznym Otwórz bota** wprowadź adres URL `http://localhost:3978/api/messages`bota, na przykład . Trasa `/api/messages` jest adresem internetowym bota.
1. Wprowadź identyfikator **aplikacji Firmy Microsoft** i hasło do aplikacji **Microsoft**App , znajdujące się w pliku **env** w katalogu głównym pobranego kodu bota.

1. W emulatorze bota wprowadź `Book a flight from Seattle to Berlin tomorrow` i uzyskaj taką samą odpowiedź dla podstawowego bota, jak w **teście w czacie internetowym.**

    [![Podstawowa odpowiedź bota w emulatorze](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png)](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png#lightbox)

1. Wybierz **pozycję Tak**. Bot odpowiada podsumowaniem swoich działań.
1. Z dziennika emulatora bota wybierz wiersz `Luis Trace`zawierający plik . Spowoduje to wyświetlenie odpowiedzi JSON z usługi LUIS dla intencji i jednostek wypowiedź.

    [![Podstawowa odpowiedź bota w emulatorze](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png)](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png#lightbox)


[!INCLUDE [Bot Information](../../../includes/cognitive-services-qnamaker-luis-bot-info.md)]

## <a name="next-steps"></a>Następne kroki

Zobacz więcej [przykładów](https://github.com/microsoft/botframework-solutions) z botami konwersacyjnymi.

> [!div class="nextstepaction"]
> [Tworzenie aplikacji do rozumienia języka przy niestandardowych domenach tematów](luis-quickstart-intents-only.md)