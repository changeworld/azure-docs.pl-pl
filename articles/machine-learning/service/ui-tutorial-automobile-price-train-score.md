---
title: 'Samouczek: Prognozowanie cen samochodów za pomocą interfejsu wizualnego'
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak uczenie, ocenę i wdrożyć model uczenia maszynowego przy użyciu przeciągania i upuszczania interfejsu wizualnego. Niniejszy samouczek jest pierwszą częścią serii legalną dwuczęściową na Prognozowanie cen samochodów przy użyciu regresji liniowej.
author: peterlu
ms.author: peterlu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 04/06/2019
ms.openlocfilehash: 1dc905b2ac89667dcb5800b0f539cb3e9f3acdcd
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/02/2019
ms.locfileid: "65026872"
---
# <a name="tutorial-predict-automobile-price-with-the-visual-interface"></a>Samouczek: Prognozowanie cen samochodów za pomocą interfejsu wizualnego

W tym samouczku możesz Przyjrzyj się rozszerzone tworzenie rozwiązania do analizy predykcyjnej w interfejsie visual usługi Azure Machine Learning. Do końca tego samouczka będziesz mieć rozwiązania, który potrafi prognozować cenę dowolnej samochód, oparte na specyfikacji technicznej, który zostanie wysłany.

W tym samouczku [jest kontynuowane od tego przewodnika Szybki Start](ui-quickstart-run-experiment.md) i **część jednej z dwóch części serii samouczków**. Jednakże nie masz do ukończenia tego przewodnika Szybki Start, przed rozpoczęciem.

W części pierwszej serii samouczków dowiesz się, jak:

> [!div class="checklist"]
> * Importowanie i czyszczenie danych (te same kroki tego przewodnika Szybki Start)
> * Szkolenie modelu uczenia maszynowego
> * Generowanie wyników i oceny modelu

W [część druga](ui-tutorial-automobile-price-deploy.md) serii samouczków dowiesz się, jak wdrożyć model predykcyjny jako usługę sieci web platformy Azure.

> [!NOTE]
> Pełną wersję tego samouczka jest dostępna jako przykładowego eksperymentu.
> Na stronie eksperymentów, przejdź do **Dodaj nowe** > **przykładowe 1 - regresji: Prediction(Basic) cen samochodów**

