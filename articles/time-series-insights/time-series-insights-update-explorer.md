---
title: Wizualizowanie danych w Eksploratorze usługi Azure czas Series Insights w wersji zapoznawczej | Dokumentacja firmy Microsoft
description: W tym artykule opisano funkcje i opcje dostępne w aplikacji sieci web Eksploratora usługi Azure czas Series Insights w wersji zapoznawczej.
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 05/03/2019
ms.custom: seodec18
ms.openlocfilehash: 862465a7611f1a2bc65dbb0c49c4de512bd239de
ms.sourcegitcommit: 4891f404c1816ebd247467a12d7789b9a38cee7e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2019
ms.locfileid: "65442087"
---
# <a name="visualize-data-in-the-explorer-preview"></a>Wizualizowanie danych w programie explorer (wersja zapoznawcza)

W tym dokumencie opisano interfejs użytkownika funkcje interfejsu Azure czas Series Insights w wersji zapoznawczej [pokazowa aplikacja sieci web](https://insights.timeseries.azure.com/preview/demo). W szczególności omówiono w nim układ hostowanej przykładu, opcje dostosowywania interfejsu i nawigacji przy użyciu podanej wersji demonstracyjnej.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć pracę z Eksploratorem usługi Azure czas Series Insights w wersji zapoznawczej, musisz mieć:

* Masz środowisko usługi Time Series Insights Konfigurowanie. Aby dowiedzieć się więcej na temat aprowizowania wystąpienia Wypróbuj nasz [Azure czas Series Insights w wersji zapoznawczej](./time-series-insights-update-create-environment.md) samouczka.
* [Zapewnia dostęp do danych](./time-series-insights-data-access.md) do środowiska usługi Time Series Insights, który został utworzony dla konta. Można zapewnić dostęp do innych użytkowników oraz do siebie.
* Dodawanie źródła zdarzeń do środowiska usługi Time Series Insights w celu wypychania danych do środowiska:
  * Dowiedz się, [jak połączyć się z Centrum zdarzeń](./time-series-insights-how-to-add-an-event-source-eventhub.md)
  * Dowiedz się, [jak połączyć się z Centrum IoT hub](./time-series-insights-how-to-add-an-event-source-iothub.md)

## <a name="learn-about-the-preview-explorer"></a>Dowiedz się więcej o explorer (wersja zapoznawcza)

Eksplorator usługi Azure czas Series Insights w wersji zapoznawczej obejmuje następujące elementy:

[![Widok Eksploratora](media/v2-update-explorer/explorer-one.png)](media/v2-update-explorer/explorer-one.png#lightbox)

1. <a href="#environment-dropdown">**Panel środowiska**</a>: Wyświetla środowiska Azure TSI.
1. <a href="#navigation-menu">**Menu nawigacji**</a>: Umożliwia przełączanie między **analizy** i **modelu** stron.
1. <a href="#hierarchy-tree">**Drzewo hierarchia**</a>: Umożliwia wybranie określonego modelu i elementy danych do wykreślenia.
1. <a href="#preview-well">**Czas serii dobrze**</a>: Wyświetla elementów aktualnie wybrane dane w formacie tabeli przy użyciu kodowania kolorami.
1. <a href="#preview-chart">**Panel wykresu**</a>:  Wyświetla bieżący wykresu pracy.
1. <a href="#time-editor-panel">**Oś czasu**</a>:  Umożliwia modyfikowanie przedział czasu swojej pracy.
1. <a href="#navigation-panel">**Pasek aplikacji**</a>:  Zawiera opcje zarządzania użytkownika, takie jak bieżącej dzierżawy i pozwala na zmianę ustawień motywu i języka.

## <a name="environment-dropdown"></a>Lista rozwijana środowisk

Lista rozwijana środowisk Wyświetla wszystkie środowiska usługi Time Series Insights, do których masz dostęp do. Lista zawiera środowiska zgodnie z rzeczywistym użyciem (wersja zapoznawcza) i środowisk S1/S2 (ogólnie lub GA). 

1. Po prostu kliknij strzałkę listy rozwijanej obok wyświetlanych środowiska:

   [![Panel sterowania](media/v2-update-explorer/explorer-two.png)](media/v2-update-explorer/explorer-two.png#lightbox)

1. Następnie wybierz żądany środowiska.

## <a name="navigation-menu"></a>Menu nawigacji

  [![W menu nawigacji](media/v2-update-explorer/explorer-three.png)](media/v2-update-explorer/explorer-three.png#lightbox)

W menu nawigacji, można wybrać między dwa widoki:

* **Analizowanie**: Umożliwia wykresu i wykonywać zaawansowane analizy na modelowanych lub niemodelowanych szeregami czasowymi.
* **Model**: Umożliwia wypychanie nowe typy czasu Series Insights w wersji zapoznawczej, hierarchie i wystąpień do modelu usługi Time Series Insights.

## <a name="hierarchy-tree"></a>Drzewo hierarchii

Drzewo hierarchia Wyświetla elementy wybrane dane, w tym modele, określonych urządzeń i czujników na swoich urządzeniach.

### <a name="model-search-panel"></a>Model wyszukiwania Panelu

Panel wyszukiwania modelu pozwala na łatwe wyszukiwanie i przejdź hierarchii modelu szeregów czasowych, aby znaleźć wystąpień serii określony czas, które mają być wyświetlane na wykresie. Po wybraniu wystąpień, dodawane zarówno bieżącego wykresu, jak i dane oraz.

  [![Panel wyszukiwania modelu](media/v2-update-explorer/explorer-four.png)](media/v2-update-explorer/explorer-four.png#lightbox)

### <a name="model-authoring"></a>Tworzenie modelu

Azure czas Series Insights w wersji zapoznawczej obsługuje cały cykl tworzenia, odczytu, aktualizowania lub usuwania (CRUD) w swojej modelu szeregów czasowych.  

* **Typ modelu serii czasu**: Typy Series Insights czasu Włącz definiowania zmiennych lub formuły do wykonywania obliczeń. Są one skojarzone z danego wystąpienia usługi Time Series Insights. Typ może mieć co najmniej jednej zmiennej.
* **Czas hierarchii modelu szeregów**: Hierarchie to systematycznego organizacji danych. Hierarchie przedstawiać relacje między różnymi jednostkami danych usługi Time Series Insights.
* **Wystąpienie modelu serii czasu**: Wystąpienia są szeregów czasowych, samodzielnie. W większości przypadków są **DeviceID** lub **AssetID**, który jest unikatowy identyfikator elementu zawartości w środowisku.

Aby dowiedzieć się więcej na temat modelu szeregów czasowych, zobacz [modeli szeregów razy](./time-series-insights-update-tsm.md).

## <a name="preview-well"></a>Dobrze (wersja zapoznawcza)

Również wyświetla pola wystąpienia i inne metadane skojarzone z wybranego wystąpienia usługi TSI. Pole wyboru po prawej stronie umożliwiają ukryć lub wyświetlić określone wystąpienia z bieżącego wykresu. Można również usunąć elementy określonych danych z bieżące dane, klikając czerwoną **Usuń** (Kosza) kontrolki w lewym rogu elementu.

  [![Podgląd dobrze](media/v2-update-explorer/explorer-five.png)](media/v2-update-explorer/explorer-five.png#lightbox)

Można także ponownie skonfigurować układ swoje **analizy** strony wykresu, wybierając ikonę elipsy w prawym górnym rogu:

  [![Opcje układu telemetrii](media/v2-update-explorer/explorer-six.png)](media/v2-update-explorer/explorer-six.png#lightbox)

> [!NOTE]
> Jeśli zobaczysz następujący komunikat, podczas wybrany przedział czasu wystąpienia nie ma żadnych danych. Aby rozwiązać ten problem, można zwiększyć zakres czasu lub upewnij się, że wystąpienie jest wypychanie danych.
>
> ![Nie powiadomień danych](media/v2-update-explorer/explorer-seven.png)

## <a name="preview-chart"></a>Podgląd wykresu

Za pomocą wykresu można wyświetlić wystąpienia usługi TSI jako wiersze. Można zwinąć panelu środowiska, modelu danych i godzina w Panelu sterowania zakresu, klikając kontrolki sieci web, aby powiększyć wykresu.

  [![Omówienie wykresu (wersja zapoznawcza)](media/v2-update-explorer/explorer-eight.png)](media/v2-update-explorer/explorer-eight.png#lightbox)

1. **Wybrany zakres dat**: Określa elementy danych, które są dostępne dla wizualizacji.

1. **Wewnętrzny narzędzia suwaka zakresu dat**: Za pomocą dwóch formantów punktu końcowego, przeciągając je w okresie żądany czas.

1. **Czas formantu Zwiń zakresu**: Zwija i rozwija Edytor panelu zakresu czasu.

1. **Formant formatu osi y**: Przełączanie po kolei dostępne opcje osi y w widoku:

    * `Default`: Każdy wiersz zawiera poszczególne osi y.
    * `Stacked`: Umożliwia stosu wielu wierszy w tej samej osi y, przy użyciu danych osi y zmiany na podstawie wiersza zaznaczone.
    * `Shared`: Wyświetlane razem wszystkie dane osi y.

1. **Bieżący element danych**: Element aktualnie wybrane dane i jego szczegóły.

Można przejść do szczegółów w wycinka danych określonego przez kliknięcie lewym przyciskiem myszy punkt danych na bieżącego wykresu, a następnie przeciągając zaznaczonego obszaru do wybranego punktu końcowego. Kliknij prawym przyciskiem myszy obszar wygaszone, wybrane, a następnie kliknij przycisk **powiększenia** pokazany na tej ilustracji:

  [![Powiększenie wykresu (wersja zapoznawcza)](media/v2-update-explorer/explorer-nine.png)](media/v2-update-explorer/explorer-nine.png#lightbox)

Po wykonaniu **powiększenia** akcji, zobaczysz wybrany zestaw danych. Kliknij formant formatu osi y, aby przechodzić między trzy reprezentacje osi y dane usługi Time Series Insights.

  [![Podgląd wykresu osi y](media/v2-update-explorer/explorer-ten.png)](media/v2-update-explorer/explorer-ten.png#lightbox)

Tutaj można zobaczyć przykład osiami udostępnione:

  [![Wersja zapoznawcza udostępnione osi y](media/v2-update-explorer/explorer-eleven.png)](media/v2-update-explorer/explorer-eleven.png#lightbox)

## <a name="time-editor-panel"></a>Panel Edytor czasu

Podczas pracy z czasu Series Insights w wersji zapoznawczej, najpierw należy wybrać przedział czasu. Określa zakres czasu wybrany określa zestaw danych, która jest dostępna do manipulowania widżetów czasu Series Insights w wersji zapoznawczej. Następujące kontrolki sieci web są dostępne w czasie Series Insights w wersji zapoznawczej dotyczące wybierania usługi przedział czasu pracy.

  [![Panel wyboru czasu](media/v2-update-explorer/explorer-twelve.png)](media/v2-update-explorer/explorer-twelve.png#lightbox)

1. **Narzędzie suwaka zakresu dat wewnętrzny**: Za pomocą dwóch formantów punktu końcowego, przeciągając je w okresie żądany czas. Ten zakres dat wewnętrzny jest ograniczony przez formant suwaka zakresu dat zewnętrznego.

1. **Zwiększać i zmniejszać przycisków zakresu dat**: Zwiększanie lub zmniejszanie swoje przedział czasu, wybierając przycisk albo dla interwału, który ma.

1. **Czas formantu Zwiń zakresu**: Tego formantu sieci web pozwala na ukrywanie wszystkich kontrolek z wyjątkiem narzędzia suwaka zakresu dat wewnętrzny.

1. **Kontrolka suwaka zakresu dat zewnętrzne**: Użyj kontrolki punktu końcowego, aby wybrać zakres zewnętrzne daty, która będzie dostępna dla kontrolki zakres dat wewnętrzny.

1. **Krótkie okresy zakres dat rozwijanej**: Umożliwia szybkie przełączanie się między wstępnie ustawiony czas zakresu wybrane opcje, takie jak ostatnie **30 minut**, **ostatnich 12 godzin**, lub **niestandardowego zakresu**. Zmiana ta wartość zmienia się również zakresy dostępne interwał omówione w narzędziu suwaka rozmiar interwału.

1. **Rozmiar interwału suwak**: Umożliwia powiększanie i pomniejszanie odstępach czasu za pośrednictwem tego samego przedział czasu. Ta akcja zapewnia dokładniejszą kontrolę ruchu między wycinkami dużych czasu. Wyświetla smooth trendów w dół wycinki tak małej, jak milisekund, dzięki czemu możesz widzieć cenowe szczegółowe, wysokiej rozdzielczości, dane. Domyślne suwaka punkt początkowy ustawiono jako najbardziej optymalną wgląd w dane z zaznaczenia rozdzielczości, szybkości zapytań i stopień szczegółowości.

1. **Zakres do i z formantu sieci web dat**: Za pomocą tego formantu sieci web można łatwo kliknąć i wybrać odpowiednią datę i zakresy czasu. Kontrolka umożliwia również przełączać się między różnych strefach czasowych. Po wprowadzeniu zmian w celu zastosowania do Twojego bieżącego obszaru roboczego wybierz **Zapisz**.

   [![Do i z panelu wyboru](media/v2-update-explorer/explorer-thirteen.png)](media/v2-update-explorer/explorer-thirteen.png#lightbox)

## <a name="navigation-panel"></a>Panel nawigacji

Panel nawigacyjny czasu Series Insights w wersji zapoznawczej pojawia się w górnej części aplikacji TSI. Zapewnia następujące funkcje.

### <a name="current-session-share-link-control"></a>Kontrola łącza udział w bieżącej sesji

  [![Ikona udostępniania](media/v2-update-explorer/explorer-fifteen.png)](media/v2-update-explorer/explorer-fifteen.png#lightbox)

Wybierz nową **udostępnianie** ikonę, aby udostępniać link z adresem URL swojemu zespołowi.

  [![Udostępnij adres URL wystąpienia](media/v2-update-explorer/url-share.png)](media/v2-update-explorer/url-share.png#lightbox)

### <a name="tenant-section"></a>Sekcja dzierżawy

  [![Wybór dzierżawy](media/v2-update-explorer/explorer-sixteen.png)](media/v2-update-explorer/explorer-sixteen.png#lightbox)

* Wyświetla bieżące informacje o koncie logowania usługi Time Series Insights.
* Umożliwia przechodzenie między dostępne motywy usługi Time Series Insights.
* Służy do wyświetlania podglądu [pokazowa aplikacja sieci web](https://insights.timeseries.azure.com/preview/demo).

### <a name="theme-selection"></a>Wybór motywu

Aby wybrać nowy motyw, kliknij ikonę Twojego profilu, znajduje się w prawym górnym rogu. Następnie wybierz **Zmień motyw**.

  [![Wybór motywu](media/v2-update-explorer/theme-selection.png)](media/v2-update-explorer/theme-selection.png#lightbox)

> [!TIP]
> Wybór języka jest również dostępna po kliknięciu ikony Twojego profilu.

Azure czas Series Insights w wersji zapoznawczej obsługuje dwa motywy:

* **Motyw jasny**: Motyw domyślny, przedstawione w tym dokumencie.
* **Motyw ciemny**:  Renderuje Eksploratora, jak pokazano poniżej:

  [![Wybrany motyw ciemny](media/v2-update-explorer/explorer-seventeen.png)](media/v2-update-explorer/explorer-seventeen.png#lightbox)

## <a name="s1s2-environment-controls"></a>Kontrolki środowiska S1/S2

### <a name="preview-terms-panel"></a>Panel terminów (wersja zapoznawcza)

Ta sekcja dotyczy tylko do istniejących środowisk S1/S2, które próbują w Interfejsie użytkownika zaktualizowane przy użyciu Eksploratora. Można używać w połączeniu GA produktu i w wersji zapoznawczej. Niektóre funkcje z istniejącego interfejsu użytkownika do dodaliśmy zaktualizowane explorer, ale można uzyskać pełnego środowiska interfejsu użytkownika dla środowiska S1/S2 w istniejących Eksploratora usługi Time Series Insights.  

Audytów hierarchii zostanie wyświetlony panel terminów usługi Time Series Insights, w którym definiowania kwerend w danym środowisku. Dzięki temu można filtrować dane na podstawie predykatu.

  [![Gdy zapytanie panelu](media/v2-update-explorer/explorer-eighteen.png)](media/v2-update-explorer/explorer-eighteen.png#lightbox)

Panel Edytor warunków czasu Series Insights w wersji zapoznawczej przyjmuje następujące parametry:

**Where**: Where — klauzula umożliwia szybkie filtr zdarzeń za pomocą zbiór operandów wymienione w poniższej tabeli. Jeśli wyszukiwania, wybierając operand, predykat jest automatycznie aktualizowana, oparte na tym wyszukiwaniem. Operand obsługiwane typy:

| Operacja | Obsługiwane typy   | Uwagi |
| --- | --- | --- |
| `<`, `>`, `<=`, `>=` | Double, DateTime, TimeSpan | |
| `=`, `!=`, `<>` | String, Bool, Double, DateTime, przedział czasu, wartość NULL |
| `IN` | String, Bool, Double, DateTime, przedział czasu, wartość NULL | Wszystkie argumenty powinny być tego samego typu lub być stała NULL. |
| `HAS` | Ciąg | Po prawej stronie dozwolone są tylko stałe literałów. Pusty ciąg i wartości NULL są niedozwolone. |

Więcej informacji na temat zapytania obsługiwane operacje i typy danych, czytając [wyrażenie serii czasu (TSX)](https://docs.microsoft.com/rest/api/time-series-insights/preview-tsx).

### <a name="examples-of-where-clauses"></a>Przykłady gdzie klauzule

  [![Gdzie przykłady klauzuli](media/v2-update-explorer/explorer-nineteen.png)](media/v2-update-explorer/explorer-nineteen.png#lightbox)

**Miara**: Menu rozwijane wyświetli wszystkie kolumny liczbowe (**wartości podwójnej precyzji**) można używać jako elementy dla bieżącego wykresu.

**Podziel według**: Tej listy rozwijanej wyświetla wszystkie dostępne podzielonych na kategorie kolumny (ciągi) w modelu, można grupować dane przez. Możesz dodać maksymalnie pięć warunki, aby wyświetlić na tej samej osi x. Wprowadź odpowiednie parametry, a następnie wybierz pozycję **Dodaj** można dodać nowej termin.

  [![Zapytanie o i filtrowanego widoku, jeden](media/v2-update-explorer/explorer-twenty.png)](media/v2-update-explorer/explorer-twenty.png#lightbox)

Można pokazać i ukryć elementy w panelu wykresu, wybierając ikonę widoczne, jak pokazano na poniższej ilustracji. Zapytania można całkowicie usunąć, klikając czerwoną **X**.

  [![Wyświetl poszukiwanych i filtrowane dwóch](media/v2-update-explorer/explorer-twenty-one.png)](media/v2-update-explorer/explorer-twenty-one.png#lightbox)

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [magazynu i transferu danych przychodzących](./time-series-insights-update-storage-ingress.md) w wersji zapoznawczej Azure czas serii szczegółowych informacji.

- Przeczytaj dokument czasu Series Insights w wersji zapoznawczej na [modelowania danych](./time-series-insights-update-tsm.md).

- Dowiedz się, [sposób diagnozowania i rozwiązywania problemów](./time-series-insights-update-how-to-troubleshoot.md) wystąpienia usługi Time Series Insights.
