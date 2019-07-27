---
title: Application Insights, Node. js — LUIS
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
ms.openlocfilehash: 72ce681b6b0e4109151b987a5f8cc4bc050aafa0
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68563292"
---
# <a name="add-luis-results-to-application-insights-from-a-bot-in-nodejs"></a>Dodaj wyniki LUIS do Application Insights z bot w języku Node. js
Ten samouczek dodaje bot i Language Understanding informacje do [Application Insights](https://azure.microsoft.com/services/application-insights/) magazynu danych telemetrycznych. Po uzyskaniu tych danych możesz wysyłać do nich zapytania przy użyciu języka Kusto lub Power BI analizowania, agregowania i raportowania na temat intencji oraz jednostek wypowiedź w czasie rzeczywistym. Ta analiza pomaga określić, jeśli Dodawanie lub edytowanie intencje i podmioty aplikacją usługi LUIS.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Przechwyć dane dotyczące bot i języka w Application Insights
> * Application Insights kwerendy dla Language Understanding danych

## <a name="prerequisites"></a>Wymagania wstępne

* Usługa Azure bot Service bot utworzona z włączonym Application Insights.
* Pobrano kod bot z poprzedniego **[samouczka](luis-nodejs-tutorial-bf-v4.md)** bot. 
* [Emulator bota](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio Code](https://code.visualstudio.com/Download)

Cały kod w tym samouczku jest dostępny na [platformie Azure-samples Language Understanding repozytorium GitHub](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/tutorial-web-app-bot-application-insights/v4/luis-nodejs-bot-johnsmith-src-telemetry). 

## <a name="add-application-insights-to-web-app-bot-project"></a>Dodaj Application Insights do projektu bot aplikacji sieci Web
Obecnie usługi Application Insights, używane w tym web Apps umożliwia zbieranie informacji o ogólnym stanem telemetria dla bota. Nie zbiera informacji LUIS. 

Aby można było przechwycić informacje LUIS, aplikacja internetowa bot musi mieć zainstalowany i skonfigurowany pakiet **[Application Insights](https://www.npmjs.com/package/applicationinsights)** npm.  

1. W zintegrowanym terminalu programu vscode w katalogu głównym projektu bot Dodaj następujące pakiety NPM przy użyciu podanego polecenia: 

    ```console
    npm install applicationinsights && npm install underscore
    ```
    
    Pakiet **podkreślenia** służy do spłaszczania struktury JSON Luis, dzięki czemu łatwiej jest zobaczyć i używać w Application Insights.
    


## <a name="capture-and-send-luis-query-results-to-application-insights"></a>Przechwytywane i wysyłane do usługi LUIS wyników zapytania do usługi Application Insights

1. W programu vscode Utwórz nowy plik **appInsightsLog. js** i Dodaj następujący kod:

    ```javascript
    const appInsights = require('applicationinsights');
    const _ = require("underscore");
    
    // Log LUIS results to Application Insights
    // must flatten as name/value pairs
    var appInsightsLog = (botContext,luisResponse) => {

        appInsights.setup(process.env.MicrosoftApplicationInsightsInstrumentationKey).start();
        const appInsightsClient = appInsights.defaultClient;

        // put bot context and LUIS results into single object
        var data = Object.assign({}, {'botContext': botContext._activity}, {'luisResponse': luisResponse});
    
        // Flatten data into name/value pairs
        flatten = (x, result, prefix) => {
            if(_.isObject(x)) {
                _.each(x, (v, k) => {
                    flatten(v, result, prefix ? prefix + '_' + k : k)
                })
            } else {
                result["LUIS_" + prefix] = x
            }
            return result;
        }
    
        // call fn to flatten data
        var flattenedData = flatten(data, {});
    
        // ApplicationInsights Trace 
        console.log(JSON.stringify(flattenedData));
    
        // send data to Application Insights
        appInsightsClient.trackTrace({message: "LUIS", severity: appInsights.Contracts.SeverityLevel.Information, properties: flattenedData});
    }
    
    module.exports.appInsightsLog = appInsightsLog;
    ```

    Ten plik przyjmuje kontekst bot i odpowiedź Luis, spłaszcza oba obiekty i wstawia je do zdarzenia **śledzenia** w usłudze Application Insights. Nazwa zdarzenia to **Luis**. 

1. Otwórz folder **Dialogs** , a następnie plik **luisHelper. js** . Dołącz nowy **appInsightsLog. js** jako plik wymagany i Przechwyć kontekst bot i odpowiedź Luis. Pełny kod dla tego pliku: 

    ```javascript
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.
    
    const { LuisRecognizer } = require('botbuilder-ai');
    const { appInsightsLog } = require('../appInsightsLog');
    
    class LuisHelper {
        /**
         * Returns an object with preformatted LUIS results for the bot's dialogs to consume.
         * @param {*} logger
         * @param {TurnContext} context
         */
        static async executeLuisQuery(logger, context) {
            const bookingDetails = {};
    
            try {
                const recognizer = new LuisRecognizer({
                    applicationId: process.env.LuisAppId,
                    endpointKey: process.env.LuisAPIKey,
                    endpoint: `https://${ process.env.LuisAPIHostName }`
                }, {}, true);
    
                const recognizerResult = await recognizer.recognize(context);
    
                // APPINSIGHT: Log results to Application Insights
                appInsightsLog(context,recognizerResult);
    
    
                const intent = LuisRecognizer.topIntent(recognizerResult);
    
                bookingDetails.intent = intent;
    
                if (intent === 'Book_flight') {
                    // We need to get the result from the LUIS JSON which at every level returns an array
    
                    bookingDetails.destination = LuisHelper.parseCompositeEntity(recognizerResult, 'To', 'Airport');
                    bookingDetails.origin = LuisHelper.parseCompositeEntity(recognizerResult, 'From', 'Airport');
    
                    // This value will be a TIMEX. And we are only interested in a Date so grab the first result and drop the Time part.
                    // TIMEX is a format that represents DateTime expressions that include some ambiguity. e.g. missing a Year.
                    bookingDetails.travelDate = LuisHelper.parseDatetimeEntity(recognizerResult);
                }
            } catch (err) {
                logger.warn(`LUIS Exception: ${ err } Check your LUIS configuration`);
            }
            return bookingDetails;
        }
    
        static parseCompositeEntity(result, compositeName, entityName) {
            const compositeEntity = result.entities[compositeName];
            if (!compositeEntity || !compositeEntity[0]) return undefined;
    
            const entity = compositeEntity[0][entityName];
            if (!entity || !entity[0]) return undefined;
    
            const entityValue = entity[0][0];
            return entityValue;
        }
    
        static parseDatetimeEntity(result) {
            const datetimeEntity = result.entities['datetime'];
            if (!datetimeEntity || !datetimeEntity[0]) return undefined;
    
            const timex = datetimeEntity[0]['timex'];
            if (!timex || !timex[0]) return undefined;
    
            const datetime = timex[0].split('T')[0];
            return datetime;
        }
    }
    
    module.exports.LuisHelper = LuisHelper;
    ```

## <a name="add-application-insights-instrumentation-key"></a>Dodaj klucz Instrumentacji Application Insights 

Aby można było dodać dane do usługi Application Insights, potrzebny jest klucz Instrumentacji.

1. W przeglądarce w [Azure Portal](https://portal.azure.com)znajdź zasób **Application Insights** bot. Jego nazwa będzie zawierać większość nazwy bot, a następnie losowe znaki na końcu nazwy, `luis-nodejs-bot-johnsmithxqowom`na przykład. 
1. Na zasobie Application Insights, na stronie **Przegląd** Skopiuj **klucz Instrumentacji**.
1. W programu vscode Otwórz plik **ENV** w folderze głównym projektu bot. Ten plik zawiera wszystkie zmienne środowiskowe.  
1. Dodaj nową zmienną `MicrosoftApplicationInsightsInstrumentationKey` z wartością klucza Instrumentacji. Nie należy umieszczać wartości w cudzysłowach. 

## <a name="start-the-bot"></a>Uruchamianie bota

1. W zintegrowanym terminalu programu vscode Uruchom bot:
    
    ```console
    npm start
    ```

1. Uruchom emulator bot i Otwórz bot. Ten [krok](luis-nodejs-tutorial-bf-v4.md#use-the-bot-emulator-to-test-the-bot) jest dostępny w poprzednim samouczku.

1. Zadawaj pytanie bot. Ten [krok](luis-nodejs-tutorial-bf-v4.md#ask-bot-a-question-for-the-book-flight-intent) jest dostępny w poprzednim samouczku.

## <a name="view-luis-entries-in-application-insights"></a>Wyświetlanie usługi LUIS wpisów w usłudze Application Insights

Otwórz usługę Application Insights, aby wyświetlić wpisy usługi LUIS. Wyświetlenie danych w Application Insights może potrwać kilka minut.

1. W [Azure Portal](https://portal.azure.com)otwórz zasób Application Insights bot. 
1. Gdy zasób zostanie otwarty, wybierz pozycję **Wyszukaj** i Wyszukaj wszystkie dane w ciągu ostatnich **30 minut** z typem zdarzenia **śledzenia**. Wybierz ślad o nazwie **Luis**. 
1. Informacje o bot i LUIS są dostępne w obszarze **właściwości niestandardowe**. 

    ![Przegląd właściwości niestandardowych LUIS przechowywanych w Application Insights](./media/luis-tutorial-appinsights/application-insights-luis-trace-custom-properties-nodejs.png)

## <a name="query-application-insights-for-intent-score-and-utterance"></a>Zapytanie usługi Application Insights dla przeznaczenie, ocena i wypowiedź
Application Insights umożliwia wykonywanie zapytań dotyczących danych przy użyciu języka [Kusto](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview#what-language-do-log-queries-use) oraz eksportowanie go do [Power BI](https://powerbi.microsoft.com). 

1. Wybierz pozycję **Dziennik (analiza)** . Nowe okno zostanie otwarte okno zapytania u góry i oknem danych tabeli poniżej. Jeśli używano wcześniej bazy danych to rozwiązanie jest znana. Zapytanie reprezentuje poprzednie odfiltrowane dane. Kolumna **CustomDimensions** zawiera informacje o bot i Luis.
1. Aby wyciągnąć górny cel, wynik i wypowiedź, Dodaj następujące tuż powyżej ostatniego wiersza ( `|top...` wiersz) w oknie zapytania:

    ```kusto
    | extend topIntent = tostring(customDimensions.LUIS_luisResponse_luisResult_topScoringIntent_intent)
    | extend score = todouble(customDimensions.LUIS_luisResponse_luisResult_topScoringIntent_score)
    | extend utterance = tostring(customDimensions.LUIS_luisResponse_text)
    ```

1. Uruchom zapytanie. Dostępne są nowe kolumny topIntent, ocena i wypowiedź. Wybierz kolumnę topIntent, aby posortować.

Dowiedz się więcej na temat [języka zapytań Kusto](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries) lub wyeksportuj [dane do Power BI](https://docs.microsoft.com/azure/application-insights/app-insights-export-power-bi). 

## <a name="next-steps"></a>Następne kroki

Inne informacje, które chcesz dodać do aplikacji danych szczegółowych informacji zawiera identyfikator aplikacji, identyfikator wersji, Data ostatniej zmiany modelu, Data ostatniego szkolenie, Data ostatniej publikacji. Te wartości można pobrać z adresu URL punktu końcowego (identyfikatora aplikacji i identyfikatora wersji) lub z wywołania interfejsu API tworzenia, a następnie ustawić je w ustawieniach bot aplikacji sieci Web i pobrać z tego miejsca.  

Jeśli używasz tej samej subskrypcji punktu końcowego dla więcej niż jedną aplikacją usługi LUIS, powinny również obejmować identyfikator subskrypcji i właściwości z informacją, że jest on klucza wstępnego. 

> [!div class="nextstepaction"]
> [Dowiedz się więcej o przykład wypowiedzi](luis-how-to-add-example-utterances.md)
