---
title: Wizualizowanie danych w ramach aktualizacji Explorer | Dokumentacja firmy Microsoft
description: Azure Time Series Insights aktualizacja programu Explorer
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 11/28/2018
ms.openlocfilehash: bf091eec271e3fb27f6ab312ff5d0096efa7f90c
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2018
ms.locfileid: "52856388"
---
# <a name="visualize-data-in-the-explorer-update"></a>Wizualizowanie danych w Eksploratorze aktualizacji

W tym artykule opisano funkcje i opcje dostępne w ramach usługi Azure Time Series Insights (wersja zapoznawcza) aplikacji sieci web programu Explorer.

## <a name="prerequisites"></a>Wymagania wstępne

Zanim użyjesz usługi Azure Time Series Insights (wersja zapoznawcza) Explorer, musisz mieć:

* Masz środowisko usługi Time Series Insights aprowizowane. Dowiedz się więcej na temat aprowizowania w środowisku usługi Time Series Insights.
* Zapewnia dostęp do danych do środowiska usługi Time Series Insights, utworzony dla konta. Mogą być dostarczane dostępu do innych użytkowników, jak również samodzielnie.
* Dodawanie źródła zdarzeń do środowiska usługi Time Series Insights w celu wypychania danych do środowiska.

## <a name="learn-about-the-azure-time-series-insights-preview-explorer"></a>Dowiedz się więcej o usłudze Azure Time Series Insights (wersja zapoznawcza) Eksploratora

  ![jeden Explorer][1]

Azure Time Series Insights (wersja zapoznawcza) Explorer został podzielony na następujące siedem elementów:

1. Pasek nawigacyjny TSI (wersja zapoznawcza) umożliwia przełączanie między stronami analizy i modelu.
1. Drzewo hierarchia TSI (wersja zapoznawcza) wybierz elementy określonych danych do wykreślenia.
1. Szeregi czasowe TSI (wersja zapoznawcza) zawiera również wszystkie elementy aktualnie wybrane dane.
1. Panel wykresu TSI (wersja zapoznawcza) Wyświetla bieżącego wykresu pracy.
1. Oś czasu TSI (wersja zapoznawcza) umożliwia modyfikowanie przedział czasu swojej pracy.
1. Pasek aplikacji TSI (wersja zapoznawcza) zawiera opcje zarządzania użytkownika (na przykład bieżącej dzierżawy) i umożliwia zmianę ustawień motywu i języka.

## <a name="tsi-preview-environment-panel"></a>Panel środowiska TSI (wersja zapoznawcza)

Na panelu środowiska są wyświetlane wszystkie środowiska TSI, do których masz dostęp do. Obejmuje to lista środowisk zgodnie z rzeczywistym użyciem (wersja zapoznawcza) oraz środowisk S1/S2 (GA). Po prostu kliknij środowiska TSI, którego chcesz użyć.

  ![dwa Explorer][2]

## <a name="time-series-insights-preview-navigation-menu"></a>Usługa Time Series Insights (wersja zapoznawcza) Menu nawigacji

  ![trzy Explorer][3]

W menu nawigacji służy do przełączania się między aplikacjami TSI:

* Analizuj — pozwala na wykresie i wykonywać zaawansowane analizy na modelowanych lub niemodelowanych szeregami czasowymi.

* Model - umożliwia wypychanie nowych typów aktualizacji TSI, hierarchie i wystąpienia do modelu usługi TSI.

## <a name="time-series-insights-update-model-authoring"></a>Usługa Time Series Insights aktualizacji, tworzenia modelu

Ta aplikacja zapewnia możliwość wykonywania operacji CRUD na Twoje modelu szeregów czasowych.  

* Typ TSM — typy TSI umożliwiają definiowanie zmiennych lub formuły do wykonywania obliczeń, są one skojarzone z danego wystąpienia usługi TSI. Typ może mieć co najmniej jednej zmiennej.
* Hierarchia TSM — hierarchie są systematycznego organizacji danych. Hierarchie przedstawiać relacje między różnymi jednostkami danych usługi TSI.
* Wystąpienia TSM — wystąpienia są szeregów czasowych, samodzielnie. W większości przypadków będzie to **DeviceID** lub **AssetID**, który jest unikatowy identyfikator elementu zawartości w środowisku.

