---
title: 'Samouczek: Przewiduj cenę samochodu z projektantem'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak szkolić, oceniać i wdrażać model uczenia maszynowego przy użyciu interfejsu przeciągania i upuszczania. Ten samouczek jest częścią pierwszej serii dwuczęściowej na temat przewidywania cen samochodów przy użyciu regresji liniowej.
author: peterclu
ms.author: peterlu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 03/12/2020
ms.openlocfilehash: 0488002352d222abb0166737f9a042060b1a1bb1
ms.sourcegitcommit: 0553a8b2f255184d544ab231b231f45caf7bbbb0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2020
ms.locfileid: "80389430"
---
# <a name="tutorial-predict-automobile-price-with-the-designer-preview"></a>Samouczek: Przewiduj cenę samochodu z projektantem (wersja zapoznawcza)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

W tym dwuczęściowym samouczku dowiesz się, jak użyć projektanta usługi Azure Machine Learning do uczenia i wdrażania modelu uczenia maszynowego, który przewiduje cenę dowolnego samochodu. Projektant jest narzędziem przeciągania i upuszczania, które umożliwia tworzenie modeli uczenia maszynowego bez jednego wiersza kodu.

W jednej części samouczka dowiesz się, jak:

> [!div class="checklist"]
> * Utwórz nowy potok.
> * Importowanie danych.
> * Przygotuj dane.
> * Trenuj model uczenia maszynowego.
> * Oceń model uczenia maszynowego.

W [drugiej części](tutorial-designer-automobile-price-deploy.md) samouczka wdrożysz model jako punkt końcowy w czasie rzeczywistym, aby przewidzieć cenę dowolnego samochodu na podstawie specyfikacji technicznych, które go wysyłasz. 

> [!NOTE]
>Ukończona wersja tego samouczka jest dostępna jako przykładowy potok.
>
>Aby go znaleźć, przejdź do projektanta w obszarze roboczym. W sekcji **Nowy potok** wybierz **przykład 1 - Regresja: Przewidywanie cen samochodów (Podstawowe)**.

## <a name="create-a-new-pipeline"></a>Tworzenie nowego potoku

Potoki usługi Azure Machine Learning organizują wiele kroków uczenia maszynowego i przetwarzania danych w jednym zasobie. Potoki umożliwiają organizowanie, zarządzanie i ponowne używanie złożonych przepływów pracy uczenia maszynowego w projektach i użytkownikach.

Aby utworzyć potok usługi Azure Machine Learning, potrzebujesz obszaru roboczego usługi Azure Machine Learning. W tej sekcji dowiesz się, jak utworzyć oba te zasoby.

### <a name="create-a-new-workspace"></a>Tworzenie nowego obszaru roboczego

Aby korzystać z projektanta, najpierw potrzebujesz obszaru roboczego usługi Azure Machine Learning. Obszar roboczy jest zasobem najwyższego poziomu dla usługi Azure Machine Learning, zapewnia scentralizowane miejsce do pracy ze wszystkimi artefaktami utworzonymi w usłudze Azure Machine Learning.

Jeśli masz obszar roboczy usługi Azure Machine Learning z wydaniem Enterprise, [przejdź do następnej sekcji](#create-the-pipeline).

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal-enterprise.md)]

### <a name="create-the-pipeline"></a>Tworzenie potoku

1. Zaloguj się, aby <a href="https://ml.azure.com?tabs=jre" target="_blank">ml.azure.com</a>i wybierz obszar roboczy, z którego chcesz pracować.

1. Wybierz **pozycję Projektant**.

    ![Zrzut ekranu przedstawiający wizualny obszar roboczy przedstawiający dostęp do projektanta](./media/tutorial-designer-automobile-price-train-score/launch-designer.png)

1. Wybierz **łatwe w użyciu moduły prekompilowane**.

1. U góry obszaru roboczego wybierz domyślną nazwę potoku **Pipeline-Created-on**. Zmień jego nazwę na *Przewidywanie cen samochodów*. Nazwa nie musi być unikatowa.

## <a name="set-the-default-compute-target"></a>Ustawianie domyślnego obiektu docelowego obliczeń

Potok jest uruchamiany na podstawie obiektu docelowego obliczeń, który jest zasobem obliczeniowym dołączonym do obszaru roboczego. Po utworzeniu obiektu docelowego obliczeń można go ponownie użyć do przyszłych uruchomień.

Można ustawić **domyślny obiekt docelowy obliczeń** dla całego potoku, który powie każdemu modułowi, aby domyślnie używał tego samego celu obliczeniowego. Można jednak określić obiekty docelowe obliczeń dla na podstawie modułu.

