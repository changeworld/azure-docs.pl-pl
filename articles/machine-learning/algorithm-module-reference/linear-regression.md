---
title: 'Regresja liniowa: Odwołania do modułu'
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak użyć modułu regresji liniowej w usłudze Azure Machine Learning, aby utworzyć model regresji liniowej do użytku w eksperymencie.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 040f726a703d34a95bae7d5b7cdd766655c62a4e
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029718"
---
# <a name="linear-regression-module"></a>Liniowy modułu regresji
W tym artykule opisano moduł interfejs graficzny (wersja zapoznawcza) dla usługi Azure Machine Learning.

Ten moduł służy do tworzenia modelu regresji liniowej do użytku w eksperymencie.  Regresja liniowa podejmuje próbę ustanowienia liniowa relacji między zmienne niezależne i wynik liczbowych lub zmienna zależnych od ustawień lokalnych. 

Ten moduł służy do definiowania metody regresji liniowej, a następnie szkolenie modelu z użyciem zestawu danych z etykietą. Następnie można uczonego modelu do przewidywania przyszłych zdarzeń.

## <a name="about-linear-regression"></a>Temat regresji liniowej

Regresja liniowa metoda jest często statystycznych, który został przyjęty w usłudze machine learning i rozszerzona o wielu nowych metod dopasowania wiersza i wyceny błąd. W tym sensie, najbardziej podstawowa regresji odnosi się do prognozowania liczbowych elementu docelowego. Regresja liniowa nadal jest dobrym rozwiązaniem, gdy chcesz prosty model podstawowe zadania predykcyjne. Regresja liniowa zwykle pracę o wielu wymiarach, rozrzedzone zestawów danych, bez złożoności.

Usługa Azure Machine Learning obsługuje różne modele regresji, oprócz regresji liniowej. Termin "regresji", które mogą być interpretowane luźno i niektóre rodzaje regresji podane w innych narzędziach nie są obsługiwane.

+ Problem regresji klasycznego obejmuje pojedynczej zmiennej niezależne i zmienna zależnych od ustawień lokalnych. Jest to nazywane *proste regresji*.  Ten moduł obsługuje prosty regresji.

+ *Wiele regresji liniowej* obejmuje co najmniej dwóch zmienne niezależne, które przyczyniają się do pojedynczej zmiennej zależnych od ustawień lokalnych. Problemy, w których wielu danych wejściowych są używane do prognozowania pojedynczy wynik liczbowe są również nazywane *wieloczynnikowa regresja liniowa*.

    **Regresji liniowej** modułu może rozwiązać te problemy, jak większość innych modułów regresji.

+ *Etykieta wielu regresji* jest zadaniem prognozowania wielu zmiennych zależnych od ustawień lokalnych w jednym modelu. Na przykład w wielu etykietach regresji logistycznej, przykładu można przypisać do wielu różnych etykiet. (To jest różni się od zadania prognozowania wielu poziomów w zmiennej jednej klasy).

    Tego typu regresji nie jest obsługiwane w usłudze Azure Machine Learning. Przewidywanie wiele zmiennych, Utwórz oddzielne uczeń dla każdego danych wyjściowych, który chcesz przewidzieć.

Przez wiele lat Statystyków mają opracowuje coraz bardziej zaawansowane metody regresji. Ta zasada obowiązuje nawet w przypadku regresji liniowej. Ten moduł obsługuje dwie metody do pomiaru błąd i dopasuj regresji: metody zwykłej najmniejszych kwadratów i spadku gradientu.

- **Spadku gradientu** to metoda, która minimalizuje ilość błędów w każdym kroku procesu uczenia modelu. Istnieje wiele odmiany spadku gradientu i często materiałami jej optymalizacji dla różnych problemów uczenia. Jeśli wybierzesz tę opcję, aby **metoda rozwiązania**, ustawić różne parametry, aby kontrolować rozmiar kroku uczenia szybkości i tak dalej. Ta opcja obsługuje także użycie parametrów zintegrowanego.

- **Zwykłe najmniejszych kwadratów** jest jednym z najczęściej używanych technik w regresji liniowej. Na przykład najmniejszych kwadratów jest metodę, która jest używana w Analysis Toolpak dla programu Microsoft Excel.

    Zwykłe najmniejszych kwadratów odnosi się do utraty funkcji oblicza błąd jako suma kwadrat odległość między wartością rzeczywistą przewidywane wiersza, która pasuje do modelu, minimalizując pierwiastek błędu. Ta metoda zakłada liniowej związek między dane wejściowe, a zmienna zależnych od ustawień lokalnych.

## <a name="configure-linear-regression"></a>Konfigurowanie regresji liniowej

Ten moduł obsługuje dwie metody, aby uzyskać dopasowanie modelu regresji przy użyciu różnych opcji:

+ [Tworzenie modelu regresji przy użyciu spadku gradientu online](#bkmk_GradientDescent)

    Spadku gradientu jest funkcją utraty lepsze dla modeli, które są bardziej złożone, lub które mają zbyt mało danych szkoleniowych, biorąc pod uwagę liczbę zmiennych.



+ [Dopasuj modelu regresji przy użyciu zwykłej najmniejszych kwadratów](#bkmk_OrdinaryLeastSquares)

    W przypadku małych zestawów danych najlepiej wybrać zwykłych najmniejszych kwadratów. To powinno dawać wyniki podobne do programu Excel.

## <a name="bkmk_OrdinaryLeastSquares"></a> Tworzenie modelu regresji przy użyciu zwykłej najmniejszych kwadratów

1. Dodaj **uczenia modelu regresji liniowej** modułu do eksperymentu w interfejsie.

    Możesz znaleźć tego modułu w **uczenia maszynowego** kategorii. Rozwiń **zainicjować modelu**, rozwiń węzeł **regresji**, a następnie przeciągnij **uczenia modelu regresji liniowej** modułu do eksperymentu.

2. W **właściwości** okienko w **metoda rozwiązania** listy rozwijanej wybierz **zwykłych najmniejszych kwadratów**. Ta opcja określa metodę obliczeń, używana do znajdowania regresji.

3. W **wagi uregulowania L2**, wpisz wartość do użycia jako wagę pamięci podręcznej L2 uregulowania. Zalecamy użycie wartość niezerową w celu uniknięcia overfitting.

     Aby dowiedzieć się więcej o tym jak uregulowania wpływa na dostosowania modelu, znajduje się w artykule: [P1 i P2 uregulowania dla usługi Machine Learning](https://msdn.microsoft.com/magazine/dn904675.aspx)

4. Wybierz opcję **termin intercept Include**, jeśli chcesz wyświetlić okres osią.

    Wyłącz tę opcję, jeśli nie potrzebujesz zapoznać się z formuła regresji.

5. Aby uzyskać **inicjatora liczb losowych**, możesz opcjonalnie wpisać wartość początkową generator liczb losowych, używanego przez model.

    Przy użyciu wartości inicjatora jest przydatne, jeśli chcesz zachować te same wyniki we wszystkich przebiegach aktualizacji różnych tym samym eksperymencie. W przeciwnym razie wartość domyślna to, aby korzystała z wartości z zegara systemowego.


7. Dodaj [Train Model](./train-model.md) modułu do eksperymentu i Połącz z etykietami zestawu danych.

8. Uruchom eksperyment.

## <a name="results-for-ordinary-least-squares-model"></a>Wyniki dla zwykłych najmniejszych kwadratów modelu

Po zakończeniu szkolenia:

+ Aby wyświetlić parametry modelu, kliknij prawym przyciskiem myszy dane wyjściowe trainer, a następnie wybierz **Visualize**.

+ Aby tworzyć prognozy, należy połączyć się z nauczonemu modelowi w celu [Score Model](./score-model.md) moduł wraz z zestawu danych z nowymi wartościami. 


## <a name="bkmk_GradientDescent"></a> Tworzenie modelu regresji przy użyciu spadku gradientu online

1. Dodaj **uczenia modelu regresji liniowej** modułu do eksperymentu w interfejsie.

    Możesz znaleźć tego modułu w **uczenia maszynowego** kategorii. Rozwiń **zainicjować modelu**, rozwiń węzeł **regresji**, a następnie przeciągnij **uczenia modelu regresji liniowej** modułu do eksperymentu

2. W **właściwości** okienko w **metoda rozwiązania** listy rozwijanej wybierz **spadku gradientu Online** jako metoda obliczeń, używana do znajdowania regresji.

3. Aby uzyskać **trybie trainer tworzenia**, wskazać, czy użytkownik zamierza uczenie modelu przy użyciu wstępnie zdefiniowany zestaw parametrów, lub jeśli chcesz zoptymalizować modelu za pomocą parametrów.

    + **Pojedynczy parametr**: Jeśli wiesz, jak chcesz skonfigurować sieć regresji liniowej, możesz podać określonych wartości jako argumenty.

   
4. Dla **uczenia współczynnik**, określ tempo uczenia początkowej optymalizatorze stochastycznego spadku gradientu.

5. Dla **liczba epok szkolenia**, wpisz wartość, która wskazuje, ile razy algorytm powinien wykonać iterację przykłady. W przypadku zestawów danych z mniejszą liczbą przykłady ta liczba może być duży do osiągnięcia zbieżności.

6. **Normalizuj funkcji**: Jeśli już mają znormalizowane dane liczbowe używane do nauczenia modelu, możesz usunąć zaznaczenie tej opcji. Domyślnie moduł normalizuje liczbowe dane wejściowe do zakresu od 0 do 1.

    > [!NOTE]
    > 
    > Pamiętaj, aby zastosować tę samą metodę normalizacji do nowych danych, używanych do oceniania.

7. W **wagi uregulowania L2**, wpisz wartość do użycia jako wagę pamięci podręcznej L2 uregulowania. Zalecamy użycie wartość niezerową w celu uniknięcia overfitting.

    Aby dowiedzieć się więcej o tym jak uregulowania wpływa na dostosowania modelu, znajduje się w artykule: [P1 i P2 uregulowania dla usługi Machine Learning](https://msdn.microsoft.com/magazine/dn904675.aspx)


9. Wybierz opcję **zmniejsz tempo uczenia**, jeśli chcesz, aby tempo uczenia, aby zmniejszyć postęp iteracji.  

10. Aby uzyskać **inicjatora liczb losowych**, możesz opcjonalnie wpisać wartość początkową generator liczb losowych, używanego przez model. Przy użyciu wartości inicjatora jest przydatne, jeśli chcesz zachować te same wyniki we wszystkich przebiegach aktualizacji różnych tym samym eksperymencie.


12. Dodaj zestaw danych oznaczonych i jeden z modułów szkolenia.

    Jeśli nie używasz parametrów, należy użyć [Train Model](train-model.md) modułu.

13. Uruchom eksperyment.

## <a name="results-for-online-gradient-descent"></a>Wyniki dla spadku gradientu online

Po zakończeniu szkolenia:

+ Aby tworzyć prognozy, należy połączyć się z nauczonemu modelowi w celu [Score Model](./score-model.md) moduł wraz z nowe dane wejściowe.


## <a name="next-steps"></a>Kolejne kroki

Zobacz [zestaw dostępnych modułów](module-reference.md) do usługi Azure Machine Learning. 