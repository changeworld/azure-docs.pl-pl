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
ms.date: 08/05/2019
ms.custom: seodec18
ms.openlocfilehash: 33e485e4fcee665e810c42bca6b38aac065ff668
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2019
ms.locfileid: "68841436"
---
# <a name="visualize-data-in-the-explorer-preview"></a>Wizualizowanie danych w programie explorer (wersja zapoznawcza)

W tym dokumencie opisano funkcje interfejsu użytkownika i interfejs użytkownika Azure Time Series Insights w wersji zapoznawczej [demonstracyjnej aplikacji sieci Web](https://insights.timeseries.azure.com/preview/demo). Szczegółowo omówiono w nim układ przykładu hostowanej, opcje dostosowywania interfejsu i nawigowanie za pomocą podanej wersji demonstracyjnej.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć pracę z Eksploratorem Azure Time Series Insights w wersji zapoznawczej, należy wykonać następujące kroki:

* Masz środowisko usługi Time Series Insights Konfigurowanie. Aby dowiedzieć się więcej o aprowizacji wystąpienia, wypróbuj samouczek [Azure Time Series Insights w wersji](./time-series-insights-update-create-environment.md) zapoznawczej.
* [Zapewnianie dostępu do danych](./time-series-insights-data-access.md) do środowiska Time Series Insights utworzonego dla konta. Można zapewnić dostęp do innych użytkowników oraz do siebie.
* Dodaj źródło zdarzenia do środowiska Time Series Insights, aby wypchnąć dane do środowiska:
  * Dowiedz się [, jak nawiązać połączenie z centrum zdarzeń](./time-series-insights-how-to-add-an-event-source-eventhub.md).
  * Dowiedz się, [jak nawiązać połączenie z usługą IoT Hub](./time-series-insights-how-to-add-an-event-source-iothub.md).

## <a name="learn-about-the-preview-explorer"></a>Dowiedz się więcej o Eksploratorze wersji zapoznawczej

Eksplorator usługi Azure czas Series Insights w wersji zapoznawczej obejmuje następujące elementy:

[![Widok Eksploratora](media/v2-update-explorer/explorer-one.png)](media/v2-update-explorer/explorer-one.png#lightbox)

- <a href="#environment-drop-down-list">Panel środowiska</a>: Wyświetla Azure Time Series Insights środowiska.
- <a href="#navigation-menu">Menu nawigacji</a>: Użyj go, aby przełączać się między stronami **Analizowanie** i **modelem** .
- <a href="#hierarchy-tree">Drzewo hierarchii</a>: Służy do wybierania określonego modelu i elementów danych do wykresu.
- <a href="#preview-well">Źródło szeregów czasowych</a>: Wyświetla aktualnie wybrane elementy danych w formacie tabeli z kodowaniem kolorem.
- <a href="#preview-chart">Panel wykresu</a>: Wyświetla bieżący wykres roboczy.
- <a href="#time-editor-panel">Oś czasu</a>: Służy do modyfikowania przedziału czasu pracy.
- <a href="#navigation-panel">Pasek aplikacji</a>: Zawiera opcje zarządzania użytkownikami, takie jak bieżąca dzierżawa. Można jej użyć do zmiany ustawień motywu i języka.

## <a name="environment-drop-down-list"></a>Lista rozwijana środowiska

Lista rozwijana środowisko zawiera wszystkie Time Series Insights środowiska, do których masz dostęp. Lista obejmuje środowiska z płatność zgodnie z rzeczywistym użyciem, takie jak wersja zapoznawcza Time Series Insights. Lista zawiera również środowiska S1/S2, które są ogólnie dostępne.

1. Wybierz strzałkę listy rozwijanej obok wyświetlanego środowiska.

   [![Panel sterowania](media/v2-update-explorer/explorer-two.png)](media/v2-update-explorer/explorer-two.png#lightbox)

1. Następnie wybierz odpowiednie środowisko.

## <a name="navigation-menu"></a>Menu nawigacji

  [![Menu nawigacji](media/v2-update-explorer/explorer-three.png)](media/v2-update-explorer/explorer-three.png#lightbox)

Użyj menu nawigacji, aby wybrać dwa widoki:

* **Analizuj**: Używaj go do grafowania i wykonywania bogatej analizy na potrzeby modelowania lub niemodelowanych danych szeregów czasowych.
* **Model**: Użyj go, aby wypchnąć nowe typy, hierarchie i wystąpienia podglądu Time Series Insights w modelu Time Series Insights.

## <a name="hierarchy-tree"></a>Drzewo hierarchii

W drzewie hierarchii są wyświetlane wybrane elementy danych, które obejmują modele, określone urządzenia i czujniki na urządzeniach.

### <a name="model-search-panel"></a>Panel wyszukiwania modelu

Korzystając z panelu wyszukiwania modelowego, można łatwo przeszukiwać hierarchię modelu szeregów czasowych i przechodzić do nich, aby znaleźć określone wystąpienia szeregów czasowych, które mają być wyświetlane na wykresie. Po wybraniu Twoich wystąpień są one dodawane zarówno do bieżącego wykresu, jak i do danych.

  [![Panel wyszukiwania modelu](media/v2-update-explorer/explorer-four.png)](media/v2-update-explorer/explorer-four.png#lightbox)

### <a name="model-authoring"></a>Tworzenie modelu

Wersja zapoznawcza Azure Time Series Insights obsługuje operacje pełnego tworzenia, odczytu, aktualizacji i usuwania (CRUD) w modelu szeregów czasowych.

* **Typ modelu szeregów czasowych**: Za pomocą typów Time Series Insights można definiować zmienne lub formuły do wykonywania obliczeń. Są one skojarzone z danym wystąpieniem Time Series Insights. Typ może mieć co najmniej jednej zmiennej.
* **Hierarchia modelu szeregów czasowych**: Hierarchie są systematycznie organizacjami danych. Hierarchie przedstawiać relacje między różnymi jednostkami danych usługi Time Series Insights.
* **Wystąpienie modelu szeregów czasowych**: Wystąpienia są same w szeregach czasowych. W większości przypadków są to **DeviceID** lub **AssetID**, który jest unikatowym identyfikatorem zasobu w środowisku.

Aby dowiedzieć się więcej na temat modelu szeregów czasowych, zobacz [modeli szeregów razy](./time-series-insights-update-tsm.md).

## <a name="preview-well"></a>Podgląd — Źródło

Wyświetla pola wystąpienia i inne metadane skojarzone z wybranymi wystąpieniami Time Series Insights. Zaznaczając pola wyboru po prawej stronie, można ukryć lub wyświetlić określone wystąpienia z bieżącego wykresu. Można również usunąć konkretne elementy danych z bieżących danych, zaznaczając formant Red **delete** (kosza) po lewej stronie elementu.

  [![Źródło podglądu](media/v2-update-explorer/explorer-five.png)](media/v2-update-explorer/explorer-five.png#lightbox)

Aby zmienić konfigurację układu strony **Analizowanie** wykresu, wybierz ikonę wielokropka w prawym górnym rogu:

  [![Opcje układu telemetrii](media/v2-update-explorer/explorer-six.png)](media/v2-update-explorer/explorer-six.png#lightbox)

> [!NOTE]
> Jeśli zobaczysz następujący komunikat, wystąpienie nie ma żadnych danych w wybranym okresie. Aby rozwiązać ten problem, należy zwiększyć przedział czasu lub potwierdzić, że wystąpienie jest wypychane dane.
>
> ![Brak powiadomienia o danych](media/v2-update-explorer/explorer-seven.png)

## <a name="preview-chart"></a>Podgląd wykresu

Za pomocą wykresu można wyświetlać wystąpienia Time Series Insights jako linie. Możesz zwinąć panel środowiska, model danych i Panel sterowania przedział czasu, wybierając kontrolki sieci Web, aby wykres był większy.

  [![Przegląd wykresu podglądu](media/v2-update-explorer/explorer-eight.png)](media/v2-update-explorer/explorer-eight.png#lightbox)

- **Wybrany zakres dat**: Kontroluje, które elementy danych są dostępne do wizualizacji.

- **Wewnętrzny suwak zakresu dat**: Użyj dwóch kontrolek punktu końcowego, przeciągając je w pożądanym okresie.

- **Kontrolka zwijania zakresu czasu**: Zwija i rozwija Edytor panelu przedziału czasu.

- **Kontrolka formatu osi Y**: Przechodzenie między dostępnymi opcjami widoku osi y:

    * `Default`: Każdy wiersz ma pojedynczą oś y.
    * `Stacked`: Służy do układania wielu linii na tej samej osi y, przy czym dane osi y zmieniają się w oparciu o wybrany wiersz.
    * `Shared`: Wszystkie dane osi y są wyświetlane razem.

- **Bieżący element danych**: Aktualnie wybrany element danych i powiązane z nim szczegóły.

Aby przejść do szczegółów określonego wycinka danych, kliknij lewym przyciskiem myszy punkt danych na bieżącym grafie, a następnie przeciągnij wybrany obszar do wybranego punktu końcowego. Kliknij prawym przyciskiem myszy szary zaznaczony obszar i wybierz polecenie **powiększenie**, jak pokazano na poniższej ilustracji:

  [![Podgląd powiększenia wykresu](media/v2-update-explorer/explorer-nine.png)](media/v2-update-explorer/explorer-nine.png#lightbox)

Po wykonaniu akcji **powiększenia** zobaczysz wybrany zestaw danych. Wybierz kontrolkę format osi y, aby przechodzić przez trzy reprezentacje osi y danych Time Series Insights.

  [![Podgląd osi y wykresu](media/v2-update-explorer/explorer-ten.png)](media/v2-update-explorer/explorer-ten.png#lightbox)

W tym miejscu można zobaczyć przykład udostępnionych osi Y:

  [![Podgląd udostępnionych osi Y](media/v2-update-explorer/explorer-eleven.png)](media/v2-update-explorer/explorer-eleven.png#lightbox)

## <a name="time-editor-panel"></a>Panel edytora czasu

Podczas pracy z czasu Series Insights w wersji zapoznawczej, najpierw należy wybrać przedział czasu. Wybrany przedział czasu kontroluje zestaw danych, który jest dostępny do manipulowania przy użyciu widżetów Time Series Insights Preview. Następujące kontrolki sieci Web są dostępne w wersji zapoznawczej Time Series Insights w celu wybrania zakresu czasu pracy:

  [![Panel wyboru czasu](media/v2-update-explorer/explorer-twelve.png)](media/v2-update-explorer/explorer-twelve.png#lightbox)

1. **Wewnętrzny suwak zakresu dat**: Użyj dwóch kontrolek punktu końcowego, przeciągając je w pożądanym okresie. Ten wewnętrzny zakres dat jest ograniczony przez kontrolkę suwaka zewnętrznego zakresu dat.

1. **Przyciski zwiększania i zmniejszania zakresu dat**: Zwiększ lub Zmniejsz zakres czasu, wybierając jeden z przycisków dla interwału, który chcesz.

1. **Kontrolka zwijania zakresu czasu**: Ten formant sieci Web umożliwia ukrycie wszystkich kontrolek z wyjątkiem wewnętrznego narzędzia suwaka zakresu dat.

1. **Kontrolka suwaka zewnętrznego zakresu dat**: Użyj kontrolek punkt końcowy, aby wybrać zewnętrzny zakres dat, który będzie dostępny dla wewnętrznej kontroli zakresu dat.

1. **Lista rozwijana szybkiego czasu — zakres dat**: Użyj go, aby szybko przełączać się między wstępnie ustawionymi opcjami przedziału czasu, takimi jak ostatnie **30 minut**, **ostatnie 12 godzin**lub **zakres niestandardowy**. Zmiana ta wartość zmienia się również zakresy dostępne interwał omówione w narzędziu suwaka rozmiar interwału.

1. **Narzędzie suwaka rozmiaru interwału**: Służy do powiększania i wypełniania interwałów w tym samym przedziale czasu. Ta akcja zapewnia dokładniejszą kontrolę ruchu między wycinkami dużych czasu. Wyświetla gładkie trendy w dół do wycinków jako milisekundy. Można jej użyć, aby zobaczyć szczegółowe i wysokiej rozdzielczości kawałki danych. Domyślne suwaka punkt początkowy ustawiono jako najbardziej optymalną wgląd w dane z zaznaczenia rozdzielczości, szybkości zapytań i stopień szczegółowości.

1. **Zakres dat do i z kontrolki sieci Web**: Za pomocą tej kontrolki sieci Web można łatwo wybierać zakresy dat i godzin. Kontrolka umożliwia również przełączać się między różnych strefach czasowych. Po wprowadzeniu zmian do zastosowania w bieżącym obszarze roboczym wybierz pozycję **Zapisz**.

   [![Do i z panelu wyboru](media/v2-update-explorer/explorer-thirteen.png)](media/v2-update-explorer/explorer-thirteen.png#lightbox)

## <a name="navigation-panel"></a>Panel nawigacji

Panel nawigacji Time Series Insights Preview pojawia się u góry aplikacji Time Series Insights. Zapewnia następujące funkcje.

### <a name="current-session-share-link-control"></a>Kontrola łącza udział w bieżącej sesji

  [![Ikona udostępniania](media/v2-update-explorer/explorer-fifteen.png)](media/v2-update-explorer/explorer-fifteen.png#lightbox)

Wybierz ikonę Nowy **udział** , aby udostępnić link adresu URL do zespołu.

  [![Udostępnianie adresu URL wystąpienia](media/v2-update-explorer/url-share.png)](media/v2-update-explorer/url-share.png#lightbox)

### <a name="tenant-section"></a>Sekcja dzierżawy

  [![Wybór dzierżawy](media/v2-update-explorer/explorer-sixteen.png)](media/v2-update-explorer/explorer-sixteen.png#lightbox)

* Wyświetla bieżące informacje o koncie logowania Time Series Insights.
* Użyj go, aby przełączać się między dostępnymi Time Series Insightsmi motywami.
* Służy do wyświetlania [demonstracyjnej aplikacji sieci Web](https://insights.timeseries.azure.com/preview/demo).

### <a name="theme-selection"></a>Wybór motywu

Aby wybrać nowy motyw, wybierz ikonę profilu znajdującą się w prawym górnym rogu. Następnie wybierz pozycję **Zmień motyw**.

  [![Wybór motywu](media/v2-update-explorer/theme-selection.png)](media/v2-update-explorer/theme-selection.png#lightbox)

> [!TIP]
> Wybór języka jest również dostępny przez wybranie ikony profilu.

Azure czas Series Insights w wersji zapoznawczej obsługuje dwa motywy:

* **Motyw jasny**: Motyw domyślny pokazywany w tym dokumencie.
* **Ciemny motyw**: Renderuje Eksploratora, jak pokazano poniżej:

  [![Wybrany motyw ciemny](media/v2-update-explorer/explorer-seventeen.png)](media/v2-update-explorer/explorer-seventeen.png#lightbox)

## <a name="s1s2-environment-controls"></a>Kontrolki środowiska S1/S2

### <a name="preview-terms-panel"></a>Panel warunków wersji zapoznawczej

Ta sekcja dotyczy tylko do istniejących środowisk S1/S2, które próbują w Interfejsie użytkownika zaktualizowane przy użyciu Eksploratora. Możesz chcieć korzystać z ogólnie dostępnego produktu i wersji zapoznawczej. Niektóre funkcje z istniejącego interfejsu użytkownika do dodaliśmy zaktualizowane explorer, ale można uzyskać pełnego środowiska interfejsu użytkownika dla środowiska S1/S2 w istniejących Eksploratora usługi Time Series Insights. 

Zamiast hierarchii zobaczysz panel warunki Time Series Insights, w którym można definiować zapytania w danym środowisku. Służy do filtrowania danych w oparciu o predykat.

  [![Miejsce, w którym panel zapytań](media/v2-update-explorer/explorer-eighteen.png)](media/v2-update-explorer/explorer-eighteen.png#lightbox)

Panel Edytor warunków czasu Series Insights w wersji zapoznawczej przyjmuje następujące parametry:

**Where**: Klauzula WHERE umożliwia szybkie filtrowanie zdarzeń przy użyciu zestawu argumentów wymienionych w poniższej tabeli. Jeśli wyszukiwania, wybierając operand, predykat jest automatycznie aktualizowana, oparte na tym wyszukiwaniem. Operand obsługiwane typy:

| Operacja | Obsługiwane typy   | Uwagi |
| --- | --- | --- |
| `<`, `>`, `<=`, `>=` | Double, DateTime, TimeSpan | |
| `=`, `!=`, `<>` | String, Bool, Double, DateTime, przedział czasu, wartość NULL |
| `IN` | String, Bool, Double, DateTime, przedział czasu, wartość NULL | Wszystkie argumenty powinny być tego samego typu lub być stała NULL. |
| `HAS` | String | Tylko stałe literały ciągu są dozwolone po prawej stronie. Pusty ciąg i wartość NULL są niedozwolone. |

Aby dowiedzieć się więcej na temat obsługiwanych operacji zapytań i typów danych, zobacz [wyrażenie szeregów czasowych (TSX)](https://docs.microsoft.com/rest/api/time-series-insights/preview-tsx).

### <a name="examples-of-where-clauses"></a>Przykłady klauzul WHERE

  [![Przykłady klauzuli WHERE](media/v2-update-explorer/explorer-nineteen.png)](media/v2-update-explorer/explorer-nineteen.png#lightbox)

**Miara**: Lista rozwijana wyświetlająca wszystkie kolumny liczbowe (**podwaja**), których można użyć jako elementów dla bieżącego wykresu.

**Podziel według**: Ta lista rozwijana zawiera wszystkie dostępne kolumny kategorii (ciągi) w modelu, według których można grupować dane. Można dodać maksymalnie pięć wyrazów, aby wyświetlić je na tej samej osi x. Wprowadź wymagane parametry, a następnie wybierz pozycję **Dodaj** , aby dodać nowy termin.

  [![Zapytania i widok filtrowany](media/v2-update-explorer/explorer-twenty.png)](media/v2-update-explorer/explorer-twenty.png#lightbox)

Możesz pokazać i ukryć elementy w panelu wykresu, wybierając ikonę widoczne, jak pokazano na poniższej ilustracji. Aby całkowicie usunąć zapytania, wybierz czerwony znak **X**.

  [![Przeszukiwany i filtrowany widok dwa](media/v2-update-explorer/explorer-twenty-one.png)](media/v2-update-explorer/explorer-twenty-one.png#lightbox)

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [magazynie i przychodzących danych](./time-series-insights-update-storage-ingress.md) w wersji zapoznawczej Azure Time Series Insights.
- Przeczytaj dokument Time Series Insights w wersji zapoznawczej dotyczące [modelowania danych](./time-series-insights-update-tsm.md).
- Dowiedz się [, jak diagnozować i rozwiązywać problemy](./time-series-insights-update-how-to-troubleshoot.md) z wystąpieniem Time Series Insights.
