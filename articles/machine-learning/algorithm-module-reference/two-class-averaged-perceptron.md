---
title: 'Regresja lasu decyzji: odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak użyć dwuklasowego modułu Perceptron uśrednionego w usłudze Azure Machine Learning, aby utworzyć model uczenia maszynowego na podstawie algorytmu perceptron uśrednionego.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 73e23dd7d350ea63e9fd8b933a525a9d8aad9e3e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77920777"
---
# <a name="two-class-averaged-perceptron-module"></a>Dwuklasowy moduł perceptron uśredniony

W tym artykule opisano moduł w projektancie usługi Azure Machine Learning (wersja zapoznawcza).

Ten moduł służy do tworzenia modelu uczenia maszynowego na podstawie algorytmu perceptron uśredniony.  
  
Ten algorytm klasyfikacji jest nadzorowaną metodą uczenia się i wymaga *oznakowanego zestawu danych,* który zawiera kolumnę etykiet. Model można trenować, udostępniając model i oznakowany zestaw danych jako dane wejściowe do [modelu pociągu](./train-model.md). Przeszkolony model może następnie służyć do przewidywania wartości dla nowych przykładów wejściowych.  

### <a name="about-averaged-perceptron-models"></a>Informacje o uśrednionych modelach perceptronowych

*Uśredniona metoda perceptronu* jest wczesną i prostą wersją sieci neuronowej. W tym podejściu dane wejściowe są klasyfikowane do kilku możliwych wyjść na podstawie funkcji liniowej, a następnie łączone z zestawem wag, które są pochodne od wektora operacji , stąd nazwa "perceptron".

Prostsze modele perceptronu nadają się do uczenia się liniowo rozdzielnych wzorców, podczas gdy sieci neuronowe (szczególnie głębokie sieci neuronowe) mogą modelować bardziej złożone granice klas. Jednak perceptrony są szybsze, a ponieważ przetwarzają sprawy szeregowo, perceptrony mogą być używane z ciągłym treningiem.

## <a name="how-to-configure-two-class-averaged-perceptron"></a>Jak skonfigurować dwuklasowy perceptron uśredniony

1.  Dodaj do potoku moduł **Perceptron uśredniony** dwuklasowy.  

2.  Określ sposób, w jaki model ma być trenowany, ustawiając opcję **Utwórz tryb trenera.**  
  
    -   **Pojedynczy parametr:** Jeśli wiesz, jak chcesz skonfigurować model, podaj określony zestaw wartości jako argumenty.

    -   **Zakres parametrów:** Wybierz tę opcję, jeśli nie masz pewności co do najlepszych parametrów i chcesz uruchomić wyciągnięcie po parametrze. Wybierz zakres wartości, aby iterować ponad i [Tune Model Hyperparameters](tune-model-hyperparameters.md) iteruje nad wszystkimi możliwymi kombinacjami ustawień podanych w celu określenia hiperparametrów, które dają optymalne wyniki.  
  
3.  W polu **Wskaźnik uczenia się**określ wartość *wskaźnika uczenia się*. Wartości szybkości uczenia się kontrolują rozmiar kroku, który jest używany w gradientu stochastycznego zejścia za każdym razem, gdy model jest testowany i korygowany.
  
     Dzięki mniejszej szybkości, można przetestować model częściej, z ryzykiem, że może utknąć w lokalnym płaskowyżu. Dzięki pomniejszeniu kroku można zbiegać się szybciej, ryzykując przekroczenie prawdziwych minimów.
  
4.  W przypadku **maksymalnej liczby iteracji**wpisz liczbę razy, w których algorytm ma sprawdzać dane szkoleniowe.  
  
     Wczesne zatrzymanie często zapewnia lepsze uogólnienie. Zwiększenie liczby iteracji poprawia dopasowanie, na ryzyko overfitting.
  
5.  W polu **Ilość materiału siewnego liczb losowych**opcjonalnie wpisz wartość całkowitą, która ma być używana jako materiał siewny. Za pomocą materiału siewnego jest zalecane, jeśli chcesz zapewnić odtwarzalność potoku przez przebiegi.  
  
1.  Połącz zestaw danych szkoleniowych i jeden z modułów szkoleniowych:
  
    -   Jeśli **ustawisz tryb Utwórz tryb trenera** na **Pojedynczy parametr,** użyj modułu [Model pociągu.](train-model.md)




## <a name="next-steps"></a>Następne kroki

Zobacz [zestaw modułów dostępnych dla](module-reference.md) usługi Azure Machine Learning. 