Aby dowiedzieć się więcej na temat TSM, przeczytaj [modeli szeregów razy](./time-series-insights-update-tsm.md).

## <a name="time-series-insights-preview-model-search-panel"></a>Usługa Time Series Insights (wersja zapoznawcza) modelu wyszukiwania Panelu

Panel wyszukiwania modelu pozwala na łatwe wyszukiwanie i przejdź hierarchii TSM można znaleźć wystąpienia serii określony czas, które mają być wyświetlane na wykresie. Po wybraniu wystąpień, nie są dodawane tylko do bieżącego wykresu, ale są również dodawane do danych dobrze.

  ![cztery Explorer][4]

## <a name="time-series-insights-preview-well"></a>Usługa Time Series Insights (wersja zapoznawcza) oraz

Również wyświetla pola wystąpienia i inne metadane skojarzone z wybranym wystąpienia serii. Pola wyboru po prawej stronie umożliwiają ukryć lub wyświetlić określone wystąpienia z bieżącego wykresu. Można również usunąć elementy określonych danych z bieżących danych, klikając kontrolki czerwony znak x z prawej strony elementu.

  ![pięć Explorer][5]

Można również oknach panelu telemetrię można również pobrać lepiej widok pionowy elementów w Twoich danych.

  ![Eksplorator 6][6]

Uwaga: Jeśli widzisz następującej ikony, wystąpienie nie ma żadnych danych podczas wybrany zakres czasu.  Aby rozwiązać problem, można zwiększyć wybrany zakres czasu lub upewnij się, że wystąpienie jest wypychanie danych.

  ![Eksplorator 7][7]

## <a name="time-series-insights-preview-chart"></a>Usługa Time Series Insights (wersja zapoznawcza) wykresu

Wykres umożliwia wyświetlanie wystąpienia serii czasu jako wiersze. Można zwinąć panelu środowiska, modelu danych i godzina w Panelu sterowania zakresu, klikając kontrolki sieci web, aby powiększyć wykresu.

  ![Eksplorator 8][8]

1. Wybrany zakres dat — obecnie wybranego zakresu dat. panelu wykresu, to określa elementy, które dane będą dostępne dla wizualizacji.

1. Wewnętrzny narzędzie suwaka zakresu dat — użyj dwóch kontrolek punktu końcowego, klikając i przeciągając je wraz z upływem czasu żądanego zakresu.

1. Formant Zwiń zakresu czasu — ten formant zwija i rozwija Edytor panelu zakresu czasu.

1. Oś y format — kliknij formantu to kontrolę przechodzić przez dostępne opcje osi y w widoku. Są dostępne opcje osi y w widoku:

    * Domyślnie — każdy wiersz zawiera poszczególne osi y
    * Skumulowany — pozwala to na stos wielu wierszy w tej samej osi y, przy użyciu danych osi y zmiany na podstawie wiersza wybrane
    * Udostępnione — wyświetlić razem wszystkie dane osi y

1. Bieżący element danych — obecnie wybranego elementu danych i jego szczegóły.

Można przejść do szczegółów w wycinka danych określonych przez kliknięcie lewym przyciskiem myszy punkt danych na wykresie bieżącego podczas przytrzymując przycisk myszy, a następnie przeciągając zaznaczonego obszaru do wybranego punktu końcowego. Kliknij prawym przyciskiem myszy obszar wygaszone, wybrane, a następnie kliknij przycisk powiększenia, jak pokazano poniżej. Możesz również wykonać następujące czynności:

  ![dziewięć Explorer][9]

Po wykonaniu akcji powiększania, zobaczą teraz wybrany zestaw danych. Kliknij na formancie format osi y, aby przechodzić między trzy różne reprezentacje osi y danych TSI.

  ![dziesięć Explorer][10]

