---
title: 'Samouczek: Language Understanding bot C# v4'
titleSuffix: Azure Cognitive Services
description: Przy użyciu języka C# twórz czatbot zintegrowany z usługą Language Understanding (LUIS). Bot jest tworzona przy użyciu platformy bot Framework w wersji 4 i usługi Azure Web App bot.
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 09/06/2019
ms.author: diberry
ms.openlocfilehash: 0911747da38ed736a79e692fd511e5bfbfaf7439
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/06/2019
ms.locfileid: "70772912"
---
# <a name="tutorial-use-a-web-app-bot-enabled-with-language-understanding-in-c"></a>Samouczek: Użyj bot aplikacji sieci Web z włączonym Language Understanding wC#

Użyj C# , aby skompilować bot rozmowy zintegrowane z funkcją interpretacji języka (Luis). Bot jest tworzona przy użyciu [aplikacji sieci Web](https://docs.microsoft.com/azure/bot-service/) platformy Azure bot Resource i [bot Framework w wersji](https://github.com/Microsoft/botbuilder-dotnet) v4.

**Ten samouczek zawiera informacje na temat wykonywania następujących czynności:**

> [!div class="checklist"]
> * Tworzenie bota aplikacji internetowej. Ten proces tworzy nową aplikację usługi LUIS.
> * Pobierz projekt bot utworzony przez usługę sieci Web bot
> * Uruchamianie bota i emulatora lokalnie na komputerze
> * Wyświetlanie wyników wypowiedzi w bocie

## <a name="prerequisites"></a>Wymagania wstępne

* [Emulator bota](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/)


## <a name="create-a-web-app-bot-resource"></a>Tworzenie zasobu bot aplikacji sieci Web

