---
title: Application Insights przy użyciuC#
titleSuffix: Azure Cognitive Services
description: Twórz Boty zintegrowana z usługą aplikacji LUIS i Application Insights przy użyciu języka C#.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 06/11/2019
ms.author: diberry
ms.openlocfilehash: 6dbaa24df8b2917dd3f68d3851ca4662554ad00a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67053270"
---
# <a name="add-luis-results-to-application-insights-with-a-bot-in-c"></a>Dodawanie usługi LUIS wyniki do usługi Application Insights z botem wC#

W tym samouczku dodaje informacje o odpowiedzi usługi LUIS do [usługi Application Insights](https://azure.microsoft.com/services/application-insights/) magazyn danych telemetrycznych. Po utworzeniu tych danych, można tworzyć zapytania po przy użyciu języka Kusto lub Power BI, aby analizować, agregowania i tworzyć raporty dotyczące intencje i podmioty wypowiedź w czasie rzeczywistym. Ta analiza pomaga określić, jeśli Dodawanie lub edytowanie intencje i podmioty aplikacją usługi LUIS.

Bot został utworzony za pomocą platformy Bot Framework 4.x i bot aplikacji sieci Web platformy Azure. A [platformy Bot Framework 4.x, za pomocą usługi LUIS samouczka](luis-csharp-tutorial-bf-v4.md) jest również dostępna.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Dodaj usługę Application Insights do bot aplikacji sieci web
> * Przechwytywane i wysyłane do usługi LUIS wyników zapytania do usługi Application Insights
> * Zapytanie usługi Application Insights dla najważniejszych przeznaczenie, wynik i wypowiedź

## <a name="prerequisites"></a>Wymagania wstępne

* Bot aplikacji sieci web usługi LUIS z **tutorial](luis-csharp-tutorial-bf-v4.md)** za pomocą usługi Application Insights włączona.
* [Program Visual Studio 2017](https://www.visualstudio.com/downloads/) zainstalowane lokalnie na komputerze.

> [!Tip]
> Jeśli nie masz już subskrypcję, możesz zarejestrować [bezpłatne konto](https://azure.microsoft.com/free/).

Cały kod w tym samouczku jest dostępny na [repozytorium GitHub Azure-Samples](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/tutorial-web-app-bot-application-insights/csharp) i każdy wiersz skojarzony z tym samouczkiem jest ujęty w za pomocą `//LUIS Tutorial:`.

## <a name="review-luis-web-app-bot"></a>Przejrzyj bot aplikacji sieci web usługi LUIS

W tym samouczku założono, masz kod, który wygląda podobnie do następujących, lub że zostały wykonane [innym samouczku](luis-csharp-tutorial-bf-v4.md):

   [!code-csharp[Web app bot with LUIS](~/samples-luis/documentation-samples/tutorial-web-app-bot/csharp/BasicLuisDialog.cs "Web app bot with LUIS")]

## <a name="application-insights-in-web-app-bot"></a>Usługa Application Insights w sieci web Apps

Obecnie dodano jako część tworzenia usługi bot aplikacji sieci web, usługę Application Insights gromadzi dane telemetryczne ogólny stan, dla bota. Nie są zbierane informacje o odpowiedzi usługi LUIS. W celu analizowania i ulepszania usługi LUIS, potrzebne informacje o odpowiedzi usługi LUIS.  

Przechwytywanie odpowiedzi usługi LUIS, bot aplikacji sieci web musi **[usługi Application Insights](https://www.nuget.org/packages/Microsoft.ApplicationInsights/)** zainstalowany i skonfigurowany dla projektu.

## <a name="download-web-app-bot"></a>Pobierz bot aplikacji sieci web

Użyj [programu Visual Studio 2017](https://www.visualstudio.com/downloads/) dodać i skonfigurować usługę Application Insights dla bota aplikacji sieci web. Aby można było używać bot aplikacji sieci web w programie Visual Studio, Pobierz program code bot aplikacji sieci web.

1. W witrynie Azure portal, bot aplikacji sieci web, wybierz **kompilacji**.

    ![Wybieranie kompilacji w portalu](./media/luis-tutorial-bot-csharp-appinsights/download-build-menu.png)

2. Wybierz **pobrany plik zip** i zaczekaj, aż plik jest gotowa.

    ![Pobierz plik zip](./media/luis-tutorial-bot-csharp-appinsights/download-link.png)

3. Wybierz **pobrany plik zip** w oknie podręcznym. Zapamiętaj lokalizację na komputerze, będzie on potrzebny w następnej sekcji.

    ![Pobierz plik zip — menu podręczne](./media/luis-tutorial-bot-csharp-appinsights/download-popup.png)

## <a name="open-solution-in-visual-studio-2017"></a>Otwórz rozwiązanie w programie Visual Studio 2017

1. Wyodrębnij plik do folderu.

2. Otwórz program Visual Studio 2017, a następnie otwórz plik rozwiązania `Microsoft.Bot.Sample.LuisBot.sln`. Jeśli pojawi się ostrzeżenie o zabezpieczeniach, wybierz pozycję "OK".

    ![Otwórz rozwiązanie w programie Visual Studio 2017](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-security-warning.png)

3. Program Visual Studio wymaga dodać zależności w rozwiązaniu. W **Eksploratora rozwiązań**, kliknij prawym przyciskiem myszy **odwołania**i wybierz **Zarządzaj pakietami NuGet...** .

    ![Zarządzanie pakietami NuGet](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-manage-nuget-packages.png)

4. Menedżer pakietów NuGet pokazuje listę zainstalowanych pakietów. Wybierz **przywrócić** żółty pasek. Poczekaj, aż do zakończenia procesu przywracania.

    ![Przywracanie pakietów NuGet](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-restore-packages.png)

## <a name="add-application-insights-to-the-project"></a>Dodaj usługę Application Insights do projektu

Instalowanie i konfigurowanie usługi Application Insights w programie Visual Studio.

1. W programie Visual Studio 2017 w górnym menu wybierz **projektu**, a następnie wybierz **Dodaj Telemetrię usługi Application Insights...** .

2. W **Konfiguracja usługi Application Insights** wybierz **Rozpocznij za darmo**

    ![Rozpocząć konfigurowanie usługi Application Insights](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-configure-app-insights.png)

3. Zarejestruj swoją aplikację za pomocą usługi Application Insights. Może być konieczne wprowadź poświadczenia platformy Azure w portalu.

4. Visual Studio dodaje usługi Application Insights do projektu, wyświetlanie stanu, ponieważ jest to realizowane.

    ![Stanu usługi Application Insights](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-adding-application-insights-to-project.png)

    Po zakończeniu tego procesu **Konfiguracja usługi Application Insights** Wyświetla stan postępu.

    ![Postęp stanu usługi Application Insights](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-configured-application-insights-to-project.png)

    **Włączenia zbierania śladów** ma kolor czerwony, co oznacza, nie jest włączona. Ten samouczek nie korzysta z tej funkcji.

## <a name="build-and-resolve-errors"></a>Tworzenie i usuwanie błędów

1. Skompiluj rozwiązanie, wybierając **kompilacji** menu, następnie wybierz pozycję **Kompiluj rozwiązanie**. Poczekaj na zakończenie kompilacji.

2. Jeśli kompilacja zakończy się niepowodzeniem z `CS0104` błędów, należy je naprawić. W `Controllers` folder, w `MessagesController.cs file`, rozwiązywanie niejednoznacznych użycie `Activity` typ przez dodanie przedrostka typ działania typu łącznika. Aby to zrobić, należy zmienić nazwę `Activity` w wierszach, 22 i 36 z `Activity` do `Connector.Activity`. Ponownie skompiluj rozwiązanie. Powinna istnieć nie więcej błędów kompilacji.

    Jest pełną tego pliku:

    [!code-csharp[MessagesController.cs file](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/csharp/MessagesController.cs "MessagesController.cs file")]

## <a name="publish-project-to-azure"></a>Publikowanie projektu na platformie Azure

**Usługi Application Insights** pakietu znajduje się teraz w projekcie i poprawnie skonfigurowane dla poświadczenia w witrynie Azure portal. Zmiany w projekcie trzeba można opublikować platformy Azure.

1. W **Eksploratora rozwiązań**, kliknij prawym przyciskiem myszy nazwę projektu, a następnie wybierz **Publikuj**.

    ![Opublikuj projekt portalu](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-publish.png)

2. W **Publikuj** wybierz **Utwórz nowy profil**.

    ![W ramach publikowania Utwórz nowy profil.](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-publish-1.png)

3. Wybierz **Importuj profil**i wybierz **OK**.

    ![W ramach publikowania Importuj profil](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-publish-2.png)

4. W **Importuj plik ustawień publikowania** systemu windows, przejdź do folderu projektu, przejdź do folderu `PostDeployScripts` folderu, wybierz plik, który kończy się `.PublishSettings`i wybierz `Open`. Skonfigurowano publikowania dla tego projektu.

5. Publikowanie kodu źródłowego lokalnej usługi Bot Service, wybierając **Publikuj** przycisku. **Dane wyjściowe** okno pokazuje stan. Pozostała część tego samouczka jest wykonywane w witrynie Azure portal. Zamknij program Visual Studio 2017.

## <a name="open-three-browser-tabs"></a>Otwórz trzech kartach przeglądarki

W witrynie Azure portal Znajdź bot aplikacji sieci web, a następnie otwórz go. Użyto trzy różne widoki bot aplikacji sieci web. Być może łatwiej będzie mieć trzy osobnych kartach w przeglądarce:
  
>  * Testowanie w rozmów w sieci Web
>  * Edytor kodu online kompilacji/Otwórz-> Edytor usługi App Service
>  * Za pomocą konsoli Kudu edytora/Otwórz usługę aplikacji -> konsoli diagnostyki

## <a name="modify-basicluisdialogcs-code"></a>Modyfikowanie kodu BasicLuisDialog.cs

1. W **Edytor usługi App Service** karty przeglądarki, otwórz `BasicLuisDialog.cs` pliku.

2. Dodaj następującą zależność NuGet w ramach istniejącego `using` wiersze:

   [!code-csharp[Add using statement](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/csharp/BasicLuisDialog.cs?range=11-12 "Add using statement")]

3. Dodaj `LogToApplicationInsights` funkcji:

   [!code-csharp[Add the LogToApplicationInsights function](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/csharp/BasicLuisDialog.cs?range=61-92 "Add the LogToApplicationInsights function")]

    Klucz Instrumentacji usługi Application Insights jest już bot aplikacji sieci web aplikacji, ustawienie o nazwie `BotDevInsightsKey`.

    Ostatni wiersz funkcja dodaje dane do usługi Application Insights. Nazwa śledzenia jest `LUIS`, unikatową nazwę, oprócz innych danych telemetrycznych zebranych przez ten bot aplikacji sieci web. Wszystkie właściwości również mają prefiks `LUIS_` , dzięki czemu można sprawdzić, jakie dane w tym samouczku dodaje w porównaniu do informacji, które jest nadawana przez bota aplikacji sieci web.

4. Wywołaj `LogToApplicationInsights` funkcji w górnej części `ShowLuisResult` funkcji:

   [!code-csharp[Use the LogToApplicationInsights function](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/csharp/BasicLuisDialog.cs?range=114-115 "Use the LogToApplicationInsights function")]

## <a name="build-web-app-bot"></a>Tworzenie bota aplikacji sieci web

1. Tworzenie bota aplikacji sieci web na jeden z dwóch sposobów. Pierwszy sposób polega na kliknij prawym przyciskiem myszy `build.cmd` w **Edytor usługi App Service**, a następnie wybierz **uruchamiane z poziomu konsoli**. Wyświetla dane wyjściowe konsoli i kończy się `Finished successfully.`

2. Jeśli to nie zakończy się pomyślnie, należy otworzyć konsolę, przejdź do skryptu i uruchom go wykonując następujące kroki. W **Edytor usługi App Service**, na górnym pasku niebieski, wybierz nazwę bota, a następnie wybierz **otwartej konsoli Kudu** na liście rozwijanej.

    ![Konsola Kudu Otwórz](./media/luis-tutorial-bot-csharp-appinsights/open-kudu-console.png)

3. W oknie konsoli wprowadź następujące polecenie:

    ```console
    cd site\wwwroot && build.cmd
    ```

    Zaczekaj na zakończenie z kompilacji `Finished successfully.`

## <a name="test-the-web-app-bot"></a>Testowanie bot aplikacji sieci web

1. Aby przetestować bota aplikacji sieci web, otwórz **testowania w czatów internetowych** funkcji w portalu.

2. Wprowadź frazę `Coffee bar on please`.  

    ![Testowanie bot aplikacji sieci web w rozmowy](./media/luis-tutorial-bot-csharp-appinsights/test-in-web-chat.png)

3. Powinien zostać wyświetlony żadnej różnicy w odpowiedzi chatbot. Zmiana wysyła dane do usługi Application Insights nie znajduje się w bot odpowiedzi. Wprowadź kilka wypowiedzi więcej, więc ma nieco większej ilości danych w usłudze Application Insights:

|Wypowiedzi|
|--|
|Należy dostarczyć pizza|
|Wyłącz wszystkie światła|
|Włącz światła hall|


## <a name="view-luis-entries-in-application-insights"></a>Wyświetlanie usługi LUIS wpisów w usłudze Application Insights

Otwórz usługę Application Insights, aby wyświetlić wpisy usługi LUIS.

1. W portalu, wybierz **wszystkie zasoby** następnie Filtruj według nazwy bot aplikacji sieci web. Kliknij zasób z typem **usługi Application Insights**. Ikona usługi Application Insights jest żarówki.

    ![Wyszukiwanie usługi app insights w witrynie Azure portal](./media/luis-tutorial-bot-csharp-appinsights/portal-service-list-app-insights.png)

2. Po otwarciu zasobu kliknij **wyszukiwania** ikonę lupy w prawym panelu. Nowy panel do wyświetla odpowiednie. W zależności od ilości danych telemetrycznych zostanie znaleziony, zespół może potrwać chwilę, aby wyświetlić. Wyszukaj `LUIS`. Lista jest zawężony do właśnie takie wyniki zapytania usługi LUIS, dodane za pomocą tego samouczka.

    ![Wyszukiwanie danych śledzenia](./media/luis-tutorial-bot-csharp-appinsights/portal-service-list-app-insights-search-luis-trace.png)

3. Zaznacz górny wpis. Nowe okno zawiera bardziej szczegółowe dane, w tym niestandardowe dane dla zapytania usługi LUIS-prawej. Dane obejmują najważniejsze intencji i jego wynik.

    ![Element śledzenia przeglądu](./media/luis-tutorial-bot-csharp-appinsights/portal-service-list-app-insights-search-luis-trace-item.png)

    Gdy wszystko będzie gotowe, wybierz najwyższy skrajna prawa **X** aby powrócić do listy elementów zależności.

> [!Tip]
> Jeśli chcesz zapisać listę zależności i wrócić do niego później, kliknij polecenie **... Więcej** i kliknij przycisk **Save ulubionych**.

## <a name="query-application-insights-for-intent-score-and-utterance"></a>Zapytanie usługi Application Insights dla przeznaczenie, ocena i wypowiedź

Usługa Application Insights daje uprawnienia do wykonywania zapytań o dane za pomocą [Kusto](https://docs.microsoft.com/azure/application-insights/app-insights-analytics#query-data-in-analytics) języka, jak eksportu do [usługi Power BI](https://powerbi.microsoft.com).

1. Kliknij pozycję **Analytics** w górnej części zależności ofercie powyżej pola filtru.

    ![Przycisk Analiza](./media/luis-tutorial-bot-csharp-appinsights/portal-service-list-app-insights-search-luis-analytics-button.png)

2. Nowe okno zostanie otwarte okno zapytania u góry i oknem danych tabeli poniżej. Jeśli używano wcześniej bazy danych to rozwiązanie jest znana. Zapytanie zawiera wszystkie elementy z ostatnich 24 godzinach od o nazwie `LUIS`. **Tabeli CustomDimensions** kolumna ma wyniki zapytania usługi LUIS jako pary nazwa/wartość.

    ![Raport analizy domyślne](./media/luis-tutorial-bot-csharp-appinsights/analytics-query-1.png)

3. Aby wyciągnąć z góry przeznaczenie, ocena i wypowiedź, dodaj następującą tuż nad ostatni wiersz w oknie zapytania:

    ```kusto
    | extend topIntent = tostring(customDimensions.LUIS_topScoringIntent)
    | extend score = todouble(customDimensions.LUIS_topScoringIntentScore)
    | extend utterance = tostring(customDimensions.LUIS_query)
    ```

4. Uruchom zapytanie. Przewiń do prawej w tabeli danych. Dostępne są nowe kolumny topIntent, ocena i wypowiedź. Kliknij kolumnę topIntent i sortowania.

    ![Raport analizy niestandardowe](./media/luis-tutorial-bot-csharp-appinsights/analytics-query-2.png)

Dowiedz się więcej o [język zapytania Kusto](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries) lub [dane są eksportowane do usługi Power BI](https://docs.microsoft.com/azure/application-insights/app-insights-export-power-bi).

## <a name="learn-more-about-bot-framework"></a>Dowiedz się więcej na temat struktury Bot Framework

Dowiedz się więcej o [platformy Bot Framework](https://dev.botframework.com/).

## <a name="next-steps"></a>Kolejne kroki

Inne informacje, które chcesz dodać do aplikacji danych szczegółowych informacji zawiera identyfikator aplikacji, identyfikator wersji, Data ostatniej zmiany modelu, Data ostatniego szkolenie, Data ostatniej publikacji. Te wartości mogą być pobierane albo z adresu URL punktu końcowego (identyfikator aplikacji i identyfikator wersji) lub [tworzenia interfejsu API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c3d) wywołań, a następnie w oknie Ustawienia bot aplikacji sieci web i pobierane z tego miejsca.  

Jeśli używasz tej samej subskrypcji punktu końcowego dla więcej niż jedną aplikacją usługi LUIS, powinny również obejmować identyfikator subskrypcji i właściwości z informacją, że jest on klucza wstępnego.

> [!div class="nextstepaction"]
> [Dowiedz się więcej o przykład wypowiedzi](luis-how-to-add-example-utterances.md)
