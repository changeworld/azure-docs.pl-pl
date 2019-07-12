---
title: 'Klasyfikacja: Przewidywanie opóźnień lotów'
titleSuffix: Azure Machine Learning service
description: W tym artykule przedstawiono sposób tworzenia modelu do prognozowania opóźnień lotów przy użyciu przeciągania i upuszczania interfejs graficzny i niestandardowy kod R uczenia maszynowego.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: article
author: xiaoharper
ms.author: zhanxia
ms.reviewer: peterlu
ms.date: 07/02/2019
ms.openlocfilehash: 773e55fe4b5ca5acf27ba1765e5a16075f625187
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2019
ms.locfileid: "67607639"
---
# <a name="sample-6---classification-predict-flight-delays-using-r"></a>Przykład 6 - klasyfikacji: Prognozowanie opóźnień lotów przy użyciu języka R

Ten eksperyment używane są lotu historycznych i danych o pogodzie w celu przewidywania, jeśli zaplanowany lot pasażerski będzie opóźniony o ponad 15 minut.

Ten problem może być skontaktowali się jako problem klasyfikacji, przewidywanie dwóch klas — opóźnione, lub na czas. Tworzenie klasyfikatora, ten model przy użyciu dużej liczby przykłady z danych historycznych lotów.

Oto wykres eksperyment końcowy:

