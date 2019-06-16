---
title: Optymalizowanie algorytmów
titleSuffix: Azure Machine Learning Studio
description: Wyjaśnia, jak wybrać optymalną parametrem ustawionym dla algorytmu w usłudze Azure Machine Learning Studio.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 11/29/2017
ms.openlocfilehash: 6dc9476f603d5664b7ea23489042b69f86647cf5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60752236"
---
# <a name="choose-parameters-to-optimize-your-algorithms-in-azure-machine-learning-studio"></a>Wybór parametrów w celu optymalizacji algorytmów w usłudze Azure Machine Learning Studio

W tym temacie opisano, jak wybrać odpowiednie hiperparametrycznego, ustaw dla algorytmu w usłudze Azure Machine Learning Studio. Większość algorytmów uczenia maszynowego parametrów wymaganych do zestawu. Ucząc model, konieczne jest podanie wartości tych parametrów. Skuteczność uczonego modelu zależy od parametrów modelu, które wybierzesz. Proces odnajdywania optymalny zestaw parametrów jest znany jako *modelu zaznaczenia*.



Istnieją różne sposoby modelu zaznaczenia. W usłudze machine learning krzyżowego sprawdzania poprawności jest jedną z najczęściej używanych metod wybór modelu i jest domyślnego mechanizmu wybór modelu w usłudze Azure Machine Learning Studio. Ponieważ usługi Azure Machine Learning Studio obsługuje języków R i Python, zawsze można zaimplementować własne mechanizmy wybór modelu przy użyciu języka R lub Python.

Istnieją cztery kroki w procesie wyszukiwania najlepszy zestaw parametrów:

1. **Definiowanie przestrzeni parametru**: Algorytm najpierw zdecyduj, wartości parametrów dokładnie, których chcesz wziąć pod uwagę.
2. **Zdefiniuj ustawienia krzyżowego sprawdzania poprawności**: Zdecyduj, jak wybrać złożeń krzyżowego sprawdzania poprawności dla zestawu danych.
3. **Zdefiniuj Metryka**: Zdecyduj, jakie metryki używaną do określania najlepszy zestaw parametrów, takich jak dokładność, mean głównego kwadrat błąd, dokładności, odwołań lub wynik f.
4. **Uczenie, ocenę i porównywanie**: Każda unikatowa kombinacja wartości parametrów krzyżowego sprawdzania poprawności jest przeprowadzane przez i oparte na metryce błąd, jaką zdefiniujesz. Po ocenie i porównanie możesz wybrać sprzedające modelu.

Na poniższym obrazie przedstawiono pokazuje, jak można to osiągnąć w usłudze Azure Machine Learning Studio.

![Znajdź najlepsze zestaw parametrów](./media/algorithm-parameters-optimize/fig1.png)

## <a name="define-the-parameter-space"></a>Zdefiniuj obszaru parametrów
Można zdefiniować parametrem ustawionym na etapie inicjowania modelu. W okienku parametrów algorytmów uczenia maszynowego wszystkich ma dwa tryby trainer: *Pojedynczy parametr* i *zakres parametru*. Wybierz tryb parametru zakresu. W trybie parametru zakresu można wprowadzić wiele wartości dla każdego parametru. W polu tekstowym, można wprowadzić wartości rozdzielanych przecinkami.

![Dwuklasowych wzmocnione drzewo decyzyjnego, jeden parametr](./media/algorithm-parameters-optimize/fig2.png)

 Alternatywnie można zdefiniować maksymalne i minimalne punkty siatki i całkowita liczba punktów do można wygenerować za pomocą **Użyj zakresu konstruktora**. Domyślne wartości parametrów są generowane na skali liniowej. Ale w tym przypadku **skali logarytmicznej** jest zaznaczone, wartości są generowane w skali logarytmicznej (współczynnik sąsiadujące punkty jest stałe zamiast ich różnicy). Dla parametrów, liczba całkowita można zdefiniować zakres przy użyciu łącznika. Na przykład "1-10" oznacza, że wszystkie liczby całkowite z zakresu od 1 do 10 (oba włącznie) tworzą zestaw parametrów. Tryb mieszany jest również obsługiwany. Na przykład, ustaw parametr "1-10, 20, 50" obejmuje liczb całkowitych 1-10, 20 do 50.