1. Obok nazwy potoku wybierz ![](./media/tutorial-designer-automobile-price-train-score/gear-icon.png) **ikonę Koła zębatego** Zrzut ekranu ikony koła zębatego u góry obszaru roboczego, aby otworzyć okienko **Ustawienia.**

1. W okienku **Ustawienia** po prawej stronie kanwy wybierz **pozycję Wybierz cel obliczeniowy**.

    Jeśli masz już dostępny obiekt docelowy obliczeń, możesz go wybrać, aby uruchomić ten potok.

    > [!NOTE]
    > Projektant może uruchamiać eksperymenty tylko na obiektach docelowych obliczeń usługi Azure Machine Learning. Inne obiekty docelowe obliczeń nie będą wyświetlane.

1. Wprowadź nazwę zasobu obliczeniowego.

1. Wybierz **pozycję Zapisz**.

    > [!NOTE]
    > Utworzenie zasobu obliczeniowego zajmuje około pięciu minut. Po utworzeniu zasobu można go ponownie użyć i pominąć ten czas oczekiwania na przyszłe przebiegi.
    >
    > Zasoby obliczeniowe są skalowane automatycznie do zera węzłów, gdy jest bezczynny, aby zaoszczędzić koszt. Podczas korzystania z niego ponownie po opóźnieniu, może wystąpić około pięciu minut czasu oczekiwania, podczas gdy skaluje kopii zapasowej.

## <a name="import-data"></a>Importowanie danych

Istnieje kilka przykładowych zestawów danych uwzględnionych w projektancie, z których można eksperymentować. W tym samouczku użyj **danych o cenach samochodów (Raw)**. 

1. Po lewej stronie kanwy potoku znajduje się paleta zestawów danych i modułów. Wybierz **pozycję Zestawy danych**, a następnie wyświetl sekcję **Przykłady,** aby wyświetlić dostępne przykładowe zestawy danych.

1. Wybierz zestaw danych **Dane ceny samochodów (raw)** i przeciągnij je na kanwę.

   ![Przeciąganie danych na kanwę](./media/tutorial-designer-automobile-price-train-score/drag-data.gif)

### <a name="visualize-the-data"></a>Wizualizacja danych

Możesz wizualizować dane, aby zrozumieć zestaw danych, którego będziesz używać.

1. Wybierz **moduł Dane o cenie samochodu (Raw).**

1. W okienku szczegółów modułu po prawej stronie kanwy wybierz pozycję **Wyjścia + dziennik**.

1. Wybierz ikonę wykresu, aby zwizualizować dane.

    ![Wizualizacja danych](./media/tutorial-designer-automobile-price-train-score/visualize-data.png)

1. Wybierz różne kolumny w oknie danych, aby wyświetlić informacje o każdej z nich.

    Każdy wiersz reprezentuje samochód, a zmienne skojarzone z każdym samochodem są wyświetlane jako kolumny. W tym zestawie danych znajduje się 205 wierszy i 26 kolumn.

## <a name="prepare-data"></a>Przygotowywanie danych

Zestawy danych zazwyczaj wymagają wstępnego przetwarzania przed analizą. Być może podczas inspekcji zestawu danych zauważono brakujące wartości. Te brakujące wartości muszą być czyszczone, tak aby model mógł poprawnie analizować dane.

### <a name="remove-a-column"></a>Usuwanie kolumny

Podczas szkolenia modelu, trzeba coś zrobić o danych, których brakuje. W tym zestawie danych w kolumnie **znormalizowane straty** brakuje wielu wartości, więc całkowicie wykluczysz tę kolumnę z modelu.

1. W palecie modułów po lewej stronie kanwy rozwiń sekcję **Transformacja danych** i znajdź moduł Wybierz kolumny w module **Zestaw danych.**

1. Przeciągnij moduł **Wybierz kolumny w zestawie danych** na kanwę. Upuść moduł poniżej modułu zestawu danych.

1. Podłącz zestaw danych **o cenach samochodów (raw)** do modułu **Wybierz kolumny w module Zestaw danych.** Przeciągnij z portu wyjściowego zestawu danych, który jest małym okręgiem u dołu zestawu danych na kanwie, do portu wejściowego **Wybierz kolumny w zestawie danych,** czyli małego okręgu u góry modułu.

    > [!TIP]
    > Przepływ danych można utworzyć za pośrednictwem potoku po podłączeniu portu wyjściowego jednego modułu do portu wejściowego innego.
    >

    ![Moduły connect](./media/tutorial-designer-automobile-price-train-score/connect-modules.gif)

