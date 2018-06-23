---
title: Dodawanie LUIS danych do usługi Application Insights przy użyciu środowiska Node.js | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Tworzenie robotów zintegrowane z usługą LUIS aplikacji i przy użyciu środowiska Node.js w usłudze Application Insights.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 01/18/2018
ms.author: v-geberr
ms.openlocfilehash: 929b6e1cc980d7215f91a616820e257aed26bab7
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349596"
---
# <a name="add-luis-results-to-application-insights-from-a-web-app-bot"></a>Dodaj LUIS wyniki do usługi Application Insights z bot aplikacji sieci web
W tym samouczku dodaje informacje żądania i odpowiedzi LUIS do [usługi Application Insights](https://azure.microsoft.com/services/application-insights/) magazynu danych telemetrycznych. Po określeniu tych danych można wykonać zapytanie po Kusto języka lub usługi Power BI do analizowania, agregacji i tworzenia raportów dotyczących intencje i jednostek utterance w czasie rzeczywistym. Pomaga to analizy okaże się, jeśli Dodawanie lub edytowanie intencje i jednostek aplikacji LUIS.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
* Dodaj biblioteki usługi Application Insights do robot aplikacji sieci web
* Przechwytywanie i wysłać LUIS wyników zapytania do usługi Application Insights
* Celem top, oceny i utterance wykonanie kwerendy usługi Application Insights

## <a name="prerequisites"></a>Wymagania wstępne

* Bot aplikacji sieci web LUIS, tak z **[poprzedniego samouczek](luis-nodejs-tutorial-build-bot-framework-sample.md)** z usługi Application Insights włączona. 

> [!Tip]
> Jeśli nie masz już subskrypcję, możesz zarejestrować dla [bezpłatne konto](https://azure.microsoft.com/free/).

Cały kod z tego samouczka jest dostępna w [przykłady LUIS repozytorium github](https://github.com/Microsoft/LUIS-Samples/tree/master/documentation-samples/tutorial-web-app-bot-application-insights/nodejs) oraz każdy wiersz skojarzony z tego samouczka jest oznaczone jako z `//APPINSIGHT:`. 

## <a name="web-app-bot-with-luis"></a>Bot aplikacji sieci Web z LUIS
Ten samouczek zakłada kod, że wygląda podobnie do następującego lub że zostały wykonane [innych samouczek](luis-nodejs-tutorial-build-bot-framework-sample.md): 

   [!code-javascript[Web app bot with LUIS](~/samples-luis/documentation-samples/tutorial-web-app-bot/nodejs/app.js "Web app bot with LUIS")]

## <a name="add-application-insights-library-to-web-app-bot"></a>Dodaj biblioteki usługi Application Insights do bot aplikacji sieci web
Obecnie usługa Application Insights, używane w tym bot aplikacji sieci web, zbiera dane telemetryczne ogólnego stanu dla bot. Nie zbiera LUIS żądań i odpowiedzi informacje potrzebne do sprawdzenia i naprawić Twojej lokalizacji docelowych i jednostek. 

Aby przechwycić LUIS żądań i odpowiedzi, bot aplikacji sieci web musi **[usługi Application Insights](https://www.npmjs.com/package/applicationinsights)** pakietu NPM zainstalowany i skonfigurowany w **app.js** pliku. Następnie konwersji okno obsługi musi wysłać LUIS żądań i odpowiedzi informacji do usługi Application Insights. 

1. W portalu Azure w usłudze bot aplikacji sieci web, wybierz **kompilacji** w obszarze **zarządzania Bot** sekcji. 

    ![Wyszukiwanie szczegółowych informacji z aplikacji](./media/luis-tutorial-appinsights/build.png)

2. Na nowej karcie przeglądarki zostanie otwarty Edytor usług z aplikacji. Wybierz nazwę aplikacji w górnym pasku, a następnie wybierz **Otwórz konsolę Kudu**. 

    ![Wyszukiwanie szczegółowych informacji z aplikacji](./media/luis-tutorial-appinsights/kudu-console.png)

3. W konsoli wprowadź następujące polecenie, aby zainstalować usługi Application Insights i pakiety podkreślenia:

    ```
    cd site\wwwroot && npm install applicationinsights && npm install underscore
    ```

    ![Wyszukiwanie szczegółowych informacji z aplikacji](./media/luis-tutorial-appinsights/npm-install.png)

    Poczekaj, aż pakietów do zainstalowania:

    ```
    luisbot@1.0.0 D:\home\site\wwwroot
    `-- applicationinsights@1.0.1 
      +-- diagnostic-channel@0.2.0 
      +-- diagnostic-channel-publishers@0.2.1 
      `-- zone.js@0.7.6 
    
    npm WARN luisbot@1.0.0 No repository field.
    luisbot@1.0.0 D:\home\site\wwwroot
    +-- botbuilder-azure@3.0.4
    | `-- azure-storage@1.4.0
    |   `-- underscore@1.4.4 
    `-- underscore@1.8.3 
    ```

    Po zakończeniu karcie kudu konsoli przeglądarki.

## <a name="capture-and-send-luis-query-results-to-application-insights"></a>Przechwytywanie i wysłać LUIS wyników zapytania do usługi Application Insights
1. Na karcie przeglądarki Edytor usług aplikacji, otwórz **app.js** pliku.

2. Dodaj następujące NPM biblioteki w obszarze istniejące `requires` wiersze:

   [!code-javascript[Add NPM packages to app.js](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/nodejs/app.js?range=12-16 "Add NPM packages to app.js")]

3. Utwórz obiekt Application Insights i ustawienie aplikacji sieci web aplikacji bot **BotDevInsightsKey**: 

   [!code-javascript[Create the Application Insights object](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/nodejs/app.js?range=68-80 "Create the Application Insights object")]

4. Dodaj **appInsightsLog** funkcji:

   [!code-javascript[Add the appInsightsLog function](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/nodejs/app.js?range=82-109 "Add the appInsightsLog function")]

    Ostatni wiersz w funkcji jest, gdzie dane są dodawane do usługi Application Insights. Nazwa zdarzenia jest **wyniki LUIS**, unikatową nazwę oprócz inne dane telemetryczne zebrane przez ten robot aplikacji sieci web. 

5. Użyj **appInsightsLog** funkcji. Można dodać do każdej opcji okna dialogowego:

   [!code-javascript[Use the appInsightsLog function](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/nodejs/app.js?range=117-118 "Use the appInsightsLog function")]

6. Aby przetestować robot aplikacji sieci web, należy użyć **testów w sieci Web rozmowę** funkcji. Ponieważ wszystkie prace nie znajduje się w usłudze Application Insights w odpowiedzi bot różnic powinna zostać wyświetlona.

## <a name="view-luis-entries-in-application-insights"></a>Wyświetlanie LUIS wpisów w usłudze Application Insights
Otwórz Application Insights, aby wyświetlić wpisy LUIS. 

1. W portalu, wybierz **wszystkie zasoby** następnie Filtruj według nazwy bot aplikacji sieci web. Kliknij zasób z typem **usługi Application Insights**. Ikona usługi Application Insights jest żarówka. 

    ![Wyszukiwanie szczegółowych informacji z aplikacji](./media/luis-tutorial-appinsights/search-for-app-insights.png)



2. Po otwarciu zasobu kliknij **wyszukiwania** ikonę lupy w prawym panelu. Nowy panel do prawej Wyświetla. W zależności od ilości danych telemetrycznych zostanie znaleziony, panelu może zająć chwilę do wyświetlenia. Wyszukaj `LUIS-results` i wprowadź na klawiaturze naciśnij klawisz. Lista jest zawężony tylko zapytania LUIS wyniki dodane w tym samouczku.

    ![Filtruj zależności](./media/luis-tutorial-appinsights/app-insights-filter.png)

3. Zaznacz górny wpis. Nowe okno wyświetla bardziej szczegółowe dane w tym dane niestandardowe dla zapytania LUIS-prawej. Dane obejmują zamiar górny, a jego wynik.

    ![Szczegóły zależności](./media/luis-tutorial-appinsights/app-insights-detail.png)

    Gdy wszystko będzie gotowe, wybierz początku prawej **X** aby powrócić do listy elementów zależności. 


> [!Tip]
> Jeśli chcesz zapisać na liście zależności i wrócić do niego później, kliknij polecenie **... Więcej** i kliknij przycisk **zapisywanie ulubionego elementu**.

## <a name="query-application-insights-for-intent-score-and-utterance"></a>Zapytanie usługi Application Insights zamiar, oceny i utterance
Usługi Application Insights daje uprawnienia do wykonywania zapytań o dane z [Kusto](https://docs.microsoft.com/azure/application-insights/app-insights-analytics#query-data-in-analytics) języka, jak również eksportu do [PowerBI](https://powerbi.microsoft.com). 

1. Polecenie **Analytics** w górnej części zależności wyświetlania powyżej pola filtru. 

    ![Przycisk Analiza](./media/luis-tutorial-appinsights/analytics-button.png)

2. Otwiera nowe okno z u góry okna zapytania i okno tabeli danych poniżej. Jeśli używasz bazy danych przed to rozmieszczenie jest znana. Zapytanie zawiera wszystkie elementy z ostatnich 24 godzin rozpoczynające się od nazwy `LUIS-results`. **CustomDimensions** kolumna ma LUIS wyniki zapytania jako pary nazwa/wartość.

    ![Okno kwerendy analityka](./media/luis-tutorial-appinsights/analytics-query-window.png)

3. Aby wysunąć zamiar top, oceny i utterance, Dodaj następujący ostatnim wierszu powyżej w oknie zapytania:

    ```SQL
    | extend topIntent = tostring(customDimensions.LUIS_intent_intent)
    | extend score = todouble(customDimensions.LUIS_intent_score)
    | extend utterance = tostring(customDimensions.LUIS_text)
    ```

4. Uruchom zapytanie. Przewiń do prawej w tabeli danych. Dostępne są nowe kolumny topIntent, oceny i utterance. Kliknij kolumnę topIntent do sortowania.

    ![Celem top analityka](./media/luis-tutorial-appinsights/app-insights-top-intent.png)


Dowiedz się więcej o [język zapytań Kusto](https://docs.loganalytics.io/docs/Learn/Getting-Started/Getting-started-with-queries) lub [eksportować dane do usługi Power BI](https://docs.microsoft.com/azure/application-insights/app-insights-export-power-bi). 

## <a name="next-steps"></a>Kolejne kroki

Inne informacje, które chcesz dodać do dane usługi application insights zawiera identyfikator aplikacji, identyfikator wersji, Data ostatniej zmiany modelu, Data ostatniej pociągu, Data ostatniej publikacji. Te wartości można pobrać albo z adresu URL punktu końcowego (identyfikator aplikacji i identyfikator wersji) lub [tworzenia interfejsu API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c3d) połączenia, a następnie w ustawieniach bot aplikacji sieci web i pobierane z tego miejsca.  

Korzystając z tej samej subskrypcji punktu końcowego dla więcej niż jedną aplikację LUIS, należy także uwzględnić identyfikator subskrypcji i właściwości informujące o tym, że klucza wspólnego. 

> [!div class="nextstepaction"]
> [Dowiedz się więcej o zniesławiających przykład](luis-how-to-add-example-utterances.md)
