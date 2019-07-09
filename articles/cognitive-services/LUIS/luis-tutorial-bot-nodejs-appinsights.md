---
title: Usługa Application Insights, środowiska Node.js
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
ms.openlocfilehash: cfed5477df75350f24e77786117e85b9c728c49a
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/08/2019
ms.locfileid: "67657751"
---
# <a name="add-luis-results-to-application-insights-from-a-bot-in-nodejs"></a>Dodawanie usługi LUIS wyniki do usługi Application Insights z Bota w środowisku Node.js
W tym samouczku dodaje bot i Language Understanding informacje [usługi Application Insights](https://azure.microsoft.com/services/application-insights/) magazyn danych telemetrycznych. Po utworzeniu tych danych, można tworzyć zapytania po przy użyciu języka Kusto lub Power BI, aby analizować, agregowania i tworzyć raporty dotyczące intencje i podmioty wypowiedź w czasie rzeczywistym. Ta analiza pomaga określić, jeśli Dodawanie lub edytowanie intencje i podmioty aplikacją usługi LUIS.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Przechwytywanie bot i Language understanding danych w usłudze Application Insights
> * Zapytanie usługi Application Insights dla danych Language Understanding

## <a name="prerequisites"></a>Wymagania wstępne

* Usługa Azure bot service bot, utworzony za pomocą usługi Application Insights włączoną.
* Pobrano bot kod z poprzednich bot  **[samouczek](luis-nodejs-tutorial-bf-v4.md)** . 
* [Emulator bota](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio Code](https://code.visualstudio.com/Download)

Cały kod w tym samouczku jest dostępny na [repozytorium przykładów dla platformy Azure Language Understanding GitHub](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/tutorial-web-app-bot-application-insights/v4/luis-nodejs-bot-johnsmith-src-telemetry). 

## <a name="add-application-insights-to-web-app-bot-project"></a>Dodaj usługę Application Insights do projektu bot aplikacji sieci web
Obecnie usługi Application Insights, używane w tym web Apps umożliwia zbieranie informacji o ogólnym stanem telemetria dla bota. Nie są zbierane informacje usługi LUIS. 

Przechwytywanie informacji usługi LUIS, bot aplikacji sieci web musi **[usługi Application Insights](https://www.npmjs.com/package/applicationinsights)** pakietu NPM zainstalowane i skonfigurowane.  

1. W zintegrowanym terminalu programu VSCode w katalogu głównym projektu bot, Dodaj następujące pakiety NPM przy użyciu polecenia wyświetlane: 

    ```console
    npm install applicationinsights && npm install underscore
    ```
    
    **Podkreślenia** spłaszczanie strukturze JSON usługi LUIS, dzięki czemu do przeglądania i używania w usłudze Application Insights jest używany pakiet.
    


## <a name="capture-and-send-luis-query-results-to-application-insights"></a>Przechwytywane i wysyłane do usługi LUIS wyników zapytania do usługi Application Insights

1. W programu VSCode, Utwórz nowy plik **appInsightsLog.js** i Dodaj następujący kod:

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

    Ten plik ma bot kontekstu i odpowiedzi usługi luis spłaszcza oba obiekty i wstawia je do **śledzenia** zdarzeń w usłudze application insights. Nazwa zdarzenia jest **LUIS**. 

1. Otwórz **okien dialogowych** folder, a następnie **luisHelper.js** pliku. Nowe **appInsightsLog.js** jako wymaganego pliku i przechwytywania kontekstu bot i LUIS odpowiedzi. Kompletny kod dla tego pliku jest: 

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

## <a name="add-application-insights-instrumentation-key"></a>Dodaj klucz Instrumentacji usługi Application Insights 

Aby dodać dane do usługi application insights, możesz potrzebować klucza instrumentacji.

1. W przeglądarce w [witryny Azure portal](https://portal.azure.com), Znajdź Twój bot **usługi Application Insights** zasobów. Jego nazwa będzie najczęściej nazwa robota, a następnie losowych znaków na końcu nazwy, takie jak `luis-nodejs-bot-johnsmithxqowom`. 
1. W zasobie usługi Application Insights na **Przegląd** stronie, skopiuj **klucz Instrumentacji**.
1. Otwórz w VSCode, **ENV** pliku w folderze głównym projektu botów. Ten plik przechowuje wszystkie zmienne środowiskowe.  
1. Dodaj nową zmienną `MicrosoftApplicationInsightsInstrumentationKey` z wartością klucza instrumentacji. Czy nie Umieść wartość w cudzysłowie. 

## <a name="start-the-bot"></a>Uruchamianie bota

1. W zintegrowanym terminalu programu VSCode start bot:
    
    ```console
    npm start
    ```

1. Uruchom bot emulator i otwórz robota. To [kroku](luis-nodejs-tutorial-bf-v4.md#use-the-bot-emulator-to-test-the-bot) znajduje się w poprzednim samouczku.

1. Zapytać bota pytań. To [kroku](luis-nodejs-tutorial-bf-v4.md#ask-bot-a-question-for-the-book-flight-intent) znajduje się w poprzednim samouczku.

## <a name="view-luis-entries-in-application-insights"></a>Wyświetlanie usługi LUIS wpisów w usłudze Application Insights

Otwórz usługę Application Insights, aby wyświetlić wpisy usługi LUIS. Może upłynąć kilka minut, zanim dane pojawią się w usłudze Application Insights.

1. W [witryny Azure portal](https://portal.azure.com), otwórz zasób usługi Application Insights botów. 
1. Po otwarciu zasobu wybierz **wyszukiwania** i wyszukiwać wszystkie dane w ciągu ostatnich **30 minut** z typem zdarzenia **śledzenia**. Wybierz opcję śledzenia o nazwie **LUIS**. 
1. Bot i LUIS informacje są dostępne w obszarze **właściwościami niestandardowymi**. 

    ![Przejrzyj usługi LUIS właściwości niestandardowe przechowywane w usłudze Application Insights](./media/luis-tutorial-appinsights/application-insights-luis-trace-custom-properties-nodejs.png)

## <a name="query-application-insights-for-intent-score-and-utterance"></a>Zapytanie usługi Application Insights dla przeznaczenie, ocena i wypowiedź
Usługa Application Insights daje uprawnienia do wykonywania zapytań o dane za pomocą [Kusto](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview#what-language-do-log-queries-use) języka, jak eksportu do [usługi Power BI](https://powerbi.microsoft.com). 

1. Wybierz **dziennika (analiza)** . Nowe okno zostanie otwarte okno zapytania u góry i oknem danych tabeli poniżej. Jeśli używano wcześniej bazy danych to rozwiązanie jest znana. Zapytanie reprezentuje poprzedniego filtrowane dane. **Tabeli CustomDimensions** kolumna ma bot i LUIS informacji.
1. Aby wyciągnąć najważniejsze przeznaczenie, ocenę i wypowiedź, Dodaj następujące powyżej ostatni wiersz ( `|top...` wiersz) w oknie zapytania:

    ```kusto
    | extend topIntent = tostring(customDimensions.LUIS_luisResponse_luisResult_topScoringIntent_intent)
    | extend score = todouble(customDimensions.LUIS_luisResponse_luisResult_topScoringIntent_score)
    | extend utterance = tostring(customDimensions.LUIS_luisResponse_text)
    ```

1. Uruchom zapytanie. Dostępne są nowe kolumny topIntent, ocena i wypowiedź. Wybierz kolumnę topIntent do sortowania.

Dowiedz się więcej o [język zapytania Kusto](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries) lub [dane są eksportowane do usługi Power BI](https://docs.microsoft.com/azure/application-insights/app-insights-export-power-bi). 

## <a name="next-steps"></a>Kolejne kroki

Inne informacje, które chcesz dodać do aplikacji danych szczegółowych informacji zawiera identyfikator aplikacji, identyfikator wersji, Data ostatniej zmiany modelu, Data ostatniego szkolenie, Data ostatniej publikacji. Te wartości albo można pobrać z adresu URL punktu końcowego (aplikacji Identyfikatora oraz identyfikator wersji) lub wywołanie interfejsu API tworzenia pakietów administracyjnych, a następnie w ustawieniach bot aplikacji sieci web i pobierane z tego miejsca.  

Jeśli używasz tej samej subskrypcji punktu końcowego dla więcej niż jedną aplikacją usługi LUIS, powinny również obejmować identyfikator subskrypcji i właściwości z informacją, że jest on klucza wstępnego. 

> [!div class="nextstepaction"]
> [Dowiedz się więcej o przykład wypowiedzi](luis-how-to-add-example-utterances.md)
