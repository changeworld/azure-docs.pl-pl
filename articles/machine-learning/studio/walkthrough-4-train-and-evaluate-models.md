---
title: 'Krok 4: Nauczanie i Ewaluacja modeli predykcyjnych analityczne | Dokumentacja firmy Microsoft'
description: 'Krok 4 opracowanie wskazówki rozwiązanie predykcyjne: pociągu, wynik i ocena wielu modeli w usłudze Azure Machine Learning Studio.'
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: d905f6b3-9201-4117-b769-5f9ed5ee1cac
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2017
ms.openlocfilehash: 95937ee74be3f6facbc482c3cfd80a8af9128ea3
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34836017"
---
# <a name="walkthrough-step-4-train-and-evaluate-the-predictive-analytic-models"></a>Przewodnik, krok 4. Uczenie i ocenianie modeli do analizy predykcyjnej
Ten temat zawiera wskazówki, czwarty krok [tworzenie rozwiązania analizy predykcyjnej w usłudze Azure Machine Learning](walkthrough-develop-predictive-solution.md)

1. [Tworzenie obszaru roboczego usługi Machine Learning](walkthrough-1-create-ml-workspace.md)
2. [Przekazywanie istniejących danych](walkthrough-2-upload-data.md)
3. [Tworzenie nowego eksperymentu](walkthrough-3-create-new-experiment.md)
4. **Nauczanie i ocena modeli**
5. [Wdrażanie usługi sieci Web](walkthrough-5-publish-web-service.md)
6. [Dostęp do usługi sieci Web](walkthrough-6-access-web-service.md)

- - -
Jedną z zalet przy użyciu usługi Azure Machine Learning Studio do tworzenia modeli uczenia maszyny jest możliwość spróbuj więcej niż jeden typ modelu jednocześnie w jednym eksperymentu, a następnie porównaj wyniki. Ten typ eksperymenty ułatwia znalezienie najlepszego rozwiązania dla danego problemu.

W eksperymencie, które firma Microsoft tworzony jest w tym przewodniku możemy utworzyć dwa rodzaje modeli i porównać ich wyników oceniania zdecydować, który algorytm możemy mają być używane w naszych końcowy eksperyment.  

Istnieją różne modele, firma Microsoft może wyboru. Aby wyświetlić dostępne modele, rozwiń węzeł **uczenia maszynowego** węzła na palecie modułów, a następnie rozwiń węzeł **zainicjować modelu** i węzły znajdujące się poniżej. Na potrzeby tego eksperymentu wybierzemy [maszyny wektorowa obsługa Two-Class] [ two-class-support-vector-machine] (SVM) i [Two-Class Boosted drzewa decyzyjnego] [ two-class-boosted-decision-tree] modułów.    

> [!TIP]
> Aby uzyskać dodatkową pomoc przy wyborze, który algorytm uczenia maszynowego najlepiej pasujące do określonej problem próbuje rozwiązać, zobacz [Wybieranie algorytmów w Microsoft Azure Machine Learning](algorithm-choice.md).
> 
> 

## <a name="train-the-models"></a>Modele uczenia

Dodamy zarówno [Two-Class Boosted drzewa decyzyjnego] [ two-class-boosted-decision-tree] modułu i [maszyny wektorowa obsługa Two-Class] [ two-class-support-vector-machine] modułu, w tym eksperymencie.

### <a name="two-class-boosted-decision-tree"></a>Two-Class Boosted algorytm

Najpierw skonfiguruj boosted decyzji drzewa modelu.

1. Znajdź [Two-Class Boosted drzewa decyzyjnego] [ two-class-boosted-decision-tree] modułu na palecie modułów i przeciągnij go do obszaru roboczego.

2. Znajdź [Train Model] [ train-model] modułu, przeciągnij go do obszaru roboczego, a następnie połącz dane wyjściowe [Two-Class Boosted drzewa decyzyjnego] [ two-class-boosted-decision-tree] port wejściowy modułu po lewej stronie [Train Model] [ train-model] modułu.
   
   [Two-Class Boosted drzewa decyzyjnego] [ two-class-boosted-decision-tree] modułu inicjuje rodzajowy modelu i [Train Model] [ train-model] używa danych szkoleniowych do uczenia model. 

