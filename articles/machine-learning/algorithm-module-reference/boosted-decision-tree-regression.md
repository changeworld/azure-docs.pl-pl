---
title: 'Regresja drzewa decyzyjnego wzmocnionej: odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak użyć modułu regresji drzewa decyzji promowane w usłudze Azure Machine Learning, aby utworzyć zespół drzew regresji przy użyciu zwiększania.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 79a2ccae31fac31d8d10bb643c35a41a3d7cb5d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456730"
---
# <a name="boosted-decision-tree-regression-module"></a>Wzmocniony moduł regresji drzewa decyzyjnego

W tym artykule opisano moduł w projektancie usługi Azure Machine Learning (wersja zapoznawcza).

Ten moduł służy do tworzenia zespołu drzew regresji za pomocą zwiększania. *Zwiększenie* oznacza, że każde drzewo jest zależne od wcześniejszych drzew. Algorytm uczy się, montując resztki drzew, które go poprzedzały. Tak więc, zwiększenie w zespole drzewa decyzyjnego ma tendencję do poprawy dokładności z pewnym niewielkim ryzykiem mniejszego pokrycia.  
  
Ta metoda regresji jest nadzorowaną metodą uczenia się i dlatego wymaga *oznaczonego zestawu danych.* Kolumna etykiety musi zawierać wartości liczbowe.  

> [!NOTE]
> Tego modułu należy używać tylko w przypadku zestawów danych, które używają zmiennych numerycznych.  

Po zdefiniowaniu modelu trenuj go za pomocą [modelu pociągu](./train-model.md).

  
## <a name="more-about-boosted-regression-trees"></a>Więcej informacji o zwiększonych drzewach regresji  

Zwiększenie jest jedną z kilku klasycznych metod tworzenia modeli zespołu, wraz z bagging, losowe lasy, i tak dalej.  W usłudze Azure Machine Learning wzmocnione drzewa decyzyjne używają wydajnej implementacji algorytmu zwiększania gradientu MART. Zwiększanie gradientu jest techniką uczenia maszynowego dla problemów regresji. Buduje każde drzewo regresji w sposób krok po kroku, przy użyciu wstępnie zdefiniowanej funkcji utraty do pomiaru błędu w każdym kroku i poprawić go w następnym. W ten sposób model przewidywania jest rzeczywiście zespół słabszych modeli przewidywania.  
  
W problemach regresji, zwiększenie buduje serię drzew w sposób krok po kroku, a następnie wybiera optymalne drzewo przy użyciu dowolnej funkcji różnicowalna strata.  
  
Aby uzyskać dodatkowe informacje, zobacz następujące artykuły:  
  
