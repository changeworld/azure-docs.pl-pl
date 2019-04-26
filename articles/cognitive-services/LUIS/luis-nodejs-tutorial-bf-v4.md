---
title: Bot — Node.js — v4
titleSuffix: Azure Cognitive Services
description: Przy użyciu platformy Node.js utwórz czatbot zintegrowany z usługą Language Understanding (LUIS). Ten czatbot używa aplikacji Human Resources, aby szybko wdrożyć rozwiązanie bota. Bot jest tworzony za pomocą platformy Bot Framework w wersji 4 i bota aplikacji internetowej platformy Azure.
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 01/30/2019
ms.author: diberry
ms.openlocfilehash: 54bae5548764ed1f89a2ffb7992eb222a058c706
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60194148"
---
# <a name="tutorial-luis-bot-in-nodejs-with-the-bot-framework-4x-and-the-azure-web-app-bot"></a>Samouczek: bot usługi LUIS w środowisku Node.js przy użyciu platformy Bot Framework 4.x i bota aplikacji internetowej platformy Azure
Przy użyciu platformy Node.js można utworzyć czatbot zintegrowany z usługą Language Understanding (LUIS). Ten bot używa aplikacji HomeAutomation do implementacji rozwiązania bota. Bot jest kompilacją aplikacji [bot aplikacji internetowej](https://docs.microsoft.com/azure/bot-service/) na platformie Azure z [Bot Framework w wersji](https://github.com/Microsoft/botbuilder-js) 4.

**Ten samouczek zawiera informacje na temat wykonywania następujących czynności:**

> [!div class="checklist"]
> * Tworzenie bota aplikacji internetowej. Ten proces tworzy nową aplikację usługi LUIS.
> * Dodawanie wstępnie utworzonej domeny do nowego modelu usługi LUIS
> * Pobieranie projektu utworzonego przez usługę internetową bota
> * Uruchamianie bota i emulatora lokalnie na komputerze
> * Modyfikowanie kodu bota dla nowych intencji usługi LUIS
> * Wyświetlanie wyników wypowiedzi w bocie

## <a name="prerequisites"></a>Wymagania wstępne

* [Emulator bota](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio Code](https://code.visualstudio.com/Download)


## <a name="create-web-app-bot"></a>Tworzenie bota aplikacji internetowej

1. W witrynie [Azure Portal](https://portal.azure.com) wybierz polecenie **Utwórz nowy zasób**.

2. W polu wyszukiwania wyszukaj i wybierz pozycję **Web App Bot** (Bot aplikacji internetowej). Wybierz pozycję **Utwórz**.

3. W polu **Bot Service** (Usługa bota) podaj wymagane informacje:

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

4. W obszarze **Bot template settings** (Ustawienia szablonu bota) wybierz następujące opcje, a następnie wybierz przycisk **Wybierz** pod tymi ustawieniami:

    |Ustawienie|Przeznaczenie|Wybór|
    |--|--|--|
    |Wersja zestawu SDK|Wersja struktury bota|**Zestaw SDK w wersji 4**|
    |Język zestawu SDK|Język programowania bota|**Node.js**|
    |Bot echo/podstawowy|Typ bota|**Bot podstawowy**|
    
5. Wybierz pozycję **Utwórz**. To powoduje utworzenie i wdrożenie usługi bota na platformie Azure. W ramach tego procesu jest tworzona nowa aplikacja usługi LUIS o nazwie `luis-nodejs-bot-XXXX`. Ta nazwa zależy od nazwy bota i aplikacji w poprzedniej sekcji.

    [![Tworzenie bota aplikacji internetowej](./media/bfv4-nodejs/create-web-app-service.png)](./media/bfv4-nodejs/create-web-app-service.png#lightbox)

6. Pozostaw tę kartę przeglądarki otwartą. Aby wykonać inne kroki w portalu LUIS otwórz nową kartę przeglądarki. Po wdrożeniu nowej usługi bota przejdź do następnej sekcji.

## <a name="add-prebuilt-domain-to-model"></a>Dodawanie wstępnie utworzonej domeny do modelu
W ramach wdrożenia usługi bota jest tworzona nowa aplikacja usługi LUIS z intencjami i przykładowymi wypowiedziami. Bot zapewnia mapowanie intencji do nowej aplikacji LUIS dla następujących intencji: 

|Intencje usługi LUIS bota podstawowego|przykładowa wypowiedź|
|--|--|
|Cancel|`stop`|
|Powitanie|`hello`|
|Pomoc|`help`|
|Brak|Cokolwiek spoza domeny aplikacji.|

Dodaj wstępnie utworzoną aplikację HomeAutomation do modelu w celu obsługi wypowiedzi, takich jak: `Turn off the living room lights`

1. Przejdź do portalu [LUIS](https://www.luis.ai) i zaloguj się.
2. Na stronie **My Apps** (Moje aplikacje) wybierz kolumnę **Created date** (Data utworzenia), aby sortować według daty utworzenia aplikacji. Usługa Azure Bot utworzyła nową aplikację w poprzedniej sekcji. Jej nazwa to `luis-nodejs-bot-`  +  `<your-name>` + 4 losowe znaki.
3. Otwórz aplikację i wybierz sekcję **Build** (Kompilacja) w górnym menu nawigacyjnym.
4. Z menu nawigacyjnego po lewej stronie wybierz pozycję **Prebuilt Domains** (Wstępnie utworzone domeny).
5. Aby wybrać domenę **HomeAutomation**, wybierz pozycję **Add domain** (Dodaj domenę) na jej karcie.
6. Wybierz opcję **Train** (Szkol) w prawym górnym menu.
7. Wybierz opcję **Publish** (Opublikuj) w prawym górnym menu. 

    Aplikacja utworzona przez usługę Azure Bot teraz ma nowe intencje:

    |Nowe intencje bota podstawowego|przykładowa wypowiedź|
    |--|--|
    |HomeAutomation.TurnOn|`turn the fan to high`
    |HomeAutomation.TurnOff|`turn off ac please`|

## <a name="download-the-web-app-bot"></a>Pobieranie bota aplikacji internetowej 
Aby tworzyć kod bota aplikacji internetowej, pobierz kod i użyj go na komputerze lokalnym. 

1. W witrynie Azure Portal, pracując nadal na zasobie bot aplikacji internetowej wybierz **Ustawienia aplikacji** i skopiuj wartości **botFilePath** i **botFileSecret**. Później trzeba dodać je do pliku środowiska. 

2. W witrynie Azure Portal wybierz pozycję **Build** (Kompilacja) z sekcji **Bot management** (Zarządzanie botem). 

3. Wybierz przycisk **Download Bot source code** (Pobierz kod źródłowy bota). 

    [![Pobieranie kodu źródłowego bota aplikacji internetowej dla bota podstawowego](../../../includes/media/cognitive-services-luis/bfv4/download-code.png)](../../../includes/media/cognitive-services-luis/bfv4/download-code.png#lightbox)

4. Po spakowaniu kodu źródłowego w komunikacie zostanie podany hiperlink umożliwiający pobranie kodu. Wybierz hiperlink. 

5. Zapisz plik zip na komputerze lokalnym i wyodrębnij pliki. Otwórz projekt. 

6. Otwórz plik bot.js i poszukaj `const results = await this.luisRecognizer.recognize(context);`. Jest to miejsce, gdzie wypowiedź użytkownika wprowadzana do bota jest wysyłania do usługi LUIS.

   ```javascript
    /**
     * Driver code that does one of the following:
     * 1. Display a welcome card upon startup
     * 2. Use LUIS to recognize intents
     * 3. Start a greeting dialog
     * 4. Optionally handle Cancel or Help interruptions
     *
     * @param {Context} context turn context from the adapter
     */
    async onTurn(context) {
        // Create a dialog context
        const dc = await this.dialogs.createContext(context);

        if(context.activity.type === ActivityTypes.Message) {
            // Perform a call to LUIS to retrieve results for the current activity message.
            const results = await this.luisRecognizer.recognize(context);
            
            const topIntent = LuisRecognizer.topIntent(results);

            // handle conversation interrupts first
            const interrupted = await this.isTurnInterrupted(dc, results);
            if(interrupted) {
                return;
            }

            // Continue the current dialog
            const dialogResult = await dc.continue();

            switch(dialogResult.status) {
                case DialogTurnStatus.empty:
                    switch (topIntent) {
                        case GREETING_INTENT:
                            await dc.begin(GREETING_DIALOG);
                            break;

                        case NONE_INTENT:
                        default:
                            // help or no intent identified, either way, let's provide some help
                            // to the user
                            await dc.context.sendActivity(`I didn't understand what you just said to me. topIntent ${topIntent}`);
                            break;
                    }

                case DialogTurnStatus.waiting:
                    // The active dialog is waiting for a response from the user, so do nothing
                break;

                case DialogTurnStatus.complete:
                    await dc.end();
                    break;

                default:
                    await dc.cancelAll();
                    break;

            }

        } else if (context.activity.type === 'conversationUpdate' && context.activity.membersAdded[0].name === 'Bot') {
            // When activity type is "conversationUpdate" and the member joining the conversation is the bot
            // we will send our Welcome Adaptive Card.  This will only be sent once, when the Bot joins conversation
            // To learn more about Adaptive Cards, see https://aka.ms/msbot-adaptivecards for more details.
            const welcomeCard = CardFactory.adaptiveCard(WelcomeCard);
            await context.sendActivity({ attachments: [welcomeCard] });
        }
    }
    ```

    Bot wysyła wypowiedź użytkownika do usługi LUIS i pobiera wyniki. Najważniejsze intencje wyznaczają przepływ konwersacji. 


## <a name="start-the-bot"></a>Uruchamianie bota
Przed zmianą kodu lub ustawień sprawdź, czy bot działa. 

1. W programie Visual Studio Code otwórz okno terminalu. 

2. Zainstaluj zależności npm dla tego bota. 

    ```bash
    npm install
    ```
3. Utwórz plik do przechowywania zmiennych środowiskowych szukanych przez kod bota. Nazwij plik `.env`. Dodaj następujące zmienne środowiskowe:

    <!--there is no code language that represents an .env file correctly-->
    ```env
    botFilePath=
    botFileSecret=
    ```

    Nadaj zmiennym środowiskowym wartości skopiowane z ustawień aplikacji usługi bota platformy Azure w kroku 1 w sekcji **[Pobieranie bota aplikacji internetowej](#download-the-web-app-bot)**.

4. Uruchom bota w trybie śledzenia. Wszelkie zmiany wprowadzone do kodu po tym uruchomieniu będą powodować automatyczne ponowne uruchomienie aplikacji.

    ```bash
    npm run watch
    ```

5. Podczas uruchamiania bota w oknie terminalu zostanie wyświetlony port lokalny, na którym działa bot:

    ```console
    > basic-bot@0.1.0 start C:\Users\pattiowens\repos\BFv4\luis-nodejs-bot-src
    > node ./index.js NODE_ENV=development

    restify listening to http://[::]:3978
    
    Get the Emulator: https://aka.ms/botframework-emulator
    
    To talk to your bot, open the luis-nodejs-bot-pattiowens.bot file in the Emulator
    ```

## <a name="start-the-emulator"></a>Uruchamianie emulatora

1. Uruchom emulator bota. 

2. W emulatorze bota wybierz plik *.bot w korzeniu projektu. Ten plik `.bot` zawiera punkt końcowy adresu URL bota dla komunikatów:

    [![Emulator bota w wersji 4](../../../includes/media/cognitive-services-luis/bfv4/bot-emulator-v4.png)](../../../includes/media/cognitive-services-luis/bfv4/bot-emulator-v4.png#lightbox)

3. Wprowadź wpis tajny skopiowany z ustawień aplikacji usługi bota platformy Azure w kroku 1 sekcji **[Pobieranie bota aplikacji internetowej](#download-the-web-app-bot)**. To pozwoli emulatorowi na dostęp do zaszyfrowanych pól w pliku .bot.

    ![Wpis tajny emulatora bota w wersji 4](../../../includes/media/cognitive-services-luis/bfv4/bot-secret.png)


4. W emulatorze bota wpisz `Hello` i pobierz właściwą odpowiedź dla bota podstawowego.

    [![Odpowiedź bota podstawowego w emulatorze](../../../includes/media/cognitive-services-luis/bfv4/emulator-test.png)](../../../includes/media/cognitive-services-luis/bfv4/emulator-test.png#lightbox)

## <a name="modify-bot-code"></a>Modyfikowanie kodu bota 

W pliku `bot.js` dodaj kod do obsługi nowych intencji. 

1. W górnej części pliku znajdź sekcję **Supported LUIS Intents** i dodaj stałe dla intencji HomeAutomation:

   ```javascript
    // Supported LUIS Intents
    const GREETING_INTENT = 'Greeting';
    const CANCEL_INTENT = 'Cancel';
    const HELP_INTENT = 'Help';
    const NONE_INTENT = 'None';
    const TURNON_INTENT = 'HomeAutomation_TurnOn'; // new intent
    const TURNOFF_INTENT = 'HomeAutomation_TurnOff'; // new intent
    ```

    Zwróć uwagę, że kropka, `.`, między domeną a intencją z aplikacji portalu LUIS został zastąpiona znakiem podkreślenia, `_`. 

2. Znajdź metodę **isTurnInterrupted**, która odbiera prognozowaną wypowiedź LUIS i dodaj wiersz, aby wyświetlić wynik w konsoli.

   ```javascript
    /**
     * Look at the LUIS results and determine if we need to handle
     * an interruptions due to a Help or Cancel intent
     *
     * @param {DialogContext} dc - dialog context
     * @param {LuisResults} luisResults - LUIS recognizer results
     */
    async isTurnInterrupted(dc, luisResults) {
        console.log(JSON.stringify(luisResults));
    ...
    ```

    Bot nie ma dokładnie tej samej odpowiedzi jak żądanie interfejsu API REST usługi LUIS, dlatego ważne jest, aby poznać różnice, patrząc na odpowiedź JSON. Właściwości tekstu i intencji są takie same, ale zostały zmodyfikowane wartości właściwości jednostek. 

    ```json
    {
        "$instance": {
            "HomeAutomation_Device": [
                {
                    "startIndex": 23,
                    "endIndex": 29,
                    "score": 0.9776345,
                    "text": "lights",
                    "type": "HomeAutomation.Device"
                }
            ],
            "HomeAutomation_Room": [
                {
                    "startIndex": 12,
                    "endIndex": 22,
                    "score": 0.9079433,
                    "text": "livingroom",
                    "type": "HomeAutomation.Room"
                }
            ]
        },
        "HomeAutomation_Device": [
            "lights"
        ],
        "HomeAutomation_Room": [
            "livingroom"
        ]
    }
    ```

3. Dodaj intencje do instrukcji switch metody onTurn dla instrukcji case `DialogTurnStatus.empty`:

   ```javascript
    switch (topIntent) {
        case GREETING_INTENT:
            await dc.begin(GREETING_DIALOG);
            break;

        // New HomeAutomation.TurnOn intent
        case TURNON_INTENT: 

            await dc.context.sendActivity(`TurnOn intent found, entities included: ${JSON.stringify(results.entities)}`);
            break;

        // New HomeAutomation.TurnOff intent
        case TURNOFF_INTENT: 

            await dc.context.sendActivity(`TurnOff intent found, entities included: ${JSON.stringify(results.entities)}`);
            break;

        case NONE_INTENT:
        default:
            // help or no intent identified, either way, let's provide some help
            // to the user
            await dc.context.sendActivity(`I didn't understand what you just said to me. topIntent ${topIntent}`);
            break;
    }
    ```

## <a name="view-results-in-bot"></a>Wyświetlanie wyników w bocie

1. W emulatorze bota wpisz wypowiedź: `Turn on the livingroom lights to 50%`

2. Bot odpowiada za pomocą:

    ```json
    TurnOn intent found, entities included: {"$instance":{“HomeAutomation_Device”:[{“startIndex”:23,“endIndex”:29,“score”:0.9776345,“text”:“lights”,“type”:“HomeAutomation.Device”}],“HomeAutomation_Room”:[{“startIndex”:12,“endIndex”:22,“score”:0.9079433,“text”:“livingroom”,“type”:“HomeAutomation.Room”}]},“HomeAutomation_Device”:[“lights”],“HomeAutomation_Room”:[“livingroom”]}
    ```

## <a name="learn-more-about-bot-framework"></a>Dowiedz się więcej na temat struktury Bot Framework
Usługa Azure Bot używa zestawu SDK Bot Framework. Dowiedz się więcej na temat zestawu SDK i struktury Bot Framework:

* Dokumentacja [usługi Azure Bot Service](https://docs.microsoft.com/azure/bot-service/bot-service-overview-introduction?view=azure-bot-service-4.0) w wersji 4
* [Przykłady Bot Builder](https://github.com/Microsoft/botbuilder-samples)
* [Zestaw SDK Bot Builder](https://docs.microsoft.com/javascript/api/botbuilder-core/?view=botbuilder-ts-latest)
* [Narzędzia Bot Builder](https://github.com/Microsoft/botbuilder-tools):

## <a name="next-steps"></a>Kolejne kroki

Utworzono usługę Azure Bot, skopiowano wpis tajny i ścieżkę pliku .bot, pobrano plik zip z kodem. Dodano wstępnie utworzoną domenę HomeAutomation do aplikacji LUIS utworzonej w ramach nowej usługi Azure Bot, a następnie przeprowadzono szkolenie i ponownie opublikowano aplikację. Wyodrębniono projekt kodu, utworzono plik środowiska (`.env`), a także ustawiono wpis tajny bota i ścieżkę pliku .bot. W pliku bot.js dodano kod w celu obsługi dwóch nowych intencji. Następnie przetestowano bota w emulatorze bota, aby zobaczyć odpowiedź usługi LUIS na wypowiedzenie jednej z nowych intencji. 


> [!div class="nextstepaction"]
> [Tworzenie domeny niestandardowej w usłudze LUIS](luis-quickstart-intents-only.md)
