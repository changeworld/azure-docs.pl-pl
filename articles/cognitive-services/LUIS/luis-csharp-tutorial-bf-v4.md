---
title: Language Understanding Bot C# v4
titleSuffix: Language Understanding - Azure Cognitive Services
description: Przy użyciu języka C# twórz czatbot zintegrowany z usługą Language Understanding (LUIS). Bot jest tworzone za pomocą platformy Bot Framework w wersji 4 i usługi bot aplikacji sieci Web platformy Azure.
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 06/17/2019
ms.author: diberry
ms.openlocfilehash: f74becc24e5d04cefdd05066b8431946578cc35e
ms.sourcegitcommit: 6e6813f8e5fa1f6f4661a640a49dc4c864f8a6cb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/17/2019
ms.locfileid: "67151137"
---
# <a name="tutorial-use-a-web-app-bot-enabled-with-language-understanding-in-c"></a>Samouczek: Użyj włączone z usługą interpretacji języka w robot aplikacji sieci WebC#

Użyj C# tworzenie czatbot zintegrowane z language understanding (LUIS). Bot jest oparte na platformie Azure [sieci Web aplikacji bot](https://docs.microsoft.com/azure/bot-service/) zasobów i [Bot Framework w wersji](https://github.com/Microsoft/botbuilder-dotnet) V4.

**Ten samouczek zawiera informacje na temat wykonywania następujących czynności:**

> [!div class="checklist"]
> * Tworzenie bota aplikacji internetowej. Ten proces tworzy nową aplikację usługi LUIS.
> * Pobieranie projektu bot utworzone przez usługę sieci Web bot
> * Uruchamianie bota i emulatora lokalnie na komputerze
> * Wyświetlanie wyników wypowiedzi w bocie

## <a name="prerequisites"></a>Wymagania wstępne

