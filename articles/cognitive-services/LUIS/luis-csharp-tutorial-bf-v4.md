---
title: 'Samouczek: Rozumienie języka Bot C# v4'
description: Przy użyciu języka C# twórz czatbot zintegrowany z usługą Language Understanding (LUIS). Bot jest zbudowany przy wsparciu platformy Bot Framework w wersji 4 i usługi bota aplikacji sieci Web platformy Azure.
ms.topic: tutorial
ms.date: 02/03/2020
ms.openlocfilehash: 2381f4cba39f81ab593f3149a2708f442156f30d
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76988004"
---
# <a name="tutorial-use-a-web-app-bot-enabled-with-language-understanding-in-c"></a>Samouczek: Korzystanie z bota aplikacji sieci Web włączone z języka rozumienia w języku C #

Użyj języka C# do tworzenia czatu bot zintegrowany ze zrozumieniem języka (LUIS). Bot jest zbudowany z zasobów [bot aplikacji](https://docs.microsoft.com/azure/bot-service/) sieci Web azure i Bot [Framework wersji](https://github.com/Microsoft/botbuilder-dotnet) V4.

**Ten samouczek zawiera informacje na temat wykonywania następujących czynności:**

> [!div class="checklist"]
> * Tworzenie bota aplikacji internetowej. Ten proces tworzy nową aplikację usługi LUIS.
> * Pobierz projekt bota utworzony przez usługę bota sieci Web
> * Uruchamianie bota i emulatora lokalnie na komputerze
> * Wyświetlanie wyników wypowiedzi w bocie

## <a name="prerequisites"></a>Wymagania wstępne

* [Emulator bota](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/)


## <a name="create-a-web-app-bot-resource"></a>Tworzenie zasobu bota aplikacji sieci Web

