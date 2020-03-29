---
title: 'Dwuklasowe drzewo decyzyjne z doładowaniem: odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak użyć modułu drzewa decyzji z zachowaniem wzmocnionej dwuklasowej usługi w usłudze Azure Machine Learning w celu utworzenia modelu uczenia maszynowego opartego na algorytmie promowanych drzew decyzyjnych.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 1d144a48f79e59b35c88c5b338747d3186ebceda
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77920743"
---
# <a name="two-class-boosted-decision-tree-module"></a>Moduł dwuklasowego wzmocnionego drzewa decyzyjnego

W tym artykule opisano moduł w projektancie usługi Azure Machine Learning (wersja zapoznawcza).

Ten moduł służy do tworzenia modelu uczenia maszynowego, który jest oparty na algorytmie wzmocnionej decyzji. 

Zwiększone drzewo decyzyjne jest metodą uczenia zespołu, w której drugie drzewo koryguje błędy pierwszego drzewa, trzecie drzewo koryguje błędy pierwszego i drugiego drzewa i tak dalej.  Prognozy są oparte na cały zespół drzew razem, który sprawia, że przewidywanie.
  
Ogólnie rzecz biorąc, po prawidłowym skonfigurowaniu, wzmocnione drzewa decyzyjne są najprostszymi metodami, za pomocą których można uzyskać najwyższą wydajność w wielu różnych zadaniach uczenia maszynowego. Jednak są one również jednym z bardziej intensywnie korzystających z pamięci, a bieżąca implementacja posiada wszystko w pamięci. W związku z tym model drzewa wzmocnionego może nie być w stanie przetworzyć dużych zestawów danych, które mogą obsługiwać niektórzy uczniowie liniowi.

## <a name="how-to-configure"></a>Jak skonfigurować

Ten moduł tworzy nieprzeszkolony model klasyfikacji. Ponieważ klasyfikacja jest nadzorowaną metodą uczenia się, aby trenować model, potrzebujesz *oznakowanego zestawu danych,* który zawiera kolumnę etykiet z wartością dla wszystkich wierszy.

Tego typu modelu można trenować za pomocą [modelu pociągu](././train-model.md). 

1.  W usłudze Azure Machine Learning dodaj moduł **promowane drzewo decyzyjne** do potoku.
  
2.  Określ sposób, w jaki model ma być trenowany, ustawiając opcję **Utwórz tryb trenera.**
  
    + **Pojedynczy parametr:** Jeśli wiesz, jak chcesz skonfigurować model, możesz podać określony zestaw wartości jako argumenty.
  
    + **Zakres parametrów:** Jeśli nie masz pewności co do najlepszych parametrów, możesz znaleźć optymalne parametry za pomocą modułu [Tune Model Hyperparameters.](tune-model-hyperparameters.md) Należy podać zakres wartości, a trener iteruje przez wiele kombinacji ustawień, aby określić kombinację wartości, która daje najlepszy wynik.
  
3.  W przypadku **maksymalnej liczby liści na drzewo**należy wskazać maksymalną liczbę węzłów zaciskowych (liści), które można utworzyć w dowolnym drzewie.
  
     Zwiększając tę wartość, potencjalnie zwiększyć rozmiar drzewa i uzyskać lepszą precyzję, na ryzyko overfitting i dłuższy czas szkolenia.
  
4.  Dla **minimalnej liczby próbek na węzeł liścia,** należy wskazać liczbę przypadków wymaganych do utworzenia dowolnego węzła terminala (liść) w drzewie.  
  
     Zwiększając tę wartość, można zwiększyć próg tworzenia nowych reguł. Na przykład z wartością domyślną 1, nawet pojedynczy przypadek może spowodować utworzenie nowej reguły. Jeśli zwiększysz wartość do 5, dane szkoleniowe będą musiały zawierać co najmniej pięć przypadków, które spełniają te same warunki.
  
5.  W przypadku **wskaźnika uczenia się**wpisz liczbę z 0 a 1, która definiuje rozmiar kroku podczas nauki.  
  
     Szybkość uczenia się określa, jak szybko lub wolno uczący się zbiega się w optymalnym rozwiązaniu. Jeśli rozmiar kroku jest zbyt duży, można przekroczyć optymalne rozwiązanie. Jeśli rozmiar kroku jest zbyt mały, szkolenie trwa dłużej, aby zbiegać się na najlepsze rozwiązanie.
  
6.  W przypadku **liczby zbudowanych drzew**należy wskazać całkowitą liczbę drzew decyzyjnych do utworzenia w zespole. Tworząc więcej drzew decyzyjnych, można potencjalnie uzyskać lepszy zasięg, ale czas szkolenia wzrośnie.
  
     Ta wartość kontroluje również liczbę drzew wyświetlanych podczas wizualizacji modelu uczonego. jeśli chcesz wyświetlić lub wydrukować pojedyncze drzewo, ustaw wartość na 1. Jednak po wykonaniu tej czynności tylko jedno drzewo jest produkowany (drzewo z początkowym zestawem parametrów) i nie dalsze iteracje są wykonywane.
  
7.  W polu **Ilość materiału siewnego liczb losowych**opcjonalnie wpisz nieujemną liczbę całkowitą, która ma być używana jako losowa wartość materiału siewnego. Określanie materiału siewnego zapewnia odtwarzalność w przebiegach, które mają te same dane i parametry.  
  
     Losowy materiał siewny jest domyślnie ustawiony na 0, co oznacza, że początkowa wartość materiału siewnego jest uzyskiwana z zegara systemowego.  Kolejne przebiegi przy użyciu losowego materiału siewnego mogą mieć różne wyniki.
  

9. Trenuj model.
  
    + Jeśli **ustawisz tryb Utwórz tryb trenera** na **Pojedynczy parametr,** połącz oznakowany zestaw danych z modułem [Model pociągu.](./train-model.md)  
   
## <a name="results"></a>Wyniki

Po zakończeniu szkolenia:

+ Aby zapisać migawkę uczonego modelu, wybierz kartę **Wyjścia** w prawym panelu modułu **Modelu pociągu.** Wybierz ikonę **Zarejestruj zestaw danych,** aby zapisać model jako moduł wielokrotnego wyboru.

+ Aby użyć modelu do oceniania, dodaj **wynik modelu** modułu do potoku.

## <a name="next-steps"></a>Następne kroki

Zobacz [zestaw modułów dostępnych dla](module-reference.md) usługi Azure Machine Learning. 