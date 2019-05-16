---
title: 'Szybki start: Przygotowanie i wizualizowanie danych bez konieczności pisania kodu'
titleSuffix: Azure Machine Learning service
description: Tworzenie eksperymentu uczenia maszynowego, aby przygotować i wizualizuj dane przy użyciu interfejsu użytkownika przeciągnij i upuść.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: quickstart
author: sdgilley
ms.author: sgilley
ms.date: 05/02/2019
ms.openlocfilehash: 0c492424e67853f7cb4a017fb4215d38a555a8a4
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2019
ms.locfileid: "65545025"
---
# <a name="quickstart-prepare-and-visualize-data-without-writing-code-in-azure-machine-learning"></a>Szybki start: Przygotowanie i wizualizowanie danych bez konieczności pisania kodu w usłudze Azure Machine Learning

Przygotowanie i wizualizowanie danych w interfejsie visual przeciąganie i upuszczanie (wersja zapoznawcza) dla usługi Azure Machine Learning. Dane, które będą używane zawiera wpisy dla różnych samochodów, na przykład informacji takich jak marka, model, przedstawiono dane techniczne i ceny.  

W tym przewodniku Szybki Start możesz eksplorować i przygotowywania danych:

- Tworzenie pierwszego eksperymentu, aby dodać, a także wyświetlić podgląd danych
- Przygotowywanie danych, usuwając brakujące wartości
- Uruchamianie eksperymentu
- Wizualizowanie danych

