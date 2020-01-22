---
title: 'Dwuklasowe drzewo decyzyjne: odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak utworzyć model uczenia maszynowego oparty na algorytmie podwyższania poziomu drzewa decyzyjnego w programie Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: 1cbbc3f0c78954b92c1265795ba365be9dc0e38c
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2020
ms.locfileid: "76315086"
---
# <a name="two-class-boosted-decision-tree-module"></a>Moduł drzewa decyzyjnej z dwoma klasami

W tym artykule opisano moduł w programie Azure Machine Learning Designer.

Ten moduł służy do tworzenia modelu uczenia maszynowego, który jest oparty na algorytmie drzew z podwyższaną decyzją. 

Wzmocnione drzewo decyzyjne to metoda uczenia się, w której drugie drzewo jest zgodne z błędami pierwszego drzewa, trzecie drzewo jest odpowiednie dla błędów pierwszego i drugiego drzewa i tak dalej.  Przewidywania opierają się na całym całości drzew, które tworzą prognozę.
  
Ogólnie rzecz biorąc, w przypadku prawidłowego skonfigurowania, podwyższane drzewa decyzyjne to najłatwiejsze metody umożliwiające uzyskanie najwyższej wydajności na szeroką gamę zadań uczenia maszynowego. Jednak są one również jednym z większej ilości informacji, a bieżąca implementacja zawiera wszystko w pamięci. W związku z tym, wzmocniony Model drzewa decyzyjnego może nie być w stanie przetwarzać dużych zestawów danych, które mogą obsłużyć pewne dojścia liniowe.

## <a name="how-to-configure"></a>Jak skonfigurować

Ten moduł tworzy niepociąg model klasyfikacji. Ponieważ klasyfikacja jest metodą uczenia nadzorowanego, do uczenia modelu, potrzebny jest *otagowany zestaw danych* , który zawiera kolumnę etykieta z wartością dla wszystkich wierszy.

Możesz przeszkolić model tego typu przy użyciu [modelu uczenia](././train-model.md). 

1.  W Azure Machine Learning Dodaj do potoku niewzmocniony moduł **drzewa decyzyjnego** .
  
2.  Określ, w jaki sposób ma być szkolony model, ustawiając opcję **tworzenia trybu Trainer** .
  
    + **Pojedynczy parametr**: Jeśli wiesz, jak chcesz skonfigurować model, możesz podać określony zestaw wartości jako argumenty.
  
  
3.  **Maksymalna liczba liści na drzewo**wskazuje maksymalną liczbę węzłów terminalu (liści), które można utworzyć w dowolnym drzewie.
  
     Zwiększając tę wartość, można zwiększyć rozmiar drzewa i uzyskać lepszą precyzję w przypadku ryzyka przekroczenia i dłuższego czasu uczenia się.
  
4.  Dla **minimalnej liczby próbek na węzeł liścia**wskaż liczbę przypadków wymaganych do utworzenia dowolnego węzła terminalu w drzewie.  
  
     Zwiększenie tej wartości spowoduje zwiększenie wartości progowej tworzenia nowych reguł. Na przykład, z wartością domyślną 1, nawet pojedynczy przypadek może spowodować utworzenie nowej reguły. W przypadku zwiększenia wartości do 5 dane szkoleniowe muszą zawierać co najmniej pięć przypadków, które spełniają te same warunki.
  
5.  W polu **stawka szkoleniowa**wpisz liczbę z zakresu od 0 do 1, która definiuje rozmiar kroku podczas uczenia się.  
  
     Szybkość uczenia określa, jak szybko lub wolno dowiedzieć się zbieżność z optymalnym rozwiązaniem. Jeśli rozmiar tego kroku jest zbyt duży, można przekroczyć optymalne rozwiązanie. Jeśli rozmiar tego kroku jest zbyt mały, szkolenie trwa dłużej niż w przypadku najlepszego rozwiązania.
  
6.  Dla **liczby skonstruowanych drzew**wskazuje łączną liczbę drzew decyzyjnych do utworzenia w całości. Przez utworzenie większej liczby drzew decyzyjnych można potencjalnie uzyskać lepszy zakres, ale zwiększy się czas uczenia.
  
     Ta wartość kontroluje również liczbę drzew wyświetlanych podczas wizualizacji przeszkolonego modelu. Jeśli chcesz zobaczyć lub wydrukować pojedyncze drzewo, ustaw wartość na 1. Jednak w takim przypadku tworzone jest tylko jedno drzewo (drzewo z początkowym zestawem parametrów) i nie są wykonywane żadne dalsze iteracje.
  
7.  W przypadku **liczby losowej inicjatora**, opcjonalnie wpisz nieujemną liczbę całkowitą do użycia jako losową wartość inicjatora. Określenie inicjatora zapewnia odtwarzalność w przebiegach, które mają te same dane i parametry.  
  
     Losowy inicjator jest domyślnie ustawiony na wartość 0, co oznacza, że początkowa wartość inicjatora jest uzyskiwana z zegara systemowego.  Kolejne uruchomienia przy użyciu losowego inicjatora mogą mieć różne wyniki.
  

9. Uczenie modelu.
  
    + Jeśli ustawisz **tryb tworzenia Trainer** na **pojedynczy parametr**, Połącz znacznikowy zestaw danych i moduł [uczenie modelu](./train-model.md) .  
   
## <a name="results"></a>Wyniki

Po zakończeniu szkolenia:

+ Aby zapisać migawkę przeszkolonego modelu, wybierz kartę dane **wyjściowe** w prawym panelu modułu **uczenie modelu** . Wybierz ikonę **zarejestruj zestaw danych** , aby zapisać model jako moduł wielokrotnego użytku.

+ Aby użyć modelu do oceniania, Dodaj moduł **model oceny** do potoku.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning. 