Tutaj można zobaczyć przykład udostępnionego osiami y.

  ![Eksplorator eleven][11]

## <a name="time-series-insights-preview-time-editor-panel"></a>Usługa Time Series Insights (wersja zapoznawcza) panelu Edytor czasu

Podczas pracy z usługi TSI użytkownik najpierw wybierze przedział czasu. Wybrany okres będzie kontrolować zestawu danych, która jest dostępna do manipulowania widżetów aktualizacji usługi TSI. Następujące kontrolki sieci web są dostępne w aktualizacji usługi TSI służąca do wybierania usługi przedział czasu pracy.

  ![dwanaście Explorer][12]

1. **Narzędzie suwaka zakresu dat wewnętrzny** — należy użyć dwóch kontrolek punktu końcowego, klikając i przeciągając je wraz z upływem czasu żądaną span. Ten zakres "Wewnętrzny daty" będzie ograniczone przez kontrolki suwaka zakresu "Zewnętrzne daty" wymienionych poniżej.

1. Zwiększyć i zmniejszenie data zakresu przyciski ** — zwiększenie lub zmniejszenie swoje przedział czasu, klikając przycisk albo dla interwału, który ma.

1. **Czas formantu Zwiń zakresu** -tego formantu sieci web pozwala na ukrycie formantów, z wyjątkiem narzędzia suwaka zakresu dat wewnętrzny.

1. **Kontrolka suwaka zakresu dat zewnętrzne** — za pomocą formantów punktu końcowego wybierz zakres dat zewnętrznego, która będzie dostępna dla kontrolki zakresu "Wewnętrzny daty".

1. **Zakres dat krótkie okresy listę rozwijaną** — daje możliwość szybkiego przełączania się między wstępnie ustawiony czas zakresu opcje ostatnich 30 minut ostatnich 12 godzin, niestandardowego zakresu, np. Zmiana ta wartość zmienia się również zakresy dostępne interwał omówionej w narzędziu interwał Rozmiar suwaka.

1. **Rozmiar interwału suwak** — narzędzie suwaka rozmiar interwału umożliwia powiększanie i pomniejszanie odstępach czasu za pośrednictwem tego samego przedział czasu. Zapewnia to dokładniejszą kontrolę ruchu pomiędzy dużych okresy przedstawiających smooth trendy w dół wycinki tak małej, jak milisekund, dzięki czemu możesz widzieć cenowe szczegółowe, wysokiej rozdzielczości, dane. Domyślne suwaka punkt początkowy jest ustawiony jako najbardziej optymalną wgląd w dane z zaznaczenia; Równoważenie rozdzielczość, prędkość wysyłania zapytań i stopień szczegółowości.

1. **Zakres do i z formantu sieci web dat** — za pomocą tego formantu sieci web, można łatwo kliknąć i wybierz odpowiednią datę i zakresy czasu. Kontrolka umożliwia również przełączać się między różnych strefach czasowych. Po wprowadzeniu zmian, o których chcesz zastosować do bieżącego obszaru roboczego kliknij pozycję Zapisz przycisku.

  ![Eksplorator trzynaście][13]

## <a name="time-series-insights-preview-navigation-panel"></a>Panel nawigacji Series Insights (wersja zapoznawcza) czasu

Panel nawigacyjny aktualizacji TSI oferuje następujące funkcje:

  ![Eksplorator czternastu][14]

### <a name="current-session-share-link-control"></a>Kontrola łącza udział w bieżącej sesji

  ![Eksplorator 15][15]

Kliknij link kółku kontrolki sieci web do generowania adresu URL, aby zapisać lub udostępnić Twojej bieżącej sesji pracy usługi Time Series Insights, w tym:

* Obecnie wybranego zakresu czasu
* Rozmiar interwału zaznaczony
* Aktualnie wybrane dane również

### <a name="tenant-section"></a>Sekcja dzierżawy

  ![Eksplorator 16][16]

* Wyświetla bieżące informacje o koncie logowania TSI
* Pozwala przełączać się między dostępnych motywów TSI.

