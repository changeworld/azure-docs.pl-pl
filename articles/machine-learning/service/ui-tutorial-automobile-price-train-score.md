---
title: 'Samouczek: Prognozowanie cen samochodów za pomocą interfejsu wizualnego'
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak uczenie, ocenę i wdrożyć model uczenia maszynowego przy użyciu przeciągania i upuszczania interfejsu wizualnego. Niniejszy samouczek jest pierwszą częścią serii legalną dwuczęściową na Prognozowanie cen samochodów przy użyciu regresji liniowej.
author: peterclu
ms.author: peterclu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 04/06/2019
ms.openlocfilehash: 21f5a2d93b708e93f124bd44177bb7852dfbd86a
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2019
ms.locfileid: "67720545"
---
# <a name="tutorial-predict-automobile-price-with-the-visual-interface"></a>Samouczek: Prognozowanie cen samochodów za pomocą interfejsu wizualnego

W tym samouczku możesz Przyjrzyj się rozszerzone tworzenie rozwiązania do analizy predykcyjnej w interfejsie visual usługi Azure Machine Learning. Do końca tego samouczka będziesz mieć rozwiązania, który potrafi prognozować cenę dowolnej samochód, oparte na specyfikacji technicznej, który zostanie wysłany.

W części pierwszej części samouczka dowiesz się, jak:

> [!div class="checklist"]
> * Importowanie i wyczyść dane
> * Szkolenie modelu uczenia maszynowego
> * Generowanie wyników i oceny modelu

W [część druga](ui-tutorial-automobile-price-deploy.md) tego samouczka dowiesz się, jak wdrożyć model predykcyjny jako usługę sieci web platformy Azure.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GY]

Pełną wersję tego samouczka jest dostępna jako przykładowego eksperymentu.

Aby ją znaleźć, z **strony eksperymentów**, wybierz opcję **Dodaj nowe**, a następnie wybierz **przykładowe 1 - regresji: Samochodów Prediction(Basic) Cena** eksperymentować.

## <a name="create-a-workspace"></a>Tworzenie obszaru roboczego

