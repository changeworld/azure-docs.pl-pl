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
ms.date: 09/05/2019
ms.author: diberry
ms.openlocfilehash: 63a0717e615ff85dbc5cfc06567f83cb9aa83a30
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/06/2019
ms.locfileid: "70735011"
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
|Getpogoda|`what's the weather like?`|
|Brak|Cokolwiek spoza domeny aplikacji.|

## <a name="test-the-bot-in-web-chat"></a>Testowanie bota w czatów internetowych

1. Mimo że w Azure Portal dla nowego bot, wybierz pozycję **Testuj w rozmowie w sieci Web**. 
1. W polu tekstowym **wpisz wiadomość** wpisz tekst `Book a flight from Seattle to Berlin tomorrow`. Bot reaguje na weryfikację, aby zaksięgować lot. 

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

1. Aby wysłać użytkownika wypowiedź do punktu końcowego przewidywania LUIS, Otwórz **okno dialogowe > flightBookingRecognizer. js** . Jest to miejsce, gdzie wypowiedź użytkownika wprowadzana do bota jest wysyłania do usługi LUIS. Odpowiedź z LUIS jest zwracana z metody **executeLuisQuery** .  

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

1. **Okna dialogowe-> mainDialog** przechwytuje wypowiedź i wysyła je do executeLuisQuery w metodzie actStep.


    ````javascript
    class MainDialog extends ComponentDialog {

        ...

        /**
         * Second step in the waterfall.  This will use LUIS to attempt to extract the origin, destination and travel dates.
         * Then, it hands off to the bookingDialog child dialog to collect any remaining details.
         */
        async actStep(stepContext) {

            ...

            const luisResult = await this.luisRecognizer.executeLuisQuery(stepContext.context);

            switch (LuisRecognizer.topIntent(luisResult)) {
                    case 'BookFlight':
                        // Extract the values for the composite entities from the LUIS result.
                        const fromEntities = this.luisRecognizer.getFromEntities(luisResult);
                        const toEntities = this.luisRecognizer.getToEntities(luisResult);
            
                        // Show a warning for Origin and Destination if we can't resolve them.
                        await this.showWarningForUnsupportedCities(stepContext.context, fromEntities, toEntities);
            
                        // Initialize BookingDetails with any entities we may have found in the response.
                        bookingDetails.destination = toEntities.airport;
                        bookingDetails.origin = fromEntities.airport;
                        bookingDetails.travelDate = this.luisRecognizer.getTravelDate(luisResult);
                        console.log('LUIS extracted these booking details:', JSON.stringify(bookingDetails));
            
                        // Run the BookingDialog passing in whatever details we have from the LUIS call, it will fill out the remainder.
                        return await stepContext.beginDialog('bookingDialog', bookingDetails);
            
                    case 'GetWeather':
                        // We haven't implemented the GetWeatherDialog so we just display a TODO message.
                        const getWeatherMessageText = 'TODO: get weather flow here';
                        await stepContext.context.sendActivity(getWeatherMessageText, getWeatherMessageText, InputHints.IgnoringInput);
                        break;
            
                    default:
                        // Catch all for unhandled intents
                        const didntUnderstandMessageText = `Sorry, I didn't get that. Please try asking in a different way (intent was ${ LuisRecognizer.topIntent(luisResult) })`;
                        await stepContext.context.sendActivity(didntUnderstandMessageText, didntUnderstandMessageText, InputHints.IgnoringInput);
                    }
            
                    return await stepContext.next();

        }

        ...

    }
    ````


## <a name="install-dependencies-and-start-the-bot-code-in-visual-studio"></a>Zainstaluj zależności i uruchom kod bot w programie Visual Studio

1. W programu vscode, z terminalu zintegrowanego, Zainstaluj zależności za pomocą polecenia `npm install`.
1. Również z poziomu terminalu zintegrowanego, uruchom bot za pomocą polecenia `npm start`. Spowoduje to rozpoczęcie aplikacji sieci Web dla bot za pomocą punktu końcowego HTTP. Konsola programu udostępnia adres URL i numer portu, aby uzyskać dostęp do uruchomionej witryny sieci Web. Numer portu jest wymagany w następnej sekcji tego samouczka.

    ```console
    > core-bot@1.0.0 start C:\Users\diberry\repos\bots\2019-bot-nodejs-basic
    > node ./index.js
    
    
    restify listening to http://[::]:3978
    
    Get Bot Framework Emulator: https://aka.ms/botframework-emulator
    ```

## <a name="create-an-environment-file-and-add-luis-values"></a>Utwórz plik środowiska i Dodaj wartości LUIS

Emulator bot potrzebuje dostępu do zasobu LUIS w celu zapewnienia szczegółowych wyników LUIS.

1. W katalogu głównym projektu Utwórz plik o nazwie `.env` i Dodaj następujące zmienne środowiskowe:

    ```console
    LuisAppId= 
    LuisAPIKey=
    LuisAPIHostName=
    ```

1. W Azure Portal dla zasobu bot Otwórz ustawienia konfiguracji App Service dla aplikacji.
1. Otwórz pozycję **Edycja zaawansowana**, aby wyświetlić wartość dla każdego ustawienia.

    ![Otwórz * * Advanced Edit * *, aby wyświetlić wartość dla każdego ustawienia.](./media/bfv4-nodejs/environment-settings-for-luis-app.png)

<a name="ask-bot-a-question-for-the-book-flight-intent"></a>

## <a name="use-the-bot-emulator-to-test-the-bot"></a>Testowanie bot przy użyciu emulatora bot

Zażądaj bot pytania dotyczącego zamiaru lotów w ramach książki.

1. Rozpocznij emulator bot i wybierz pozycję **Otwórz bot**.
1. W wyskakującym okienku Otwórz okno dialogowe **bot** wprowadź adres URL bot, taki jak `http://localhost:3978/api/messages`. `/api/messages` Trasa jest adresem sieci Web dla bot.

1. W emulatorze bot wprowadź `Book a flight from Seattle to Berlin tomorrow` i uzyskaj taką samą odpowiedź dla podstawowego bot, jak w przypadku **testu w rozmowie w sieci Web**.

    [![Odpowiedź bota podstawowego w emulatorze](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png)](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png#lightbox)

1. Wybierz pozycję **tak**. Bot reaguje z podsumowaniem jego akcji. 
1. Z dziennika emulatora bot wybierz wiersz, który zawiera `Luis Trace`. Spowoduje to wyświetlenie odpowiedzi JSON z LUIS dla zamiar i jednostek wypowiedź.

    [![Odpowiedź bota podstawowego w emulatorze](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png)](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png#lightbox)


[!INCLUDE [Bot Information](../../../includes/cognitive-services-qnamaker-luis-bot-info.md)]

## <a name="next-steps"></a>Następne kroki

Zobacz więcej [przykładów](https://github.com/microsoft/botframework-solutions) z botami konwersacyjnymi. 

> [!div class="nextstepaction"]
> [Tworzenie aplikacji Language Understanding z niestandardową domeną podmiotu](luis-quickstart-intents-only.md)