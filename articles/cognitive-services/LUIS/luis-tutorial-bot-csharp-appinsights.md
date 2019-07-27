---
title: Application Insights, C# -Luis
titleSuffix: Azure Cognitive Services
description: Ten samouczek dodaje bot i Language Understanding informacje do Application Insights magazynu danych telemetrycznych.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 06/16/2019
ms.author: diberry
ms.openlocfilehash: bc8cf9973ed6889b0820e5ada5565d0541532fa3
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68560064"
---
# <a name="add-luis-results-to-application-insights-from-a-bot-in-c"></a>Dodaj wyniki LUIS do Application Insights z bot wC#

Ten samouczek dodaje bot i Language Understanding informacje do [Application Insights](https://azure.microsoft.com/services/application-insights/) magazynu danych telemetrycznych. Po uzyskaniu tych danych możesz wysyłać do nich zapytania przy użyciu języka Kusto lub Power BI analizowania, agregowania i raportowania na temat intencji oraz jednostek wypowiedź w czasie rzeczywistym. Ta analiza pomaga określić, jeśli Dodawanie lub edytowanie intencje i podmioty aplikacją usługi LUIS.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Przechwyć dane dotyczące bot i języka w Application Insights
> * Application Insights kwerendy dla Language Understanding danych

## <a name="prerequisites"></a>Wymagania wstępne

* Usługa Azure bot Service bot utworzona z włączonym Application Insights.
* Pobrano kod bot z poprzedniego **[samouczka](luis-csharp-tutorial-bf-v4.md)** bot. 
* [Emulator bota](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio Code](https://code.visualstudio.com/Download)

Cały kod w tym samouczku jest dostępny na [platformie Azure-samples Language Understanding repozytorium GitHub](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/tutorial-web-app-bot-application-insights/v4/luis-csharp-bot-johnsmith-src-telemetry). 

## <a name="add-application-insights-to-web-app-bot-project"></a>Dodaj Application Insights do projektu bot aplikacji sieci Web

Obecnie usługi Application Insights, używane w tym web Apps umożliwia zbieranie informacji o ogólnym stanem telemetria dla bota. Nie zbiera informacji LUIS. 

Aby można było przechwycić informacje LUIS, aplikacja internetowa bot musi mieć zainstalowany i skonfigurowany pakiet NuGet **[Microsoft. ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights/)** .  

1. W programie Visual Studio Dodaj zależność do rozwiązania. W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy nazwę projektu i wybierz pozycję **Zarządzaj pakietami NuGet..** .. Menedżer pakietów NuGet pokazuje listę zainstalowanych pakietów. 
1. Wybierz pozycję **Przeglądaj** , a następnie wyszukaj ciąg **Microsoft. ApplicationInsights**.
1. Zainstaluj pakiet. 

## <a name="capture-and-send-luis-query-results-to-application-insights"></a>Przechwytywane i wysyłane do usługi LUIS wyników zapytania do usługi Application Insights

1. `LuisHelper.cs` Otwórz plik i Zastąp jego zawartość następującym kodem. Metoda **LogToApplicationInsights** przechwytuje dane bot i Luis i wysyła je do Application Insights jako zdarzenia śledzenia o nazwie `LUIS`.

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
    using Microsoft.ApplicationInsights;
    using System.Collections.Generic;
    
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
    
                    LuisHelper.LogToApplicationInsights(configuration, turnContext, recognizerResult);
    
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
            public static void LogToApplicationInsights(IConfiguration configuration, ITurnContext turnContext, RecognizerResult result)
            {
                // Create Application Insights object
                TelemetryClient telemetry = new TelemetryClient();
    
                // Set Application Insights Instrumentation Key from App Settings
                telemetry.InstrumentationKey = configuration["BotDevAppInsightsKey"];
    
                // Collect information to send to Application Insights
                Dictionary<string, string> logProperties = new Dictionary<string, string>();
    
                logProperties.Add("BotConversation", turnContext.Activity.Conversation.Name);
                logProperties.Add("Bot_userId", turnContext.Activity.Conversation.Id);
    
                logProperties.Add("LUIS_query", result.Text);
                logProperties.Add("LUIS_topScoringIntent_Name", result.GetTopScoringIntent().intent);
                logProperties.Add("LUIS_topScoringIntentScore", result.GetTopScoringIntent().score.ToString());
    
    
                // Add entities to collected information
                int i = 1;
                if (result.Entities.Count > 0)
                {
                    foreach (var item in result.Entities)
                    {
                        logProperties.Add("LUIS_entities_" + i++ + "_" + item.Key, item.Value.ToString());
                    }
                }
    
                // Send to Application Insights
                telemetry.TrackTrace("LUIS", ApplicationInsights.DataContracts.SeverityLevel.Information, logProperties);
    
            }
        }
    }
    ```

## <a name="add-application-insights-instrumentation-key"></a>Dodaj klucz Instrumentacji Application Insights 

Aby można było dodać dane do usługi Application Insights, potrzebny jest klucz Instrumentacji.

1. W przeglądarce w [Azure Portal](https://portal.azure.com)znajdź zasób **Application Insights** bot. Jego nazwa będzie zawierać większość nazwy bot, a następnie losowe znaki na końcu nazwy, `luis-csharp-bot-johnsmithxqowom`na przykład. 
1. Na zasobie Application Insights, na stronie **Przegląd** Skopiuj **klucz Instrumentacji**.
1. W programie Visual Studio Otwórz plik **appSettings. JSON** w katalogu głównym projektu bot. Ten plik zawiera wszystkie zmienne środowiskowe.
1. Dodaj nową zmienną `BotDevAppInsightsKey` z wartością klucza Instrumentacji. Wartość w polu powinna znajdować się w cudzysłowie. 

## <a name="build-and-start-the-bot"></a>Kompiluj i uruchamiaj bot

1. W programie Visual Studio Kompiluj i uruchamiaj bot. 
1. Uruchom emulator bot i Otwórz bot. Ten [krok](luis-csharp-tutorial-bf-v4.md#use-the-bot-emulator-to-test-the-bot) jest dostępny w poprzednim samouczku.

1. Zadawaj pytanie bot. Ten [krok](luis-csharp-tutorial-bf-v4.md#ask-bot-a-question-for-the-book-flight-intent) jest dostępny w poprzednim samouczku.

## <a name="view-luis-entries-in-application-insights"></a>Wyświetlanie usługi LUIS wpisów w usłudze Application Insights

Otwórz usługę Application Insights, aby wyświetlić wpisy usługi LUIS. Wyświetlenie danych w Application Insights może potrwać kilka minut.

1. W [Azure Portal](https://portal.azure.com)otwórz zasób Application Insights bot. 
1. Gdy zasób zostanie otwarty, wybierz pozycję **Wyszukaj** i Wyszukaj wszystkie dane w ciągu ostatnich **30 minut** z typem zdarzenia **śledzenia**. Wybierz ślad o nazwie **Luis**. 
1. Informacje o bot i LUIS są dostępne w obszarze **właściwości niestandardowe**. 

    ![Przegląd właściwości niestandardowych LUIS przechowywanych w Application Insights](./media/luis-tutorial-appinsights/application-insights-luis-trace-custom-properties-csharp.png)

## <a name="query-application-insights-for-intent-score-and-utterance"></a>Zapytanie usługi Application Insights dla przeznaczenie, ocena i wypowiedź
Application Insights umożliwia wykonywanie zapytań dotyczących danych przy użyciu języka [Kusto](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview#what-language-do-log-queries-use) oraz eksportowanie go do [Power BI](https://powerbi.microsoft.com). 

1. Wybierz pozycję **Dziennik (analiza)** . Nowe okno zostanie otwarte okno zapytania u góry i oknem danych tabeli poniżej. Jeśli używano wcześniej bazy danych to rozwiązanie jest znana. Zapytanie reprezentuje poprzednie odfiltrowane dane. Kolumna **CustomDimensions** zawiera informacje o bot i Luis.
1. Aby wyciągnąć górny cel, wynik i wypowiedź, Dodaj następujące tuż powyżej ostatniego wiersza ( `|top...` wiersz) w oknie zapytania:

    ```kusto
    | extend topIntent = tostring(customDimensions.LUIS_topScoringIntent_Name)
    | extend score = todouble(customDimensions.LUIS_topScoringIntentScore)
    | extend utterance = tostring(customDimensions.LUIS_query)
    ```

1. Uruchom zapytanie. Dostępne są nowe kolumny topIntent, ocena i wypowiedź. Wybierz kolumnę topIntent, aby posortować.

Dowiedz się więcej na temat [języka zapytań Kusto](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries) lub wyeksportuj [dane do Power BI](https://docs.microsoft.com/azure/application-insights/app-insights-export-power-bi). 


## <a name="learn-more-about-bot-framework"></a>Dowiedz się więcej na temat struktury Bot Framework

Dowiedz się więcej o [platformy Bot Framework](https://dev.botframework.com/).

## <a name="next-steps"></a>Kolejne kroki

Inne informacje, które chcesz dodać do aplikacji danych szczegółowych informacji zawiera identyfikator aplikacji, identyfikator wersji, Data ostatniej zmiany modelu, Data ostatniego szkolenie, Data ostatniej publikacji. Te wartości można pobrać z adresu URL punktu końcowego (identyfikatora aplikacji i identyfikatora wersji) lub z wywołania interfejsu API tworzenia, a następnie ustawić je w ustawieniach bot aplikacji sieci Web i pobrać z tego miejsca.  

Jeśli używasz tej samej subskrypcji punktu końcowego dla więcej niż jedną aplikacją usługi LUIS, powinny również obejmować identyfikator subskrypcji i właściwości z informacją, że jest on klucza wstępnego.

> [!div class="nextstepaction"]
> [Dowiedz się więcej o przykład wypowiedzi](luis-how-to-add-example-utterances.md)
