---
title: 'Samouczek: przewidywanie ceny samochodów z interfejsem wizualnym'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak uczenie, ocenę i wdrożenie modelu uczenia maszynowego przy użyciu graficznego interfejsu przeciągania i upuszczania. Ten samouczek jest częścią jednej z serii dwóch części na przewidywanie cen samochodów, przy użyciu regresji liniowej.
author: peterclu
ms.author: peterlu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 10/09/2019
ms.openlocfilehash: b0c9fd85171020c9b78dc166980f85bcd89d8d67
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2019
ms.locfileid: "72692285"
---
# <a name="tutorial-predict-automobile-price-with-the-visual-interface"></a>Samouczek: przewidywanie ceny samochodów z interfejsem wizualnym

W tym dwuczęściowym samouczku dowiesz się, jak za pomocą interfejsu wizualnego Azure Machine Learning opracowywać i wdrażać rozwiązanie do analizy predykcyjnej, które przewiduje cenę dowolnego samochodu. 

W części pierwszej należy skonfigurować środowisko, przeciągać i upuszczać moduły na interaktywną kanwę, a następnie połączyć je ze sobą w celu utworzenia potoku Azure Machine Learning.

W pierwszej części samouczka dowiesz się, jak:

> [!div class="checklist"]
> * Tworzenie nowego potoku
> * Importowanie danych
> * Przygotowywanie danych
> * Uczenie modelu uczenia maszynowego
> * Oceń model uczenia maszynowego

W [drugiej części](ui-tutorial-automobile-price-deploy.md) samouczka dowiesz się, jak wdrożyć model predykcyjny jako usługę sieci Web platformy Azure, aby przewidzieć cenę dowolnego samochodu w oparciu o specyfikacje techniczne, które wysyłasz. 

> [!Note]
>Kompletna wersja tego samouczka jest dostępna jako potoku przykładowe.
>
>Aby go znaleźć, przejdź do **interfejsu wizualizacji w obszarze roboczym**. W sekcji **nowe potoku** wybierz pozycję **przykład 1-regresja: Automobile — Prognoza cenowa (podstawowa)** .

## <a name="create-a-new-pipeline"></a>Tworzenie nowego potoku

Potoki Azure Machine Learning organizują wiele zależnych kroków przetwarzania danych do pojedynczego zasobu. Potoki ułatwiają organizowanie i ponowne używanie złożonych przepływów pracy uczenia maszynowego między projektami i użytkownikami oraz zarządzanie nimi. Do utworzenia potoku Azure Machine Learning jest wymagany obszar roboczy usługi Azure Machine Learning. W tej sekcji dowiesz się, jak utworzyć oba te zasoby.

### <a name="create-a-new-workspace"></a>Utwórz nowy obszar roboczy

Jeśli masz obszar roboczy Azure Machine Learning, przejdź do następnej sekcji.

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

### <a name="create-a-pipeline"></a>Tworzenie potoku

