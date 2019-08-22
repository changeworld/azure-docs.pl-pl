---
title: 'Samouczek: Przewidywanie ceny samochodów za pomocą interfejsu wizualnego'
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak uczenie, ocenę i wdrożenie modelu uczenia maszynowego przy użyciu graficznego interfejsu przeciągania i upuszczania. Ten samouczek jest częścią jednej z serii dwóch części na przewidywanie cen samochodów, przy użyciu regresji liniowej.
author: peterclu
ms.author: peterlu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 08/16/2019
ms.openlocfilehash: a2134853c48ca09faa150f038be2d9327af75eee
ms.sourcegitcommit: a3a40ad60b8ecd8dbaf7f756091a419b1fe3208e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/21/2019
ms.locfileid: "69891626"
---
# <a name="tutorial-predict-automobile-price-with-the-visual-interface"></a>Samouczek: Przewidywanie ceny samochodów za pomocą interfejsu wizualnego

W tym dwuczęściowym samouczku dowiesz się, jak za pomocą interfejsu wizualnego usługi Azure Machine Learning utworzyć i wdrożyć rozwiązanie analityczne do analizy predykcyjnej, które przewiduje cenę dowolnego samochodu. 

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GY]

W części pierwszej należy skonfigurować środowisko, przeciągnąć i upuścić zestawy danych oraz moduły analizy na interaktywną kanwę, a następnie połączyć je ze sobą w celu utworzenia eksperymentu. 

W pierwszej części samouczka dowiesz się, jak:

> [!div class="checklist"]
> * Tworzenie nowego eksperymentu
> * Importowanie danych
> * Przygotowywanie danych
> * Uczenie modelu uczenia maszynowego
> * Oceń model uczenia maszynowego

W [drugiej części](ui-tutorial-automobile-price-deploy.md) tego samouczka dowiesz się, jak wdrożyć model predykcyjny jako usługę sieci Web platformy Azure, aby można było używać go do przewidywania cen wszelkich samochodów w oparciu o specyfikacje techniczne, które wysyłasz. 

Kompletna wersja tego samouczka jest dostępna jako przykładowy eksperyment.

Aby go znaleźć, na **stronie eksperymenty**wybierz pozycję **Dodaj nową**, **a następnie wybierz pozycję przykład 1-regresja: Eksperyment cenowy dla samochodów (Basic** ).

## <a name="create-a-new-experiment"></a>Tworzenie nowego eksperymentu

Aby utworzyć eksperyment interfejsu wizualizacji, należy najpierw potrzebować obszaru roboczego usługi Uczenie maszynowe platformy Azure. W tej sekcji dowiesz się, jak utworzyć oba te zasoby.

### <a name="create-a-new-workspace"></a>Utwórz nowy obszar roboczy

Jeśli masz obszar roboczy usługi Azure Machine Learning, przejdź do następnej sekcji.

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

### <a name="create-an-experiment"></a>Tworzenie eksperymentu

