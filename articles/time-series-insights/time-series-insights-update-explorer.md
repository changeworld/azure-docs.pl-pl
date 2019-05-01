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
ms.date: 12/03/2018
ms.custom: seodec18
ms.openlocfilehash: d348592589f448dab9b8b4f3a1a3eb286d464417
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64723456"
---
# <a name="visualize-data-in-the-explorer-preview"></a>Wizualizowanie danych w programie explorer (wersja zapoznawcza)

W tym artykule opisano funkcje i opcje dostępne w wersji zapoznawczej Azure czas serii Insights [aplikacji sieci web w Eksploratorze](https://insights.timeseries.azure.com/preview/samples).

## <a name="prerequisites"></a>Wymagania wstępne

Przed skorzystaniem z Eksploratora usługi Azure czas Series Insights w wersji zapoznawczej, musisz mieć:

* Masz środowisko usługi Time Series Insights Konfigurowanie. Aby uzyskać więcej informacji, zobacz [Samouczek: Wersja zapoznawcza usługi Azure Time Series Insights](./time-series-insights-update-create-environment.md).
* Zapewnia dostęp do danych do środowiska usługi Time Series Insights, który został utworzony dla konta. Można zapewnić dostęp do innych użytkowników oraz do siebie.
* Dodawanie źródła zdarzeń do środowiska usługi Time Series Insights w celu wypychania danych do środowiska.

## <a name="learn-about-the-azure-time-series-insights-preview-explorer"></a>Dowiedz się więcej o Eksploratorze usługi Azure czas Series Insights w wersji zapoznawczej

  ![jeden Explorer][1]

Eksplorator usługi Azure czas Series Insights w wersji zapoznawczej obejmuje następujące elementy:

* **Pasek nawigacyjny**: Umożliwia przechodzenie między stronami analizy i modelu.
* **Drzewo hierarchia**: Umożliwia wybranie określonych danych elementów do wykreślenia.
* **Czas serii dobrze**: Wyświetla elementów aktualnie wybranych danych.
* **Panel wykresu**: Wyświetla bieżący wykresu pracy.
* **Oś czasu**: Umożliwia modyfikowanie przedział czasu swojej pracy.
* **Pasek aplikacji**: Zawiera opcje zarządzania użytkownika, takie jak bieżącej dzierżawy i pozwala na zmianę ustawień motywu i języka.

## <a name="time-series-insights-preview-environment-panel"></a>Panel środowiska Series Insights w wersji zapoznawczej czasu

Na panelu środowiska są wyświetlane wszystkie środowiska usługi Time Series Insights, do których masz dostęp do. Lista zawiera środowiska zgodnie z rzeczywistym użyciem (wersja zapoznawcza) i S1/S2 środowisk (GA). Po prostu kliknij środowiska usługi Time Series Insights, które chcesz użyć.

  ![dwa Explorer][2]

## <a name="time-series-insights-preview-navigation-menu"></a>Menu nawigacji Series Insights w wersji zapoznawczej czasu

  ![trzy Explorer][3]

Z menu nawigacji można przełączać się między aplikacjami usługi Time Series Insights:

* **Analizowanie**: Umożliwia wykresu i wykonywać zaawansowane analizy na modelowanych lub niemodelowanych szeregami czasowymi.

* **Model**: Umożliwia wypychanie nowe typy czasu Series Insights w wersji zapoznawczej, hierarchie i wystąpień do modelu usługi Time Series Insights.

## <a name="time-series-insights-preview-model-authoring"></a>Time Series Insights w wersji zapoznawczej modelu tworzenia

Za pomocą tej aplikacji można wykonywać operacje tworzenia, odczytu, aktualizowania lub usuwania (CRUD) w swojej modelu szeregów czasowych.  

* **Typ modelu serii czasu**: Typy Series Insights czasu Włącz definiowania zmiennych lub formuły do wykonywania obliczeń. Są one skojarzone z danego wystąpienia usługi Time Series Insights. Typ może mieć co najmniej jednej zmiennej.
* **Czas hierarchii modelu szeregów**: Hierarchie to systematycznego organizacji danych. Hierarchie przedstawiać relacje między różnymi jednostkami danych usługi Time Series Insights.
* **Wystąpienie modelu serii czasu**: Wystąpienia są szeregów czasowych, samodzielnie. W większości przypadków są DeviceID lub AssetID, który jest unikatowy identyfikator elementu zawartości w środowisku.

Aby dowiedzieć się więcej na temat modelu szeregów czasowych, zobacz [modeli szeregów razy](./time-series-insights-update-tsm.md).

## <a name="time-series-insights-preview-model-search-panel"></a>Czas Series Insights w wersji zapoznawczej modelu wyszukiwania Panelu

Panel wyszukiwania modelu pozwala na łatwe wyszukiwanie i przejdź hierarchii modelu szeregów czasowych, aby znaleźć wystąpień serii określony czas, które mają być wyświetlane na wykresie. Po wybraniu wystąpień, dodawane zarówno bieżącego wykresu, jak i dane oraz.

  ![cztery Explorer][4]

## <a name="time-series-insights-preview-well"></a>Dobrze Time Series Insights w wersji zapoznawczej

Również wyświetla pola wystąpienia i inne metadane skojarzone z wybranym wystąpienia serii. Pole wyboru po prawej stronie umożliwiają ukryć lub wyświetlić określone wystąpienia z bieżącego wykresu. Można również usunąć elementy określonych danych z bieżących danych, klikając kontrolki czerwony znak x z prawej strony elementu.

  ![pięć Explorer][5]

Można również oknach panelu telemetrię można również pobrać lepiej widok pionowy elementów w Twoich danych.

  ![Eksplorator 6][6]

> [!NOTE]
> Jeśli zobaczysz następujący komunikat, podczas wybrany przedział czasu wystąpienia nie ma żadnych danych. Aby rozwiązać ten problem, można zwiększyć zakres czasu lub upewnij się, że wystąpienie jest wypychanie danych.
>
> ![Eksplorator 7][7]

## <a name="time-series-insights-preview-chart"></a>Wykres Series Insights w wersji zapoznawczej w czasie

Za pomocą wykresu można wyświetlić wystąpienia serii czasu jako wiersze. Można zwinąć panelu środowiska, modelu danych i godzina w Panelu sterowania zakresu, klikając kontrolki sieci web, aby powiększyć wykresu.

  ![Eksplorator 8][8]

1. **Wybrany zakres dat**: Określa elementy danych, które są dostępne dla wizualizacji.

1. **Wewnętrzny narzędzia suwaka zakresu dat**: Za pomocą dwóch formantów punktu końcowego, przeciągając je w okresie żądany czas.

1. **Czas formantu Zwiń zakresu**: Zwija i rozwija Edytor panelu zakresu czasu.

1. **Formant formatu osi y**: Przełączanie po kolei dostępne opcje osi y w widoku:

    * `Default`: Każdy wiersz zawiera poszczególne osi y.
    * `Stacked`: Umożliwia stosu wielu wierszy w tej samej osi y, przy użyciu danych osi y zmiany na podstawie wiersza zaznaczone.
    * `Shared`: Wyświetlane razem wszystkie dane osi y.

1. **Bieżący element danych**: Element aktualnie wybrane dane i jego szczegóły.

Można przejść do szczegółów w wycinka danych określonego przez kliknięcie lewym przyciskiem myszy punkt danych na bieżącego wykresu, a następnie przeciągając zaznaczonego obszaru do wybranego punktu końcowego. Kliknij prawym przyciskiem myszy obszar wygaszone, wybrane, a następnie kliknij przycisk powiększenia, jak pokazano to na poniższej ilustracji:

  ![dziewięć Explorer][9]

Po wykonaniu akcji powiększania, zobaczysz wybrany zestaw danych. Kliknij formant formatu osi y, aby przechodzić między trzy reprezentacje osi y dane usługi Time Series Insights.

  ![dziesięć Explorer][10]

Tutaj można zobaczyć przykład osiami udostępnione:

  ![Eksplorator eleven][11]

## <a name="time-series-insights-preview-time-editor-panel"></a>Panel Edytor czasu Series Insights w wersji zapoznawczej czasu

Podczas pracy z czasu Series Insights w wersji zapoznawczej, najpierw należy wybrać przedział czasu. Określa zakres czasu wybrany określa zestaw danych, która jest dostępna do manipulowania widżetów czasu Series Insights w wersji zapoznawczej. Następujące kontrolki sieci web są dostępne w czasie Series Insights w wersji zapoznawczej dotyczące wybierania usługi przedział czasu pracy.

  ![dwanaście Explorer][12]

1. **Narzędzie suwaka zakresu dat wewnętrzny**: Za pomocą dwóch formantów punktu końcowego, przeciągając je w okresie żądany czas. Ten zakres dat wewnętrzny jest ograniczony przez formant suwaka zakresu dat zewnętrznego.

1. **Zwiększać i zmniejszać przycisków zakresu dat**: Zwiększanie lub zmniejszanie swoje przedział czasu, wybierając przycisk albo dla interwału, który ma.

1. **Czas formantu Zwiń zakresu**: Tego formantu sieci web pozwala na ukrywanie wszystkich kontrolek z wyjątkiem narzędzia suwaka zakresu dat wewnętrzny.

1. **Kontrolka suwaka zakresu dat zewnętrzne**: Użyj kontrolki punktu końcowego, aby wybrać zakres zewnętrzne daty, która będzie dostępna dla kontrolki zakres dat wewnętrzny.

1. **Krótkie okresy daty rozwijanej zakresu**: Pozwala na szybkie przełączanie się między wstępnie ustawiony czas zakresu wybrane opcje, takie jak ostatnie 30 minut, z ostatnich 12 godzin lub niestandardowy zakres. Zmiana ta wartość zmienia się również zakresy dostępne interwał omówione w narzędziu suwaka rozmiar interwału.

1. **Rozmiar interwału suwak**: Umożliwia powiększanie i pomniejszanie odstępach czasu za pośrednictwem tego samego przedział czasu. Ta akcja zapewnia dokładniejszą kontrolę ruchu między wycinkami dużych czasu. Wyświetla smooth trendów w dół wycinki tak małej, jak milisekund, dzięki czemu możesz widzieć cenowe szczegółowe, wysokiej rozdzielczości, dane. Domyślne suwaka punkt początkowy ustawiono jako najbardziej optymalną wgląd w dane z zaznaczenia rozdzielczości, szybkości zapytań i stopień szczegółowości.

1. **Zakres do i z formantu sieci web dat**: Za pomocą tego formantu sieci web można łatwo kliknąć i wybrać odpowiednią datę i zakresy czasu. Kontrolka umożliwia również przełączać się między różnych strefach czasowych. Po wprowadzeniu zmian w celu zastosowania do Twojego bieżącego obszaru roboczego wybierz **Zapisz**.

   ![Eksplorator trzynaście][13]

## <a name="time-series-insights-preview-navigation-panel"></a>Panel nawigacji Series Insights w wersji zapoznawczej czasu

Panel nawigacyjny czasu Series Insights w wersji zapoznawczej oferuje następujące funkcje:

  ![Eksplorator czternastu][14]

### <a name="current-session-share-link-control"></a>Kontrola łącza udział w bieżącej sesji

  ![Eksplorator 15][15]

Wybierz łącze kontrolki sieci web (wyróżnione), można wygenerować adresu URL, aby zapisać lub udostępnić bieżącej sesji usługi Azure Time Series Insights, w tym:

* Obecnie wybranego zakresu czasu
* Rozmiar interwału zaznaczony
* Aktualnie wybrane dane również

### <a name="tenant-section"></a>Sekcja dzierżawy

  ![Eksplorator 16][16]

* Wyświetla bieżące informacje o koncie logowania usługi Time Series Insights.
* Umożliwia przechodzenie między dostępne motywy usługi Time Series Insights.

### <a name="theme-selection"></a>Wybór motywu

Azure czas Series Insights w wersji zapoznawczej obsługuje dwa motywy:

* **Motyw jasny**: Motyw domyślny, przedstawione w tym dokumencie.
* **Motyw ciemny**:  Renderuje Eksploratora, jak pokazano poniżej:

  ![Eksplorator siedemnastu][17]

W tym miejscu możesz również zmienić między obsługiwanych języków.

## <a name="s1s2-environment-controls"></a>Kontrolki środowiska S1/S2

### <a name="time-series-insights-preview-terms-panel"></a>Panel terminów Series Insights w wersji zapoznawczej czasu

Ta sekcja dotyczy tylko do istniejących środowisk S1/S2, które próbują w Interfejsie użytkownika zaktualizowane przy użyciu Eksploratora. Można używać w połączeniu GA produktu i w wersji zapoznawczej. Niektóre funkcje z istniejącego interfejsu użytkownika do dodaliśmy zaktualizowane explorer, ale można uzyskać pełnego środowiska interfejsu użytkownika dla środowiska S1/S2 w istniejących Eksploratora usługi Time Series Insights.  

Audytów hierarchii zostanie wyświetlony panel terminów usługi Time Series Insights, w którym definiowania kwerend w danym środowisku. Dzięki temu można filtrować dane na podstawie predykatu.

  ![Eksplorator 18][18]

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

  ![Eksplorator tysiąc][19]

**Miara**: Ta lista rozwijana zawiera wszystkie kolumny liczbowe (**wartości podwójnej precyzji**), można użyć jako elementy, dla bieżącego wykresu.

**Podziel według**: Tej listy rozwijanej wyświetla wszystkie dostępne podzielonych na kategorie kolumny (ciągi) w modelu, można grupować dane przez. Możesz dodać maksymalnie pięć warunki, aby wyświetlić na tej samej osi x. Wprowadź odpowiednie parametry, a następnie wybierz pozycję **Dodaj** można dodać nowej termin.

  ![dwadzieścia Explorer][20]

Można pokazać i ukryć elementy w panelu wykresu, wybierając ikonę widoczne, jak pokazano na poniższej ilustracji. Zapytania można całkowicie usunąć, klikając czerwoną **x**.

  ![Eksplorator dwadzieścia jednego][21]

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [magazynu i transferu danych przychodzących](./time-series-insights-update-storage-ingress.md) w wersji zapoznawczej Azure czas serii szczegółowych informacji.

- Przeczytaj dokument czasu Series Insights w wersji zapoznawczej na [modelowania danych](./time-series-insights-update-tsm.md).

- Dowiedz się, [sposób diagnozowania i rozwiązywania problemów](./time-series-insights-update-how-to-troubleshoot.md) wystąpienia usługi Time Series Insights.

<!-- Images -->
[1]: media/v2-update-explorer/explorer-one.png
[2]: media/v2-update-explorer/explorer-two.png
[3]: media/v2-update-explorer/explorer-three.png
[4]: media/v2-update-explorer/explorer-four.png
[5]: media/v2-update-explorer/explorer-five.png
[6]: media/v2-update-explorer/explorer-six.png
[7]: media/v2-update-explorer/explorer-seven.png
[8]: media/v2-update-explorer/explorer-eight.png
[9]: media/v2-update-explorer/explorer-nine.png
[10]: media/v2-update-explorer/explorer-ten.png
[11]: media/v2-update-explorer/explorer-eleven.png
[12]: media/v2-update-explorer/explorer-twelve.png
[13]: media/v2-update-explorer/explorer-thirteen.png
[14]: media/v2-update-explorer/explorer-fourteen.png
[15]: media/v2-update-explorer/explorer-fifteen.png
[16]: media/v2-update-explorer/explorer-sixteen.png
[17]: media/v2-update-explorer/explorer-seventeen.png
[18]: media/v2-update-explorer/explorer-eighteen.png
[19]: media/v2-update-explorer/explorer-nineteen.png
[20]: media/v2-update-explorer/explorer-twenty.png
[21]: media/v2-update-explorer/explorer-twenty-one.png
