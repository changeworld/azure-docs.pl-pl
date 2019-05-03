---
title: 'Regresja logistyczna Two-Class: Odwołania do modułu'
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak użyć modułu Two-Class regresji logistycznej w usłudze Azure Machine Learning, aby utworzyć model regresji logistycznej, który może służyć do przewidywania wyników dwóch (i tylko dwa).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: aacaf6c64ef77d0e694f97e3675060eca33794ed
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029253"
---
# <a name="two-class-logistic-regression-module"></a>Moduł regresji logistycznej Two-Class

W tym artykule opisano moduł interfejs graficzny (wersja zapoznawcza) dla usługi Azure Machine Learning.

Ten moduł służy do tworzenia modelu regresji logistycznej, który może służyć do przewidywania wyników dwóch (i tylko dwa). 

Regresja logistyczna jest dobrze znane techniki statystyczne, który służy do modelowania wiele rodzajów problemów. Ten algorytm stanowi *uczenia nadzorowanego* metody.  w związku z tym należy podać zestaw danych, który zawiera już wyniki do nauczenia modelu.  

### <a name="about-logistic-regression"></a>Regresja logistyczna — informacje  

Regresja logistyczna jest metodą dobrze znane w statystyce, jest używany w celu przewidywania prawdopodobieństwa spisania wynik, która jest szczególnie popularnych zadań klasyfikacji. Algorytm przewiduje prawdopodobieństwo wystąpienia zdarzenia, przybliżając dane logistyczne funkcji.
  
W tym module algorytm klasyfikacji jest zoptymalizowany pod kątem zmiennych dichotomous lub binarne. Jeśli potrzebujesz do klasyfikowania wiele wyników, użyj [regresji logistycznej kontra](./multiclass-logistic-regression.md) modułu.

##  <a name="how-to-configure"></a>Jak skonfigurować  

Do nauczenia modelu, należy podać zestaw danych, który zawiera kolumnę etykiety lub klasy. Ponieważ ten moduł jest przeznaczony dla problemów dwuklasowych, kolumna etykiety lub klasa musi zawierać dokładnie dwie wartości. 

Na przykład etykieta kolumna może być [pod głosowanie] za pomocą możliwych wartości "Yes" lub "No". Lub może być [ryzyka kredytowego] z możliwych wartości "Wysoka" lub "Low". 
  
1.  Dodaj **regresji logistycznej Two-Class** modułu do eksperymentu.  
  
2.  Określ, jak model, który ma być uczony, ustawiając **trybie trainer tworzenia** opcji.  
  
    -   **Pojedynczy parametr**: Jeśli wiesz, jak chcesz skonfigurować modelu, możesz podać określonych wartości jako argumenty.  
  
3.  Aby uzyskać **tolerancji optymalizacji**, podaj wartość progową, aby użyć podczas optymalizacji modelu. Jeśli poprawy między poszczególnymi iteracjami spadnie poniżej określonego progu, algorytm jest uważany za mają zbieżności na rozwiązanie i zatrzymuje szkolenia.  
  
4.  Dla **wagi uregulowania L1** i **wagi uregulowania L2**, wpisz wartości dla parametrów uregulowania P1 i P2. Wartość niezerową jest zalecane dla obu.  
  
     *Uregulowania* to metoda zapobieganie overfitting niedyskryminacyjną modeli przy użyciu wartości skrajnych współczynnik. Uregulowania działa przez dodanie spadek, skojarzony z wartości współczynnika błędu hipotezę. W związku z tym dokładne modelu przy użyciu wartości skrajnych współczynnik będzie bardziej karane, ale mniej dokładne modelu przy użyciu wartości bardziej konserwatywnego będzie karane mniej.  
  
     P1 i P2 uregulowania dają różne efekty i używa.  
  
    -   L1 można zastosować do modeli rozrzedzonych, co jest przydatne podczas pracy z danymi o wielu wymiarach.  
  
    -   Z kolei uregulowania L2 jest preferowane dla danych, który nie jest rozrzedzony.  
  
     Ten algorytm obsługuje liniowej kombinacja wartości uregulowania P1 i P2: oznacza to, jeśli <code>x = L1</code> i <code>y = L2</code>, następnie <code>ax + by = c</code> definiuje zakres liniowy warunki uregulowania.  
  
    > [!NOTE]
    >  Chcesz dowiedzieć się więcej na temat uregulowania P1 i P2? Następujący artykuł zawiera omówienie sposobu uregulowania P1 i P2 są różne, i ich wpływ na dopasowanie modelu, przy użyciu przykładów kodu dla modeli sieci neuronowych i regresji logistycznej:  [P1 i P2 uregulowania dla usługi Machine Learning](https://msdn.microsoft.com/magazine/dn904675.aspx)  
    >
    > Różne kombinacje liniowej P1 i P2 warunki mają został zaprojektowany dla modele regresji logistycznej: na przykład [elastycznej uregulowania netto](https://wikipedia.org/wiki/Elastic_net_regularization). Zaleca się, że odwołujesz się te kombinacje do zdefiniowania kombinacji liniowej, która ma zastosowanie w modelu.
      
5.  Dla **rozmiar pamięci L BFGS**, określ ilość pamięci do wykorzystania przez *L BFGS* optymalizacji.  
  
     L-BFGS oznacza "ograniczonej pamięci Broyden-Fletcher-Goldfarb-Shanno". Jest algorytm optymalizacji, którego znaną szacowania parametru. Ten parametr określa liczbę ostatnich pozycji i gradientów do przechowywania na potrzeby obliczania następnego kroku.  
  
     Ten parametr optymalizacji ogranicza ilość pamięci, która jest używana do obliczania następnego kroku i kierunku. Po określeniu mniej pamięci, szkolenie jest szybsze, ale mniej dokładne.  
  
6.  Aby uzyskać **inicjatora liczb losowych**, wpisać wartość całkowitą. Definiowanie wartości inicjatora jest ważne, jeśli chcesz, aby wyniki, aby zostać powtórzone, za pośrednictwem wielu uruchomień z tym samym eksperymencie.  
  
  
8. Dodaj oznakowane zestaw danych do eksperymentu i połącz jeden z [modułów szkoleniowych](module-reference.md).  
  
    -   Jeśli ustawisz **trybie trainer tworzenia** do **pojedynczy parametr**, użyj [Train Model](./train-model.md) modułu.  
  
9. Uruchom eksperyment.  
  
## <a name="results"></a>Wyniki

Po zakończeniu szkolenia:

+ Aby wyświetlić podsumowanie parametrów modelu, wraz z wagi funkcji z szkolenia, kliknij prawym przyciskiem myszy dane wyjściowe [Train Model](./train-model.md) i wybierz **Visualize**.   
  
+ Aby tworzyć prognozy na nowe dane, należy użyć jako dane wejściowe trenowanego modelu, a nowych danych [Score Model](./score-model.md) modułu. 


## <a name="next-steps"></a>Kolejne kroki

Zobacz [zestaw dostępnych modułów](module-reference.md) do usługi Azure Machine Learning. 