1. Otwórz obszar roboczy w [Azure Portal](https://portal.azure.com/).

1. W obszarze roboczym wybierz opcję **interfejs graficzny**. Następnie wybierz pozycję **Uruchom interfejs graficzny**. 

    ![Zrzut ekranu przedstawiający Azure Portal pokazujący, jak uzyskać dostęp do interfejsu wizualizacji z obszaru roboczego usługi Machine Learning](./media/ui-tutorial-automobile-price-train-score/launch-ui.png)

1. Utwórz nowy eksperyment, wybierając pozycję **+ Nowy** u dołu okna interfejsu wizualizacji.

1. Wybierz pozycję **pusty eksperyment**.

1. Wybierz domyślną nazwę eksperymentu **"eksperyment utworzony w...** " w górnej części kanwy i zmień jej nazwę na zrozumiałą. Na przykład **"Funkcja prognozowania cen dla samochodów"** . Nazwa nie musi być unikatowa.

## <a name="import-data"></a>Importowanie danych

Uczenie maszynowe jest zależne od danych. Na szczęście, istnieje kilka przykładowych zestawów danych zawartych w tym interfejsie, które są dostępne do eksperymentowania z. Na potrzeby tego samouczka Użyj przykładowych **danych cen samochodów (RAW)** usługi DataSet. 

1. Z lewej strony obszaru roboczego eksperymentu znajduje się paleta zawierająca zestawy danych i moduły. Wybierz pozycję **zapisane zestawy danych** , a następnie wybierz pozycję **przykłady** , aby wyświetlić dostępne przykładowe zestawy danych.

1. Wybierz zestaw danych, **dane cen samochodów (RAW)** i przeciągnij je na kanwę.

   ![Przeciągnij dane do kanwy](./media/ui-tutorial-automobile-price-train-score/drag-data.gif)

1. Wybierz kolumny danych, z którymi chcesz współpracować. Wpisz **Wybierz** w polu wyszukiwania w górnej części palety, aby znaleźć moduł **SELECT Columns in DataSet (Wybieranie kolumn w zestawie danych** ).

1. Kliknij i przeciągnij moduł **Wybieranie kolumn w zestawie danych** na kanwę. Upuść moduł poniżej modułu DataSet.

1. Połącz zestaw danych, który został wcześniej dodany do modułu **SELECT Columns in DataSet (Wybieranie kolumn w zestawie danych** ), klikając i przeciągając. Przeciągnij z portu wyjściowego zestawu danych, czyli mały okrąg w dolnej części zestawu danych na kanwie, cały sposób do portu wejściowego **SELECT kolumn w zestawie danych**, czyli mały okrąg w górnej części modułu.

    > [!TIP]
    > Możesz utworzyć przepływ danych za pomocą eksperymentu, gdy połączysz port wyjściowy jednego modułu z portem wejściowym innego.
    >

    ![Połącz moduły](./media/ui-tutorial-automobile-price-train-score/connect-modules.gif)

    Czerwony wykrzyknik wskazuje, że nie ustawiono jeszcze właściwości dla modułu.

1. Wybierz pozycję **Wybierz kolumny w zestawie danych** .

1. W okienku **Właściwości** z prawej strony kanwy wybierz pozycję **Edytuj kolumny**.

    W oknie dialogowym **Wybieranie kolumn** wybierz pozycję **wszystkie kolumny** i Dołącz **wszystkie funkcje**. Okno dialogowe powinno wyglądać następująco:

     ![Selektor kolumn](./media/ui-tutorial-automobile-price-train-score/select-all.gif)

1. W prawym dolnym rogu wybierz przycisk **OK** , aby zamknąć selektora kolumn.

### <a name="run-the-experiment"></a>Uruchamianie eksperymentu

W dowolnym momencie kliknij port wyjściowy zestawu danych lub modułu, aby zobaczyć, jak wyglądają dane w tym momencie w przepływie danych. Jeśli opcja **Wizualizacja** jest wyłączona, należy najpierw uruchomić eksperyment.

[!INCLUDE [aml-ui-create-training-compute](../../../includes/aml-ui-create-training-compute.md)]

Gdy element docelowy obliczeń będzie dostępny, eksperyment zostanie uruchomiony. Po zakończeniu przebiegu jest wyświetlany zielony znacznik wyboru dla każdego modułu.


### <a name="visualize-the-data"></a>Wizualizacja danych

Po uruchomieniu początkowego eksperymentu możesz wizualizować dane, aby dowiedzieć się więcej na temat zestawu danych, który posiadasz.

1. Wybierz port wyjściowy w dolnej części okna **Wybieranie kolumn w zestawie danych** , a następnie wybierz opcję **Wizualizuj**.

1. Kliknij różne kolumny w oknie dane, aby wyświetlić informacje o tej kolumnie.

    W tym zestawie danych poszczególne wiersze reprezentują samochody, a zmienne skojarzone z samochodami są wyświetlane jako kolumny. Ten zestaw danych zawiera 205 wierszy i 26 kolumn.

    Za każdym razem, gdy klikniesz kolumnę danych, informacje statystyczne i obraz **wizualizacji** tej kolumny pojawiają się po lewej stronie.

    [![Podgląd danych](./media/ui-tutorial-automobile-price-train-score/preview-data.gif)](./media/ui-tutorial-automobile-price-train-score/preview-data.gif#lightbox)

1. Kliknij każdą kolumnę, aby dowiedzieć się więcej o zestawie danych, i pomyśl o tym, czy te kolumny będą przydatne do przewidywania cen samochodów.

## <a name="prepare-data"></a>Przygotowywanie danych

Zazwyczaj zestaw danych wymaga przetworzenia wstępnego, zanim będzie można go przeanalizować. Podczas wizualizacji zestawu danych mogą znajdować się pewne brakujące wartości. Te brakujące wartości muszą zostać wyczyszczone, aby umożliwić modelowi wykonanie poprawnej analizy danych. Usuniesz wszystkie wiersze, w których brakuje wartości. Ponadto kolumna **znormalizowanych strat** ma dużą część brakujących wartości, dlatego ta kolumna jest wykluczana z modelu całkowicie.

> [!TIP]
> Większość modułów wymaga wyczyszczenia brakujących wartości w danych wejściowych.

### <a name="remove-column"></a>Usunięcie kolumny

Najpierw Usuń kolumnę **znormalizowanych strat** .

1. Wybierz pozycję **Wybierz kolumny w zestawie danych** .

1. W okienku **Właściwości** z prawej strony kanwy wybierz pozycję **Edytuj kolumny**.

    * Pozostaw **reguły** i **wszystkie wybrane kolumny** .

    * Z list rozwijanych wybierz pozycje **Exclude** (Wyklucz) i **column names** (nazwy kolumn), a następnie kliknij wewnątrz pola tekstowego. Typ **znormalizowany — straty**.

    * W prawym dolnym rogu wybierz przycisk **OK** , aby zamknąć selektora kolumn.

    ![Wykluczanie kolumny](./media/ui-tutorial-automobile-price-train-score/exclude-column.gif)
        
    Teraz okienko właściwości dla opcji Wybieranie kolumn w zestawie danych wskazuje, że zostanie przekazane przez wszystkie kolumny z zestawu danych z wyjątkiem **znormalizowanych strat**.
        
    Okienko właściwości pokazuje, że kolumna **znormalizowana strata** jest wykluczona.

1. Kliknij dwukrotnie moduł **Wybieranie kolumn w zestawie danych** i wpisz komentarz "Wykluczanie znormalizowanych strat". 
    
    Po wpisaniu komentarza kliknij poza modułem. Zostanie wyświetlona strzałka w dół, aby pokazać, że moduł zawiera komentarz.

1. Kliknij strzałkę w dół, aby wyświetlić komentarz.

    W module jest teraz widoczna Strzałka w górę umożliwiająca ukrycie komentarza.
        
    ![Komentarze](./media/ui-tutorial-automobile-price-train-score/comments.png)

### <a name="clean-missing-data"></a>Wyczyść brakujące dane

Podczas uczenia modelu trzeba wykonać coś dotyczące brakujących danych. W takim przypadku dodasz moduł, aby usunąć wszystkie pozostałe wiersze, które mają brakujące dane.

1. Wpisz **Clean** w polu wyszukiwania, aby znaleźć nieczysty moduł **danych** .

1. Przeciągnij **czysty moduł brakujących danych** do kanwy eksperymentu i połącz go z modułem **SELECT Columns in DataSet (Wybieranie kolumn w zestawie danych** ). 

1. W okienku właściwości wybierz pozycję **Usuń cały wiersz** w obszarze **Tryb czyszczenia**.

1. Kliknij dwukrotnie moduł i wpisz komentarz „Usunięcie wierszy z brakującymi wartościami”.

    Eksperyment powinien teraz wyglądać następująco:
    
    ![Zaznacz kolumnę](./media/ui-tutorial-automobile-price-train-score/experiment-clean.png)

## <a name="train-a-machine-learning-model"></a>Uczenie modelu uczenia maszynowego

Teraz, gdy dane są gotowe, można utworzyć model predykcyjny. Będziesz używać danych do uczenia modelu. Następnie testujesz model, aby sprawdzić, jak ścisłe jest przewidywalność cen.

### <a name="select-an-algorithm"></a>Wybieranie algorytmu

Algorytmy **klasyfikacji** i **regresji** to dwa typy nadzorowanego uczenia maszynowego. **Klasyfikacja** przewiduje odpowiedź ze zdefiniowanego zestawu kategorii, takich jak kolor (czerwony, niebieski lub zielony). **Regresja** służy do przewidywania liczby.

Ponieważ chcesz przewidzieć cenę, która jest liczbą, możesz użyć algorytmu regresji. W tym przykładzie zostanie użyty model regresji liniowej.

### <a name="split-the-data"></a>Podziel dane

Użyj swoich danych w celu przeprowadzenia szkolenia modelu i przetestowania go, dzieląc dane na oddzielne szkolenie i testowanie zestawów danych.

1. Wpisz **Split Data** w polu wyszukiwania, aby znaleźć moduł **Split Data** i połączyć go z lewym portem **czystego nieistniejącego modułu danych** .

1. Wybierz moduł **Split Data** . W okienku właściwości ustaw ułamek wierszy w pierwszym zestawie danych wyjściowych na 0,7. W ten sposób będziemy używać 70 procent danych do uczenia modelu i zatrzymamy 30% do testowania.

1. Kliknij dwukrotnie pozycję **dane podzielone** i wpisz komentarz "Podziel zestaw danych na zestaw szkoleniowy (0.7) i zestaw testów (0,3)"

### <a name="train-the-model"></a>Uczenie modelu

Uczenie modelu przez nadanie mu zestawu danych, który zawiera cenę. Model skanuje dane i wyszukuje korelacje między funkcjami samochodu i jego ceną.

1. Aby wybrać algorytm uczenia, usuń zaznaczenie pola wyszukiwania palety modułu.

1. Rozwiń **Machine Learning** a następnie rozwiń węzeł **zainicjuj model**. Zostaną wyświetlone różne kategorie modułów, których można użyć do zainicjowania algorytmów uczenia maszynowego.

1. Na potrzeby tego eksperymentu > wybierz**regresja liniowa** regresji i przeciągnij ją do kanwy eksperymentu.

1. Znajdź i przeciągnij moduł **uczenie modelu** do kanwy eksperymentu. Połącz dane wyjściowe modułu regresji liniowej z lewym wejściem modułu uczenie modelu i Połącz dane wyjściowe szkoleń (lewy port) modułu **Split Data (podział danych** ) z prawym wejściem modułu **szkolenia model** .

    ![Zrzut ekranu przedstawiający poprawną konfigurację modułu uczenie modelu. Moduł regresja liniowa łączy się z lewym portem modułu uczenia modelowego, a moduł Split Data łączy się z odpowiednim portem modelu uczenia](./media/ui-tutorial-automobile-price-train-score/train-model.png)

1. Wybierz moduł **uczenie modelu** . W okienku właściwości wybierz pozycję Uruchom selektor kolumny, a następnie wpisz **Cena** obok pozycji **Uwzględnij nazwy kolumn**. Cena to wartość, która jest przewidywana przez model

    ![Zrzut ekranu przedstawiający poprawną konfigurację modułu selektora kolumn. Z regułami > Uwzględnij nazwy kolumn > "price"](./media/ui-tutorial-automobile-price-train-score/select-price.png)

    Eksperyment powinien wyglądać następująco:

    ![Zrzut ekranu przedstawiający poprawną konfigurację eksperymentu po dodaniu modułu uczenie modelu.](./media/ui-tutorial-automobile-price-train-score/train-graph.png)

## <a name="evaluate-a-machine-learning-model"></a>Oceń model uczenia maszynowego

Teraz, gdy model został przeszkolony przy użyciu 70 procent danych, możesz użyć jej do oceny innych 30 procent danych, aby zobaczyć, jak dobrze działa model.

1. W polu wyszukiwania wpisz ciąg **model oceny** , aby znaleźć moduł **model oceny** i przeciągnij moduł do kanwy eksperymentu. Połącz dane wyjściowe modułu **uczenie modelu** z lewym portem wejściowym **modelu wynikowego**. Połącz dane wyjściowe testu (prawy port) modułu **Split Data (dane** wejściowe) z odpowiednim portem wejściowym **modelu wynikowego**.

1. Wpisz **wartość** w polu wyszukiwania, aby znaleźć **model oszacowania** i przeciągnąć moduł na kanwę eksperymentu. Połącz dane wyjściowe modułu z **modelem wynikowym** z lewym wejściem do **oceny modelu**. Końcowy eksperyment powinien wyglądać następująco:

    ![Zrzut ekranu przedstawiający ostateczną poprawną konfigurację eksperymentu.](./media/ui-tutorial-automobile-price-train-score/final-graph.png)

1. Uruchom eksperyment przy użyciu utworzonego wcześniej zasobu obliczeniowego.

1. Aby wyświetlić dane wyjściowe z modułu **wyniku modelu oceny** , należy wybrać port wyjściowy **modelu oceny** i kliknąć opcję **Wizualizuj**. Dane wyjściowe zawierają przewidywane wartości cen oraz znane wartości pochodzące z danych testowych.

    ![Zrzut ekranu przedstawiający wizualizację danych wyjściowych z wyróżnioną kolumną "z oceną"](./media/ui-tutorial-automobile-price-train-score/score-result.png)

1. Aby wyświetlić dane wyjściowe z modułu **Oceń model** , wybierz port wyjściowy, a następnie wybierz opcję **Wizualizuj**.

    ![Zrzut ekranu przedstawiający wyniki oceny dla eksperymentu końcowego.](./media/ui-tutorial-automobile-price-train-score/evaluate-result.png)

Następujące statystyki są wyświetlane dla modelu:

* **Średni błąd bezwzględny (Mae)** : Średnią z błędów bezwzględnych (błąd jest różnica między wartością przewidywaną a wartością rzeczywistą).
* **Błąd oznaczający**, że pierwiastek: pierwiastek kwadratowy ze średniej kwadratów błędów prognoz dla zestawu danych testowych.
* **Względny błąd absolutny**: iloraz średniej błędów absolutnych i bezwzględnej wartości różnicy między wartościami rzeczywistymi a średnią wszystkich wartości rzeczywistych.
* **Błąd względny średniokwadratowy**: iloraz średniej kwadratów błędów i kwadratu różnicy między wartościami rzeczywistymi a średnią wszystkich wartości rzeczywistych.
* **Współczynnik determinacji**: Nazywana również wartością R kwadratową jest metryką statystyczną wskazującą, jak dobrze model dopasowuje dane.

W przypadku wszystkich powyższych statystyk mniejsze wartości oznaczają lepszą jakość modelu. Mniejsze wartości błędów wskazują na ściślejsze dopasowanie prognoz do rzeczywistych wartości. Dla współczynnika wyznaczania wartość bliższej wartości to 1 (1,0), tym lepsze przewidywania.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

W pierwszej części tego samouczka zostały wykonane następujące czynności:

* Utworzono eksperyment
* Przygotowywanie danych
* Uczenie modelu
* Ocena i Ocena modelu

W części drugiej dowiesz się, jak wdrożyć model jako usługę sieci Web platformy Azure.

> [!div class="nextstepaction"]
> [Kontynuuj Wdrażanie modeli](ui-tutorial-automobile-price-deploy.md)