1. Wybierz moduł Wybierz kolumny w module **Zestaw danych.**

1. W okienku szczegółów modułu po prawej stronie obszaru roboczego wybierz pozycję **Edytuj kolumnę**.

1. Rozwiń **pozycję Nazwy kolumn** obok pozycji **Uwzględnij**i wybierz pozycję **Wszystkie kolumny**.

1. Wybierz, **+** aby dodać nową regułę.

1. Z menu rozwijanych wybierz pozycję **Wyklucz** i **nazwy kolumn**.
    
1. Wprowadź *znormalizowane straty* w polu tekstowym.

1. W prawym dolnym przycisku wybierz pozycję **Zapisz,** aby zamknąć selektor kolumny.

    ![Wykluczanie kolumny](./media/tutorial-designer-automobile-price-train-score/exclude-column.png)

1. Wybierz moduł Wybierz kolumny w module **Zestaw danych.** 

1. W okienku szczegółów modułu po prawej stronie obszaru roboczego zaznacz pole tekstowe **Komentarz** i wprowadź *wyklucz znormalizowane straty*.

    Komentarze pojawią się na wykresie, aby ułatwić organizowanie potoku.

### <a name="clean-missing-data"></a>Czyszczenie brakujących danych

Po usunięciu kolumny **znormalizowane straty** w zestawie danych nadal brakuje wartości. Pozostałe brakujące dane można usunąć za pomocą modułu **Czyste brakujące dane.**

> [!TIP]
> Czyszczenie brakujących wartości z danych wejściowych jest warunkiem wstępnym do korzystania z większości modułów w projektancie.

1. W palecie modułów po lewej stronie kanwy rozwiń sekcję **Transformacja danych**i znajdź moduł **Czyste brakujące dane.**

1. Przeciągnij moduł **Czyste brakujące dane** na kanwę potoku. Podłącz go do modułu Wybierz kolumny w module **Zestawu danych.** 

1. Wybierz moduł **Czyste brakujące dane.**

1. W okienku szczegółów modułu po prawej stronie obszaru roboczego wybierz pozycję **Edytuj kolumnę**.

1. W **wyświetlonym oknie Kolumny mają być czyszczone** rozwiń menu rozwijane obok pozycji **Uwzględnij**. Zaznaczanie, **Wszystkie kolumny**

1. Wybierz pozycję **Zapisz**.

1. W okienku szczegółów modułu po prawej stronie obszaru roboczego wybierz pozycję **Usuń cały wiersz** w obszarze Tryb **czyszczenia**.

1. W okienku szczegółów modułu po prawej stronie kanwy zaznacz pole **Komentarz** i wprowadź *usuń brakujące wiersze wartości*. 

    Potok powinien teraz wyglądać mniej więcej tak:
    
    ![Wybierz kolumnę](./media/tutorial-designer-automobile-price-train-score/pipeline-clean.png)

## <a name="train-a-machine-learning-model"></a>Szkolenie modelu uczenia maszynowego

Teraz, gdy masz moduły w miejscu do przetwarzania danych, można skonfigurować moduły szkoleniowe.

Ponieważ chcesz przewidzieć cenę, która jest liczbą, można użyć algorytmu regresji. W tym przykładzie należy użyć modelu regresji liniowej.

### <a name="split-the-data"></a>Dzielenie danych

Dzielenie danych jest typowym zadaniem w uczeniu maszynowym. Dane zostaną podzielone na dwa oddzielne zestawy danych. Jeden zestaw danych będzie szkolić model, a drugi będzie testować, jak dobrze model wykonywane.

1. W palecie modułów rozwiń sekcję **Transformacja danych** i znajdź moduł **Podziel dane.**

1. Przeciągnij moduł **Podziel dane** na kanwę potoku.

1. Podłącz lewy port modułu **Czyste brakujące dane** do modułu **Podziel dane.**

    > [!IMPORTANT]
    > Upewnij się, że lewe porty wyjściowe **czystych brakujących danych** łączą się z **podzielonymi danymi**. Lewy port zawiera wyczyszczone dane. Prawy port zawiera dane z dyskusconym.

1. Wybierz moduł **Podziel dane.**

1. W okienku szczegółów modułu po prawej stronie kanwy ustaw **ułamek wierszy w pierwszym zestawie danych wyjściowych** na 0,7.

    Ta opcja dzieli 70 procent danych do szkolenia modelu i 30 procent do testowania go. Zestaw danych 70 procent będzie dostępny za pośrednictwem lewego portu wyjściowego. Pozostałe dane będą dostępne za pośrednictwem odpowiedniego portu wyjściowego.