+ [https://wikipedia.org/wiki/Gradient_boosting#Gradient_tree_boosting](https://wikipedia.org/wiki/Gradient_boosting)

    Ten artykuł w Wikipedii na temat zwiększania gradientu zapewnia pewne tło na wzmocnionych drzewach. 
  
-  [https://research.microsoft.com/apps/pubs/default.aspx?id=132652](https://research.microsoft.com/apps/pubs/default.aspx?id=132652)  

    Microsoft Research: Od RankNet do LambdaRank do LambdaMART: Przegląd. Przez J.C. Burges.

Metoda zwiększania gradientu może być również używana w przypadku problemów z klasyfikacją, zmniejszając je do regresji za pomocą odpowiedniej funkcji utraty. Aby uzyskać więcej informacji na temat implementacji wzmocnionych drzew dla zadań klasyfikacji, zobacz [Dwuklasowe drzewo decyzji wzmocnione](./two-class-boosted-decision-tree.md).  

## <a name="how-to-configure-boosted-decision-tree-regression"></a>Jak skonfigurować regresję wzmocnionego drzewa decyzyjnego

1.  Dodaj moduł **Wzmocnione drzewo decyzyjne** do potoku. Ten moduł można znaleźć w obszarze **Uczenie maszynowe**, **Inicjalizuj**, w kategorii **Regresja.** 
  
2.  Określ sposób, w jaki model ma być trenowany, ustawiając opcję **Utwórz tryb trenera.**  
  
    -   **Pojedynczy parametr:** Wybierz tę opcję, jeśli wiesz, jak chcesz skonfigurować model, i podaj określony zestaw wartości jako argumenty. 
     
    -   **Zakres parametrów:** Wybierz tę opcję, jeśli nie masz pewności co do najlepszych parametrów i chcesz uruchomić wyciągnięcie po parametrze. Wybierz zakres wartości, aby iterować ponad i [Tune Model Hyperparameters](tune-model-hyperparameters.md) iteruje nad wszystkimi możliwymi kombinacjami ustawień podanych w celu określenia hiperparametrów, które dają optymalne wyniki.    
   
  
3. **Maksymalna liczba liści na drzewo:** Wskazuje maksymalną liczbę węzłów zaciskowych (liści), które mogą być tworzone w dowolnym drzewie.  

    Zwiększając tę wartość, potencjalnie zwiększyć rozmiar drzewa i uzyskać lepszą precyzję, na ryzyko overfitting i dłuższy czas szkolenia.  

4. **Minimalna liczba próbek na węzeł liścia:** Wskazać minimalną liczbę przypadków wymaganych do utworzenia dowolnego węzła terminala (liścia) w drzewie.

    Zwiększając tę wartość, można zwiększyć próg tworzenia nowych reguł. Na przykład z wartością domyślną 1, nawet pojedynczy przypadek może spowodować utworzenie nowej reguły. Jeśli zwiększysz wartość do 5, dane szkoleniowe będą musiały zawierać co najmniej 5 przypadków, które spełniają te same warunki.

5. **Szybkość uczenia się:** Wpisz liczbę z 0 a 1, która definiuje rozmiar kroku podczas nauki. Szybkość uczenia się określa, jak szybko lub wolno uczący się zbiega się w optymalnym rozwiązaniu. Jeśli rozmiar kroku jest zbyt duży, można przekroczyć optymalne rozwiązanie. Jeśli rozmiar kroku jest zbyt mały, szkolenie trwa dłużej, aby zbiegać się na najlepsze rozwiązanie.

6. **Liczba zbudowanych drzew**: Wskazuje całkowitą liczbę drzew decyzyjnych do utworzenia w zespole. Tworząc więcej drzew decyzyjnych, można potencjalnie uzyskać lepszy zasięg, ale czas szkolenia wzrasta.

    Ta wartość kontroluje również liczbę drzew wyświetlanych podczas wizualizacji modelu uczonego. Jeśli chcesz wyświetlić lub wydrukować pojedyncze drzewo, możesz ustawić wartość na 1; jednak tylko jedno drzewo jest produkowany (drzewo z początkowym zestawem parametrów) i nie są wykonywane dalsze iteracje.

7. **Siewny liczb losowych:** Wpisz opcjonalną nieujemną liczbę całkowitą, która ma być używana jako losowa wartość materiału siewnego. Określanie materiału siewnego zapewnia odtwarzalność w przebiegach, które mają te same dane i parametry.

    Domyślnie losowy materiał siewny jest ustawiony na 0, co oznacza, że początkowa wartość materiału siewnego jest otrzymywana z zegara systemowego.
  

9. Dodaj zestaw danych szkolenia i jeden z modułów szkoleniowych:

    - Jeśli **ustawisz** opcję Utwórz tryb trenera na **Pojedynczy parametr,** użyj modułu [Model pociągu.](train-model.md)  
  
    

10. Prześlij potok.  
  
## <a name="results"></a>Wyniki

Po zakończeniu szkolenia:

+ Aby użyć modelu do oceniania, połącz go z [modelem wyników,](./score-model.md)aby przewidzieć wartości dla nowych przykładów wejściowych.

+ Aby zapisać migawkę uczonego modelu, wybierz kartę **Dane wyjściowe** w prawym panelu **modelu przeszkolonego** i kliknij **ikonę Zarejestruj zestaw danych.** Kopia przeszkolonego modelu zostanie zapisana jako moduł w drzewie modułów i nie będzie aktualizowana w kolejnych przebiegach potoku.

## <a name="next-steps"></a>Następne kroki

Zobacz [zestaw modułów dostępnych dla](module-reference.md) usługi Azure Machine Learning. 
