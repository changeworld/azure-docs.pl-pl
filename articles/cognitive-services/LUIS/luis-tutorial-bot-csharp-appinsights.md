---
title: Dodawanie LUIS danych do usługi Application Insights przy użyciu języka C# | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Tworzenie robotów zintegrowane z usługą LUIS aplikacji i usługi Application Insights przy użyciu języka C#.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/07/2018
ms.author: v-geberr
ms.openlocfilehash: 52b6ae224b0e8da12eb4903f5100a6e5cc39704d
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2018
ms.locfileid: "35356124"
---
# <a name="add-luis-results-to-application-insights-from-a-web-app-bot"></a>Dodaj LUIS wyniki do usługi Application Insights z bot aplikacji sieci web
W tym samouczku dodaje informacje o odpowiedzi LUIS do [usługi Application Insights](https://azure.microsoft.com/services/application-insights/) magazynu danych telemetrycznych. Po określeniu tych danych można wykonać zapytanie po Kusto języka lub usługi Power BI do analizowania, agregacji i tworzenia raportów dotyczących intencje i jednostek utterance w czasie rzeczywistym. Pomaga to analizy okaże się, jeśli Dodawanie lub edytowanie intencje i jednostek aplikacji LUIS.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
* Dodawanie usługi Application Insights do robot aplikacji sieci web
* Przechwytywanie i wysłać LUIS wyników zapytania do usługi Application Insights
* Celem top, oceny i utterance wykonanie kwerendy usługi Application Insights

## <a name="prerequisites"></a>Wymagania wstępne

* Bot aplikacji sieci web LUIS, tak z **[poprzedniego samouczek](luis-csharp-tutorial-build-bot-framework-sample.md)** z usługi Application Insights włączona. 
* [Visual Studio 2017](https://www.visualstudio.com/downloads/) zainstalowane lokalnie na komputerze.

> [!Tip]
> Jeśli nie masz już subskrypcję, możesz zarejestrować dla [bezpłatne konto](https://azure.microsoft.com/free/).

Cały kod z tego samouczka jest dostępna w [przykłady LUIS repozytorium github](https://github.com/Microsoft/LUIS-Samples/tree/master/documentation-samples/tutorial-web-app-bot-application-insights/csharp) oraz każdy wiersz skojarzony z tego samouczka jest oznaczone jako z `//LUIS Tutorial:`. 

## <a name="review-luis-web-app-bot"></a>Przejrzyj bot aplikacji sieci web LUIS
Ten samouczek zakłada kod, że wygląda podobnie do następującego lub że zostały wykonane [innych samouczek](luis-csharp-tutorial-build-bot-framework-sample.md): 

   [!code-csharp[Web app bot with LUIS](~/samples-luis/documentation-samples/tutorial-web-app-bot/csharp/BasicLuisDialog.cs "Web app bot with LUIS")]

## <a name="application-insights-in-web-app-bot"></a>Usługa Application Insights w bot aplikacji sieci web
Obecnie usługa Application Insights w ramach tworzenia usługi bot aplikacji sieci web, dodać zbiera dane telemetryczne ogólnego stanu dla bot. Nie zbiera informacje o LUIS odpowiedzi. Do analizowania i usprawniania LUIS, wymagane są informacje o odpowiedzi LUIS.  

Aby przechwycić odpowiedzi LUIS, bot aplikacji sieci web musi **[usługi Application Insights](https://www.nuget.org/packages/Microsoft.ApplicationInsights/)** zainstalowany i skonfigurowany dla projektu. 

## <a name="download-web-app-bot"></a>Pobierz bot aplikacji sieci web
Użyj [programu Visual Studio 2017](https://www.visualstudio.com/downloads/) Aby dodać i skonfigurować usługi Application Insights dla bot aplikacji sieci web. Aby można było używać bot aplikacji sieci web w programie Visual Studio, Pobierz kod bot aplikacji sieci web.

1. W portalu Azure, bot aplikacji sieci web, wybierz **kompilacji**.

    ![Wybieranie kompilacji w portalu](./media/luis-tutorial-bot-csharp-appinsights/download-build-menu.png)

2. Wybierz **pobierania pliku zip** i zaczekaj, aż plik jest gotowy.

    ![Pobierz plik zip](./media/luis-tutorial-bot-csharp-appinsights/download-link.png)

3. Wybierz **pobierania pliku zip** w oknie podręcznym. Zapamiętaj lokalizację na komputerze, będzie potrzebny w następnej sekcji.

    ![Pobierz plik zip — menu podręczne](./media/luis-tutorial-bot-csharp-appinsights/download-popup.png)

## <a name="open-solution-in-visual-studio-2017"></a>Otwórz rozwiązanie w Visual Studio 2017 r.

1. Wyodrębnij plik do folderu. 

2. Otwórz program Visual Studio 2017 i Otwórz plik rozwiązania `Microsoft.Bot.Sample.LuisBot.sln`. Jeśli na wyskakującej ostrzeżenie o zabezpieczeniach, wybierz "OK".

    ![Otwórz rozwiązanie w Visual Studio 2017 r.](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-security-warning.png)

3. Program Visual Studio musi dodać zależności do rozwiązania. W **Eksploratora rozwiązań**, kliknij prawym przyciskiem myszy **odwołania**i wybierz **Zarządzaj pakietami NuGet...** . 

    ![Zarządzanie pakietami NuGet](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-manage-nuget-packages.png)

4. Menedżer pakietów NuGet to lista zainstalowanych pakietów. Wybierz **przywrócić** żółty pasek. Poczekaj na zakończenie procesu przywracania.

    ![Przywracanie pakietów NuGet](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-restore-packages.png)

## <a name="add-application-insights-to-the-project"></a>Dodawanie usługi Application Insights do projektu
Instalowanie i konfigurowanie usługi Application Insights w programie Visual Studio. 

1. W programie Visual Studio 2017 r. w górnym menu, wybierz **projektu**, a następnie wybierz pozycję **Dodaj Telemetrię usługi Application Insights...** .

2. W **konfigurację aplikacji informacje na temat technologii** wybierz **Start bezpłatna**

    ![Rozpocząć konfigurowanie usługi Application Insights](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-configure-app-insights.png)

3. Zarejestrować aplikację w usłudze Application Insights. Może być konieczne wprowadzanie poświadczeń portalu Azure. 

4. Visual Studio dodaje usługi Application Insights do projektu, wyświetlanie stanu, jak robi to. 

    ![Application Insights stanu](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-adding-application-insights-to-project.png)

    Po zakończeniu tego procesu, **konfigurację aplikacji informacje na temat technologii** pokazuje stan postępu. 

    ![Application Insights postęp całej procedury](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-configured-application-insights-to-project.png)

    **Włączyć zbieranie śladów** czerwony, co oznacza nie jest włączona. Ten samouczek nie korzysta z funkcji. 

## <a name="build-and-resolve-errors"></a>Tworzenie i usuwanie błędów

1. Skompiluj rozwiązanie, wybierając **kompilacji** menu, następnie wybierz **Kompiluj ponownie rozwiązanie**. Poczekaj na zakończenie kompilacji. 

2. Jeśli kompilacja zakończy się niepowodzeniem z `CS0104` błędów, trzeba je usunąć. W `Controllers` folderu w `MessagesController.cs file`, napraw niejednoznaczne użycie `Activity` typu, dodając typ działania typu łącznika. Aby to zrobić, należy zmienić nazwę `Activity` w wierszach 22 i 36 z `Activity` do `Connector.Activity`. Ponownie skompiluj rozwiązanie. Powinna być już więcej błędy kompilacji.

    Pełne źródło tego pliku jest:

    [!code-csharp[MessagesController.cs file](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/csharp/MessagesController.cs "MessagesController.cs file")]

## <a name="publish-project-to-azure"></a>Publikowania projektu na platformę Azure
**Usługi Application Insights** pakietów jest teraz w projekcie i poprawnie skonfigurowany dla poświadczeń w portalu Azure. Zmiany w projekcie muszą być publikowane Azure.

1. W **Eksploratora rozwiązań**, kliknij prawym przyciskiem myszy nazwę projektu, a następnie wybierz **publikowania**.

    ![Publikowanie do portalu projektu](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-publish.png)

2. W **publikowania** wybierz **Utwórz nowy profil**.

    ![Publikowanie do portalu projektu](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-publish-1.png)

3. Wybierz **Importowanie profilu**i wybierz **OK**.

    ![Publikowanie do portalu projektu](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-publish-2.png)

4. W **pliku ustawień publikowania importu** systemu windows, przejdź do folderu projektu, przejdź do `PostDeployScripts` folderu, wybierz plik, który kończy się `.PublishSettings`i wybierz `Open`. Publikowanie dla tego projektu został skonfigurowany. 

5. Opublikowania użytkownika lokalnego kodu źródłowego w usłudze Bot wybierając **publikowania** przycisku. **Dane wyjściowe** okno wyświetla stan. Pozostała część tego samouczka zostanie ukończona w portalu Azure. Zamknij program Visual Studio 2017 r. 

## <a name="open-three-browser-tabs"></a>Otwórz trzy karty przeglądarki
W portalu Azure Znajdź bot aplikacji sieci web i otwórz go. Poniższe kroki Użyj trzy różne widoki bot aplikacji sieci web. Może to być łatwiejsze do trzech osobnych kart Otwórz w przeglądarce: 
  
>  * Testowanie w rozmów w sieci Web
>  * Edytor kodu online kompilacji/Otwórz-> Edytor usługi aplikacji
>  * Konsola Kudu Edytor/otworzyć usługi aplikacji -> konsoli diagnostyki

## <a name="modify-basicluisdialogcs-code"></a>Zmodyfikuj kod BasicLuisDialog.cs

1. W **Edytor usług aplikacji** karty przeglądarki, otwórz `BasicLuisDialog.cs` pliku.

2. Dodaj następujące zależności NuGet w istniejących `using` wiersze:

   [!code-csharp[Add using statement](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/csharp/BasicLuisDialog.cs?range=11-12 "Add using statement")]

3. Dodaj `LogToApplicationInsights` funkcji:

   [!code-csharp[Add the LogToApplicationInsights function](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/csharp/BasicLuisDialog.cs?range=61-92 "Add the LogToApplicationInsights function")]

    Klucz Instrumentacji usługi Application Insights jest już bot aplikacji sieci web aplikacji ustawienie o nazwie `BotDevInsightsKey`. 

    Ostatni wiersz w funkcji dodaje dane do usługi Application Insights. Nazwa śledzenia jest `LUIS`, unikatową nazwę oprócz inne dane telemetryczne zebrane przez ten robot aplikacji sieci web. Wszystkie właściwości również mają przedrostek `LUIS_` pozwala zobaczyć, jakie dane w tym samouczku dodaje w porównaniu do informacji jest określany przez bot aplikacji sieci web.

4. Wywołanie `LogToApplicationInsights` funkcji w górnej części `ShowLuisResult` funkcji:

   [!code-csharp[Use the LogToApplicationInsights function](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/csharp/BasicLuisDialog.cs?range=114-115 "Use the LogToApplicationInsights function")]

## <a name="build-web-app-bot"></a>Tworzenie bot aplikacji sieci web
1. Tworzenie bot aplikacji sieci web w jeden z dwóch sposobów. Pierwsze rozwiązanie polega na kliknij prawym przyciskiem myszy `build.cmd` w **Edytor usług aplikacji**, a następnie wybierz pozycję **uruchomić z konsoli**. Dane wyjściowe konsoli Wyświetla i kończy działanie z `Finished successfully.`

2. Jeśli to nie zakończy się pomyślnie, należy otworzyć konsolę, przejdź do skrypt i uruchom go, wykonując następujące kroki. W **Edytor usług aplikacji**, na górnym pasku niebieski, wybierz nazwę Twojej bot, a następnie wybierz **Otwórz konsolę Kudu** na liście rozwijanej.

    ![Otwórz program Kudu konsoli](./media/luis-tutorial-bot-csharp-appinsights/open-kudu-console.png)

3. W oknie konsoli wprowadź następujące polecenie:

    ```
    cd site\wwwroot && build.cmd
    ```

    Poczekaj na zakończenie z kompilacji `Finished successfully.`

## <a name="test-the-web-app-bot"></a>Testowanie bot aplikacji sieci web

1. Aby przetestować robot aplikacji sieci web, otwórz **testów w sieci Web rozmowę** funkcji w portalu. 

2. Wprowadź wyrażenie `Coffee bar on please`.  

    ![Testowanie bot aplikacji sieci web w rozmowy](./media/luis-tutorial-bot-csharp-appinsights/test-in-web-chat.png)

3. Nie różnicy w odpowiedzi chatbot powinna zostać wyświetlona. Zmiana jest wysyłanie danych do usługi Application Insights nie bot odpowiedzi. Wprowadź kilka więcej zniesławiających, tak aby nieco większej ilości danych w usłudze Application Insights:

```
Please deliver a pizza
Turn off all the lights
Turn on the hall light
```

## <a name="view-luis-entries-in-application-insights"></a>Wyświetlanie LUIS wpisów w usłudze Application Insights
Otwórz Application Insights, aby wyświetlić wpisy LUIS. 

1. W portalu, wybierz **wszystkie zasoby** następnie Filtruj według nazwy bot aplikacji sieci web. Kliknij zasób z typem **usługi Application Insights**. Ikona usługi Application Insights jest żarówka. 

    ![Wyszukiwanie szczegółowych informacji z aplikacji](./media/luis-tutorial-bot-csharp-appinsights/portal-service-list-app-insights.png)

2. Po otwarciu zasobu kliknij **wyszukiwania** ikonę lupy w prawym panelu. Nowy panel do prawej Wyświetla. W zależności od ilości danych telemetrycznych zostanie znaleziony, panelu może zająć chwilę do wyświetlenia. Wyszukaj `LUIS`. Lista jest zawężony tylko zapytania LUIS wyniki dodane w tym samouczku.

    ![Wyszukaj dane śledzenia](./media/luis-tutorial-bot-csharp-appinsights/portal-service-list-app-insights-search-luis-trace.png)

3. Zaznacz górny wpis. Nowe okno wyświetla bardziej szczegółowe dane w tym dane niestandardowe dla zapytania LUIS-prawej. Dane obejmują zamiar górny, a jego wynik.

    ![Zapoznaj się z pozycją śledzenia](./media/luis-tutorial-bot-csharp-appinsights/portal-service-list-app-insights-search-luis-trace-item.png)

    Gdy wszystko będzie gotowe, wybierz początku prawej **X** aby powrócić do listy elementów zależności. 


> [!Tip]
> Jeśli chcesz zapisać na liście zależności i wrócić do niego później, kliknij polecenie **... Więcej** i kliknij przycisk **zapisywanie ulubionego elementu**.

## <a name="query-application-insights-for-intent-score-and-utterance"></a>Zapytanie usługi Application Insights zamiar, oceny i utterance
Usługi Application Insights daje uprawnienia do wykonywania zapytań o dane z [Kusto](https://docs.microsoft.com/azure/application-insights/app-insights-analytics#query-data-in-analytics) języka, jak również eksportu do [PowerBI](https://powerbi.microsoft.com). 

1. Polecenie **Analytics** w górnej części zależności wyświetlania powyżej pola filtru. 

    ![Przycisk Analiza](./media/luis-tutorial-bot-csharp-appinsights/portal-service-list-app-insights-search-luis-analytics-button.png)

2. Otwiera nowe okno z u góry okna zapytania i okno tabeli danych poniżej. Jeśli używasz bazy danych przed to rozmieszczenie jest znana. Zapytanie zawiera wszystkie elementy z ostatnich 24 godzin rozpoczynające się od nazwy `LUIS`. **CustomDimensions** kolumna ma LUIS wyniki zapytania jako pary nazwa/wartość.

    ![Raport analizy domyślne](./media/luis-tutorial-bot-csharp-appinsights/analytics-query-1.png)

3. Aby wysunąć zamiar top, oceny i utterance, Dodaj następujący ostatnim wierszu powyżej w oknie zapytania:

    ```SQL
    | extend topIntent = tostring(customDimensions.LUIS_topScoringIntent)
    | extend score = todouble(customDimensions.LUIS_topScoringIntentScore)
    | extend utterance = tostring(customDimensions.LUIS_query)
    ```

4. Uruchom zapytanie. Przewiń do prawej w tabeli danych. Dostępne są nowe kolumny topIntent, oceny i utterance. Kliknij kolumnę topIntent do sortowania.

    ![Raport analizy niestandardowych](./media/luis-tutorial-bot-csharp-appinsights/analytics-query-2.png)


Dowiedz się więcej o [język zapytań Kusto](https://docs.loganalytics.io/docs/Learn/Getting-Started/Getting-started-with-queries) lub [eksportować dane do usługi Power BI](https://docs.microsoft.com/azure/application-insights/app-insights-export-power-bi). 


## <a name="learn-more-about-bot-framework"></a>Dowiedz się więcej o Bot Framework
Dowiedz się więcej o [Bot Framework](https://dev.botframework.com/).

## <a name="next-steps"></a>Kolejne kroki

Inne informacje, które chcesz dodać do dane usługi application insights zawiera identyfikator aplikacji, identyfikator wersji, Data ostatniej zmiany modelu, Data ostatniej pociągu, Data ostatniej publikacji. Te wartości można pobrać albo z adresu URL punktu końcowego (identyfikator aplikacji i identyfikator wersji) lub [tworzenia interfejsu API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c3d) połączenia, a następnie w ustawieniach bot aplikacji sieci web i pobierane z tego miejsca.  

Korzystając z tej samej subskrypcji punktu końcowego dla więcej niż jedną aplikację LUIS, należy także uwzględnić identyfikator subskrypcji i właściwości informujące o tym, że klucza wspólnego. 

> [!div class="nextstepaction"]
> [Dowiedz się więcej o zniesławiających przykład](luis-how-to-add-example-utterances.md)