1. W okienku szczegółów modułu po prawej stronie kanwy zaznacz pole **Komentarz** i wprowadź *podziel zestaw danych w zestaw szkoleniowy (0,7) i zestaw testów (0,3).*

### <a name="train-the-model"></a>Uczenie modelu

Trenuj model, nadając mu zestaw danych, który zawiera cenę. Algorytm tworzy model, który wyjaśnia relację między funkcjami i ceną przedstawioną przez dane szkoleniowe.

1. W palecie modułów rozwiń **algorytmy uczenia maszynowego**.
    
    Ta opcja wyświetla kilka kategorii modułów, których można użyć do zainicjowania algorytmów uczenia się.

1. Wybierz **regresję** > **liniową regresji regresji**i przeciągnij ją do kanwy potoku.

1. Połącz dane wyjściowe modułu **regresji liniowej** z lewym wejściem modułu **Model pociągu.**

1. W palecie modułów rozwiń sekcję **Szkolenie modułu**i przeciągnij moduł **Modelu pociągu** na kanwę.

1. Wybierz moduł **Model pociągu** i przeciągnij go do kanwy potoku.

1. Podłącz dane wyjściowe szkolenia (lewy port) modułu **Split Data** do prawego wejścia modułu **Model pociągu.**
    
    > [!IMPORTANT]
    > Upewnij się, że lewe porty wyjściowe **podzielonych danych** łączą się z **modelem pociągu**. Lewy port zawiera zestaw szkoleniowy. Prawy port zawiera zestaw testowy.

    ![Zrzut ekranu przedstawiający prawidłową konfigurację modułu Model pociągu. Moduł regresji liniowej łączy się z lewym portem modułu Model pociągu, a moduł Split Data łączy się z prawym portem modelu pociągu](./media/tutorial-designer-automobile-price-train-score/pipeline-train-model.png)

1. Wybierz moduł **Train Model** (Trenowanie modelu).

1. W okienku szczegółów modułu po prawej stronie obszaru roboczego wybierz pozycję Edytuj selektor **kolumn.**

1. W oknie dialogowym **Kolumna Etykieta** rozwiń menu rozwijane i wybierz pozycję **Nazwy kolumn**. 

1. W polu tekstowym wprowadź *cenę,* aby określić wartość, którą model będzie przewidywał.

    >[!IMPORTANT]
    > Upewnij się, że nazwa kolumny została dokładnie wprowadzona. Nie kapitalizuj **ceny**. 

    Potok powinien wyglądać następująco:

    ![Zrzut ekranu przedstawiający prawidłową konfigurację potoku po dodaniu modułu Modelu pociągu.](./media/tutorial-designer-automobile-price-train-score/pipeline-train-graph.png)

### <a name="add-the-score-model-module"></a>Dodawanie modułu Model wyników

Po przeszkoleniu modelu przy użyciu 70 procent danych, można go użyć do oceny innych 30 procent, aby zobaczyć, jak dobrze funkcjonuje model.

1. Wprowadź *model wyników* w polu wyszukiwania, aby znaleźć moduł Modelu **wyników.** Przeciągnij moduł do kanwy potoku. 

1. Połącz wyjście modułu **Train Model** (Uczenie modelu) z lewym portem wejściowym modułu **Score Model** (Generowanie wyników przez model). Połącz wyjście danych testowych (prawy port) modułu **Split Data** (Podział danych) z prawym portem wejściowym modułu **Score Model** (Generowanie wyników przez model).

### <a name="add-the-evaluate-model-module"></a>Dodawanie modułu Evaluate Model (Ewaluacja modelu)

Użyj **oceń model** modułu, aby ocenić, jak dobrze model ocenił zestaw danych testu.

1. Wprowadź *wartość oceny* w polu wyszukiwania, aby znaleźć moduł **Oceń model.** Przeciągnij moduł do kanwy potoku. 

1. Podłącz dane wyjściowe modułu **Score Model** do lewego wejścia **evaluate model**. 

    Końcowy potok powinien wyglądać mniej więcej tak:

    ![Zrzut ekranu przedstawiający prawidłową konfigurację potoku.](./media/tutorial-designer-automobile-price-train-score/pipeline-final-graph.png)

## <a name="submit-the-pipeline"></a>Prześlij potok

