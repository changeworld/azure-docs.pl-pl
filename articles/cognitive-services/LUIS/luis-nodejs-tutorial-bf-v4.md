---
title: Bot interpretacji języka Node.js w wersji 4
titleSuffix: Azure Cognitive Services
description: Przy użyciu platformy Node.js utwórz czatbot zintegrowany z usługą Language Understanding (LUIS). Ten czatbot używa aplikacji Human Resources, aby szybko wdrożyć rozwiązanie bota. Bot jest tworzony za pomocą platformy Bot Framework w wersji 4 i bota aplikacji internetowej platformy Azure.
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 06/15/2019
ms.author: diberry
ms.openlocfilehash: 832a62c5cc5440d81f4b92d2463a563f5bb884a3
ms.sourcegitcommit: 6e6813f8e5fa1f6f4661a640a49dc4c864f8a6cb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/17/2019
ms.locfileid: "67150810"
---
# <a name="tutorial-use-a-web-app-bot-enabled-with-language-understanding-in-nodejs"></a>Samouczek: Użyj Bot aplikacji sieci Web, włączone z usługą interpretacji języka w środowisku Node.js 

Za pomocą środowiska Node.js do tworzenia czatbot zintegrowane z usługą language understanding (LUIS). Bot jest oparte na platformie Azure [sieci Web aplikacji bot](https://docs.microsoft.com/azure/bot-service/) zasobów i [Bot Framework w wersji](https://github.com/Microsoft/botbuilder-dotnet) V4.

**Ten samouczek zawiera informacje na temat wykonywania następujących czynności:**

> [!div class="checklist"]
> * Tworzenie bota aplikacji internetowej. Ten proces tworzy nową aplikację usługi LUIS.
> * Pobieranie projektu bot utworzone przez usługę sieci Web bot
> * Uruchamianie bota i emulatora lokalnie na komputerze
> * Wyświetlanie wyników wypowiedzi w bocie

## <a name="prerequisites"></a>Wymagania wstępne

* [Emulator bota](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio Code](https://code.visualstudio.com/Download)


## <a name="create-a-web-app-bot-resource"></a>Utwórz zasób bot aplikacji sieci web

1. W witrynie [Azure Portal](https://portal.azure.com) wybierz polecenie **Utwórz nowy zasób**.

1. W polu wyszukiwania wyszukaj i wybierz pozycję **Web App Bot** (Bot aplikacji internetowej). Wybierz pozycję **Utwórz**.

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
    |App service plan/lokalizacja|Nie należy zmieniać z wartości podanej domyślnej.|
    |Application Insights|Nie należy zmieniać z wartości podanej domyślnej.|
    |Identyfikator aplikacji firmy Microsoft i hasła|Nie należy zmieniać z wartości podanej domyślnej.|

1. W **szablonu Bota**, wybrać następujące opcje, a następnie wybierz **wybierz** przycisku w ramach tych ustawień:

    |Ustawienie|Przeznaczenie|Wybór|
    |--|--|--|
    |Wersja zestawu SDK|Wersja struktury bota|**Zestaw SDK w wersji 4**|
    |Język zestawu SDK|Język programowania bota|**Node.js**|
    |Bot|Typ bota|**Bot podstawowy**|
    
1. Wybierz pozycję **Utwórz**. To powoduje utworzenie i wdrożenie usługi bota na platformie Azure. W ramach tego procesu jest tworzona nowa aplikacja usługi LUIS o nazwie `luis-nodejs-bot-XXXX`. Ta nazwa opiera się na nazwę aplikacji /Azure Bot Service.

    [![Tworzenie bota aplikacji internetowej](./media/bfv4-nodejs/create-web-app-service.png)](./media/bfv4-nodejs/create-web-app-service.png#lightbox)

    Poczekaj, aż usługa bot zostanie utworzona przed kontynuowaniem.

## <a name="the-bot-has-a-language-understanding-model"></a>Bot wykorzystuje model rozpoznawania języka

Proces tworzenia bota usługa również tworzy nową aplikację usługi LUIS z intencje i wypowiedzi przykład. Bot zapewnia mapowanie intencji do nowej aplikacji LUIS dla następujących intencji: 

|Intencje usługi LUIS bota podstawowego|przykładowa wypowiedź|
|--|--|
|Flight książki|`Travel to Paris`|
|Cancel|`bye`|
|Brak|Cokolwiek spoza domeny aplikacji.|

## <a name="test-the-bot-in-web-chat"></a>Testowanie bota w czatów internetowych

1. Mając nadal w witrynie Azure portal. nowe bot, wybierz **testu w czatów internetowych**. 
1. W **wpisz wiadomość** polu tekstowym wprowadź tekst `hello`. Bot odpowiada za pomocą informacji na temat platformy bot framework, a także przykładowe zapytania dla określonego modelu usługi LUIS, takich jak rezerwacji lotu do Paryża. 

    ![Zrzut ekranu witryny Azure portal, wprowadź tekst "hello".](./media/bfv4-nodejs/ask-bot-question-in-portal-test-in-web-chat.png)

    Funkcja testów można używać do szybkiego testowania bota. Aby uzyskać więcej informacji o ukończenie testowania, w tym debugowania i Pobierz program bot code przy użyciu programu Visual Studio. 

## <a name="download-the-web-app-bot-source-code"></a>Pobierz kod źródłowy bot aplikacji sieci web
Aby tworzyć kod bota aplikacji internetowej, pobierz kod i użyj go na komputerze lokalnym. 

1. W witrynie Azure Portal wybierz pozycję **Build** (Kompilacja) z sekcji **Bot management** (Zarządzanie botem). 

1. Wybierz przycisk **Download Bot source code** (Pobierz kod źródłowy bota). 

    [![Pobieranie kodu źródłowego bota aplikacji internetowej dla bota podstawowego](../../../includes/media/cognitive-services-luis/bfv4/download-code.png)](../../../includes/media/cognitive-services-luis/bfv4/download-code.png#lightbox)

1. Po wyskakującego okna dialogowego komunikatu z pytaniem **Uwzględnij ustawienia aplikacji w pliku zip pobranego?** , wybierz opcję **tak**.

1. Po spakowaniu kodu źródłowego w komunikacie zostanie podany hiperlink umożliwiający pobranie kodu. Wybierz hiperlink. 

1. Zapisz plik zip na komputerze lokalnym i wyodrębnij pliki. Otwórz projekt za pomocą programu Visual Studio. 

## <a name="review-code-to-send-utterance-to-luis-and-get-response"></a>Przejrzyj kod, aby wysyłać wypowiedź LUIS i Uzyskaj odpowiedzi

1. Otwórz **okien dialogowych -> luisHelper.js** pliku. Jest to miejsce, gdzie wypowiedź użytkownika wprowadzana do bota jest wysyłania do usługi LUIS. Odpowiedź z usługi LUIS jest zwracana z metody jako **bookDetails** obiekt JSON. Podczas tworzenia własnych bot, należy również utworzyć obiekt do zwrócenia szczegółowe informacje z usługi LUIS. 

    ```nodejs
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.
    
    const { LuisRecognizer } = require('botbuilder-ai');
    
    class LuisHelper {
        /**
         * Returns an object with preformatted LUIS results for the bot's dialogs to consume.
         * @param {*} logger
         * @param {TurnContext} context
         */
        static async executeLuisQuery(logger, context) {
            const bookingDetails = {};
    
            try {
                const recognizer = new LuisRecognizer({
                    applicationId: process.env.LuisAppId,
                    endpointKey: process.env.LuisAPIKey,
                    endpoint: `https://${ process.env.LuisAPIHostName }`
                }, {}, true);
    
                const recognizerResult = await recognizer.recognize(context);
    
                const intent = LuisRecognizer.topIntent(recognizerResult);
    
                bookingDetails.intent = intent;
    
                if (intent === 'Book_flight') {
                    // We need to get the result from the LUIS JSON which at every level returns an array
    
                    bookingDetails.destination = LuisHelper.parseCompositeEntity(recognizerResult, 'To', 'Airport');
                    bookingDetails.origin = LuisHelper.parseCompositeEntity(recognizerResult, 'From', 'Airport');
    
                    // This value will be a TIMEX. And we are only interested in a Date so grab the first result and drop the Time part.
                    // TIMEX is a format that represents DateTime expressions that include some ambiguity. e.g. missing a Year.
                    bookingDetails.travelDate = LuisHelper.parseDatetimeEntity(recognizerResult);
                }
            } catch (err) {
                logger.warn(`LUIS Exception: ${ err } Check your LUIS configuration`);
            }
            return bookingDetails;
        }
    
        static parseCompositeEntity(result, compositeName, entityName) {
            const compositeEntity = result.entities[compositeName];
            if (!compositeEntity || !compositeEntity[0]) return undefined;
    
            const entity = compositeEntity[0][entityName];
            if (!entity || !entity[0]) return undefined;
    
            const entityValue = entity[0][0];
            return entityValue;
        }
    
        static parseDatetimeEntity(result) {
            const datetimeEntity = result.entities['datetime'];
            if (!datetimeEntity || !datetimeEntity[0]) return undefined;
    
            const timex = datetimeEntity[0]['timex'];
            if (!timex || !timex[0]) return undefined;
    
            const datetime = timex[0].split('T')[0];
            return datetime;
        }
    }
    
    module.exports.LuisHelper = LuisHelper;
    ```

1. Otwórz **okien dialogowych -> bookingDialog.js** Aby zrozumieć, jak obiekt BookingDetails jest używany do zarządzania przepływem konwersacji. Szczegóły podróży, zostaną poproszeni o w krokach, a następnie cały rezerwacji jest potwierdzone, a na koniec jest powtarzany do użytkownika. 

    ```nodejs
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.
    
    const { TimexProperty } = require('@microsoft/recognizers-text-data-types-timex-expression');
    const { ConfirmPrompt, TextPrompt, WaterfallDialog } = require('botbuilder-dialogs');
    const { CancelAndHelpDialog } = require('./cancelAndHelpDialog');
    const { DateResolverDialog } = require('./dateResolverDialog');
    
    const CONFIRM_PROMPT = 'confirmPrompt';
    const DATE_RESOLVER_DIALOG = 'dateResolverDialog';
    const TEXT_PROMPT = 'textPrompt';
    const WATERFALL_DIALOG = 'waterfallDialog';
    
    class BookingDialog extends CancelAndHelpDialog {
        constructor(id) {
            super(id || 'bookingDialog');
    
            this.addDialog(new TextPrompt(TEXT_PROMPT))
                .addDialog(new ConfirmPrompt(CONFIRM_PROMPT))
                .addDialog(new DateResolverDialog(DATE_RESOLVER_DIALOG))
                .addDialog(new WaterfallDialog(WATERFALL_DIALOG, [
                    this.destinationStep.bind(this),
                    this.originStep.bind(this),
                    this.travelDateStep.bind(this),
                    this.confirmStep.bind(this),
                    this.finalStep.bind(this)
                ]));
    
            this.initialDialogId = WATERFALL_DIALOG;
        }
    
        /**
         * If a destination city has not been provided, prompt for one.
         */
        async destinationStep(stepContext) {
            const bookingDetails = stepContext.options;
    
            if (!bookingDetails.destination) {
                return await stepContext.prompt(TEXT_PROMPT, { prompt: 'To what city would you like to travel?' });
            } else {
                return await stepContext.next(bookingDetails.destination);
            }
        }
    
        /**
         * If an origin city has not been provided, prompt for one.
         */
        async originStep(stepContext) {
            const bookingDetails = stepContext.options;
    
            // Capture the response to the previous step's prompt
            bookingDetails.destination = stepContext.result;
            if (!bookingDetails.origin) {
                return await stepContext.prompt(TEXT_PROMPT, { prompt: 'From what city will you be travelling?' });
            } else {
                return await stepContext.next(bookingDetails.origin);
            }
        }
    
        /**
         * If a travel date has not been provided, prompt for one.
         * This will use the DATE_RESOLVER_DIALOG.
         */
        async travelDateStep(stepContext) {
            const bookingDetails = stepContext.options;
    
            // Capture the results of the previous step
            bookingDetails.origin = stepContext.result;
            if (!bookingDetails.travelDate || this.isAmbiguous(bookingDetails.travelDate)) {
                return await stepContext.beginDialog(DATE_RESOLVER_DIALOG, { date: bookingDetails.travelDate });
            } else {
                return await stepContext.next(bookingDetails.travelDate);
            }
        }
    
        /**
         * Confirm the information the user has provided.
         */
        async confirmStep(stepContext) {
            const bookingDetails = stepContext.options;
    
            // Capture the results of the previous step
            bookingDetails.travelDate = stepContext.result;
            const msg = `Please confirm, I have you traveling to: ${ bookingDetails.destination } from: ${ bookingDetails.origin } on: ${ bookingDetails.travelDate }.`;
    
            // Offer a YES/NO prompt.
            return await stepContext.prompt(CONFIRM_PROMPT, { prompt: msg });
        }
    
        /**
         * Complete the interaction and end the dialog.
         */
        async finalStep(stepContext) {
            if (stepContext.result === true) {
                const bookingDetails = stepContext.options;
    
                return await stepContext.endDialog(bookingDetails);
            } else {
                return await stepContext.endDialog();
            }
        }
    
        isAmbiguous(timex) {
            const timexPropery = new TimexProperty(timex);
            return !timexPropery.types.has('definite');
        }
    }
    
    module.exports.BookingDialog = BookingDialog;
    ```


## <a name="install-dependencies-and-start-the-bot-code-in-visual-studio"></a>Instalowanie zależności i uruchomić kod robotów w programie Visual Studio

1. W programu VSCode, z poziomu terminalu zintegrowanego, zainstalowanie zależności za pomocą polecenia `npm install`.
1. Również w zintegrowanym terminalu Uruchom bota przy użyciu polecenia `npm start`. 


## <a name="use-the-bot-emulator-to-test-the-bot"></a>Korzystanie z emulatora bot do testowania robota

1. Rozpocznij emulatora Bot, a następnie wybierz pozycję **Otwórz Bot**.
1. W **Otwórz robota** wyskakującego okna dialogowego, wprowadź adres URL bot, takich jak `http://localhost:3978/api/messages`. `/api/messages` Trasy jest adres sieci web dla bota.
1. Wprowadź **Identyfikatora aplikacji Microsoft** i **hasło Microsoft App**znajdujące się w **ENV** plik w folderze głównym kodu bot został pobrany.

    Opcjonalnie możesz utworzyć nowe bot konfiguracji i skopiuj `MicrosoftAppId` i `MicrosoftAppPassword` z **ENV** pliku w projekcie programu Visual Studio dla bota. Nazwa pliku konfiguracji robota powinna być taka sama jak nazwa robota. 

    ```json
    {
        "name": "<bot name>",
        "description": "<bot description>",
        "services": [
            {
                "type": "endpoint",
                "appId": "<appId from .env>",
                "appPassword": "<appPassword from .env>",
                "endpoint": "http://localhost:3978/api/messages",
                "id": "<don't change this value>",
                "name": "http://localhost:3978/api/messages"
            }
        ],
        "padlock": "",
        "version": "2.0",
        "overrides": null,
        "path": "<local path to .bot file>"
    }
    ```

