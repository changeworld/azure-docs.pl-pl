---
title: 'Regresja logistyczna wieloklasowa: odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak użyć modułu regresji logistycznej wieloklasowej w usłudze Azure Machine Learning, aby utworzyć model regresji logistycznej, który może służyć do przewidywania wielu wartości.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: ca446b0ab67a8a202c1f4d505262660ac55f42db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456169"
---
# <a name="multiclass-logistic-regression-module"></a>Wieloklasowy moduł regresji logistycznej

W tym artykule opisano moduł w projektancie usługi Azure Machine Learning (wersja zapoznawcza).

Ten moduł służy do tworzenia modelu regresji logistycznej, który może służyć do przewidywania wielu wartości.

Klasyfikacja przy użyciu regresji logistycznej jest nadzorowaną metodą uczenia się i dlatego wymaga oznaczonego zestawu danych. Trenuj model, udostępniając model i oznaczony zestaw danych jako dane wejściowe do modułu, takiego jak [Train Model.](./train-model.md) Przeszkolony model może następnie służyć do przewidywania wartości dla nowych przykładów wejściowych.

Usługa Azure Machine Learning udostępnia również dwuklasowy moduł [regresji logistycznej,](./two-class-logistic-regression.md) który nadaje się do klasyfikacji zmiennych binarnych lub dychotomicznych.

## <a name="about-multiclass-logistic-regression"></a>Regresja logistyczna wieloklasowa — o wielu klasach

Regresja logistyczna jest dobrze znaną metodą w statystykach, która jest używana do przewidywania prawdopodobieństwa wyniku i jest popularna w przypadku zadań klasyfikacji. Algorytm przewiduje prawdopodobieństwo wystąpienia zdarzenia przez dopasowanie danych do funkcji logistycznej. 

W regresji logistycznej wieloklasowej klasyfikator może służyć do przewidywania wielu wyników.

## <a name="configure-a-multiclass-logistic-regression"></a>Konfigurowanie wieloklasowej regresji logistycznej

1. Dodaj moduł **regresji logistycznej wieloklasowej** do potoku.

2. Określ sposób, w jaki model ma być trenowany, ustawiając opcję **Utwórz tryb trenera.**

    + **Pojedynczy parametr:** Użyj tej opcji, jeśli wiesz, jak chcesz skonfigurować model i podać określony zestaw wartości jako argumenty.

    + **Zakres parametrów:** Wybierz tę opcję, jeśli nie masz pewności co do najlepszych parametrów i chcesz uruchomić wyciągnięcie po parametrze. Wybierz zakres wartości, aby iterować ponad i [Tune Model Hyperparameters](tune-model-hyperparameters.md) iteruje nad wszystkimi możliwymi kombinacjami ustawień podanych w celu określenia hiperparametrów, które dają optymalne wyniki.  

3. **Tolerancja optymalizacji**, określ wartość progową zbieżności optymalizatora. Jeśli poprawa między iteracjami jest mniejsza niż próg, algorytm zatrzymuje i zwraca bieżący model.

4. **L1 waga regularyzyjna,** **waga regularyzyjna L2**: Wpisz wartość używaną dla parametrów regularyzacji L1 i L2. Wartość niezerowa jest zalecana dla obu.

    Regularization jest metodą zapobiegania nadmiernemu dosyć przez karanie modeli o ekstremalnych wartościach współczynnika. Prawidłowość działa przez dodanie kary, która jest skojarzona z wartościami współczynnika do błędu hipotezy. Dokładny model o ekstremalnych wartościach współczynnika byłby karany bardziej, ale mniej dokładny model o bardziej konserwatywnych wartościach byłby karany mniej.

     L1 i L2 regularizacji mają różne efekty i zastosowań. L1 może być stosowany do modeli rozrzedzonych, co jest przydatne podczas pracy z danymi o wysokiej wymiarze. Natomiast regularyzacja L2 jest korzystniejsza dla danych, które nie są rzadkie.  Algorytm ten obsługuje liniową kombinację wartości regularyzacji L1 i `x = L1` `y = L2`L2: oznacza to, że if i , `ax + by = c` definiuje liniowy zakres terminów regularyzacji.

     Dla modeli regresji logistycznej opracowano różne kombinacje liniowe terminów L1 i L2, takie jak [elastyczna legalizacja sieci.](https://wikipedia.org/wiki/Elastic_net_regularization)

6. **Losowy numer materiału siewnego:** Wpisz wartość całkowitą do użycia jako inicjatora algorytmu, jeśli chcesz, aby wyniki były powtarzalne w przebiegach. W przeciwnym razie wartość zegara systemowego jest używana jako materiał siewny, który może produkować nieco inne wyniki w przebiegach tego samego potoku.

8. Podłącz oznaczony zestaw danych i jeden z modułów pociągu:

    + Jeśli **ustawisz tryb Utwórz tryb trenera** na **Pojedynczy parametr,** użyj modułu [Model pociągu.](./train-model.md)

9. Prześlij potok.



## <a name="next-steps"></a>Następne kroki

Zobacz [zestaw modułów dostępnych dla](module-reference.md) usługi Azure Machine Learning. 