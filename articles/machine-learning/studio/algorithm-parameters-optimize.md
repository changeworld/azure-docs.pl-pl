---
title: Optymalizuj algorytmy
titleSuffix: ML Studio (classic) - Azure
description: Wyjaśnia, jak wybrać optymalny zestaw parametrów dla algorytmu w Azure Machine Learning Studio (klasyczny).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 11/29/2017
ms.openlocfilehash: ac3aa0dc619ec05dcd79a4f8740026b1eabc19aa
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75427712"
---
# <a name="choose-parameters-to-optimize-your-algorithms-in-azure-machine-learning-studio-classic"></a>Wybierz parametry, aby zoptymalizować algorytmy w Azure Machine Learning Studio (klasyczny)

W tym temacie opisano, jak wybrać prawy parametr ustawiony dla algorytmu w Azure Machine Learning Studio (klasyczny). Większość algorytmów uczenia maszynowego parametrów wymaganych do zestawu. Ucząc model, konieczne jest podanie wartości tych parametrów. Skuteczność uczonego modelu zależy od parametrów modelu, które wybierzesz. Proces odnajdywania optymalny zestaw parametrów jest znany jako *modelu zaznaczenia*.



Istnieją różne sposoby modelu zaznaczenia. W uczeniu maszynowym, krzyżowe sprawdzanie poprawności jest jednym z najczęściej używanych metod wyboru modelu i jest domyślnym mechanizmem wyboru modelu w Azure Machine Learning Studio (klasyczny). Ponieważ Azure Machine Learning Studio (klasyczny) obsługuje zarówno język R, jak i Python, zawsze można zaimplementować własne mechanizmy wyboru modelu przy użyciu języka R lub Python.

Istnieją cztery kroki w procesie wyszukiwania najlepszy zestaw parametrów:

1. **Definiowanie przestrzeni parametru**: algorytm, najpierw zdecyduj, wartości parametrów dokładnie chcesz wziąć pod uwagę.
2. **Zdefiniuj ustawienia krzyżowego sprawdzania poprawności**: Zdecyduj, jak wybrać złożeń krzyżowego sprawdzania poprawności dla zestawu danych.
3. **Zdefiniuj Metryka**: Zdecyduj, jakie metryki używaną do określania najlepszy zestaw parametrów, takich jak dokładność, mean głównego kwadrat błąd, dokładności, odwołań lub wynik f.
4. **Uczenie, ocenę i porównywanie**: każda unikatowa kombinacja wartości parametrów krzyżowego sprawdzania poprawności jest przeprowadzane przez i oparte na metryce błąd, należy zdefiniować. Po ocenie i porównanie możesz wybrać sprzedające modelu.

Na poniższej ilustracji przedstawiono, jak można to osiągnąć w Azure Machine Learning Studio (klasyczny).

![Znajdź najlepsze zestaw parametrów](./media/algorithm-parameters-optimize/fig1.png)

## <a name="define-the-parameter-space"></a>Zdefiniuj obszaru parametrów
Można zdefiniować parametrem ustawionym na etapie inicjowania modelu. Okienko parametru wszystkich algorytmów uczenia maszynowego w dwóch trybach trainer: *pojedynczy parametr* i *zakres parametru*. Wybierz tryb parametru zakresu. W trybie parametru zakresu można wprowadzić wiele wartości dla każdego parametru. W polu tekstowym, można wprowadzić wartości rozdzielanych przecinkami.

![Dwuklasowych wzmocnione drzewo decyzyjnego, jeden parametr](./media/algorithm-parameters-optimize/fig2.png)

 Alternatywnie można zdefiniować maksymalne i minimalne punkty siatki i całkowita liczba punktów do można wygenerować za pomocą **Użyj zakresu konstruktora**. Domyślne wartości parametrów są generowane na skali liniowej. Ale w tym przypadku **skali logarytmicznej** jest zaznaczone, wartości są generowane w skali logarytmicznej (współczynnik sąsiadujące punkty jest stałe zamiast ich różnicy). Dla parametrów, liczba całkowita można zdefiniować zakres przy użyciu łącznika. Na przykład "1-10" oznacza, że wszystkie liczby całkowite z zakresu od 1 do 10 (oba włącznie) tworzą zestaw parametrów. Tryb mieszany jest również obsługiwany. Na przykład, ustaw parametr "1-10, 20, 50" obejmuje liczb całkowitych 1-10, 20 do 50.

![Dwuklasowych wzmocnione drzewo decyzyjnego, parametr zakresu](./media/algorithm-parameters-optimize/fig3.png)

## <a name="define-cross-validation-folds"></a>Zdefiniuj złożeń krzyżowego sprawdzania poprawności
Na [partycji i przykładowym][partition-and-sample] module można używać do losowego przypisywania zapisywanych danych. W następującej konfiguracji przykładowy moduł firma Microsoft Definiowanie pięciu złożeń i losowo przypisać numer zwijania do wystąpień przykładowe.

![Partycja i próbka](./media/algorithm-parameters-optimize/fig4.png)

## <a name="define-the-metric"></a>Zdefiniuj wartość metryki
Moduł " [dostrajanie modelu][tune-model-hyperparameters] " pozwala obsłużyć doświadczalnie Wybieranie najlepszego zestawu parametrów dla danego algorytmu i zestawu danych. Oprócz innych informacji dotyczących uczenia modelu, **właściwości** okienko ten moduł zawiera metryki określania najlepszy zestaw parametrów. Ma dwa pola listy rozwijanej różnych algorytmów klasyfikacji i regresji, odpowiednio. Jeśli algorytm pod uwagę jest to algorytm klasyfikacji, metryki regresji jest ignorowany i na odwrót. W tym konkretnym przykładzie Metryka to **dokładność**.   

![Odchylenia parametrów](./media/algorithm-parameters-optimize/fig5.png)

## <a name="train-evaluate-and-compare"></a>Uczenie, ocenę i porównania
Ten sam moduł [strojenia modelu][tune-model-hyperparameters] pociąga za siebie wszystkie modele, które odpowiadają zestawowi parametrów, ocenia różne metryki, a następnie tworzy model najlepiej szkolony na podstawie wybranej metryki. Ten moduł ma dwa obiekty wejściowe wymagane:

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
