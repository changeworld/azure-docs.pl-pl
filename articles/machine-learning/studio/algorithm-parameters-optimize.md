---
title: Zoptymalizuj algorytmy w usłudze Azure Machine Learning | Dokumentacja firmy Microsoft
description: Wyjaśniono, jak wybrać optymalne zestawu parametrów dla algorytmu w usłudze Azure Machine Learning.
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: 6717e30e-b8d8-4cc1-ad0b-1d4727928d32
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.openlocfilehash: 64d725712dc8cf87deb150944a390c9cef87b56f
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34833900"
---
# <a name="choose-parameters-to-optimize-your-algorithms-in-azure-machine-learning"></a>Wybierz parametry w celu zoptymalizowania algorytmy w usłudze Azure Machine Learning
W tym temacie opisano, jak wybrać prawa hyperparameter, ustaw dla algorytmu w usłudze Azure Machine Learning. Większość algorytmów uczenia maszynowego ma parametry, aby ustawić. Podczas uczenia modelu, konieczne jest podanie wartości tych parametrów. Efektywnie tworzyć trenowanego modelu zależy od parametrów modelu, które można wybrać. Proces odnajdywania optymalne zestaw parametrów jest nazywany *modelu zaznaczenia*.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

Istnieją różne sposoby modelu zaznaczenia. W uczeniu maszynowym, krzyżowe sprawdzanie poprawności jest jednym z najpopularniejszych metod wybór modelu i jest domyślny mechanizm wyboru modelu w usłudze Azure Machine Learning. Ponieważ uczenia maszynowego Azure obsługuje R i Python, zawsze można zaimplementować własne mechanizmy wybór modelu przy użyciu R lub Python.

Istnieją cztery czynności w procesie znajdowanie najlepsze zestaw parametrów:

1. **Zdefiniuj miejsca parametru**: dla algorytmu, najpierw zdecyduj, wartości parametrów dokładne chcesz wziąć pod uwagę.
2. **Zdefiniuj ustawienia krzyżowego sprawdzania poprawności**: zdecydować, jak wybrać złożeń krzyżowego sprawdzania poprawności dla zestawu danych.
3. **Zdefiniuj Metryka**: Zdecyduj, jakie Metryka używaną do określania zestawu najlepszych parametrów, takich jak dokładność, średniej głównego kwadrat błąd, dokładność, odwołania lub wynik f.
4. **Uczenie, ocenę i porównywania**: dla każdej kombinacji unikatowe wartości parametrów jest przeprowadzane przez i oparta na Metryka błędu, należy zdefiniować krzyżowego sprawdzania poprawności. Po oceny i porównania możesz wybrać najlepiej wykonywania modelu.

Na poniższym obrazie przedstawiono pokazuje, jak można to osiągnąć w usłudze Azure Machine Learning.

![Znajdź najlepsze zestaw parametrów](./media/algorithm-parameters-optimize/fig1.png)

## <a name="define-the-parameter-space"></a>Zdefiniuj miejsca parametru
Można określić parametr ustawiony w kroku inicjowania modelu. W okienku parametru wszystkich algorytmów uczenia maszynowego ma dwa tryby trainer: *pojedynczy parametr* i *zakres parametru*. Wybierz tryb zakres parametru. W trybie parametru zakresu można wprowadzić wiele wartości dla każdego parametru. W polu tekstowym, można wprowadzić wartości rozdzielanych przecinkami.

![Drzewo decyzyjne boosted dwuklasowych, jeden parametr](./media/algorithm-parameters-optimize/fig2.png)

 Alternatywnie można zdefiniować maksymalne i minimalne punktów siatki i łączną liczbę punktów, które ma zostać wygenerowane z **Użyj konstruktora zakresu**. Domyślnie wartości parametrów, które są generowane na skali liniowej. Jeśli jednak **skali logarytmicznej** zaznaczono wartości są generowane w skali logarytmicznej (stosunek sąsiadujących punktów jest stałe, zamiast ich różnica). Dla parametrów liczba całkowita można zdefiniować zakres przy użyciu łącznika. Na przykład "1-10" oznacza, że wszystkie liczby całkowite z przedziału od 1 do 10 (zarówno z wartościami granicznymi) tworzą zestaw parametrów. Tryb mieszany jest również obsługiwany. Na przykład ustawić parametr "1-10, 20, 50" obejmuje liczb całkowitych 1-10, 20 do 50.

