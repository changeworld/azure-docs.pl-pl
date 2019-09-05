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
ms.date: 09/04/2019
ms.author: diberry
ms.openlocfilehash: 79f75fbae9a13db985f46408145c7d6762ff56b9
ms.sourcegitcommit: aebe5a10fa828733bbfb95296d400f4bc579533c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/05/2019
ms.locfileid: "70375582"
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
|Brak|Cokolwiek spoza domeny aplikacji.|

## <a name="test-the-bot-in-web-chat"></a>Testowanie bota w czatów internetowych

1. Mimo że w Azure Portal dla nowego bot, wybierz pozycję **Testuj w rozmowie w sieci Web**. 
1. W polu tekstowym **wpisz wiadomość** wpisz tekst `hello`. Bot reaguje na informacje o strukturze bot, a także przykładowe zapytania dotyczące konkretnego modelu LUIS, takiego jak rezerwacja lotu do paryski. 

    ![Zrzut ekranu przedstawiający Azure Portal, wprowadź tekst "Hello".](./media/bfv4-csharp/ask-bot-question-in-portal-test-in-web-chat.png)

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

1. Otwórz plik **LuisHelper.cs** . Jest to miejsce, gdzie wypowiedź użytkownika wprowadzana do bota jest wysyłania do usługi LUIS. Odpowiedź z LUIS jest zwracana z metody jako obiekt **BookDetails** . Podczas tworzenia własnego bot należy również utworzyć własny obiekt, aby zwrócić szczegóły z LUIS. 


    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.
    
    using System;
    using System.Linq;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Bot.Builder;
    using Microsoft.Bot.Builder.AI.Luis;
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Logging;
    
    namespace Microsoft.BotBuilderSamples
    {
        public static class LuisHelper
        {
            public static async Task<BookingDetails> ExecuteLuisQuery(IConfiguration configuration, ILogger logger, ITurnContext turnContext, CancellationToken cancellationToken)
            {
                var bookingDetails = new BookingDetails();
    
                try
                {
                    // Create the LUIS settings from configuration.
                    var luisApplication = new LuisApplication(
                        configuration["LuisAppId"],
                        configuration["LuisAPIKey"],
                        "https://" + configuration["LuisAPIHostName"]
                    );
    
                    var recognizer = new LuisRecognizer(luisApplication);
    
                    // The actual call to LUIS
                    var recognizerResult = await recognizer.RecognizeAsync(turnContext, cancellationToken);
    
                    var (intent, score) = recognizerResult.GetTopScoringIntent();
                    if (intent == "Book_flight")
                    {
                        // We need to get the result from the LUIS JSON which at every level returns an array.
                        bookingDetails.Destination = recognizerResult.Entities["To"]?.FirstOrDefault()?["Airport"]?.FirstOrDefault()?.FirstOrDefault()?.ToString();
                        bookingDetails.Origin = recognizerResult.Entities["From"]?.FirstOrDefault()?["Airport"]?.FirstOrDefault()?.FirstOrDefault()?.ToString();
    
                        // This value will be a TIMEX. And we are only interested in a Date so grab the first result and drop the Time part.
                        // TIMEX is a format that represents DateTime expressions that include some ambiguity. e.g. missing a Year.
                        bookingDetails.TravelDate = recognizerResult.Entities["datetime"]?.FirstOrDefault()?["timex"]?.FirstOrDefault()?.ToString().Split('T')[0];
                    }
                }
                catch (Exception e)
                {
                    logger.LogWarning($"LUIS Exception: {e.Message} Check your LUIS configuration.");
                }
    
                return bookingDetails;
            }
        }
    }
    ```

1. Otwórz **BookingDetails.cs** , aby zobaczyć, jak obiekt abstrakcyjny informacje o Luis. 

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.
    
    namespace Microsoft.BotBuilderSamples
    {
        public class BookingDetails
        {
            public string Destination { get; set; }
    
            public string Origin { get; set; }
    
            public string TravelDate { get; set; }
        }
    }
    ```

