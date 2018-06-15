---
title: Integracja LUIS z robotów dla środowiska Node.js na platformie Azure przy użyciu zestawu SDK konstruktora Bot | Dokumentacja firmy Microsoft
description: Tworzenie robotów zintegrowany z aplikacją LUIS przy użyciu platformy Bot.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/06/2018
ms.author: v-geberr
ms.openlocfilehash: 12cc84942c139d3c5e981aec902557201c9c8092
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "35349797"
---
# <a name="integrate-luis-with-a-bot-using-the-bot-builder-sdk-for-nodejs"></a>Integracja LUIS z robotów przy użyciu zestawu SDK konstruktora Bot dla środowiska Node.js

Ten samouczek przedstawia tworzenie robotów z [Bot Framework] [ BotFramework] może być zintegrowana z aplikacją LUIS.

## <a name="prerequisite"></a>Wymagania wstępne

Przed utworzeniem bot, postępuj zgodnie z instrukcjami [Utwórz aplikację](./luis-get-started-create-app.md) do tworzenia aplikacji LUIS, który go używa.

Bot odpowiada lokalizacji docelowych z domeny HomeAutomation, które znajdują się w aplikacji LUIS. Dla każdego z tych opcji aplikacji LUIS zapewnia celem mapujący do niego. Bot zawiera okno dialogowe, które obsługuje opcje LUIS wykrywa.

| Celem | Przykład utterance | Funkcje BOT |
|:----:|:----------:|---|
| HomeAutomation.TurnOn | Włącz kontrolki. | Wywołuje bot `TurnOnDialog` podczas `HomeAutomation.TurnOn` została wykryta. To okno dialogowe jest, gdzie powodowałoby wywołanie usługi IoT, aby włączyć na urządzeniu i monituj użytkownika, że urządzenie zostało włączone. |
| HomeAutomation.TurnOff | Wyłącz światła sypialnię. | Wywołuje bot `TurnOffDialog` podczas `HomeAutomation.TurnOff` została wykryta. To okno dialogowe, gdzie powodowałoby wywołanie usługi IoT, aby wyłączyć urządzenie i poinformuj użytkownika, że urządzenie zostało wyłączone. |


## <a name="create-a-language-understanding-bot-with-bot-service"></a>Utwórz bot opis języka z usługą Bot

