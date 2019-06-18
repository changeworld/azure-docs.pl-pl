---
title: Usługa Application InsightsC#
titleSuffix: Azure Cognitive Services
description: W tym samouczku dodaje bot i Language Understanding informacji do przechowywania danych telemetrycznych usługi Application Insights.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 06/16/2019
ms.author: diberry
ms.openlocfilehash: fa7147dd1b5f22ead17a60042c1c35c4b770cd18
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/17/2019
ms.locfileid: "67154918"
---
# <a name="add-luis-results-to-application-insights-from-a-bot-in-c"></a>Dodawanie usługi LUIS wyniki do usługi Application Insights z Bota wC#

W tym samouczku dodaje bot i Language Understanding informacje [usługi Application Insights](https://azure.microsoft.com/services/application-insights/) magazyn danych telemetrycznych. Po utworzeniu tych danych, można tworzyć zapytania po przy użyciu języka Kusto lub Power BI, aby analizować, agregowania i tworzyć raporty dotyczące intencje i podmioty wypowiedź w czasie rzeczywistym. Ta analiza pomaga określić, jeśli Dodawanie lub edytowanie intencje i podmioty aplikacją usługi LUIS.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Przechwytywanie bot i Language understanding danych w usłudze Application Insights
> * Zapytanie usługi Application Insights dla danych Language Understanding

## <a name="prerequisites"></a>Wymagania wstępne

* Usługa Azure bot service bot, utworzony za pomocą usługi Application Insights włączoną.
* Pobrano bot kod z poprzednich bot  **[samouczek](luis-csharp-tutorial-bf-v4.md)** . 
* [Emulator bota](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio Code](https://code.visualstudio.com/Download)

Cały kod w tym samouczku jest dostępny na [repozytorium przykładów dla platformy Azure Language Understanding GitHub](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/tutorial-web-app-bot-application-insights/v4/luis-csharp-bot-johnsmith-src-telemetry). 

## <a name="add-application-insights-to-web-app-bot-project"></a>Dodaj usługę Application Insights do projektu bot aplikacji sieci web

Obecnie usługi Application Insights, używane w tym web Apps umożliwia zbieranie informacji o ogólnym stanem telemetria dla bota. Nie są zbierane informacje usługi LUIS. 

Przechwytywanie informacji usługi LUIS, bot aplikacji sieci web musi **[Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights/)** pakietu NuGet zainstalowany i skonfigurowany.  

1. W programie Visual Studio należy dodać zależności do rozwiązania. W **Eksploratora rozwiązań**, kliknij prawym przyciskiem myszy nazwę projektu i wybierz **Zarządzaj pakietami NuGet...** . Menedżer pakietów NuGet pokazuje listę zainstalowanych pakietów. 
1. Wybierz **Przeglądaj** Wyszukaj **Microsoft.ApplicationInsights**.
1. Zainstaluj pakiet. 

## <a name="capture-and-send-luis-query-results-to-application-insights"></a>Przechwytywane i wysyłane do usługi LUIS wyników zapytania do usługi Application Insights

1. Otwórz `LuisHelper.cs` plik i zastąp jego zawartość następującym kodem. **LogToApplicationInsights** metody przechwytywania bot i LUIS danych i wysyła je do usługi Application Insights jako zdarzenia śledzenia o nazwie `LUIS`.

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

## <a name="add-application-insights-instrumentation-key"></a>Dodaj klucz Instrumentacji usługi Application Insights 

Aby dodać dane do usługi application insights, możesz potrzebować klucza instrumentacji.

1. W przeglądarce w [witryny Azure portal](https://portal.azure.com), Znajdź Twój bot **usługi Application Insights** zasobów. Jego nazwa będzie najczęściej nazwa robota, a następnie losowych znaków na końcu nazwy, takie jak `luis-csharp-bot-johnsmithxqowom`. 
1. W zasobie usługi Application Insights na **Przegląd** stronie, skopiuj **klucz Instrumentacji**.
1. W programie Visual Studio, otwórz **appsettings.json** pliku w folderze głównym projektu botów. Ten plik przechowuje wszystkie zmienne środowiskowe.
1. Dodaj nową zmienną `BotDevAppInsightsKey` z wartością klucza instrumentacji. Wartość w powinien być w cudzysłowie. 

## <a name="build-and-start-the-bot"></a>Skompilować i uruchomić robota

1. W programie Visual Studio należy skompilować i uruchomić robota. 
1. Uruchom bot emulator i otwórz robota. To [kroku](luis-csharp-tutorial-bf-v4.md#use-the-bot-emulator-to-test-the-bot) znajduje się w poprzednim samouczku.

1. Zapytać bota pytań. To [kroku](luis-csharp-tutorial-bf-v4.md#ask-bot-a-question-for-the-book-flight-intent) znajduje się w poprzednim samouczku.

## <a name="view-luis-entries-in-application-insights"></a>Wyświetlanie usługi LUIS wpisów w usłudze Application Insights

Otwórz usługę Application Insights, aby wyświetlić wpisy usługi LUIS. Może upłynąć kilka minut, zanim dane pojawią się w usłudze Application Insights.

1. W [witryny Azure portal](https://portal.azure.com), otwórz zasób usługi Application Insights botów. 
1. Po otwarciu zasobu wybierz **wyszukiwania** i wyszukiwać wszystkie dane w ciągu ostatnich **30 minut** z typem zdarzenia **śledzenia**. Wybierz opcję śledzenia o nazwie **LUIS**. 
1. Bot i LUIS informacje są dostępne w obszarze **właściwościami niestandardowymi**. 

    ![Przejrzyj usługi LUIS właściwości niestandardowe przechowywane w usłudze Application Insights](./media/luis-tutorial-appinsights/application-insights-luis-trace-custom-properties-csharp.png)

## <a name="query-application-insights-for-intent-score-and-utterance"></a>Zapytanie usługi Application Insights dla przeznaczenie, ocena i wypowiedź
Usługa Application Insights daje uprawnienia do wykonywania zapytań o dane za pomocą [Kusto](https://docs.microsoft.com/azure/application-insights/app-insights-analytics#query-data-in-analytics) języka, jak eksportu do [usługi Power BI](https://powerbi.microsoft.com). 

1. Wybierz **dziennika (analiza)** . Nowe okno zostanie otwarte okno zapytania u góry i oknem danych tabeli poniżej. Jeśli używano wcześniej bazy danych to rozwiązanie jest znana. Zapytanie reprezentuje poprzedniego filtrowane dane. **Tabeli CustomDimensions** kolumna ma bot i LUIS informacji.
1. Aby wyciągnąć najważniejsze przeznaczenie, ocenę i wypowiedź, Dodaj następujące powyżej ostatni wiersz ( `|top...` wiersz) w oknie zapytania:

    ```kusto
    | extend topIntent = tostring(customDimensions.LUIS_topScoringIntent_Name)
    | extend score = todouble(customDimensions.LUIS_topScoringIntentScore)
    | extend utterance = tostring(customDimensions.LUIS_query)
    ```

1. Uruchom zapytanie. Dostępne są nowe kolumny topIntent, ocena i wypowiedź. Wybierz kolumnę topIntent do sortowania.

Dowiedz się więcej o [język zapytania Kusto](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries) lub [dane są eksportowane do usługi Power BI](https://docs.microsoft.com/azure/application-insights/app-insights-export-power-bi). 


## <a name="learn-more-about-bot-framework"></a>Dowiedz się więcej na temat struktury Bot Framework

Dowiedz się więcej o [platformy Bot Framework](https://dev.botframework.com/).

## <a name="next-steps"></a>Kolejne kroki

Inne informacje, które chcesz dodać do aplikacji danych szczegółowych informacji zawiera identyfikator aplikacji, identyfikator wersji, Data ostatniej zmiany modelu, Data ostatniego szkolenie, Data ostatniej publikacji. Te wartości albo można pobrać z adresu URL punktu końcowego (aplikacji Identyfikatora oraz identyfikator wersji) lub wywołanie interfejsu API tworzenia pakietów administracyjnych, a następnie w ustawieniach bot aplikacji sieci web i pobierane z tego miejsca.  

Jeśli używasz tej samej subskrypcji punktu końcowego dla więcej niż jedną aplikacją usługi LUIS, powinny również obejmować identyfikator subskrypcji i właściwości z informacją, że jest on klucza wstępnego.

> [!div class="nextstepaction"]
> [Dowiedz się więcej o przykład wypowiedzi](luis-how-to-add-example-utterances.md)
