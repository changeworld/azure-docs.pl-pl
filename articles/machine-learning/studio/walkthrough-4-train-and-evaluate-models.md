---
title: 'Krok 4: Szkolenie modeli do analizy predykcyjnej'
titleSuffix: Azure Machine Learning Studio
description: 'Krok 4 programowanie przewodnik rozwiązania do analizy predykcyjnej: Szkolenie, ocena i ocenianie wielu modeli w usłudze Azure Machine Learning Studio.'
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: article
author: garyericson
ms.author: garye
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/23/2017
ms.openlocfilehash: 50b0a6276d34567651da2cb3e76f0686e8b10fea
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2019
ms.locfileid: "55511568"
---
# <a name="walkthrough-step-4-train-and-evaluate-the-predictive-analytic-models-in-machine-learning-studio"></a>Przewodnik, krok 4 Nauczanie i ocena modeli do analizy predykcyjnej w usłudze Machine Learning Studio
Ten temat zawiera czwartym kroku tego przewodnika, [tworzenia rozwiązania analizy predykcyjnej w usłudze Azure Machine Learning](walkthrough-develop-predictive-solution.md)

1. [Tworzenie obszaru roboczego usługi Machine Learning Studio](walkthrough-1-create-ml-workspace.md)
2. [Przekazywanie istniejących danych](walkthrough-2-upload-data.md)
3. [Tworzenie nowego eksperymentu](walkthrough-3-create-new-experiment.md)
4. **Nauczanie i ocena modeli**
5. [Wdrażanie usługi sieci Web](walkthrough-5-publish-web-service.md)
6. [Dostęp do usługi sieci Web](walkthrough-6-access-web-service.md)

- - -
Jedną z korzyści z używania usługi Azure Machine Learning Studio do tworzenia modeli uczenia maszynowego jest możliwość spróbuj więcej niż jeden typ modelu w czasie w eksperymencie i porównać wyniki. Tego rodzaju eksperymentowania ułatwia znajdowanie najlepszym rozwiązaniem dla danego problemu.

Eksperyment, który opracowujemy w tym przewodniku utworzymy dwa różne typy modeli i następnie porównaj ich wyniki oceny do określania, który algorytm chcemy do użycia w naszym eksperyment końcowy.  

Istnieją różne modele, które możemy wybrać. Aby wyświetlić dostępne modele, rozwiń węzeł **uczenia maszynowego** węzła w palety modułów, a następnie rozwiń węzeł **zainicjować modelu** i węzły znajdujące się poniżej. Do celów tego eksperymentu, wybierzemy [Two-Class pomocy technicznej Vector Machine] [ two-class-support-vector-machine] (SVM) i [Two-Class Boosted Decision drzewa] [ two-class-boosted-decision-tree] modułów.    

> [!TIP]
> Aby uzyskać pomoc przy wyborze rozwiązania, który algorytm uczenia maszynowego, najlepiej pasujące do konkretnych problemów, które próbujesz rozwiązać, zobacz [jak wybór algorytmów dla usługi Microsoft Azure Machine Learning Studio](algorithm-choice.md).
> 
> 

## <a name="train-the-models"></a>Szkolenie modeli

Dodamy zarówno [Two-Class Boosted Decision drzewa] [ two-class-boosted-decision-tree] modułu i [Two-Class pomocy technicznej Vector Machine] [ two-class-support-vector-machine] modułu, w tym eksperymencie.

### <a name="two-class-boosted-decision-tree"></a>Two-Class wzmocnione drzewo decyzyjnego

Najpierw skonfiguruj modelu drzewa decyzyjnego.

1. Znajdź [Two-Class Boosted Decision drzewa] [ two-class-boosted-decision-tree] modułu w palecie modułów i przeciągnij go na kanwie.

2. Znajdź [Train Model] [ train-model] modułu, przeciągnij go na kanwie, a następnie Połącz wyjście [Two-Class Boosted Decision drzewa] [ two-class-boosted-decision-tree] portem wejściowym modułu po lewej stronie [Train Model] [ train-model] modułu.
   
   [Two-Class Boosted Decision drzewa] [ two-class-boosted-decision-tree] modułu inicjuje model ogólny i [uczenie modelu] [ train-model] używa danych szkoleniowych do szkolenia model. 

3. Połącz po lewej Wyjście po lewej stronie [wykonanie skryptu języka R] [ execute-r-script] portem wejściowym modułu po prawej stronie [Train Model] [ train-model] (podjęliśmy decyzję o w module [Kroku 3](walkthrough-3-create-new-experiment.md) tego przewodnika na korzystanie z danych pochodzących z lewego boku modułu dzielenia danych szkoleń).
   
   > [!TIP]
   > Nie potrzebujemy dwóch danych wejściowych, a drugi dane wyjściowe [wykonanie skryptu języka R] [ execute-r-script] modułu, w tym eksperymencie, dzięki czemu firma Microsoft może pozostawić je niedołączone. 
   > 
   > 