![Dwuklasowych wzmocnione drzewo decyzyjnego, parametr zakresu](./media/algorithm-parameters-optimize/fig3.png)

## <a name="define-cross-validation-folds"></a>Zdefiniuj złożeń krzyżowego sprawdzania poprawności
[Partycja i próbka] [ partition-and-sample] modułu może służyć do losowo przypisać złożeń do danych. W następującej konfiguracji przykładowy moduł firma Microsoft Definiowanie pięciu złożeń i losowo przypisać numer zwijania do wystąpień przykładowe.

![Partycja i próbka](./media/algorithm-parameters-optimize/fig4.png)

## <a name="define-the-metric"></a>Zdefiniuj wartość metryki
[Hiperparametry modelu] [ tune-model-hyperparameters] moduł zapewnia obsługę empirically wybierając najlepszy zestaw parametrów algorytmu danego i zestawu danych. Oprócz innych informacji dotyczących uczenia modelu, **właściwości** okienko ten moduł zawiera metryki określania najlepszy zestaw parametrów. Ma dwa pola listy rozwijanej różnych algorytmów klasyfikacji i regresji, odpowiednio. Jeśli algorytm pod uwagę jest to algorytm klasyfikacji, metryki regresji jest ignorowany i na odwrót. W tym konkretnym przykładzie Metryka to **dokładność**.   

![Odchylenia parametrów](./media/algorithm-parameters-optimize/fig5.png)

## <a name="train-evaluate-and-compare"></a>Uczenie, ocenę i porównania
Taki sam [Hiperparametry modelu] [ tune-model-hyperparameters] pociągów modułu wszystkie modele, które odnoszą się do parametru zestawu, ocenia różne metryki, a następnie tworzy najlepiej uczonego modelu na podstawie metryki możesz Wybierz. Ten moduł ma dwa obiekty wejściowe wymagane:

* Nieprzeszkolonych learner
* Zestaw danych

Moduł zawiera również opcjonalny zestaw danych wejściowych. Połącz z zestawu danych informacjami zwijania w danych wejściowych obowiązkowe zestawu danych. Jeśli zestaw danych nie przypisano żadnych informacji zwijania, 10-krotnych krzyżowego sprawdzania poprawności zostanie automatycznie wykonany domyślnie. Jeśli przypisanie zwijania nie jest wykonywane sprawdzanie poprawności zestawu danych znajduje się na porcie opcjonalny zestaw danych, jest wybrany tryb train-test i pierwszego zestawu danych jest używany do nauczenia modelu, dla każdej kombinacji parametrów.

![Klasyfikator drzewa decyzyjnego](./media/algorithm-parameters-optimize/fig6a.png)

Model jest następnie oceniany na zestawie danych sprawdzania poprawności. Lewy port wyjściowy modułu różne metryki jest wyświetlany jako funkcji wartości parametrów. Port wyjściowy w prawo powoduje sprzedające modelu zgodnie z wybranym metryki trenowanego modelu, który odpowiada (**dokładność** w tym przypadku).  

![Sprawdzanie poprawności zestawu danych](./media/algorithm-parameters-optimize/fig6b.png)

Możesz zobaczyć dokładnie parametrów, wybierany przez wizualizację port wyjściowy prawo. Ten model może służyć w oceniania zestawu testowego lub zoperacjonalizowanej usługi sieci web po zapisaniu jako uczonego modelu.

<!-- Module References -->
[partition-and-sample]: https://msdn.microsoft.com/library/azure/a8726e34-1b3e-4515-b59a-3e4a475654b8/
[tune-model-hyperparameters]: https://msdn.microsoft.com/library/azure/038d91b6-c2f2-42a1-9215-1f2c20ed1b40/
