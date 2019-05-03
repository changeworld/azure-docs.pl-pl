---
title: 'Uczenie modelu: Odwołania do modułu'
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak używać **uczenie modelu** modułu w usłudze Azure Machine Learning to w opracowywaniu model klasyfikacji lub regresji.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 29d0f698456b83c1520a92bc7df47b26540325f4
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028113"
---
# <a name="train-model-module"></a>Train Model modułu

W tym artykule opisano moduł interfejs graficzny (wersja zapoznawcza) dla usługi Azure Machine Learning.

Ten moduł służy do nauczenia model klasyfikacji lub regresji. Szkolenie odbywa się po zdefiniowany model i ustaw jego parametrów i wymaga danych oznakowanych. Można również użyć **Train Model** doskonalenie istniejącego modelu przy użyciu nowych danych. 

## <a name="how-the-training-process-works"></a>Jak działa proces szkolenia

W usłudze Azure Machine Learning tworzenie i używanie modelu usługi machine learning jest zwykle trzy kroki proces. 

1. Model, należy skonfigurować, wybierając określonego typu algorytmów i zdefiniowanie jego parametrów lub hiperparametrów. Wybierz dowolną z następujących typów modelu: 

    + **Klasyfikacja** modeli, w oparciu o sieci neuronowych, drzewa decyzyjne i decyzji lasów i inne algorytmy.
    + **Regresja** modeli, które mogą obejmować standardowa regresji liniowej, lub które wykorzystują inne algorytmy, w tym sieci neuronowe i regresji Bayesowskie.  

2. Zapewnia zestaw danych jest oznaczona etykietą, która jest zgodna z algorytmem danych. Łączenie danych i modelu, który ma **Train Model**.

    Jakie szkolenia jest tworzony jest określonym formacie binarnym iLearner, hermetyzujący statystyczne wzorce przedstawiono w danych. Nie można bezpośrednio modyfikować ani odczytać ten format; jednak inne moduły można użyć tego uczonego modelu. 
    
    Można również wyświetlić właściwości modelu. Aby uzyskać więcej informacji zobacz sekcję wyników.

3. Po zakończeniu szkolenia za pomocą uczonego modelu jednej z [oceniania modułów](./score-model.md), jak tworzyć prognozy na nowych danych.

## <a name="how-to-use-train-model"></a>Jak używać **Train Model**  
  
1.  W usłudze Azure Machine Learning skonfiguruj model klasyfikacji lub modelu regresji.
    
2. Dodaj **Train Model** modułu do eksperymentu.  Można znaleźć w tym module w ramach **uczenia maszynowego** kategorii. Rozwiń **Train**, a następnie przeciągnij **Train Model** modułu do eksperymentu.
  
3.  W danych wejściowych po lewej stronie Dołącz nieprzeszkolonych trybu. Dołącz zestaw danych szkoleniowych do wprowadzania po prawej stronie **Train Model**.

    Zestaw danych szkoleniowych musi zawierać kolumnę etykiety. Wszystkie wiersze bez etykiet są ignorowane.
  
4.  Dla **etykiety kolumn**, kliknij przycisk **uruchamianie selektora kolumn**i wybierz pojedynczy kolumnę, która zawiera wyniki modelu można użyć do trenowania.
  
    - W przypadku problemów z klasyfikacji kolumnie Etykieta musi zawierać albo **podzielonych na kategorie** wartości lub **dyskretnych** wartości. Niektóre przykłady może być oceny tak/nie, kod klasyfikacji choroby lub nazwa lub grupę przychodów.  W przypadku wybrania kolumny noncategorical, moduł zwróci błąd podczas szkolenia.
  
    -   W przypadku problemów z regresji kolumna etykieta musi zawierać **liczbowych** danych, który reprezentuje zmienną odpowiedzi. W idealnym dane liczbowe reprezentuje ciągłe skali. 
    
    Przykłady może być środki ocenę ryzyka, przewidywany czas na awarie dysku twardego lub prognozowanych liczbę wywołań do Centrum telefonicznej w danym dniu lub czas.  Jeśli nie zostanie wybrane kolumny liczbowej, możesz otrzymać błąd.
  
    -   Jeśli nie określisz etykiety kolumn do użycia, usługi Azure Machine Learning podejmie próbę wywnioskować, czyli kolumny odpowiednią etykietę, posługując się metadanymi zestawu danych. Jeśli go wybierze niewłaściwej kolumnie, należy użyć selektora kolumn go poprawić.
  
    > [!TIP] 
    > Jeśli masz problemy z używaniem selektora kolumn, zobacz artykuł [Select Columns in Dataset](./select-columns-in-dataset.md) porady. Opisuje niektóre typowe scenariusze i wskazówki dotyczące korzystania z **przy użyciu reguł** i **według nazwy** opcje.
  
5.  Uruchom eksperyment. Jeśli masz dużą ilość danych, to może chwilę potrwać.

## <a name="bkmk_results"></a> wyniki

Po model jest uczony:

+ Aby wyświetlić parametry modelu i wagi funkcji, kliknij prawym przyciskiem myszy dane wyjściowe, a następnie wybierz pozycję **Visualize**.
+ Do korzystania z modelu w innych eksperymentów, kliknij prawym przyciskiem myszy model, a następnie wybierz **Zapisywanie modelu**. Wpisz nazwę modelu. 

    Spowoduje to zapisanie modelu jako migawkę, który nie jest aktualizowany przez powtarzanych przebiegów eksperymentu.
+ Aby użyć modelu w przewidywaniu nowe wartości, połącz go [Score Model](./score-model.md) moduł wraz z nowe dane wejściowe.


## <a name="next-steps"></a>Kolejne kroki

Zobacz [zestaw dostępnych modułów](module-reference.md) do usługi Azure Machine Learning. 