Ta część eksperymentu teraz wygląda następująco:  

![Uczenie modelu][1]

Teraz należy przekazać [Train Model] [ train-model] modułu, że chcemy model do przewidywania wartości oceny ryzyka kredytowego.

1. Wybierz [Train Model] [ train-model] modułu. W **właściwości** okienku kliknij **uruchamianie selektora kolumn**.

2. W **wybrać pojedynczą kolumnę** okno dialogowe, wpisz "środki ryzyka" w polu wyszukiwania w obszarze **dostępnych kolumn**, a następnie wybierz pozycję "Środki ryzyka" poniżej i kliknij przycisk strzałki w prawo (**>**) aby przenieść "Ryzyka kredytowego" **wybrane kolumny**. 

    ![Wybierz kolumnę oceny ryzyka kredytowego modułu Train Model][0]

3. Kliknij przycisk **OK** znacznik wyboru.

### <a name="two-class-support-vector-machine"></a>Algorytm SVM dla problemu dwuklasowego

Następnie możemy skonfigurować SVM model.  

Pierwszy jest nieco wyjaśnienie SVM. Drzewa decyzyjnego dobrze współpracować z funkcjami dowolnego typu. Jednak ponieważ moduł SVM generuje liniowej klasyfikatora, modelu, który generuje podlega najlepsze błąd testu, gdy wszystkie funkcje numeryczne mają tę samą skalę. Aby przekonwertować wszystkie funkcje numeryczne tę samą skalę, używamy transformacji "Tanh" (z [normalizacji danych] [ normalize-data] modułu). Nasze cyfry to przekształca do zakresu [0,1]. Moduł SVM konwertuje ciąg funkcje kategorii funkcji, a następnie binarne funkcje 0/1, dzięki czemu nie trzeba ręcznie Przekształcanie ciągów funkcji. Ponadto nie chcemy przekształcić ryzyko kredytowe kolumny (21) — jest liczbowe, ale jest to wartość, firma Microsoft uczony model do przewidywania, dlatego musimy pozostawiony.  

Aby skonfigurować SVM model, wykonaj następujące czynności:

1. Znajdź [Two-Class pomocy technicznej Vector Machine] [ two-class-support-vector-machine] modułu w palecie modułów i przeciągnij go na kanwie.

2. Kliknij prawym przyciskiem myszy [Train Model] [ train-model] modułu, wybierz opcję **kopiowania**, a następnie kliknij prawym przyciskiem myszy kanwy, a następnie wybierz pozycję **Wklej**. Kopię [Train Model] [ train-model] moduł ma ten sam kolumn wybór co oryginalny.

3. Połącz wyjście [Two-Class pomocy technicznej Vector Machine] [ two-class-support-vector-machine] drugi portem wejściowym modułu modułu po lewej stronie [Train Model] [ train-model] modułu.

4. Znajdź [normalizacji danych] [ normalize-data] modułu i przeciągnij go na kanwie.

5. Połącz po lewej Wyjście po lewej stronie [wykonanie skryptu języka R] [ execute-r-script] modułu w danych wejściowych w tym module (Zwróć uwagę, że port wyjściowy modułu mogą być połączone z więcej niż jeden inny moduł).

6. Połącz lewy port wyjściowy [normalizacji danych] [ normalize-data] modułu po prawej stronie wprowadź port drugiego [Train Model] [ train-model] modułu.

Ta część nasz eksperyment powinien teraz wyglądać mniej więcej tak:  

![Szkolenie w drugim modelu][2]  

Teraz skonfigurować [normalizacji danych] [ normalize-data] modułu:

1. Kliknij, aby wybrać [normalizacji danych] [ normalize-data] modułu. W **właściwości** okienku wybierz **Tanh** dla **metoda przekształcania** parametru.

2. Kliknij przycisk **uruchamianie selektora kolumn**, wybierz opcję "Nie kolumny" dla **rozpocząć z**, wybierz opcję **Include** z pierwszej listy rozwijanej wybierz **typ kolumny** w drugiej listy rozwijanej i wybierz pozycję **liczbowych** na trzeciej liście rozwijanej. To ustawienie określa wszystkie kolumny liczbowe (i tylko liczbowe) są przekształcane.

