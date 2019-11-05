---
title: 'Regresja drzewa decyzyjnej: odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, w jaki sposób użyć modułu regresja drzewa decyzyjnego w Azure Machine Learning, aby utworzyć zbiór drzew regresji przy użyciu podwyższania poziomu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: b04a44a82f793f39b24ffd94083ca8fd5e66cfb8
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73493889"
---
# <a name="boosted-decision-tree-regression-module"></a>Moduł regresji drzewa decyzyjnego

W tym artykule opisano moduł w programie Azure Machine Learning Designer (wersja zapoznawcza).

Ten moduł służy do tworzenia kompletu drzew regresji przy użyciu zwiększania. *Zwiększenie wydajności* oznacza, że każde drzewo jest zależne od wcześniejszych drzew. Algorytm uczy się, instalując resztę drzew, które je poprzedza. W tym celu zwiększenie dokładności w drzewie decyzyjnym pozwala zwiększyć dokładność z mniejszym ryzykiem.  
  
Ta metoda regresji jest metodą uczenia nadzorowanego i dlatego wymaga *oznaczonego zestawu danych*. Kolumna Label musi zawierać wartości numeryczne.  

> [!NOTE]
> Tego modułu należy używać tylko z zestawami danych, które używają zmiennych liczbowych.  

Po zdefiniowaniu modelu nauczenie go przy użyciu [modelu uczenia](./train-model.md).

> [!TIP]
> Chcesz dowiedzieć się więcej na temat utworzonych drzew? Po przeszkoleniu modelu kliknij prawym przyciskiem myszy dane wyjściowe modułu [uczenie modelu](./train-model.md) i wybierz polecenie **Wizualizacja** , aby zobaczyć drzewo, które zostało utworzone w każdej iteracji. Możesz przejść do szczegółów podziałów dla każdego drzewa i wyświetlić reguły dla każdego węzła.  
  
## <a name="more-about-boosted-regression-trees"></a>Więcej informacji o wzmocnionych drzewach regresji  

Zwiększenie wydajności jest jedną z wielu metod klasycznych do tworzenia modeli zestawów, a także do worków, losowych lasów i tak dalej.  W Azure Machine Learning, podwyższane drzewa decyzyjne wykorzystują wydajną implementację algorytmu zwiększania gradientów SKŁADNI. Zwiększanie gradientu jest techniką uczenia maszynowego w przypadku problemów z regresją. Kompiluje Każde drzewo regresji w sposób funkcjonalny, przy użyciu wstępnie zdefiniowanej funkcji utraty do pomiaru błędu w każdym kroku i poprawnego dla niego w następnym. W ten sposób model przewidywania jest w rzeczywistości integralną częścią modeli predykcyjnych.  
  
W przypadku problemów z regresją zwiększenie wydajności kompiluje szereg drzew w sposób krokowy, a następnie wybiera optymalne drzewo przy użyciu dowolnej funkcji differentiable strat.  
  
Aby uzyskać dodatkowe informacje, zobacz następujące artykuły:  
  
