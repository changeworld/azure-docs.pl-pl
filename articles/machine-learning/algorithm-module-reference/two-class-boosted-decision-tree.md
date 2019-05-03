---
title: 'Two-Class wzmocnione drzewo decyzyjnego dla: Odwołania do modułu'
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak użyć modułu Two-Class Boosted Decision drzewa w usłudze Azure Machine Learning, aby utworzyć model, który jest oparty na algorytm drzewa decyzyjnego uczenia maszynowego.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 09ea530cac5bdbd62208f134177e5ceaccb545e2
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/02/2019
ms.locfileid: "65027948"
---
# <a name="two-class-boosted-decision-tree-module"></a>Moduł Two-Class Boosted Decision drzewa

W tym artykule opisano moduł interfejs graficzny (wersja zapoznawcza) dla usługi Azure Machine Learning.

Ten moduł służy do tworzenia modelu uczenia maszynowego, który jest oparty na algorytm drzewa decyzyjnego. 

Drzewa decyzyjnego jest zespołu uczenia metody, w którym drugi drzewa koryguje błędy pierwszy drzewa trzeci drzewa koryguje błędy drzew pierwszego i drugiego, i tak dalej.  Prognozy są oparte na cały zespół drzew razem sprawia, że prognozowania.
  
Ogólnie rzecz biorąc po poprawnym skonfigurowaniu drzewa decyzyjnego są najprostszym metody, z którego mają zostać pobrane największą wydajność na szeroką gamę zadania uczenia maszynowego. Jednak również są uczących bardziej wymagających dużej ilości pamięci, a bieżąca implementacja parametru zawiera wszystko, co w pamięci. W związku z tym modelu drzewa decyzyjnego może nie móc przetwarzania dużych zestawów danych, która może obsłużyć niektóre liniowej uczących.

## <a name="how-to-configure"></a>Jak skonfigurować

Ten moduł tworzy model nieprzeszkolonych klasyfikacji. Klasyfikacja jest metodą uczenia nadzorowanego do nauczenia modelu, dlatego konieczne *oznakowane dataset* zawierającej kolumnę etykiety o wartości we wszystkich wierszach.

Możesz uczyć modelu przy użyciu tego typu [uczenie modelu](././train-model.md). 

1.  W usłudze Azure Machine Learning, należy dodać **wzmocnione drzewo decyzyjne** modułu do eksperymentu.
  
2.  Określ, jak model, który ma być uczony, ustawiając **trybie trainer tworzenia** opcji.
  
    + **Pojedynczy parametr**: Jeśli wiesz, jak chcesz skonfigurować modelu, możesz podać określonych wartości jako argumenty.
  
  
3.  Dla **maksymalną dozwoloną liczbę pozostawia na drzewie**, wskazuje maksymalną liczbę węzłów końcowych (liści) utworzone w dowolnym drzewa.
  
     Zwiększenie tej wartości, możesz potencjalnie zwiększyć rozmiar drzewa i uzyskać lepszą dokładności, ryzyko overfitting i już szkolenia czasu.
  
4.  Aby uzyskać **minimalna liczba próbek na węzeł liścia**, określa liczbę przypadków wymaganych do utworzenia dowolnego węzła w terminalu (liść) w drzewie.  
  
     Zwiększenie tej wartości, można zwiększyć wartość progową do tworzenia nowych zasad. Na przykład wartość domyślną 1, jeden przypadek może spowodować nową regułę, która ma zostać utworzony. Jeśli zwiększysz wartość 5 dane szkoleniowe musi zawierać co najmniej pięć przypadki, które spełniają te same warunki.
  
5.  Dla **uczenia współczynnik**, wpisz liczbę od 0 do 1, który definiuje rozmiar kroku podczas nauki.  
  
     Tempo uczenia Określa, jak szybko lub wolno uczeń zbieżna dla wartości na optymalne rozwiązanie. Jeśli rozmiar kroku jest zbyt duży, może być przekroczeń optymalne rozwiązanie. Jeśli rozmiar kroku jest zbyt mały, szkolenie dłużej zbieżne najlepsze rozwiązanie.
  
6.  Dla **liczby drzew skonstruowany**, wskazywać sumę drzewa decyzyjne, które można utworzyć w zespole. Tworząc więcej drzewa decyzyjne, mogą potencjalnie uzyskać lepszego pokrycia, ale wypoczynkowych więc ceny wzrosną.
  
     Ta wartość kontroluje również liczbę drzew wyświetlane, gdy wizualizacja uczonego modelu. Jeśli chcesz wyświetlić lub wydrukować jedno drzewo, ustaw wartość na 1. Jednak jeśli tak zrobisz, tylko jeden drzewa jest generowany (drzewo za pomocą początkowego zestawu parametrów) i są wykonywane nie dalsze iteracji.
  
7.  Dla **inicjatora liczb losowych**, opcjonalnie wpisz nieujemną liczbę całkowitą jako wartość losowa. Określając inicjator zapewnia odtwarzaniem we wszystkich przebiegach aktualizacji, które mają tych samych danych i parametry.  
  
     Inicjator losowy jest domyślnie ustawiany na 0, co oznacza, że wartości początkowej są uzyskiwane z zegara systemowego.  Kolejnych przebiegów, przy użyciu Inicjator losowy mogą mieć różne wyniki.
  

9. Uczenie modelu.
  
    + Jeśli ustawisz **trybie trainer tworzenia** do **pojedynczy parametr**, Połącz oznakowane zestawu danych i [Train Model](./train-model.md) modułu.  
  
   
## <a name="results"></a>Wyniki

Po zakończeniu szkolenia modelu, kliknij prawym przyciskiem myszy dane wyjściowe [Train Model](./train-model.md) , aby wyświetlić wyniki:

+ Aby wyświetlić drzewa, który został utworzony w każdej iteracji, wybierz **Visualize**. 
+ Kliknij każdego drzewa, aby przejść do dzieli dane i wyświetlić reguły dla każdego węzła.


## <a name="next-steps"></a>Kolejne kroki

Zobacz [zestaw dostępnych modułów](module-reference.md) do usługi Azure Machine Learning. 