3. Kliknij znak plus (+) z prawej strony tego wiersza — spowoduje to utworzenie wiersza list rozwijanych. Wybierz **wykluczyć** z pierwszej listy rozwijanej wybierz **nazwy kolumn** na drugiej liście rozwijanej, a następnie wprowadź "Ryzyko kredytowe" w polu tekstowym. Określa, że ryzyko kredytowe kolumny mają być ignorowane (należy to zrobić, ponieważ ta kolumna jest wartością liczbową, a zatem będą przekształcane Jeśli firma Microsoft nie go wykluczyć).

4. Kliknij przycisk **OK** znacznik wyboru.  

    ![Wybierz kolumny dla modułu normalizacji danych][5]

[Normalizacji danych] [ normalize-data] moduł są teraz ustawione przekształcenie Tanh dla wszystkich kolumn liczbowych, z wyjątkiem kolumny oceny ryzyka kredytowego.  

## <a name="score-and-evaluate-the-models"></a>Ocena i Ewaluacja modeli

Używamy danych testowych, które zostało rozdzielone się [podziału danych] [ split] modułu, aby oceniać nasze przeszkolone modele. Firma Microsoft jest następnie porównaj wyniki z dwóch modeli, aby zobaczyć, których generowany w poszukiwaniu lepszych wyników.  

### <a name="add-the-score-model-modules"></a>Dodaj moduły Score Model

1. Znajdź [Score Model] [ score-model] modułu i przeciągnij go na kanwie.

2. Połącz [Train Model] [ train-model] moduł, który jest podłączony do [Two-Class Boosted Decision drzewa] [ two-class-boosted-decision-tree] portem wejściowym modułu z lewej strony [Score Model] [ score-model] modułu.

3. Połącz po prawej stronie [wykonanie skryptu języka R] [ execute-r-script] portem wejściowym modułu (naszych danych testowych) po prawej stronie [Score Model] [ score-model] modułu.

    ![Moduł score Model połączone][6]
   
   [Score Model] [ score-model] modułu można teraz podjąć środki informacje z danych testowych, uruchomić go za pomocą modelu i porównywania przewidywań modelu generuje z kolumną o podwyższonym ryzyku rzeczywiste środków w Testowanie danych.

4. Skopiuj i Wklej [Score Model] [ score-model] moduł służący do tworzenia drugiej kopii.

5. Połącz wyjście modelu SVM (oznacza to, że port wyjściowy [Train Model] [ train-model] moduł, który jest podłączony do [Two-Class pomocy technicznej Vector Machine] [ two-class-support-vector-machine] moduł) z portem wejściowym drugiego [Score Model] [ score-model] modułu.

6. W przypadku modelu SVM musimy to zrobić przekształcenia danych testowych, ile My mieliśmy do danych treningowych. Dlatego skopiuj i Wklej [normalizacji danych] [ normalize-data] modułu, aby utworzyć drugą kopię i połączyć ją z prawej strony [wykonanie skryptu języka R] [ execute-r-script] modułu.

7. Połącz po lewej wyjście drugi [normalizacji danych] [ normalize-data] modułu po prawej stronie wprowadź port drugiego [Score Model] [ score-model] modułu.

    ![Oba moduły Score Model połączone][7]

### <a name="add-the-evaluate-model-module"></a>Dodawanie modułu Evaluate Model

Aby obliczyć dwa wyniki oceniania i porównaj je, użyjemy [Evaluate Model] [ evaluate-model] modułu.  

1. Znajdź [Evaluate Model] [ evaluate-model] modułu i przeciągnij go na kanwie.

2. Połącz port wyjściowy [Score Model] [ score-model] skojarzonego z tym modelem drzewa decyzyjnego do lewym portem wejściowym modułu [Evaluate Model] [ evaluate-model] modułu.

3. Połącz drugi [Score Model] [ score-model] portem wejściowym modułu po prawej stronie.  

    ![Ocena modelu modułu połączone][8]

### <a name="run-the-experiment-and-check-the-results"></a>Uruchom eksperyment, aby sprawdzić wyniki

Aby uruchomić eksperyment, kliknij **Uruchom** znajdujący się poniżej obszaru roboczego. Może to potrwać kilka minut. Wskaźnik rotowania na każdy moduł pokazuje, że jest uruchomiona, a następnie zielony znacznik wyboru wskazuje, po zakończeniu modułu. Jeśli zaznaczono wszystkie moduły zakończeniu eksperymentu.

Eksperyment powinien teraz wyglądać mniej więcej tak:  

![Dokonanie oceny oprogramowania obu modeli][3]

Aby sprawdzić wyniki, kliknij port wyjściowy [Evaluate Model] [ evaluate-model] modułu, a następnie wybierz pozycję **Visualize**.  

