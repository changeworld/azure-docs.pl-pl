---
title: 'Samouczek: przewidywanie ceny za samochód przy użyciu narzędzia Projektant'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak uczenie, ocenę i wdrożenie modelu uczenia maszynowego przy użyciu interfejsu typu "przeciągnij i upuść". Ten samouczek jest częścią jednej z serii dwóch części na przewidywania cen samochodów, przy użyciu regresji liniowej.
author: peterclu
ms.author: peterlu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: 917ded03892f3a8a5812948bcbfe31f029fc5cf8
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2020
ms.locfileid: "76314984"
---
# <a name="tutorial-predict-automobile-price-with-the-designer"></a>Samouczek: przewidywanie ceny za samochód przy użyciu narzędzia Projektant
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

W tym dwuczęściowym samouczku dowiesz się, jak za pomocą projektanta Azure Machine Learning utworzyć i wdrożyć rozwiązanie do analizy predykcyjnej, które przewiduje cenę dowolnego samochodu. 

W części pierwszej należy skonfigurować środowisko, przeciągnąć moduły na interaktywną kanwę, a następnie połączyć je ze sobą w celu utworzenia potoku Azure Machine Learning.

W pierwszej części samouczka dowiesz się, jak:

> [!div class="checklist"]
> * Utwórz nowy potok.
> * Importuj dane.
> * Przygotuj dane.
> * Uczenie modelu uczenia maszynowego.
> * Oceń model uczenia maszynowego.

W [drugiej części](tutorial-designer-automobile-price-deploy.md) samouczka dowiesz się, jak wdrożyć model predykcyjny jako punkt końcowy inferencing w czasie rzeczywistym, aby przewidzieć cenę dowolnego samochodu w oparciu o specyfikacje techniczne, które wysyłasz. 

> [!NOTE]
>Kompletna wersja tego samouczka jest dostępna jako potoku przykładowe.
>
>Aby go znaleźć, przejdź do projektanta w obszarze roboczym. W sekcji **nowe potoku** wybierz pozycję **przykład 1-regresja: Automobile — Prognoza cenowa (podstawowa)** .

## <a name="create-a-new-pipeline"></a>Tworzenie nowego potoku

Potoki Azure Machine Learning organizują wiele zależnych etapów uczenia maszynowego i przetwarzania danych w ramach jednego zasobu. Potoki ułatwiają organizowanie i ponowne używanie złożonych przepływów pracy uczenia maszynowego między projektami i użytkownikami oraz zarządzanie nimi. Do utworzenia potoku Azure Machine Learning jest wymagany obszar roboczy Azure Machine Learning. W tej sekcji dowiesz się, jak utworzyć oba te zasoby.

### <a name="create-a-new-workspace"></a>Utwórz nowy obszar roboczy

Jeśli masz obszar roboczy Azure Machine Learning z wersją Enterprise, [Przejdź do następnej sekcji](#create-the-pipeline).

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal-enterprise.md)]

### <a name="create-the-pipeline"></a>Tworzenie potoku

