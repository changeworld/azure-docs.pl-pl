---
title: 'Samouczek: Wizualizuj dane z Eksploratora danych usługi Azure w usłudze Power BI'
description: Z tego samouczka dowiesz się, jak połączyć się z usługą Azure Data Explorer za pomocą usługi Power BI i zwizualizować swoje dane.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: tutorial
ms.date: 09/24/2018
ms.openlocfilehash: f253911c1830e606dd47b64aaea1f17cb3478cd5
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2019
ms.locfileid: "58757734"
---
# <a name="tutorial-visualize-data-from-azure-data-explorer-in-power-bi"></a>Samouczek: Wizualizuj dane z Eksploratora danych usługi Azure w usłudze Power BI

Azure Data Explorer to szybka i wysoce skalowalna usługa eksploracji danych na potrzeby danych dziennika i telemetrycznych. Usługa Power BI to rozwiązanie do analizy biznesowej, które pozwala wizualizować dane i udostępniać wyniki w organizacji. Z tego samouczka na początku dowiesz się, jak renderować wizualizacje w usłudze Azure Data Explorer. Następnie połączysz się z usługą Azure Data Explorer za pomocą usługi Power BI, utworzysz raport na podstawie przykładowych danych i opublikujesz raport dla usługi Power BI.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/). Jeśli nie masz konta dla usługi Power BI Pro, przed rozpoczęciem [utwórz konto bezpłatnej wersji próbnej](https://app.powerbi.com/signupredirect?pbi_source=web).

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Renderowanie wizualizacji w usłudze Azure Data Explorer
> * Połączenie z usługą Azure Data Explorer w programie Power BI Desktop
> * Praca z danymi w programie Power BI Desktop
> * Tworzenie raportu z wizualizacjami
> * Publikowanie i udostępnianie raportu

## <a name="prerequisites"></a>Wymagania wstępne

Oprócz subskrypcji platformy Azure i usługi Power BI do ukończenia samouczka potrzebne są następujące elementy:

* [Klaster testowy i baza danych](create-cluster-database-portal.md)

* [Przykładowe dane StormEvents](ingest-sample-data.md). [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

* Program [Power BI Desktop](https://powerbi.microsoft.com/get-started/) (wybierz pozycję **POBIERZ BEZPŁATNIE**)

## <a name="render-visuals-in-azure-data-explorer"></a>Renderowanie wizualizacji w usłudze Azure Data Explorer

Zanim przejdziemy do usługi Power BI, przyjrzyjmy się, jak w usłudze Azure Data Explorer można renderować wizualizacje. Jest to doskonałe rozwiązanie dla pewnych szybkich analiz.

1. Zaloguj się do witryny [https://dataexplorer.azure.com](https://dataexplorer.azure.com).

1. W okienku po lewej stronie wybierz testową bazę danych, która zawiera przykładowe dane StormEvents.

1. Wklej poniższe zapytanie w oknie po prawej stronie, a następnie wybierz pozycję **Uruchom**.

    ```Kusto
    StormEvents
    | summarize event_count=count() by State
    | where event_count > 1800
    | project State, event_count
    | sort by event_count
    | render columnchart
    ```

    To zapytanie zlicza zdarzenia pogodowe w podziale na stany. Następnie powoduje renderowanie wykresu kolumnowego dotyczącego wszystkich stanów, które mają ponad 1800 zdarzeń pogodowych.

    ![Wykres kolumnowy zdarzeń](media/visualize-power-bi/events-column-chart.png)

1. Wklej poniższe zapytanie w oknie po prawej stronie, a następnie wybierz pozycję **Uruchom**.

    ```Kusto
    StormEvents
    | where State == "WASHINGTON" and StartTime >= datetime(2007-07-01) and StartTime <= datetime(2007-07-31)
    | summarize StormCount = count() by EventType
    | render piechart
    ```

    To zapytanie zlicza zdarzenia pogodowe pogrupowane według typu, które miały miejsce w lipcu w stanie Waszyngton. Następnie powoduje renderowanie wykresu kołowego, przedstawiającego udział procentowy poszczególnych typów zdarzeń.

    ![Wykres kołowy zdarzeń](media/visualize-power-bi/events-pie-chart.png)

Teraz nadszedł czas, aby przyjrzeć się usłudze Power BI, ale w usłudze Azure Data Explorer można wykonać dużo więcej operacji związanych z wizualizacjami.

## <a name="connect-to-azure-data-explorer"></a>Połączenie z usługą Azure Data Explorer

Teraz połączysz się z usługą Azure Data Explorer w programie Power BI Desktop.

1. W programie Power BI Desktop na karcie **Narzędzia główne** wybierz pozycję **Pobierz dane**, a następnie pozycję **Więcej**.

    ![Pobieranie danych](media/visualize-power-bi/get-data-more.png)

1. Wyszukaj pozycję *Azure Data Explorer*, wybierz pozycję **Azure Data Explorer (beta)**, a następnie pozycję **Połącz**.

    ![Wyszukiwanie i pobieranie danych](media/visualize-power-bi/search-get-data.png)

1. Na ekranie **Łącznik w wersji zapoznawczej** wybierz opcję **Kontynuuj**.

1. Na następnym ekranie wprowadź nazwę klastra testowego i bazy danych. Nazwa klastra powinna mieć format `https://<ClusterName>.<Region>.kusto.windows.net`. Jako nazwę tabeli wprowadź *StormEvents*. Dla wszystkich pozostałych opcji pozostaw wartości domyślne, a następnie wybierz przycisk **OK**.

    ![Opcje klastra, bazy danych, tabeli](media/visualize-power-bi/cluster-database-table.png)

1. Na ekranie podglądu danych wybierz opcję **Edytuj**.

    W edytorze Power Query zostanie otwarta tabela, w której przed zaimportowaniem danych można edytować wiersze i kolumny.

## <a name="work-with-data-in-power-bi-desktop"></a>Praca z danymi w programie Power BI Desktop

Teraz, gdy masz połączenie z usługą Azure Data Explorer, będziesz edytować dane w edytorze Power Query. Usuniesz wiersze z wartościami zerowymi w kolumnie **BeginLat** i całkowicie usuniesz kolumnę JSON **StormSummary**. To są proste czynności, ale podczas importowania danych możesz dokonywać również złożonych przekształceń.

1. Wybierz strzałkę dla kolumny **BeginLat**, wyczyść pole wyboru **null**, a następnie wybierz przycisk **OK**.

    ![Filtrowanie kolumny](media/visualize-power-bi/filter-column.png)

1. Kliknij prawym przyciskiem myszy nagłówek kolumny **StormSummary**, a następnie wybierz pozycję **Usuń**.

    ![Usunięcie kolumny](media/visualize-power-bi/remove-column.png)

1. W okienku **USTAWIENIA ZAPYTANIA** zmień nazwę z *Zapytanie1* na *StormEvents*.

    ![Zmiana nazwy zapytania](media/visualize-power-bi/query-name.png)

1. Na karcie **Narzędzia główne** na wstążce wybierz pozycję **Zamknij i zastosuj**.

    ![Zamknięcie i zastosowanie](media/visualize-power-bi/close-apply.png)

    Program Power Query spowoduje zastosowanie zmian, a następnie zaimportuje dane przykładowe do *modelu danych*. Kilka następnych kroków pokazuje, jak można wzbogacić model. Ponownie jest to tylko prosty przykład, dający pogląd na to, co jest możliwe.

1. Po lewej stronie okna głównego wybierz widok danych.

    ![Widok danych](media/visualize-power-bi/data-view.png)

1. Na karcie **Modelowanie** na wstążce wybierz pozycję **Nowa kolumna**.

    ![Nowa kolumna](media/visualize-power-bi/new-column.png)

1. W pasku formuły wprowadź następującą formułę języka DAX (Data Analysis Expressions), a następnie naciśnij klawisz Enter.

    ```DAX
    DurationHours = DATEDIFF(StormEvents[StartTime], StormEvents[EndTime], hour)
    ```

    ![Pasek formuły](media/visualize-power-bi/formula-bar.png)

    Ta formuła tworzy kolumnę *DurationHours*, obliczającą liczbę godzin, przez którą trwało każde zdarzenie pogodowe. Użyjesz tej kolumny w wizualizacji w następnej sekcji.

1. Przewiń do prawej strony tabeli, aby zobaczyć tę kolumnę.

## <a name="create-a-report-with-visuals"></a>Tworzenie raportu z wizualizacjami

Teraz, gdy dane są zaimportowane i model danych został udoskonalony, nadszedł czas na tworzenie raportu z wizualizacjami. Dodasz wykres kolumnowy oparty o czas trwania zdarzenia i mapę pokazującą szkody w uprawach.

1. Po lewej stronie okna wybierz widok raportu.

    ![Widok raportu](media/visualize-power-bi/report-view.png)

1. W okienku **WIZUALIZACJE** wybierz wykres kolumnowy grupowany.

    ![Dodawanie wykresu kolumnowego](media/visualize-power-bi/add-column-chart.png)

    Do kanwy został dodany pusty wykres.

    ![Pusty wykres](media/visualize-power-bi/blank-chart.png)

1. Na liście **POLA** wybierz pozycje **DurationHours** i **State**.

    ![Wybór pól](media/visualize-power-bi/select-fields.png)

    Masz teraz wykres przedstawiający łączną liczbę godzin zdarzeń pogodowych w ciągu roku w podziale na stany.

    ![Wykres kolumnowy czasu trwania](media/visualize-power-bi/duration-column-chart.png)

1. Kliknij w dowolnym miejscu na kanwie poza wykresem kolumnowym.

1. W okienku **WIZUALIZACJE** wybierz mapę.

    ![Dodawanie mapy](media/visualize-power-bi/add-map.png)

1. Na liście **POLA** wybierz pozycję **CropDamage** i **State**. Zmień rozmiar mapy, aby stany USA były wyraźnie widoczne.

    ![Mapa szkód w uprawach](media/visualize-power-bi/crop-damage-map.png)

    Rozmiar bąbelków reprezentuje wyrażoną w dolarach wartość szkód w uprawach. Aby wyświetlić szczegóły, ustaw wskaźnik myszy na nad bąbelkami.

1. Przenoś wizualizacje i zmieniaj ich rozmiar, dzięki czemu uzyskasz raport, który wygląda podobnie jak na poniższej ilustracji.

    ![Gotowy raport](media/visualize-power-bi/finished-report.png)

1. Zapisz raport pod nazwą *storm-events.pbix*.

## <a name="publish-and-share-the-report"></a>Publikowanie i udostępnianie raportu

Do tego momentu wszystko, co zostało wykonane w usłudze Power BI, jest dostępne lokalnie przy użyciu programu Power BI Desktop. Teraz opublikujesz raport w usłudze Power BI, udostępniając go innym osobom.

1. W programie Power BI Desktop na karcie **Narzędzia główne** na wstążce wybierz pozycję **Publikuj**.

    ![Przycisk Opublikuj](media/visualize-power-bi/publish-button.png)

1. Jeśli jeszcze nie zostało to zrobione, przejdź przez proces logowania w usłudze Power BI.

1. Wybierz opcję **Mój obszar roboczy**, a następnie pozycję **Wybierz**.

    ![Wybór obszaru roboczego](media/visualize-power-bi/select-workspace.png)

1. Po zakończeniu publikowania wybierz pozycję **Otwórz plik storm-events.pbix w usłudze Power BI**.

    ![Publikowanie powiodło się](media/visualize-power-bi/publishing-succeeded.png)

    Raport zostanie otwarty w usłudze przy użyciu tych samych wizualizacji i układu, które zostały zdefiniowane w programie Power BI Desktop.

1. W prawym górnym rogu raportu wybierz pozycję **Udostępnij**.

    ![Przycisk udostępniania](media/visualize-power-bi/share-button.png)

1. Na ekranie **Udostępnianie raportu** dodaj współpracownika z Twojej organizacji, dodaj uwagę, a następnie wybierz pozycję **Udostępnij**.

    ![Udostępnianie raportu](media/visualize-power-bi/share-report.png)

    Jeśli Twój współpracownik ma odpowiednie uprawnienia, może uzyskać dostęp do udostępnionego przez Ciebie raportu.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie chcesz zachować utworzonego raportu, po prostu usuń plik *storm-events.pbix*. Jeśli chcesz usunąć opublikowany raport, wykonaj następujące kroki.

1. W obszarze **Mój obszar roboczy** przewiń w dół do pozycji **RAPORTY** i znajdź pozycję **storm-events**.

1. Wybierz przycisk wielokropka (**. . .**) znajdujący się obok pozycji **storm-events**, a następnie wybierz pozycję **USUŃ**.

    ![Usuwanie raportu](media/visualize-power-bi/remove-report.png)

1. Potwierdź usunięcie.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Pisanie zapytań](write-queries.md)