Jeśli dopiero zaczynasz do usługi machine learning, seria wideo [przetwarzanie danych dla początkujących](https://docs.microsoft.com/azure/machine-learning/studio/data-science-for-beginners-the-5-questions-data-science-answers) stanowi znakomite wprowadzenie do usługi machine learning.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję usługi Azure Machine Learning](https://aka.ms/AMLFree) już dziś.

### <a name="create-a-workspace"></a>Tworzenie obszaru roboczego

Jeśli masz obszar roboczy usługi Azure Machine Learning, przejdź do [następnej sekcji](#start). W przeciwnym razie utworzyć teraz.

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="start"></a> Otwórz interfejs graficzny strony sieci Web

1. Otwieranie obszaru roboczego w [witryny Azure portal](https://portal.azure.com/).  

1. W obszarze roboczym, wybierz **interfejs graficzny**.  Następnie wybierz pozycję **interfejs graficzny uruchamiania**.  
 
    ![Uruchom interfejs graficzny](./media/ui-quickstart-run-experiment/launch-ui.png)

    Strony sieci Web interfejs otwiera nową stronę w przeglądarce.  

## <a name="create-your-first-experiment"></a>Tworzenie pierwszego eksperymentu

Narzędzie interfejs graficzny zapewnia interaktywny, wizualny miejsce do łatwego tworzenia, testowania i wykonania iteracji modelu analizy predykcyjnej. Możesz przeciągnij i upuść zestawy danych i analiza modułów analityczne na interaktywną, łącząc je ze sobą w celu przygotowania _eksperymentować_.  Teraz Utwórz swój pierwszy eksperyment z zakresu.

1. W lewym dolnym rogu, wybierz **Dodaj nowe**.
![Dodaj nowy eksperyment](./media/ui-quickstart-run-experiment/add-new.png)

1. Wybierz **Blank Experiment**.

1. Twoje doświadczenie jest nadawana nazwa domyślna. Zaznacz ten tekst i zmień jego nazwę na "Zapoznaj się z przewodnika Szybki Start danych". Ta nazwa nie musi być unikatowa.

1. **Mapy Mini** w dolnej części ekranu jest przydatny do wyświetlania dużych eksperymentów.  Nie będzie potrzebny w tym przewodniku Szybki Start kliknij strzałkę znajdującą się u góry, aby je zminimalizować.  

    ![Zmienianie nazwy eksperymentu](./media/ui-quickstart-run-experiment/rename.png)

## <a name="add-data"></a>Dodawanie danych

Pierwszą rzeczą, jaką należy dla usługi machine learning są dane. Istnieje kilka przykładowych zestawów danych w tym interfejsie, którego można używać, lub możesz zaimportować dane z wielu źródeł. W tym przykładzie użyjemy przykładowego zestawu danych **danych dotyczących cen samochodów (Raw)**. 

1. Z lewej strony obszaru roboczego eksperymentu znajduje się paleta zawierająca zestawy danych i moduły. Wybierz **zapisane zestawów danych** polecenie **przykłady** do wyświetlania dostępnych przykładowych zestawów danych.

1. Wybierz zestaw danych, **(nieprzetworzone) dane cen samochodów**i przeciągnij go na kanwie.

   ![Przeciągnij danych do kanwy](./media/ui-quickstart-run-experiment/drag-data.png)


## <a name="select-columns"></a>Wybieranie kolumn

Wybierz kolumny, które dane chcesz pracować.  Do konfigurowania modułu, aby wyświetlić wszystkie dostępne kolumny.

> [!TIP]
> Jeśli znasz nazwę danych lub moduł, który ma umożliwia pasek wyszukiwania w górnej części palety szybkie znajdowanie.  Pozostała część tego przewodnika Szybki Start będzie używać tego skrótu.

1. Typ **wybierz** w polu wyszukiwania, aby znaleźć **Select Columns in Dataset** modułu.

1. Kliknij i przeciągnij **Select Columns in Dataset** na kanwę. Usuń moduł poniżej zestawu danych, który dodano wcześniej.

1. Łączenie zestawu danych na **Select Columns in Dataset**: kliknij port wyjściowy zestawu danych, przeciągnij portem wejściowym **Select Columns in Dataset**, następnie zwolnij przycisk myszy. Zestaw danych i moduł pozostaną połączone, nawet jeśli będziesz przenosić je w obrębie kanwy.

    > [!TIP]
    > Zestawy danych i moduły mają porty wejściowe i wyjściowe oznaczone małymi kołami — porty wejściowe znajdują się u góry, a wyjściowe u dołu. Utworzysz przepływ danych w eksperymencie, po nawiązaniu połączenia z portem wejściowym innego port wyjściowy jeden moduł.
    >
    > Jeśli masz problemy z połączeniem z modułów, wypróbuj przeciągnięcie aż do węzła, w którym jest nawiązywane.

    ![Łączenie modułów](./media/ui-quickstart-run-experiment/connect-modules.gif) 

    Czerwony wykrzyknik wskazuje, że właściwości modułu nie zostały ustawione jeszcze. Zrobisz to w następnym kroku.
   
1. Wybierz **Select Columns in Dataset** modułu.

1. W **właściwości** w okienku po prawej stronie obszaru roboczego wybierz **Upravit sloupce**.

    W **wybierz kolumny** okno dialogowe, wybierz **wszystkie kolumny** i obejmują **wszystkie funkcje**. Okno dialogowe powinno wyglądać następująco:

     ![Selektor kolumn](./media/ui-quickstart-run-experiment/select-all.png)

1. W prawym dolnym rogu wybierz **OK** aby zamknąć selektora kolumn.

## <a name="run-the-experiment"></a>Uruchamianie eksperymentu

W dowolnym momencie kliknij port wyjściowy zestawu danych lub modułu, aby wyświetlić dane wygląda podobnie jak w tym momencie przepływu danych.  Jeśli **Visualize** opcja jest wyłączona, należy najpierw uruchomić eksperyment.  Zrobisz to w następnym kroku.

[!INCLUDE [aml-ui-create-training-compute](../../../includes/aml-ui-create-training-compute.md)]

Po udostępnieniu obliczeniowego elementu docelowego jest uruchamiany eksperymentu. Po zakończeniu przebiegu na każdy moduł pojawi się zielony znacznik wyboru.

![Wyświetl stan](./media/ui-quickstart-run-experiment/status1.png)

## <a name="preview-the-data"></a>Wyświetl podgląd danych

Teraz, gdy uruchomiono początkowej eksperymentu, możesz wizualizować dane, aby dowiedzieć się więcej o informacje, które trzeba pracować.

1. Wybierz port wyjściowy w dolnej części **Select Columns in Dataset** polecenie **Visualize**.

1. Polecenie różnych kolumn w oknie dane, aby wyświetlić informacje o tej kolumny.  

    W tym zestawie danych poszczególne wiersze reprezentują samochody, a zmienne skojarzone z samochodami są wyświetlane jako kolumny.    Brak 205 wierszy i kolumn 26, w tym zestawie danych.

     Każde kliknięcie kolumny danych, **statystyki** informacji i **wizualizacji** po lewej stronie jest wyświetlany obraz tej kolumny.  Na przykład po kliknięciu **num z drzwi** zobaczysz w nim 2 unikatowe wartości i 2 brakujące wartości.  Przewiń w dół, aby zobaczyć wartości: drzwi dwóch do czterech.

     ![Wyświetl podgląd danych](./media/ui-quickstart-run-experiment/preview-data.gif)

1. Kliknij przycisk dotyczących poszczególnych kolumn, aby dowiedzieć się więcej na temat zestawu danych.

## <a name="prepare-data"></a>Przygotowywanie danych

Zestawy danych zwykle wymagają przetworzenia wstępnego przed rozpoczęciem analizy. Zwróć uwagę na to, że w przypadku niektórych wierszy w kolumnach brakuje wartości. Te brakujące wartości muszą zostać wyczyszczone, aby umożliwić modelowi wykonanie poprawnej analizy danych. Należy usunąć wszystkie wiersze z brakującymi wartościami. Ponadto **kolumny znormalizowane straty** kolumna ma dużą część brakujące wartości, dzięki czemu będzie wyłączyć tę kolumnę z modelu całkowicie.

> [!TIP]
> Większość modułów wymaga wyczyszczenia brakujących wartości w danych wejściowych.  

### <a name="remove-column"></a>Usunięcie kolumny

Najpierw należy usunąć **kolumny znormalizowane straty** kolumny całkowicie.

1. Wybierz **Select Columns in Dataset** modułu.

1. W **właściwości** w okienku po prawej stronie obszaru roboczego wybierz **Upravit sloupce**.

    * Pozostaw **przy użyciu reguł** i **wszystkie kolumny** wybrane.

    * Z list rozwijanych wybierz pozycje **Exclude** (Wyklucz) i **column names** (nazwy kolumn), a następnie kliknij wewnątrz pola tekstowego. Typ **kolumny znormalizowane straty**.

    * W prawym dolnym rogu wybierz **OK** aby zamknąć selektora kolumn.

    ![Wyklucz kolumny](./media/ui-quickstart-run-experiment/exclude-column.png)
        
    Teraz zawartość okienka właściwości Select Columns in Dataset wskazuje, że zostaną przetworzone wszystkie kolumny z zestawu danych, z wyjątkiem **kolumny znormalizowane straty**.
        
    Zawartość okienka właściwości wskazuje, że **kolumny znormalizowane straty** wykluczenie kolumny.
        
    ![Okienko właściwości](./media/ui-quickstart-run-experiment/property-pane.png)
        
    Aby dodać komentarz do modułu, kliknij dwukrotnie moduł i wpisz tekst. Pozwoli to od razu sprawdzić rolę modułu w eksperymencie. 

1. Kliknij dwukrotnie **Select Columns in Dataset** moduł i wpisz komentarz "wykluczenie kolumny znormalizowane straty". 
    
    Po użytkownik wpisze komentarz, kliknij na zewnątrz modułu.  Aby pokazać, że moduł zawiera komentarz pojawi się kształt strzałki w dół.

1. Kliknij strzałkę w dół, aby wyświetlić komentarz.

    Moduł zawiera obecnie strzałkę w górę, aby ukryć komentarz.
        
    ![Komentarze](./media/ui-quickstart-run-experiment/comments.png)

### <a name="clean-missing-data"></a>Wyczyść brakujące dane

Teraz Dodaj inny moduł, który usunie wszystkie pozostałe wiersze z brakującymi danymi.

1. Typ **czysty** w polu wyszukiwania, aby znaleźć **Clean Missing Data** modułu.

1. Przeciągnij **Clean Missing Data** modułów na eksperyment kanwy i połącz go **Select Columns in Dataset** modułu. 

1. W okienku właściwości wybierz **Usuń cały wiersz** w obszarze **tryb czyszczenia**.

    Te opcje bezpośredniego **Clean Missing Data** aby wyczyścić dane przez usunięcie wierszy, które nie mają żadnych wartości.

1. Kliknij dwukrotnie moduł i wpisz komentarz „Usunięcie wierszy z brakującymi wartościami”.
 
    ![Usuń wiersze](./media/ui-quickstart-run-experiment/remove-rows.png)

    Eksperyment powinien teraz wyglądać mniej więcej tak:
    
    ![Wybierz kolumny](./media/ui-quickstart-run-experiment/experiment-clean.png)

## <a name="visualize-the-results"></a>Wizualizacja wyników

Od wprowadzenia zmian do modułów w eksperymencie, stan został zmieniony na "Robocza w".  Do wizualizacji nowych danych czystego, należy najpierw uruchomić eksperyment ponownie.

1. Wybierz **Uruchom** u dołu, aby uruchomić eksperyment.

    Teraz możesz ponownie użyć docelowej obliczeń, utworzonego wcześniej.  

1. Wybierz **Uruchom** w oknie dialogowym.

   ![Uruchamianie eksperymentu](./media/ui-quickstart-run-experiment/select-compute.png)

1. Po zakończeniu przebiegu, kliknij prawym przyciskiem myszy **Clean Missing Data** modułu do wizualizacji nowych danych czyste.  

    ![Wizualizuj Wyczyść dane](./media/ui-quickstart-run-experiment/visualize-cleaned.png)

1. Polecenie różnych kolumn w oknie operacji czyszczenia danych, aby zobaczyć, jak dane zostały zmienione.  

    ![Wizualizuj Wyczyść dane](media/ui-quickstart-run-experiment/visualize-result.png)

    Istnieją teraz 193 wierszy i kolumn 25.

    Po kliknięciu **num z drzwi** Zobacz środki, nieopłacone 2 unikatowe wartości, ale ma teraz 0 brakuje wartości.  

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Kolejne kroki

W tym przewodniku Szybki start zawarto informacje na temat wykonywania następujących czynności:

- Tworzenie pierwszego eksperymentu, aby dodać, a także wyświetlić podgląd danych
- Przygotowywanie danych, usuwając brakujące wartości
- Wizualizowanie danych

Przejdź do samouczka, aby użyć tych danych do prognozowania cen samochodów.

> [!div class="nextstepaction"]
> [Samouczek: Prognozowanie cen samochodów za pomocą interfejsu wizualnego](ui-tutorial-automobile-price-train-score.md)