![Drzewo decyzyjne boosted dwuklasowych, parametr zakresu](./media/algorithm-parameters-optimize/fig3.png)

## <a name="define-cross-validation-folds"></a>Zdefiniuj złożeń krzyżowego sprawdzania poprawności
[Partycja i próbka] [ partition-and-sample] modułu może służyć do losowo przypisać złożeń do danych. W następujących Przykładowa konfiguracja modułu możemy zdefiniować pięć złożeń i losowo przypisania do wystąpień próbki numeru złożenia.

![Partycja i próbka](./media/algorithm-parameters-optimize/fig4.png)

## <a name="define-the-metric"></a>Zdefiniuj metrykę
[Hiperparametry modelu] [ tune-model-hyperparameters] modułu zapewnia obsługę empirycznie Wybieranie najlepszych zbiór parametrów dla podanego algorytmu i zestawu danych. Oprócz innych informacji dotyczących uczenia modelu, **właściwości** okienko ten moduł zawiera metrykę określania najlepsze zestaw parametrów. Ma on dwa pola listy rozwijanej różnych algorytmów klasyfikacji i regresji, odpowiednio. Jeśli algorytm pod uwagę to algorytm klasyfikacji, metryka regresji jest ignorowany i na odwrót. W tym przykładzie określonych metryka jest **dokładność**.   

![Parametry odchylenia](./media/algorithm-parameters-optimize/fig5.png)

## <a name="train-evaluate-and-compare"></a>Uczenie, ocenę i porównywania
Taki sam [Hiperparametry modelu] [ tune-model-hyperparameters] pociągu modułu wszystkie modele, które odpowiadają parametr ustawić, ocenia różnych metryk, a następnie tworzy najlepiej trenowanego modelu na podstawie na Metryka Wybierz. Ten moduł ma dwóch parametrów wejściowych obowiązkowe:

* Nieprzeszkolonych uczeń
* Zestaw danych

Moduł ma również zestaw opcjonalne danych wejściowych. Zestaw danych Uzyskuj dostęp do złożenia informacji w danych wejściowych obowiązkowe zestawu danych. Jeśli zestaw danych nie przypisano żadnych informacji złożenia, 10-krotnych krzyżowego sprawdzania poprawności automatycznie jest wykonywana domyślnie. Jeśli przypisanie składanie nie została wykonana i sprawdzanie poprawności zestawu danych jest dostępne w porcie opcjonalny zestaw danych, wybrany jest tryb train testu i pierwszego zestawu danych jest używany do nauczenia modelu, dla każdej kombinacji parametrów.

![Klasyfikator drzewa decyzyjnego boosted](./media/algorithm-parameters-optimize/fig6a.png)

Model jest oceniane oddzielnie dla zestawu danych sprawdzania poprawności. Lewy port wyjściowy modułu zawiera różne metryki jako funkcje wartości parametrów. Port wyjściowy prawym daje trenowanego modelu, który odpowiada modelowi najlepiej zostanie wykonana zgodnie z wybranym metryka (**dokładność** w takim przypadku).  

![Sprawdzanie poprawności zestawu danych](./media/algorithm-parameters-optimize/fig6b.png)

Widać parametrami wybierany przez wizualizacja port wyjściowy w prawo. Ten model można oceniania zestawu testowego lub usługi sieci web operationalized po zapisaniu jako uczonego modelu.

<!-- Module References -->
[partition-and-sample]: https://msdn.microsoft.com/library/azure/a8726e34-1b3e-4515-b59a-3e4a475654b8/
[tune-model-hyperparameters]: https://msdn.microsoft.com/library/azure/038d91b6-c2f2-42a1-9215-1f2c20ed1b40/
