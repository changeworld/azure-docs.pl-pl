---
title: 'Samouczek 2: uczenie modeli ryzyka kredytowego'
titleSuffix: ML Studio (classic) - Azure
description: Szczegółowy Samouczek przedstawiający sposób tworzenia rozwiązania do analizy predykcyjnej w celu oceny ryzyka kredytowego w Azure Machine Learning Studio (klasyczny). Ten samouczek jest drugą częścią trzyczęściowej serii samouczków. Przedstawia on trenowanie i ewaluację modeli.
keywords: ryzyko kredytowe, rozwiązanie analizy predykcyjnej, ocena ryzyka
author: sdgilley
ms.author: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: tutorial
ms.date: 02/11/2019
ms.openlocfilehash: 9aeaed6565e69409bc55a71985296b2393226ba4
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75427482"
---
# <a name="tutorial-2-train-credit-risk-models---azure-machine-learning-studio-classic"></a>Samouczek 2: uczenie modeli ryzyka kredytowego — Azure Machine Learning Studio (klasyczny)

W tym samouczku szczegółowo przedstawiono proces opracowywania rozwiązania analizy predykcyjnej. Tworzysz prosty model w Machine Learning Studio (klasyczny).  Następnie wdrożysz model jako usługę internetową w ramach usługi Azure Machine Learning.  Wdrożony model może tworzyć przewidywania przy użyciu nowych danych. Ten samouczek jest **drugą częścią trzyczęściowej serii samouczków**.

Załóżmy, że chcesz przewidzieć ryzyko kredytowe osoby na podstawie informacji przekazanych we wniosku kredytowym.  

Ocena ryzyka kredytowego to złożony problem, ale w tym samouczku zostanie on nieco uproszczony. Będziesz jej używać jako przykładu tworzenia rozwiązania do analizy predykcyjnej przy użyciu Microsoft Azure Machine Learning Studio (klasyczne). Dla tego rozwiązania będziesz używać Azure Machine Learning Studio (klasyczny) i Machine Learning usługi sieci Web.  

W tym trzyczęściowym samouczku zaczniesz od publicznie dostępnych danych ryzyka kredytowego.  Następnie wdrożysz i wytrenujesz model predykcyjny.  Na koniec wdrożysz model jako usługę internetową.

W [pierwszej części samouczka](tutorial-part1-credit-risk.md)utworzono obszar roboczy Machine Learning Studio (klasyczny), przekazane dane i utworzono eksperyment.

W tej części samouczka zostaną wykonane następujące czynności:
 
> [!div class="checklist"]
> * Trenowanie wielu modeli
> * Generowanie wyników dla modeli i ich ewaluacja


W [trzeciej części samouczka](tutorial-part3-credit-risk-deploy.md) wdrożysz model jako usługę internetową.

## <a name="prerequisites"></a>Wymagania wstępne

Wykonanie [pierwszej części samouczka](tutorial-part1-credit-risk.md).

## <a name="train"></a>Trenowanie wielu modeli

Jedną z zalet używania Azure Machine Learning Studio (klasycznego) do tworzenia modeli uczenia maszynowego jest możliwość wypróbowania więcej niż jednego typu modelu jednocześnie w jednym doświadczeniu i porównać wyniki. Taki eksperyment ułatwia znalezienie najlepszego rozwiązania problemu.

W ramach eksperymentu opracowywanego w tym samouczku utworzysz dwa różne typy modelu i porównasz wyniki ich oceny, aby określić, który algorytm ma zostać użyty w końcowym eksperymencie.  

Masz do wyboru różne modele. Aby wyświetlić dostępne modele, rozwiń węzeł **Machine Learning** na palecie modułów, a następnie rozwiń węzeł **Initialize Model** (Inicjuj model) i węzły znajdujące się poniżej. Na potrzeby tego eksperymentu należy wybrać [dwuklasową maszynę z obsługą wektorową][two-class-support-vector-machine] (SVM) i moduły [drzewa podwyższenie poziomu decyzyjne dla dwóch klas][two-class-boosted-decision-tree] .