1. W witrynie [Azure Portal](https://portal.azure.com) wybierz polecenie **Utwórz nowy zasób**.

1. W polu wyszukiwania wyszukaj i wybierz pozycję **Web App Bot** (Bot aplikacji internetowej). Wybierz pozycję **Utwórz**.

1. W polu **Bot Service** (Usługa bota) podaj wymagane informacje:

    |Ustawienie|Cel|Zalecane ustawienia|
    |--|--|--|
    |Nazwa bota|Nazwa zasobu|`luis-csharp-bot-` + `<your-name>`, na przykład `luis-csharp-bot-johnsmith`|
    |Subscription|Subskrypcja miejsca utworzenia bota.|Subskrypcja podstawowa.
    |Resource group|Logiczna grupa zasobów platformy Azure|Utwórz nową grupę do przechowywania wszystkich zasobów używanych z tym botem, nazwij grupę `luis-csharp-bot-resource-group`.|
    |Location|Region platformy Azure — nie musi być taki sam jak region tworzenia lub publikowania usługi LUIS.|`westus`|
    |Warstwa cenowa|Służy do określania limitów żądań usługi i rozliczeń.|`F0` to warstwa bezpłatna.
    |Nazwa aplikacji|Nazwa jest używana jako domena podrzędna, gdy bot jest wdrażany w chmurze (na przykład humanresourcesbot.azurewebsites.net).|`luis-csharp-bot-` + `<your-name>`, na przykład `luis-csharp-bot-johnsmith`|
    |Szablon bota|Ustawienia struktury bota — zobacz następną tabelę|
    |Lokalizacja aplikacji usługi LUIS|Musi być taka sama jak region zasobu usługi LUIS|`westus`|
    |Plan/Lokalizacja usługi App Service|Nie zmieniaj podanej wartości domyślnej.|
    |Application Insights|Nie zmieniaj podanej wartości domyślnej.|
    |Identyfikator i hasło aplikacji firmy Microsoft|Nie zmieniaj podanej wartości domyślnej.|

1. W **szablonie bot**wybierz poniższe opcje, a następnie wybierz przycisk **Wybierz** w obszarze te ustawienia:

    |Ustawienie|Cel|Wybór|
    |--|--|--|
    |Wersja zestawu SDK|Wersja struktury bota|**Zestaw SDK w wersji 4**|
    |Język zestawu SDK|Język programowania bota|**C#**|
    |Bot|Typ bota|**Bot podstawowy**|
    
1. Wybierz pozycję **Utwórz**. To powoduje utworzenie i wdrożenie usługi bota na platformie Azure. W ramach tego procesu jest tworzona nowa aplikacja usługi LUIS o nazwie `luis-csharp-bot-XXXX`. Ta nazwa jest oparta na nazwie aplikacji usługi/Azure bot.

    [![Tworzenie bota aplikacji internetowej](./media/bfv4-csharp/create-web-app-service.png)](./media/bfv4-csharp/create-web-app-service.png#lightbox)

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

1. Aby wysłać użytkownika wypowiedź do punktu końcowego przewidywania LUIS, Otwórz plik **FlightBookingRecognizer.cs** . Jest to miejsce, gdzie wypowiedź użytkownika wprowadzana do bota jest wysyłania do usługi LUIS. Odpowiedź z LUIS jest zwracana z metody **RecognizeAsync** .  

    ```csharp
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Bot.Builder;
    using Microsoft.Bot.Builder.AI.Luis;
    using Microsoft.Extensions.Configuration;
    
    namespace Microsoft.BotBuilderSamples
    {
        public class FlightBookingRecognizer : IRecognizer
        {
            private readonly LuisRecognizer _recognizer;
    
            public FlightBookingRecognizer(IConfiguration configuration)
            {
                var luisIsConfigured = !string.IsNullOrEmpty(configuration["LuisAppId"]) && !string.IsNullOrEmpty(configuration["LuisAPIKey"]) && !string.IsNullOrEmpty(configuration["LuisAPIHostName"]);
                if (luisIsConfigured)
                {
                    var luisApplication = new LuisApplication(
                        configuration["LuisAppId"],
                        configuration["LuisAPIKey"],
                        "https://" + configuration["LuisAPIHostName"]);
    
                    _recognizer = new LuisRecognizer(luisApplication);
                }
            }
    
            // Returns true if luis is configured in the appsettings.json and initialized.
            public virtual bool IsConfigured => _recognizer != null;
    
            public virtual async Task<RecognizerResult> RecognizeAsync(ITurnContext turnContext, CancellationToken cancellationToken)
                => await _recognizer.RecognizeAsync(turnContext, cancellationToken);
    
            public virtual async Task<T> RecognizeAsync<T>(ITurnContext turnContext, CancellationToken cancellationToken)
                where T : IRecognizerConvert, new()
                => await _recognizer.RecognizeAsync<T>(turnContext, cancellationToken);
        }
    }
    ````

1. Otwarte **okna dialogowe — > MainDialog.cs** przechwytuje wypowiedź i wysyła je do executeLuisQuery w metodzie actStep. 

    ```csharp
    public class MainDialog : ComponentDialog
    {
        private readonly FlightBookingRecognizer _luisRecognizer;

        ...

        public MainDialog(FlightBookingRecognizer luisRecognizer, BookingDialog bookingDialog, ILogger<MainDialog> logger)
                    : base(nameof(MainDialog))
        {
            _luisRecognizer = luisRecognizer;
            ...
        }

        private async Task<DialogTurnResult> ActStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
        {
            if (!_luisRecognizer.IsConfigured)
            {
                // LUIS is not configured, we just run the BookingDialog path with an empty BookingDetailsInstance.
                return await stepContext.BeginDialogAsync(nameof(BookingDialog), new BookingDetails(), cancellationToken);
            }

            // Call LUIS and gather any potential booking details. (Note the TurnContext has the response to the prompt.)
            var luisResult = await _luisRecognizer.RecognizeAsync<FlightBooking>(stepContext.Context, cancellationToken);
            switch (luisResult.TopIntent().intent)
            {
                case FlightBooking.Intent.BookFlight:
                    await ShowWarningForUnsupportedCities(stepContext.Context, luisResult, cancellationToken);

                    // Initialize BookingDetails with any entities we may have found in the response.
                    var bookingDetails = new BookingDetails()
                    {
                        // Get destination and origin from the composite entities arrays.
                        Destination = luisResult.ToEntities.Airport,
                        Origin = luisResult.FromEntities.Airport,
                        TravelDate = luisResult.TravelDate,
                    };

                    // Run the BookingDialog giving it whatever details we have from the LUIS call, it will fill out the remainder.
                    return await stepContext.BeginDialogAsync(nameof(BookingDialog), bookingDetails, cancellationToken);

                case FlightBooking.Intent.GetWeather:
                    // We haven't implemented the GetWeatherDialog so we just display a TODO message.
                    var getWeatherMessageText = "TODO: get weather flow here";
                    var getWeatherMessage = MessageFactory.Text(getWeatherMessageText, getWeatherMessageText, InputHints.IgnoringInput);
                    await stepContext.Context.SendActivityAsync(getWeatherMessage, cancellationToken);
                    break;

                default:
                    // Catch all for unhandled intents
                    var didntUnderstandMessageText = $"Sorry, I didn't get that. Please try asking in a different way (intent was {luisResult.TopIntent().intent})";
                    var didntUnderstandMessage = MessageFactory.Text(didntUnderstandMessageText, didntUnderstandMessageText, InputHints.IgnoringInput);
                    await stepContext.Context.SendActivityAsync(didntUnderstandMessage, cancellationToken);
                    break;
            }

            return await stepContext.NextAsync(null, cancellationToken);
        }
        
        ...

    }
    ```

## <a name="start-the-bot-code-in-visual-studio"></a>Uruchamianie kodu bot w programie Visual Studio

W programie Visual Studio 2019 Uruchom bot. Zostanie otwarte okno przeglądarki z witryną sieci web bota aplikacji internetowej pod adresem `http://localhost:3978/`. Zostanie wyświetlona strona główna z informacjami o Twoim bot.

![Zostanie wyświetlona strona główna z informacjami o Twoim bot.](./media/bfv4-csharp/running-bot-web-home-page-success.png)

## <a name="use-the-bot-emulator-to-test-the-bot"></a>Testowanie bot przy użyciu emulatora bot

1. Rozpocznij emulator bot i wybierz pozycję **Otwórz bot**.
1. W wyskakującym okienku Otwórz okno dialogowe **bot** wprowadź adres URL bot, taki jak `http://localhost:3978/api/messages`. `/api/messages` Trasa jest adresem sieci Web dla bot.
1. Wprowadź **Identyfikator aplikacji firmy Microsoft** i **hasło aplikacji firmy**Microsoft, które znajdują się w pliku **appSettings. JSON** w katalogu głównym pobranego kodu bot.


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