[![Wykres eksperymentu](media/ui-sample-classification-predict-flight-delay/experiment-graph.png)](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Wybierz **Otwórz** przycisku w eksperymencie przykład 6:

    ![Otwórz eksperyment](media/ui-sample-classification-predict-flight-delay/open-sample6.png)

## <a name="get-the-data"></a>Pobieranie danych

Eksperymentowanie to wykorzystania **danych dotyczących opóźnień lotów** zestawu danych. Tego część gromadzenia danych TranStats Stanów Zjednoczonych Z departamentu transportu. Zestaw danych zawiera informacje opóźnienia lotów od kwietnia do października 2013. Przed przekazaniem danych do interfejsu wizualnego, jej wstępnym przetworzeniu w następujący sposób:

* Filtrowana w celu uwzględnienia 70 lotniskach najczęściej odwiedzane w kontynentalnych Stanach Zjednoczonych.
* Aby uzyskać anulowane lotów, relabeled jako opóźniony o ponad 15 minut.
* Odfiltrowane lotów kierunku.
* Wybrane kolumny 14.

Aby uzupełnić danych lotów **zestaw danych o pogodzie** jest używany. Dane o pogodzie zawiera godzinowe obserwacje lądową pogodowe z agencji NOAA i reprezentuje uwagi z stacje pogody port lotniczy, obejmujący takie same okresu o długości kwietnia — październik 2013. Przed przekazaniem do usługi Azure ML interfejs graficzny, jej wstępnym przetworzeniu w następujący sposób:

* Identyfikatory pogodowa byli zamapowani na lotnisku odpowiednich identyfikatorów.
* Pogoda stacji nie jest skojarzona z 70 lotniskach najczęściej odwiedzane zostały usunięte.
* Kolumna dat został podzielony na oddzielne kolumny: Rok, miesiąc i dzień.
* Wybrane kolumny 26.

## <a name="pre-process-the-data"></a>Wstępne przetwarzanie danych

Zestaw danych wymaga, niektóre przetwarzania wstępnego przed mogą być analizowane.

![data-process](media/ui-sample-classification-predict-flight-delay/data-process.png)

### <a name="flight-data"></a>Dane lotu

Kolumny **operatora**, **OriginAirportID**, i **DestAirportID** są zapisywane jako liczby całkowite. Jednak są one podzielone na kategorie atrybutów, należy użyć **edytować metadane** modułu w celu przekonwertowania ich do kategorii.

![edit-metadata](media/ui-sample-classification-predict-flight-delay/edit-metadata.png)

Następnie użyj **wybierz kolumny** w module zestawu danych, aby wykluczyć z kolumny zestawu danych, które są leakers możliwe miejsce docelowe: **DepDelay**, **DepDel15**, **ArrDelay**, **anulowane**, **roku**. 

Aby przyłączyć rekordów lotu godzinowe rekordy pogody, czasu zaplanowanego wyjścia należy używać jako jeden z kluczy sprzężenia. Celu przyłączenia kolumn CSRDepTime musi zaokrąglona w dół do najbliższej godzinę odbywa się przez w **wykonanie skryptu języka R** modułu. 

### <a name="weather-data"></a>Dane o pogodzie

Kolumny, które mają duży odsetek brakujących wartości są wyłączone, za pomocą **kolumny projektu** modułu. Kolumny te obejmują wszystkie kolumny z ciągami: **ValueForWindCharacter**, **WetBulbFarenheit**, **WetBulbCelsius**, **PressureTendency**, **PressureChange**, **SeaLevelPressure**, i **StationPressure**.

**Clean Missing Data** modułu jest następnie stosowane do pozostałych kolumn w celu usunięcia wierszy z brakującymi danymi.

Czasy obserwacji pogody jest zaokrąglana do najbliższej całą godzinę. Czas zaplanowany lot i czasy obserwacji pogody są zaokrąglane w przeciwnych kierunkach, aby upewnić się, że model wykorzystuje tylko pogody przed upływem terminu lotu. 

Ponieważ dane o pogodzie jest zgłaszane w czasie lokalnym, różnice stref czasowych są rozliczane przez odjęcie kolumny strefy czasowej z czas zaplanowanego wyjścia i obserwowanie o pogodzie. Te operacje są wykonywane przy użyciu **wykonanie skryptu języka R** modułu.

### <a name="joining-datasets"></a>Łączenie zestawów danych

Rekordy lotów są łączone z danych o pogodzie w miejscu pochodzenia lotu (**OriginAirportID**) przy użyciu **Dołącz dane** modułu.

 ![Dołącz lotu i pogoda według źródła](media/ui-sample-classification-predict-flight-delay/join-origin.png)


Rekordy lotów są łączone z danych o pogodzie przy użyciu docelowego lotu (**DestAirportID**).

 ![Dołącz do lotu i pogoda według docelowego](media/ui-sample-classification-predict-flight-delay/join-destination.png)

### <a name="preparing-training-and-test-samples"></a>Przygotowywanie szkoleń i próbki

**Podziału danych** modułu dzieli dane w kwietniu za pośrednictwem rekordów września szkolenia i października rekordów dla testu.

 ![Podziel, szkolenie i testowanie danych](media/ui-sample-classification-predict-flight-delay/split.png)

Rok, miesiąc i strefy czasowej kolumny są usuwane z zestawu danych szkoleniowych, przy użyciu modułu Select Columns.

## <a name="define-features"></a>Definiowanie funkcji

W uczenia maszynowego funkcje są poszczególne mierzalne właściwości interesujących Cię w. Znajdowanie szerokiego zestawu funkcji wymaga znajomości eksperymentowania i domeny. Pewne cechy lepiej nadają się do prognozowania danych docelowych. Ponadto niektóre funkcje mogą są ściśle powiązane z innymi funkcjami i nie będzie dodać nowe informacje do modelu. Te funkcje mogą zostać usunięte.

Do tworzenia modelu, możesz korzystać ze wszystkich funkcji lub wybrać podzbiór funkcji.

## <a name="choose-and-apply-a-learning-algorithm"></a>Wybieranie i stosowanie algorytmu uczenia

Tworzenie przy użyciu modelu **regresji logistycznej Two-Class** modułu i nauczenia go w zestawie danych szkoleniowych. 

Wynik **Train Model** moduł jest model klasyfikacji przeszkolonych, który może służyć do oceniania nowe przykłady w celu prognozowania. Za pomocą testu zestawu do generowania wyników z przeszkolone modele. Następnie użyj **Evaluate Model** modułu do analizowania i porównywania jakość modeli.

Po uruchomieniu eksperymentu można wyświetlić dane wyjściowe z **Score Model** modułu, kliknij port wyjściowy i wybierając **Visualize**. Dane wyjściowe obejmują ocenami etykiety i prawdopodobieństwa dla etykiet.

Na koniec przetestować jakość wyników, Dodaj **Evaluate Model** modułów na eksperyment kanwy, a następnie połącz lewy port wejściowy z danymi wyjściowymi modułu Score Model. Uruchom eksperyment, aby wyświetlić dane wyjściowe **Evaluate Model** modułu, kliknij port wyjściowy i wybierając **Visualize**.

## <a name="evaluate"></a>Evaluate
Model regresji logistycznej ma AUC 0.631 na testowej, w zestawu.

 ![evaluate](media/ui-sample-classification-predict-flight-delay/evaluate.png)

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z innymi przykładami dostępne dla interfejsu wizualnego:

- [Przykład 1 - regresji. Prognozowanie cen samochodów](ui-sample-regression-predict-automobile-price-basic.md)
- [Przykład 2 - regresji. Porównaj algorytmy Prognozowanie cen samochodów](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [Próba 3 — klasyfikacji: Prognozowanie ryzyka kredytowego](ui-sample-classification-predict-credit-risk-basic.md)
- [Przykład 4 - klasyfikacji: Prognozowanie ryzyka kredytowego (koszt poufnych)](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [Przykład 5 - klasyfikacji: Przewidywanie zmienności](ui-sample-classification-predict-churn.md)