3. Połącz dane wyjściowe po lewej stronie po lewej stronie [wykonanie skryptu języka R] [ execute-r-script] modułu po prawej stronie wprowadź port [Train Model] [ train-model] (zdecydowaliśmy w module [Kroku 3](walkthrough-3-create-new-experiment.md) tego przewodnika to użycie danych pochodzących z lewej strony modułu podziału danych szkoleń).
   
   > [!TIP]
   > Firma Microsoft nie ma potrzeby dwóch danych wejściowych, a drugi dane wyjściowe [wykonanie skryptu języka R] [ execute-r-script] modułu do tego eksperymentu, dlatego firma Microsoft może narazić je odłączyć. 
   > 
   > 

Ta część eksperyment teraz wygląda następująco:  

![Uczenie modelu][1]

Teraz musimy sprawdzić [Train Model] [ train-model] modułu czy chcemy model do przewidywania wartości ryzyka kredytowego.

1. Wybierz [Train Model] [ train-model] modułu. W **właściwości** okienku, kliknij przycisk **Uruchom selektor kolumn**.

2. W **wybrać pojedynczą kolumnę** okna dialogowego, wpisz "ryzyko kredytowe" w polu wyszukiwania w obszarze **dostępne kolumny**, wybierz "Ryzyko kredytowe" poniżej i kliknij przycisk strzałki w prawo (**>**) aby przenieść "Ryzyka kredytowego" **wybrane kolumny**. 

    ![Wybierz kolumnę ryzyka kredytowego modułu Train Model][0]

3. Kliknij przycisk **OK** znacznik wyboru.

### <a name="two-class-support-vector-machine"></a>Algorytm SVM dla problemu dwuklasowego

Następnie skonfigurowanie SVM modelu.  

Pierwszy, nieco wyjaśnienie SVM. Drzewa decyzyjne boosted działają prawidłowo w przypadku funkcji dowolnego typu. Jednak ponieważ moduł SVM generuje klasyfikatora liniowego, modelu, który generuje ma najlepsze błąd testu, gdy wszystkie funkcje numeryczne mieć takiej samej skali. Aby przekonwertować wszystkie funkcje numeryczne takiej samej skali, używamy transformację "Tanh" (z [normalizacji danych] [ normalize-data] modułu). To przekształca naszych numery w zakresie [0,1]. Moduł SVM konwertuje ciąg funkcji funkcje podzielone na kategorie, a następnie binarne funkcji 0/1, nie należy ręcznie Przekształcanie funkcji ciągu. Ponadto nie chcemy przekształcenie ryzyka kredytowego kolumna (kolumna 21) — liczbowych jest, ale jest wartość możemy uczony model do przewidywania, więc musimy pozostawiony.  

Aby skonfigurować SVM model, wykonaj następujące czynności:

1. Znajdź [maszyny wektorowa obsługa Two-Class] [ two-class-support-vector-machine] modułu na palecie modułów i przeciągnij go do obszaru roboczego.

2. Kliknij prawym przyciskiem myszy [Train Model] [ train-model] modułu, wybierz opcję **kopiowania**i kliknij prawym przyciskiem myszy kanwę i wybierz **Wklej**. Kopię [Train Model] [ train-model] moduł ma tego samego kolumnę zaznaczenia co oryginalny.

3. Połącz dane wyjściowe [maszyny wektorowa obsługa Two-Class] [ two-class-support-vector-machine] modułu po lewej stronie wprowadź port drugiego [Train Model] [ train-model] modułu.

4. Znajdź [normalizacji danych] [ normalize-data] modułu i przeciągnij go do obszaru roboczego.

5. Połącz dane wyjściowe po lewej stronie po lewej stronie [wykonanie skryptu języka R] [ execute-r-script] modułu w danych wejściowych w tym module (powiadomienia, czy port wyjściowy modułu może być podłączony do więcej niż jeden moduł innych).

6. Połącz lewy port wyjściowy [normalizacji danych] [ normalize-data] modułu po prawej stronie wprowadź port drugiego [Train Model] [ train-model] modułu.

Ta część naszego eksperyment powinien teraz wyglądać mniej więcej tak:  

![Uczenie modelu drugi][2]  

Teraz skonfigurować [normalizacji danych] [ normalize-data] modułu:

1. Kliknij, aby wybrać [normalizacji danych] [ normalize-data] modułu. W **właściwości** okienku wybierz **Tanh** dla **metody przekształcania** parametru.

2. Kliknij przycisk **Uruchom selektor kolumn**, wybierz opcję "Brak kolumn" dla **od**, wybierz pozycję **Include** z pierwszej listy rozwijanej wybierz **typ kolumny** w drugim listy rozwijanej, a następnie wybierz **liczbowych** na liście rozwijanej trzecich. To ustawienie określa przekształcone wszystkie kolumny liczbowe (i tylko numeryczne).

3. Kliknij znak plus (+) z prawej strony tego wiersza — spowoduje to utworzenie wiersza listę rozwijaną. Wybierz **wykluczyć** z pierwszej listy rozwijanej wybierz **nazwy kolumn** w drugim listy rozwijanej, a następnie wprowadź "Ryzyko kredytowe" w polu tekstowym. Określa kolumnę ryzyka kredytowego należy ją ignorować (należy to zrobić, ponieważ ta kolumna jest liczbowe w związku z czym może zostać przekształcone Jeśli firma Microsoft nie go wykluczyć).

4. Kliknij przycisk **OK** znacznik wyboru.  

    ![Wybierz kolumnę do modułu normalizacji danych][5]

[Normalizacji danych] [ normalize-data] modułu ma teraz wartość przekształcenie Tanh dla wszystkich kolumn liczbowych, z wyjątkiem kolumny ryzyka kredytowego.  

## <a name="score-and-evaluate-the-models"></a>Uczenie i Ewaluacja modeli

Używamy testowania danych, który został oddzielony się przez [podziału danych] [ split] modułu do wyniku naszych przeszkolone modeli. Firma Microsoft może następnie porównaj wyniki działania dwa modele, aby zobaczyć, która jest generowana w poszukiwaniu lepszych wyników.  

### <a name="add-the-score-model-modules"></a>Dodaj moduł Score Model

1. Znajdź [Score Model] [ score-model] modułu i przeciągnij go do obszaru roboczego.

2. Połącz [Train Model] [ train-model] moduł, który jest połączony z [Two-Class Boosted drzewa decyzyjnego] [ two-class-boosted-decision-tree] port wejściowy modułu w lewo [Score Model] [ score-model] modułu.

3. Połącz prawo [wykonanie skryptu języka R] [ execute-r-script] modułu (danych testowych) po prawej stronie wprowadź port [Score Model] [ score-model] modułu.

    ![Moduł score Model połączenia][6]
   
   [Score Model] [ score-model] modułu można teraz odpowiednie informacje środki z danych testowych, uruchom go za pośrednictwem modelu i porównywania prognoz modelu generuje z kolumną ryzyka rzeczywiste środki w Testowanie danych.

4. Skopiuj i Wklej [Score Model] [ score-model] modułu do utworzenia drugiej kopii.

5. Połącz dane wyjściowe modelu SVM (to znaczy portem wyjściowym [Train Model] [ train-model] moduł, który jest połączony z [maszyny wektorowa obsługa Two-Class] [ two-class-support-vector-machine] modułu) do portu wejściowego drugiego [Score Model] [ score-model] modułu.

6. SVM model zostały celu przekształcenia do danych testowych, jak robiliśmy danych szkoleniowych. Aby skopiować i wkleić [normalizacji danych] [ normalize-data] modułu Tworzenie drugiej kopii i połącz go z prawej strony [wykonanie skryptu języka R] [ execute-r-script] modułu.

7. Połącz dane wyjściowe po lewej stronie drugiego [normalizacji danych] [ normalize-data] modułu po prawej stronie wprowadź port drugiego [Score Model] [ score-model] modułu.

    ![Zarówno modułów Score Model połączenia][7]

### <a name="add-the-evaluate-model-module"></a>Dodanie modułu Evaluate Model

Aby ocenić dwóch wyników oceniania i porównaj je, używamy [Evaluate Model] [ evaluate-model] modułu.  

1. Znajdź [Evaluate Model] [ evaluate-model] modułu i przeciągnij go do obszaru roboczego.