* [Emulator bota](https://aka.ms/abs/build/emulatordownload)
* [Program Visual Studio](https://visualstudio.microsoft.com/downloads/)


## <a name="create-a-web-app-bot-resource"></a>Utwórz zasób bot aplikacji sieci web

1. W witrynie [Azure Portal](https://portal.azure.com) wybierz polecenie **Utwórz nowy zasób**.

1. W polu wyszukiwania wyszukaj i wybierz pozycję **Web App Bot** (Bot aplikacji internetowej). Wybierz pozycję **Utwórz**.

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
    |App service plan/lokalizacja|Nie należy zmieniać z wartości podanej domyślnej.|
    |Application Insights|Nie należy zmieniać z wartości podanej domyślnej.|
    |Identyfikator aplikacji firmy Microsoft i hasła|Nie należy zmieniać z wartości podanej domyślnej.|

1. W **szablonu Bota**, wybrać następujące opcje, a następnie wybierz **wybierz** przycisku w ramach tych ustawień:

    |Ustawienie|Przeznaczenie|Wybór|
    |--|--|--|
    |Wersja zestawu SDK|Wersja struktury bota|**Zestaw SDK w wersji 4**|
    |Język zestawu SDK|Język programowania bota|**C#**|
    |Bot|Typ bota|**Bot podstawowy**|
    
1. Wybierz pozycję **Utwórz**. To powoduje utworzenie i wdrożenie usługi bota na platformie Azure. W ramach tego procesu jest tworzona nowa aplikacja usługi LUIS o nazwie `luis-csharp-bot-XXXX`. Ta nazwa opiera się na nazwę aplikacji /Azure Bot Service.

    [![Tworzenie bota aplikacji internetowej](./media/bfv4-csharp/create-web-app-service.png)](./media/bfv4-csharp/create-web-app-service.png#lightbox)

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

    ![Zrzut ekranu witryny Azure portal, wprowadź tekst "hello".](./media/bfv4-csharp/ask-bot-question-in-portal-test-in-web-chat.png)

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

1. Otwórz **LuisHelper.cs** pliku. Jest to miejsce, gdzie wypowiedź użytkownika wprowadzana do bota jest wysyłania do usługi LUIS. Odpowiedź z usługi LUIS jest zwracana z metody jako **BookDetails** obiektu. Podczas tworzenia własnych bot, należy również utworzyć obiekt do zwrócenia szczegółowe informacje z usługi LUIS. 


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

1. Otwórz **BookingDetails.cs** do wyświetlania, jak obiekt przenosi dane z usługi LUIS. 

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

1. Otwórz **okien dialogowych -> BookingDialog.cs** Aby zrozumieć, jak obiekt BookingDetails jest używany do zarządzania przepływem konwersacji. Szczegóły podróży, zostaną poproszeni o w krokach, a następnie cały rezerwacji jest potwierdzone, a na koniec jest powtarzany do użytkownika. 

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


## <a name="start-the-bot-code-in-visual-studio"></a>Uruchom kod robotów w programie Visual Studio

Uruchom bota w programie Visual Studio. Zostanie otwarte okno przeglądarki z witryną sieci web bota aplikacji internetowej pod adresem `http://localhost:3978/`. Strona główna wyświetla informacje o Twoim czatbocie.

![Strona główna wyświetla informacje o Twoim czatbocie.](./media/bfv4-csharp/running-bot-web-home-page-success.png)

## <a name="use-the-bot-emulator-to-test-the-bot"></a>Korzystanie z emulatora bot do testowania robota

1. Rozpocznij emulatora Bot, a następnie wybierz pozycję **Otwórz Bot**.
1. W **Otwórz robota** wyskakującego okna dialogowego, wprowadź adres URL bot, takich jak `http://localhost:3978/api/messages`. `/api/messages` Trasy jest adres sieci web dla bota.
1. Wprowadź **Identyfikatora aplikacji Microsoft** i **hasło Microsoft App**znajdujące się w **appsettings.json** plik w folderze głównym kodu bot został pobrany.

    Opcjonalnie możesz utworzyć nowe bot konfiguracji i skopiuj `appId` i `appPassword` z **appsettings.json** pliku w projekcie programu Visual Studio dla bota. Nazwa pliku konfiguracji robota powinna być taka sama jak nazwa robota. 

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

1. Bot, w emulatorze wpisz `Hello` i uzyskać tę samą odpowiedź na podstawowy bot, jak wartość otrzymana w **testu w czatów internetowych**.

    [![Odpowiedź bota podstawowego w emulatorze](./media/bfv4-csharp/ask-bot-emulator-a-question-and-get-response.png)](./media/bfv4-csharp/ask-bot-emulator-a-question-and-get-response.png#lightbox)


## <a name="ask-bot-a-question-for-the-book-flight-intent"></a>Zadaj pytanie na intencje lotu książki bot

1. W emulatorze bot książki lotu, wprowadzając następujące wypowiedź: 

    ```bot
    Book a flight from Paris to Berlin on March 22, 2020
    ```

    Bot emulator z prośbą o potwierdzenie. 

1. Wybierz **tak**. Bot odpowiada za pomocą podsumowanie działań. 
1. W dzienniku emulatora bot, wybierz wiersz, który zawiera `Luis Trace`. Spowoduje to wyświetlenie odpowiedź JSON z usługi LUIS intencje i podmioty wypowiedź.

    [![Odpowiedź bota podstawowego w emulatorze](./media/bfv4-csharp/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png)](./media/bfv4-csharp/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png#lightbox)

## <a name="learn-more-about-the-web-app-bot-and-framework"></a>Dowiedz się więcej o Bot aplikacji sieci Web i platforma

Usługa Azure Bot używa zestawu SDK Bot Framework. Dowiedz się więcej na temat zestawu SDK i struktury Bot Framework:

* Dokumentacja [usługi Azure Bot Service](https://docs.microsoft.com/azure/bot-service/bot-service-overview-introduction?view=azure-bot-service-4.0) w wersji 4
* [Przykłady Bot Builder](https://github.com/Microsoft/botbuilder-samples)
* [Bot Builder C# zestawu SDK](https://github.com/Microsoft/botbuilder-dotnet)
* [Narzędzia Bot Builder](https://github.com/Microsoft/botbuilder-tools):

## <a name="next-steps"></a>Kolejne kroki

Zobacz więcej [przykładów](https://github.com/microsoft/botframework-solutions) z botami konwersacyjnymi. 

> [!div class="nextstepaction"]
> [Tworzenie aplikacji przy użyciu domeny podmiotu niestandardowego Language Understanding](luis-quickstart-intents-only.md)
