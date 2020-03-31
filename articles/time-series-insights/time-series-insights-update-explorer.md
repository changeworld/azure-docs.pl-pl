---
title: Wizualizuj dane w Eksploratorze wersji zapoznawczej — usługa Azure Time Series Insights | Dokumenty firmy Microsoft
description: Dowiedz się więcej o funkcjach i opcjach dostępnych w Eksploratorze usługi Azure Time Series Insights Preview.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 01/07/2020
ms.custom: seodec18
ms.openlocfilehash: c117510749abcf997e414371faa6dea1dc79ea7a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75861765"
---
# <a name="azure-time-series-insights-preview-explorer"></a>Eksplorator usługi Azure Time Series Insights Preview

W tym artykule opisano różne funkcje i opcje dostępne w aplikacji sieci web w wersji [demonstracyjnej](https://insights.timeseries.azure.com/preview/demo)usługi Azure Time Series Insights Preview .

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć pracę z Eksploratorem usługi Azure Time Series Insights Preview, należy:

* Mieć środowisko usługi Time Series Insights aprowizowana. Dowiedz się więcej o inicjowaniu obsługi administracyjnej wystąpienia, czytając samouczek [usługi Azure Time Series Insights Preview.](./time-series-insights-update-create-environment.md)
* [Zapewnij dostęp do danych](./time-series-insights-data-access.md) do środowiska usługi Time Series Insights utworzonego dla konta. Możesz zapewnić dostęp do innych, jak i do siebie.
* Dodaj źródło zdarzeń do środowiska usługi Time Series Insights, aby wypychać dane do środowiska:
  * Dowiedz [się, jak połączyć się z centrum zdarzeń](./time-series-insights-how-to-add-an-event-source-eventhub.md) 
  * Dowiedz [się, jak połączyć się z centrum IoT](./time-series-insights-how-to-add-an-event-source-iothub.md)

## <a name="explore-the-time-series-insights-preview-explorer"></a>Eksplorowanie Eksploratora wglądu w usługi Szczegółowe informacje o szeregach czasowych

Eksplorator usługi Azure Time Series Insights Preview składa się z następujących siedmiu elementów:

[![Omówienie eksploratora usługiLordys insights](media/v2-update-explorer/preview-explorer-overview.png)](media/v2-update-explorer/preview-explorer-overview.png#lightbox)

1. [Panel Środowisko:](#1-environment-panel)Wyświetla wszystkie środowiska usługi Azure Time Series Insights.
1. [Pasek nawigacyjny:](#2-navigation-bar)Umożliwia przełączanie między stronami **Analiza** i **Model.**
1. [Drzewo hierarchii i panel wyszukiwania](#3-hierarchy-tree-and-search-panel): Umożliwia zaznaczanie i wyszukiwanie określonych elementów danych, które mają być wykresami.
1. [Szeregi czasowe dobrze:](#4-time-series-well)pokazuje wszystkie aktualnie wybrane elementy danych.
1. [Panel wykresu](#5-chart-panel): Wyświetla bieżący wykres roboczy.
1. [Oś czasu :](#6-time-editor-panel)Umożliwia modyfikowanie przedziału czasu pracy.
1. [Pasek aplikacji](#7-app-bar): Zawiera opcje zarządzania użytkownikami (takie jak bieżąca dzierżawa) i umożliwia ich zmianę i ustawienia języka.


## <a name="1-environment-panel"></a>1. Panel ochrony środowiska

Panel środowiska wyświetla wszystkie środowiska usługi Time Series Insights, do których masz dostęp. Lista zawiera środowiska płatności zgodnie z rzeczywistymu po podróży (wersja zapoznawcza) oraz środowiska S1/S2 (Ogólna dostępność). Po prostu wybierz środowisko usługi Time Series Insights, którego chcesz użyć, aby natychmiast zostać tam odebrane.

1. Wybierz strzałkę rozwijaną obok wyświetlanego środowiska.

   [![Panel Środowisko](media/v2-update-explorer/environment-panel.png)](media/v2-update-explorer/environment-panel.png#lightbox)

1. Następnie wybierz środowisko, które chcesz.

## <a name="2-navigation-bar"></a>2. Pasek nawigacyjny

  [![Pasek nawigacyjny](media/v2-update-explorer/tsi-preview-navigation-bar.png)](media/v2-update-explorer/tsi-preview-navigation-bar.png#lightbox)

Użyj paska nawigacyjnego, aby wybrać jeden z dwóch widoków:

* **Analizuj:** Użyj go do wykresu i wykonywania analiz rozszerzonych na modelowanych lub niezamodelowanych danych szeregów czasowych.
* **Model:** Użyj go do wypychania nowych typów podglądu, hierarchii i wystąpień usługi Time Series Insights do modelu usługi Time Series Insights.

### <a name="model-authoring"></a>Tworzenie modelu

Usługa Azure Time Series Insights Preview obsługuje pełne operacje tworzenia, odczytu, aktualizacji i usuwania (CRUD) w modelu szeregów czasowych.

[![Panel wyszukiwania modelu](media/v2-update-explorer/model-search-panel.png)](media/v2-update-explorer/model-search-panel.png#lightbox)

* **Typ modelu szeregów czasowych: Typy**usługi Time Series Insights umożliwiają definiowanie zmiennych lub formuł do wykonywania obliczeń. Są one skojarzone z danym wystąpieniem usługi Time Series Insights. Typ może mieć jedną lub więcej zmiennych.
* **Hierarchia modeli szeregów czasowych:** Hierarchie są systematycznymi organizacjami danych. Hierarchie przedstawiają relacje między różnymi encjami w danych usługi Time Series Insights.
* **Wystąpienie modelu szeregów czasowych:** Wystąpienia są same szeregi czasowe. W większości przypadków są to **DeviceID** lub **AssetID**, który jest unikatowym identyfikatorem zasobu w środowisku.

Aby dowiedzieć się więcej o modelu szeregów czasowych, przeczytaj artykuł [Modele times series](./time-series-insights-update-tsm.md).

## <a name="3-hierarchy-tree-and-search-panel"></a>3. Drzewo hierarchii i panel wyszukiwania

Drzewo hierarchii i panel wyszukiwania umożliwiają łatwe wyszukiwanie i poruszanie się po hierarchii [modelu szeregów czasowych](./time-series-insights-update-tsm.md) w celu znalezienia określonych wystąpień szeregów czasowych, które mają być wyświetlane na wykresie. Po wybraniu wystąpień są one nie tylko dodawane do bieżącego wykresu, ale są również dobrze dodawane do danych. 

[![Drzewo hierarchii i panel wyszukiwania](media/v2-update-explorer/tsi-preview-explorer-hierarchy-search.png)](media/v2-update-explorer/tsi-preview-explorer-hierarchy-search.png#lightbox)

Okienko wyników wyszukiwania umożliwia również wyświetlanie wyników w widoku hierarchii lub widoku listy, co ułatwia znajdowanie wystąpień, które mają być wyświetlane.
 
## <a name="4-time-series-well"></a>4. Dobrze szeregi czasowe

Dobrze wyświetla pola wystąpienia i inne metadane skojarzone z wybranymi wystąpieniami usługi Time Series Insights. Zaznaczając pola wyboru po prawej stronie, można ukryć lub wyświetlić określone wystąpienia z bieżącego wykresu. 

  [![Dobrze podgląd](media/v2-update-explorer/tsi-preview-ui-explorer-well.png)](media/v2-update-explorer/tsi-preview-ui-explorer-well.png#lightbox)

Można usunąć określone elementy danych z bieżących danych dobrze, wybierając czerwony **Delete** (kosza) kontroli po lewej stronie elementu. Studnia pozwala również kontrolować sposób wyświetlania każdego elementu na wykresie. Można dodać min/max cienie, punkty danych, przesunąć element w czasie i wizualizować wystąpienie w sposób stopniowy. 

Dodatkowo, Kontrola eksploracji pozwala łatwo tworzyć zmiany czasu i wykresy punktowe.  

  [![Opcje układu studni](media/v2-update-explorer/well-layout-options.png)](media/v2-update-explorer/well-layout-options.png#lightbox)

> [!NOTE]
> Jeśli pojawi się następujący komunikat, wystąpienie nie ma żadnych danych w wybranym przedziale czasu. Aby rozwiązać ten problem, należy zwiększyć przedział czasu lub potwierdzić, że wystąpienie wypycha dane.
>
> ![Brak powiadomienia o danych](media/v2-update-explorer/tsi-preview-no-data-warning.png)

## <a name="5-chart-panel"></a>5. Panel wykresów

Wykres umożliwia wyświetlanie wystąpień szeregów czasowych jako linii. Panel środowiska, model danych i panel sterowania przedziału czasu można zwinąć, klikając formanty sieci Web, aby powiększyć wykres. 

  [![Omówienie wykresu podglądu](media/v2-update-explorer/tsi-preview-chart-overview.png)](media/v2-update-explorer/tsi-preview-chart-overview.png#lightbox)

1. **Typ wykresu**: Określa, które elementy danych są dostępne do wizualizacji.

1. **Rozmiar interwału:** Narzędzie suwak Rozmiar interwału umożliwia powiększanie i pomniejszanie interwałów w tym samym przedziale czasu. Zapewnia to bardziej precyzyjną kontrolę ruchu między dużymi wycinkami czasu, które pokazują płynne trendy w dół do plasterków tak małych jak milisekunda, co pozwala na przeglądanie szczegółowych, wysokiej rozdzielczości cięć danych. Domyślny punkt początkowy suwaka jest ustawiony jako najbardziej optymalny widok danych z wybranego wyboru; równoważenia, szybkość kwerendy i szczegółowość.

1. **Powiększanie i przesuwanie:** Wybierz ten formant, aby powiększyć i przesunąć wykres.

1. **Sterowanie osią Y:** Przechodzi przez dostępne opcje widoku osi y:

    * `Stacked`: Każda linia ma indywidualną oś Y.
    * `Overlap`: Służy do układania wielu linii na tej samej osi Y, przy czym dane osi Y zmieniają się na podstawie wybranej linii.
    * `Shared`: Wszystkie dane osi Y wyświetlane razem.

1. **Element znacznika**: Aktualnie wybrany element danych i skojarzone z nim szczegóły.

Można dodatkowo przejść do określonego wycinka danych, **klikając lewym przyciskiem myszy** punkt danych na bieżącym wykresie, przytrzymując mysz, a następnie przeciągając zaznaczony obszar do wybranego punktu końcowego. **Kliknij prawym przyciskiem myszy** niebieski, zaznaczony obszar, a następnie wybierz **polecenie Powiększ,** jak pokazano poniżej. Można również wyświetlać i pobierać zdarzenia telemetryczne w wybranym rozpiętości czasowej.

  [![Podgląd powiększenia wykresu](media/v2-update-explorer/preview-chart-zoom.png)](media/v2-update-explorer/preview-chart-zoom.png#lightbox)

Po wykonaniu akcji **Powiększenie** zostanie wyświetlony wybrany zestaw danych. Wybierz formant formatu, aby przełączać się między trzema reprezentacjami osi y danych usługi Time Series Insights.

  [![Podgląd osi wykresu y](media/v2-update-explorer/tsi-preview-explorer-standard-chart.png)](media/v2-update-explorer/tsi-preview-explorer-standard-chart.png#lightbox)

W tym miejscu przedstawiono przykład **nakładającego się wykresu:**

  [![Opcja nakładania wykresu](media/v2-update-explorer/tsi-preview-explorer-overlapping-chart.png)](media/v2-update-explorer/tsi-preview-explorer-overlapping-chart.png#lightbox)

Przycisk **Więcej akcji** zostanie wyświetlony, aby wyświetlić funkcję Pobierz jako **CSV**, **Połącz z usługą Power BI**, Pokaż dane **wykresu jako tabelę**i **Opcje Eksploruj zdarzenia nieprzetworzone.**

  [![Opcja Więcej akcji](media/v2-update-explorer/more-actions-icon.png)](media/v2-update-explorer/more-actions-icon.png#lightbox)

Dowiedz się więcej o opcji **Połącz z serią Power BI** w [natywnym złączu usługi Power BI usługi Time Series Insights](concepts-power-bi.md).

## <a name="6-time-editor-panel"></a>6. Panel edytora czasu

Podczas pracy z time series insights najpierw wybierzesz przedział czasu. Wybrany przedział czasu będzie kontrolować zestaw danych, który jest dostępny do manipulowania za pomocą widżetów aktualizacji usługi Time Series Insights.

  [![Panel wyboru czasu](media/v2-update-explorer/tsi-preview-explorer-timeline-element.png)](media/v2-update-explorer/tsi-preview-explorer-timeline-element.png#lightbox)

> [!TIP]
> Część osi czasu jest podświetlona kolorem bursztynowym lub pomarańczowym, aby wskazać zakres danych dostępnych w ciepłym magazynie.

Następujące formanty sieci Web są dostępne w aktualizacji usługi Time Series Insights w celu wybrania przedziału czasu pracy. 

  [![Kontrola studni poszukiwawczych](media/v2-update-explorer/exploration-well-control.png)](media/v2-update-explorer/exploration-well-control.png#lightbox)

1. **Kontrolka suwaka wewnętrznego zakresu dat:** Użyj dwóch kontrolek punktu końcowego, przeciągając je w odpowiednim przedziale czasu. Ten wewnętrzny zakres dat jest ograniczony przez kontrolkę suwaka zewnętrznego zakresu dat.

1. **Zwiększanie i zmniejszanie przycisków zakresu dat:** Zwiększ lub zmniejsz swój czas, wybierając jeden z przycisków odpowiedniego interwału.

1. **Kontrola zwijania przedziału czasu:** Ta kontrolka sieci web umożliwia ukrycie wszystkich formantów z wyjątkiem narzędzia suwaka wewnętrznego zakresu dat.

1. **Kontrolka suwaka zewnętrznego zakresu dat:** Użyj kontrolek punktu końcowego, aby wybrać zewnętrzny zakres dat, który będzie dostępny dla wewnętrznej kontroli zakresu dat.

1. **Suwak zakresu czasu:** Użyj go, aby szybko przełączać się między wstępnie ustawionymi opcjami przedziału czasu, takimi jak ostatnie **30 minut,** **ostatnie 12 godzin**lub **zakres niestandardowy.** Zmiana tej wartości powoduje również zmianę dostępnych zakresów interwałów omówionych w narzędziu suwaka o rozmiarze interwału.

   [![Do i z panelu wyboru](media/v2-update-explorer/to-and-from-element.png)](media/v2-update-explorer/to-and-from-element.png#lightbox)

## <a name="7-app-bar"></a>7. Pasek aplikacji

Panel nawigacyjny Podgląd warstwy czasowej wglądu w dane czasowe jest wyświetlany u góry aplikacji Usługi Time Series Insights. Zapewnia następujące funkcje:

### <a name="current-session-share-link-control"></a>Kontrola łącza udziału bieżącej sesji

  [![Ikona Udostępnij](media/v2-update-explorer/tsi-preview-explorer-share-icon.png)](media/v2-update-explorer/tsi-preview-explorer-share-icon.png#lightbox)

Wybierz nową ikonę **Udostępnij,** aby udostępnić łącze URL swojemu zespołowi.

  [![Udostępnianie adresu URL wystąpienia](media/v2-update-explorer/tsi-preview-explorer-share-your-view.png)](media/v2-update-explorer/tsi-preview-explorer-share-your-view.png#lightbox)

### <a name="tenant-section"></a>Sekcja Dzierżawy

  [![Wybór dzierżawy](media/v2-update-explorer/tsi-preview-explorer-tenant-selection.png)](media/v2-update-explorer/tsi-preview-explorer-tenant-selection.png#lightbox)

* Wyświetla bieżące informacje o koncie logowania usługi Time Series Insights.
* Użyj go, aby przełączać się między dostępnymi motywami usługi Time Series Insights.
* Użyj go, aby wyświetlić podgląd [wersji demonstracyjnej aplikacji sieci web](https://insights.timeseries.azure.com/preview/demo).

### <a name="theme-selection"></a>Wybór motywu

Aby wybrać nowy motyw, wybierz ikonę profilu znajdującą się w prawym górnym rogu. Następnie wybierz pozycję **Zmień motyw**.

  [![Wybór motywu](media/v2-update-explorer/tsi-preview-theme-selection.png)](media/v2-update-explorer/tsi-preview-theme-selection.png#lightbox)

> [!TIP]
> Wybór języka jest również dostępny po wybraniu ikony profilu.

Usługa Azure Time Series Insights Preview obsługuje dwa motywy:

* **Motyw świetlny:** Domyślny motyw wyświetlany w tym dokumencie.
* **Ciemny motyw**: Renderuje eksploratora, jak pokazano tutaj:

  [![Wybrany ciemny motyw](media/v2-update-explorer/tsi-preview-dark-theme-selected.png)](media/v2-update-explorer/tsi-preview-dark-theme-selected.png#lightbox)

## <a name="s1s2-environment-controls"></a>Sterowanie środowiskiem S1/S2

### <a name="preview-terms-panel"></a>Panel Terminy podglądu

Ta sekcja dotyczy tylko istniejących środowisk S1/S2, które próbują użyć eksploratora w zaktualizowanym interfejsie użytkownika. Można użyć ogólnie dostępnego produktu i wersji zapoznawczej w połączeniu. Dodaliśmy niektóre funkcje z istniejącego interfejsu użytkownika do zaktualizowanego eksploratora, ale można uzyskać pełne środowisko interfejsu użytkownika dla środowiska S1/S2 w istniejącym Eksploratorze usługi Time Series Insights. 

Zamiast hierarchii wyświetlany jest panel Terminy usługi Time Series Insights. Panel terminów umożliwia definiowanie zapytań w twoim środowisku. Służy do filtrowania danych na podstawie predykatu.

  [![Gdzie panel kwerend](media/v2-update-explorer/s1-s2-preview-query.png)](media/v2-update-explorer/s1-s2-preview-query.png#lightbox)

W panelu edytora terminów usługi Time Series Insights Preview przyjmuje następujące parametry:

**Gdzie**: Użyj klauzuli where, aby szybko filtrować zdarzenia przy użyciu zestawu operandów wymienionych w poniższej tabeli. Jeśli przeprowadzisz wyszukiwanie, wybierając operand, predykat jest automatycznie aktualizowany na podstawie tego wyszukiwania. Obsługiwane typy operandów są następujące:

| Operacja | Obsługiwane typy   | Uwagi |
| --- | --- | --- |
| `<`, `>`, `<=`, `>=` | Pokój dwuosobowy, DateTime, TimeSpan | |
| `=`, `!=`, `<>` | Ciąg, Bool, Podwójny, Data), TimeSpan, NULL |
| `IN` | Ciąg, Bool, Podwójny, Data), TimeSpan, NULL | Wszystkie argumenty powinny być tego samego typu lub być stałą NULL. |
| `HAS` | Ciąg | Po prawej stronie dozwolone są tylko stałe literały ciągów. Pusty ciąg i wartość NULL nie są dozwolone. |

Aby dowiedzieć się więcej o obsługiwanych operacjach kwerend i typach danych, przeczytaj artykuł [Wyrażenie o szeregach czasowych (TSX)](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax).

### <a name="examples-of-where-clauses"></a>Przykłady, w których klauzule

  [![W przypadku gdy przykłady klauzul](media/v2-update-explorer/tsi-preview-example-queries.png)](media/v2-update-explorer/tsi-preview-example-queries.png#lightbox)

**Miara:** Lista rozwijana, która wyświetla wszystkie kolumny liczbowe (**Podwaja**) można użyć jako elementy dla bieżącego wykresu.

**Podziel według:** Ta lista rozwijana wyświetla wszystkie dostępne kolumny kategoryczne (Ciągi) w modelu, według których można pogrupować dane. Można dodać maksymalnie pięć terminów do wyświetlenia na tej samej osi x. Wprowadź żądane parametry, a następnie wybierz pozycję **Dodaj,** aby dodać nowy termin.

  [![Widok z zapytanym i filtrowany](media/v2-update-explorer/s1-s2-preview-filtered-view.png)](media/v2-update-explorer/s1-s2-preview-filtered-view.png#lightbox)

Elementy w panelu wykresu można wyświetlać i ukrywać, wybierając widoczną ikonę, jak pokazano na poniższej ilustracji. Aby całkowicie usunąć kwerendy, wybierz czerwony **X**.

  [![Anulowanie opcji kwerendy i filtrowania](media/v2-update-explorer/s1-s2-preview-filtered-view-cancel.png)](media/v2-update-explorer/s1-s2-preview-filtered-view-cancel.png#lightbox)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [magazynie i ruchu przychodzącym](./time-series-insights-update-storage-ingress.md) w usłudze Azure Time Series Insights Preview.

- Przeczytaj dokument Usługi Time Series Insights Preview na [temat modelowania danych](./time-series-insights-update-tsm.md).

- Dowiedz [się, jak diagnozować i rozwiązywać problemy z](./time-series-insights-update-how-to-troubleshoot.md) wystąpieniem usługi Time Series Insights.
