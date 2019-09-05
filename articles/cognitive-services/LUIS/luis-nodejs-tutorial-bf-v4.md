---
title: 'Samouczek: Language Understanding bot Node. js v4'
titleSuffix: Azure Cognitive Services
description: Przy użyciu platformy Node.js utwórz czatbot zintegrowany z usługą Language Understanding (LUIS). Ten czatbot używa aplikacji Human Resources, aby szybko wdrożyć rozwiązanie bota. Bot jest tworzony za pomocą platformy Bot Framework w wersji 4 i bota aplikacji internetowej platformy Azure.
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 09/04/2019
ms.author: diberry
ms.openlocfilehash: 8227a7683c1333b21537630f7f074f624926f984
ms.sourcegitcommit: aebe5a10fa828733bbfb95296d400f4bc579533c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/05/2019
ms.locfileid: "70375768"
---
# <a name="tutorial-use-a-web-app-bot-enabled-with-language-understanding-in-nodejs"></a>Samouczek: Korzystanie z bot aplikacji sieci Web z włączonym Language Understanding w języku Node. js 

Użyj środowiska Node. js, aby skompilować bot z integracją z funkcją interpretacji języka (LUIS). Bot jest tworzona przy użyciu [aplikacji sieci Web](https://docs.microsoft.com/azure/bot-service/) platformy Azure bot Resource i [bot Framework w wersji](https://github.com/Microsoft/botbuilder-dotnet) v4.

**Ten samouczek zawiera informacje na temat wykonywania następujących czynności:**

> [!div class="checklist"]
> * Tworzenie bota aplikacji internetowej. Ten proces tworzy nową aplikację usługi LUIS.
> * Pobierz projekt bot utworzony przez usługę sieci Web bot
> * Uruchamianie bota i emulatora lokalnie na komputerze
> * Wyświetlanie wyników wypowiedzi w bocie

## <a name="prerequisites"></a>Wymagania wstępne

* [Emulator bota](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio Code](https://code.visualstudio.com/Download)


## <a name="create-a-web-app-bot-resource"></a>Tworzenie zasobu bot aplikacji sieci Web

1. W witrynie [Azure Portal](https://portal.azure.com) wybierz polecenie **Utwórz nowy zasób**.

1. W polu wyszukiwania wyszukaj i wybierz pozycję **Web App Bot** (Bot aplikacji internetowej). Wybierz pozycję **Utwórz**.

1. W polu **Bot Service** (Usługa bota) podaj wymagane informacje:

    |Ustawienie|Cel|Zalecane ustawienia|
    |--|--|--|
    |Nazwa bota|Nazwa zasobu|`luis-nodejs-bot-` + `<your-name>`, na przykład `luis-nodejs-bot-johnsmith`|
    |Subscription|Subskrypcja miejsca utworzenia bota.|Subskrypcja podstawowa.
    |Resource group|Logiczna grupa zasobów platformy Azure|Utwórz nową grupę do przechowywania wszystkich zasobów używanych z tym botem, nazwij grupę `luis-nodejs-bot-resource-group`.|
    |Location|Region platformy Azure — nie musi być taki sam jak region tworzenia lub publikowania usługi LUIS.|`westus`|
    |Warstwa cenowa|Służy do określania limitów żądań usługi i rozliczeń.|`F0` to warstwa bezpłatna.
    |Nazwa aplikacji|Nazwa jest używana jako domena podrzędna, gdy bot jest wdrażany w chmurze (na przykład humanresourcesbot.azurewebsites.net).|`luis-nodejs-bot-` + `<your-name>`, na przykład `luis-nodejs-bot-johnsmith`|
    |Szablon bota|Ustawienia struktury bota — zobacz następną tabelę|
    |Lokalizacja aplikacji usługi LUIS|Musi być taka sama jak region zasobu usługi LUIS|`westus`|
    |Plan/Lokalizacja usługi App Service|Nie zmieniaj podanej wartości domyślnej.|
    |Application Insights|Nie zmieniaj podanej wartości domyślnej.|
    |Identyfikator i hasło aplikacji firmy Microsoft|Nie zmieniaj podanej wartości domyślnej.|

1. W **szablonie bot**wybierz poniższe opcje, a następnie wybierz przycisk **Wybierz** w obszarze te ustawienia:

    |Ustawienie|Cel|Wybór|
    |--|--|--|
    |Wersja zestawu SDK|Wersja struktury bota|**Zestaw SDK w wersji 4**|
    |Język zestawu SDK|Język programowania bota|**Node.js**|
    |Bot|Typ bota|**Bot podstawowy**|
    
1. Wybierz pozycję **Utwórz**. To powoduje utworzenie i wdrożenie usługi bota na platformie Azure. W ramach tego procesu jest tworzona nowa aplikacja usługi LUIS o nazwie `luis-nodejs-bot-XXXX`. Ta nazwa jest oparta na nazwie aplikacji usługi/Azure bot.

    [![Tworzenie bota aplikacji internetowej](./media/bfv4-nodejs/create-web-app-service.png)](./media/bfv4-nodejs/create-web-app-service.png#lightbox)

    Przed kontynuowaniem poczekaj na utworzenie usługi bot.

## <a name="the-bot-has-a-language-understanding-model"></a>Bot ma model Language Understanding

Proces tworzenia usługi bot tworzy również nową aplikację LUIS z intencjami i przykładem wyrażenia długości. Bot zapewnia mapowanie intencji do nowej aplikacji LUIS dla następujących intencji: 

|Intencje usługi LUIS bota podstawowego|przykładowa wypowiedź|
|--|--|
|Lot z książki|`Travel to Paris`|
|Cancel|`bye`|
|Brak|Cokolwiek spoza domeny aplikacji.|

## <a name="test-the-bot-in-web-chat"></a>Testowanie bota w czatów internetowych

1. Mimo że w Azure Portal dla nowego bot, wybierz pozycję **Testuj w rozmowie w sieci Web**. 
1. W polu tekstowym **wpisz wiadomość** wpisz tekst `hello`. Bot reaguje na informacje o strukturze bot, a także przykładowe zapytania dotyczące konkretnego modelu LUIS, takiego jak rezerwacja lotu do paryski. 

    ![Zrzut ekranu przedstawiający Azure Portal, wprowadź tekst "Hello".](./media/bfv4-nodejs/ask-bot-question-in-portal-test-in-web-chat.png)

    Możesz użyć funkcji testu, aby szybko przetestować bot. Aby uzyskać pełniejsze testowanie, w tym debugowanie, Pobierz kod bot i użyj programu Visual Studio. 

## <a name="download-the-web-app-bot-source-code"></a>Pobierz kod źródłowy bot aplikacji sieci Web
Aby tworzyć kod bota aplikacji internetowej, pobierz kod i użyj go na komputerze lokalnym. 

1. W witrynie Azure Portal wybierz pozycję **Build** (Kompilacja) z sekcji **Bot management** (Zarządzanie botem). 

1. Wybierz przycisk **Download Bot source code** (Pobierz kod źródłowy bota). 

    [![Pobieranie kodu źródłowego bota aplikacji internetowej dla bota podstawowego](../../../includes/media/cognitive-services-luis/bfv4/download-code.png)](../../../includes/media/cognitive-services-luis/bfv4/download-code.png#lightbox)

1. Po wyświetleniu okna dialogowego z monitem o **uwzględnienie ustawień aplikacji w pobranym pliku zip**wybierz pozycję **tak**.

1. Po spakowaniu kodu źródłowego w komunikacie zostanie podany hiperlink umożliwiający pobranie kodu. Wybierz hiperlink. 

1. Zapisz plik zip na komputerze lokalnym i wyodrębnij pliki. Otwórz projekt za pomocą programu Visual Studio. 

## <a name="review-code-to-send-utterance-to-luis-and-get-response"></a>Przejrzyj kod, aby wysłać wypowiedź do LUIS i uzyskać odpowiedź

1. Otwórz **okno dialogowe > luisHelper. js** . Jest to miejsce, gdzie wypowiedź użytkownika wprowadzana do bota jest wysyłania do usługi LUIS. Odpowiedź z LUIS jest zwracana z metody jako obiekt JSON **bookDetails** . Podczas tworzenia własnego bot należy również utworzyć własny obiekt, aby zwrócić szczegóły z LUIS. 

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

1. Otwórz **okna dialogowe — > bookingDialog. js** , aby zrozumieć, jak obiekt BookingDetails jest używany do zarządzania przepływem konwersacji. Szczegóły podróży zostały przedstawione w krokach, a następnie całe Księgowanie zostanie potwierdzone i ostatecznie powtórzone z powrotem do użytkownika. 

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


## <a name="install-dependencies-and-start-the-bot-code-in-visual-studio"></a>Zainstaluj zależności i uruchom kod bot w programie Visual Studio

1. W programu vscode, z terminalu zintegrowanego, Zainstaluj zależności za pomocą polecenia `npm install`.
1. Również z poziomu terminalu zintegrowanego, uruchom bot za pomocą polecenia `npm start`. 


## <a name="use-the-bot-emulator-to-test-the-bot"></a>Testowanie bot przy użyciu emulatora bot

1. Rozpocznij emulator bot i wybierz pozycję **Otwórz bot**.
1. W wyskakującym okienku Otwórz okno dialogowe **bot** wprowadź adres URL bot, taki jak `http://localhost:3978/api/messages`. `/api/messages` Trasa jest adresem sieci Web dla bot.
1. Wprowadź **Identyfikator aplikacji firmy Microsoft** i **hasło aplikacji firmy**Microsoft, które znajdują się w pliku **ENV** w katalogu głównym pobranego kodu bot.

    Opcjonalnie można utworzyć nową konfigurację bot i skopiować `MicrosoftAppId` plik i `MicrosoftAppPassword` z pliku **ENV** w projekcie programu Visual Studio dla bot. Nazwa pliku konfiguracji bot powinna być taka sama jak nazwa bot. 

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

1. W emulatorze bot wprowadź `Hello` i uzyskaj taką samą odpowiedź dla podstawowego bot, jak w przypadku **testu w rozmowie w sieci Web**.

    [![Odpowiedź bota podstawowego w emulatorze](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png)](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png#lightbox)


## <a name="ask-bot-a-question-for-the-book-flight-intent"></a>Zadawaj bot pytanie dotyczące zamiaru lotu książki

1. W emulatorze bot należy zaksięgować lot, wprowadzając następujący wypowiedź: 

    ```console
    Book a flight from Paris to Berlin on March 22, 2020
    ```

    Emulator bot prosi o potwierdzenie. 

1. Wybierz pozycję **tak**. Bot reaguje z podsumowaniem jego akcji. 
1. Z dziennika emulatora bot wybierz wiersz, który zawiera `Luis Trace`. Spowoduje to wyświetlenie odpowiedzi JSON z LUIS dla zamiar i jednostek wypowiedź.

    [![Odpowiedź bota podstawowego w emulatorze](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png)](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png#lightbox)


[!INCLUDE [Bot Information](../../../includes/cognitive-services-qnamaker-luis-bot-info.md)]

## <a name="next-steps"></a>Następne kroki

Zobacz więcej [przykładów](https://github.com/microsoft/botframework-solutions) z botami konwersacyjnymi. 

> [!div class="nextstepaction"]
> [Tworzenie aplikacji Language Understanding z niestandardową domeną podmiotu](luis-quickstart-intents-only.md)