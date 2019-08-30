---
title: 'Regresja lasu decyzyjnego: Dokumentacja modułu'
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak utworzyć model uczenia maszynowego na podstawie średniego algorytmu Perceptron przy użyciu dwuklasowego modułu Perceptron w usłudze Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: f560923b0a5457ac5fd03c7f76fc4315c6ca08e8
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128427"
---
# <a name="two-class-averaged-perceptron-module"></a>Moduł Perceptron o średniej klasie

W tym artykule opisano moduł Visual Interface (wersja zapoznawcza) dla usługi Azure Machine Learning.

Ten moduł służy do tworzenia modelu uczenia maszynowego na podstawie średniego algorytmu Perceptron.  
  
Ten algorytm klasyfikacji to nadzorowana Metoda uczenia i wymaga *oznakowanego zestawu danych*, który zawiera kolumnę etykiet. Możesz nauczyć model, dostarczając model i otagowany zestaw danych jako dane wejściowe do [modelu uczenia](./train-model.md). Model przeszkolony może być następnie używany do przewidywania wartości dla nowych przykładów wejściowych.  

### <a name="about-averaged-perceptron-models"></a>Informacje o średnich modelach Perceptron

*Średnia Metoda Perceptron* to wczesna i prosta wersja sieci neuronowych. W tym podejściu dane wejściowe są klasyfikowane do kilku możliwych wyjść opartych na funkcji liniowej, a następnie połączone z zestawem wag, które pochodzą od wektora funkcji, dlatego nazwa "Perceptron".

Prostsze modele Perceptron są odpowiednie do uczenia się liniowo oddzielnych wzorców, natomiast sieci neuronowych (zwłaszcza głębokie sieci neuronowych) mogą modelować bardziej złożone granice klas. Perceptrons są jednak szybsze i ponieważ przetwarzają przypadki seryjnie, Perceptrons może być używany z ciągłym szkoleniem.

## <a name="how-to-configure-two-class-averaged-perceptron"></a>Jak skonfigurować Perceptron średniej klasy dwuklasowej

1.  Dodaj do eksperymentu moduł **Perceptron o średniej klasie** .  

2.  Określ, w jaki sposób ma być szkolony model, ustawiając opcję **tworzenia trybu Trainer** .  
  
    -   **Pojedynczy parametr**: Jeśli wiesz, jak chcesz skonfigurować model, podaj określony zestaw wartości jako argumenty.
  
3.  Wpolu Stawka szkoleniowa Określ wartość dla *stawki szkoleniowej*. Wartości współczynnika uczenia kontrolują rozmiar kroku, który jest używany w gradiencie stochastycznego przy każdym przetestowaniu i skorygowaniu modelu.
  
     Zmniejszając szybkość, można testować model częściej, z ryzykiem, który może zostać zablokowany w lokalnej Plateau. Dzięki powiększeniu tego kroku można szybciej łączyć się z ryzykiem w przypadku przekroczenia rzeczywistych wartości.
  
4.  Dla **maksymalnej liczby iteracji**wpisz, ile razy algorytm ma przeanalizować dane szkoleniowe.  
  
     Zatrzymywanie wczesne często zapewnia lepszy sposób uogólniania. Zwiększenie liczby iteracji usprawnia dopasowanie w przypadku naruszenia.
  
5.  W przypadku **liczby losowej inicjatora**opcjonalnie wpisz wartość całkowitą, która ma być używana jako inicjator. Użycie inicjatora jest zalecane, jeśli chcesz zapewnić odtwarzalność eksperymentu w ramach przebiegów.  
  
1.  Połącz zestaw danych szkoleniowych i jeden z modułów szkoleniowych:
  
    -   W przypadku ustawienia opcji **Utwórz tryb Trainer** na **pojedynczy parametr**Użyj modułu [uczenie modelu](train-model.md) .

## <a name="results"></a>Wyniki

Po zakończeniu szkolenia:

+ Aby wyświetlić podsumowanie parametrów modelu wraz z wagami funkcji zdobywanymi od szkoleń, kliknij prawym przyciskiem myszy dane wyjściowe [modelu uczenia](./train-model.md).


## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning usługi. 