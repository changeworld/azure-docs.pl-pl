---
title: 'Wieloklasowe drzewo decyzyjne z premią: odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak użyć modułu Wieloklasowego drzewa decyzji promowanego w usłudze Azure Machine Learning, aby utworzyć klasyfikator przy użyciu danych oznaczonych etykietą.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/19/2020
ms.openlocfilehash: 7d51e3007b7773e28d846f8d30178426f5668cfb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77920063"
---
# <a name="multiclass-boosted-decision-tree"></a>Wieloklasowe wzmocnione drzewo decyzyjne

W tym artykule opisano moduł w projektancie usługi Azure Machine Learning (wersja zapoznawcza).

Ten moduł służy do tworzenia modelu uczenia maszynowego, który jest oparty na algorytmie wzmocnionej decyzji.

Zwiększone drzewo decyzyjne jest metodą uczenia zespołu, w której drugie drzewo koryguje błędy pierwszego drzewa, trzecie drzewo koryguje błędy pierwszego i drugiego drzewa i tak dalej. Prognozy są oparte na zespole drzew razem.

## <a name="how-to-configure"></a>Jak skonfigurować 

Ten moduł tworzy nieprzeszkolony model klasyfikacji. Ponieważ klasyfikacja jest nadzorowaną metodą uczenia się, potrzebny jest *oznaczony zestaw danych zawierający* kolumnę etykiet z wartością dla wszystkich wierszy.

Tego typu modelu można trenować za pomocą [modelu pociągu](././train-model.md). 

1.  Dodaj moduł **Wieloklasowego wzmocnionego drzewa decyzyjnego** do potoku.

1.  Określ sposób trenowania modelu, ustawiając opcję **Utwórz tryb trenera.**

    + **Pojedynczy parametr:** Jeśli wiesz, jak chcesz skonfigurować model, możesz podać określony zestaw wartości jako argumenty.
    
    + **Zakres parametrów:** Wybierz tę opcję, jeśli nie masz pewności co do najlepszych parametrów i chcesz uruchomić wyciągnięcie po parametrze. Wybierz zakres wartości, aby iterować ponad i [Tune Model Hyperparameters](tune-model-hyperparameters.md) iteruje nad wszystkimi możliwymi kombinacjami ustawień podanych w celu określenia hiperparametrów, które dają optymalne wyniki.  

1. **Maksymalna liczba liści na drzewo** ogranicza maksymalną liczbę węzłów końcowych (liści), które mogą być tworzone w dowolnym drzewie.
    
        By increasing this value, you potentially increase the size of the tree and achieve higher precision, at the risk of overfitting and longer training time.
  
1. **Minimalna liczba próbek na węzeł liścia** wskazuje liczbę przypadków wymaganych do utworzenia dowolnego węzła terminala (liścia) w drzewie.  

         By increasing this value, you increase the threshold for creating new rules. For example, with the default value of 1, even a single case can cause a new rule to be created. If you increase the value to 5, the training data would have to contain at least five cases that meet the same conditions.

1. **Szybkość uczenia się** określa rozmiar kroku podczas nauki. Wprowadź liczbę z 0 do 1.

         The learning rate determines how fast or slow the learner converges on an optimal solution. If the step size is too large, you might overshoot the optimal solution. If the step size is too small, training takes longer to converge on the best solution.

1. **Liczba zbudowanych drzew** wskazuje całkowitą liczbę drzew decyzyjnych do utworzenia w zespole. Tworząc więcej drzew decyzyjnych, można potencjalnie uzyskać lepszy zasięg, ale czas szkolenia wzrośnie.

1. **Inicjator liczb losowych** opcjonalnie ustawia nieujemną liczbę całkowitą do użycia jako losową wartość materiału siewnego. Określanie materiału siewnego zapewnia odtwarzalność w przebiegach, które mają te same dane i parametry.  

         The random seed is set by default to 42. Successive runs using different random seeds can have different results.

> [!Note]
> Jeśli **ustawisz tryb Utwórz tryb trenera** na **Pojedynczy parametr,** połącz oznakowany zestaw danych z modułem [Model pociągu.](./train-model.md)

## <a name="next-steps"></a>Następne kroki

Zobacz [zestaw modułów dostępnych dla](module-reference.md) usługi Azure Machine Learning. 
