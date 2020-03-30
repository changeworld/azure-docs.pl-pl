---
title: 'Regresja liniowa: Odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak użyć modułu regresji liniowej w usłudze Azure Machine Learning do utworzenia modelu regresji liniowej do użycia w potoku.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 602553637e21b17aa4f9bc7402753af024c697c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477565"
---
# <a name="linear-regression-module"></a>Moduł regresji liniowej
W tym artykule opisano moduł w projektancie usługi Azure Machine Learning (wersja zapoznawcza).

Ten moduł służy do tworzenia modelu regresji liniowej do użycia w potoku.  Regresja liniowa próbuje ustanowić relację liniową między jedną lub kilkoma zmiennymi niezależnymi a wynikiem liczbowym lub zmienną zależną. 

Ten moduł służy do definiowania metody regresji liniowej, a następnie trenowania modelu przy użyciu oznaczonego zestawu danych. Przeszkolony model może następnie służyć do prognozowania.

## <a name="about-linear-regression"></a>Regresja liniowa – informacje

Regresja liniowa jest powszechną metodą statystyczną, która została przyjęta w uczeniu maszynowym i wzbogacona o wiele nowych metod dopasowania błędu liniowego i pomiarowego. W najbardziej podstawowym znaczeniu regresja odnosi się do przewidywania liczbowego celu. Regresja liniowa jest nadal dobrym wyborem, gdy potrzebujesz prostego modelu dla podstawowego zadania predykcyjnego. Regresja liniowa ma również tendencję do dobrego działania w wysokowymiarowych, rzadkich zestawach danych pozbawionych złożoności.

Usługa Azure Machine Learning obsługuje wiele modeli regresji, oprócz regresji liniowej. Jednak termin "regresja" może być interpretowany luźno, a niektóre typy regresji podane w innych narzędziach nie są obsługiwane.

+ Klasyczny problem regresji obejmuje pojedynczą zmienną niezależną i zmienną zależną. Nazywa się to *prostą regresją*.  Ten moduł obsługuje prostą regresję.

+ *Wielokrotna regresja liniowa* obejmuje dwie lub więcej niezależnych zmiennych, które przyczyniają się do pojedynczej zmiennej zależnej. Problemy, w których wiele wejść są używane do przewidywania pojedynczego wyniku numerycznego są również nazywane *regresji liniowej wielowymiarowej*.

    Moduł **regresji liniowej** może rozwiązać te problemy, podobnie jak większość innych modułów regresji.

+ *Regresja wielu etykiet* jest zadaniem przewidywania wielu zmiennych zależnych w ramach jednego modelu. Na przykład w regresji logistycznej wielu etykiet próbki można przypisać do wielu różnych etykiet. (To różni się od zadania przewidywania wielu poziomów w ramach jednej zmiennej klasy.)

    Ten typ regresji nie jest obsługiwany w usłudze Azure Machine Learning. Aby przewidzieć wiele zmiennych, należy utworzyć oddzielny uczeń dla każdego wyjścia, które chcesz przewidzieć.

Statystycy od lat opracowują coraz bardziej zaawansowane metody regresji. Dotyczy to nawet regresji liniowej. Ten moduł obsługuje dwie metody pomiaru błędu i dopasowania linii regresji: zwykłą metodę najmniejszych kwadratów i zejście gradientu.

- **Gradient zejście** jest metodą, która minimalizuje ilość błędu na każdym etapie procesu szkolenia modelu. Istnieje wiele odmian gradientu zejścia i jego optymalizacji dla różnych problemów uczenia się został szeroko zbadany. Jeśli wybierzesz tę opcję dla **metody rozwiązanie,** można ustawić różne parametry, aby kontrolować rozmiar kroku, szybkość uczenia się i tak dalej. Ta opcja obsługuje również użycie zintegrowanego wyciągnięcia pochyłości parametrów.

- **Zwykłe najmniejsze kwadraty** są jedną z najczęściej stosowanych technik regresji liniowej. Na przykład najmniej kwadratów jest metodą używaną w pakiecie Analysis Toolpak dla programu Microsoft Excel.

    Zwykłe najmniejsze kwadraty odnoszą się do funkcji utraty, która oblicza błąd jako sumę kwadratu odległości od rzeczywistej wartości do przewidywanej linii i pasuje do modelu, minimalizując kwadratowy błąd. Ta metoda zakłada silną relację liniową między wejściami a zmienną zależną.

## <a name="configure-linear-regression"></a>Konfigurowanie regresji liniowej

Ten moduł obsługuje dwie metody montażu modelu regresji, z różnymi opcjami:

+ [Dopasuj model regresji przy użyciu zwykłych najmniejszych kwadratów](#create-a-regression-model-using-ordinary-least-squares)

    W przypadku małych zestawów danych najlepiej wybrać zwykłe najmniejsze kwadraty. Powinno to dać podobne wyniki do programu Excel.
    
+ [Tworzenie modelu regresji przy użyciu gradientu online](#create-a-regression-model-using-online-gradient-descent)

    Zejście gradientu jest lepszą funkcją utraty dla modeli, które są bardziej złożone lub które mają zbyt mało danych szkoleniowych, biorąc pod uwagę liczbę zmiennych.

### <a name="create-a-regression-model-using-ordinary-least-squares"></a>Tworzenie modelu regresji przy użyciu zwykłych najmniejszych kwadratów

1. Dodaj moduł **modelu regresji liniowej** do potoku w projektancie.

    Ten moduł można znaleźć w kategorii **Uczenie maszynowe.** Rozwiń pozycję **Inicjowanie modelu,** rozwiń **regresję,** a następnie przeciągnij moduł **Modelu regresji liniowej** do potoku.

2. W okienku **Właściwości** na liście rozwijanej **Metoda rozwiązania** wybierz pozycję Zwykłe **najmniej kwadratów**. Ta opcja określa metodę obliczeń używaną do znajdowania linii regresji.

3. W **L2 waga legalizacji**, wpisz wartość do użycia jako waga do legalizacji L2. Zaleca się użycie wartości niezerowej, aby uniknąć overfitting.

     Aby dowiedzieć się więcej o tym, jak uregulowanie wpływa na dopasowanie modelu, zobacz ten artykuł: [L1 i L2 Regularization for Machine Learning](https://msdn.microsoft.com/magazine/dn904675.aspx)

4. Wybierz opcję **Dołącz termin przecięcia**, jeśli chcesz wyświetlić termin przecięcia.

    Usuń zaznaczenie tej opcji, jeśli nie musisz przeglądać formuły regresji.

5. W polu **Ilość materiału siewnego liczb losowych**można opcjonalnie wpisać wartość, aby wysiewnić generator liczb losowych używany przez model.

    Za pomocą wartości inicjatora jest przydatne, jeśli chcesz zachować te same wyniki w różnych przebiegach tego samego potoku. W przeciwnym razie wartością domyślną jest użycie wartości z zegara systemowego.


7. Dodaj moduł [Modelu pociągu](./train-model.md) do potoku i połącz zestaw danych z etykietą.

8. Prześlij potok.

### <a name="results-for-ordinary-least-squares-model"></a>Wyniki dla zwykłego modelu najmniejszych kwadratów

Po zakończeniu szkolenia:


+ Aby dokonać prognoz, należy połączyć przeszkolony model do [score model](./score-model.md) modułu, wraz z zestawem danych nowych wartości. 


### <a name="create-a-regression-model-using-online-gradient-descent"></a>Tworzenie modelu regresji przy użyciu gradientu online

1. Dodaj moduł **modelu regresji liniowej** do potoku w projektancie.

    Ten moduł można znaleźć w kategorii **Uczenie maszynowe.** Rozwiń **rozwijanie modułu Inicjuj model,** rozwiń **regresję**i przeciągnij moduł **Model regresji liniowej** do potoku

2. W okienku **Właściwości** na liście rozwijanej **Metody rozwiązania** wybierz pozycję **Zejście gradientu online** jako metodę obliczeń używaną do znajdowania linii regresji.

3. W przypadku **opcji Utwórz tryb trenera**należy wskazać, czy model ma być trenowany ze wstępnie zdefiniowanym zestawem parametrów, czy też chcesz zoptymalizować model przy użyciu wyciągnięcia po parametrze.

    + **Pojedynczy parametr:** Jeśli wiesz, jak chcesz skonfigurować sieć regresji liniowej, możesz podać określony zestaw wartości jako argumenty.
    
    + **Zakres parametrów:** Wybierz tę opcję, jeśli nie masz pewności co do najlepszych parametrów i chcesz uruchomić wyciągnięcie po parametrze. Wybierz zakres wartości, aby iterować ponad i [Tune Model Hyperparameters](tune-model-hyperparameters.md) iteruje nad wszystkimi możliwymi kombinacjami ustawień podanych w celu określenia hiperparametrów, które dają optymalne wyniki.  

   
4. W przypadku **wskaźnika uczenia się**określ początkową szybkość uczenia się optymalizatora zejścia gradientu stochastycznego.

5. W polu **Liczba epok treningowych**wpisz wartość, która wskazuje, ile razy algorytm powinien iterować za pomocą przykładów. W przypadku zestawów danych z niewielką liczbą przykładów liczba ta powinna być duża, aby osiągnąć zbieżność.

6. **Normalizuj funkcje:** Jeśli zostały już znormalizowane dane liczbowe użyte do przeszkolenia modelu, można usunąć zaznaczenie tej opcji. Domyślnie moduł normalizuje wszystkie dane wejściowe numeryczne do zakresu od 0 do 1.

    > [!NOTE]
    > 
    > Pamiętaj, aby zastosować tę samą metodę normalizacji do nowych danych używanych do oceniania.

7. W **L2 waga legalizacji**, wpisz wartość do użycia jako waga do legalizacji L2. Zaleca się użycie wartości niezerowej, aby uniknąć overfitting.

    Aby dowiedzieć się więcej o tym, jak uregulowanie wpływa na dopasowanie modelu, zobacz ten artykuł: [L1 i L2 Regularization for Machine Learning](https://msdn.microsoft.com/magazine/dn904675.aspx)


9. Wybierz opcję **Zmniejsz szybkość uczenia się**, jeśli chcesz, aby szybkość uczenia się zmniejszała się wraz z postępem iteracji.  

10. W polu **Ilość materiału siewnego liczb losowych**można opcjonalnie wpisać wartość, aby wysiewnić generator liczb losowych używany przez model. Za pomocą wartości inicjatora jest przydatne, jeśli chcesz zachować te same wyniki w różnych przebiegach tego samego potoku.


12. Dodaj oznaczony zestaw danych i jeden z modułów szkoleniowych.

    Jeśli nie używasz wyciągnięcia po parametrze, użyj modułu [Model pociągu.](train-model.md)

13. Prześlij potok.

### <a name="results-for-online-gradient-descent"></a>Wyniki zejścia gradientu online

Po zakończeniu szkolenia:

+ Aby dokonać prognoz, należy podłączyć przeszkolony model do [score model](./score-model.md) modułu, wraz z nowymi danymi wejściowymi.


## <a name="next-steps"></a>Następne kroki

Zobacz [zestaw modułów dostępnych dla](module-reference.md) usługi Azure Machine Learning. 