[Evaluate Model] [ evaluate-model] moduł generuje parę krzywe i metryki, które pozwalają porównać wyniki z dwóch modeli ocenami. Można wyświetlić wyniki w postaci krzywych odbiorcy cechy — Operator (ROC), krzywych dokładności/odwołania lub krzywych przyrostu. Dodatkowe dane obejmuje macierz pomyłek, łączne wartości dla powierzchni pod krzywą (AUC) i innych metryk. Można zmienić wartość progowa, przesuwając suwak w lewo lub w prawo i zobacz, jak wpływa na zestaw metryk.  

Z prawej strony wykresu kliknij **oceniane dataset** lub **oceniane zestawu danych, aby porównać** do wyróżnienia krzywej skojarzone oraz do wyświetlania skojarzonych z nimi metryk poniżej. W legendzie dla krzywych "Oceniane zestawu danych" odnosi się do lewy port wejściowy z [Evaluate Model] [ evaluate-model] moduł — w naszym przypadku jest to modelu drzewa decyzyjnego. "Oceniane zestawu danych, aby porównać" odnosi się do prawy port wejściowy — model SVM w naszym przypadku. Po kliknięciu jednego z tych etykiet, jest wyróżniona krzywej dla tego modelu i odpowiednie metryki są wyświetlane, jak pokazano na poniższym rysunku.  

![Krzywe ROC dla modeli][4]

Badanie tych wartości, można zdecydować, model, który znajduje się najbliżej daje rezultatów, którego szukasz. Możesz wrócić i wykonywanie iteracji eksperymentu, zmieniając wartości parametrów w różnych modelach. 

Nauki i sztukę interpretowanie tych wynikach i dostrajanie wydajności modelu znajduje się poza zakresem tego przewodnika. Aby uzyskać dodatkową pomoc może przeczytać następujące artykuły:
- [Ocenianie wydajności modelu w usłudze Azure Machine Learning](evaluate-model-performance.md)
- [Wybór parametrów w celu optymalizacji algorytmów w usłudze Azure Machine Learning](algorithm-parameters-optimize.md)
- [Interpretacja wyników modelu w usłudze Azure Machine Learning](interpret-model-results.md)

> [!TIP]
> Każdym uruchomieniu eksperymentu rekord tej iteracji są przechowywane w historii uruchamiania. Można wyświetlić te iteracje i powrócić do żadnego z nich, klikając **widok HISTORII URUCHAMIANIA** poniżej obszaru roboczego. Możesz również kliknąć **wcześniejszego uruchomienia** w **właściwości** okienko, aby wrócić do iteracji, bezpośrednio poprzedzających jeden mają otwarte.
> 
> Można utworzyć kopię dowolną iterację eksperymentu, klikając **SAVE AS** poniżej obszaru roboczego. 
> Użyj eksperymentu **Podsumowanie** i **opis** właściwości, aby rejestrować co sprawdzone w iterację eksperymentu.
> 
> Aby uzyskać więcej informacji, zobacz [Zarządzanie iteracjami eksperymentów w usłudze Azure Machine Learning Studio](manage-experiment-iterations.md).  
> 
> 

- - -
**Dalej: [Wdrażanie usługi sieci web](walkthrough-5-publish-web-service.md)**

[0]: ./media/walkthrough-4-train-and-evaluate-models/train-model-select-column.png
[1]: ./media/walkthrough-4-train-and-evaluate-models/experiment-with-train-model.png
[2]: ./media/walkthrough-4-train-and-evaluate-models/svm-model-added.png
[3]: ./media/walkthrough-4-train-and-evaluate-models/final-experiment.png
[4]: ./media/walkthrough-4-train-and-evaluate-models/roc-curves.png
[5]: ./media/walkthrough-4-train-and-evaluate-models/normalize-data-select-column.png
[6]: ./media/walkthrough-4-train-and-evaluate-models/score-model-connected.png
[7]: ./media/walkthrough-4-train-and-evaluate-models/both-score-models-added.png
[8]: ./media/walkthrough-4-train-and-evaluate-models/evaluate-model-added.png


<!-- Module References -->
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[normalize-data]: https://msdn.microsoft.com/library/azure/986df333-6748-4b85-923d-871df70d6aaf/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[two-class-boosted-decision-tree]: https://msdn.microsoft.com/library/azure/e3c522f8-53d9-4829-8ea4-5c6a6b75330c/
[two-class-support-vector-machine]: https://msdn.microsoft.com/library/azure/12d8479b-74b4-4e67-b8de-d32867380e20/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