1. Otwarte **okna dialogowe — > BookingDialog.cs** , aby zrozumieć, jak obiekt BookingDetails jest używany do zarządzania przepływem konwersacji. Szczegóły podróży zostały przedstawione w krokach, a następnie całe Księgowanie zostanie potwierdzone i ostatecznie powtórzone z powrotem do użytkownika. 

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.
    
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Bot.Builder;
    using Microsoft.Bot.Builder.Dialogs;
    using Microsoft.Recognizers.Text.DataTypes.TimexExpression;
    
    namespace Microsoft.BotBuilderSamples.Dialogs
    {
        public class BookingDialog : CancelAndHelpDialog
        {
            public BookingDialog()
                : base(nameof(BookingDialog))
            {
                AddDialog(new TextPrompt(nameof(TextPrompt)));
                AddDialog(new ConfirmPrompt(nameof(ConfirmPrompt)));
                AddDialog(new DateResolverDialog());
                AddDialog(new WaterfallDialog(nameof(WaterfallDialog), new WaterfallStep[]
                {
                    DestinationStepAsync,
                    OriginStepAsync,
                    TravelDateStepAsync,
                    ConfirmStepAsync,
                    FinalStepAsync,
                }));
    
                // The initial child Dialog to run.
                InitialDialogId = nameof(WaterfallDialog);
            }
    
            private async Task<DialogTurnResult> DestinationStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
            {
                var bookingDetails = (BookingDetails)stepContext.Options;
    
                if (bookingDetails.Destination == null)
                {
                    return await stepContext.PromptAsync(nameof(TextPrompt), new PromptOptions { Prompt = MessageFactory.Text("Where would you like to travel to?") }, cancellationToken);
                }
                else
                {
                    return await stepContext.NextAsync(bookingDetails.Destination, cancellationToken);
                }
            }
    
            private async Task<DialogTurnResult> OriginStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
            {
                var bookingDetails = (BookingDetails)stepContext.Options;
    
                bookingDetails.Destination = (string)stepContext.Result;
    
                if (bookingDetails.Origin == null)
                {
                    return await stepContext.PromptAsync(nameof(TextPrompt), new PromptOptions { Prompt = MessageFactory.Text("Where are you traveling from?") }, cancellationToken);
                }
                else
                {
                    return await stepContext.NextAsync(bookingDetails.Origin, cancellationToken);
                }
            }
            private async Task<DialogTurnResult> TravelDateStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
            {
                var bookingDetails = (BookingDetails)stepContext.Options;
    
                bookingDetails.Origin = (string)stepContext.Result;
    
                if (bookingDetails.TravelDate == null || IsAmbiguous(bookingDetails.TravelDate))
                {
                    return await stepContext.BeginDialogAsync(nameof(DateResolverDialog), bookingDetails.TravelDate, cancellationToken);
                }
                else
                {
                    return await stepContext.NextAsync(bookingDetails.TravelDate, cancellationToken);
                }
            }
    
            private async Task<DialogTurnResult> ConfirmStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
            {
                var bookingDetails = (BookingDetails)stepContext.Options;
    
                bookingDetails.TravelDate = (string)stepContext.Result;
    
                var msg = $"Please confirm, I have you traveling to: {bookingDetails.Destination} from: {bookingDetails.Origin} on: {bookingDetails.TravelDate}";
    
                return await stepContext.PromptAsync(nameof(ConfirmPrompt), new PromptOptions { Prompt = MessageFactory.Text(msg) }, cancellationToken);
            }
    
            private async Task<DialogTurnResult> FinalStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
            {
                if ((bool)stepContext.Result)
                {
                    var bookingDetails = (BookingDetails)stepContext.Options;
    
                    return await stepContext.EndDialogAsync(bookingDetails, cancellationToken);
                }
                else
                {
                    return await stepContext.EndDialogAsync(null, cancellationToken);
                }
            }
    
            private static bool IsAmbiguous(string timex)
            {
                var timexProperty = new TimexProperty(timex);
                return !timexProperty.Types.Contains(Constants.TimexTypes.Definite);
            }
        }
    }
    ```


## <a name="start-the-bot-code-in-visual-studio"></a>Uruchamianie kodu bot w programie Visual Studio

Uruchom bota w programie Visual Studio. Zostanie otwarte okno przeglądarki z witryną sieci web bota aplikacji internetowej pod adresem `http://localhost:3978/`. Zostanie wyświetlona strona główna z informacjami o Twoim bot.

![Zostanie wyświetlona strona główna z informacjami o Twoim bot.](./media/bfv4-csharp/running-bot-web-home-page-success.png)

## <a name="use-the-bot-emulator-to-test-the-bot"></a>Testowanie bot przy użyciu emulatora bot

1. Rozpocznij emulator bot i wybierz pozycję **Otwórz bot**.
1. W wyskakującym okienku Otwórz okno dialogowe **bot** wprowadź adres URL bot, taki jak `http://localhost:3978/api/messages`. `/api/messages` Trasa jest adresem sieci Web dla bot.
1. Wprowadź **Identyfikator aplikacji firmy Microsoft** i **hasło aplikacji firmy**Microsoft, które znajdują się w pliku **appSettings. JSON** w katalogu głównym pobranego kodu bot.

    Opcjonalnie można utworzyć nową konfigurację bot i skopiować `appId` pliki i `appPassword` z pliku **appSettings. JSON** w projekcie programu Visual Studio dla bot. Nazwa pliku konfiguracji bot powinna być taka sama jak nazwa bot. 

    ```json
    {
        "name": "<bot name>",
        "description": "<bot description>",
        "services": [
            {
                "type": "endpoint",
                "appId": "<appId from appsettings.json>",
                "appPassword": "<appPassword from appsettings.json>",
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

    [![Odpowiedź bota podstawowego w emulatorze](./media/bfv4-csharp/ask-bot-emulator-a-question-and-get-response.png)](./media/bfv4-csharp/ask-bot-emulator-a-question-and-get-response.png#lightbox)


## <a name="ask-bot-a-question-for-the-book-flight-intent"></a>Zadawaj bot pytanie dotyczące zamiaru lotu książki

1. W emulatorze bot należy zaksięgować lot, wprowadzając następujący wypowiedź: 

    ```console
    Book a flight from Paris to Berlin on March 22, 2020
    ```

    Emulator bot prosi o potwierdzenie. 

1. Wybierz pozycję **tak**. Bot reaguje z podsumowaniem jego akcji. 
1. Z dziennika emulatora bot wybierz wiersz, który zawiera `Luis Trace`. Spowoduje to wyświetlenie odpowiedzi JSON z LUIS dla zamiar i jednostek wypowiedź.

    [![Odpowiedź bota podstawowego w emulatorze](./media/bfv4-csharp/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png)](./media/bfv4-csharp/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png#lightbox)

[!INCLUDE [Bot Information](../../../includes/cognitive-services-qnamaker-luis-bot-info.md)]

## <a name="next-steps"></a>Następne kroki

Zobacz więcej [przykładów](https://github.com/microsoft/botframework-solutions) z botami konwersacyjnymi. 

> [!div class="nextstepaction"]
> [Tworzenie aplikacji Language Understanding z niestandardową domeną podmiotu](luis-quickstart-intents-only.md)