> [!TIP]
> Aby uzyskać pomoc w wyborze, który algorytm Machine Learning najlepiej odpowiada konkretnemu problemowi, który próbujesz rozwiązać, zobacz [jak wybrać algorytmy dla Microsoft Azure Machine Learning Studio (klasyczne)](algorithm-choice.md).
> 
> 

Do tego eksperymentu zostanie dodany zarówno moduł [drzewa decyzyjnej podwyższej klasy][two-class-boosted-decision-tree] , jak i [dwuklasowy moduł maszyny wektorowej][two-class-support-vector-machine] .

### <a name="two-class-boosted-decision-tree"></a>Two-Class Boosted Decision Tree (Dwuklasowe wzmocnione drzewo decyzyjne)

Najpierw skonfiguruj model wzmocnionego drzewa decyzyjnego.

1. Znajdź moduł [drzewa decyzyjnej z dwoma klasami][two-class-boosted-decision-tree] w palecie modułów i przeciągnij go na kanwę.

1. Znajdź moduł [uczenie modelu][train-model] , przeciągnij go na kanwę, a następnie Połącz dane wyjściowe [dwuklasowego modułu drzewa decyzyjnego][two-class-boosted-decision-tree] z lewym portem wejściowym modułu [uczenie modelu][train-model] .
   
   Moduł [drzewa podwyższej decyzji z dwoma klasami][two-class-boosted-decision-tree] inicjuje model ogólny i [pouczenie modelu][train-model] używa danych szkoleniowych do uczenia modelu. 