> [!VIDEO https://www.youtube.com/embed/VDKpDNX96aA]


## <a name="create-a-workspace"></a>Tworzenie obszaru roboczego

Jeśli masz obszar roboczy usługi Azure Machine Learning, przejdź do [następnej sekcji](#open-the-visual-interface-webpage). W przeciwnym razie utworzyć teraz.

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="open-the-visual-interface-webpage"></a>Otwórz interfejs graficzny strony sieci Web

1. Otwieranie obszaru roboczego w [witryny Azure portal](https://portal.azure.com/).  

1. W obszarze roboczym, wybierz **interfejs graficzny**.  Następnie wybierz pozycję **interfejs graficzny uruchamiania**.  

    ![Zrzut ekranu witryny Azure portal pokazujący sposób dostępu interfejs graficzny z obszaru roboczego usługi Machine Learning](./media/ui-tutorial-automobile-price-train-score/launch-ui.png)

    Strony sieci Web interfejs otwiera nową stronę w przeglądarce.  

## <a name="import-and-clean-your-data"></a>Importowanie i czyszczenie danych

Pierwszą rzeczą, jaką należy to czyste dane. Jeśli ukończono Przewodnik Szybki Start można ponownie użyć danych przygotowania eksperymentu w tym miejscu. Jeśli nie wykonano tego przewodnika Szybki Start, Pomiń następnej sekcji i [Rozpoczynanie od nowego eksperymentu](#start-from-a-new-experiment).

### <a name="reuse-the-quickstart-experiment"></a>Ponowne użycie eksperymentu Szybki Start

1. Otwórz eksperyment Szybki Start.

1. Wybierz **Zapisz jako** w dolnej części okna.

1. Nadaj nową nazwę w wyskakującym oknie dialogowym, który pojawia się.

    ![Zrzut ekranu przedstawiający sposób zmiany nazwy eksperymentu do "Samouczek — Prognozowanie Automobile Price"](./media/ui-tutorial-automobile-price-train-score/save-a-copy.png)

1. Eksperyment powinien wyglądać teraz mniej więcej tak:

    ![Zrzut ekranu przedstawiający oczekiwany stan eksperymentu. Zestawu danych dotyczących samochodów i nawiązanie połączenia z modułu Select Columns, który nawiązuje połączenie z Clean Missing Data](./media/ui-tutorial-automobile-price-train-score/save-copy-result.png)

Jeśli pomyślnie ponownie użyty eksperymentu Szybki Start, Pomiń następnej sekcji, aby rozpocząć [szkolenie modelu](#train-the-model).

### <a name="start-from-a-new-experiment"></a>Rozpoczynanie od nowego eksperymentu

Jeśli nie możesz ukończyć tego przewodnika Szybki Start, wykonaj następujące kroki, aby szybko utworzyć nowy eksperyment, który importuje i czyści samochodów zestawu danych.

1. Tworzenie nowego eksperymentu, wybierając **+ nowy** w dolnej części okna interfejsu wizualnego.

1. Wybierz pozycję **EXPERIMENT** (EKSPERYMENT) >  **Blank Experiment** (Pusty eksperyment).

1. Wybierz domyślną nazwę eksperymentu **"badawcze, mające utworzone na...** "w górnej części obszaru roboczego i zmień jego nazwę na bardziej opisową nazwę. Na przykład **Prognozowanie cen samochodów**. Nazwa nie musi być unikatowa.

1. Z lewej strony obszaru roboczego eksperymentu znajduje się paleta zawierająca zestawy danych i moduły. Aby znaleźć modułów, użyj pola wyszukiwania w górnej części palety modułów. Typ **samochodów** w polu wyszukiwania, aby znaleźć zestaw danych z etykietą **danych dotyczących cen samochodów (Raw)**. Przeciągnij ten zestaw danych do obszaru roboczego eksperymentu.

    ![Zrzut ekranu jak znaleźć zestaw danych cen samochodów](./media/ui-tutorial-automobile-price-train-score/automobile-dataset.png)

    Teraz, gdy masz dane, można dodać moduł, który usunie **kolumny znormalizowane straty** kolumny całkowicie. Następnie należy dodać inny moduł, który usunie wszystkie wiersze z brakującymi danymi.

1. Typ **wybierz kolumny** w polu wyszukiwania, aby znaleźć **Select Columns in Dataset** modułu. Następnie przeciągnij go do obszaru roboczego eksperymentu. Ten moduł służy do wybierania, kolumny danych, które mają zostać dołączone lub wykluczone w modelu.

1. Połącz port wyjściowy zestawu danych **Automobile price data (Raw)** (Nieprzetworzone dane z cenami samochodów) z portem wejściowym modułu Select Columns in Dataset (Wybieranie kolumn w zestawie danych).

    ![Animowany obraz gif przedstawiający sposób łączenie modułu Select Columns modułem Automobile Price Data](./media/ui-tutorial-automobile-price-train-score/connect-modules.gif)

1. Wybierz kolumny, wybierz w module zestawu danych, a następnie wybierz pozycję **uruchamianie selektora kolumn** w **właściwości** okienka.

   1. Po lewej stronie, wybierz **przy użyciu reguł**

   1. Obok pozycji **rozpocząć z**, wybierz opcję **wszystkie kolumny**. Te reguły bezpośredniej **Select Columns in Dataset** przekazywane do wszystkich kolumn (z wyjątkiem tych wykluczonych).

   1. Wybierz z listy rozwijane **wykluczyć** i **nazwy kolumn**, a następnie wpisz **kolumny znormalizowane straty** wewnątrz pola tekstowego.

   1. Wybierz przycisk OK, aby zamknąć selektora kolumn (w lewym dolnym rogu).

     Zawartość okienka właściwości modułu **Select Columns in Dataset** (Wybieranie kolumn w zestawie danych) określa, że zostaną przetworzone wszystkie kolumny zestawu danych z wyjątkiem kolumny **normalized-losses** (znormalizowane straty).

1. Dodaj komentarz do **Select Columns in Dataset** modułu, kliknij dwukrotnie moduł i wprowadzając "Wyklucz znormalizowane straty". Może to ułatwić możesz zobaczyć na pierwszy rzut oka moduł działania w eksperymencie.

    ![Zrzut ekranu przedstawiający prawidłowej konfiguracji modułu wybierz kolumny](./media/ui-tutorial-automobile-price-train-score/select-columns.png)

1. Typ **czysty** w polu wyszukiwania, aby znaleźć **Clean Missing Data** modułu. Przeciągnij **Clean Missing Data** modułów na eksperyment kanwy i połącz go **Select Columns in Dataset** modułu.

1. W okienku **Properties** (Właściwości) w obszarze **Cleaning mode** (Tryb czyszczenia) wybierz pozycję **Remove entire row** (Usuń cały wiersz). Te opcje bezpośredniego **Clean Missing Data** aby wyczyścić dane przez usunięcie wierszy, które nie mają żadnych wartości. Kliknij dwukrotnie moduł i wpisz komentarz „Usunięcie wierszy z brakującymi wartościami”.

![Zrzut ekranu przedstawiający prawidłowej konfiguracji modułu Clean Missing Data](./media/ui-tutorial-automobile-price-train-score/clean-missing-data.png)

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

    Teraz eksperyment powinien wyglądać następująco.
    ![Zrzut ekranu przedstawiający prawidłowej konfiguracji eksperymentu po dodaniu modułu Train Model.](./media/ui-tutorial-automobile-price-train-score/train-graph.png)

### <a name="run-the-training-experiment"></a>Uruchomienie eksperymentu szkolenia

[!INCLUDE [aml-ui-create-training-compute](../../../includes/aml-ui-create-training-compute.md)]

## <a name="score-and-evaluate-the-model"></a>Generowanie wyników i oceny modelu

Skoro już uczony model przy użyciu danych do 70 procent, służy do oceniania inne 30 procent danych, aby zobaczyć, jak dobrze funkcji modelu.

1. Typ **score model** w polu wyszukiwania, aby znaleźć **Score Model** moduł i przeciągnij moduł do obszaru roboczego eksperymentu. Połącz wyjście **Train Model** portem wejściowym modułu po lewej stronie **Score Model**. Połącz wyjście danych testowych (prawy port) z **podziału danych** portem wejściowym modułu po prawej stronie **Score Model**.

1. Typ **oceny** w polu wyszukiwania, aby znaleźć **Evaluate Model** i przeciągnij it modułu do obszaru roboczego eksperymentu. Połącz wyjście **Score Model** wejściem modułu po lewej stronie **Evaluate Model**. Końcowy eksperyment powinien wyglądać następująco:

    ![Zrzut ekranu przedstawiający końcowego prawidłowej konfiguracji eksperymentu.](./media/ui-tutorial-automobile-price-train-score/final-graph.png)

1. Uruchom eksperyment, przy użyciu tej samej wartości docelowej obliczeń użyte wcześniej.

1. Wyświetl dane wyjściowe z **Score Model** modułu, wybierając port wyjściowy **Score Model** i wybierz **Visualize**. Dane wyjściowe zawierają przewidywane wartości cen oraz znane wartości pochodzące z danych testowych.

    ![Zrzut ekranu przedstawiający wizualizacji danych wyjściowych, wyróżnianie kolumny "Oceniane Label"](./media/ui-tutorial-automobile-price-train-score/score-result.png)

1. Aby wyświetlić dane wyjściowe z modułu Evaluate Model, wybierz port wyjściowy, a następnie wybierz wizualizacja.

    ![Zrzut ekranu przedstawiający wyniki oceny eksperymentu końcowej.](./media/ui-tutorial-automobile-price-train-score/evaluate-result.png)

Dla modelu, wyświetlane są następujące statystyki:

* **Średni bezwzględny błąd (dostosowania)**: Średnia bezwzględnych błędów (błąd występuje różnica między wartością prognozowaną a wartością rzeczywistą).
* **Średnia głównego kwadrat błąd (RMSE)**: pierwiastek kwadratowy ze średniej kwadratów błędów prognoz dla zestawu danych testowych.
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

## <a name="next-steps"></a>Kolejne kroki

W części pierwszej części tego samouczka można wykonać następujące czynności:

* Ponowne użycie eksperymentu, utworzone w przewodniku Szybki Start
* Przygotowywanie danych
* Uczenie modelu
* Generowanie wyników i oceny modelu

Część druga dowiesz się, jak wdrożyć model jako usługę sieci web platformy Azure.

> [!div class="nextstepaction"]
> [Wdrażanie modeli w dalszym ciągu](ui-tutorial-automobile-price-deploy.md)
