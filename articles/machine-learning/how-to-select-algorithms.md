---
title: Jak wybrać algorytm uczenia maszynowego
titleSuffix: Azure Machine Learning
description: Jak wybrać algorytmy usługi Azure Machine Learning do nadzorowanego i nienadzorowanego uczenia się w eksperymentach klastrowania, klasyfikacji lub regresji.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: FrancescaLazzeri
ms.author: lazzeri
ms.reviewer: cgronlun
ms.date: 03/05/2020
ms.openlocfilehash: e0482bac9569a834adf3e1cdef2b3f702980eac0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78328667"
---
# <a name="how-to-select-algorithms-for-azure-machine-learning"></a>Jak wybrać algorytmy dla usługi Azure Machine Learning

Typowe pytanie brzmi:"Jaki algorytm uczenia maszynowego powinienem używać?" Wybrany algorytm zależy przede wszystkim od dwóch różnych aspektów scenariusza nauki o danych:

 - **Co chcesz zrobić ze swoimi danymi?** W szczególności, jakie jest pytanie biznesowe, na które chcesz odpowiedzieć, ucząc się na podstawie przeszłych danych?

 - **Jakie są wymagania scenariusza nauki o danych?** W szczególności, co to jest dokładność, czas szkolenia, liniowość, liczba parametrów i liczba funkcji, które obsługuje rozwiązanie?

 ![Zagadnienia dotyczące wyboru algorytmów: Co chcesz wiedzieć? Jakie są wymagania dotyczące scenariusza?](./media/how-to-select-algorithms/how-to-select-algorithms.png)

## <a name="business-scenarios-and-the-machine-learning-algorithm-cheat-sheet"></a>Scenariusze biznesowe i arkusz kodu algorytmu uczenia maszynowego

[Arkusz ściągawki algorytmu usługi Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/algorithm-cheat-sheet?WT.mc_id=docs-article-lazzeri) pomaga w pierwszej analizie: Co chcesz zrobić z **danymi?** W arkuszu ściągawki algorytmu uczenia maszynowego poszukaj zadania, które chcesz wykonać, a następnie znajdź algorytm [projektanta usługi Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/concept-designer?WT.mc_id=docs-article-lazzeri) dla rozwiązania do analizy predykcyjnej. 

Projektant uczenia maszynowego oferuje kompleksowe portfolio algorytmów, takich jak [wieloklasowy las decyzyjny,](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/multiclass-decision-forest?WT.mc_id=docs-article-lazzeri) [systemy rekomendacji,](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/evaluate-recommender?WT.mc_id=docs-article-lazzeri) [regresja sieci neuronowej,](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/neural-network-regression?WT.mc_id=docs-article-lazzeri) [wieloklasowa sieć neuronowa](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/multiclass-neural-network?WT.mc_id=docs-article-lazzeri)i [klastrowanie k-means.](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/k-means-clustering?WT.mc_id=docs-article-lazzeri) Każdy algorytm jest przeznaczony do rozwiązania innego typu problemu uczenia maszynowego. Zobacz [algorytm projektanta uczenia maszynowego i odwołanie do modułu,](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/module-reference?WT.mc_id=docs-article-lazzeri) aby uzyskać pełną listę wraz z dokumentacją dotyczącą działania każdego algorytmu i sposobu dostrajania parametrów w celu optymalizacji algorytmu.

