---
title: 'Wieloklasowej regresji logistycznej: Odwołania do modułu'
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak użyć modułu kontra regresji logistycznej w usłudze Azure Machine Learning, aby utworzyć model regresji logistycznej, który może służyć do prognozowania wielu wartości.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: ac4310e851808d6e6d89d1a2b506975eea3b1d69
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65029328"
---
# <a name="multiclass-logistic-regression-module"></a>Wieloklasowej regresji logistycznej modułu

W tym artykule opisano moduł interfejs graficzny (wersja zapoznawcza) dla usługi Azure Machine Learning.

Ten moduł służy do tworzenia modelu regresji logistycznej, który może służyć do prognozowania wielu wartości.

Klasyfikacja za pomocą regresji logistycznej jest metodą uczenia nadzorowanego i dlatego wymaga etykietami zestawu danych. Uczenie modelu, takich jak podając modelu i etykietami zestawu danych jako dane wejściowe do modułu [uczenie modelu](./train-model.md). Następnie można uczonego modelu do prognozowania wartości dla nowe przykłady danych wejściowych.

Usługa Azure Machine Learning udostępnia również [regresji logistycznej Two-Class](./two-class-logistic-regression.md) moduł, który jest odpowiedni dla klasyfikacji binarne lub dichotomous zmiennych.

## <a name="about-multiclass-logistic-regression"></a>Temat wieloklasowej regresji logistycznej

Regresja logistyczna jest metodą dobrze znane w statystyce, która jest używana w celu przewidywania prawdopodobieństwa spisania wynik, te są popularne zadania klasyfikacji. Algorytm przewiduje prawdopodobieństwo wystąpienia zdarzenia, przybliżając dane logistyczne funkcji. 

W wieloklasowej regresji logistycznej klasyfikatora może służyć do prognozowania wielu wyników.

## <a name="configure-a-multiclass-logistic-regression"></a>Konfigurowanie wieloklasowej regresji logistycznej

1. Dodaj **regresji logistycznej kontra** modułu do eksperymentu.

2. Określ, jak model, który ma być uczony, ustawiając **trybie trainer tworzenia** opcji.

    + **Pojedynczy parametr**: Użyj tej opcji, jeśli wiesz, jak chcesz skonfigurować model i zapewnić określony zbiór wartości jako argumenty.

    + **Parametr zakresu**: Użyj tej opcji, jeśli nie pewności najlepszych parametrów, a chcesz używać parametrów.

3. **Tolerancja optymalizacji**, określ wartość progowa zbieżności optymalizatora. Jeśli poprawy między poszczególnymi iteracjami jest poniżej progu, algorytm zatrzymuje i zwraca bieżącego modelu.

4. **Waga uregulowania L1**, **wagi uregulowania L2**: Wpisz wartość, aby użyć parametrów uregulowania P1 i P2. Wartość niezerową jest zalecane dla obu.

    Uregulowania to metoda zapobieganie overfitting niedyskryminacyjną modeli przy użyciu wartości skrajnych współczynnik. Uregulowania działa przez dodanie spadek, skojarzony z wartości współczynnika błędu hipotezę. Dokładne modelu przy użyciu wartości skrajnych współczynnik będzie bardziej karane, ale mniej dokładne modelu przy użyciu wartości bardziej konserwatywnego będzie karane mniej.

     P1 i P2 uregulowania dają różne efekty i używa. L1 można zastosować do modeli rozrzedzonych, co jest przydatne podczas pracy z danymi o wielu wymiarach. Z kolei uregulowania L2 jest preferowane dla danych, który nie jest rozrzedzony.  Ten algorytm obsługuje liniowej kombinacja wartości uregulowania P1 i P2: oznacza to, jeśli `x = L1` i `y = L2`, `ax + by = c` definiuje zakres liniowy warunki uregulowania.

     Różne kombinacje liniowej P1 i P2 warunki mają został zaprojektowany dla modele regresji logistycznej, takich jak [elastycznej uregulowania netto](https://wikipedia.org/wiki/Elastic_net_regularization).

6. **Inicjator losowy numer**: Wpisz wartość całkowitą służące jako inicjatora dla algorytmu, jeśli chcesz, aby wyniki, aby być powtarzalne za pośrednictwem przebiegów. W przeciwnym razie wartość Zegar systemu jest używana jako zalążek, które może wygenerować nieco różne wyniki w przebiegów eksperymentu tego samego.

8. Połącz z etykietami zestawu danych, a jeden z modułów train:

    + Jeśli ustawisz **trybie trainer tworzenia** do **pojedynczy parametr**, użyj [Train Model](./train-model.md) modułu.

9. Uruchom eksperyment.

## <a name="results"></a>Wyniki

Po zakończeniu szkolenia można wyświetlić podsumowanie parametrów modelu, wraz z wagi funkcji z szkolenia, kliknij prawym przyciskiem myszy dane wyjściowe [Train Model](./train-model.md) modułu, a następnie wybierz **Visualize**.


## <a name="next-steps"></a>Kolejne kroki

Zobacz [zestaw dostępnych modułów](module-reference.md) do usługi Azure Machine Learning. 