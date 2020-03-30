---
title: 'Regresja logistyczna dwóch klas: odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak użyć modułu regresji logistycznej dwuklasowej w usłudze Azure Machine Learning, aby utworzyć model regresji logistycznej, który może służyć do przewidywania dwóch (i tylko dwóch) wyników.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 7eb1ad00c3c947c3ed6d4ca450bddc0956a08d71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79455829"
---
# <a name="two-class-logistic-regression-module"></a>Dwuklasowy moduł regresji logistycznej

W tym artykule opisano moduł w projektancie usługi Azure Machine Learning (wersja zapoznawcza).

Ten moduł służy do tworzenia modelu regresji logistycznej, który może służyć do przewidywania dwóch (i tylko dwa) wyników. 

Regresja logistyczna jest dobrze znaną techniką statystyczną, która jest używana do modelowania wielu rodzajów problemów. Algorytm ten jest nadzorowaną metodą *uczenia się;*  w związku z tym należy podać zestaw danych, który już zawiera wyniki do uczenia modelu.  

### <a name="about-logistic-regression"></a>Regresja logistyczna — informacje  

Regresja logistyczna jest dobrze znaną metodą w statystykach, która jest używana do przewidywania prawdopodobieństwa wyniku i jest szczególnie popularna w przypadku zadań klasyfikacji. Algorytm przewiduje prawdopodobieństwo wystąpienia zdarzenia przez dopasowanie danych do funkcji logistycznej.
  
W tym module algorytm klasyfikacji jest zoptymalizowany pod kątem zmiennych dychotomicznych lub binarnych. Jeśli trzeba sklasyfikować wiele wyników, należy użyć [multiklasy logistycznej regresji](./multiclass-logistic-regression.md) modułu.

##  <a name="how-to-configure"></a>Jak skonfigurować  

Aby uszkodywać ten model, należy podać zestaw danych zawierający etykietę lub kolumnę klasy. Ponieważ ten moduł jest przeznaczony dla problemów dwuklasowych, etykieta lub kolumna klasy musi zawierać dokładnie dwie wartości. 

Na przykład kolumna etykiety może być [Głosowana] z możliwymi wartościami "Tak" lub "Nie". Lub może to być [Ryzyko kredytowe], z możliwymi wartościami "Wysoki" lub "Niski". 
  
1.  Dodaj moduł **regresji logistycznej dwóch klas** do potoku.  
  
2.  Określ sposób, w jaki model ma być trenowany, ustawiając opcję **Utwórz tryb trenera.**  
  
    -   **Pojedynczy parametr:** Jeśli wiesz, jak chcesz skonfigurować model, możesz podać określony zestaw wartości jako argumenty.  

    -   **Zakres parametrów:** Jeśli nie masz pewności co do najlepszych parametrów, możesz znaleźć optymalne parametry za pomocą modułu [Tune Model Hyperparameters.](tune-model-hyperparameters.md) Należy podać zakres wartości, a trener iteruje przez wiele kombinacji ustawień, aby określić kombinację wartości, która daje najlepszy wynik.
  
3.  W polu **Tolerancja optymalizacji**należy określić wartość progową używaną podczas optymalizacji modelu. Jeśli poprawa między iteracjami spadnie poniżej określonego progu, algorytm jest uważany za zbieżne na rozwiązanie i szkolenia zatrzymuje.  
  
4.  Dla **masy regularyzacji L1** i **masy regularyzacji L2**wpisz wartość do użycia dla parametrów regularyzacji L1 i L2. Wartość niezerowa jest zalecana dla obu.  
     *Regularization* jest metodą zapobiegania nadmiernemu dosyć przez karanie modeli o ekstremalnych wartościach współczynnika. Prawidłowość działa przez dodanie kary, która jest skojarzona z wartościami współczynnika do błędu hipotezy. W ten sposób dokładny model o ekstremalnych wartościach współczynnika zostałby bardziej ukarany, ale mniej dokładny model o bardziej konserwatywnych wartościach zostałby ukarany mniej.  
  
     L1 i L2 regularizacji mają różne efekty i zastosowań.  
  
    -   L1 może być stosowany do modeli rozrzedzonych, co jest przydatne podczas pracy z danymi o wysokiej wymiarze.  
  
    -   Natomiast regularyzacja L2 jest korzystniejsza dla danych, które nie są rzadkie.  
  
     Algorytm ten obsługuje liniową kombinację wartości regularyzacji L1 i <code>x = L1</code> <code>y = L2</code>L2: oznacza to, że if i , następnie <code>ax + by = c</code> definiuje zakres liniowy terminów regularyzacji.  
  
    > [!NOTE]
    >  Chcesz dowiedzieć się więcej o legalizacji L1 i L2? Poniższy artykuł zawiera omówienie, jak L1 i L2 regularizacji są różne i jak wpływają one na dopasowanie modelu, z przykładami kodu dla regresji logistycznej i modeli sieci neuronowych: [L1 i L2 Regularization for Machine Learning](https://msdn.microsoft.com/magazine/dn904675.aspx)  
    >
    > Dla modeli regresji logistycznej opracowano różne kombinacje liniowe terminów L1 i L2: na przykład [elastyczna legalizacja sieci.](https://wikipedia.org/wiki/Elastic_net_regularization) Sugerujemy odwołanie się do tych kombinacji, aby zdefiniować kombinację liniową, która jest skuteczna w modelu.
      
5.  W przypadku **rozmiaru pamięci dla L-BFGS**należy określić ilość pamięci używanej do optymalizacji *L-BFGS.*  
  
     L-BFGS oznacza "ograniczoną pamięć Broyden-Fletcher-Goldfarb-Shanno". Jest to algorytm optymalizacji, który jest popularny do szacowania parametrów. Ten parametr wskazuje liczbę przeszłych pozycji i gradientów do przechowywania w celu obliczenia następnego kroku.  
  
     Ten parametr optymalizacji ogranicza ilość pamięci, która jest używana do obliczania następnego kroku i kierunku. Po określeniu mniej pamięci, szkolenie jest szybsze, ale mniej dokładne.  
  
6.  W polu **Siewna liczb losowych**wpisz wartość całkowitą. Definiowanie wartości inicjatora jest ważne, jeśli chcesz, aby wyniki były powtarzalne w wielu przebiegach tego samego potoku.  
  
  
8. Dodaj oznakowany zestaw danych do potoku i połącz jeden z [modułów szkoleniowych](module-reference.md).  
  
    -   Jeśli **ustawisz tryb Utwórz tryb trenera** na **Pojedynczy parametr,** użyj modułu [Model pociągu.](./train-model.md)  
  
9. Prześlij potok.  
  
## <a name="results"></a>Wyniki

Po zakończeniu szkolenia:
 
  
+ Aby prognozować na nowe dane, należy użyć przeszkolonego modelu i nowych danych jako dane wejściowe do [modelu wynik](./score-model.md) modułu. 


## <a name="next-steps"></a>Następne kroki

Zobacz [zestaw modułów dostępnych dla](module-reference.md) usługi Azure Machine Learning. 