Jeśli masz obszar roboczy usługi Azure Machine Learning, przejdź do [następnej sekcji](#open-the-visual-interface-webpage).

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="open-the-visual-interface-webpage"></a>Otwórz interfejs graficzny strony sieci Web

1. Otwieranie obszaru roboczego w [witryny Azure portal](https://portal.azure.com/).

1. W obszarze roboczym, wybierz **interfejs graficzny**. Następnie wybierz pozycję **interfejs graficzny uruchamiania**. 

    ![Zrzut ekranu witryny Azure portal pokazujący sposób dostępu interfejs graficzny z obszaru roboczego usługi Machine Learning](./media/ui-tutorial-automobile-price-train-score/launch-ui.png)

## <a name="create-your-first-experiment"></a>Tworzenie pierwszego eksperymentu

Narzędzie interfejs graficzny miejsce interaktywny, wizualny do tworzenia modeli do analizy predykcyjnej. Przeciągnij i upuść zestawy danych i analiza moduły analityczne na interaktywną kanwy i łączyć je ze sobą, aby utworzyć *eksperymentować*.

1. Tworzenie nowego eksperymentu, wybierając **+ nowy** w dolnej części okna interfejsu wizualnego.

    ![Dodaj nowy eksperyment](./media/ui-tutorial-automobile-price-train-score/add-new.png)

1. Wybierz **Blank Experiment**.

1. Wybierz domyślną nazwę eksperymentu **"badawcze, mające utworzone na...** "w górnej części obszaru roboczego i zmień jego nazwę na bardziej opisową nazwę. Na przykład **Prognozowanie cen samochodów**. Nazwa nie musi być unikatowa.

## <a name="add-data"></a>Dodawanie danych

Pierwszą rzeczą, jaką należy dla usługi machine learning są dane. Istnieje kilka przykładowych zestawów danych w tym interfejsie, którego można używać. Można również importować dane z istniejących źródeł. W tym samouczku Użyj przykładowego zestawu danych **danych dotyczących cen samochodów (Raw)** . 

1. Z lewej strony obszaru roboczego eksperymentu znajduje się paleta zawierająca zestawy danych i moduły. Wybierz **zapisane zestawów danych** polecenie **przykłady** do wyświetlania dostępnych przykładowych zestawów danych.

1. Wybierz zestaw danych, **(nieprzetworzone) dane cen samochodów**i przeciągnij go na kanwie.

   ![Przeciągnij danych do kanwy](./media/ui-tutorial-automobile-price-train-score/drag-data.png)

## <a name="select-columns"></a>Wybieranie kolumn

Wybierz kolumny, które dane chcesz pracować. Do konfigurowania modułu, aby wyświetlić wszystkie dostępne kolumny.

> [!TIP]
> Jeśli znasz nazwę danych lub moduł, który ma umożliwia pasek wyszukiwania w górnej części palety szybkie znajdowanie. Pozostała część tego samouczka będzie używać tego skrótu.


1. Typ **wybierz** w polu wyszukiwania, aby znaleźć **Select Columns in Dataset** modułu.

1. Kliknij i przeciągnij **Select Columns in Dataset** na kanwę. Usuń moduł poniżej zestawu danych, który dodano wcześniej.

1. Łączenie zestawu danych na **Select Columns in Dataset**: kliknij port wyjściowy zestawu danych, przeciągnij portem wejściowym **Select Columns in Dataset**, następnie zwolnij przycisk myszy. Zestaw danych i moduł pozostaną połączone, nawet jeśli będziesz przenosić je w obrębie kanwy.

    > [!TIP]
    > Zestawy danych i moduły mają porty wejściowe i wyjściowe oznaczone małymi kołami — porty wejściowe znajdują się u góry, a wyjściowe u dołu. Utworzysz przepływ danych w eksperymencie, po nawiązaniu połączenia z portem wejściowym innego port wyjściowy jeden moduł.
    >

    ![Łączenie modułów](./media/ui-tutorial-automobile-price-train-score/connect-modules.gif)

    Czerwony wykrzyknik wskazuje, że właściwości modułu nie zostały ustawione jeszcze.

1. Wybierz **Select Columns in Dataset** modułu.

1. W **właściwości** w okienku po prawej stronie obszaru roboczego wybierz **Upravit sloupce**.

    W **wybierz kolumny** okno dialogowe, wybierz **wszystkie kolumny** i obejmują **wszystkie funkcje**. Okno dialogowe powinno wyglądać następująco:

     ![Selektor kolumn](./media/ui-tutorial-automobile-price-train-score/select-all.png)

1. W prawym dolnym rogu wybierz **OK** aby zamknąć selektora kolumn.

## <a name="run-the-experiment"></a>Uruchamianie eksperymentu

W dowolnym momencie kliknij port wyjściowy zestawu danych lub modułu, aby wyświetlić dane wygląda podobnie jak w tym momencie przepływu danych. Jeśli **Visualize** opcja jest wyłączona, należy najpierw uruchomić eksperyment.

Eksperyment działa na cel obliczenia, czyli zasób obliczeniowy, który jest dołączony do swojego obszaru roboczego. Po utworzeniu obliczeniowego elementu docelowego, możesz użyć go ponownie dla przyszłych przebiegów.

[!INCLUDE [aml-ui-create-training-compute](../../../includes/aml-ui-create-training-compute.md)]

Po udostępnieniu obliczeniowego elementu docelowego jest uruchamiany eksperymentu. Po zakończeniu przebiegu na każdy moduł pojawi się zielony znacznik wyboru.


## <a name="preview-the-data"></a>Wyświetl podgląd danych

Teraz, gdy uruchomiono początkowej eksperymentu, możesz wizualizować dane, aby dowiedzieć się więcej na temat zestawu danych, które trzeba pracować z.

1. Wybierz port wyjściowy w dolnej części **Select Columns in Dataset** polecenie **Visualize**.

1. Polecenie różnych kolumn w oknie dane, aby wyświetlić informacje o tej kolumny.

    W tym zestawie danych poszczególne wiersze reprezentują samochody, a zmienne skojarzone z samochodami są wyświetlane jako kolumny. Brak 205 wierszy i kolumn 26, w tym zestawie danych.

     Każde kliknięcie kolumny danych, **statystyki** informacji i **wizualizacji** po lewej stronie jest wyświetlany obraz tej kolumny. Na przykład po kliknięciu **num z drzwi** zobaczysz w nim 2 unikatowe wartości i 2 brakujące wartości. Przewiń w dół, aby zobaczyć wartości: drzwi dwóch do czterech.

     ![Wyświetl podgląd danych](./media/ui-tutorial-automobile-price-train-score/preview-data.gif)

1. Kliknij przycisk dotyczących poszczególnych kolumn, aby dowiedzieć się więcej o zestaw danych i zastanów się, czy te kolumny będą przydatne do prognozowania cen samochodów.

## <a name="prepare-data"></a>Przygotowywanie danych

Zestawy danych zwykle wymagają przetworzenia wstępnego przed rozpoczęciem analizy. Być może Zauważyłeś, niektóre brakujące wartości podczas wizualizacji zestawu danych. Te brakujące wartości muszą zostać wyczyszczone, aby umożliwić modelowi wykonanie poprawnej analizy danych. Należy usunąć wszystkie wiersze z brakującymi wartościami. Ponadto **kolumny znormalizowane straty** kolumna ma dużą część brakujące wartości, dzięki czemu będzie wyłączyć tę kolumnę z modelu całkowicie.

> [!TIP]
> Większość modułów wymaga wyczyszczenia brakujących wartości w danych wejściowych.

### <a name="remove-column"></a>Usunięcie kolumny

Najpierw należy usunąć **kolumny znormalizowane straty** kolumny całkowicie.

1. Wybierz **Select Columns in Dataset** modułu.

1. W **właściwości** w okienku po prawej stronie obszaru roboczego wybierz **Upravit sloupce**.

    * Pozostaw **przy użyciu reguł** i **wszystkie kolumny** wybrane.

    * Z list rozwijanych wybierz pozycje **Exclude** (Wyklucz) i **column names** (nazwy kolumn), a następnie kliknij wewnątrz pola tekstowego. Typ **kolumny znormalizowane straty**.

    * W prawym dolnym rogu wybierz **OK** aby zamknąć selektora kolumn.

    ![Wyklucz kolumny](./media/ui-tutorial-automobile-price-train-score/exclude-column.png)
        
    Teraz zawartość okienka właściwości Select Columns in Dataset wskazuje, że zostaną przetworzone wszystkie kolumny z zestawu danych, z wyjątkiem **kolumny znormalizowane straty**.
        
    Zawartość okienka właściwości wskazuje, że **kolumny znormalizowane straty** wykluczenie kolumny.
        
    ![Okienko właściwości](./media/ui-tutorial-automobile-price-train-score/property-pane.png)
        
    Aby dodać komentarz do modułu, kliknij dwukrotnie moduł i wpisz tekst. Pozwoli to od razu sprawdzić rolę modułu w eksperymencie. 

1. Kliknij dwukrotnie **Select Columns in Dataset** moduł i wpisz komentarz "wykluczenie kolumny znormalizowane straty". 
    
    Po użytkownik wpisze komentarz, kliknij na zewnątrz modułu. Aby pokazać, że moduł zawiera komentarz pojawi się kształt strzałki w dół.

1. Kliknij strzałkę w dół, aby wyświetlić komentarz.

    Moduł zawiera obecnie strzałkę w górę, aby ukryć komentarz.
        
    ![Komentarze](./media/ui-tutorial-automobile-price-train-score/comments.png)

### <a name="clean-missing-data"></a>Wyczyść brakujące dane

Ucząc model trzeba zrobić coś o danych, które nie istnieje. W takim przypadku należy dodać moduł, który chcesz usunąć wszystkie pozostałe wiersze z brakującymi danymi.

1. Typ **czysty** w polu wyszukiwania, aby znaleźć **Clean Missing Data** modułu.

1. Przeciągnij **Clean Missing Data** modułów na eksperyment kanwy i połącz go **Select Columns in Dataset** modułu. 

1. W okienku właściwości wybierz **Usuń cały wiersz** w obszarze **tryb czyszczenia**.

    Te opcje bezpośredniego **Clean Missing Data** aby wyczyścić dane przez usunięcie wierszy, które nie mają żadnych wartości.

1. Kliknij dwukrotnie moduł i wpisz komentarz „Usunięcie wierszy z brakującymi wartościami”.
 
    ![Usuń wiersze](./media/ui-tutorial-automobile-price-train-score/remove-rows.png)

    Eksperyment powinien teraz wyglądać mniej więcej tak:
    
    ![Wybierz kolumny](./media/ui-tutorial-automobile-price-train-score/experiment-clean.png)

## <a name="visualize-the-results"></a>Wizualizacja wyników

Od wprowadzenia zmian do modułów w eksperymencie, stan został zmieniony na "Robocza w".  Do wizualizacji nowych danych czystego, należy najpierw uruchomić eksperyment ponownie.

1. Wybierz **Uruchom** u dołu, aby uruchomić eksperyment.

    Teraz możesz ponownie użyć docelowej obliczeń, utworzonego wcześniej.

1. Wybierz **Uruchom** w oknie dialogowym.

   ![Uruchamianie eksperymentu](./media/ui-tutorial-automobile-price-train-score/select-compute.png)

1. Po zakończeniu przebiegu, kliknij prawym przyciskiem myszy **Clean Missing Data** modułu do wizualizacji nowych danych czyste.

    ![Wizualizuj Wyczyść dane](./media/ui-tutorial-automobile-price-train-score/visualize-cleaned.png)

1. Polecenie różnych kolumn w oknie operacji czyszczenia danych, aby zobaczyć, jak dane zostały zmienione.

    ![Wizualizuj Wyczyść dane](media/ui-tutorial-automobile-price-train-score/visualize-result.png)

    Istnieją teraz 193 wierszy i kolumn 25.

    Po kliknięciu **num z drzwi** Zobacz środki, nieopłacone 2 unikatowe wartości, ale ma teraz 0 brakuje wartości. Klikaj elementy pozostałe kolumny, aby zobaczyć, że nie istnieją żadne brakujące wartości, które pozostanie w zestawie danych. 

## <a name="train-the-model"></a>Uczenie modelu

Teraz, że dane są gotowe, możesz utworzyć model predykcyjny. Użyjemy danych do nauczenia modelu. Następnie krokiem jest przetestowanie modelu, aby zobaczyć, jak blisko jest w stanie do prognozowania cen.

Algorytmy **klasyfikacji** i **regresji** to dwa typy nadzorowanego uczenia maszynowego. **Klasyfikacja** przewiduje odpowiedź na podstawie zdefiniowanego zestawu kategorii, takich jak kolory (czerwony, niebieski lub zielony). **Regresja** służy do prognozowania liczby.

Ponieważ chcemy przewidzieć cenę, która jest liczbą, używając algorytmu regresji. W tym przykładzie użyjemy modelu regresji liniowej.

Uczenie modelu polega na przekazaniu mu zestawu danych zawierających cenę. Model skanuje dane i szuka korelacji między funkcjami samochodu a jego ceną. Następnie Testowanie modelu polega na przekazaniu mu zestawu funkcji dla samochodów, którego znasz i zobacz, jak blisko jest dostarczany modelu prognozowania cen.

Korzystanie z Twoich danych, zarówno dla uczenia modelu i testowania, dzieląc dane na oddzielnych szkolenia i testowania zestawów danych.

1. Typ **podział danych** w polu wyszukiwania, aby znaleźć **podziału danych** modułu i połączyć ją z lewym portem **Clean Missing Data** modułu.

1. Wybierz **podziału danych** modułu którym nawiązano połączenie go zaznaczyć. W okienku właściwości ustaw ułamek wierszy w pierwszym zestawie danych wyjściowych 0,7. W ten sposób firma Microsoft będzie Użyj 70 procent danych do nauczenia modelu, a pozostałe 30% do testów.

    ![Zrzut ekranu przedstawiający prawidłowej konfiguracji w okienku właściwości. Wartości z "Split Data" powinien być "Podziału wierszy" 0,7, wybierane podzielić, 0-FAŁSZ.](./media/ui-tutorial-automobile-price-train-score/split-data.png)

1. Kliknij dwukrotnie **podziału danych** i wpisz komentarz "Podzielić zestawu danych szkoleniowych set(0.3) set(0.7) tworzenie i testowanie"

1. Aby wybrać algorytm uczenia, wyczyść pole wyszukiwania palety modułu.

1. Rozwiń **uczenia maszynowego** następnie rozwiń **zainicjować modelu**. Zostaną wyświetlone różne kategorie modułów, których można użyć do zainicjowania algorytmów uczenia maszynowego.

1. W tym eksperymencie wybierz **regresji** > **regresji liniowej** i przeciągnij go do obszaru roboczego eksperymentu.

    ![Zrzut ekranu przedstawiający prawidłowej konfiguracji w okienku właściwości. Wartości z "Split Data" powinien być "Podziału wierszy" 0,7, wybierane podzielić, 0-FAŁSZ.](./media/ui-tutorial-automobile-price-train-score/linear-regression-module.png)

1. Znajdowanie i przeciąganie **Train Model** modułu do obszaru roboczego eksperymentu. Połącz wyjście modułu regresji liniowej lewym wejściem modułu Train Model i Połącz wyjście danych szkoleniowych (lewy port) z **podziału danych** modułu prawym wejściem **Train Model**modułu.

    ![Zrzut ekranu przedstawiający prawidłowej konfiguracji modułu Train Model. Moduł regresji liniowej nawiązanie połączenia z lewym portem Train Model modułu i modułu dzielenia danych łączy się prawy port z Train Model](./media/ui-tutorial-automobile-price-train-score/train-model.png)

1. Wybierz **Train Model** modułu. W okienku właściwości wybierz uruchamiania selektora kolumn, a następnie wpisz **cena** obok **zawierają nazwy kolumn**. Cena jest wartość, która będzie prognozowana modelu

    ![Zrzut ekranu przedstawiający prawidłowej konfiguracji modułu selektora kolumn. Za pomocą reguł > zawierają nazwy kolumn > "price"](./media/ui-tutorial-automobile-price-train-score/select-price.png)

    Eksperyment powinien wyglądać następująco:

    ![Zrzut ekranu przedstawiający prawidłowej konfiguracji eksperymentu po dodaniu modułu Train Model.](./media/ui-tutorial-automobile-price-train-score/train-graph.png)

### <a name="run-the-training-experiment"></a>Uruchomienie eksperymentu szkolenia

[!INCLUDE [aml-ui-create-training-compute](../../../includes/aml-ui-create-training-compute.md)]

## <a name="score-and-evaluate-the-model"></a>Generowanie wyników i oceny modelu

Skoro już uczony model przy użyciu danych do 70 procent, służy do oceniania inne 30 procent danych, aby zobaczyć, jak dobrze funkcji modelu.

1. Typ **score model** w polu wyszukiwania, aby znaleźć **Score Model** moduł i przeciągnij moduł do obszaru roboczego eksperymentu. Połącz wyjście **Train Model** portem wejściowym modułu po lewej stronie **Score Model**. Połącz wyjście danych testowych (prawy port) z **podziału danych** portem wejściowym modułu po prawej stronie **Score Model**.

1. Typ **oceny** w polu wyszukiwania, aby znaleźć **Evaluate Model** i przeciągnij moduł do obszaru roboczego eksperymentu. Połącz wyjście **Score Model** wejściem modułu po lewej stronie **Evaluate Model**. Końcowy eksperyment powinien wyglądać następująco:

    ![Zrzut ekranu przedstawiający końcowego prawidłowej konfiguracji eksperymentu.](./media/ui-tutorial-automobile-price-train-score/final-graph.png)

1. Uruchom eksperyment, przy użyciu tej samej wartości docelowej obliczeń użyte wcześniej.

1. Wyświetl dane wyjściowe z **Score Model** modułu, wybierając port wyjściowy **Score Model** i wybierz **Visualize**. Dane wyjściowe zawierają przewidywane wartości cen oraz znane wartości pochodzące z danych testowych.

    ![Zrzut ekranu przedstawiający wizualizacji danych wyjściowych, wyróżnianie kolumny "Oceniane Label"](./media/ui-tutorial-automobile-price-train-score/score-result.png)

1. Aby wyświetlić dane wyjściowe z modułu Evaluate Model, wybierz port wyjściowy, a następnie wybierz wizualizacja.

    ![Zrzut ekranu przedstawiający wyniki oceny eksperymentu końcowej.](./media/ui-tutorial-automobile-price-train-score/evaluate-result.png)

Dla modelu, wyświetlane są następujące statystyki:

* **Średni bezwzględny błąd (dostosowania)** : Średnia bezwzględnych błędów (błąd występuje różnica między wartością prognozowaną a wartością rzeczywistą).
* **Średnia głównego kwadrat błąd (RMSE)** : pierwiastek kwadratowy ze średniej kwadratów błędów prognoz dla zestawu danych testowych.
* **Względny błąd absolutny**: iloraz średniej błędów absolutnych i bezwzględnej wartości różnicy między wartościami rzeczywistymi a średnią wszystkich wartości rzeczywistych.
* **Błąd względny średniokwadratowy**: iloraz średniej kwadratów błędów i kwadratu różnicy między wartościami rzeczywistymi a średnią wszystkich wartości rzeczywistych.
* **Współczynnik determinacji**: Znany także jako wartość R-kwadrat, to jest miarą statystyczną wskazujący, jak dobrze pasuje do modelu danych.

W przypadku wszystkich powyższych statystyk mniejsze wartości oznaczają lepszą jakość modelu. Mniejsze wartości błędów wskazują na ściślejsze dopasowanie prognoz do rzeczywistych wartości. Współczynnik oznaczania, im jego wartość jest bliższa jedności (1,0), prognozy są tym lepsze.

## <a name="manage-experiments-in-azure-machine-learning-service-workspace"></a>Zarządzanie eksperymenty w obszarze roboczym usługi Azure Machine Learning

Eksperymenty, utworzone w interfejs graficzny można zarządzać z obszaru roboczego usługi Azure Machine Learning. Aby wyświetlić bardziej szczegółowe informacje, takie jak doświadczenia użytkowników indywidualnych systemem dzienniki diagnostyczne, wykonanie wykresy i inne, należy użyć obszaru roboczego.

1. Otwieranie obszaru roboczego w [witryny Azure portal](https://portal.azure.com/).  

1. W obszarze roboczym, wybierz **eksperymentów**. Następnie wybierz eksperyment, który został utworzony.

    ![Zrzut ekranu pokazujący sposób przejścia do eksperymentów w witrynie Azure portal](./media/ui-tutorial-automobile-price-train-score/portal-experiments.png)

    Na tej stronie zostanie wyświetlony przegląd eksperymentu i ich najnowszych przebiegów.

    ![Zrzut ekranu przedstawiający Przegląd statystyk eksperymentu w witrynie Azure portal](./media/ui-tutorial-automobile-price-train-score/experiment-overview.png)

1. Wybierz przebieg, aby zobaczyć więcej szczegółów na temat określonego wykonania.

    ![Zrzut ekranu szczegółowe Uruchom raport](./media/ui-tutorial-automobile-price-train-score/run-details.png)

    Uruchom raport jest aktualizowany w czasie rzeczywistym. Jeśli użyto **wykonanie skryptu Python** modułu w eksperymencie można określić skrypt dzienniki, aby dane wyjściowe w **dzienniki** kartę.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

W części pierwszej części tego samouczka można wykonać następujące czynności:

* Utworzyć eksperyment
* Przygotowywanie danych
* Uczenie modelu
* Generowanie wyników i oceny modelu

Część druga dowiesz się, jak wdrożyć model jako usługę sieci web platformy Azure.

> [!div class="nextstepaction"]
> [Wdrażanie modeli w dalszym ciągu](ui-tutorial-automobile-price-deploy.md)