1. W witrynie [Azure Portal](https://portal.azure.com) wybierz polecenie **Utwórz nowy zasób**.

1. W polu wyszukiwania wyszukaj i wybierz pozycję **Web App Bot** (Bot aplikacji internetowej). Wybierz **pozycję Utwórz**.

1. W polu **Bot Service** (Usługa bota) podaj wymagane informacje:

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
    |Plan usługi aplikacji/lokalizacja|Nie należy zmieniać wartości domyślnej podanej wartości.|
    |Application Insights|Nie należy zmieniać wartości domyślnej podanej wartości.|
    |Identyfikator aplikacji i hasło firmy Microsoft|Nie należy zmieniać wartości domyślnej podanej wartości.|

1. W **szablonie Bot**wybierz następujące opcje, a następnie wybierz przycisk **Wybierz** w tych ustawieniach:

    |Ustawienie|Przeznaczenie|Wybór|
    |--|--|--|
    |Wersja zestawu SDK|Wersja struktury bota|**Zestaw SDK w wersji 4**|
    |Język zestawu SDK|Język programowania bota|**C #**|
    |Bot|Typ bota|**Bot podstawowy**|

1. Wybierz **pozycję Utwórz**. To powoduje utworzenie i wdrożenie usługi bota na platformie Azure. W ramach tego procesu jest tworzona nowa aplikacja usługi LUIS o nazwie `luis-csharp-bot-XXXX`. Ta nazwa jest oparta na nazwie aplikacji /Azure Bot Service.

    > [!div class="mx-imgBorder"]
    > [![Tworzenie bota aplikacji sieci Web](./media/bfv4-csharp/create-web-app-service.png)](./media/bfv4-csharp/create-web-app-service.png#lightbox)

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

1. Gdy w oknie podręcznym pojawi się pytanie **Uwzględnij ustawienia aplikacji w pobranym pliku zip?**, **wybierz**tak .

1. Po spakowaniu kodu źródłowego w komunikacie zostanie podany hiperlink umożliwiający pobranie kodu. Wybierz hiperlink.

1. Zapisz plik zip na komputerze lokalnym i wyodrębnij pliki. Otwórz projekt za pomocą programu Visual Studio.

## <a name="review-code-to-send-utterance-to-luis-and-get-response"></a>Przejrzyj kod, aby wysłać wypowiedź do usługi LUIS i uzyskać odpowiedź

1. Aby wysłać wypowiedź użytkownika do punktu końcowego przewidywania usługi LUIS, otwórz plik **FlightBookingRecognizer.cs.** Jest to miejsce, gdzie wypowiedź użytkownika wprowadzana do bota jest wysyłania do usługi LUIS. Odpowiedź z usługi LUIS jest zwracana z **RecognizeAsync** metody.

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

1. Otwórz **dialogi -> MainDialog.cs** przechwytuje wypowiedź i wysyła go do executeLuisQuery w actStep metody.

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Bot.Builder;
    using Microsoft.Bot.Builder.Dialogs;
    using Microsoft.Bot.Schema;
    using Microsoft.Extensions.Logging;
    using Microsoft.Recognizers.Text.DataTypes.TimexExpression;

    namespace Microsoft.BotBuilderSamples.Dialogs
    {
        public class MainDialog : ComponentDialog
        {
            private readonly FlightBookingRecognizer _luisRecognizer;
            protected readonly ILogger Logger;

            // Dependency injection uses this constructor to instantiate MainDialog
            public MainDialog(FlightBookingRecognizer luisRecognizer, BookingDialog bookingDialog, ILogger<MainDialog> logger)
                : base(nameof(MainDialog))
            {
                _luisRecognizer = luisRecognizer;
                Logger = logger;

                AddDialog(new TextPrompt(nameof(TextPrompt)));
                AddDialog(bookingDialog);
                AddDialog(new WaterfallDialog(nameof(WaterfallDialog), new WaterfallStep[]
                {
                    IntroStepAsync,
                    ActStepAsync,
                    FinalStepAsync,
                }));

                // The initial child Dialog to run.
                InitialDialogId = nameof(WaterfallDialog);
            }

            private async Task<DialogTurnResult> IntroStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
            {
                if (!_luisRecognizer.IsConfigured)
                {
                    await stepContext.Context.SendActivityAsync(
                        MessageFactory.Text("NOTE: LUIS is not configured. To enable all capabilities, add 'LuisAppId', 'LuisAPIKey' and 'LuisAPIHostName' to the appsettings.json file.", inputHint: InputHints.IgnoringInput), cancellationToken);

                    return await stepContext.NextAsync(null, cancellationToken);
                }

                // Use the text provided in FinalStepAsync or the default if it is the first time.
                var messageText = stepContext.Options?.ToString() ?? "What can I help you with today?\nSay something like \"Book a flight from Paris to Berlin on March 22, 2020\"";
                var promptMessage = MessageFactory.Text(messageText, messageText, InputHints.ExpectingInput);
                return await stepContext.PromptAsync(nameof(TextPrompt), new PromptOptions { Prompt = promptMessage }, cancellationToken);
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

            // Shows a warning if the requested From or To cities are recognized as entities but they are not in the Airport entity list.
            // In some cases LUIS will recognize the From and To composite entities as a valid cities but the From and To Airport values
            // will be empty if those entity values can't be mapped to a canonical item in the Airport.
            private static async Task ShowWarningForUnsupportedCities(ITurnContext context, FlightBooking luisResult, CancellationToken cancellationToken)
            {
                var unsupportedCities = new List<string>();

                var fromEntities = luisResult.FromEntities;
                if (!string.IsNullOrEmpty(fromEntities.From) && string.IsNullOrEmpty(fromEntities.Airport))
                {
                    unsupportedCities.Add(fromEntities.From);
                }

                var toEntities = luisResult.ToEntities;
                if (!string.IsNullOrEmpty(toEntities.To) && string.IsNullOrEmpty(toEntities.Airport))
                {
                    unsupportedCities.Add(toEntities.To);
                }

                if (unsupportedCities.Any())
                {
                    var messageText = $"Sorry but the following airports are not supported: {string.Join(',', unsupportedCities)}";
                    var message = MessageFactory.Text(messageText, messageText, InputHints.IgnoringInput);
                    await context.SendActivityAsync(message, cancellationToken);
                }
            }

            private async Task<DialogTurnResult> FinalStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
            {
                // If the child dialog ("BookingDialog") was cancelled, the user failed to confirm or if the intent wasn't BookFlight
                // the Result here will be null.
                if (stepContext.Result is BookingDetails result)
                {
                    // Now we have all the booking details call the booking service.

                    // If the call to the booking service was successful tell the user.

                    var timeProperty = new TimexProperty(result.TravelDate);
                    var travelDateMsg = timeProperty.ToNaturalLanguage(DateTime.Now);
                    var messageText = $"I have you booked to {result.Destination} from {result.Origin} on {travelDateMsg}";
                    var message = MessageFactory.Text(messageText, messageText, InputHints.IgnoringInput);
                    await stepContext.Context.SendActivityAsync(message, cancellationToken);
                }

                // Restart the main dialog with a different message the second time around
                var promptMessage = "What else can I do for you?";
                return await stepContext.ReplaceDialogAsync(InitialDialogId, promptMessage, cancellationToken);
            }
        }
    }
    ```

## <a name="start-the-bot-code-in-visual-studio"></a>Uruchamianie kodu bota w programie Visual Studio

W programie Visual Studio 2019 uruchom bota. Zostanie otwarte okno przeglądarki z witryną sieci web bota aplikacji internetowej pod adresem `http://localhost:3978/`. Zostanie wyświetlona strona główna z informacjami o botie.

![Zostanie wyświetlona strona główna z informacjami o botie.](./media/bfv4-csharp/running-bot-web-home-page-success.png)

## <a name="use-the-bot-emulator-to-test-the-bot"></a>Użyj emulatora bota, aby przetestować bota

1. Rozpocznij emulator botów i wybierz pozycję **Otwórz bota**.
1. W oknie **podręcznym Otwórz bota** wprowadź adres URL `http://localhost:3978/api/messages`bota, na przykład . Trasa `/api/messages` jest adresem internetowym bota.
1. Wprowadź identyfikator **aplikacji Firmy Microsoft** i hasło do aplikacji **Microsoft**App , znajdujące się w pliku **appsettings.json** w katalogu głównym pobranego kodu bota.


1. W emulatorze bota wprowadź `Book a flight from Seattle to Berlin tomorrow` i uzyskaj taką samą odpowiedź dla podstawowego bota, jak w **teście w czacie internetowym** w poprzedniej sekcji.

    [![Podstawowa odpowiedź bota w emulatorze](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png)](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png#lightbox)

1. Wybierz **pozycję Tak**. Bot odpowiada podsumowaniem swoich działań.
1. Z dziennika emulatora bota wybierz wiersz `Luis Trace`zawierający plik . Spowoduje to wyświetlenie odpowiedzi JSON z usługi LUIS dla intencji i jednostek wypowiedź.

    [![Podstawowa odpowiedź bota w emulatorze](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png)](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png#lightbox)


[!INCLUDE [Bot Information](../../../includes/cognitive-services-qnamaker-luis-bot-info.md)]


## <a name="next-steps"></a>Następne kroki

Zobacz więcej [przykładów](https://github.com/microsoft/botframework-solutions) z botami konwersacyjnymi.

> [!div class="nextstepaction"]
> [Tworzenie aplikacji do rozumienia języka przy niestandardowych domenach tematów](luis-quickstart-intents-only.md)
