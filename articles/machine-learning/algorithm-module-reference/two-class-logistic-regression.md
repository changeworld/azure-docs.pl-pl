---
title: 'Regresja logistyczna dla dwóch klas: Dokumentacja modułu'
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak utworzyć model regresji logistycznej w usłudze Azure Machine Learning przy użyciu dwuklasowego modułu regresji logistycznej, który może być używany do przewidywania dwóch (i tylko dwóch) rezultatów.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 818b8627decd5ee7db711abc417f71c83e32b6c0
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128382"
---
# <a name="two-class-logistic-regression-module"></a>Moduł regresji logistycznej dla dwóch klas

W tym artykule opisano moduł Visual Interface (wersja zapoznawcza) dla usługi Azure Machine Learning.

Użyj tego modułu, aby utworzyć model regresji logistycznej, który może służyć do przewidywania dwóch (i tylko dwóch) wyników. 

Regresja logistyczna to dobrze znana technika statystyczna, która jest używana do modelowania wielu rodzajów problemów. Ten algorytm to *nadzorowana* Metoda uczenia się;  w związku z tym musisz dostarczyć zestaw danych, który zawiera już wyniki do uczenia modelu.  

### <a name="about-logistic-regression"></a>Regresja logistyczna — informacje  

Regresja logistyczna to dobrze znana metoda w statystyce, która jest używana do przewidywania prawdopodobieństwa wyniku i jest szczególnie popularna w przypadku zadań klasyfikacji. Algorytm przewiduje prawdopodobieństwo wystąpienia zdarzenia przez dopasowanie danych do funkcji logistycznej.
  
W tym module algorytm klasyfikacji jest zoptymalizowany pod kątem dichotomous lub zmiennych binarnych. Jeśli potrzebujesz sklasyfikować wiele wyników, użyj modułu regresja logistyczna dla wielu [klas](./multiclass-logistic-regression.md) .

##  <a name="how-to-configure"></a>Jak skonfigurować  

Aby szkolić ten model, należy dostarczyć zestaw danych, który zawiera etykietę lub kolumnę klasy. Ponieważ ten moduł jest przeznaczony do rozwiązywania problemów z dwoma klasami, etykieta lub kolumna klasy musi zawierać dokładnie dwie wartości. 

Na przykład kolumna etykieta może być [głosowana] z możliwymi wartościami "yes" lub "No". Lub może to być [ryzyko kredytowe], z możliwymi wartościami "High" lub "Low". 
  
1.  Dodaj moduł **regresja logistyczna dla dwóch klas** do eksperymentu.  
  
2.  Określ, w jaki sposób ma być szkolony model, ustawiając opcję **tworzenia trybu Trainer** .  
  
    -   **Pojedynczy parametr**: Jeśli wiesz, jak chcesz skonfigurować model, możesz podać określony zestaw wartości jako argumenty.  
  
3.  Aby uzyskać **tolerancję optymalizacji**, określ wartość progową, która ma być używana podczas optymalizowania modelu. Jeśli poprawa między iteracjami spadnie poniżej określonego progu, algorytm jest uznawany za zbieżny w rozwiązaniu, a szkolenie zostanie zatrzymane.  
  
4.  W przypadku **wag uregulowania** i **wagi L2**wpisz wartość, która ma być używana dla parametrów uregulowania L1 i L2. Dla obu tych zaleca się wartość różną od zera.  
  
     *Uregulowanie* to metoda zapobiegania zastępowaniu poprzez nakładanie się modeli o wartości skrajnego współczynnika. Uregulowanie działa przez dodanie kary, która jest skojarzona z wartościami współczynnika, do błędu hipotezy. W ten sposób dokładny model z wartościami współczynnika skrajnie byłby bardziej karany, ale mniej dokładny model z bardziej bardziej nieprawidłowymi wartościami będzie mniejszy.  
  
     Uregulowanie L1 i L2 mają różne efekty i używa.  
  
    -   L1 można zastosować do modeli rozrzedzonych, co jest przydatne podczas pracy z danymi o dużym wymiarze.  
  
    -   Z kolei w przypadku danych, które nie są rozrzedzone, preferowane jest uregulowanie L2.  
  
     Ten algorytm obsługuje liniową kombinację wartości rozliczania L1 i L2: to jest, <code>x = L1</code> Jeśli <code>y = L2</code>i, <code>ax + by = c</code> następnie definiuje liniowy zakres warunków uregulowania.  
  
    > [!NOTE]
    >  Chcesz dowiedzieć się więcej na temat uregulowania L1 i L2? Poniższy artykuł zawiera omówienie sposobu, w jaki uregulowania L1 i L2 są różne i jak wpływają na ich dopasowanie, z przykładami kodu dla regresji logistycznej i modeli sieci neuronowych:  [Uregulowanie L1 i L2 dla Machine Learning](https://msdn.microsoft.com/magazine/dn904675.aspx)  
    >
    > Dla modeli regresji logistycznej opracowano różne liniowe kombinacje warunków L1 i L2: na przykład [elastyczne uregulowanie netto](https://wikipedia.org/wiki/Elastic_net_regularization). Sugerujemy, aby odwoływać się do tych kombinacji w celu zdefiniowania kombinacji liniowej, która obowiązuje w modelu.
      
5.  W polu **rozmiar pamięci dla l-BFGS**określ ilość pamięci, która ma być używana dla optymalizacji *l-BFGS* .  
  
     L-BFGS oznacza "Limited Memory Broyden-Fletcher-Goldfarb-Shanno". Jest to algorytm optymalizacji, który jest popularny dla szacowania parametrów. Ten parametr wskazuje liczbę wcześniejszych pozycji i gradientów do przechowania w obliczeniach następnego kroku.  
  
     Ten parametr optymalizacji ogranicza ilość pamięci, która jest używana do obliczania następnego etapu i kierunku. W przypadku określenia mniejszej ilości pamięci szkolenie jest szybsze, ale mniej dokładne.  
  
6.  W przypadku **liczby losowej inicjatora**wpisz wartość całkowitą. Definiowanie wartości inicjatora jest ważne, jeśli chcesz, aby wyniki były odtwarzalne dla wielu przebiegów tego samego eksperymentu.  
  
  
8. Dodaj oznakowany zestaw danych do eksperymentu i Połącz jeden z [modułów szkoleniowych](module-reference.md).  
  
    -   W przypadku ustawienia opcji **Utwórz tryb Trainer** na **pojedynczy parametr**Użyj modułu [uczenie modelu](./train-model.md) .  
  
9. Uruchom eksperyment.  
  
## <a name="results"></a>Wyniki

Po zakończeniu szkolenia:

+ Aby wyświetlić podsumowanie parametrów modelu wraz z wagami funkcji zdobywanymi od szkoleń, kliknij prawym przyciskiem myszy dane wyjściowe [modelu uczenia](./train-model.md) i wybierz polecenie **Wizualizuj**.   
  
+ Aby dokonać prognoz dotyczących nowych danych, użyj przeszkolonego modelu i nowych danych jako danych wejściowych w module [model oceny](./score-model.md) . 


## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning usługi. 