1. Zaloguj się do [ml.Azure.com](https://ml.azure.com) i wybierz obszar roboczy, z którym chcesz współpracować.

1. Wybierz **interfejs graficzny**.

    ![Zrzut ekranu przedstawiający, jak uzyskać dostęp do interfejsu wizualizacji w obszarze roboczym](./media/ui-tutorial-automobile-price-train-score/launch-visual-interface.png)

1. Wybierz pozycję **pusty potok**.

1. Wybierz domyślną nazwę potoku **"potok-utworzony-on..."** w górnej części kanwy i zmień jej nazwę na bardziej zrozumiałą. Na przykład **"Funkcja prognozowania cen dla samochodów"** . Nazwa nie musi być unikatowa.

## <a name="import-data"></a>Importowanie danych

Istnieje kilka przykładowych zestawów danych zawartych w interfejsie wizualizacji, dzięki którym można eksperymentować z programem. Na potrzeby tego samouczka Użyj przykładowych **danych cen samochodów (RAW)** usługi DataSet. 

1. Na lewo od kanwy potoku jest paletą zestawów danych i modułów. Wybierz pozycję **zestawy danych** , a następnie Wyświetl sekcję **Samples (przykłady** ), aby wyświetlić dostępne przykładowe zestawy danych.

1. Wybierz zestaw danych, **dane cen samochodów (RAW)** i przeciągnij je na kanwę.

   ![Przeciągnij dane do kanwy](./media/ui-tutorial-automobile-price-train-score/drag-data.gif)

1. Wybierz kolumny danych, z którymi chcesz współpracować. Wpisz **Wybierz** w polu wyszukiwania w górnej części palety, aby znaleźć moduł **SELECT Columns in DataSet (Wybieranie kolumn w zestawie danych** ).

1. Kliknij i przeciągnij moduł **Wybieranie kolumn w zestawie danych** na kanwę. Upuść moduł poniżej modułu DataSet.

1. Połącz zestaw danych, który został wcześniej dodany do modułu **SELECT Columns in DataSet (Wybieranie kolumn w zestawie danych** ), klikając i przeciągając. Przeciągnij z portu wyjściowego zestawu danych, czyli małego okręgu w dolnej części zestawu danych na kanwie, do portu wejściowego **SELECT kolumn w zestawie danych**, czyli małego okręgu w górnej części modułu.

    > [!TIP]
    > Przepływ danych można utworzyć za pomocą potoku po podłączeniu portu wyjściowego jednego modułu do portu wejściowego innego.
    >

    ![Połącz moduły](./media/ui-tutorial-automobile-price-train-score/connect-modules.gif)

1. Wybierz pozycję **Wybierz kolumny w zestawie danych** .

1. W okienku **Właściwości** z prawej strony kanwy wybierz pozycję **Edytuj kolumnę**.

    W oknie dialogowym **Wybieranie kolumn** wybierz pozycję **wszystkie kolumny** i Dołącz **wszystkie funkcje**.

1. W prawym dolnym rogu wybierz pozycję **Zapisz** , aby zamknąć selektor kolumny.

### <a name="run-the-pipeline"></a>Uruchamianie potoku

W dowolnym momencie kliknij port wyjściowy zestawu danych lub modułu, aby zobaczyć, jak wyglądają dane w tym momencie w przepływie danych. Jeśli karta dane **wyjściowe** nie zostanie wyświetlona, musisz najpierw uruchomić potok.

[!INCLUDE [aml-ui-create-training-compute](../../../includes/aml-ui-create-training-compute.md)]

### <a name="visualize-the-data"></a>Wizualizacja danych

Możesz wizualizować dane, aby zrozumieć zestaw danych, który będzie używany.

1. Wybierz moduł **dane cen samochodów (RAW)** .

1. W okienku **Właściwości** z prawej strony kanwy wybierz pozycję dane **wyjściowe**.

1. Wybierz ikonę grafu, aby wizualizować dane.

    ![Wizualizacja danych](./media/ui-tutorial-automobile-price-train-score/visualize-data.png)

1. Wybierz różne kolumny w oknie dane, aby wyświetlić informacje o każdej z nich.

    Każdy wiersz reprezentuje samochód, a zmienne skojarzone z poszczególnymi urządzeniami przenośnymi są wyświetlane jako kolumny. Ten zestaw danych zawiera 205 wierszy i 26 kolumn.

## <a name="prepare-data"></a>Przygotowywanie danych

Zestawy danych zwykle wymagają pewnego przetworzenia przed analizą. Podczas wizualizacji zestawu danych mogą znajdować się pewne brakujące wartości. Te brakujące wartości muszą zostać wyczyszczone, aby umożliwić modelowi wykonanie poprawnej analizy danych. Usuniesz wszystkie wiersze, w których brakuje wartości.

1. Wpisz **Wybierz** w polu wyszukiwania w górnej części palety, aby znaleźć moduł **SELECT Columns in DataSet (Wybieranie kolumn w zestawie danych** ).

1. Kliknij i przeciągnij moduł **Wybieranie kolumn w zestawie danych** na kanwę. Upuść moduł poniżej modułu DataSet.

1. Połącz zestaw danych, który został wcześniej dodany do modułu **SELECT Columns in DataSet (Wybieranie kolumn w zestawie danych** ), klikając i przeciągając. Przeciągnij z portu wyjściowego zestawu danych, czyli małego okręgu w dolnej części zestawu danych na kanwie, do portu wejściowego **SELECT kolumn w zestawie danych**, czyli małego okręgu w górnej części modułu.

    ![Połącz moduły](./media/ui-tutorial-automobile-price-train-score/connect-modules.gif)

1. Wybierz pozycję **Wybierz kolumny w zestawie danych** .

1. W okienku **Właściwości** z prawej strony kanwy wybierz pozycję **Edytuj kolumnę**.

    W oknie dialogowym **Wybieranie kolumn** wybierz pozycję **wszystkie kolumny** i Dołącz **wszystkie funkcje**.

1. W prawym dolnym rogu wybierz pozycję **Zapisz** , aby zamknąć selektor kolumny.

> [!TIP]
> Czyszczenie brakujących wartości z danych wejściowych jest wymaganiem wstępnym w przypadku używania większości modułów w interfejsie wizualizacji.

### <a name="remove-column"></a>Usunięcie kolumny

Podczas uczenia modelu trzeba wykonać coś dotyczące brakujących danych. W tym zestawie danych kolumna **znormalizowanych strat** ma dużą liczbę brakujących wartości, dlatego w celu wykluczania tej kolumny z modelu można ją całkowicie wykluczyć.

1. Wybierz pozycję **Wybierz kolumny w zestawie danych** .

1. W okienku **Właściwości** po prawej stronie kanwy wybierz pozycję **Parametry**  > **Edytuj kolumnę**.

1. Wybierz **+** , aby dodać nową regułę.

1. Z menu rozwijanego wybierz opcję **Wyklucz** i **nazwy kolumn**.
    
1. Wprowadź **znormalizowane straty** do pola tekstowego.

1. W prawym dolnym rogu wybierz pozycję **Zapisz** , aby zamknąć selektor kolumny.

    ![Wykluczanie kolumny](./media/ui-tutorial-automobile-price-train-score/exclude-column.png)
        
    Okienko właściwości pokazuje, że kolumna **znormalizowana strata** jest wykluczona.

1. Wybierz pozycję **Wybierz kolumny w zestawie danych** . 

1. We **właściwościach**wybierz pozycję **Parametry**  > **komentarz** i wprowadź "Wykluczanie znormalizowanych strat".

### <a name="clean-missing-data"></a>Wyczyść brakujące dane

Zestaw danych nadal ma brakujące wartości po usunięciu kolumny **znormalizowanych strat** . Pozostałe brakujące dane można usunąć przy użyciu modułu **czyste brakujące dane** .

1. Wpisz **Clean** w polu wyszukiwania, aby znaleźć **nieczysty moduł danych** .

1. Przeciągnij **czysty moduł brakujących danych** do kanwy potoku i połącz go z modułem **Wybierz kolumny w zestawie danych** . 

1. W okienku właściwości wybierz pozycję **Usuń cały wiersz** w obszarze **Tryb czyszczenia**.

1. W okienku właściwości wpisz "Usuń wiersze brakującej wartości". w polu **komentarz** .  

    Potok powinien teraz wyglądać następująco:
    
    ![Zaznacz kolumnę](./media/ui-tutorial-automobile-price-train-score/pipeline-clean.png)

## <a name="train-a-machine-learning-model"></a>Uczenie modelu uczenia maszynowego

Teraz, gdy dane są wstępnie przetworzone, można utworzyć model predykcyjny. Będziesz używać danych do uczenia modelu. Następnie testujesz model, aby sprawdzić, jak ścisłe jest przewidywalność cen.

### <a name="select-an-algorithm"></a>Wybieranie algorytmu

Algorytmy **klasyfikacji** i **regresji** to dwa typy nadzorowanego uczenia maszynowego. **Klasyfikacja** przewiduje odpowiedź ze zdefiniowanego zestawu kategorii, takich jak kolor (czerwony, niebieski lub zielony). **Regresja** służy do przewidywania liczby.

Ponieważ chcesz przewidzieć cenę, która jest liczbą, możesz użyć algorytmu regresji. W tym przykładzie zostanie użyty model regresji liniowej.

### <a name="split-the-data"></a>Podziel dane

Użyj swoich danych w celu przeprowadzenia szkolenia modelu i przetestowania go, dzieląc dane na dwa oddzielne zestawy danych.

1. Wpisz **Split Data** w polu wyszukiwania, aby znaleźć moduł **Split Data** i połączyć go z lewym portem **czystego nieistniejącego modułu danych** .

1. Wybierz moduł **Split Data** .

1. W okienku właściwości ustaw **ułamek wierszy w pierwszym zestawie danych wyjściowych** na 0,7.

    Spowoduje to poddzielenie 70 procent danych, aby szkolić model i obsłużyć 30% do testowania.

1. W okienku właściwości wprowadź "Podziel zestaw danych na zestaw szkoleniowy (0.7) i zestaw testów (0,3)". w polu **komentarz** .

### <a name="train-the-model"></a>Trenowanie modelu

Uczenie modelu przez nadanie mu zestawu danych, który zawiera cenę. Model skanuje dane i wyszukuje korelacje między funkcjami samochodu i jego ceną.

1. Aby wybrać algorytm uczenia, usuń zaznaczenie pola wyszukiwania palety modułu.

1. Rozwiń **algorytmy Machine Learning**.
    
    Zostaną wyświetlone różne kategorie modułów, których można użyć do zainicjowania algorytmów uczenia maszynowego.

1. Dla tego potoku wybierz opcję **regresja**  > **regresji liniowej** i przeciągnij ją na kanwę potoku.

1. Znajdź i przeciągnij moduł **uczenie modelu** na kanwę potoku. 

1. Połącz dane wyjściowe modułu regresji liniowej z lewym wejściem modułu uczenie modelu.

1. Połącz Wyjście danych szkoleniowych (lewy port) modułu **Split Data (podział danych** ) z prawym wejściem modułu **uczenie modelu** .

    ![Zrzut ekranu przedstawiający poprawną konfigurację modułu uczenie modelu. Moduł regresja liniowa łączy się z lewym portem modułu uczenia modelowego, a moduł Split Data łączy się z odpowiednim portem modelu uczenia](./media/ui-tutorial-automobile-price-train-score/pipeline-train-model.png)

1. Wybierz moduł **uczenie modelu** .

1. W okienku właściwości wybierz pozycję **Edytuj selektor kolumny** .

1. W oknie dialogowym **etykieta kolumny** rozwiń menu rozwijane i wybierz pozycję **nazwy kolumn**. W polu tekstowym wprowadź **Price**. Cena to wartość, która jest przewidywana przez model.

    Potok powinien wyglądać następująco:

    ![Zrzut ekranu przedstawiający poprawną konfigurację potoku po dodaniu modułu uczenie modelu.](./media/ui-tutorial-automobile-price-train-score/pipeline-train-graph.png)

## <a name="evaluate-a-machine-learning-model"></a>Oceń model uczenia maszynowego

Teraz, gdy model został przeszkolony przy użyciu 70 procent danych, możesz użyć jej do oceny innych 30 procent danych, aby zobaczyć, jak dobrze działa model.

1. W polu wyszukiwania wpisz ciąg **model oceny** , aby znaleźć moduł **model oceny** i przeciągnąć moduł na kanwę potoku. 

1. Połącz dane wyjściowe modułu **uczenie modelu** z lewym portem wejściowym **modelu wynikowego**. Połącz dane wyjściowe testu (prawy port) modułu **Split Data (dane** wejściowe) z odpowiednim portem wejściowym **modelu wynikowego**.

1. Wpisz **wartość** w polu wyszukiwania, aby znaleźć **model oszacowania** i przeciągnąć moduł na kanwę potoku. 

1. Połącz dane wyjściowe modułu z **modelem wynikowym** z lewym wejściem do **oceny modelu**. 

    Końcowy potok powinien wyglądać następująco:

    ![Zrzut ekranu przedstawiający poprawną konfigurację potoku.](./media/ui-tutorial-automobile-price-train-score/pipeline-final-graph.png)

### <a name="run-the-pipeline"></a>Uruchamianie potoku

[!INCLUDE [aml-ui-create-training-compute](../../../includes/aml-ui-create-training-compute.md)]

### <a name="view-results"></a>Wyświetlanie wyników

Po zakończeniu przebiegu można wyświetlić wyniki uruchomienia potoku. 

1. Aby wyświetlić dane wyjściowe z modułu **wynik model** , wybierz moduł **oceny modelu** .

1. W okienku **Właściwości** wybierz pozycję **wyjściowe**  > **Wizualizacja**. Dane wyjściowe zawierają przewidywane wartości cen oraz znane wartości pochodzące z danych testowych.

    ![Zrzut ekranu przedstawiający wizualizację danych wyjściowych z wyróżnioną kolumną "z oceną"](./media/ui-tutorial-automobile-price-train-score/score-result.png)

1. Aby wyświetlić dane wyjściowe z modułu **oceny modelu** , należy wybrać moduł **model** oceny.

1. W okienku **Właściwości** wybierz pozycję **Wyjocie**  > **Wizualizacja**, a następnie wybierz opcję **Wizualizuj**.

Następujące statystyki są wyświetlane dla modelu:

* **Średni błąd bezwzględny (Mae)** : Średnia liczba błędów bezwzględnych (błąd to różnica między wartością przewidywaną a wartością rzeczywistą).
* **Błąd średnika "pierwiastek" z wartości głównej (RMSE)** : pierwiastek kwadratowy średniej wartości kwadratowych błędów prognoz wykonanych na testowym zestawie danych.
* **Względny błąd absolutny**: iloraz średniej błędów absolutnych i bezwzględnej wartości różnicy między wartościami rzeczywistymi a średnią wszystkich wartości rzeczywistych.
* **Błąd względny średniokwadratowy**: iloraz średniej kwadratów błędów i kwadratu różnicy między wartościami rzeczywistymi a średnią wszystkich wartości rzeczywistych.
* **Współczynnik wyznaczania**: znany również jako wartość R kwadratowa, jest to metryka statystyczna wskazująca, jak dobrze model dopasowuje dane.

W przypadku wszystkich powyższych statystyk mniejsze wartości oznaczają lepszą jakość modelu. Mniejsze wartości błędów wskazują na ściślejsze dopasowanie prognoz do rzeczywistych wartości. Dla współczynnika wyznaczania wartość bliższej wartości to 1 (1,0), tym lepsze przewidywania.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

W pierwszej części tego samouczka zostały wykonane następujące czynności:

* Tworzenie potoku
* Przygotowane dane
* Przeszkolony model
* Ocenianie i Ocena modelu

W części drugiej dowiesz się, jak wdrożyć model jako punkt końcowy potoku.

> [!div class="nextstepaction"]
> [Kontynuuj Wdrażanie modeli](ui-tutorial-automobile-price-deploy.md)