+ [https://wikipedia.org/wiki/Gradient_boosting#Gradient_tree_boosting](https://wikipedia.org/wiki/Gradient_boosting)

    Ten artykuł w witrynie Wikipedia na temat zwiększania gradientów zawiera niektóre w tle dla podwyższających drzew. 
  
-  [https://research.microsoft.com/apps/pubs/default.aspx?id=132652](https://research.microsoft.com/apps/pubs/default.aspx?id=132652)  

    Microsoft Research: od RankNet do LambdaRank do LambdaMART: Omówienie. Według J.C. Burges.

Metodę zwiększania gradientu można również użyć w przypadku problemów z klasyfikacją przez zredukowanie ich do regresji przy użyciu odpowiedniej funkcji strat. Aby uzyskać więcej informacji na temat wdrażania podwyższanych drzew dla zadań klasyfikacji, zobacz [dwuklasowe drzewo decyzyjne](./two-class-boosted-decision-tree.md).  

## <a name="how-to-configure-boosted-decision-tree-regression"></a>Jak skonfigurować regresję drzewa decyzyjnej podwyższania

1.  Dodaj do potoku moduł **podwyższający decyzje** . Ten moduł można znaleźć w obszarze **Machine Learning**, **zainicjować**, w kategorii **regresja** . 
  
2.  Określ, w jaki sposób ma być szkolony model, ustawiając opcję **tworzenia trybu Trainer** .  
  
    -   **Pojedynczy parametr**: zaznacz tę opcję, Jeśli wiesz, jak chcesz skonfigurować model i udostępnić określony zestaw wartości jako argumenty.  
   
  
3. **Maksymalna liczba liści na drzewo**: wskazuje maksymalną liczbę węzłów terminalu (liści), które można utworzyć w dowolnym drzewie.  

    Zwiększając tę wartość, można zwiększyć rozmiar drzewa i uzyskać lepszą precyzję w przypadku ryzyka przekroczenia i dłuższego czasu uczenia się.  

4. **Minimalna liczba próbek na węzeł liścia**: wskazuje minimalną liczbę przypadków wymaganych do utworzenia dowolnego węzła terminalu w drzewie.

    Zwiększenie tej wartości spowoduje zwiększenie wartości progowej tworzenia nowych reguł. Na przykład, z wartością domyślną 1, nawet pojedynczy przypadek może spowodować utworzenie nowej reguły. W przypadku zwiększenia wartości do 5 dane szkoleniowe muszą zawierać co najmniej 5 przypadków, które spełniają te same warunki.

5. **Szybkość uczenia**: wpisz liczbę z zakresu od 0 do 1, która definiuje rozmiar kroku podczas uczenia się. Szybkość uczenia określa, jak szybko lub wolno dowiedzieć się zbieżność z optymalnym rozwiązaniem. Jeśli rozmiar tego kroku jest zbyt duży, można przekroczyć optymalne rozwiązanie. Jeśli rozmiar tego kroku jest zbyt mały, szkolenie trwa dłużej niż w przypadku najlepszego rozwiązania.

6. **Liczba skonstruowanych drzew**: wskazuje całkowitą liczbę drzew decyzyjnych do utworzenia w całości. Przez utworzenie większej liczby drzew decyzyjnych można potencjalnie uzyskać lepszy zakres, ale wydłużyć czas uczenia.

    Ta wartość kontroluje również liczbę drzew wyświetlanych podczas wizualizacji przeszkolonego modelu. Jeśli chcesz zobaczyć lub wydrukować pojedyncze drzewo, możesz ustawić wartość 1. jednak tworzone jest tylko jedno drzewo (drzewo z początkowym zestawem parametrów) i nie są wykonywane żadne dalsze iteracje.

7. **Inicjator liczb losowych**: wpisz opcjonalną nieujemną liczbę całkowitą, która ma być używana jako wartość inicjatora losowego. Określenie inicjatora zapewnia odtwarzalność w przebiegach, które mają te same dane i parametry.

    Domyślnie losowy inicjator jest ustawiony na 0, co oznacza, że początkowa wartość inicjatora jest uzyskiwana z zegara systemowego.
  
8. **Zezwalaj na nieznane poziomy kategorii**: Wybierz tę opcję, aby utworzyć grupę dla nieznanych wartości w zestawach szkoleń i walidacji. W przypadku zaznaczenia tej opcji model może akceptować tylko wartości, które są zawarte w danych szkoleniowych. Model może być mniej dokładny dla znanych wartości, ale może zapewnić lepsze przewidywania dla nowych wartości (nieznanych).

9. Dodaj zestaw danych szkoleniowych i jeden z modułów szkoleniowych:

    - Jeśli ustawisz opcję **tworzenia trybu Trainer** na **pojedynczy parametr**, użyj modułu [uczenie modelu](train-model.md) .  
  
    

10. Uruchamianie potoku.  
  
## <a name="results"></a>Wyniki

Po zakończeniu szkolenia:

+ Aby zobaczyć drzewo, które zostało utworzone w każdej iteracji, kliknij prawym przyciskiem myszy dane wyjściowe modułu [uczenie modelu](train-model.md) i wybierz polecenie **Wizualizuj**.
  
     Kliknij każde drzewo, aby przejść do szczegółów i wyświetlić reguły dla każdego węzła.  

+ Aby użyć modelu do oceniania, połącz go z [modelem oceny](./score-model.md), aby przewidzieć wartości dla nowych przykładów wejściowych.

+ Aby zapisać migawkę przeszkolonego modelu, kliknij prawym przyciskiem myszy **przeszkolony model** danych wyjściowych modułu szkoleniowego i wybierz polecenie **Zapisz jako**. Kopia przeszkolonego modelu, który jest zapisywany, nie jest aktualizowana w kolejnych uruchomieniach potoku.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning. 
