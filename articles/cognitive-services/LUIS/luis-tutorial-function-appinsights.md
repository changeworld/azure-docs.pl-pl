---
title: Usługa Application Insights, środowiska Node.js
titleSuffix: Azure Cognitive Services
description: Twórz Boty zintegrowana z usługą aplikacji LUIS i przy użyciu środowiska Node.js w usłudze Application Insights.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 05/30/2019
ms.author: diberry
ms.openlocfilehash: bde1983f89cb2fcd0a6fddadc2c3379dee4310be
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/30/2019
ms.locfileid: "66399628"
---
# <a name="add-luis-results-to-application-insights-and-azure-functions"></a>Dodawanie usługi LUIS wyniki do usługi Application Insights i Azure functions
W tym samouczku dodaje informacje do żądania i odpowiedzi usługi LUIS do [usługi Application Insights](https://azure.microsoft.com/services/application-insights/) magazyn danych telemetrycznych. Po utworzeniu tych danych, można tworzyć zapytania po przy użyciu języka Kusto lub Power BI, aby analizować, agregowania i tworzyć raporty dotyczące intencje i podmioty wypowiedź w czasie rzeczywistym. Ta analiza pomaga określić, jeśli Dodawanie lub edytowanie intencje i podmioty aplikacją usługi LUIS.

Bot został utworzony za pomocą platformy Bot Framework bota aplikacji sieci Web platformy Azure i 3.x. A [platformy Bot Framework 4.x, za pomocą usługi LUIS samouczka](luis-nodejs-tutorial-bf-v4.md) jest również dostępna.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Dodaj bibliotekę usługi Application Insights do bota aplikacji sieci web
> * Przechwytywane i wysyłane do usługi LUIS wyników zapytania do usługi Application Insights
> * Zapytanie usługi Application Insights dla najważniejszych przeznaczenie, wynik i wypowiedź

## <a name="prerequisites"></a>Wymagania wstępne

* Twój bot aplikacji sieci web usługi LUIS z **[poprzedniego samouczka](luis-nodejs-tutorial-build-bot-framework-sample.md)** za pomocą usługi Application Insights włączona. 

> [!Tip]
> Jeśli nie masz już subskrypcję, możesz zarejestrować [bezpłatne konto](https://azure.microsoft.com/free/).

Cały kod w tym samouczku jest dostępny na [repozytorium GitHub Azure-Samples](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/tutorial-web-app-bot-application-insights/nodejs) i każdy wiersz skojarzony z tym samouczkiem jest ujęty w za pomocą `//APPINSIGHT:`. 

## <a name="web-app-bot-with-luis"></a>Bot aplikacji sieci Web z użyciem usługi LUIS
W tym samouczku założono, masz kod, który wygląda podobnie do następujących, lub że zostały wykonane [innym samouczku](luis-nodejs-tutorial-build-bot-framework-sample.md): 

   [!code-javascript[Web app bot with LUIS](~/samples-luis/documentation-samples/tutorial-web-app-bot/nodejs/app.js "Web app bot with LUIS")]

## <a name="add-application-insights-library-to-web-app-bot"></a>Dodaj bibliotekę usługi Application Insights do bota aplikacji sieci web
Obecnie usługi Application Insights, używane w tym web Apps umożliwia zbieranie informacji o ogólnym stanem telemetria dla bota. Nie są zbierane informacje żądania i odpowiedzi usługi LUIS, które należy sprawdzić i poprawić Twoje intencje i podmioty. 

Przechwytywanie LUIS żądania i odpowiedzi, bot aplikacji sieci web musi **[usługi Application Insights](https://www.npmjs.com/package/applicationinsights)** pakietu NPM zainstalować i skonfigurować w **app.js** pliku. Następnie obsługi konwersji okna dialogowego należy wysłać informacje żądania i odpowiedzi usługi LUIS do usługi Application Insights. 

1. W witrynie Azure portal, usługę sieci web aplikacji bot wybierz **kompilacji** w obszarze **zarządzania Bot** sekcji. 

    ![W witrynie Azure portal usługi bot aplikacji sieci web wybierz pozycję "Kompilacja" w sekcji "Zarządzanie Bot".](./media/luis-tutorial-appinsights/build.png)

2. Nowa karta przeglądarki zostanie otwarty przy użyciu edytorze usługi App Service. Wybierz nazwę aplikacji na górnym pasku, a następnie wybierz **otwartej konsoli Kudu**. 

    ![Wybierz nazwę aplikacji na górnym pasku, a następnie wybierz pozycję "Otwórz Konsola Kudu".](./media/luis-tutorial-appinsights/kudu-console.png)

3. W konsoli wprowadź następujące polecenie, aby zainstalować usługi Application Insights i pakietów podkreślenia:

    ```console
    cd site\wwwroot && npm install applicationinsights && npm install underscore
    ```

    ![Użyj polecenia npm, aby zainstalować usługi Application Insights i pakietów podkreślenia](./media/luis-tutorial-appinsights/npm-install.png)

    Poczekaj, aż pakiety do zainstalowania:

    ```console
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

    Po zakończeniu karta przeglądarki Konsola kudu.

## <a name="capture-and-send-luis-query-results-to-application-insights"></a>Przechwytywane i wysyłane do usługi LUIS wyników zapytania do usługi Application Insights
1. Edytor usługi App Service karty przeglądarki, otwórz **app.js** pliku.

2. Dodaj następujące biblioteki NPM w ramach istniejącego `requires` wiersze:

   [!code-javascript[Add NPM packages to app.js](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/nodejs/app.js?range=12-16 "Add NPM packages to app.js")]

3. Utwórz obiekt usługi Application Insights, a następnie użyć ustawienia aplikacji sieci web aplikacji bot **BotDevInsightsKey**: 

   [!code-javascript[Create the Application Insights object](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/nodejs/app.js?range=68-80 "Create the Application Insights object")]

4. Dodaj **appInsightsLog** funkcji:

   [!code-javascript[Add the appInsightsLog function](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/nodejs/app.js?range=82-109 "Add the appInsightsLog function")]

    Ostatni wiersz w funkcji jest, gdzie dane są dodawane do usługi Application Insights. Nazwa zdarzenia jest **wyniki usługi LUIS**, unikatową nazwę, oprócz innych danych telemetrycznych zebranych przez ten bot aplikacji sieci web. 

5. Użyj **appInsightsLog** funkcji. Możesz dodać go do każdego elementu intent okno dialogowe:

   [!code-javascript[Use the appInsightsLog function](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/nodejs/app.js?range=117-118 "Use the appInsightsLog function")]

6. Aby przetestować bota aplikacji sieci web, użyj **testowania w czatów internetowych** funkcji. Żadnej różnicy powinny być widoczne, ponieważ cała praca nie znajduje się w usłudze Application Insights w odpowiedzi botów.

## <a name="view-luis-entries-in-application-insights"></a>Wyświetlanie usługi LUIS wpisów w usłudze Application Insights
Otwórz usługę Application Insights, aby wyświetlić wpisy usługi LUIS. 

1. W portalu, wybierz **wszystkie zasoby** następnie Filtruj według nazwy bot aplikacji sieci web. Kliknij zasób z typem **usługi Application Insights**. Ikona usługi Application Insights jest żarówki. 

    ! [[Wyszukiwania dla usługi app insights w witrynie Azure portal](./media/luis-tutorial-appinsights/search-for-app-insights.png)

2. Po otwarciu zasobu kliknij **wyszukiwania** ikonę lupy w prawym panelu. Nowy panel do wyświetla odpowiednie. W zależności od ilości danych telemetrycznych zostanie znaleziony, zespół może potrwać chwilę, aby wyświetlić. Wyszukaj `LUIS-results` i naciśnij klawisz na klawiaturze, należy wprowadzić. Lista jest zawężony do właśnie takie wyniki zapytania usługi LUIS, dodane za pomocą tego samouczka.

    ![Filtruj do zależności](./media/luis-tutorial-appinsights/app-insights-filter.png)

3. Zaznacz górny wpis. Nowe okno zawiera bardziej szczegółowe dane, w tym niestandardowe dane dla zapytania usługi LUIS-prawej. Dane obejmują najważniejsze intencji i jego wynik.

    ![Szczegóły zależności](./media/luis-tutorial-appinsights/app-insights-detail.png)

    Gdy wszystko będzie gotowe, wybierz najwyższy skrajna prawa **X** aby powrócić do listy elementów zależności. 


> [!Tip]
> Jeśli chcesz zapisać listę zależności i wrócić do niego później, kliknij polecenie **... Więcej** i kliknij przycisk **Save ulubionych**.

## <a name="query-application-insights-for-intent-score-and-utterance"></a>Zapytanie usługi Application Insights dla przeznaczenie, ocena i wypowiedź
Usługa Application Insights daje uprawnienia do wykonywania zapytań o dane za pomocą [Kusto](https://docs.microsoft.com/azure/application-insights/app-insights-analytics#query-data-in-analytics) języka, jak eksportu do [usługi Power BI](https://powerbi.microsoft.com). 

1. Kliknij pozycję **Analytics** w górnej części zależności ofercie powyżej pola filtru. 

    ![Przycisk Analiza](./media/luis-tutorial-appinsights/analytics-button.png)

2. Nowe okno zostanie otwarte okno zapytania u góry i oknem danych tabeli poniżej. Jeśli używano wcześniej bazy danych to rozwiązanie jest znana. Zapytanie zawiera wszystkie elementy z ostatnich 24 godzinach od o nazwie `LUIS-results`. **Tabeli CustomDimensions** kolumna ma wyniki zapytania usługi LUIS jako pary nazwa/wartość.

    ![Okno zapytania usługi Analytics](./media/luis-tutorial-appinsights/analytics-query-window.png)

3. Aby wyciągnąć z góry przeznaczenie, ocena i wypowiedź, dodaj następującą tuż nad ostatni wiersz w oknie zapytania:

    ```kusto
    | extend topIntent = tostring(customDimensions.LUIS_intent_intent)
    | extend score = todouble(customDimensions.LUIS_intent_score)
    | extend utterance = tostring(customDimensions.LUIS_text)
    ```

4. Uruchom zapytanie. Przewiń do prawej w tabeli danych. Dostępne są nowe kolumny topIntent, ocena i wypowiedź. Kliknij kolumnę topIntent i sortowania.

    ![Najważniejsze celem analizy](./media/luis-tutorial-appinsights/app-insights-top-intent.png)


Dowiedz się więcej o [język zapytania Kusto](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries) lub [dane są eksportowane do usługi Power BI](https://docs.microsoft.com/azure/application-insights/app-insights-export-power-bi). 

## <a name="next-steps"></a>Kolejne kroki

Inne informacje, które chcesz dodać do aplikacji danych szczegółowych informacji zawiera identyfikator aplikacji, identyfikator wersji, Data ostatniej zmiany modelu, Data ostatniego szkolenie, Data ostatniej publikacji. Te wartości mogą być pobierane albo z adresu URL punktu końcowego (identyfikator aplikacji i identyfikator wersji) lub [tworzenia interfejsu API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c3d) wywołań, a następnie w oknie Ustawienia bot aplikacji sieci web i pobierane z tego miejsca.  

Jeśli używasz tej samej subskrypcji punktu końcowego dla więcej niż jedną aplikacją usługi LUIS, powinny również obejmować identyfikator subskrypcji i właściwości z informacją, że jest on klucza wstępnego. 

> [!div class="nextstepaction"]
> [Dowiedz się więcej o przykład wypowiedzi](luis-how-to-add-example-utterances.md)
