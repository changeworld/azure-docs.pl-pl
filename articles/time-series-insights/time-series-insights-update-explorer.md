---
title: Wizualizowanie danych w Eksploratorze usługi Azure czas Series Insights w wersji zapoznawczej | Dokumentacja firmy Microsoft
description: W tym artykule opisano funkcje i opcje dostępne w aplikacji sieci web Eksploratora usługi Azure czas Series Insights w wersji zapoznawczej.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 05/03/2019
ms.custom: seodec18
ms.openlocfilehash: c4f3053063ce33d2777387da2c53effd61b05f1a
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/30/2019
ms.locfileid: "66399872"
---
# <a name="visualize-data-in-the-explorer-preview"></a>Wizualizowanie danych w programie explorer (wersja zapoznawcza)

W tym dokumencie opisano interfejs użytkownika i funkcje środowiska użytkownika i interfejs Azure czas Series Insights w wersji zapoznawczej [pokazowa aplikacja sieci web](https://insights.timeseries.azure.com/preview/demo). W szczególności omówiono w nim układ hostowanej przykładu, opcje dostosowywania interfejsu i nawigacji przy użyciu podanej wersji demonstracyjnej.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć pracę z Eksploratorem usługi Azure czas Series Insights w wersji zapoznawczej, musisz mieć:

* Masz środowisko usługi Time Series Insights Konfigurowanie. Aby dowiedzieć się więcej na temat aprowizowania wystąpienia, spróbuj [Azure czas Series Insights w wersji zapoznawczej](./time-series-insights-update-create-environment.md) samouczka.
* [Zapewnia dostęp do danych](./time-series-insights-data-access.md) do środowiska usługi Time Series Insights, który został utworzony dla konta. Można zapewnić dostęp do innych użytkowników oraz do siebie.
* Dodawanie źródła zdarzeń do środowiska usługi Time Series Insights w celu wypychania danych do środowiska:
  * Dowiedz się, [jak połączyć się z Centrum zdarzeń](./time-series-insights-how-to-add-an-event-source-eventhub.md).
  * Dowiedz się, [jak połączyć się z Centrum IoT hub](./time-series-insights-how-to-add-an-event-source-iothub.md).

## <a name="learn-about-the-preview-explorer"></a>Dowiedz się więcej o explorer (wersja zapoznawcza)

Eksplorator usługi Azure czas Series Insights w wersji zapoznawczej obejmuje następujące elementy:

[![Widok Eksploratora](media/v2-update-explorer/explorer-one.png)](media/v2-update-explorer/explorer-one.png#lightbox)

- <a href="#environment-drop-down-list">Panel środowiska</a>: Wyświetla środowiska usługi Azure Time Series Insights.
- <a href="#navigation-menu">Menu nawigacji</a>: Umożliwia przełączanie między **analizy** i **modelu** stron.
- <a href="#hierarchy-tree">Drzewo hierarchia</a>: Użyj go, aby wybrać określone elementy modelu i danych do wykreślenia.
- <a href="#preview-well">Czas serii dobrze</a>: Wyświetla elementów aktualnie wybrane dane w formacie tabeli z kolorami.
- <a href="#preview-chart">Panel wykresu</a>: Wyświetla bieżący wykresu pracy.
- <a href="#time-editor-panel">Oś czasu</a>: Użyj go, aby zmodyfikować przedział czasu swojej pracy.
- <a href="#navigation-panel">Pasek aplikacji</a>: Zawiera opcje zarządzania użytkownika, takie jak bieżącej dzierżawy. Służy do zmiany ustawień języka i motyw.

## <a name="environment-drop-down-list"></a>Środowisko listy rozwijanej

Na liście rozwijanej środowiska zostaną wyświetlone wszystkie środowiska usługi Time Series Insights, do których masz dostęp do. Lista zawiera środowiska zgodnie z rzeczywistym użyciem, takich jak serie czasu Insights (wersja zapoznawcza). Lista zawiera także S1/S2 środowisk, które są ogólnie dostępne.

1. Wybierz strzałkę listy rozwijanej obok wyświetlanych środowiska.

   [![Panel sterowania](media/v2-update-explorer/explorer-two.png)](media/v2-update-explorer/explorer-two.png#lightbox)

1. Następnie wybierz środowisko, które chcesz.

## <a name="navigation-menu"></a>Menu nawigacji

  [![W menu nawigacji](media/v2-update-explorer/explorer-three.png)](media/v2-update-explorer/explorer-three.png#lightbox)

Użyj menu nawigacji, aby wybrać między dwa widoki:

* **Analizowanie**: Umożliwia ona wykresów i wykonywać zaawansowane analizy na modelowanych lub niemodelowanych szeregami czasowymi.
* **Model**: Dzięki niemu Wypchnij nowe typy czasu Series Insights w wersji zapoznawczej, hierarchie i wystąpień do modelu usługi Time Series Insights.

## <a name="hierarchy-tree"></a>Drzewo hierarchii

Drzewo hierarchia Wyświetla elementy wybrane dane, które zawierają modele, określonych urządzeń i czujników na swoich urządzeniach.

### <a name="model-search-panel"></a>Model wyszukiwania Panelu

Za pomocą panelu Wyszukiwanie modelu można łatwo wyszukiwać i przejdź hierarchii modelu szeregów czasowych, aby znaleźć wystąpień serii określony czas, które mają być wyświetlane na wykresie. Po zaznaczeniu wystąpień są dodawane do bieżącego wykresu i dane oraz.

  [![Panel wyszukiwania modelu](media/v2-update-explorer/explorer-four.png)](media/v2-update-explorer/explorer-four.png#lightbox)

### <a name="model-authoring"></a>Tworzenie modelu

Pełna obsługa Azure czas Series Insights w wersji zapoznawczej służy do tworzenia, odczytu, aktualizacji i usuwania operacji (CRUD) w swojej modelu szeregów czasowych.

* **Typ modelu serii czasu**: Typy usługi Time Series Insights umożliwia definiowanie zmiennych lub formuł do wykonywania obliczeń. Są one skojarzone z danego wystąpienia usługi Time Series Insights. Typ może mieć co najmniej jednej zmiennej.
* **Czas hierarchii modelu szeregów**: Hierarchie to systematycznego organizacji danych. Hierarchie przedstawiać relacje między różnymi jednostkami danych usługi Time Series Insights.
* **Wystąpienie modelu serii czasu**: Wystąpienia są szeregów czasowych, samodzielnie. W większości przypadków są one **DeviceID** lub **AssetID**, który jest unikatowy identyfikator elementu zawartości w środowisku.

Aby dowiedzieć się więcej na temat modelu szeregów czasowych, zobacz [modeli szeregów razy](./time-series-insights-update-tsm.md).

## <a name="preview-well"></a>Dobrze (wersja zapoznawcza)

Również wyświetla pola wystąpienia i inne metadane skojarzone z wybranego wystąpienia usługi Time Series Insights. Zaznaczając pole wyboru po prawej stronie, można ukryć lub wyświetlić określone wystąpienia z bieżącego wykresu. Elementy określone dane mogą również usuwać z bieżących danych, dobrze, wybierając czerwoną **Usuń** (Kosza) kontroli po lewej stronie elementu.

  [![Podgląd dobrze](media/v2-update-explorer/explorer-five.png)](media/v2-update-explorer/explorer-five.png#lightbox)

Aby zmienić konfigurację układ swoje **analizy** strony wykresu, wybierz ikonę elipsy w prawym górnym rogu:

  [![Opcje układu telemetrii](media/v2-update-explorer/explorer-six.png)](media/v2-update-explorer/explorer-six.png#lightbox)

> [!NOTE]
> Jeśli zobaczysz następujący komunikat, wystąpienie nie zawiera żadnych danych podczas wybrany przedział czasu. Aby rozwiązać ten problem, Zwiększ zakres czasu lub upewnij się, że wystąpienie jest wypychanie danych.
>
> ![Nie powiadomień danych](media/v2-update-explorer/explorer-seven.png)

## <a name="preview-chart"></a>Podgląd wykresu

Za pomocą wykresu można wyświetlić wystąpienia usługi Time Series Insights jako wiersze. Panelu środowiska, modelu danych i godzina w Panelu sterowania zakresu można zwinąć, wybierając kontrolki sieci web, aby powiększyć wykresu.

  [![Omówienie wykresu (wersja zapoznawcza)](media/v2-update-explorer/explorer-eight.png)](media/v2-update-explorer/explorer-eight.png#lightbox)

- **Wybrany zakres dat**: Określa elementy danych, które są dostępne dla wizualizacji.

- **Narzędzie suwaka zakresu wewnętrznego data**: Za pomocą dwóch formantów punktu końcowego, przeciągając je za pośrednictwem przedział czasu, który ma.

- **Czas formantu Zwiń zakresu**: Zwija i rozwija Edytor panelu zakresu czasu.

- **Formant formatu osi y**: Przełączanie po kolei y dostępne opcje wyświetlania:

    * `Default`: Każdy wiersz zawiera poszczególne osi y.
    * `Stacked`: Dzięki niemu stosu wielu wierszy w tej samej osi y, przy użyciu danych osi y zmiany na podstawie wiersza zaznaczone.
    * `Shared`: Wyświetlane razem wszystkie dane osi y.

- **Bieżący element danych**: Element aktualnie wybrane dane i jego szczegóły.

Aby przejść do dalszych do wycinka danych specyficznych dla kliknij lewym przyciskiem myszy punkt danych na wykresie bieżący, a następnie przeciągnij zaznaczonego obszaru do wybranego punktu końcowego. Kliknij prawym przyciskiem myszy szary obszar wybrany, a następnie wybierz pozycję **powiększenia**, jak pokazano na poniższym obrazie:

  [![Powiększenie wykresu (wersja zapoznawcza)](media/v2-update-explorer/explorer-nine.png)](media/v2-update-explorer/explorer-nine.png#lightbox)

Po wykonaniu **powiększenia** akcji, zobacz wybranego zestawu danych. Zaznacz formant formatu osi y, aby przechodzić między trzy reprezentacje osi y dane usługi Time Series Insights.

  [![Oś y wykresu (wersja zapoznawcza)](media/v2-update-explorer/explorer-ten.png)](media/v2-update-explorer/explorer-ten.png#lightbox)

W tym miejscu można zobaczyć przykład udostępnionego osi Y:

  [![Wersja zapoznawcza udostępnione osi Y](media/v2-update-explorer/explorer-eleven.png)](media/v2-update-explorer/explorer-eleven.png#lightbox)

## <a name="time-editor-panel"></a>Panel Edytor czasu

Podczas pracy z czasu Series Insights w wersji zapoznawczej, najpierw należy wybrać przedział czasu. Określa zakres czasu wybrany określa zestaw danych, która jest dostępna do manipulowania widżetów czasu Series Insights w wersji zapoznawczej. Następujące kontrolki sieci web są dostępne w czasie Series Insights w wersji zapoznawczej służąca do wybierania swojej pracy przedział czasu:

  [![Panel wyboru czasu](media/v2-update-explorer/explorer-twelve.png)](media/v2-update-explorer/explorer-twelve.png#lightbox)

1. **Narzędzie suwaka zakresu wewnętrznego data**: Za pomocą dwóch formantów punktu końcowego, przeciągając je za pośrednictwem przedział czasu, który ma. Ten zakres dat wewnętrzny jest ograniczone przez formant suwaka zakresu zewnętrznego daty.

1. **Zwiększać i zmniejszać przycisków zakresu dat**: Zwiększanie lub zmniejszanie swoje przedział czasu, wybierając przycisk albo dla interwału, który ma.

1. **Czas formantu Zwiń zakresu**: Tego formantu sieci web pozwala na ukrywanie wszystkich kontrolek z wyjątkiem narzędzia suwaka zakresu dat wewnętrzny.

1. **Kontrolka suwaka zakresu zewnętrznego data**: Użyj kontrolki punktu końcowego, aby wybrać zakres dat zewnętrznej, która będzie dostępna dla kontrolki zakresu wewnętrznego daty.

1. **Listy rozwijanej zakres dat krótkie okresy**: Umożliwia szybkie przełączanie się między wstępnie ustawiony czas zakresu wybrane opcje, takie jak ostatnie **30 minut**, **ostatnich 12 godzin**, lub **niestandardowego zakresu**. Zmiana ta wartość zmienia się również zakresy dostępne interwał omówione w narzędziu suwaka rozmiar interwału.

1. **Rozmiar interwału suwak**: Umożliwia powiększanie i pomniejszanie odstępach czasu za pośrednictwem tego samego przedział czasu. Ta akcja zapewnia dokładniejszą kontrolę ruchu między wycinkami dużych czasu. Wyświetla smooth trendów w dół wycinki tak małej, jak Milisekunda. Aby wyświetlić szczegółowe, wysokiej rozdzielczości odpowiednie dane, można użyć go. Domyślne suwaka punkt początkowy ustawiono jako najbardziej optymalną wgląd w dane z zaznaczenia rozdzielczości, szybkości zapytań i stopień szczegółowości.

1. **Kontrolka sieci web do a z zakresu daty**: Za pomocą tego formantu sieci web możesz łatwo wybrać zakresy daty i godziny, który ma. Kontrolka umożliwia również przełączać się między różnych strefach czasowych. Po wprowadzeniu zmian do zastosowania do Twojego bieżącego obszaru roboczego wybierz **Zapisz**.

   [![Do i z panelu wyboru](media/v2-update-explorer/explorer-thirteen.png)](media/v2-update-explorer/explorer-thirteen.png#lightbox)

## <a name="navigation-panel"></a>Panel nawigacji

Panel nawigacyjny czasu Series Insights w wersji zapoznawczej pojawia się w górnej części aplikacji usługi Time Series Insights. Zapewnia następujące funkcje.

### <a name="current-session-share-link-control"></a>Kontrola łącza udział w bieżącej sesji

  [![Ikona udostępniania](media/v2-update-explorer/explorer-fifteen.png)](media/v2-update-explorer/explorer-fifteen.png#lightbox)

Wybierz nową **udostępnianie** ikonę, aby udostępniać link z adresem URL swojemu zespołowi.

  [![Udostępnij adres URL wystąpienia](media/v2-update-explorer/url-share.png)](media/v2-update-explorer/url-share.png#lightbox)

### <a name="tenant-section"></a>Sekcja dzierżawy

  [![Wybór dzierżawy](media/v2-update-explorer/explorer-sixteen.png)](media/v2-update-explorer/explorer-sixteen.png#lightbox)

* Wyświetla swoje aktualne informacje konto logowania usługi Time Series Insights.
* Użyj go, aby przełączać się między dostępne motywy usługi Time Series Insights.
* Użyj go, aby wyświetlić podgląd [pokazowa aplikacja sieci web](https://insights.timeseries.azure.com/preview/demo).

### <a name="theme-selection"></a>Wybór motywu

Aby wybrać nowy motyw, wybierz pozycję ikona profilu znajduje się w prawym górnym rogu. Następnie wybierz **Zmień motyw**.

  [![Wybór motywu](media/v2-update-explorer/theme-selection.png)](media/v2-update-explorer/theme-selection.png#lightbox)

> [!TIP]
> Wybór języka jest również dostępna, wybierając ikonę Twojego profilu.

Azure czas Series Insights w wersji zapoznawczej obsługuje dwa motywy:

* **Motyw jasny**: Motyw domyślny, przedstawione w tym dokumencie.
* **Motyw ciemny**: Renderuje Eksploratora, jak pokazano poniżej:

  [![Wybrany motyw ciemny](media/v2-update-explorer/explorer-seventeen.png)](media/v2-update-explorer/explorer-seventeen.png#lightbox)

## <a name="s1s2-environment-controls"></a>Kontrolki środowiska S1/S2

### <a name="preview-terms-panel"></a>Panel terminów (wersja zapoznawcza)

Ta sekcja dotyczy tylko do istniejących środowisk S1/S2, które próbują w Interfejsie użytkownika zaktualizowane przy użyciu Eksploratora. Można używać w połączeniu produktu jest ogólnie dostępna i w wersji zapoznawczej. Niektóre funkcje z istniejącego interfejsu użytkownika do dodaliśmy zaktualizowane explorer, ale można uzyskać pełnego środowiska interfejsu użytkownika dla środowiska S1/S2 w istniejących Eksploratora usługi Time Series Insights. 

Zamiast hierarchii zostanie wyświetlony panel terminów usługi Time Series Insights, w którym definiowania kwerend w danym środowisku. Go użyć do filtrowania danych na podstawie predykatu.

  [![Gdy zapytanie panelu](media/v2-update-explorer/explorer-eighteen.png)](media/v2-update-explorer/explorer-eighteen.png#lightbox)

Panel Edytor warunków czasu Series Insights w wersji zapoznawczej przyjmuje następujące parametry:

**Where**: Użyj where klauzuli szybkie filtrowanie zdarzeń za pomocą zbiór operandów wymienione w poniższej tabeli. Jeśli wyszukiwania, wybierając operand, predykat jest automatycznie aktualizowana, oparte na tym wyszukiwaniem. Operand obsługiwane typy:

| Operacja | Obsługiwane typy   | Uwagi |
| --- | --- | --- |
| `<`, `>`, `<=`, `>=` | Double, DateTime, TimeSpan | |
| `=`, `!=`, `<>` | String, Bool, Double, DateTime, przedział czasu, wartość NULL |
| `IN` | String, Bool, Double, DateTime, przedział czasu, wartość NULL | Wszystkie argumenty powinny być tego samego typu lub być stała NULL. |
| `HAS` | String | Tylko literałów ciągów stałe są dozwolone po prawej stronie. Pusty ciąg i wartości NULL nie są dozwolone. |

Aby dowiedzieć się więcej na temat operacji obsługiwanych zapytań i typy danych, zobacz [wyrażenie serii czasu (TSX)](https://docs.microsoft.com/rest/api/time-series-insights/preview-tsx).

### <a name="examples-of-where-clauses"></a>Przykłady gdzie klauzule

  [![Gdzie przykłady klauzuli](media/v2-update-explorer/explorer-nineteen.png)](media/v2-update-explorer/explorer-nineteen.png#lightbox)

**Miara**: Listy rozwijanej, która wyświetla wszystkie kolumny liczbowe (**wartości podwójnej precyzji**) można użyć jako elementy, dla bieżącego wykresu.

**Podziel według**: Tej listy rozwijanej wyświetla wszystkie dostępne podzielonych na kategorie kolumny (ciągi) w modelu, można grupować dane przez. Możesz dodać maksymalnie pięć warunki, aby wyświetlić na tej samej osi x. Wprowadź parametry, a następnie wybierz **Dodaj** można dodać nowej termin.

  [![Zapytanie o i filtrowanego widoku, jeden](media/v2-update-explorer/explorer-twenty.png)](media/v2-update-explorer/explorer-twenty.png#lightbox)

Można pokazać i ukryć elementy w panelu wykresu, wybierając ikonę widoczne, jak pokazano na poniższej ilustracji. Aby całkowicie usunąć zapytań, wybierz czerwony **X**.

  [![Wyświetl poszukiwanych i filtrowane dwóch](media/v2-update-explorer/explorer-twenty-one.png)](media/v2-update-explorer/explorer-twenty-one.png#lightbox)

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [magazynu i transferu danych przychodzących](./time-series-insights-update-storage-ingress.md) w wersji zapoznawczej Azure czas serii szczegółowych informacji.
- Przeczytaj dokument czasu Series Insights w wersji zapoznawczej na [modelowania danych](./time-series-insights-update-tsm.md).
- Dowiedz się, [sposób diagnozowania i rozwiązywania problemów](./time-series-insights-update-how-to-troubleshoot.md) wystąpienia usługi Time Series Insights.