> [!NOTE]
> Aby pobrać arkusz kodu algorytmu uczenia maszynowego, przejdź do [arkusza kodu algorytmu uczenia maszynowego platformy Azure.](https://docs.microsoft.com/azure/machine-learning/algorithm-cheat-sheet?WT.mc_id=docs-article-lazzeri)
> 
> 

Wraz ze wskazówkami w arkuszu kodu algorytmu uczenia maszynowego azure, należy pamiętać o innych wymaganiach podczas wybierania algorytmu uczenia maszynowego dla rozwiązania. Poniżej przedstawiono dodatkowe czynniki, które należy wziąć pod uwagę, takie jak dokładność, czas szkolenia, liniowość, liczba parametrów i liczba funkcji.

## <a name="additional-requirements-for-a-data-science-scenario"></a>Dodatkowe wymagania dotyczące scenariusza nauki o danych

Gdy wiesz, co chcesz zrobić z danymi, musisz określić dodatkowe wymagania dotyczące rozwiązania. 

Dokonuj wyborów i ewentualnie kompromisów dla następujących wymagań:

- Dokładność
- Czas trenowania
- Liniowości
- Liczba parametrów
- Liczba funkcji

## <a name="accuracy"></a>Dokładność

Dokładność uczenia maszynowego mierzy skuteczność modelu jako proporcję rzeczywistych wyników do wszystkich przypadków. W projektancie uczenia maszynowego [moduł Oceniaj model](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/evaluate-model?WT.mc_id=docs-article-lazzeri) oblicza zestaw standardowych metryk oceny branżowej. Za pomocą tego modułu można zmierzyć dokładność przeszkolonego modelu.

Uzyskanie możliwie najdokładniejszej odpowiedzi nie zawsze jest konieczne. Czasami przybliżenie jest odpowiednie, w zależności od tego, do czego chcesz go używać. W takim przypadku możesz znacznie skrócić czas przetwarzania, trzymając się bardziej przybliżonych metod. Przybliżone metody również naturalnie mają tendencję do unikania overfitting.

Istnieją trzy sposoby użycia modułu Oceń model:

- Generowanie wyników na danych treningowych w celu oceny modelu
- Generowanie wyników w modelu, ale porównać te wyniki z wynikami na zarezerwowanym zestawie testów
- Porównaj wyniki dla dwóch różnych, ale powiązanych modeli, używając tego samego zestawu danych

Aby uzyskać pełną listę metryk i podejść, których można użyć do oceny dokładności modeli uczenia maszynowego, zobacz [Ocena modułu modelu](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/evaluate-model?WT.mc_id=docs-article-lazzeri).

## <a name="training-time"></a>Czas trenowania

W uczeniu nadzorowanym szkolenie oznacza używanie danych historycznych do tworzenia modelu uczenia maszynowego, który minimalizuje błędy. Liczba minut lub godzin niezbędnych do szkolenia modelu różni się znacznie między algorytmami. Czas szkolenia jest często ściśle związany z dokładnością; jeden zazwyczaj towarzyszy drugiemu. 

Ponadto niektóre algorytmy są bardziej wrażliwe na liczbę punktów danych niż inne. Można wybrać określony algorytm, ponieważ masz ograniczenie czasu, zwłaszcza gdy zestaw danych jest duży.

W projektancie usługi Machine Learning tworzenie i używanie modelu uczenia maszynowego jest zazwyczaj procesem trzyetapowym:

1.  Skonfiguruj model, wybierając określony typ algorytmu, a następnie definiując jego parametry lub hiperparametry. 

2.  Podaj zestaw danych, który jest oznaczony etykietą i ma dane zgodne z algorytmem. Połącz dane i model z [modułem Train Model](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/train-model?WT.mc_id=docs-article-lazzeri).

3.  Po zakończeniu szkolenia użyj przeszkolonego modelu z jednym z [modułów oceniania,](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/score-model?WT.mc_id=docs-article-lazzeri) aby przewidywać nowe dane.

## <a name="linearity"></a>Liniowości

Liniowość w statystykach i uczeniu maszynowym oznacza, że istnieje liniowa zależność między zmienną a stałą w zestawie danych. Na przykład algorytmy klasyfikacji liniowej zakładają, że klasy mogą być oddzielone linią prostą (lub jej analogową o wyższym wymiarze).

Wiele algorytmów uczenia maszynowego korzysta z liniowości. W projektancie usługi Azure Machine Learning obejmują one: 

- [Wieloklasowa regresja logistyczna](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/multiclass-logistic-regression?WT.mc_id=docs-article-lazzeri)
- [Regresja logistyczna dwuklasowa](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/two-class-logistic-regression?WT.mc_id=docs-article-lazzeri)
- [Obsługuje maszyny wektorowe](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/two-class-support-vector-machine?WT.mc_id=docs-article-lazzeri)  

Algorytmy regresji liniowej zakładają, że trendy danych przebiegają zgodnie z linią prostą. To założenie nie jest złe dla niektórych problemów, ale dla innych zmniejsza dokładność. Pomimo ich wad, algorytmy liniowe są popularne jako pierwsza strategia. Wydają się być algorytmicznie proste i szybkie do pociągu.

![Granica klasy nieliniowej](./media/how-to-select-algorithms/nonlinear-class-boundary.png)

***Granica klasy nieliniowej:*** *Poleganie na algorytmie klasyfikacji liniowej spowodowałoby niską dokładność.*

![Dane z trendem nieliniowym](./media/how-to-select-algorithms/nonlinear-trend.png)

***Dane z trendem nieliniowym:*** *Użycie metody regresji liniowej wygenerowałoby znacznie większe błędy niż jest to konieczne.*

## <a name="number-of-parameters"></a>Liczba parametrów

Parametry to pokrętła, które naukowiec danych włącza podczas konfigurowania algorytmu. Są to liczby, które wpływają na zachowanie algorytmu, takie jak tolerancja błędów lub liczba iteracji lub opcje między wariantami zachowania algorytmu. Czas szkolenia i dokładność algorytmu mogą czasami być wrażliwe na uzyskanie tylko odpowiednie ustawienia. Zazwyczaj algorytmy z dużą liczbą parametrów wymagają najwięcej prób i błędów, aby znaleźć dobrą kombinację.

Alternatywnie istnieje [tune model hyperparameters moduł](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/tune-model-hyperparameters?WT.mc_id=docs-article-lazzeri) w projektancie uczenia maszynowego: Celem tego modułu jest określenie optymalnych hiperparametrów dla modelu uczenia maszynowego. Moduł tworzy i testuje wiele modeli przy użyciu różnych kombinacji ustawień. Porównuje metryki we wszystkich modelach, aby uzyskać kombinacje ustawień. 

Chociaż jest to świetny sposób, aby upewnić się, że masz spanned przestrzeni parametru, czas wymagany do szkolenia modelu zwiększa wykładniczo z liczbą parametrów. Plusem jest to, że posiadanie wielu parametrów zazwyczaj wskazuje, że algorytm ma większą elastyczność. Często osiąga bardzo dobrą dokładność, pod warunkiem, że można znaleźć odpowiednią kombinację ustawień parametrów.

## <a name="number-of-features"></a>Liczba funkcji

W uczeniu maszynowym funkcja jest wymierną zmienną zjawiska, które próbujesz przeanalizować. W przypadku niektórych typów danych liczba funkcji może być bardzo duża w porównaniu z liczbą punktów danych. Często ma to miejsce w przypadku genetyki lub danych tekstowych. 

Wiele funkcji może bagno niektóre algorytmy uczenia się, dzięki czemu czas szkolenia niewykonasię długo. [Obsługuje maszyny wektorowe](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/two-class-support-vector-machine?WT.mc_id=docs-article-lazzeri) są szczególnie dobrze dostosowane do scenariuszy z dużą liczbą funkcji. Z tego powodu były one używane w wielu aplikacjach, od pobierania informacji do klasyfikacji tekstu i obrazu. Maszyny wektorowe wsparcia mogą być używane zarówno do klasyfikacji i regresji zadań.

Wybór funkcji odnosi się do procesu stosowania testów statystycznych do danych wejściowych, biorąc pod uwagę określone dane wyjściowe. Celem jest określenie, które kolumny są bardziej predykcyjne danych wyjściowych. [Moduł wyboru funkcji oparty na filtrze](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/filter-based-feature-selection?WT.mc_id=docs-article-lazzeri) w projektancie uczenia maszynowego udostępnia wiele algorytmów wyboru funkcji do wyboru. Moduł zawiera metody korelacji, takie jak korelacja Pearsona i wartości chi-kwadrat.

[Moduł Ważność funkcji permutacji](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/permutation-feature-importance?WT.mc_id=docs-article-lazzeri) można również użyć do obliczenia zestawu wyników ważności funkcji dla zestawu danych. Następnie można wykorzystać te wyniki, aby ułatwić określenie najlepszych funkcji do użycia w modelu.


## <a name="next-steps"></a>Następne kroki

 - [Dowiedz się więcej o projektancie usługi Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/concept-designer?WT.mc_id=docs-article-lazzeri)
 - Opisy wszystkich algorytmów uczenia maszynowego dostępnych w projektancie usługi Azure Machine Learning można znaleźć w części [Algorytm projektanta usługi Machine Learning i informacje o module](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/module-reference?WT.mc_id=docs-article-lazzeri)
 - Aby poznać związek między uczeniem głębokim, uczeniem maszynowym i sią, zobacz [Uczenie głębokie a uczenie maszynowe](https://docs.microsoft.com/azure/machine-learning/service/concept-deep-learning-vs-machine-learning?WT.mc_id=docs-article-lazzeri)