Teraz, gdy potok jest skonfigurowany, można przesłać przebieg potoku, aby nauczyć modelu uczenia maszynowego. Można przesłać prawidłowe uruchomienie potoku w dowolnym momencie, który może służyć do przeglądania zmian w potoku podczas tworzenia.

1. U góry obszaru roboczego wybierz pozycję **Prześlij**.

1. W oknie dialogowym **Konfigurowanie uruchamiania potoku** wybierz pozycję **Utwórz nowy**.

    > [!NOTE]
    > Grupa eksperymentów podobny potok przebiega razem. Jeśli potok jest uruchamiany wiele razy, można wybrać ten sam eksperyment dla kolejnych przebiegów.

    1. Wprowadź opisową nazwę **nazwy nowego eksperymentu**.

    1. Wybierz pozycję **Prześlij**.
    
    Stan uruchomienia i szczegóły można wyświetlić w prawym górnym rogu kanwy.
    
    Jeśli jest to pierwsze uruchomienie, może upłynąć do 20 minut, aby zakończyć uruchamianie potoku. Domyślne ustawienia obliczeniowe mają minimalny rozmiar węzła 0, co oznacza, że projektant musi przydzielić zasoby po bezczynności. Powtarzające się przebiegi potoku zajmie mniej czasu, ponieważ zasoby obliczeniowe są już przydzielone. Ponadto projektant używa buforowanych wyników dla każdego modułu, aby jeszcze bardziej zwiększyć wydajność.

### <a name="view-scored-labels"></a>Wyświetlanie etykiet z ocenami

Po zakończeniu uruchamiania można wyświetlić wyniki uruchomienia potoku. Najpierw przyjrzyj się prognozom generowanym przez model regresji.

1. Wybierz moduł **Score Model,** aby wyświetlić jego dane wyjściowe.

1. W okienku szczegółów modułu po prawej stronie obszaru roboczego wybierz pozycję ![Wyjścia +](./media/tutorial-designer-automobile-price-train-score/visualize-icon.png) **dzienniki** > ikona wykresu wizualizuj ikonę, aby wyświetlić wyniki.

    Tutaj możesz zobaczyć przewidywane ceny i rzeczywiste ceny z danych testowych.

    ![Zrzut ekranu przedstawiający wizualizację wyjściową wyróżniającą kolumnę Etykieta punktowa](./media/tutorial-designer-automobile-price-train-score/score-result.png)

### <a name="evaluate-models"></a>Ocenianie modeli

Użyj **Oceń model,** aby zobaczyć, jak dobrze przeszkolony model wykonywane na zestaw danych testu.

1. Wybierz moduł **Oceń model,** aby wyświetlić jego dane wyjściowe.

1. W okienku szczegółów modułu po prawej stronie obszaru roboczego wybierz pozycję ![Wyjścia +](./media/tutorial-designer-automobile-price-train-score/visualize-icon.png) **dzienniki** > ikona wykresu wizualizuj ikonę, aby wyświetlić wyniki.

Dla modelu wyświetlane są następujące statystyki:

* **Średni błąd bezwzględny (MAE)**: średnia błędów bezwzględnych. Błąd jest różnicą między przewidywaną wartością a wartością rzeczywistą.
* **Główny średni kwadratowy błąd (RMSE)**: Pierwiastek kwadratowy średniej kwadratowych błędów prognoz dokonanych w zestawie danych testowych.
* **Względny błąd absolutny**: iloraz średniej błędów absolutnych i bezwzględnej wartości różnicy między wartościami rzeczywistymi a średnią wszystkich wartości rzeczywistych.
* **Błąd względny średniokwadratowy**: iloraz średniej kwadratów błędów i kwadratu różnicy między wartościami rzeczywistymi a średnią wszystkich wartości rzeczywistych.
* **Współczynnik oznaczania:** Znany również jako R kwadratwarta wartość, ta metryka statystyczna wskazuje, jak dobrze model pasuje do danych.

W przypadku wszystkich powyższych statystyk mniejsze wartości oznaczają lepszą jakość modelu. Mniejsza wartość wskazuje, że prognozowania są bliżej wartości rzeczywistych. Dla współczynnika oznaczania im bliżej jego wartości jest jeden (1.0), tym lepsze prognozy.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Pomiń tę sekcję, jeśli chcesz kontynuować część 2 samouczka, [wdrażając modele](tutorial-designer-automobile-price-deploy.md).

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

W części drugiej dowiesz się, jak wdrożyć model jako punkt końcowy w czasie rzeczywistym.

> [!div class="nextstepaction"]
> [Kontynuuj wdrażanie modeli](tutorial-designer-automobile-price-deploy.md)