### <a name="theme-selection"></a>Wybór motywu

Azure TSI (wersja zapoznawcza) obsługuje dwa motywy:

* **Motyw jasny**: jest to domyślny motyw, przedstawione w tym dokumencie.
* **Motyw ciemny**: Ta opcja powoduje wyświetlenie Eksploratora do motyw ciemny jak pokazano poniżej:

  ![Eksplorator siedemnastu][17]

W tym miejscu możesz również zmienić między obsługiwanych języków.

## <a name="s1s2-environment-controls"></a>Kontrolki środowiska S1/S2

### <a name="time-series-insights-preview-terms-panel"></a>Usługa Time Series Insights (wersja zapoznawcza) Panel terminów

Ta sekcja dotyczy tylko istniejących środowisk S1/S2 próby w Interfejsie użytkownika zaktualizowane przy użyciu Eksploratora. Można to zrobić, aby zostanie zaktualizowany (wersja zapoznawcza) w połączeniu ze sobą i korzystania z produktu w wersji ogólnie dostępnej. Firma Microsoft dodane niektóre funkcje z istniejącego interfejsu użytkownika do zaktualizowanych explorer, ale o tym, że zawsze można uzyskać pełnego środowiska interfejsu użytkownika dla środowiska S1/S2 w istniejących Eksplorator usługi TSI.  

Audytów hierarchii zostanie wyświetlony panel terminów TSI. Jest to, gdzie definiowania kwerend w danym środowisku. Daje możliwość filtrowania danych na podstawie przy użyciu predykat.

  ![Eksplorator 18][18]

Usługa TSI Przesyła (wersja zapoznawcza) Panel Edytor warunków przyjmuje następujące parametry

**Gdzie**: gdzie klauzula umożliwia szybkie filtrowanie zdarzeń przy użyciu zestawu argumentów wymienione poniżej. Jeśli wyszukiwania, wybierając klikając, predykatu będą automatycznie aktualizowane na podstawie tego wyszukiwania. Operand obsługiwane typy:

| Operacja | Obsługiwane typy   | Uwagi |
| --- | --- | --- |
| `<`, `>`, `<=`, `>=` |    Double, DateTime, TimeSpan  | |
| `=`, `!=`, `<>`   | String, Bool, Double, DateTime, przedział czasu, wartość NULL    |
| `IN` |    String, Bool, Double, DateTime, przedział czasu, wartość NULL |    Wszystkie argumenty powinny być tego samego typu lub być stała NULL. |
| `HAS` |   Ciąg |    Po prawej stronie dozwolone są tylko stałe literałów. Pusty ciąg i wartości NULL są niedozwolone. |

### <a name="examples-of-where-clauses"></a>Przykłady gdzie klauzule

  ![Eksplorator tysiąc][19]

**Miara**: Ta lista rozwijana pokazuje wszystkie kolumny liczbowe (**wartości podwójnej precyzji**), można użyć jako elementy, dla bieżącego wykresu.

**Podziel według**: tej listy rozwijanej wyświetlane wszystkie kolumny podzielone na kategorie (ciągi) w modelu dostępne, służące do grupowania danych przez. Możesz dodać maksymalnie pięć warunki, aby wyświetlić na tej samej osi x. Wprowadź odpowiednie parametry, a następnie użyj **Dodaj** przycisk, aby dodać świeże termin.

  ![dwadzieścia Explorer][20]

Można ukryć i pokazać elementy z panelu wykresu, klikając ikonę widoczne, jak pokazano poniżej. Zapytania można całkowicie usunąć, klikając czerwoną `X` pokazano poniżej.

  ![Eksplorator dwadzieścia jednego][21]

## <a name="next-steps"></a>Kolejne kroki

Odczyt [Azure TSI (wersja zapoznawcza), magazynu i transferu danych przychodzących](./time-series-insights-update-storage-ingress.md).

Przeczytaj o nowym [modeli szeregów czasowych](./time-series-insights-update-tsm.md).

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