---
title: 'Wieloklasowa regresja logistyczna: odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak za pomocą wieloklasowego modułu regresji logistycznej w Azure Machine Learning utworzyć model regresji logistycznej, który może służyć do przewidywania wielu wartości.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: 8d6140e97383fb07e65c87f74a555046f918ac41
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2020
ms.locfileid: "76314865"
---
# <a name="multiclass-logistic-regression-module"></a>Wieloklasowy moduł regresji logistycznej

W tym artykule opisano moduł w programie Azure Machine Learning Designer.

Ten moduł służy do tworzenia modelu regresji logistycznej, który może służyć do przewidywania wielu wartości.

Klasyfikacja przy użyciu regresji logistycznej to nadzorowana Metoda uczenia i dlatego wymaga oznaczonego zestawu danych. Możesz nauczyć model, dostarczając model i oznaczony zestaw danych jako dane wejściowe do modułu, takiego jak [uczenie modelu](./train-model.md). Model przeszkolony może być następnie używany do przewidywania wartości nowych przykładów wejściowych.

Azure Machine Learning udostępnia również moduł [regresji logistycznej z dwoma klasami](./two-class-logistic-regression.md) , który jest odpowiedni do klasyfikacji zmiennych binarnych lub dichotomous.

## <a name="about-multiclass-logistic-regression"></a>Informacje o regresji logistycznej wieloklasowej

Regresja logistyczna to dobrze znana metoda w statystyce, która jest używana do przewidywania prawdopodobieństwa wyniku i jest popularna dla zadań klasyfikacji. Algorytm przewiduje prawdopodobieństwo wystąpienia zdarzenia przez dopasowanie danych do funkcji logistycznej. 

W przypadku regresji logistycznej z wieloma klasami Klasyfikator może służyć do przewidywania wielu wyników.

## <a name="configure-a-multiclass-logistic-regression"></a>Konfigurowanie wieloklasowej regresji logistycznej

1. Dodaj moduł **regresji logistycznej dla wieloklasowego** do potoku.

2. Określ, w jaki sposób ma być szkolony model, ustawiając opcję **tworzenia trybu Trainer** .

    + **Pojedynczy parametr**: Użyj tej opcji, Jeśli wiesz, jak chcesz skonfigurować model i udostępnić określony zestaw wartości jako argumenty.

    + **Zakres parametrów**: Użyj tej opcji, jeśli nie masz pewności co do najlepszych parametrów i chcesz użyć odchylenia parametrów.

3. **Tolerancja optymalizacji**, określ wartość progową zbieżności Optymalizatora. Jeśli poprawa między iteracjami jest mniejsza niż wartość progowa, algorytm zatrzyma się i zwróci bieżący model.

4. **Waga uregulowania L1**, **waga uregulowania L2**: wpisz wartość, która ma być używana dla parametrów uregulowania L1 i L2. Dla obu tych zaleca się wartość różną od zera.

    Uregulowanie to metoda zapobiegania zastępowaniu poprzez nakładanie się modeli o wartości skrajnego współczynnika. Uregulowanie działa przez dodanie kary, która jest skojarzona z wartościami współczynnika, do błędu hipotezy. Dokładny model z wartościami współczynnika skrajnie byłby bardziej karany, ale mniej dokładny model z bardziej bardziej bardziej nieprawidłowymi wartościami byłyby mniejsze.

     Uregulowanie L1 i L2 mają różne efekty i używa. L1 można zastosować do modeli rozrzedzonych, co jest przydatne podczas pracy z danymi o dużym wymiarze. Z kolei w przypadku danych, które nie są rozrzedzone, preferowane jest uregulowanie L2.  Ten algorytm obsługuje liniową kombinację wartości rozliczania L1 i L2: oznacza to, że jeśli `x = L1` i `y = L2`, `ax + by = c` definiuje zakres liniowy warunków uregulowania.

     Dla modeli regresji logistycznej opracowano różne liniowe kombinacje warunków L1 i L2, takie jak [elastyczne uregulowanie netto](https://wikipedia.org/wiki/Elastic_net_regularization).

6. **Inicjator liczb losowych**: wpisz wartość całkowitą do użycia jako inicjator dla algorytmu, jeśli chcesz, aby wyniki były powtarzane. W przeciwnym razie wartość zegara systemowego jest używana jako inicjator, który może generować nieco inne wyniki w przebiegach tego samego potoku.

8. Połącz zestaw danych z etykietą i jeden z modułów uczenia:

    + W przypadku ustawienia opcji **Utwórz tryb Trainer** na **pojedynczy parametr**Użyj modułu [uczenie modelu](./train-model.md) .

9. Uruchamianie potoku.



## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning. 