1. Zaloguj się do [ml.Azure.com](https://ml.azure.com)i wybierz obszar roboczy, z którym chcesz współpracować.

1. Wybierz pozycję **Projektant**.

    ![Zrzut ekranu przedstawiający, jak uzyskać dostęp do projektanta](./media/tutorial-designer-automobile-price-train-score/launch-designer.png)

1. Wybierz **łatwe w użyciu wstępnie skompilowane moduły**.

1. Wybierz domyślny potok Nazwa potoku **— utworzony** w górnej części kanwy. Zmień jej nazwę na zrozumiałą. Przykładem jest *Prognoza cen na urządzeniu przenośnym*. Nazwa nie musi być unikatowa.

## <a name="import-data"></a>Importowanie danych

Projektant zawiera kilka przykładowych zestawów danych, z którymi można eksperymentować. Na potrzeby tego samouczka Użyj **danych cen samochodów (RAW)** . 

1. Na lewo od kanwy potoku jest paletą zestawów danych i modułów. Wybierz pozycję **zestawy danych**, a następnie Wyświetl sekcję **przykłady** , aby wyświetlić dostępne przykładowe zestawy danych.

1. Wybierz pozycję zestaw **danych cena samochodów (RAW)** i przeciągnij ją na kanwę.

   ![Przeciągnij dane do kanwy](./media/tutorial-designer-automobile-price-train-score/drag-data.gif)

### <a name="visualize-the-data"></a>Wizualizacja danych

Możesz wizualizować dane, aby zrozumieć zestaw danych, który będzie używany.

1. Wybierz moduł **dane cen samochodów (RAW)** .

1. W okienku właściwości z prawej strony kanwy wybierz pozycję dane **wyjściowe**.

1. Wybierz ikonę grafu, aby wizualizować dane.

    ![Wizualizacja danych](./media/tutorial-designer-automobile-price-train-score/visualize-data.png)

1. Wybierz różne kolumny w oknie dane, aby wyświetlić informacje o każdej z nich.

    Każdy wiersz reprezentuje samochód, a zmienne skojarzone z poszczególnymi urządzeniami przenośnymi są wyświetlane jako kolumny. Ten zestaw danych zawiera 205 wierszy i 26 kolumn.

## <a name="prepare-data"></a>Przygotowywanie danych

Zestawy danych zwykle wymagają pewnego przetworzenia przed analizą. Podczas inspekcji zestawu danych mogą znajdować się pewne brakujące wartości. Te brakujące wartości muszą zostać oczyszczone, aby model mógł prawidłowo analizować dane.

### <a name="remove-a-column"></a>Usuwanie kolumny

Podczas uczenia modelu trzeba wykonać coś dotyczące brakujących danych. W tym zestawie danych w kolumnie **znormalizowanych strat** brakuje wielu wartości, dlatego ta kolumna jest wykluczana z modelu całkowicie.

1. Wprowadź wartość **SELECT** w polu wyszukiwania w górnej części palety, aby znaleźć moduł **SELECT Columns in DataSet** .

1. Przeciągnij moduł **Wybierz kolumny w zestawie danych** na kanwę. Upuść moduł poniżej modułu DataSet.

1. Połącz zestaw danych **cen samochodów (RAW)** z modułem **Wybieranie kolumn w zestawie danych** . Przeciągnij z portu wyjściowego zestawu danych, czyli małego okręgu w dolnej części zestawu danych na kanwie, do portu wejściowego **SELECT kolumn w zestawie danych**, czyli małego okręgu w górnej części modułu.

    > [!TIP]
    > Przepływ danych można utworzyć za pomocą potoku po podłączeniu portu wyjściowego jednego modułu do portu wejściowego innego.
    >

    ![Połącz moduły](./media/tutorial-designer-automobile-price-train-score/connect-modules.gif)

1. Wybierz pozycję **Wybierz kolumny w zestawie danych** .

1. W okienku właściwości po prawej stronie kanwy wybierz pozycję **parametry** > **Edytuj kolumnę**.

1. Wybierz **+** , aby dodać nową regułę.

1. Z menu rozwijanego wybierz opcję **Wyklucz** i **nazwy kolumn**.
    
1. Wprowadź *znormalizowane straty* w polu tekstowym.

1. W prawym dolnym rogu wybierz pozycję **Zapisz** , aby zamknąć selektor kolumny.

    ![Wykluczanie kolumny](./media/tutorial-designer-automobile-price-train-score/exclude-column.png)
        
    Okienko właściwości pokazuje, że kolumna **znormalizowana strata** jest wykluczona.

1. Wybierz pozycję **Wybierz kolumny w zestawie danych** . 

1. W okienku właściwości wybierz pozycję **parametry** > **komentarz** i wprowadź *wykluczanie znormalizowanych strat*.

### <a name="clean-missing-data"></a>Wyczyść brakujące dane

Zestaw danych nadal ma brakujące wartości po usunięciu kolumny **znormalizowanych strat** . Pozostałe brakujące dane można usunąć przy użyciu modułu **czyste brakujące dane** .

> [!TIP]
> Czyszczenie brakujących wartości z danych wejściowych jest wymaganiem wstępnym w przypadku korzystania z większości modułów w projektancie.

1. Wprowadź **Wyczyść** w polu wyszukiwania, aby znaleźć **nieczysty moduł danych** .

1. Przeciągnij **nieczysty moduł danych** do kanwy potoku. Połącz go z modułem **Wybieranie kolumn w zestawie danych** . 

1. W okienku właściwości wybierz pozycję **Usuń cały wiersz** w obszarze **Tryb czyszczenia**.

1. W okienku właściwości w polu **komentarz** wprowadź *Usuń brakujące wiersze wartości*. 

    Potok powinien teraz wyglądać następująco:
    
    ![Zaznacz kolumnę](./media/tutorial-designer-automobile-price-train-score/pipeline-clean.png)

## <a name="train-a-machine-learning-model"></a>Uczenie modelu uczenia maszynowego

Teraz, gdy dane są przetwarzane, można przeprowadzić uczenie modelu predykcyjnego.

### <a name="select-an-algorithm"></a>Wybieranie algorytmu

Algorytmy *klasyfikacji* i *regresji* to dwa typy nadzorowanego uczenia maszynowego. Klasyfikacja przewiduje odpowiedź ze zdefiniowanego zestawu kategorii, takich jak kolor czerwony, niebieski lub zielony. Regresja służy do prognozowania liczby.

Ponieważ chcesz przewidzieć cenę, która jest liczbą, możesz użyć algorytmu regresji. W tym przykładzie używany jest model regresji liniowej.

### <a name="split-the-data"></a>Dzielenie danych

Podziel dane na dwa osobne zestawy danych, aby przeanalizować model i przetestować go.

1. Wprowadź **Podziel dane** w polu wyszukiwania, aby znaleźć moduł **Split Data** . Połącz go z lewym portem **czystego brakujących danych** .

1. Wybierz moduł **Split Data** .

1. W okienku właściwości ustaw **ułamek wierszy w pierwszym zestawie danych wyjściowych** na 0,7.

    Ta opcja dzieli na 70 procent danych, aby szkolić model i 30 procent na potrzeby testowania.

1. W **okienku właściwości wpisz polecenie** *Podziel zestaw danych na zestaw szkoleniowy (0,7) i zestaw testów (0,3)* .

### <a name="train-the-model"></a>Trenowanie modelu

Uczenie modelu przez nadanie mu zestawu danych, który zawiera cenę. Model skanuje dane i wyszukuje korelacje między funkcjami samochodu a jego ceną do konstruowania modelu.

1. Aby wybrać algorytm uczenia, usuń zaznaczenie pola wyszukiwania palety modułu.

1. Rozwiń **algorytmy Machine Learning**.
    
    Ta opcja umożliwia wyświetlenie kilku kategorii modułów, których można użyć do zainicjowania algorytmów uczenia.

1. Wybierz **regresję** > **regresji liniowej**i przeciągnij ją na kanwę potoku.

1. Znajdź i przeciągnij moduł **uczenie modelu** na kanwę potoku. 

1. Połącz dane wyjściowe modułu **regresji liniowej** z lewym wejściem modułu **uczenie modelu** .

1. Połącz Wyjście danych szkoleniowych (lewy port) modułu **Split Data (podział danych** ) z prawym wejściem modułu **uczenie modelu** .

    ![Zrzut ekranu przedstawiający poprawną konfigurację modułu uczenie modelu. Moduł regresja liniowa łączy się z lewym portem modułu uczenia modelowego, a moduł Split Data łączy się z odpowiednim portem modelu uczenia](./media/tutorial-designer-automobile-price-train-score/pipeline-train-model.png)

1. Wybierz moduł **uczenie modelu** .

1. W okienku właściwości wybierz pozycję **Edytuj selektor kolumny** .

1. W oknie dialogowym **etykieta kolumny** rozwiń menu rozwijane i wybierz pozycję **nazwy kolumn**. 

1. W polu tekstowym wprowadź *Price*. Cena to wartość, która jest przewidywana przez model.

    Potok powinien wyglądać następująco:

    ![Zrzut ekranu przedstawiający poprawną konfigurację potoku po dodaniu modułu uczenie modelu.](./media/tutorial-designer-automobile-price-train-score/pipeline-train-graph.png)

## <a name="evaluate-a-machine-learning-model"></a>Oceń model uczenia maszynowego

Po nauczeniu modelu przy użyciu 70 procent danych, można użyć go do oceny pozostałych 30 procent, aby zobaczyć, jak dobrze działa model.

1. Wprowadź ciąg " *model oceny* " w polu wyszukiwania, aby znaleźć moduł **modelu oceny** . Przeciągnij moduł do kanwy potoku. 

1. Połącz dane wyjściowe modułu **uczenie modelu** z lewym portem wejściowym **modelu wynikowego**. Połącz dane wyjściowe testu (prawy port) modułu **Split Data (dane** wejściowe) z odpowiednim portem wejściowym **modelu wynikowego**.

1. Wprowadź *wartość Oceń* w polu wyszukiwania, aby znaleźć moduł **Oceń model** . Przeciągnij moduł do kanwy potoku. 

1. Połącz dane wyjściowe modułu z **modelem wynikowym** z lewym wejściem do **oceny modelu**. 

    Końcowy potok powinien wyglądać następująco:

    ![Zrzut ekranu przedstawiający poprawną konfigurację potoku.](./media/tutorial-designer-automobile-price-train-score/pipeline-final-graph.png)

### <a name="run-the-pipeline"></a>Uruchamianie potoku

[!INCLUDE [aml-ui-create-training-compute](../../includes/aml-ui-create-training-compute.md)]

### <a name="view-results"></a>Wyświetlanie wyników

Po zakończeniu przebiegu można wyświetlić wyniki uruchomienia potoku. 

1. Wybierz moduł **model oceny** , aby wyświetlić jego dane wyjściowe.

1. W okienku właściwości wybierz pozycję **wyjściowe** > **Wizualizacja**.

    W tym miejscu możesz zobaczyć przewidywane ceny i rzeczywiste ceny z danych testowych.

    ![Zrzut ekranu przedstawiający wizualizację danych wyjściowych z wyróżnioną kolumną etykieta z wynikami](./media/tutorial-designer-automobile-price-train-score/score-result.png)

1. Wybierz moduł **Oceń model** , aby wyświetlić jego dane wyjściowe.

1. W okienku właściwości wybierz pozycję **Output** > **wizualizator**.

Następujące statystyki są wyświetlane dla modelu:

* **Średni błąd bezwzględny (Mae)** : Średnia liczba błędów bezwzględnych. Jest to różnica między wartością przewidywaną a wartością rzeczywistą.
* **Błąd średnika "pierwiastek" z wartości głównej (RMSE)** : pierwiastek kwadratowy średniej wartości kwadratowych błędów prognoz wykonanych na testowym zestawie danych.
* **Względny błąd absolutny**: iloraz średniej błędów absolutnych i bezwzględnej wartości różnicy między wartościami rzeczywistymi a średnią wszystkich wartości rzeczywistych.
* **Błąd względny średniokwadratowy**: iloraz średniej kwadratów błędów i kwadratu różnicy między wartościami rzeczywistymi a średnią wszystkich wartości rzeczywistych.
* **Współczynnik wyznaczania**: znany również jako wartość R kwadratowa, ta Metryka statystyczna wskazuje, jak dobrze model dopasowuje dane.

W przypadku wszystkich powyższych statystyk mniejsze wartości oznaczają lepszą jakość modelu. Mniejsza wartość wskazuje, że przewidywania są bliżej rzeczywistych wartości. Dla współczynnika wyznaczania wartość bliższej wartości to 1 (1,0), tym lepsze przewidywania.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

W pierwszej części tego samouczka zostały wykonane następujące zadania:

* Tworzenie potoku
* Przygotowywanie danych
* Trenowanie modelu
* Ocena i Ocena modelu

W części drugiej dowiesz się, jak wdrożyć model jako punkt końcowy w czasie rzeczywistym.

> [!div class="nextstepaction"]
> [Kontynuuj Wdrażanie modeli](tutorial-designer-automobile-price-deploy.md)