1. Bot, w emulatorze wpisz `Hello` i uzyskać tę samą odpowiedź na podstawowy bot, jak wartość otrzymana w **testu w czatów internetowych**.

    [![Odpowiedź bota podstawowego w emulatorze](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png)](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png#lightbox)


## <a name="ask-bot-a-question-for-the-book-flight-intent"></a>Zadaj pytanie na intencje lotu książki bot

1. W emulatorze bot książki lotu, wprowadzając następujące wypowiedź: 

    ```bot
    Book a flight from Paris to Berlin on March 22, 2020
    ```

    Bot emulator z prośbą o potwierdzenie. 

1. Wybierz **tak**. Bot odpowiada za pomocą podsumowanie działań. 
1. W dzienniku emulatora bot, wybierz wiersz, który zawiera `Luis Trace`. Spowoduje to wyświetlenie odpowiedź JSON z usługi LUIS intencje i podmioty wypowiedź.

    [![Odpowiedź bota podstawowego w emulatorze](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png)](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png#lightbox)

## <a name="learn-more-about-the-web-app-bot-and-framework"></a>Dowiedz się więcej o Bot aplikacji sieci Web i platforma

Usługa Azure Bot używa zestawu SDK Bot Framework. Dowiedz się więcej na temat zestawu SDK i struktury Bot Framework:

* Dokumentacja [usługi Azure Bot Service](https://docs.microsoft.com/azure/bot-service/bot-service-overview-introduction?view=azure-bot-service-4.0) w wersji 4
* [Przykłady Bot Builder](https://github.com/Microsoft/botbuilder-samples)
* [Bot Builder Node.js SDK](https://github.com/Microsoft/botbuilder-js)
* [Narzędzia Bot Builder](https://github.com/Microsoft/botbuilder-tools):

## <a name="next-steps"></a>Kolejne kroki

Zobacz więcej [przykładów](https://github.com/microsoft/botframework-solutions) z botami konwersacyjnymi. 

> [!div class="nextstepaction"]
> [Tworzenie aplikacji przy użyciu domeny podmiotu niestandardowego Language Understanding](luis-quickstart-intents-only.md)