1. W [portalu Azure](https://portal.azure.com), wybierz pozycję **utworzyć nowy zasób** w bloku menu i wybierz **zobaczyć wszystkie**.

    ![Tworzenie nowego zasobu](./media/luis-tutorial-node-bot/bot-service-creation.png)

2. W polu wyszukiwania, wyszukaj **Bot aplikacji sieci Web**. 

    ![Tworzenie nowego zasobu](./media/luis-tutorial-node-bot/bot-service-selection.png)

3. W **usługi Bot** bloku, podaj wymagane informacje, a następnie wybierz **Utwórz**. To tworzy i wdraża bot usług i aplikacji LUIS na platformie Azure. Jeśli chcesz użyć [plucia mowy](https://docs.microsoft.com/bot-framework/bot-service-manage-speech-priming), przejrzyj [wymagania region](luis-resources-faq.md#what-luis-regions-support-bot-framework-speech-priming) przed utworzeniem sieci bot. 
    * Ustaw **Nazwa aplikacji** Twojego bot nazwy. Nazwa jest używana jako poddomeny po wdrożeniu programu bot do chmury (na przykład mynotesbot.azurewebsites.net). <!-- This name is also used as the name of the LUIS app associated with your bot. Copy it to use later, to find the LUIS app associated with the bot. -->
    * Wybierz subskrypcję, [grupy zasobów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview), plan usługi App service, i [lokalizacji](https://azure.microsoft.com/regions/).
    * Wybierz **opis języka (Node.js)** szablon **szablonu Bot** pola.
    * Wybierz **lokalizacji aplikacji LUIS**. Jest to tworzeniem [region] [ LUIS] aplikacji jest tworzony w.
    * Zaznacz pole wyboru potwierdzenia powiadomień prawnych. Warunki prawne komunikatu znajdują się poniżej pola wyboru.

    ![Bot usługi bloku](./media/luis-tutorial-node-bot/bot-service-setting-callout-template.png)


4. Upewnij się, że wdrożono usługę bot.
    * Wybierz powiadomienia o (ikonę dzwonka, która jest wzdłuż górnej krawędzi portalu Azure). Powiadomienie zostanie zmieniony z **rozpoczęto wdrażanie** do **wdrożenie zakończyło się pomyślnie**.
    * Po zmianie powiadomienia do **wdrożenie zakończyło się pomyślnie**, wybierz pozycję **przejdź do zasobu** na powiadomienie.

## <a name="try-the-default-bot"></a>Spróbuj bot domyślne

Upewnij się, że wdrożono bot sprawdzając **powiadomienia**. Powiadomienia ulegnie zmianie z **wdrożenie w toku...**  do **wdrożenie zakończyło się pomyślnie**. Wybierz **przejdź do zasobu** przycisk, aby otworzyć blok zasobów bot.

<!-- this step isn't supposed to be necessary -->
## <a name="install-npm-resources"></a>Instalowanie programu NPM zasobów
Instalowanie pakietów NPM następujące czynności:

1. Wybierz **kompilacji** z **zarządzania Bot** sekcji Bot aplikacji sieci Web. 

2. A new, drugie okno przeglądarki. Wybierz **Edytor Otwórz kodu online**.

3. W górnym pasku nawigacyjnym, wybierz nazwę bot aplikacji sieci web `homeautomationluisbot`. 

4. Na liście rozwijanej **Otwórz konsolę Kudu**.

5. Otwiera nowe okno przeglądarki. W konsoli wprowadź następujące polecenie:

    ```
    cd site\wwwroot && npm install
    ```

    Poczekaj na zakończenie procesu instalacji. Wróć do pierwszego okna przeglądarki. 

## <a name="test-in-web-chat"></a>Testowanie w rozmów w sieci Web
Po zarejestrowaniu bot wybierz **testów w sieci Web rozmowę** aby otworzyć okienko rozmowę sieci Web. Wpisz tekst "hello" w sieci Web rozmawiać.

  ![Testowanie bot w sieci Web rozmowę](./media/luis-tutorial-node-bot/bot-service-web-chat.png)

Bot odpowiada mówiąc "osiągnięto pozdrowienie. Możesz powiedzieć: hello ". To potwierdzenie, że bot odebrał wiadomość i przekazanego aplikacji LUIS, utworzony domyślny. To ustawienie domyślne LUIS aplikacji wykryto zamiar pozdrowienie. W następnym kroku połączę się z bot aplikacji LUIS utworzonych wcześniej zamiast domyślnej LUIS aplikacji.

## <a name="connect-your-luis-app-to-the-bot"></a>Łączenie aplikacji z LUIS bot

Otwórz **ustawienia aplikacji** w pierwszym oknie przeglądarki i edytowanie **LuisAppId** pole ma zawierać identyfikator aplikacji LUIS aplikacji.

  ![Aktualizuj LUIS identyfikator aplikacji na platformie Azure](./media/luis-tutorial-node-bot/bot-service-app-id.png)

Jeśli nie masz LUIS identyfikator aplikacji, zaloguj się do [LUIS](luis-reference-regions.md) witryny sieci Web przy użyciu tego samego konta, które są używane do logowania się do platformy Azure. Wybierz na **Moje aplikacje**. 

1. Znajdź LUIS utworzonego wcześniej, zawierający intencje i jednostek z domeny HomeAutomation.

2. W **ustawienia** stronie LUIS aplikacji, należy znaleźć i skopiować identyfikator aplikacji.

3. Jeśli nie zostało to jeszcze uczony aplikacji, wybierz **uczenia** przycisk w prawym górnym rogu do uczenia aplikacji.

4. Jeśli jeszcze nie opublikowane aplikacji, wybierz **publikowania** w górnym pasku nawigacyjnym, aby otworzyć **publikowania** strony. Wybierz miejsca produkcyjnego i **publikowania** przycisku.

## <a name="modify-the-bot-code"></a>Zmodyfikuj kod bot

Przejdź do okna przeglądarki w drugim przypadku nadal otworzyć lub w pierwszym oknie przeglądarki, wybierz **kompilacji** , a następnie wybierz **Edytor Otwórz kodu online**.

   ![Otwórz online Edytor kodu](./media/luis-tutorial-node-bot/bot-service-build.png)

Otwórz w edytorze kodu `app.js`. Ten przewodnik zawiera następujący kod:

```javascript
/*-----------------------------------------------------------------------------
A simple Language Understanding (LUIS) bot for the Microsoft Bot Framework. 
-----------------------------------------------------------------------------*/

var restify = require('restify');
var builder = require('botbuilder');
var botbuilder_azure = require("botbuilder-azure");

// Setup Restify Server
var server = restify.createServer();
server.listen(process.env.port || process.env.PORT || 3978, function () {
   console.log('%s listening to %s', server.name, server.url); 
});
  
// Create chat connector for communicating with the Bot Framework Service
var connector = new builder.ChatConnector({
    appId: process.env.MicrosoftAppId,
    appPassword: process.env.MicrosoftAppPassword,
    openIdMetadata: process.env.BotOpenIdMetadata 
});

// Listen for messages from users 
server.post('/api/messages', connector.listen());

/*----------------------------------------------------------------------------------------
* Bot Storage: This is a great spot to register the private state storage for your bot. 
* We provide adapters for Azure Table, CosmosDb, SQL Azure, or you can implement your own!
* For samples and documentation, see: https://github.com/Microsoft/BotBuilder-Azure
* ---------------------------------------------------------------------------------------- */

var tableName = 'botdata';
var azureTableClient = new botbuilder_azure.AzureTableClient(tableName, process.env['AzureWebJobsStorage']);
var tableStorage = new botbuilder_azure.AzureBotStorage({ gzipData: false }, azureTableClient);

// Create your bot with a function to receive messages from the user
// This default message handler is invoked if the user's utterance doesn't
// match any intents handled by other dialogs.
var bot = new builder.UniversalBot(connector, function (session, args) {
    session.send('You reached the default message handler. You said \'%s\'.', session.message.text);
});

bot.set('storage', tableStorage);

// Make sure you add code to validate these fields
var luisAppId = process.env.LuisAppId;
var luisAPIKey = process.env.LuisAPIKey;
var luisAPIHostName = process.env.LuisAPIHostName || 'westus.api.cognitive.microsoft.com';

const LuisModelUrl = 'https://' + luisAPIHostName + '/luis/v2.0/apps/' + luisAppId + '?subscription-key=' + luisAPIKey;

// Create a recognizer that gets intents from LUIS, and add it to the bot
var recognizer = new builder.LuisRecognizer(LuisModelUrl);
bot.recognizer(recognizer);

// Add a dialog for each intent that the LUIS app recognizes.
// See https://docs.microsoft.com/bot-framework/nodejs/bot-builder-nodejs-recognize-intent-luis 
bot.dialog('GreetingDialog',
    (session) => {
        session.send('You reached the Greeting intent. You said \'%s\'.', session.message.text);
        session.endDialog();
    }
).triggerAction({
    matches: 'Greeting'
})

bot.dialog('HelpDialog',
    (session) => {
        session.send('You reached the Help intent. You said \'%s\'.', session.message.text);
        session.endDialog();
    }
).triggerAction({
    matches: 'Help'
})

bot.dialog('CancelDialog',
    (session) => {
        session.send('You reached the Cancel intent. You said \'%s\'.', session.message.text);
        session.endDialog();
    }
).triggerAction({
    matches: 'Cancel'
})
```

Istniejące opcje w app.js są ignorowane. Możesz pozostawić je. 

## <a name="add-a-dialog-that-matches-homeautomationturnon"></a>Dodaj zgodny HomeAutomation.TurnOn okna dialogowego

Skopiuj poniższy kod i dodaj go do `app.js`.

```javascript
bot.dialog('TurnOn',
    (session) => {
        session.send('You reached the TurnOn intent. You said \'%s\'.', session.message.text);
        session.endDialog();
    }
).triggerAction({
    matches: 'HomeAutomation.TurnOn'
})
```

[Odpowiada] [ matches] opcja [triggerAction] [ triggerAction] dołączony do okna dialogowego Określa nazwę celem. Aparat rozpoznawania uruchamiane przy każdym bot odbiera utterance od użytkownika. Jeśli zgodny najwyższy zamiar oceniania wykrytego `triggerAction` powiązany z okna dialogowego, bot wywołuje tego okna dialogowego.

## <a name="add-a-dialog-that-matches-homeautomationturnoff"></a>Dodaj zgodny HomeAutomation.TurnOff okna dialogowego

Skopiuj poniższy kod i dodaj go do `app.js`.

```javascript
bot.dialog('TurnOff',
    (session) => {
        session.send('You reached the TurnOff intent. You said \'%s\'.', session.message.text);
        session.endDialog();
    }
).triggerAction({
    matches: 'HomeAutomation.TurnOff'
})
```
## <a name="test-the-bot"></a>Testowanie bot

W portalu Azure wybierz na **testów w sieci Web rozmowę** do testowania bot. Spróbuj komunikaty typu like "Włącz kontrolki" i "Wyłącz Moje grzejnik" można wywołać lokalizacji docelowych, które dodano do niego.
   ![Testowanie HomeAutomation bot w sieci Web rozmowę](./media/luis-tutorial-node-bot/bot-service-chat-results.png)

> [!TIP]
> Odnalezienie Twojego bot zawsze nie rozpoznaje poprawne zamiar lub jednostek należy poprawić wydajność aplikacji LUIS przez nadanie mu więcej zniesławiających przykład w celu przeszkolenia go. Można ponownie ucz aplikacji LUIS bez żadnych modyfikacji kodu z bot. Zobacz [dodać zniesławiających przykład](https://docs.microsoft.com/azure/cognitive-services/LUIS/add-example-utterances) i [nauczenia i przetestowania aplikacji LUIS](https://docs.microsoft.com/azure/cognitive-services/LUIS/train-test).

## <a name="learn-more-about-bot-framework"></a>Dowiedz się więcej o Bot Framework
Dowiedz się więcej o [Bot Framework](https://dev.botframework.com/) i [3.x](https://github.com/Microsoft/BotBuilder) i [4.x](https://github.com/Microsoft/botbuilder-js) zestawów SDK.

## <a name="next-steps"></a>Kolejne kroki

<!-- From trying the bot, you can see that the recognizer can trigger interruption of the currently active dialog. Allowing and handling interruptions is a flexible design that accounts for what users really do. Learn more about the various actions you can associate with a recognized intent.-->
Możesz dodać inne opcje, takie jak uzyskać pomoc, Anuluj i powitanie, do aplikacji LUIS. Następnie dodaj okien dialogowych do nowej lokalizacji docelowych i je za pomocą bot testu. 

<!-- 
> [!NOTE] 
> The default LUIS app that the template created contains example utterances for Cancel, Greeting, and Help intents. In the list of apps, find the app that begins with the name specified in **App name** in the **Bot Service** blade when you created the Bot Service. -->

> [!div class="nextstepaction"]
> [Dodaj intencje](./luis-how-to-add-intents.md)
> [plucia mowy](https://docs.microsoft.com/bot-framework/bot-service-manage-speech-priming)


[intentDialog]: https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.intentdialog.html

[intentDialog_matches]: https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.intentdialog.html#matches 

[NotesSample]: https://github.com/Microsoft/BotFramework-Samples/tree/master/docs-samples/Node/basics-naturalLanguage

[triggerAction]: https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.dialog.html#triggeraction

[confirmPrompt]: https://docs.botframework.com/node/builder/chat-reference/interfaces/_botbuilder_d_.itriggeractionoptions.html#confirmprompt

[waterfall]: bot-builder-nodejs-dialog-manage-conversation-flow.md#manage-conversation-flow-with-a-waterfall

[session_userData]: https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.session.html#userdata

[EntityRecognizer_findEntity]: https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.entityrecognizer.html#findentity

[matches]: https://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.itriggeractionoptions.html#matches

[LUISAzureDocs]: https://docs.microsoft.com/azure/cognitive-services/LUIS/Home

[Dialog]: https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.dialog.html

[IntentRecognizerSetOptions]: https://docs.botframework.com/node/builder/chat-reference/interfaces/_botbuilder_d_.iintentrecognizersetoptions.html

[LuisRecognizer]: https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.luisrecognizer

[LUISConcepts]: https://docs.botframework.com/node/builder/guides/understanding-natural-language/

[DisambiguationSample]: https://github.com/Microsoft/BotBuilder/tree/master/Node/examples/feature-onDisambiguateRoute

[IDisambiguateRouteHandler]: https://docs.botframework.com/node/builder/chat-reference/interfaces/_botbuilder_d_.idisambiguateroutehandler.html

[RegExpRecognizer]: https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.regexprecognizer.html

[AlarmBot]: https://github.com/Microsoft/BotBuilder/blob/master/Node/examples/basics-naturalLanguage/app.js

[LUISBotSample]: https://github.com/Microsoft/BotBuilder-Samples/tree/master/Node/intelligence-LUIS

[UniversalBot]: https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.universalbot.html


<!-- Old Links -->
[Github-BotFramework-Emulator-Download]: https://aka.ms/bot-framework-emulator
[Github-LUIS-Samples]: https://github.com/Microsoft/LUIS-Samples
[Github-LUIS-Samples-node-hotel-bot]:https://github.com/Microsoft/LUIS-Samples/tree/master/bot-integration-samples/hotel-finder/nodejs
[NodeJs]: https://nodejs.org/
[BFPortal]: https://dev.botframework.com/
[RegisterInstructions]: https://docs.microsoft.com/bot-framework/portal-register-bot
[BotFramework]: https://docs.microsoft.com/bot-framework/
[LUIS]:luis-reference-regions.md