1. Połącz lewe dane wyjściowe modułu [skryptu Execute R][execute-r-script] z właściwym portem wejściowym modułu [uczenie modelu][train-model] (w tym samouczku [użyto danych pochodzących z lewej strony](#train) modułu Split Data) do szkolenia).
   
   > [!TIP]
   > nie są potrzebne dwa dane wejściowe i jeden z wyjść modułu [skryptu języka R][execute-r-script] dla tego eksperymentu, dlatego można pozostawić je niedołączone. 
   > 
   > 

Ta część eksperymentu teraz wygląda mniej więcej tak:  

![Trenowanie modelu](./media/tutorial-part2-credit-risk-train/experiment-with-train-model.png)

Teraz musisz poinformować moduł [modelu uczenia][train-model] , że model ma przewidywać wartość ryzyka kredytowego.

1. Wybierz moduł [uczenie modelu][train-model] . W okienku **Properties** (Właściwości) kliknij pozycję **Launch column selector** (Uruchom selektor kolumn).

1. W oknie dialogowym **Select a single column** (Wybieranie pojedynczej kolumny) wpisz „credit risk” („ryzyko kredytowe”) w polu wyszukiwania w obszarze **Available Columns** (Dostępne kolumny), wybierz pozycję „Credit risk” („Ryzyko kredytowe”) poniżej i kliknij przycisk strzałki w prawo ( **>** ) aby przenieść pozycję „Credit risk” („Ryzyko kredytowe”) do pola **Selected Columns** (Wybrane kolumny). 

    ![Wybieranie kolumny Credit risk (Ryzyko kredytowe) dla modułu Train Model (Trenowanie modelu)](./media/tutorial-part2-credit-risk-train/train-model-select-column.png)

1. Kliknij znacznik wyboru **OK**.

### <a name="two-class-support-vector-machine"></a>Algorytm SVM dla problemu dwuklasowego

Następnie skonfigurujesz model SVM.  

Najpierw krótko objaśnimy model SVM. Wzmocnione drzewa decyzyjne dobrze współpracują z cechami dowolnego typu. Jednak ponieważ moduł SVM generuje klasyfikator liniowy, wygenerowany przez niego model ma najlepszy błąd testu, gdy wszystkie cechy liczbowe mają tę samą skalę. Aby skonwertować wszystkie funkcje liczbowe na tę samą skalę, należy użyć "tanh" transformacji (z modułem [normalizacji danych][normalize-data] ). W ten sposób liczby zostaną przetransformowane do zakresu [0,1]. Moduł SVM konwertuje cechy ciągu na cechy kategorialne, a następnie na binarne cechy 0/1, więc nie musisz ręcznie transformować cech ciągu. Ponadto nie należy transformować kolumny 21 Credit Risk (Ryzyko kredytowe) — jest to kolumna liczbowa, lecz zawiera także wartość, pod kątem przewidywania której moduł jest trenowany, dlatego należy pozostawić ją bez zmian.  

Aby skonfigurować model SVM, wykonaj następujące działania:

1. Znajdź moduł [maszyny wektorowej obsługujący dwie klasy][two-class-support-vector-machine] w palecie modułów i przeciągnij ją na kanwę.

1. Kliknij prawym przyciskiem myszy moduł [uczenie modelu][train-model] , wybierz pozycję **Kopiuj**, a następnie kliknij prawym przyciskiem myszy kanwę i wybierz polecenie **Wklej**. Kopia modułu [uczenie modelu][train-model] ma taką samą kolumnę jak oryginalna.

1. Połącz dane wyjściowe modułu [maszyny wektorowej z obsługą dwóch klas][two-class-support-vector-machine] do lewego portu wejściowego modułu [modelu uczenia][train-model] .

1. Znajdź moduł [normalizowanie danych][normalize-data] i przeciągnij go na kanwę.

1. Połącz lewe dane wyjściowe modułu [uruchamiania języka R][execute-r-script] w lewo z danymi wejściowymi tego modułu (należy zauważyć, że port wyjściowy modułu może być połączony z więcej niż jednym innym modułem).

1. Połącz lewy port wyjściowy modułu [normalizowanie danych][normalize-data] z właściwym portem wejściowym modułu drugiego [uczenia modelu][train-model] .

Ta część eksperymentu powinna teraz wyglądać mniej więcej tak:  

![Trenowanie drugiego modelu](./media/tutorial-part2-credit-risk-train/svm-model-added.png)

Teraz Skonfiguruj moduł [normalizacji danych][normalize-data] :

1. Kliknij, aby wybrać moduł [normalizowanie danych][normalize-data] . W okienku **Properties** (Właściwości) wybierz wartość **Tanh** dla parametru **Transformation method** (Metoda transformacji).

1. Kliknij pozycję **Launch column selector** (Uruchom selektor kolumn), wybierz opcję „No columns” („Bez kolumn”) dla pozycji **Begin With** (Rozpocznij z), wybierz pozycję **Include** (Dołącz) na pierwszej liście rozwijanej, wybierz pozycję **column type** (typ kolumny) na drugiej liście rozwijanej i pozycję **Numeric** (Liczbowa) na trzeciej liście rozwijanej. W ten sposób określono, że wszystkie kolumny liczbowe (i tylko liczbowe) są przekształcane.

1. Kliknij znak plus (+) z prawej strony tego wiersza — spowoduje to utworzenie wiersza list rozwijanych. Wybierz pozycję **Exclude** (Wyklucz) na pierwszej liście rozwijanej, wybierz pozycję **column names** (nazwy kolumn) na drugiej liście rozwijanej, a następnie wpisz „Credit risk” („Ryzyko kredytowe”) w polu tekstowym. W ten sposób określono, że kolumna Credit Risk (Ryzyko kredytowe) ma być ignorowana (jest to konieczne, ponieważ jest to kolumna liczbowa i byłaby przekształcana, jeśli nie zostałaby wykluczona).

1. Kliknij znacznik wyboru **OK**.  

    ![Wybieranie kolumn dla modułu Normalize Data (Normalizacja danych)](./media/tutorial-part2-credit-risk-train/normalize-data-select-column.png)


Moduł [normalizacji danych][normalize-data] jest teraz skonfigurowany do przeprowadzania przekształceń tanh dla wszystkich kolumn liczbowych z wyjątkiem kolumny ryzyka kredytowego.  

## <a name="score-and-evaluate-the-models"></a>Generowanie wyników dla modeli i ich ewaluacja

dane testowe, które zostały rozdzielone przez moduł [Split Data][split] , służą do oceny naszych przeszkolonych modeli. Następnie można porównać wyniki dwóch modeli, aby zobaczyć, który wygenerował lepsze wyniki.  

### <a name="add-the-score-model-modules"></a>Dodawanie modułów Score Model (Generowanie wyników dla modelu)

1. Znajdź moduł [model oceny][score-model] i przeciągnij go na kanwę.

1. Połącz moduł [uczenia modelu][train-model] , który jest połączony z [dwuklasowym modułem drzewa decyzyjnego][two-class-boosted-decision-tree] do lewego portu wejściowego modułu [wyniku][score-model] .

1. Połącz właściwy moduł [skryptu języka R][execute-r-script] (nasze dane testowe) z prawidłowym portem wejściowym modułu [wyniku][score-model] .

    ![Połączony moduł Score Model (Generowanie wyników dla modelu)](./media/tutorial-part2-credit-risk-train/score-model-connected.png)

   
   Moduł [model oceny][score-model] może teraz przyjmować informacje o kredycie z danych testowych, uruchamiać je za pośrednictwem modelu i porównywać przewidywania generowane przez model z rzeczywistą kolumną ryzyka kredytowego w danych testowych.

1. Skopiuj i wklej moduł [model oceny][score-model] , aby utworzyć drugą kopię.

1. Połącz dane wyjściowe modelu SVM (czyli port wyjściowy modułu [uczenia modelu][train-model] , który jest podłączony do [dwuklasowego modułu wektorowego obsługi][two-class-support-vector-machine] ), do portu wejściowego drugiego modułu [modelu wynikowego][score-model] .

1. W przypadku modelu SVM należy wykonać tę samą transformację danych testowych co w przypadku danych treningowych. W związku z tym skopiuj i wklej moduł [normalizowanie danych][normalize-data] , aby utworzyć drugą kopię i połączyć ją z odpowiednim modułem [uruchamiania skryptów języka R][execute-r-script] .

1. Połącz lewe dane wyjściowe drugiego modułu [normalizacji danych][normalize-data] z właściwym portem wejściowym drugiego modułu [modelu wyniku][score-model] .

    ![Połączone oba moduły Score Model (Generowanie wyników dla modelu)](./media/tutorial-part2-credit-risk-train/both-score-models-added.png)


### <a name="add-the-evaluate-model-module"></a>Dodawanie modułu Evaluate Model (Ewaluacja modelu)

Aby oszacować dwa wyniki oceniania i porównać je, należy użyć modułu [oceny modelu][evaluate-model] .  

1. Znajdź moduł [Oceń model][evaluate-model] i przeciągnij go na kanwę.

1. Połącz port wyjściowy modułu [punktacji modelu][score-model] z podwyższanym modelem drzewa decyzyjnego z lewym portem wejściowym modułu [oceny modelu][evaluate-model] .

1. Połącz inny moduł [modelu oceny][score-model] z właściwym portem wejściowym.  

    ![Połączony moduł Evaluate Model (Ewaluacja modelu)](./media/tutorial-part2-credit-risk-train/evaluate-model-added.png)


### <a name="run-the-experiment-and-check-the-results"></a>Uruchomienie eksperymentu i sprawdzenie wyników

Aby uruchomić eksperyment, kliknij przycisk **RUN** (URUCHOM) poniżej kanwy. Może to potrwać kilka minut. Obracający się wskaźnik na każdym module oznacza, że moduł działa, a następnie zielony znacznik wyboru oznacza, że moduł zakończył pracę. Gdy wszystkie moduły mają zielony znacznik wyboru, oznacza to, że działanie eksperymentu zostało zakończone.

Eksperyment powinien wyglądać teraz mniej więcej tak:  

![Ewaluacja obu modeli](./media/tutorial-part2-credit-risk-train/final-experiment.png)


Aby sprawdzić wyniki, kliknij port wyjściowy modułu [Oceń model][evaluate-model] i wybierz opcję **Wizualizuj**.  

Moduł [Oceń model][evaluate-model] tworzy parę krzywych i metryk, które umożliwiają porównanie wyników dwóch modeli z wynikami. Wyniki można wyświetlić w postaci krzywych operacyjno-charakterystycznych, Dokładność/Czułość lub Wzrost. Dodatkowe wyświetlane dane obejmują macierz błędów, skumulowane wartości dla obszaru pod krzywą i inne metryki. Wartość progu można zmienić, przesuwając suwak w lewo lub w prawo i obserwując, jak wpływa to na zestaw metryk.  

Po prawej stronie wykresu kliknij pozycję **Scored dataset** (Oceniony zestaw danych) lub **Scored dataset to compare** (Oceniony zestaw danych do porównania), aby wyróżnić skojarzoną krzywą i wyświetlić skojarzone metryki poniżej. W legendzie dla krzywych "wynikowy zestaw danych" odnosi się do lewego portu wejściowego modułu [oceny modelu][evaluate-model] — w naszym przypadku jest to bardziej rozmieszczony Model drzewa decyzyjnego. Pozycja „Scored dataset to compare” („Oceniony zestaw danych do porównania”) odnosi się do prawego portu wejściowego — w naszym przypadku jest to model SVM. Po kliknięciu jednej z tych etykiet krzywa dla danego modelu zostanie wyróżniona, a odpowiadające metryki zostaną wyświetlone, jak pokazano na poniższym rysunku.  

![Krzywe operacyjno-charakterystyczne dla modeli](./media/tutorial-part2-credit-risk-train/roc-curves.png)

Badając te wartości, możesz zdecydować, który model daje wyniki najbardziej zbliżone do wyników, których szukasz. Możesz wrócić do eksperymentu i powtórzyć go, zmieniając wartości parametrów w różnych modelach. 

Nauka i sztuka interpretowania tych wyników i strojenie wydajności modelu wykracza poza zakres tego samouczka. Aby uzyskać dodatkową pomoc, możesz przeczytać następujące artykuły:
- [Jak oszacować wydajność modelu w Azure Machine Learning Studio (klasyczny)](evaluate-model-performance.md)
- [Wybierz parametry, aby zoptymalizować algorytmy w Azure Machine Learning Studio (klasyczny)](algorithm-parameters-optimize.md)
- [Interpretowanie wyników modelu w Azure Machine Learning Studio (klasyczny)](interpret-model-results.md)

> [!TIP]
> Każde uruchomienie eksperymentu powoduje zapisanie rekordu iteracji w historii uruchamiania. Te iteracje możesz wyświetlić i wrócić do dowolnej z nich, klikając pozycję **VIEW RUN HISTORY** (WYŚWIETL HISTORIĘ URUCHAMIANIA) poniżej kanwy. Możesz także kliknąć pozycję **Prior Run** (Poprzednie uruchomienie) w okienku **Properties** (Właściwości), aby wrócić do iteracji bezpośrednio poprzedzającej obecnie otwartą iterację.
> 
> Kopię dowolnej iteracji eksperymentu można utworzyć, klikając pozycję **SAVE AS** (ZAPISZ JAKO) poniżej kanwy. 
> Użyj właściwości **Summary** (Podsumowanie) i **Description** (Opis) eksperymentu, aby zanotować własne informacje dotyczące celów iteracji eksperymentu.
> 
> Aby uzyskać więcej informacji, zobacz [Zarządzanie iteracjami eksperymentów w Azure Machine Learning Studio (klasyczny)](manage-experiment-iterations.md).  
> 
> 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [machine-learning-studio-clean-up](../../../includes/machine-learning-studio-clean-up.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku zostały wykonane następujące kroki: 
 
> [!div class="checklist"]
> * Tworzenie eksperymentu
> * Trenowanie wielu modeli
> * Generowanie wyników dla modeli i ich ewaluacja

Teraz możesz przystąpić do wdrażania modeli dla danych.

> [!div class="nextstepaction"]
> [Samouczek 3 — wdrażanie modeli](tutorial-part3-credit-risk-deploy.md)


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[normalize-data]: https://msdn.microsoft.com/library/azure/986df333-6748-4b85-923d-871df70d6aaf/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[two-class-boosted-decision-tree]: https://msdn.microsoft.com/library/azure/e3c522f8-53d9-4829-8ea4-5c6a6b75330c/
[two-class-support-vector-machine]: https://msdn.microsoft.com/library/azure/12d8479b-74b4-4e67-b8de-d32867380e20/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/