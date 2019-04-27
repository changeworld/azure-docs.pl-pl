---
title: Bot — C# — v4
titleSuffix: Language Understanding - Azure Cognitive Services
description: Przy użyciu języka C# twórz czatbot zintegrowany z usługą Language Understanding (LUIS). Ten czatbot używa aplikacji Human Resources, aby szybko wdrożyć rozwiązanie bota. Bot jest tworzony za pomocą platformy Bot Framework w wersji 4 i bota aplikacji internetowej platformy Azure.
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 01/09/2019
ms.author: diberry
ms.openlocfilehash: 028c06924e41606ba1d4e0b15fe26f2b7270db3c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60710125"
---
# <a name="tutorial-luis-bot-in-c-with-the-bot-framework-4x-and-the-azure-web-app-bot"></a>Samouczek: bot usługi LUIS w języku C# przy użyciu platformy Bot Framework 4.x i bota aplikacji internetowej platformy Azure
Przy użyciu języka C# można utworzyć czatbot zintegrowany z usługą Language Understanding (LUIS). Ten bot używa aplikacji HomeAutomation do implementacji rozwiązania bota. Bot jest kompilacją aplikacji [bot aplikacji internetowej](https://docs.microsoft.com/azure/bot-service/) na platformie Azure z [Bot Framework w wersji](https://github.com/Microsoft/botbuilder-js) 4.

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
* [Program Visual Studio](https://visualstudio.microsoft.com/downloads/)


## <a name="create-web-app-bot"></a>Tworzenie bota aplikacji internetowej

1. W witrynie [Azure Portal](https://portal.azure.com) wybierz polecenie **Utwórz nowy zasób**.

2. W polu wyszukiwania wyszukaj i wybierz pozycję **Web App Bot** (Bot aplikacji internetowej). Wybierz pozycję **Utwórz**.

3. W polu **Bot Service** (Usługa bota) podaj wymagane informacje:

    |Ustawienie|Przeznaczenie|Zalecane ustawienia|
    |--|--|--|
    |Nazwa bota|Nazwa zasobu|`luis-csharp-bot-` + `<your-name>`, na przykład `luis-csharp-bot-johnsmith`|
    |Subskrypcja|Subskrypcja miejsca utworzenia bota.|Subskrypcja podstawowa.
    |Grupa zasobów|Logiczna grupa zasobów platformy Azure|Utwórz nową grupę do przechowywania wszystkich zasobów używanych z tym botem, nazwij grupę `luis-csharp-bot-resource-group`.|
    |Lokalizacja|Region platformy Azure — nie musi być taki sam jak region tworzenia lub publikowania usługi LUIS.|`westus`|
    |Warstwa cenowa|Służy do określania limitów żądań usługi i rozliczeń.|`F0` to warstwa bezpłatna.
    |Nazwa aplikacji|Nazwa jest używana jako domena podrzędna, gdy bot jest wdrażany w chmurze (na przykład humanresourcesbot.azurewebsites.net).|`luis-csharp-bot-` + `<your-name>`, na przykład `luis-csharp-bot-johnsmith`|
    |Szablon bota|Ustawienia struktury bota — zobacz następną tabelę|
    |Lokalizacja aplikacji usługi LUIS|Musi być taka sama jak region zasobu usługi LUIS|`westus`|

4. W obszarze **Bot template settings** (Ustawienia szablonu bota) wybierz następujące opcje, a następnie wybierz przycisk **Wybierz** pod tymi ustawieniami:

    |Ustawienie|Przeznaczenie|Wybór|
    |--|--|--|
    |Wersja zestawu SDK|Wersja struktury bota|**Zestaw SDK w wersji 4**|
    |Język zestawu SDK|Język programowania bota|**C#**|
    |Bot echo/podstawowy|Typ bota|**Bot podstawowy**|
    
5. Wybierz pozycję **Utwórz**. To powoduje utworzenie i wdrożenie usługi bota na platformie Azure. W ramach tego procesu jest tworzona nowa aplikacja usługi LUIS o nazwie `luis-csharp-bot-XXXX`. Ta nazwa zależy od nazwy bota i aplikacji w poprzedniej sekcji.

    [![Tworzenie bota aplikacji internetowej](./media/bfv4-csharp/create-web-app-service.png)](./media/bfv4-csharp/create-web-app-service.png#lightbox)

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
2. Na stronie **My Apps** (Moje aplikacje) wybierz kolumnę **Created date** (Data utworzenia), aby sortować według daty utworzenia aplikacji. Usługa Azure Bot utworzyła nową aplikację w poprzedniej sekcji. Jej nazwa to `luis-csharp-bot-`  +  `<your-name>` + 4 losowe znaki.
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

6. Otwórz plik bot.cs i poszukaj `_services.LuisServices`. Jest to miejsce, gdzie wypowiedź użytkownika wprowadzana do bota jest wysyłania do usługi LUIS.

    ```csharp
    /// <summary>
    /// Run every turn of the conversation. Handles orchestration of messages.
    /// </summary>
    /// <param name="turnContext">Bot Turn Context.</param>
    /// <param name="cancellationToken">Task CancellationToken.</param>
    /// <returns>A <see cref="Task"/> representing the asynchronous operation.</returns>
    public async Task OnTurnAsync(ITurnContext turnContext, CancellationToken cancellationToken)
    {
        var activity = turnContext.Activity;

        if (activity.Type == ActivityTypes.Message)
        {
            // Perform a call to LUIS to retrieve results for the current activity message.
            var luisResults = await _services.LuisServices[LuisConfiguration].RecognizeAsync(turnContext, cancellationToken).ConfigureAwait(false);

            // If any entities were updated, treat as interruption.
            // For example, "no my name is tony" will manifest as an update of the name to be "tony".
            var topScoringIntent = luisResults?.GetTopScoringIntent();

            var topIntent = topScoringIntent.Value.intent;
            switch (topIntent)
            {
                case GreetingIntent:
                    await turnContext.SendActivityAsync("Hello.");
                    break;
                case HelpIntent:
                    await turnContext.SendActivityAsync("Let me try to provide some help.");
                    await turnContext.SendActivityAsync("I understand greetings, being asked for help, or being asked to cancel what I am doing.");
                    break;
                case CancelIntent:
                    await turnContext.SendActivityAsync("I have nothing to cancel.");
                    break;
                case NoneIntent:
                default:
                    // Help or no intent identified, either way, let's provide some help.
                    // to the user
                    await turnContext.SendActivityAsync("I didn't understand what you just said to me.");
                    break;
            }
        }
        else if (activity.Type == ActivityTypes.ConversationUpdate)
        {
            if (activity.MembersAdded.Any())
            {
                // Iterate over all new members added to the conversation.
                foreach (var member in activity.MembersAdded)
                {
                    // Greet anyone that was not the target (recipient) of this message.
                    // To learn more about Adaptive Cards, see https://aka.ms/msbot-adaptivecards for more details.
                    if (member.Id != activity.Recipient.Id)
                    {
                        var welcomeCard = CreateAdaptiveCardAttachment();
                        var response = CreateResponse(activity, welcomeCard);
                        await turnContext.SendActivityAsync(response).ConfigureAwait(false);
                    }
                }
            }
        }

    }
    ```

    Bot wysyła wypowiedź użytkownika do usługi LUIS i pobiera wyniki. Najważniejsze intencje wyznaczają przepływ konwersacji. 


## <a name="start-the-bot"></a>Uruchamianie bota
Przed zmianą kodu lub ustawień, sprawdź, czy bot działa. 

1. Otwórz plik rozwiązania w programie Visual Studio. 

2. Utwórz plik `appsettings.json` do przechowywania zmiennych bota, których szuka kod bota:

    ```JSON
    {
    "botFileSecret": "",
    "botFilePath": ""

    }
    ```

    Nadaj zmiennym wartości skopiowane z ustawień aplikacji usługi bota platformy Azure w kroku 1 w sekcji **[Pobieranie bota aplikacji internetowej](#download-the-web-app-bot)**.

3. Uruchom bota w programie Visual Studio. Zostanie otwarte okno przeglądarki z witryną sieci web bota aplikacji internetowej pod adresem `http://localhost:3978/`.

## <a name="start-the-emulator"></a>Uruchamianie emulatora

1. Uruchom emulator bota.

2. W emulatorze bota wybierz plik *.bot w korzeniu projektu. Ten plik `.bot` zawiera punkt końcowy adresu URL bota dla komunikatów:

    [![Emulator bota w wersji 4](../../../includes/media/cognitive-services-luis/bfv4/bot-emulator-v4.png)](../../../includes/media/cognitive-services-luis/bfv4/bot-emulator-v4.png#lightbox)

3. Wprowadź wpis tajny skopiowany z ustawień aplikacji usługi bota platformy Azure w kroku 1 sekcji **[Pobieranie bota aplikacji internetowej](#download-the-web-app-bot)**. To pozwoli emulatorowi na dostęp do zaszyfrowanych pól w pliku `.bot`.

    ![Wpis tajny emulatora bota w wersji 4](../../../includes/media/cognitive-services-luis/bfv4/bot-secret.png)

4. W emulatorze bota wpisz `Hello` i pobierz właściwą odpowiedź dla bota podstawowego.

    [![Odpowiedź bota podstawowego w emulatorze](../../../includes/media/cognitive-services-luis/bfv4/emulator-test.png)](../../../includes/media/cognitive-services-luis/bfv4/emulator-test.png#lightbox)

## <a name="modify-bot-code"></a>Modyfikowanie kodu bota 

W pliku `BasicBot.cs` dodaj kod do obsługi nowych intencji. 

1. W górnej części pliku znajdź sekcję **Supported LUIS Intents** i dodaj stałe dla intencji HomeAutomation:

    ```csharp
    // Supported LUIS Intents
    public const string GreetingIntent = "Greeting";
    public const string CancelIntent = "Cancel";
    public const string HelpIntent = "Help";
    public const string NoneIntent = "None";
    public const string TurnOnIntent = "HomeAutomation_TurnOn"; // new intent
    public const string TurnOffIntent = "HomeAutomation_TurnOff"; // new intent
    ```

    Zwróć uwagę, że kropka, `.`, między domeną a intencją z aplikacji portalu LUIS została zastąpiona znakiem podkreślenia, `_`. 

2. Znajdź metodę **OnTurnAsync**, która odbiera prognozowanie wypowiedzi przez usługę LUIS. Dodaj kod w instrukcji switch, aby zwracać odpowiedź usługi LUIS dla dwóch intencji HomeAutomation. 

    ```csharp
    case TurnOnIntent:
        await turnContext.SendActivityAsync("TurnOn intent found, JSON response: " + luisResults?.Entities.ToString());
        break;
    case TurnOffIntent:
        await turnContext.SendActivityAsync("TurnOff intent found, JSON response: " + luisResults?.Entities.ToString());
        break;
    ```

    Bot nie ma dokładnie tej samej odpowiedzi jak żądanie interfejsu API REST usługi LUIS, dlatego ważne jest, aby poznać różnice, patrząc na odpowiedź JSON. Właściwości tekstu i intencji są takie same, ale zostały zmodyfikowane wartości właściwości jednostki. 

    ```JSON
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



## <a name="view-results-in-bot"></a>Wyświetlanie wyników w bocie

1. W emulatorze bota wpisz wypowiedź: `Turn on the livingroom lights to 50%`

2. Bot odpowiada za pomocą:

    ```JSON
    TurnOn intent found, JSON response: {"$instance":{“HomeAutomation_Device”:[{“startIndex”:23,“endIndex”:29,“score”:0.9776345,“text”:“lights”,“type”:“HomeAutomation.Device”}],“HomeAutomation_Room”:[{“startIndex”:12,“endIndex”:22,“score”:0.9079433,“text”:“livingroom”,“type”:“HomeAutomation.Room”}]},“HomeAutomation_Device”:[“lights”],“HomeAutomation_Room”:[“livingroom”]}
    ```    

## <a name="learn-more-about-bot-framework"></a>Dowiedz się więcej na temat struktury Bot Framework
Usługa Azure Bot używa zestawu SDK Bot Framework. Dowiedz się więcej na temat zestawu SDK i struktury Bot Framework:

* Dokumentacja [usługi Azure Bot Service](https://docs.microsoft.com/azure/bot-service/bot-service-overview-introduction?view=azure-bot-service-4.0) w wersji 4
* [Przykłady Bot Builder](https://github.com/Microsoft/botbuilder-samples)
* [Zestaw SDK Bot Builder](https://docs.microsoft.com/javascript/api/botbuilder-core/?view=botbuilder-ts-latest)
* [Narzędzia Bot Builder](https://github.com/Microsoft/botbuilder-tools):

## <a name="next-steps"></a>Kolejne kroki

Utworzono usługę Azure Bot, skopiowano wpis tajny i ścieżkę pliku `.bot`, pobrano plik zip z kodem. Dodano wstępnie utworzoną domenę HomeAutomation do aplikacji LUIS utworzonej w ramach nowej usługi Azure Bot, a następnie przeprowadzono szkolenie i ponownie opublikowano aplikację. Wyodrębniono projekt kodu, utworzono plik środowiska (`.env`), a także ustawiono wpis tajny bota i ścieżkę pliku `.bot`. W pliku bot.js dodano kod w celu obsługi dwóch nowych intencji. Następnie przetestowano bota w emulatorze bota, aby zobaczyć odpowiedź usługi LUIS na wypowiedzenie jednej z nowych intencji. 

Zobacz więcej [przykładów](https://github.com/Microsoft/AI) z botami konwersacyjnymi. 

> [!div class="nextstepaction"]
> [Tworzenie domeny niestandardowej w usłudze LUIS](luis-quickstart-intents-only.md)