2. Połącz port wyjściowy [Score Model] [ score-model] modułu skojarzonego z tym modelem drzewa decyzyjnego boosted do lewy port wejściowy z [Evaluate Model] [ evaluate-model] modułu.

3. Połącz innych [Score Model] [ score-model] port wejściowy modułu z prawej strony.  

    ![Ocena modelu modułu połączone][8]

### <a name="run-the-experiment-and-check-the-results"></a>Uruchom eksperyment i sprawdzić wyniki

Aby uruchomić eksperyment, kliknij przycisk **Uruchom** znajdujący się poniżej obszaru roboczego. Może upłynąć kilka minut. Wskaźnik Obracająca dla każdego modułu pokazuje, że jest uruchomiona, i czym wyświetlany zielony znacznik wyboru, po zakończeniu modułu. Jeśli zaznaczono wszystkie moduły eksperyment zakończył działanie.

Eksperyment powinien teraz wyglądać mniej więcej tak:  

![Ocena obu modeli][3]

Aby sprawdzić wyniki, kliknij port wyjściowy [Evaluate Model] [ evaluate-model] moduł i zaznacz **Visualize**.  

[Evaluate Model] [ evaluate-model] modułu tworzy parę krzywych i metryk, które pozwalają porównać wyniki dwa modele scored. Można wyświetlić wyniki w postaci krzywych cech Operator odbiornika (ROC), krzywych dokładności/wycofania lub krzywych przyrostu. Dodatkowe dane wyświetlane obejmuje macierz pomyłek, łączne wartości dla obszarze krzywej (AUC) i innych metryk. Można zmienić wartość progowa za pomocą suwaka, lewo lub w prawo i zobacz, jak wpływa na zbiór metryki.  

Po prawej stronie wykresu **oceniane zestawu danych** lub **oceniane zestawu danych, aby porównać** Wyróżnij skojarzone krzywej i wyświetlania metryk skojarzone poniżej. W legendzie dla krzywych "Oceniane zestawu danych" odpowiada lewy port wejściowy z [Evaluate Model] [ evaluate-model] moduł — w tym przypadku jest to model drzewa boosted decyzji. "Zestawu danych, aby porównać wynik" odpowiada prawy port wejściowy - SVM modelu w tym przypadku. Po kliknięciu jednego z tych etykiet zostanie wyróżniona krzywej modelu i wyświetlane są odpowiednie metryki, jak pokazano na poniższym rysunku.  

![Krzywe ROC dla modeli][4]

Badanie tych wartości, można zdecydować, model, który jest najbardziej zbliżony do daje rezultatów, którego szukasz. Możesz wrócić do poprzedniej strony i iterację eksperymentu, zmieniając wartości parametrów w różne modele. 

Nauki i grafik interpretowanie te wyniki i dostrajania wydajności modelu wykracza poza zakres tego przewodnika. Aby uzyskać dodatkową pomoc może przeczytać następujące artykuły:
- [Jak do oceny wydajności modelu w usłudze Azure Machine Learning](evaluate-model-performance.md)
- [Wybierz parametry w celu zoptymalizowania algorytmy w usłudze Azure Machine Learning](algorithm-parameters-optimize.md)
- [Interpretowanie wyników modelu w usłudze Azure Machine Learning](interpret-model-results.md)

> [!TIP]
> Zawsze uruchomić eksperyment rekord tego iteracji jest przechowywanych w historii uruchamiania. Można wyświetlić te iteracji i wróć do każdej z nich, klikając **WYŚWIETL HISTORIĘ URUCHAMIANIA** poniżej obszaru roboczego. Możesz również kliknąć **uprzedniego uruchomienia** w **właściwości** okienko, aby powrócić do iteracji, bezpośrednio przed otwartych.
> 
> Można utworzyć kopię dowolną iterację eksperymentu, klikając **SAVE AS** poniżej obszaru roboczego. 
> Użyj eksperymentu **Podsumowanie** i **opis** właściwości, aby rejestrować co podjęto w Twojej iteracje eksperymentu.
> 
> Aby uzyskać więcej informacji, zobacz [Zarządzanie iteracjami eksperymentów w usłudze Azure Machine Learning Studio](manage-experiment-iterations.md).  
> 
> 

- - -
**Następnie: [wdrażanie usługi sieci web](walkthrough-5-publish-web-service.md)**

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
