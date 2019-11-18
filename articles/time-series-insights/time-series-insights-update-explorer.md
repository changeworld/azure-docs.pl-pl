---
title: Wizualizowanie danych w Eksploratorze w wersji zapoznawczej — Azure Time Series Insights | Microsoft Docs
description: Dowiedz się więcej o funkcjach i opcjach dostępnych w Eksploratorze Azure Time Series Insights w wersji zapoznawczej.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 11/13/2019
ms.custom: seodec18
ms.openlocfilehash: 17ba808ebfabb68765cf35bbf0799d117bc6383b
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/16/2019
ms.locfileid: "74133438"
---
# <a name="azure-time-series-insights-preview-explorer"></a>Eksplorator Azure Time Series Insights w wersji zapoznawczej

W tym artykule opisano różne funkcje i opcje dostępne w [aplikacji sieci Web demonstracyjnej](https://insights.timeseries.azure.com/preview/demo)w wersji zapoznawczej programu Azure Time Series Insights.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć pracę z Eksploratorem Azure Time Series Insights w wersji zapoznawczej, należy wykonać następujące kroki:

* Zainicjowano środowisko Time Series Insights. Dowiedz się więcej o aprowizacji wystąpienia, odczytując samouczek [Azure Time Series Insights Preview](./time-series-insights-update-create-environment.md) .
* [Zapewnianie dostępu do danych](./time-series-insights-data-access.md) do środowiska Time Series Insights utworzonego dla konta. Można zapewnić dostęp do innych użytkowników oraz do siebie.
* Dodaj źródło zdarzenia do środowiska Time Series Insights, aby wypchnąć dane do środowiska:
  * Dowiedz się [, jak nawiązać połączenie z centrum zdarzeń](./time-series-insights-how-to-add-an-event-source-eventhub.md) 
  * Dowiedz się, [jak nawiązać połączenie z usługą IoT Hub](./time-series-insights-how-to-add-an-event-source-iothub.md)

## <a name="explore-the-time-series-insights-preview-explorer"></a>Eksplorowanie Eksploratora Time Series Insights w wersji zapoznawczej

Eksplorator Azure Time Series Insights w wersji zapoznawczej składa się z następujących siedmiu elementów:

[Przegląd ![Time Series Insights w wersji zapoznawczej](media/v2-update-explorer/preview-explorer-overview.png)](media/v2-update-explorer/preview-explorer-overview.png#lightbox)

1. [Panel środowiska](#1-environment-panel): wyświetla wszystkie środowiska Azure Time Series Insights.
1. [Pasek nawigacyjny](#2-navigation-bar): umożliwia przełączenie między stronami **Analizowanie** i **modelem** .
1. [Drzewo hierarchii i panel wyszukiwania](#3-hierarchy-tree-and-search-panel): umożliwia wybranie i wyszukanie określonych elementów danych do grafu.
1. [Źródło szeregów czasowych](#4-time-series-well): pokazuje wszystkie aktualnie wybrane elementy danych.
1. [Panel wykresu](#5-chart-panel): Wyświetla bieżące wykresu pracy.
1. [Oś czasu](#6-time-editor-panel): umożliwia modyfikowanie przedział czasu swojej pracy.
1. [Pasek aplikacji](#7-app-bar): zawiera opcje zarządzania użytkownikami (takie jak bieżąca dzierżawa) i umożliwia zmianę ustawień języka.


## <a name="1-environment-panel"></a>1. Panel środowiska

Na panelu środowiska są wyświetlane wszystkie środowiska usługi Time Series Insights, do których masz dostęp do. Lista obejmuje środowiska z opcją płatność zgodnie z rzeczywistym użyciem (wersja zapoznawcza), a także środowiska S1/S2 (ogólna dostępność). Po prostu kliknij środowisko Time Series Insights, które ma być używane do natychmiastowego wykonania.

1. Wybierz strzałkę listy rozwijanej obok wyświetlanego środowiska.

   [Panel środowiska ![](media/v2-update-explorer/environment-panel.png)](media/v2-update-explorer/environment-panel.png#lightbox)

1. Następnie wybierz odpowiednie środowisko.

## <a name="2-navigation-bar"></a>2. pasek nawigacyjny

  [![pasku nawigacyjnym](media/v2-update-explorer/navigation-bar.png)](media/v2-update-explorer/navigation-bar.png#lightbox)

Użyj paska nawigacyjnego, aby wybrać dwa widoki:

* **Analizuj**: Użyj go do grafowania i wykonywania bogatej analizy danych z szeregów czasowych w modelu lub w modelu.
* **Model**: Użyj go, aby wypchnąć nowe typy, hierarchie i wystąpienia podglądu Time Series Insights w modelu Time Series Insights.

### <a name="model-authoring"></a>Tworzenie modelu

Wersja zapoznawcza Azure Time Series Insights obsługuje operacje pełnego tworzenia, odczytu, aktualizacji i usuwania (CRUD) w modelu szeregów czasowych.

[![panelu wyszukiwania modelu](media/v2-update-explorer/model-search-panel.png)](media/v2-update-explorer/model-search-panel.png#lightbox)

* **Typ modelu szeregów czasowych**: można użyć typów Time Series Insights do definiowania zmiennych lub formuł do wykonywania obliczeń. Są one skojarzone z danym wystąpieniem Time Series Insights. Typ może mieć co najmniej jednej zmiennej.
* **Czas hierarchii modelu szeregów**: hierarchie są systematycznego organizacji danych. Hierarchie przedstawiać relacje między różnymi jednostkami danych usługi Time Series Insights.
* **Wystąpienie modelu serii czasu**: wystąpienia są szeregów czasowych, samodzielnie. W większości przypadków są to **DeviceID** lub **AssetID**, który jest unikatowym identyfikatorem zasobu w środowisku.

Aby dowiedzieć się więcej na temat modelu szeregów czasowych, zobacz [modeli szeregów razy](./time-series-insights-update-tsm.md).

## <a name="3-hierarchy-tree-and-search-panel"></a>3. drzewo hierarchii i panel wyszukiwania

Drzewo hierarchii i panel wyszukiwania umożliwiają łatwe wyszukiwanie i nawigowanie w hierarchii [modelu szeregów czasowych](./time-series-insights-update-tsm.md) , aby znaleźć określone wystąpienia szeregów czasowych, które mają być wyświetlane na wykresie. Po wybraniu wystąpień nie są one dodawane tylko do bieżącego wykresu, ale również są dodawane do obszaru dane. 

[drzewo hierarchii ![i panel wyszukiwania](media/v2-update-explorer/hierarchy-search.png)](media/v2-update-explorer/hierarchy-search.png#lightbox)

Okienko wyników wyszukiwania umożliwia również wyświetlanie wyników w widoku hierarchii lub widoku listy, ułatwiając Znajdowanie wystąpień, które mają być wyświetlane.
 
## <a name="4-time-series-well"></a>4. okres szeregu czasowego

Wyświetla pola wystąpienia i inne metadane skojarzone z wybranymi wystąpieniami Time Series Insights. Zaznaczając pola wyboru po prawej stronie, można ukryć lub wyświetlić określone wystąpienia z bieżącego wykresu. 

  [![Źródło podglądu](media/v2-update-explorer/preview-well.png)](media/v2-update-explorer/preview-well.png#lightbox)

Można usunąć konkretne elementy danych z bieżących danych, zaznaczając formant Red **delete** (kosza) po lewej stronie elementu. Ponadto pozwala kontrolować sposób wyświetlania poszczególnych elementów na wykresie. Można wybrać opcję dodawania minimalnych i maksymalnych cieni, punktów danych, przesunąć element w czasie i wizualizować wystąpienie w sposób. 

Ponadto kontrolka eksploracji pozwala łatwo tworzyć zmiany czasu i wykresy punktowe.  

  [![opcje układu](media/v2-update-explorer/well-layout-options.png)](media/v2-update-explorer/well-layout-options.png#lightbox)

> [!NOTE]
> Jeśli zobaczysz następujący komunikat, wystąpienie nie ma żadnych danych w wybranym okresie. Aby rozwiązać ten problem, należy zwiększyć przedział czasu lub potwierdzić, że wystąpienie jest wypychane dane.
>
> ![Brak powiadomienia o danych](media/v2-update-explorer/no-data-warning.png)

## <a name="5-chart-panel"></a>5. Panel wykresu

Wykres umożliwia wyświetlanie wystąpień szeregów czasowych jako wierszy. Można zwinąć panelu środowiska, modelu danych i godzina w Panelu sterowania zakresu, klikając kontrolki sieci web, aby powiększyć wykresu. 

  [Przegląd wykresu ![Podgląd](media/v2-update-explorer/chart-overview.png)](media/v2-update-explorer/chart-overview.png#lightbox)

1. **Typ wykresu**: określa, które elementy danych są dostępne do wizualizacji.

1. **Rozmiar interwału**: Narzędzie suwaka Rozmiar interwału umożliwia powiększanie i wyświetlanie interwałów w tym samym przedziale czasu. Zapewnia to dokładniejszą kontrolę nad przenoszeniem między dużymi wycinkami czasu, które przedstawiają płynne trendy w dół do wycinków jako milisekundy, co pozwala na wyświetlanie szczegółowych i wysokiej rozdzielczości kawałków danych. Domyślny punkt początkowy suwaka jest ustawiany jako najbardziej optymalny widok danych z wyboru. zrównoważenie rozdzielczości, szybkość zapytania i stopień szczegółowości.

1. **Powiększanie i kadrowanie**: kliknij tę kontrolkę, aby powiększyć i przesunąć wykres.

1. **Kontrolka osi y**: przechodzenie między dostępnymi opcjami widoku osi y:

    * `Stacked`: Każdy wiersz zawiera poszczególne osi y.
    * `Overlap`: Użyj go, aby układać wiele linii na tej samej osi Y, przy czym dane osi Y zmieniają się w zależności od wybranego wiersza.
    * `Shared`: Wyświetlane razem wszystkie dane osi y.

1. **Znacznik — element**: aktualnie wybrany element danych i jego skojarzone szczegóły.

Możesz kontynuować przechodzenie do określonego wycinka danych, **klikając lewym przyciskiem** myszy punkt danych na bieżącym grafie, trzymając mysz, a następnie przeciągając wybrany obszar do wybranego punktu końcowego. **Kliknij prawym przyciskiem myszy** szary, wybrany obszar, a następnie kliknij pozycję **powiększenie** , jak pokazano poniżej. Możesz również wyświetlić i pobrać zdarzenia telemetryczne z wybranego przedziału czasu.

  [![powiększenie wykresu podglądu](media/v2-update-explorer/preview-chart-zoom.png)](media/v2-update-explorer/preview-chart-zoom.png#lightbox)

Po wykonaniu akcji **powiększenia** zobaczysz wybrany zestaw danych. Wybierz kontrolkę format, aby przechodzić przez trzy reprezentacje osi y danych Time Series Insights.

  [![Podgląd osi y wykresu](media/v2-update-explorer/standard-chart.png)](media/v2-update-explorer/standard-chart.png#lightbox)

Tutaj można zobaczyć przykład **nakładającego się wykresu**:

  [![nakładania się opcji wykresu](media/v2-update-explorer/overlapping-chart.png)](media/v2-update-explorer/overlapping-chart.png#lightbox)

Przycisk **więcej akcji** rozwija się, aby wyświetlić opcje **Pobierz jako CSV** i **Eksportuj do Power BI** .

  [Opcja ![więcej akcji](media/v2-update-explorer/more-actions-icon.png)](media/v2-update-explorer/more-actions-icon.png#lightbox)

Przeczytaj o [Time Series Insights Power BI natywnego łącznika](concepts-power-bi.md).

## <a name="6-time-editor-panel"></a>6. Panel edytora czasu

Podczas pracy z Time Series Insights najpierw wybierz przedział czasu. Wybrany przedział czasu będzie kontrolować zestaw danych, który jest dostępny do manipulowania za pomocą widżetów aktualizacji Time Series Insights.

  [Panel wyboru ![czas](media/v2-update-explorer/timeline-element.png)](media/v2-update-explorer/timeline-element.png#lightbox)

> [!TIP]
> Część osi czasu jest wyróżniona w kolorze bursztynowym lub pomarańczowym, aby wskazać zakres danych dostępnych w sklepie ciepłym.

Poniższe kontrolki sieci Web są dostępne w ramach aktualizacji Time Series Insights do wybierania zakresu czasu pracy. 

  [![kontrolę dobrze](media/v2-update-explorer/exploration-well-control.png)](media/v2-update-explorer/exploration-well-control.png#lightbox)

1. **Kontrolka suwaka wewnętrznego zakresu dat**: Użyj dwóch kontrolek punktu końcowego, przeciągając je w pożądanym okresie. Ten wewnętrzny zakres dat jest ograniczony przez kontrolkę suwaka zewnętrznego zakresu dat.

1. **Zwiększać i zmniejszać przycisków zakresu dat**: zwiększyć lub zmniejszyć czas span, wybierając przycisk albo interwał ma.

1. **Kontrolka zwijania zakresu czasu**: ten formant sieci Web umożliwia ukrycie wszystkich kontrolek z wyjątkiem wewnętrznego narzędzia suwaka zakresu dat.

1. **Kontrolka suwaka zewnętrznego zakresu dat**: Użyj kontrolek punktu końcowego, aby wybrać zewnętrzny zakres dat, który będzie dostępny dla wewnętrznej kontroli zakresu dat.

1. **Kontrolka suwaka zakresu czasu**: Użyj jej, aby szybko przełączać się do wybranych opcji przedziału czasu, takich jak ostatnie **30 minut**, **ostatnie 12 godzin**lub **niestandardowy zakres**. Zmiana ta wartość zmienia się również zakresy dostępne interwał omówione w narzędziu suwaka rozmiar interwału.

   [![do i z panelu wyboru](media/v2-update-explorer/to-and-from-element.png)](media/v2-update-explorer/to-and-from-element.png#lightbox)

## <a name="7-app-bar"></a>7. pasek aplikacji

Panel nawigacji Time Series Insights Preview pojawia się u góry aplikacji Time Series Insights. Zapewnia następujące funkcje:

### <a name="current-session-share-link-control"></a>Kontrola łącza udział w bieżącej sesji

  [ikona udostępniania ![](media/v2-update-explorer/share-icon.png)](media/v2-update-explorer/share-icon.png#lightbox)

Wybierz ikonę Nowy **udział** , aby udostępnić link adresu URL do zespołu.

  [![udostępnić swój adres URL wystąpienia](media/v2-update-explorer/share-your-view.png)](media/v2-update-explorer/share-your-view.png#lightbox)

### <a name="tenant-section"></a>Sekcja dzierżawy

  [![wybór dzierżawy](media/v2-update-explorer/tenant-selection.png)](media/v2-update-explorer/tenant-selection.png#lightbox)

* Wyświetla bieżące informacje o koncie logowania Time Series Insights.
* Użyj go, aby przełączać się między dostępnymi Time Series Insightsmi motywami.
* Służy do wyświetlania [demonstracyjnej aplikacji sieci Web](https://insights.timeseries.azure.com/preview/demo).

### <a name="theme-selection"></a>Wybór motywu

Aby wybrać nowy motyw, wybierz ikonę profilu znajdującą się w prawym górnym rogu. Następnie wybierz pozycję **Zmień motyw**.

  [wybór motywu ![](media/v2-update-explorer/theme-selection.png)](media/v2-update-explorer/theme-selection.png#lightbox)

> [!TIP]
> Wybór języka jest również dostępny przez wybranie ikony profilu.

Azure czas Series Insights w wersji zapoznawczej obsługuje dwa motywy:

* **Motyw jasny**: motyw domyślny pokazywany w tym dokumencie.
* **Ciemny motyw**: renderuje Eksploratora, jak pokazano poniżej:

  [![wybrany motyw ciemny](media/v2-update-explorer/dark-theme-selected.png)](media/v2-update-explorer/dark-theme-selected.png#lightbox)

## <a name="s1s2-environment-controls"></a>Kontrolki środowiska S1/S2

### <a name="preview-terms-panel"></a>Panel warunków wersji zapoznawczej

Ta sekcja dotyczy tylko do istniejących środowisk S1/S2, które próbują w Interfejsie użytkownika zaktualizowane przy użyciu Eksploratora. Możesz chcieć korzystać z ogólnie dostępnego produktu i wersji zapoznawczej. Niektóre funkcje z istniejącego interfejsu użytkownika do dodaliśmy zaktualizowane explorer, ale można uzyskać pełnego środowiska interfejsu użytkownika dla środowiska S1/S2 w istniejących Eksploratora usługi Time Series Insights. 

Zamiast hierarchii zobaczysz panel warunki Time Series Insights, w którym można definiować zapytania w danym środowisku. Służy do filtrowania danych w oparciu o predykat.

  [![, gdzie panel zapytania](media/v2-update-explorer/s1-s2-preview-query.png)](media/v2-update-explorer/s1-s2-preview-query.png#lightbox)

Panel Edytor warunków czasu Series Insights w wersji zapoznawczej przyjmuje następujące parametry:

**Gdzie**: Użyj klauzuli WHERE, aby szybko filtrować zdarzenia przy użyciu zestawu argumentów zamieszczonych w poniższej tabeli. Jeśli wyszukiwania, wybierając operand, predykat jest automatycznie aktualizowana, oparte na tym wyszukiwaniem. Obsługiwane są następujące typy operandów:

| Operacja | Obsługiwane typy   | Uwagi |
| --- | --- | --- |
| `<`, `>`, `<=`, `>=` | Double, DateTime, TimeSpan | |
| `=`, `!=`, `<>` | String, Bool, Double, DateTime, przedział czasu, wartość NULL |
| `IN` | String, Bool, Double, DateTime, przedział czasu, wartość NULL | Wszystkie argumenty powinny być tego samego typu lub być stała NULL. |
| `HAS` | Ciąg | Tylko stałe literały ciągu są dozwolone po prawej stronie. Pusty ciąg i wartość NULL są niedozwolone. |

Aby dowiedzieć się więcej na temat obsługiwanych operacji zapytań i typów danych, zobacz [wyrażenie szeregów czasowych (TSX)](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax).

### <a name="examples-of-where-clauses"></a>Przykłady klauzul WHERE

  [Przykłady klauzul ![WHERE](media/v2-update-explorer/example-queries.png)](media/v2-update-explorer/example-queries.png#lightbox)

**Miara**: Lista rozwijana, która wyświetla wszystkie kolumny liczbowe (**podwaja**), których można użyć jako elementów dla bieżącego wykresu.

**Podział według**: Ta lista rozwijana zawiera wszystkie dostępne kolumny kategorii (ciągi) w modelu, według których można grupować dane. Można dodać maksymalnie pięć wyrazów, aby wyświetlić je na tej samej osi x. Wprowadź wymagane parametry, a następnie wybierz pozycję **Dodaj** , aby dodać nowy termin.

  [![zapytania i widok filtrowany](media/v2-update-explorer/s1-s2-preview-filtered-view.png)](media/v2-update-explorer/s1-s2-preview-filtered-view.png#lightbox)

Możesz pokazać i ukryć elementy w panelu wykresu, wybierając ikonę widoczne, jak pokazano na poniższej ilustracji. Aby całkowicie usunąć zapytania, wybierz czerwony znak **X**.

  [![anulować odszukaną i przefiltrowaną opcję](media/v2-update-explorer/s1-s2-preview-filtered-view-cancel.png)](media/v2-update-explorer/s1-s2-preview-filtered-view-cancel.png#lightbox)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [magazynie i przychodzących danych](./time-series-insights-update-storage-ingress.md) w wersji zapoznawczej Azure Time Series Insights.

- Przeczytaj dokument Time Series Insights w wersji zapoznawczej dotyczące [modelowania danych](./time-series-insights-update-tsm.md).

- Dowiedz się [, jak diagnozować i rozwiązywać problemy](./time-series-insights-update-how-to-troubleshoot.md) z wystąpieniem Time Series Insights.
